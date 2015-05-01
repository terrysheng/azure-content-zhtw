<properties 
	pageTitle="利用 Azure 媒體服務提供即時資料流" 
	description="本主題描述媒體服務即時資料流的一般工作流程步驟。" 
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
	ms.date="03/10/2015" 
	ms.author="juliako"/>


# 利用 Azure 媒體服務提供即時資料流

## 概觀

本主題說明 Azure 媒體服務 (AMS) 即時資料流的一般工作流程步驟。每個步驟會連結至相關主題。針對可以使用不同技術達成的工作，有幾個按鈕連結至您選擇的技術 (例如 .NET 或 REST)。   

請注意，您可以將媒體服務與現有工具和程序整合。例如，編碼站台上的內容，然後將其上傳至媒體服務以轉碼為多種格式，並透過 Azure CDN 或協力廠商 CDN 進行傳遞。 

下圖顯示即時資料流工作流程中涉及的媒體服務平台主要部分。

![即時工作流程][live-overview]

本主題描述即時資料流相關概念並提供一些主題連結，您可以從這些連結中瞭解如何完成即時資料流工作。

## 概念

如需即時資料流相關概念，請參閱[媒體服務概念](media-services-concepts.md)。

## 建立媒體服務帳戶

使用 **Azure 管理入口網站**[建立 Azure 媒體服務帳戶](media-services-create-account.md)。

## 設定串流端點

如需有關串流端點以及有管理方法簡介，請參閱[如何管理媒體服務帳戶中的串流處理端點](media-services-manage-origins.md)

## 設定開發環境  

針對開發環境選擇 **.NET** 或 **REST API**。

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

## 以程式設計方式連接  

選擇 **.NET** 或 **REST API** 以程式設計方式連接到 Azure 媒體服務。

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]


## 使用內部部署即時編碼器，將多位元速率資料流輸出至通道

## 使用第三方即時轉碼器

如需詳細資訊，請參閱[搭配使用協力廠商即時編碼器與 Azure 媒體服務](https://msdn.microsoft.com/library/azure/dn783464.aspx)。

## 管理通道、程式、資產

**概觀**：[管理通道和程式概觀](media-services-manage-channels-overview.md).

依序選擇 [入口網站]、[.NET]、[REST API]，即可查看範例。

[AZURE.INCLUDE [media-services-selector-manage-channels](../includes/media-services-selector-manage-channels.md)]

## 設定資產傳遞原則

使用 **.NET** 或 **REST API** 設定資產傳遞原則。

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

## 建立內容金鑰

建立當您利用 **.NET** 或 **REST API** 加密資產時會用到的內容金鑰。

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

## 設定內容金鑰授權原則 

使用 **.NET** 或 **REST API** 設定內容保護和金鑰授權原則。

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]


## 發行及傳遞資產

**概觀**：[傳遞內容概觀](media-services-deliver-content-overview.md)

使用 **Azure 管理入口網站**或 **.NET** (藉由建立定位器) 發行資產。

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


## 啟用 Azure CDN

媒體服務支援與 CDN 的整合。如需瞭解如何啟用 Azure CDN，請參閱[如何管理媒體服務帳戶中的串流端點](media-services-manage-origins.md#enable_cdn)。

## 調整媒體服務帳戶

您可以藉由指定您要佈建給帳戶的**串流保留單元**數目來調整 **媒體服務**。 

如需調整串流單元的相關資訊，請參閱：[如何調整串流單位](media-services-manage-origins.md#scale_streaming_endpoints.md).




[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png


<!--HONumber=52-->