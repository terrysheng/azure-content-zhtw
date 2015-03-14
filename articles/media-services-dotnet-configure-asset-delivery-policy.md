<properties 
	pageTitle="使用 .NET 設定資產傳遞原則" 
	description="本主題顯示如何設定不同的資產傳遞原則。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="juliako"/>

# 作法：設定資產傳遞原則
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

這篇文章屬於[要求工作流程上的媒體服務視訊](../media-services-video-on-demand-workflow)和[媒體服務即時資料流工作流程](../media-services-live-streaming-workflow)系列。 

媒體服務內容傳遞工作流程中的其中一個步驟正在設定您要進行串流處理之資產的傳遞原則。資產傳遞原則會告訴媒體服務您想要如何傳遞資產：應該將資產動態封裝至其中的串流通訊協定 (例如，MPEG DASH、HLS、Smooth Streaming 或全部)、是否要動態加密資產，以及方法 (信封或一般加密)。 

本主題討論建立和設定資產傳遞原則的原因和方法。 

>[AZURE.NOTE]若要能夠使用動態封裝和動態加密，您必須確定至少有一個縮放單元 (也稱為串流單元)。如需詳細資訊，請參閱[如何調整媒體服務](../media-services-manage-origins#scale_streaming_endpoints)。 
>
>您的資產也必須包含一組調適性位元速率 MP4 或調適性位元速率 Smooth Streaming 檔案。      

您可以將不同的原則套用至相同的資產。例如，您可以將 PlayReady 加密套用至 Smooth Streaming，以及將 AES Envelope 加密套用至 MPEG DASH 和 HLS。將會封鎖傳遞原則中未定義的任何通訊協定 (例如，您新增只將 HLS 指定為通訊協定的單一原則) 進行串流處理。例外狀況是您根本未定義任何資產傳遞原則時。然後，將以純文字允許所有通訊協定。

請注意，如果您想要傳遞儲存體加密資產，則必須設定資產的傳遞原則。串流伺服器要先移除儲存體加密，並使用指定的傳遞原則來串流處理內容，才能串流處理資產。例如，若要傳遞利用進階加密標準 (AES) 信封加密金鑰所加密的資產，請將原則類型設定為 **DynamicEnvelopeEncryption**。若要移除儲存體加密，並以純文字串流處理資產，請將原則類型設定為 **NoDynamicEncryption**。顯示如何設定這些原則類型的範例如下。 

根據資產傳遞原則的設定方式，您可以動態封裝、動態加密，以及串流處理下列串流通訊協定：Smooth Streaming、HLS、MPEG DASH 和 HDS 資料流。  

下列清單顯示用來串流處理 Smooth、HLS、DASH 和 HDS 的格式。  

Smooth Streaming：

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS：

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 

HDS

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

如需如何發佈資產以及建置串流 URL 的指示，請參閱[建置串流 URL](../media-services-deliver-streaming-content)。

## 清除資產傳遞原則 

下列 **ConfigureClearAssetDeliveryPolicy** 方法指定不套用動態加密，以及使用下列任何通訊協定來傳遞資料流：MPEG DASH、HLS 和 Smooth Streaming 通訊協定。 
  
如需建立 AssetDeliveryPolicy 時可指定之值的詳細資訊，請參閱[定義 AssetDeliveryPolicy 時所使用的類型](#types) 小節。 

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption, 
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }

## DynamicCommonEncryption 資產傳遞原則 


下列 **CreateAssetDeliveryPolicy** 方法會建立 **AssetDeliveryPolicy**，而後者設定成將動態一般加密 (**DynamicCommonEncryption**) 套用至 Smooth Streaming 通訊協定 (將會封鎖其他通訊協定進行串流處理)。此方法會採用兩個參數：**Asset** (您要套用傳遞原則的資產) 和 **IContentKey** (**CommonEncryption** 類型的內容金鑰，如需詳細資訊，請參閱：[建立內容金鑰](../media-services-dotnet-create-contentkey#common_contentkey))。

如需建立 AssetDeliveryPolicy 時可指定之值的詳細資訊，請參閱[定義 AssetDeliveryPolicy 時所使用的類型](#types) 小節。 


    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }



## DynamicEnvelopeEncryption 資產傳遞原則 

下列 **CreateAssetDeliveryPolicy** 方法會建立 **AssetDeliveryPolicy**，而後者設定成將動態信封加密 (**DynamicEnvelopeEncryption**) 套用至 HLS 和 DASH 通訊協定 (將會封鎖其他通訊協定進行串流處理)。此方法會採用兩個參數：**Asset** (您要套用傳遞原則的資產) 和 **IContentKey** (**EnvelopeEncryption** 類型的內容金鑰，如需詳細資訊，請參閱：[建立內容金鑰](../media-services-dotnet-create-contentkey#envelope_contentkey))。


如需建立 AssetDeliveryPolicy 時可指定之值的詳細資訊，請參閱[定義 AssetDeliveryPolicy 時所使用的類型](#types) 小節。   

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


## <a id="types"></a>定義 AssetDeliveryPolicy 時所使用的類型

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp
    }

### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,
    }
<!--HONumber=45--> 
