<properties 
	pageTitle="使用工作流程設計工具建立進階編碼工作流程" 
	description="深入了解如何使用工作流程設計工具建立進階編碼工作流程。" 
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
	ms.date="09/07/2015"
	ms.author="juliako"/>


#使用工作流程設計工具建立進階編碼工作流程

##概觀
「工作流程設計工具」是 Windows 傳統型工具，用來設計和建立自訂工作流程，以便用於「媒體編碼器高階工作流程」進行編碼。

這項工具也可用來修改[現有的工作流程](media-services-workflow-designer.md#existing_workflows)。

>[AZURE.NOTE]若想取得工作流程設計工具，請連絡 mepd@microsoft.com。


建立工作流程檔案後，此檔案可上傳做為資產，然後用來編碼媒體檔案。如需如何使用 **.NET** 以 **Media Encoder Premium Workflow** 進行編碼的相關資訊，請參閱[使用 Media Encoder Premium Workflow 進行進階編碼](media-services-encode-with-premium-workflow.md)。

##<a id="existing_workflows"></a>修改現有的工作流程

可以使用設計工具來修改預設的工作流程檔案。您可在[這裡](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)取得預設的工作流程檔案。資料夾也包含這些檔案的說明。

下列影片示範如何使用設計工具。

###第 1 天 – 開始使用

第 1 天影片涵蓋：

- 設計工具概觀
- 基本工作流程 – "Hello World"
- 建立多個搭配 Azure 媒體服務串流的輸出 MP4 檔案

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###第 2 天

第 2 天影片涵蓋：

- 不同來源檔案的案例 – 處理音訊
- 使用進階邏輯的工作流程
- 圖形階段

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###第 3 天

第 3 天影片涵蓋：

- 工作流程/藍圖中的指令碼處理
- 目前編碼器的限制
- 問答集
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]



##媒體服務學習路徑

您可以在此檢視 AMS 學習路徑：

- [AMS 即時資料流工作流程](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS 隨選資料流工作流程](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##另請參閱

[Azure Premium 編碼器工作流程設計工具訓練影片](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

<!---HONumber=Oct15_HO3-->