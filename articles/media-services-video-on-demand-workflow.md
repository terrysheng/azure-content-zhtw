<properties 
	pageTitle="媒體服務隨選視訊工作流程" 
	description="本主題說明媒體服務隨選視訊的一般工作流程步驟。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#媒體服務隨選視訊工作流程

##概觀

本主題說明 Azure 媒體服務 (AMS) 隨選視訊的一般工作流程步驟。每個步驟會連結至相關主題。針對可以使用不同技術達成的工作，有幾個按鈕連結至您選擇的技術 (例如 .NET 或 REST)。   

請注意，您可以將媒體服務與現有工具和程序整合。例如，編碼站台上的內容，然後將其上傳至媒體服務以轉碼為多種格式，並透過 Azure CDN 或協力廠商 CDN 進行傳遞。 

下圖顯示在點播視訊工作流程中牽涉到的媒體服務平台的主要部分。
![VoD workflow][vod-overview]

如需詳細資訊，請參閱[媒體服務概觀](media-services-overview.md)。

##建立媒體服務帳戶

使用 **Azure 管理入口網站** [建立 Azure 媒體服務帳戶](media-services-create-account.md)。 

##設定串流端點

[進行中]

##設定開發環境  

針對開發環境選擇 **.NET** 或 **REST API**。

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

##以程式設計方式連接  

選擇 **.NET** 或 **REST API** 以程式設計方式連接到 Azure 媒體服務。

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

##上傳媒體 

使用 **Azure 管理入口網站**、**.NET** 或 **REST API** 上傳檔案。

[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

##處理媒體：編碼、編製索引、監視工作

###取得媒體處理器

使用 **.NET** 或 **REST API** 取得媒體處理器。

[AZURE.INCLUDE [media-services-selector-get-media-processor](../includes/media-services-selector-get-media-processor.md)]

###建立工作 (Job) 

工作 (Job) 是一個實體，其中包含一組工作 (Task，例如編碼或編製索引) 的相關中繼資料。每一項工作 (Task) 會對輸入資產執行不可部分完成的作業。如需如何建立編碼工作 (Job) 的範例，請參閱：

[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

###監視工作進度

使用 **Azure 管理入口網站**、**.NET** 或 **REST API** 監視工作進度。

[AZURE.INCLUDE [media-services-selector-job-progress](../includes/media-services-selector-job-progress.md)]

###編製索引

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

###編碼 

請參閱[以 Azure 媒體服務進行編碼](media-services-encode-asset.md)。

##設定內容金鑰授權原則 

使用 **.NET** 或 **REST API** 設定內容保護和金鑰授權原則。

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

##設定資產傳遞原則

使用 **.NET** 或 **REST API** 設定資產傳遞原則。

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

##發行資產

使用 **Azure 管理入口網站**或 **.NET** (藉由建立定位器)  發行資產。

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]

##調整媒體服務帳戶

您可以指定要佈建給帳戶的**串流保留單元**和**編碼保留單元**，藉以調整**媒體服務**。 

您也可以透過新增儲存體帳戶，來調整媒體服務帳戶。每個儲存體帳戶的限制為 500 TB。若想將儲存空間擴大為超過預設限制，您可以選擇將多個儲存體帳戶附加至單一媒體服務帳戶。

[此](media-services-how-to-scale.md) 為相關主題的主題連結。


##播放您的內容

如需詳細資訊，請參閱[使用現有播放器來播放您的內容](media-services-playback-content.md)。

[vod-overview]: ./media/media-services-overview/media-services-video-on-demand.png
<!--HONumber=47-->
