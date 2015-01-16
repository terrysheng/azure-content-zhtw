<properties title="Monitor and manage Azure Data Factory using Azure Preview Portal" pageTitle="使用 Azure 預覽入口網站監視和管理 Azure Data Factory" description="了解如何使用 Azure Management Portal 監控和管理您建立的 Azure 資料處理站。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# 使用 Azure 預覽入口網站監視 Azure Data Factory

- [檢視 Azure 訂閱中的所有 Data Factory](#AllDataFactories)
- [檢視 Data Factory 的詳細資料](#DataFactoryDetails)
- [檢視 Data Factory 的圖表檢視](#DataFactoryDiagram)
- [檢視 Data Factory 中的連結服務](#DataFactoryLinkedServices)
- [檢視連結服務的詳細資料](#DataFactoryLinkedService) 
- [檢視 Data Factory 中的資料集](#DataFactoryDatasets)
- [檢視資料集的詳細資料](#DataFactoryDataset)
- [檢視配量的詳細資料](#DataFactorySlice) 
- [檢視配量的所有活動執行](#DataFactoryActivtyRuns) 
- [檢視活動執行的詳細資料](#DataFactoryActivtyRunDetails)
- [作業鏡頭 - 過去一週的事件](#EventsInThePastweek)  
   


## <a name="AllDataFactories"></a> 檢視 Azure 訂閱中的所有 Data Factory

- 登入 [Azure 預覽入口網站][azure-preview-portal].
- 按一下左邊的 [**瀏覽**] 中心，然後按一下 [**Data Factory**]。  

	![BROWSE hub -> Data Factories][image-data-factory-browse-datafactories]

	如果看不到 **Data Factory**，請按一下 [**全部內容**]，然後按一下 [**瀏覽**] 分頁中的 [**Data Factory**]。

	![BROWSE hub -> Everything] [image-data-factory-browse-everything]

- 您應該在 [**Data Factory**] 分頁中看到所有 Data Factory。

	![Data factories blade][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> 檢視 Data Factory 的詳細資料

若要檢視 Data Factory 的詳細資料，請執行下列其中一項： 


- 按一下上面顯示之 [**Data Factory**] 分頁中的 Data Factory。
- 按一下 [**開始面板**] 上的 Data Factory。**開始面板**是您在登入 Azure 預覽入口網站時看到的分頁。如果您在建立 Data Factory 時選取了 [**Add to Startboard**] (預設選項)，則應該在開始面板上看到 Data Factory 連結，如下圖所示。在此範例中，**開始面板**提供有 **ADFTutorialDataFactory**、**ADFTutorialDataFactoryDF** 和 **LogProcessingFactory** Data Factory 連結。


![Data factory from the Startboard][image-data-factory-datafactory-from-startboard]

無論如何，您都會看到所選取 Data Factory 的 **DATA FACTORY** 分頁，如下圖所示。 

 ![Data Factory Home Page][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> 檢視 Data Factory 的圖表檢視
在 Data Factory 的 [**DATA FACTORY**] 分頁中，按一下 [**圖表**] 圖磚，即可查看 Data Factory 的圖表檢視。 

![Data Factory Diagram View][image-data-factory-diagram-view]
 

## <a name="DataFactoryLinkedServices"></a> 檢視 Data Factory 中的連結服務
在 Data Factory 的 [**DATA FACTORY**] 分頁中，按一下 [**連結的服務**] 圖磚，即可查看清單中所有連結的服務。 

![Linked Services Blade][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> 檢視連結服務的詳細資料
在 [**連結的服務**] 分頁中，按一下清單中連結的服務，即可查看其詳細資料。 

![Linked Service Blade][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> 檢視 Data Factory 中的資料集 
在 Data Factory 的 [**DATA FACTORY**] 分頁中，按一下 [**資料集**] 圖磚，即可查看 Data Factory 中的所有資料表。

![Data Sets Blade][image-data-factory-datasets] 

## <a name="DataFactoryDataset"></a>  檢視資料集的詳細資料
按一下 [資料集] 分頁上資料集清單中的資料集，即可查看資料集的詳細資料。請注意，資料表是具有結構描述的矩形資料集。它是目前唯一支援的資料集類型。 

![Table Blade][image-data-factory-table]

在上述的 [**表格**] 分頁中，您會看到 [**最新的配量**] 和 [**問題配量**]。按一下 [**...(省略符號)**，即可看到所有配量。 

![All Slices of a Table][image-data-factory-all-slices]

在 [**資料配量**] 分頁上，您可以使用篩選器以查看您想要檢閱的特定配量。


## <a name="DataFactorySlice"></a> 檢視配量的詳細資料
按一下 [**資料表**] 分頁或 [**資料配量**] 分頁上配量清單中的配量，即可查看該配量的詳細資料。 

![Data Slice][image-data-factory-dataslice]


### <a name="DataFactoryActivtyRuns"></a> 檢視配量的所有活動執行
一個配量可有多次執行。例如，配量失敗時，服務可能會重試幾次。您也可以重新執行所有重試失敗的配量。您可在底端清單中看到 [**資料配量**] 分頁上的所有活動執行。 

## <a name="DataFactoryActivtyRunDetails"></a>  檢視活動執行的詳細資料
從 [**資料配量**] 分頁上的執行清單按一下活動執行，即可查看該活動執行的詳細資料。 

![Activity Run Details][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> 作業鏡頭 - 過去一週的事件
在 Data Factory 的 [**DATA FACTORY**] 分頁 (或首頁) 中，按一下 [**作業**鏡片]中的 [**過去一週的事件**，即可查看過去一週的事件。這可協助您取得 Data Factory 在過去一週所執行之作業的高階檢視。它也可協助您對移動/處理資料時發生的任何錯誤進行疑難排解。 

![ Data Factory Events][image-data-factory-events]


## 另請參閱

文章 | 描述
------ | ---------------
[使用 PowerShell 監視和管理 Azure Data Factory][monitor-manage-using-powershell] | 本文描述如何使用 Azure PowerShell Cmdlet 監視 Azure Data Factory。 
[可讓您的管線使用內部部署資料][use-onpremises-datasources] | 本文具有逐步解說，展示如何將資料從內部部署 SQL Server 資料庫複製至 Azure Blob。
[使用 Pig 和 Hive 搭配 Data Factory][use-pig-and-hive-with-data-factory] | 本文具有逐步解說，展示如何使用 HDInsight 活動，執行 hive/pig 指令碼來處理輸入資料，以產生輸出資料。 
[教學課程：使用 Data Factory 來移動並處理記錄檔][adf-tutorial] | 本文提供端對端逐步解說，展示如何使用 Azure Data Factory 實作近乎真實世界的案例，將記錄檔中的資料轉換為見解。
[在 Data Factory 中使用自訂活動][use-custom-activities] | 本文提供逐步解說，逐步指示您建立自訂活動，並在管線中使用它。 
[對 Data Factory 問題進行疑難排解][troubleshoot] | 本文描述如何對 Azure Data Factory 問題進行疑難排解。
[Azure Data Factory 開發人員參考][developer-reference] | 「開發人員參考」具有 Cmdlet、JSON 指令碼、函數等等的完整參考內容。 
[Azure Data Factory Cmdlet 參考][cmdlet-reference] | 本參考內容詳述所有 **Data Factory Cmdlet**。


[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

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
