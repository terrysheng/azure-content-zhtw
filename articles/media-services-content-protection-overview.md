<properties 
	pageTitle="保護內容概觀" 
	description="此文章簡介如何利用 Media Services 保護內容。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/04/2015" 
	ms.author="juliako"/>

# 保護內容概觀

Microsoft Azure 媒體服務可讓您傳遞利用進階加密標準 (AES) (使用 128 位元加密金鑰) 和 PlayReady DRM 所加密的內容 (動態)。媒體服務也提供服務，傳遞金鑰和 PlayReady 授權給授權用戶端。 

![content-protection][content-protection]

要想使用動態加密，您必須從負責傳送加密內容的資料流端點至少取得一個資料流保留單位。

## 概念

### 動態加密

Microsoft Azure 媒體服務可讓您傳遞利用進階加密標準 (AES) (使用 128 位元加密金鑰) 和 PlayReady DRM 所加密的內容 (動態)。 

您目前可以加密下列串流格式：HLS、MPEG DASH 以及 Smooth Streaming。無法加密 HDS 串流格式，或漸進式下載。

如果您希望媒體服務加密資產，則需要將加密金鑰 (CommonEncryption 或 EnvelopeEncryption) 與資產產生關聯，同時設定金鑰的授權原則。

您也需要設定資產的傳遞原則。如果您想要串流儲存體加密的資產，請務必設定資產傳遞原則以指定您的傳遞方式。  

播放程式要求串流時，媒體服務便會使用 AES 或 PlayReady 加密，使用指定的金鑰動態加密您的內容。為了將串流解密，播放程式將從金鑰傳遞服務要求金鑰。為了決定使用者是否有權取得金鑰，服務會評估為金鑰指定的授權原則。

### PlayReady DRM 授權和 AES 清除金鑰傳遞服務

媒體服務提供另一項服務，可傳遞 PlayReady 授權和 AES 清除金鑰給已授權用戶端。若要為您的授權和金鑰設定授權和驗證原則，您可以使用 Azure 管理入口網站、REST API 或 .NET 適用的媒體服務 SDK。

請注意，如果您使用入口網站，您可以設定一個 AES 原則 (這會套用到所有以 AES 加密的內容)，以及一個 PlayReady 原則 (這會套用到所有以 PlayReady 加密的內容)。如果您想要更充分控制組態，請使用 .NET 適用的媒體服務 SDK。

### PlayReady 授權範本

媒體服務提供傳遞 PlayReady 授權的服務。當使用者播放程式 (例如 Silverlight) 嘗試播放受 PlayReady 保護的內容時，會傳送要求至授權傳遞服務以取得授權。如果授權服務核准要求，就會發出授權並傳送給用戶端，用來解密和播放指定的內容。

授權中包含您要 PlayReady DRM 執行階段在使用者嘗試播放受保護的內容時強制執行的權限和限制。媒體服務提供可讓您設定 PlayReady 授權的 API。如需詳細資訊，請參閱[媒體服務 PlayReady 授權範本概觀](https://msdn.microsoft.com/library/azure/dn783459.aspx)

### Token 限制

內容金鑰授權原則可能會有一個或多個授權限制：open、token 限制或 IP 限制。權杖限制原則必須伴隨著安全權杖服務 (STS) 所發出的權杖。媒體服務支援簡單 Web 權杖 (SWT) 格式和 JSON Web 權杖 (JWT) 格式的權杖媒體服務不提供安全權杖服務。您可以建立自訂 STS，或利用 Microsoft Azure ACS 來發行權杖。STS 必須設定為建立使用指定的索引鍵和問題宣告您在權杖限制組態中指定簽署的權杖。如果權杖有效，且權杖中的宣告符合為金鑰 (或授權) 設定的宣告，媒體服務金鑰傳遞服務就會將要求的金鑰 (或授權) 傳回給用戶端。
設定權杖限制原則時，您必須指定主要驗證金鑰、簽發者和對象參數。主要驗證金鑰包含簽署權杖使用的金鑰，簽發者是發行權杖的安全權杖服務。對象 (有時稱為範圍) 描述權杖或權杖獲授權存取之資源的用途。媒體服務金鑰傳遞服務會驗證權杖中的這些值符合在範本中的值。

## 常見案例

### 保護儲存體中的內容、提供動態加密串流處理媒體  

1. 將高品質夾層檔內嵌到資產。將儲存體加密選項套用到資產。
2. 設定串流端點。
1. 編碼為調適性位元速率 MP4 集。將儲存體加密選項套用到資產。
1. 為您想要在播放期間動態加密的資產建立加密內容金鑰。
2. 設定內容金鑰授權原則。
1. 設定資產傳遞原則 (使用動態封裝和動態加密)。
1. 藉由建立 OnDemand 定位器的方法來發行資產。
1. 串流發佈的內容。

### 將媒體服務金鑰和授權傳遞服務運用到您自己的加密與串流處理服務

如需詳細資訊，請參閱[如何整合 Azure PlayReady 授權服務與您自己的加密程式/串流伺服器](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server)。

## 一般內容加密工作

>[AZURE.NOTE]如果您的內容是儲存體加密，請務必設定資產傳遞原則。

### 設定串流端點

如需有關串流端點以及有管理方法簡介，請參閱[如何管理媒體服務帳戶中的串流處理端點](media-services-manage-origins.md)。

### 建立內容金鑰

建立當您利用 **.NET** 或 **REST API** 加密資產時會用到的內容金鑰。

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

### 設定內容金鑰授權原則 

使用 **.NET** 或 **REST API** 設定內容保護和金鑰授權原則。

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

### 設定資產傳遞原則

使用 **.NET** 或 **REST API** 設定資產傳遞原則。

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]


## 相關連結

[利用 Azure 媒體服務宣告 PlayReady 是一個服務和動態加密](http://mingfeiy.com/playready)

[Azure 媒體服務 PlayReady 授權傳遞定價說明](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[如何偵錯 Azure 媒體服務的 AES 加密串流](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT 權杖驗證](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[整合 Azure 媒體服務 OWIN MVC 型應用程式與 Azure Active Directory 並根據 JWT 宣告限制內容金鑰傳遞](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)。

[使用 Azure ACS 發行權杖](http://mingfeiy.com/acs-with-key-services)。

[content-protection]: ./media/media-services-content-protection/media-services-content-protection.png


<!--HONumber=52-->