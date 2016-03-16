<properties 
	pageTitle="使用 Azure 媒體服務傳遞 DRM 授權或 AES 金鑰" 
	description="本文將說明如何使用 Azure 媒體服務 (AMS) 來傳遞 PlayReady 和/或 Widevine 授權及 AES 金鑰，但使用您的內部部署伺服器完成其餘部分 (編碼、加密、串流)。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2016" 
	ms.author="juliako"/>


#使用 Azure 媒體服務傳遞 DRM 授權或 AES 金鑰

Azure 媒體服務 (AMS) 可讓您內嵌、編碼、新增內容保護，以及串流您的內容 (如需詳細資料，請參閱[這篇](media-services-protect-with-drm.md)文章)。不過，有一些客戶只想使用 AMS 來傳遞授權和/或金鑰，並使用他們的內部部署伺服器來進行編碼、加密和串流。本文章說明如何使用 AMS 來傳遞 PlayReady 和/或 Widevine 授權，但使用您的內部部署伺服器來完成其餘部分。


## 概觀

媒體服務提供傳遞 PlayReady 和 Widevine DRM 授權及 AES-128 金鑰的服務。媒體服務也提供 API，可讓您設定您要 DRM 執行階段在使用者播放受 DRM 保護內容時強制執行的權限和限制。當使用者要求受保護的內容時，播放器應用程式會向 AMS 授權服務要求授權。如果播放器已獲授權，則 AMS 授權服務會發出授權給播放器。PlayReady 和 Widevine 授權包含解密金鑰，可被用戶端播放器用來解密和串流處理內容。

媒體服務支援多種方式，來授權給提出授權要求或金鑰要求的使用者。您可以設定內容金鑰授權原則，且該原則可能會有一個或多個限制：Open 或是 Token 限制。權杖限制原則必須伴隨著安全權杖服務 (STS) 所發出的權杖。媒體服務支援簡單 Web 權杖 (SWT) 格式和 JSON Web 權杖 (JWT) 格式的權杖。


下圖示範使用 AMS 傳遞 PlayReady 和/或 Widevine 授權，但使用您的內部部署伺服器來完成其餘部分所需採取的主要步驟。

![利用 PlayReady 保護](./media/media-services-deliver-keys-and-licenses/media-services-diagram1.png)

##下載範例

您可以從[這裡](https://github.com/Azure/media-services-dotnet-deliver-drm-licenses)下載本文所述的範例。

##.NET 程式碼範例

本主題中的程式碼範例示範如何建立通用內容金鑰，並取得 PlayReady 或 Widevine 授權取得 URL。您需要從 AMS 取得下列資訊項目，並設定您的內部部署伺服器：**內容金鑰**、**金鑰識別碼**、**授權取得 URL**。一旦設定內部部署伺服器之後，就可以從您的串流伺服器串流處理。由於加密的串流指向 AMS 授權伺服器，您的播放器將會從 AMS 要求授權。如果您選擇權杖驗證，AMS 授權伺服器將會驗證您透過 HTTPS 所傳送的權杖，然後 (如果有效) 將授權傳遞回您的播放器。(此程式碼範例只示範如何建立通用內容金鑰，並取得 PlayReady 或 Widevine 授權取得 URL。如果您想要傳遞 AES-128 金鑰，您必須建立信封內容金鑰，並取得金鑰取得 URL，而[這篇](media-services-protect-with-aes128.md)文章將說明做法)。
	
	
	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Threading;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
	using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
	using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
	using Newtonsoft.Json;
	
	
	namespace DeliverDRMLicenses
	{
	    class Program
	    {
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	
	        private static readonly Uri _sampleIssuer =
	            new Uri(ConfigurationManager.AppSettings["Issuer"]);
	        private static readonly Uri _sampleAudience =
	            new Uri(ConfigurationManager.AppSettings["Audience"]);
	
	        // Field for service context.
	        private static CloudMediaContext _context = null;
	        private static MediaServicesCredentials _cachedCredentials = null;
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            bool tokenRestriction = true;
	            string tokenTemplateString = null;
	
	
	            IContentKey key = CreateCommonTypeContentKey();
	
	            // Print out the key ID and Key in base64 string format
	            Console.WriteLine("Created key {0} with key value {1} ", 
	                key.Id, System.Convert.ToBase64String(key.GetClearKeyValue()));
	
	            Console.WriteLine("PlayReady License Key delivery URL: {0}", 
	                key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
	
	            Console.WriteLine("Widevine License Key delivery URL: {0}",
	                key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine));
	
	            if (tokenRestriction)
	                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
	            else
	                AddOpenAuthorizationPolicy(key);
	
	            Console.WriteLine("Added authorization policy: {0}", 
	                key.AuthorizationPolicyId);
	            Console.WriteLine();
	            Console.ReadLine();
	        }
	
	        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
	        {
	
	            // Create ContentKeyAuthorizationPolicy with Open restrictions 
	            // and create authorization policy          
	
	            List<ContentKeyAuthorizationPolicyRestriction> restrictions = 
	                new List<ContentKeyAuthorizationPolicyRestriction>
	            {
	                new ContentKeyAuthorizationPolicyRestriction
	                {
	                    Name = "Open",
	                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
	                    Requirements = null
	                }
	            };
	
	            // Configure PlayReady and Widevine license templates.
	            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
	
	            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
	                _context.ContentKeyAuthorizationPolicyOptions.Create("",
	                    ContentKeyDeliveryType.PlayReadyLicense,
	                        restrictions, PlayReadyLicenseTemplate);
	
	            IContentKeyAuthorizationPolicyOption WidevinePolicy =
	                _context.ContentKeyAuthorizationPolicyOptions.Create("",
	                    ContentKeyDeliveryType.Widevine,
	                    restrictions, WidevineLicenseTemplate);
	
	            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                        ContentKeyAuthorizationPolicies.
	                        CreateAsync("Deliver Common Content Key with no restrictions").
	                        Result;
	
	
	            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
	            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
	            // Associate the content key authorization policy with the content key.
	            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	            contentKey = contentKey.UpdateAsync().Result;
	        }
	
	        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	        {
	            string tokenTemplateString = GenerateTokenRequirements();
	
	            List<ContentKeyAuthorizationPolicyRestriction> restrictions = 
	                new List<ContentKeyAuthorizationPolicyRestriction>
	            {
	                new ContentKeyAuthorizationPolicyRestriction
	                {
	                    Name = "Token Authorization Policy",
	                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	                    Requirements = tokenTemplateString,
	                }
	            };
	
	            // Configure PlayReady and Widevine license templates.
	            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
	
	            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
	                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
	                    ContentKeyDeliveryType.PlayReadyLicense,
	                        restrictions, PlayReadyLicenseTemplate);
	
	            IContentKeyAuthorizationPolicyOption WidevinePolicy =
	                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
	                    ContentKeyDeliveryType.Widevine,
	                        restrictions, WidevineLicenseTemplate);
	
	            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                        ContentKeyAuthorizationPolicies.
	                        CreateAsync("Deliver Common Content Key with token restrictions").
	                        Result;
	
	            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
	            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
	
	            // Associate the content key authorization policy with the content key
	            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	            contentKey = contentKey.UpdateAsync().Result;
	
	            return tokenTemplateString;
	        }

	        static private string GenerateTokenRequirements()
	        {
	            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	            template.PrimaryVerificationKey = new SymmetricVerificationKey();
	            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
	            template.Audience = _sampleAudience.ToString();
	            template.Issuer = _sampleIssuer.ToString();
	            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	            return TokenRestrictionTemplateSerializer.Serialize(template);
	        }

	        static private string ConfigurePlayReadyLicenseTemplate()
	        {
	            // The following code configures PlayReady License Template using .NET classes
	            // and returns the XML string.
	
	            //The PlayReadyLicenseResponseTemplate class represents the template 
	            //for the response sent back to the end user. 
	            //It contains a field for a custom data string between the license server 
	            //and the application (may be useful for custom app logic) 
	            //as well as a list of one or more license templates.
	
	            PlayReadyLicenseResponseTemplate responseTemplate = 
	                new PlayReadyLicenseResponseTemplate();
	
	            // The PlayReadyLicenseTemplate class represents a license template 
	            // for creating PlayReady licenses
	            // to be returned to the end users. 
	            // It contains the data on the content key in the license 
	            // and any rights or restrictions to be 
	            // enforced by the PlayReady DRM runtime when using the content key.
	            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
	
	            // Configure whether the license is persistent 
	            // (saved in persistent storage on the client) 
	            // or non-persistent (only held in memory while the player is using the license).  
	            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
	
	            // AllowTestDevices controls whether test devices can use the license or not.  
	            // If true, the MinimumSecurityLevel property of the license
	            // is set to 150.  If false (the default), 
	            // the MinimumSecurityLevel property of the license is set to 2000.
	            licenseTemplate.AllowTestDevices = true;
	
	            // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
	            // It grants the user the ability to playback the content subject to the zero or more restrictions 
	            // configured in the license and on the PlayRight itself (for playback specific policy). 
	            // Much of the policy on the PlayRight has to do with output restrictions 
	            // which control the types of outputs that the content can be played over and 
	            // any restrictions that must be put in place when using a given output.
	            // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
	            //then the DRM runtime will only allow the video to be displayed over digital outputs 
	            //(analog video outputs won’t be allowed to pass the content).
	
	            // IMPORTANT: These types of restrictions can be very powerful 
	            // but can also affect the consumer experience. 
	            // If the output protections are configured too restrictive, 
	            // the content might be unplayable on some clients. 
	            // For more information, see the PlayReady Compliance Rules document.
	
	            // For example:
	            //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);
	
	            responseTemplate.LicenseTemplates.Add(licenseTemplate);
	
	            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
	        }
	
	
	        private static string ConfigureWidevineLicenseTemplate()
	        {
	            var template = new WidevineMessage
	            {
	                allowed_track_types = AllowedTrackTypes.SD_HD,
	                content_key_specs = new[]
	                {
	                    new ContentKeySpecs
	                    {
	                        required_output_protection = 
	                            new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
	                        security_level = 1,
	                        track_type = "SD"
	                    }
	                },
	                policy_overrides = new
	                {
	                    can_play = true,
	                    can_persist = true,
	                    can_renew = false
	                }
	            };
	
	            string configuration = JsonConvert.SerializeObject(template);
	            return configuration;
	        }
	
	
	        static public IContentKey CreateCommonTypeContentKey()
	        {
	            // Create envelope encryption content key
	            Guid keyId = Guid.NewGuid();
	            byte[] contentKey = GetRandomBuffer(16);
	
	            IContentKey key = _context.ContentKeys.Create(
	                                    keyId,
	                                    contentKey,
	                                    "ContentKey",
	                                    ContentKeyType.CommonEncryption);
	
	            return key;
	        }
	
	
	
	        static private byte[] GetRandomBuffer(int length)
	        {
	            var returnValue = new byte[length];
	
	            using (var rng =
	                new System.Security.Cryptography.RNGCryptoServiceProvider())
	            {
	                rng.GetBytes(returnValue);
	            }
	
	            return returnValue;
	        }
	
	
	    }
	}
	

##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##另請參閱

[使用 PlayReady 和/或 Widevine 動態 Common Encryption](media-services-protect-with-drm.md)

[使用 AES-128 動態加密和金鑰傳遞服務](media-services-protect-with-aes128.md)

[使用合作夥伴將 Widevine 授權傳遞到 Azure 媒體服務](media-services-licenses-partner-integration.md)

<!---HONumber=AcomDC_0302_2016-------->