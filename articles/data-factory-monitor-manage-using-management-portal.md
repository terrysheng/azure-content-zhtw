<properties title="Monitor and manage Azure Data Factory using Azure Preview Portal" pageTitle="使用 Azure 預覽入口網站監視和管理 Azure Data Factory" description="了解如何使用 Azure 管理入口網站來監視及管理您已經建立的 Azure 資料處理站。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# 使用 Azure 預覽入口網站監視 Azure Data Factory

- [檢視 Azure 訂用帳戶中的所有 Data Factory](#AllDataFactories)
- [檢視 Data Factory 的詳細資料](#DataFactoryDetails)
- [檢視 Data Factory 的圖表檢視](#DataFactoryDiagram)
- [檢視 Data Factory 中的連結服務](#DataFactoryLinkedServices)
- [檢視連結服務的詳細資料](#DataFactoryLinkedService) 
- [檢視 Data Factory 中的資料集](#DataFactoryDatasets)
- [檢視資料集的詳細資料](#DataFactoryDataset)
- [檢視配量的詳細資料](#DataFactorySlice) 
- [檢視配量的所有活動執行](#DataFactoryActivtyRuns) 
- [檢視活動執行的詳細資料](#DataFactoryActivtyRunDetails)
- [作業透鏡 - 過去一週的事件](#EventsInThePastweek)  
   


## <a name="AllDataFactories"></a> 檢視 Azure 訂閱中的所有 Data Factory

- 登入 [Azure Preview 入口網站][azure-preview-portal]。
- 按一下左邊的 [瀏覽]**** 中樞，然後按一下 [Data Factory]****。  

	![BROWSE hub -> Data Factories][image-data-factory-browse-datafactories]

	如果看不到 [Data Factory]****，請按一下 [所有項目]****，然後按一下 [瀏覽]**** 刀鋒視窗中的 [Data Factory]****。

	![BROWSE hub -> Everything] [image-data-factory-browse-everything]

- 您應該會在 [Data Factory]**** 刀鋒視窗中看到所有 Data Factory。

	![Data factories blade][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> 檢視 Data Factory 的詳細資料

若要檢視 Data Factory 的詳細資料，請執行下列其中一項： 


- 按一下上面顯示之 [Data Factory]**** 刀鋒視窗中的 Data Factory。
- 按一下 [開始面板]****] 上的 Data Factory 連結。****「開始面板」是您登入「Azure Preview 入口網站」時看到的刀鋒視窗。如果您在建立 Data Factory 時選取了 [新增到開始面板]**** (預設選項)，就應該會在「開始面板」上看到 Data Factory 連結，如下圖所示。在此範例中，「開始面板」****上有 **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** 及 **LogProcessingFactory** Data Factory 連結。


![Data factory from the Startboard][image-data-factory-datafactory-from-startboard]

不論是哪一種方式，您都會看到所選 Data Factory 的 [DATA FACTORY]**** 刀鋒視窗，如下圖所示。 

 ![Data Factory Home Page][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> 檢視 Data Factory 的圖表檢視
在 Data Factory 的 [DATA FACTORY]**** 刀鋒視窗中，按一下 [圖表]**** 磚，即可查看該 Data Factory 的圖表檢視。 

![Data Factory Diagram View][image-data-factory-diagram-view]
 

## <a name="DataFactoryLinkedServices"></a> 檢視 Data Factory 中的連結服務
在 Data Factory 的 [DATA FACTORY]**** 刀鋒視窗中，按一下 [連結的服務]**** 磚，即可查看清單中所有連結的服務。 

![Linked Services Blade][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> 檢視連結服務的詳細資料
在 [連結的服務]**** 刀鋒視窗中，按一下清單中的連結服務，即可查看其詳細資料。 

![Linked Service Blade][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> 檢視 Data Factory 中的資料集 
在 Data Factory 的 [DATA FACTORY]**** 刀鋒視窗中，按一下 [資料集]**** 磚，即可查看 Data Factory 中的所有資料表。

![Data Sets Blade][image-data-factory-datasets] 

## <a name="DataFactoryDataset"></a>  檢視資料集的詳細資料
按一下 [資料集] 分頁上資料集清單中的資料集，即可查看資料集的詳細資料。請注意，資料表是具有結構描述的矩形資料集。這是目前唯一支援的資料集類型。 

![Table Blade][image-data-factory-table]

在上述的 [資料表]**** 刀鋒視窗中，您會看到 [最近的配量]**** 和 [問題配量]****。按一下 [...]**** (省略符號)，即可查看所有配量。 

![All Slices of a Table][image-data-factory-all-slices]

在 [資料配量]**** 刀鋒視窗中，按一下 [篩選] 按鈕以查看 [篩選] 刀鋒視窗，此視窗可讓您「篩選」****配量來查看您想要檢閱的特定配量。

![Filter Blade][image-data-factory-filter-blade]


啟動 [篩選]**** 刀鋒視窗時，[到]**** 欄位會自動設為最近的時間 (四捨五入) 來限制傳回的記錄數目。[從]**** 欄位也會自動一併設定。您可以按一下 [行事曆]**** 按鈕來變更 [從]****日期。變更 [從]**** 日期時，[到]**** 日期也會自動變更。 

您可以按一下 [上一個]****/[下一個]**** 按鈕，來檢視上一段期間/下一段期間的配量。[上一個]**** 和 [下一個]**** 按鈕的時間範圍是根據配量頻率和間隔來設定的，如下表所示。

頻率 | 間隔值範圍 | 產生的時間區塊
----------| -------------------- | --------------------
分鐘 | 1-4 | 6 小時
分鐘 | 5-29 | 1 天
分鐘 | 30-180 | 7 天
分鐘 | 180+ | 28 天 (大約。行事曆月份)
小時 | 1-3 | 7 天
小時 | 4-11 | 28 天 (大約。行事曆月份)
小時 | 12-72 | 182 天 (大約。6 個月)
小時 | 73+ | 1 年
天 | 1-6 | 1 年
天 | 7-20 | 5 年
天 | 21+ | 10 年
週 | 1-3 | 5 年
週 | 4+ | 10 年
月 | 任一 | 10 年
 
例如，如果您將 [頻率]**** 定義為 [小時]****，並將 [間隔]**** 定義為 **2**，按一下 [下一個]****/[上一個]**** 按鈕時，就會將時間範圍往其中一個方向移動「7 天」****。不論您檢視的是所有配量、最近的配量或問題配量，此邏輯皆適用於 [篩選] 刀鋒視窗。

[篩選]**** 刀鋒視窗可讓您根據配量的 [狀態]**** 來篩選配量。下表提供所有配量狀態及其描述。
 
配量狀態 | 描述
------------ | ------------
PendingExecution | 尚未開始資料處理
InProgress | 正在進行資料處理
Ready | 已經完成資料處理，並且已經備妥資料配量。
Failed | 產生配量的執行失敗。
Skip | 略過配量處理。
Retry | 正在重新嘗試產生配量的執行。
Timed Out | 配量的資料處理逾時。
PendingValidation | 資料配量正在於開始處理之前，等待依照驗證原則進行驗證。
RetryValidation | 正在重新嘗試配量驗證。
FailedValidation | 配量驗證失敗。
LongRetry | 如果已在資料表 JSON 中指定 LongRetry，而且配量的一般重試失敗，配量便會處於這個狀態。
ValidationInProgress | 正在執行配量驗證 (根據資料表 JSON 中定義的原則) 。



## <a name="DataFactorySlice"></a> 檢視配量的詳細資料
按一下 [資料表]**** 刀鋒視窗或 [資料配量]**** 刀鋒視窗上配量清單中的配量，即可查看該配量的詳細資料。   

![Data Slice][image-data-factory-dataslice]


### <a name="DataFactoryActivtyRuns"></a> 檢視配量的所有活動執行
一個配量可有多次執行。例如，配量失敗時，服務可能會重試幾次。您也可以重新執行所有重試失敗的配量。您可在底端清單中看到 [資料配量]**** 刀鋒視窗上的所有活動執行。 

## <a name="DataFactoryActivtyRunDetails"></a>  檢視活動執行的詳細資料
從 [資料配量]**** 刀鋒視窗上的執行清單按一下活動執行，即可查看該活動執行的詳細資料。 

![Activity Run Details][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> 作業透鏡 - 過去一週的事件
在 Data Factory 的 [DATA FACTORY]**** 刀鋒視窗 (或首頁) 中，按一下 [作業]**** 透鏡中的 [過去一週的事件]****，即可查看過去一週的事件。這可協助您取得 Data Factory 在過去一週所執行之作業的高階檢視。同時也可協助您對移動/處理資料時發生的任何錯誤進行疑難排解。 

![ Data Factory Events][image-data-factory-events]


## 另請參閱

文章 | 描述
------ | ---------------
[使用 PowerShell 來監視和管理 Azure Data Factory][monitor-manage-using-powershell] | 本文說明如何使用 Azure PowerShell Cmdlet 來監視 Azure Data Factory。
[讓您的管線能夠與內部部署資料搭配使用][use-onpremises-datasources] | 本文提供逐步解說，示範如何將資料從內部部署 SQL Server 資料庫複製到 Azure Blob。
[使用 Pig 和 Hive 搭配 Data Factory][use-pig-and-hive-with-data-factory] | 本文提供逐步解說，示範如何使用「HDInsight 活動」來執行 hive/pig 指令碼，以處理輸入資料來產生輸出資料。
[教學課程：使用 Data Factory 移動及處理記錄檔][adf-tutorial] | 本文提供端對端逐步解說，展示如何使用 Azure Data Factory 實作近乎真實世界的案例，將記錄檔中的資料轉換為見解。
[在 Data Factory 中使用自訂活動][use-custom-activities] | 本文提供逐步解說，內含建立自訂活動並在管線中使用此活動的逐步指示。
[Data Factory 問題疑難排解][troubleshoot] | 本文說明如何對 Azure Data Factory 問題進行疑難排解。
[Azure Data Factory 開發人員參考][developer-reference] |＜開發人員參考＞提供 Cmdlet、JSON 指令碼、函式等等的完整參考內容。 
[Azure Data Factory Cmdlet 參考][cmdlet-reference] | 本參考內容提供所有 **Data Factory Cmdlet** 的相關詳細資料。


[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-filter-blade]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade.png

[image-data-factory-browse-everything]: ./media/data-factory-monitor-manage-using-management-portal/BrowseEverything.png

[image-data-factory-browse-datafactories]: ./media/data-factory-monitor-manage-using-management-portal/BrowseDataFactories.png

[image-data-factory-datafactories-blade]: ./media/data-factory-monitor-manage-using-management-portal/DataFactories.png

[image-data-factory-datafactory-from-startboard]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryFromStartboard.png

[image-data-factory-datafactory-home-page]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryHomePage.png

[image-data-factory-diagram-view]: ./media/data-factory-monitor-manage-using-management-portal/DiagramView.png

[image-data-factory-linked-services]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServicesBlade.png

[image-data-factory-linked-service]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServiceBlade.png

[image-data-factory-datasets]: ./media/data-factory-monitor-manage-using-management-portal/Datasets.png

[image-data-factory-table]: ./media/data-factory-monitor-manage-using-management-portal/Table.png

[image-data-factory-all-slices]: ./media/data-factory-monitor-manage-using-management-portal/AllSlices.png

[image-data-factory-filter]: ./media/data-factory-monitor-manage-using-management-portal/Filter.png

[image-data-factory-dataslice]: ./media/data-factory-monitor-manage-using-management-portal/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-monitor-manage-using-management-portal/ActivityRunDetails.png

[image-data-factory-events]: ./media/data-factory-monitor-manage-using-management-portal/Events.png

<!--HONumber=35.2-->
