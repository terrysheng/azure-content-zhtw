<properties 
	pageTitle="Azure 媒體服務概觀和常見案例" 
	description="本主題提供 Azure 媒體服務的概觀" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article" 
	ms.date="02/02/2016"
	ms.author="juliako"/>

#Azure 媒體服務概觀和常見案例

Microsoft Azure 媒體服務是一個可延伸的雲端型平台，供開發人員建置可擴充的媒體管理和傳遞應用程式。媒體服務是以 REST API 為基礎，可讓您安全地上傳、儲存、編碼和封裝視訊或音訊內容，以用於隨選和即時資料流傳遞給各種用戶端 (例如電視、電腦和行動裝置)。

您可以建置完全採用媒體服務的端對端工作流程。您也可以選擇在工作流程的某些部分採用協力廠商元件。例如，使用第三方編碼器來進行編碼；然後使用媒體服務上傳、保護、封裝、傳遞。

您可以選擇即時串流您的內容或隨選傳遞內容。本主題說明[即時](media-services-overview.md#live_scenarios)傳遞內容或 [隨選](media-services-overview.md#vod_scenarios)傳遞內容的常見案例。本主題也會連結到其他相關主題。

## SDK 及工具 

若要建置媒體服務解決方案，您可以使用：

- [媒體服務 REST API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- 其中一個可用的用戶端 SDK： 
	- [Azure Media Services SDK for .NET](https://github.com/Azure/azure-sdk-for-media-services)、 
	- [Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java)、 
	- [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)、 
	- [Azure Media Services for Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (這是非 Microsoft 版本的 Node.js SDK。它是由社群維護，且目前並沒有 AMS API 的 100% 涵蓋範圍)。 
- 現有的工具： 
	- [Azure 傳統入口網站](http://manage.windowsazure.com/) 
	- [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure 媒體服務總管 (AMSE) 是適用於 Windows 的 Winforms/C# 應用程式)

##媒體服務學習路徑

您可以在此檢視 AMS 學習路徑：

- [AMS 即時資料流工作流程](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS 隨選資料流工作流程](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##海報

[這裡](https://azure.microsoft.com/documentation/infographics/media-services/)您可以檢視說明 AMS 工作流程的 Azure 媒體服務海報，說明從媒體建立到取用的步驟。

##必要條件

若要開始使用 Azure 媒體服務，您應該具備下列項目：
 
3. 一個 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](azure.microsoft.com)。
2. Azure 媒體服務帳戶。使用 Azure 傳統入口網站、.NET 或 REST API 建立 Azure 媒體服務帳戶。如需詳細資訊，請參閱[建立帳戶](media-services-create-account.md)。
3. (選擇性) 設定開發環境。針對開發環境選擇 .NET 或 REST API。如需詳細資訊，請參閱[設定環境](media-services-dotnet-how-to-use.md)。 

	此外，請了解如何以程式設計方式[連線](media-services-dotnet-connect_programmatically.md)。
4. (建議) 配置一或多個縮放單位。建議您為生產環境中的應用程式配置一個或多個縮放單位。如需詳細資訊，請參閱[管理串流端點](media-services-manage-origins.md)。

##概念和概觀

如需 Azure 媒體服務概念，請參閱[概念](media-services-concepts.md)。

如需介紹 Azure 媒體服務的所有主要元件的操作說明系列，請參閱 [Azure 媒體服務逐步教學課程](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series)。這一系列有絕佳的概念概觀，並使用 AMSE 工具來示範 AME 工作。請注意，AMSE 工具是一種 Windows 工具。這項工具支援大部分可以使用 [AMS SDK for .NET](https://github.com/Azure/azure-sdk-for-media-services)、[Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java) 或 [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php) 以程式設計方式完成的工作。

##<a id="vod_scenarios"></a>使用 Azure 媒體服務傳遞點播媒體：常見案例和工作

本節描述常見的案例並提供相關主題的連結。下圖顯示在傳遞隨選內容中牽涉到的媒體服務平台的主要部分。

![VoD 工作流程][vod-overview]


###保護儲存體中的內容並提供不加密的串流媒體

1. 將高品質夾層檔上傳到資產。
	
	建議您將儲存體加密選項套用到資產，以便內容在上傳時以及在儲存體中存放時可以得到保護。
 
1. 編碼成一組調適性位元速率 MP4 檔案。

	建議您將儲存體加密選項套用到輸出資產，以便保護存放的內容。
	
1. 設定資產傳遞原則 (用於動態封裝)。
	
	如果您的資產是已加密的儲存體，**必須**設定資產傳遞原則。

1. 藉由建立 OnDemand 定位器的方法來發行資產。

	確定負責傳送內容的串流端點上，至少有一個串流保留單位。

1. 串流發佈的內容。

###保護儲存體中的內容、提供動態加密串流處理媒體  

要想使用動態加密，您必須從負責傳送加密內容的資料流端點至少取得一個資料流保留單位。

1. 將高品質夾層檔上傳到資產。將儲存體加密選項套用到資產。
1. 編碼成一組調適性位元速率 MP4 檔案。將儲存體加密選項套用到輸出資產。
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
1. 編碼成單一 MP4 檔案。
1. 藉由建立 OnDemand 或 SAS 定位器的方法來發行資產。

	如果使用 OnDemand 定位器，請在您計劃進行漸進式下載內容的串流端點上，確定至少有一個串流保留單位。

	如果使用 SAS 定位器，內容是從 Azure blob 儲存體下載的。在此情況下，您不需要串流保留單位。
  
1. 漸進式下載內容。

###另請參閱

- [如何上傳內容](media-services-manage-content.md#upload)
- [如何取得媒體處理器](media-services-get-media-processor.md)
- [如何編碼內容](media-services-manage-content.md#encode)
- [如何監視工作](media-services-portal-check-job-progress.md)
- [如何編製內容索引](media-services-manage-content.md#index)
- [如何保護內容](media-services-manage-content.md#encrypt)
- [如何保護發佈](media-services-manage-content.md#publish)
- [如何調整編碼](media-services-portal-encoding-units.md)

##<a id="live_scenarios"></a>使用 Azure 媒體服務傳遞即時串流事件

使用即時資料流時通常涉及下列元件：

- 相機，用來廣播事件。
- 即時視訊編碼器，它會將相機中的訊號轉換成資料流，然後再傳送至即時資料流服務。 
  
	(選擇性) 多個即時編碼器。針對某些需要相當高度可用性與高品質經驗的重要即時事件，建議使用主動對主動備援編碼器，以達成順暢容錯移轉，而不會遺失資料。
- 即時串流服務可讓您執行下列動作： 
	- 使用各種即時串流處理通訊協定 (例如 RTMP 或 Smooth Streaming) 擷取即時內容， 
	- 將您的串流編碼成調適性位元速率串流
	- 預覽您的即時串流，
	- 儲存擷取的內容以於稍後進行串流 (隨選視訊)
	- 透過一般串流通訊協定 (例如，MPEG DASH、Smooth、HLS、HDS) 直接將內容傳遞給客戶，或傳遞至內容傳遞網路 (CDN) 供進一步的發佈。 
	
		
**Microsoft Azure 媒體服務** (AMS) 提供擷取、編碼、預覽、儲存和傳遞即時串流內容的能力。

當您將內容傳遞給客戶時，您的目標是在不同的網路條件下將高品質的視訊傳遞到各種裝置。請注意品質和網路狀況，使用即時編碼器將您的串流編碼成多位元速率 (調適性位元速率) 視訊串流。請注意不同裝置上的串流，使用媒體服務[動態封裝](media-services-dynamic-packaging-overview.md)將串流動態地重新封裝至不同的通訊協定。媒體服務支援傳遞下列可調位元速率資料流技術：HTTP 即時資料流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS (僅適用於 Adobe PrimeTime/Access licensees)。

在 Azure 媒體服務中，**通道**、**程式**及 **StreamingEndpoints** 會處理所有的即時串流功能，包括內嵌、格式化、DVR、安全性、延展性和備援能力。

在 Azure 媒體服務中，**通道**代表處理即時串流內容的管線。目前，通道可以接收即時輸入串流的方式如下：


- 內部部署即時編碼器會傳送單一位元速率串流至通道，可以使用下列格式之一，以媒體服務執行即時編碼：RTP (MPEG-TS)、RTMP 或 Smooth Streaming (分散的 MP4) 。通道接著會執行即時編碼，將連入的單一位元速率串流編碼成多位元速率 (自動調整) 視訊串流。接到要求時，媒體服務會傳遞串流給客戶。

	利用媒體服務編碼即時串流處於**預覽**狀態。
- 內部部署即時編碼器會傳送多位元速率 **RTMP** 或 **Smooth Streaming** (分散式 MP4) 到通道。您可以使用下列輸出多位元速率 Smooth Streaming 的即時編碼器：Elemental、Envivio、Cisco。下列即時編碼器會輸出 RTMP：Adobe Flash Live、Telestream Wirecast 和 Tricaster 轉錄器。內嵌的串流會通過**通道**，而不需任何進一步處理。您的即時編碼器也會傳送單一位元速率串流至無法用於即時編碼的通道，但是不建議。接到要求時，媒體服務會傳遞串流給客戶。


###使用啟用的通道來以 Azure 媒體服務執行即時編碼


下圖顯示 AMS 平台的主要部分，與通道可以使用媒體服務執行即時編碼的即時串流工作流程有關。

![即時工作流程][live-overview1]

如需詳細資訊，請參閱[使用啟用的通道來以 Azure 媒體服務執行即時編碼](media-services-manage-live-encoder-enabled-channels.md)。


###使用通道，從內部部署編碼器接收多位元速率即時串流


下圖顯示即時串流工作流程中涉及的 AMS 平台主要部分。

![即時工作流程][live-overview2]

如需詳細資訊，請參閱[使用通道，從內部部署編碼器接收多位元速率即時串流](media-services-manage-channels-overview.md)。

##使用內容

Azure 媒體服務提供一些工具，供您用來建立適用於大部分平台的豐富、動態用戶端播放器應用程式，此處所述的平台包括：iOS 裝置、Android 裝置、Windows、Windows Phone、Xbox 和機上盒。下列主題也會提供 SDK 和 Player Framework 連結，可讓您開發自己的用戶端應用程式，使用來自媒體服務的串流媒體。

[開發視訊播放器應用程式](media-services-develop-video-players.md)

##啟用 Azure CDN

媒體服務支援與 Azure CDN 整合。如需如何啟用 Azure CDN 的資訊，請參閱[如何管理媒體服務帳戶中的串流端點](media-services-manage-origins.md#enable_cdn)。

##調整媒體服務帳戶

您可以指定要佈建給帳戶的**串流保留單元**和**編碼保留單元**，藉以調整**媒體服務**。

您也可以透過新增儲存體帳戶，來調整媒體服務帳戶。每個儲存體帳戶的限制為 500 TB。若想將儲存空間擴大為超過預設限制，您可以選擇將多個儲存體帳戶附加至單一媒體服務帳戶。

[本主題](media-services-how-to-scale.md)會連結相關主題。

##支援

[Azure 支援](https://azure.microsoft.com/support/options/)提供 Azure 的支援選項，包括媒體服務。

##模式與實例指南

[模式與實例指南](https://wamsg.codeplex.com/) [線上文件](https://msdn.microsoft.com/library/dn735912.aspx) [可下載的電子書](https://www.microsoft.com/download/details.aspx?id=42629)


##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##服務等級協定 (SLA)

- 對於媒體服務編碼，我們保證 REST API 交易可用性高達 99.9%。
- 對於串流，我們在至少購買一個串流單位時，針對現有的媒體內容，可保證以 99.9% 的可用性成功服務要求。
- 對於即時通道，我們保證執行中的通道至少 99.9% 的時間具有外部連線能力。
- 對於內容保護，我們保證至少 99.9% 的時間將會成功滿足金鑰要求。
- 對於索引子，我們將會在 99.9% 的時間成功服務編碼保留單元處理的索引子工作要求。

	如需詳細資訊，請參閱 [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/)。

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 

<!---HONumber=AcomDC_0309_2016-->