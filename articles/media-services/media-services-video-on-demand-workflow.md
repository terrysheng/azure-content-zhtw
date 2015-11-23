<properties 
	pageTitle="利用 Azure 媒體服務提供點播媒體" 
	description="本主題說明利用 Azure 媒體服務傳遞點播媒體時常見的案例。" 
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
	ms.date="09/07/2015"   
	ms.author="juliako"/>


#利用 Azure 媒體服務提供點播媒體

##概觀

本主題說明 Azure 媒體服務 (AMS) 隨選視訊的一般工作流程步驟。每個步驟會連結至相關主題。針對可以使用不同技術達成的工作，有幾個按鈕連結至您選擇的技術 (例如 .NET 或 REST)。

請注意，您可以將媒體服務與現有工具和程序整合。例如，編碼站台上的內容，然後將其上傳至媒體服務以轉碼為多種格式，並透過 Azure CDN 或協力商廠 CDN 進行傳遞。

下圖顯示在點播視訊工作流程中牽涉到的媒體服務平台的主要部分。![VoD 工作流程][vod-overview]

##<a id="vod_scenarios"></a>常見案例：提供點播媒體。

###保護儲存體中的內容並提供不加密的串流媒體

1. 將高品質夾層檔上傳到資產。
	
	建議您將儲存體加密選項套用到資產，以便內容在上傳時以及在儲存體中存放時可以得到保護。 
1. 編碼為調適性位元速率 MP4 集。 

	建議您將儲存體加密選項套用到輸出資產，以便保護存放的內容。
	
1. 設定資產傳遞原則 (用於動態封裝)。
	
	如果您的資產是已加密的儲存體，**必須**設定資產傳遞原則。

1. 藉由建立 OnDemand 定位器的方法來發行資產。

	確定負責傳送內容的串流端點上，至少有一個串流保留單位。

1. 串流發佈的內容。

###保護儲存體中的內容、提供動態加密串流處理媒體  

要想使用動態加密，您必須從負責傳送加密內容的資料流端點至少取得一個資料流保留單位。

1. 將高品質夾層檔上傳到資產。將儲存體加密選項套用到資產。
1. 編碼為調適性位元速率 MP4 集。將儲存體加密選項套用到輸出資產。
1. 針對您想要在播放期間動態加密的資產，建立加密內容金鑰。
2. 設定內容金鑰授權原則。
1. 設定資產傳遞原則 (供動態封裝和動態加密使用)。
1. 透過建立 OnDemand 定位器，來發佈資產。
1. 串流發佈的內容。 

###編製內容索引

1. 將高品質夾層檔上傳到資產。
1. 編製內容索引。

	索引會產生一些檔案，而它們可以當做播放視訊時會用到的隱藏式字幕 (CC)。它也會產生一些檔案，讓您可以在視訊中進行搜尋，然後跳至視訊的正確位置。

1. 使用索引內容。


###提供漸進式下載 

1. 將高品質夾層檔上傳到資產。
1. 編碼為調適性位元速率 MP4 集或單一 MP4。
1. 藉由建立 OnDemand 或 SAS 定位器的方法來發行資產。

	如果使用 OnDemand 定位器，請在您計劃進行漸進式下載內容的串流端點上，確定至少有一個串流保留單位。

	如果使用 SAS 定位器，內容是從 Azure blob 儲存體下載的。在此情況下，您不需要串流保留單位。
  
1. 漸進式下載內容。

本文章提供一些連結，為您示範如何設定開發環境以及執行上述工作。


##概念

如需了解如何傳遞點播內容的概念，請參閱[媒體服務概念](media-services-concepts.md)。

##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
 

<!---HONumber=Nov15_HO3-->