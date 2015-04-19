<properties 
	pageTitle="媒體服務即時串流工作流程" 
	description="本主題描述媒體服務即時串流的一般工作流程步驟。" 
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
	ms.date="02/18/2015" 
	ms.author="juliako"/>


#媒體服務即時串流工作流程

##概觀

本主題說明 Azure 媒體服務 (AMS) 即時串流的一般工作流程步驟。每個步驟會連結至相關主題。針對可以使用不同技術達成的工作，有幾個按鈕連結至您選擇的技術 (例如 .NET 或 REST)。   

請注意，您可以將媒體服務與現有工具和程序整合。例如，編碼站台上的內容，然後將其上傳至媒體服務以轉碼為多種格式，並透過 Azure CDN 或協力廠商 CDN 進行傳遞。 

下圖顯示在點播視訊工作流程中牽涉到的媒體服務平台的主要部分。
![Live workflow][live-overview]


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

##使用即時轉錄器

如需詳細資訊，請參閱[搭配使用協力廠商即時編碼器與 Azure 媒體服務](https://msdn.microsoft.com/library/azure/dn783464.aspx)。

##管理通道、程式、資產

如需詳細資訊，請參閱[即時串流](https://msdn.microsoft.com/library/azure/dn783466.aspx)。

##設定內容保護和內容金鑰授權原則 

使用 **.NET** 或 **REST API** 設定內容金鑰授權原則。

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

##設定資產傳遞原則

使用 **.NET** 或 **REST API** 設定資產傳遞原則。

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

##發行資產

使用 **Azure 管理入口網站**或 **.NET** (藉由建立定位器)  發行資產。

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


##調整媒體服務帳戶

您可以藉由指定您要佈建給帳戶的**串流保留單元**數目來調整 **媒體服務**。 

如需調整串流單元的相關資訊，請參閱：[如何調整串流單位](media-services-manage-origins#scale_streaming_endpoints.md)。



[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png
<!--HONumber=47-->
