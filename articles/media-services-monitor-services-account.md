<properties urlDisplayName="How to monitor" pageTitle="監視媒體服務帳戶 - Azure" metaKeywords="" description="說明如何為 Azure 中的媒體服務帳戶設定監視功能。" metaCanonical="" services="media-services" documentationCenter="" title="How to Monitor a Media Services Account" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





<h1><a id="monitormediaservicesaccount"></a>如何監視媒體服務帳戶</h1>
Azure 媒體服務儀表板會顯示可用來管理媒體服務帳戶的使用量度量和帳戶資訊。

您可以監視排入佇列的編碼工作、失敗的編碼工作、作用中編碼工作的數目 (以編碼器的輸入和輸出資料來表示)，以及使用量度量和帳戶資訊 Blob 儲存體使用量。此外，如果將內容串流傳輸給客戶，則您也可以擷取各種串流度量。您可以選擇監視過去 6 小時、24 小時或 7 天的資料。
 
>[WACOM.NOTE] 其他成本與 Azure 管理入口網站中監視儲存體資料有關。如需詳細資訊，請參閱[儲存體分析及計費](http://go.microsoft.com/fwlink/?LinkId=256667)。

<h2><a id="configuremonitoring"></a>作法：監視媒體服務帳戶</h2>

1. 在[管理入口網站](http://go.microsoft.com/fwlink/?LinkID=256666)中，按一下 [**媒體服務**]，然後按一下 [媒體服務帳戶名稱] 來開啟儀表板。 

	![MediaServices_Dashboard][dashboard]

2. 若要監視您的編碼工作或資料，只要開始將編碼工作提交至媒體服務即可，或利用 Azure 媒體隨選串流將內容串流傳輸給客戶。大約一小時過後，您在儀表板上應該就會開始看到監視資料。

<h2><a id="configuringstorage"></a>作法：監視 Blob 儲存體使用量 (選用)</h2>
1. 按一下 [**快速概覽**] 區段下的 [**儲存體帳戶**] 名稱。
2. 在儲存體帳戶頁面上，按一下 [**設定頁面**] 連結，然後向下捲動至 Blob、資料表及佇列服務的 [**監視**] 設定，如下所示。

	>[WACOM.NOTE] Blob 是媒體服務中唯一支援的儲存體類型。

	![StorageOptions][storage_options_scoped]

3. 在 [**監視**] 中，設定 Blob 的監視等級和資料保留原則：

-  若要設定監視等級，請選取下列其中一項：

      **最小** - 可收集針對 Blob、資料表和佇列服務彙總的度量 (例如入口流量/出口流量、可用性、延遲和成功百分比)。

      **詳細資訊** - 除了最小度量之外，在 Azure 儲存體服務 API 中也針對每個儲存體作業收集相同的度量集合。詳細資訊度量可供進一步分析在應用程式運作期間發生的問題。 

      **關閉** - 關閉監視。現有的監視資料會儲存到保留期間結束。

- 若要設定資料保留原則，請在 [**保留期限 (以天為單位)**] 中，輸入要保留資料的天數，範圍從 1 到 365 天。如果不想設定保留原則，則請輸入零。如果沒有保留原則，您可以決定是否刪除監視資料。我們建議根據要保留帳戶的儲存體分析資料多久來設定保留原則，以便系統可以不花費成本地刪除舊的無用分析資料。

4. 完成監視組態時，按一下 [**儲存**]。
與媒體服務度量相似，大約一小時過後，您在儀表板上應該就會開始看到監視資料。
度量存放在儲存體帳戶中的四個資料表：$MetricsTransactionsBlob、$MetricsTransactionsTable、$MetricsTransactionsQueue 及 $MetricsCapacityBlob。如需詳細資訊，請參閱[儲存體分析度量](http://go.microsoft.com/fwlink/?LinkId=256668)。


<!-- Images -->
[dashboard]: ./media/media-services-monitor-services-account/media-services-dashboard.png
[storage_options_scoped]: ./media/media-services-monitor-services-account/storagemonitoringoptions_scoped.png


<!--HONumber=35.1-->
