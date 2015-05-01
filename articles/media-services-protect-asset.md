<properties 
	pageTitle="如何為媒體服務中的資產加密 - Azure" 
	description="了解如何使用 Microsoft PlayReady 保護為媒體服務中的資產加密。程式碼範例以 C# 撰寫，並使用 Media Services SDK for .NET。程式碼範例以 C# 撰寫，並使用 Media Services SDK for .NET。" 
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


# 做法：使用 PlayReady 或 AES-128 動態加密資產

這篇文章是[媒體服務點播視訊工作流程](media-services-video-on-demand-workflow.md)和[媒體服務即時資料流工作流程](media-services-live-streaming-workflow.md)系列的一部分。
  
## 概觀

Microsoft Azure 媒體服務可讓您傳遞利用進階加密標準 (AES) (使用 128 位元加密金鑰) 和 PlayReady DRM 所加密的內容 (動態)。媒體服務也提供服務，傳遞金鑰和 PlayReady 授權給授權用戶端。若要提供受保護的內容，您需要設定內容金鑰授權原則並設定資產傳遞原則。

## 設定

如需如何設定內容金鑰授權原則的相關資訊 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)] 

設定資產傳遞原則
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]
 


<!--HONumber=52-->