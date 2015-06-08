<properties 
	pageTitle="使用 Azure 預覽入口網站監視和管理 Azure Data Factory" 
	description="了解如何使用 Azure Management Portal 監控和管理您建立的 Azure 資料處理站。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# 使用 Azure 預覽入口網站監視 Azure Data Factory
本文說明各種案例使用 Azure 預覽入口網站來監視和管理 Azure 資料處理站。

## <a name="AllDataFactories"></a> 在 Azure 訂用帳戶中檢視所有資料處理站

- 登入 [Azure 預覽入口網站][azure-preview-portal]。
- 按一下 **瀏覽** 左邊，然後按一下 [中樞 **資料 factory**。  

	![瀏覽中樞]-> [資料處理站][image-data-factory-browse-datafactories]

	如果看不到 **資料 factory**, ，按一下 **一切** ，然後按一下 [ **資料 factorries** 中 **瀏覽** 分頁。

	![瀏覽中樞-> 所有項目][image-data-factory-browse-everything]

- 您應該會看到所有資料工廠 **資料 factory** 分頁。

	![資料處理站分頁][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> 檢視的資料處理站的詳細資料

若要檢視 Data Factory 的詳細資料，請執行下列其中一項：


- 按一下中的資料處理站 **資料 factory** 刀鋒如上所示。
- 按一下連結資料 factory **儀表板**。**儀表板** 是的分頁，您會看到當您登入在 Azure 預覽入口網站。如果您已選取 **新增到儀表板** 時建立的資料處理站 (預設選項)，您應該會看到資料 factory 儀表板上的連結如下圖所示。在此範例中， **ADFTutorialDataFactory**, ，**ADFTutorialDataFactoryDF** 和 **LogProcessingFactory** 資料 factory 連結適用於 **儀表板**。


![從開始面板] 的資料處理站][image-data-factory-datafactory-from-startboard]

無論如何，您會看到 **資料 FACTORY** 分頁之選取的資料處理站，如下圖所示。

 ![資料處理站首頁][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> 檢視資料 factory 的圖表檢視
在 **資料 FACTORY** 刀鋒伺服器的資料處理站中，按一下 **圖表** 方塊，查看圖表檢視的資料處理站。

![資料處理站圖表檢視][image-data-factory-diagram-view]
 
### 在 [圖表] 檢視中開啟管線
您可以在管線中檢視的所有活動，以滑鼠右鍵按一下 [圖表] 都檢視中的管線，然後按一下 **開放的管線**。您應該會看到活動的輸入和輸出資料集以及管線中的活動。 ![開啟管線](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

按一下 **資料 factory** 軌跡中若要回到 [圖表] 檢視的左上角。[圖表] 檢視會顯示所有的管線。在此範例中，您只建立了一個管線。

## <a name="DataFactoryLinkedServices"></a> 檢視連結的服務中的資料處理站
在 **資料 FACTORY** 刀鋒伺服器的資料處理站中，按一下 **連結服務** 方塊，查看所有連結的服務清單中。

![連結的服務分頁][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> 檢視連結的服務的詳細資料
在 **連結服務** blade 中，按一下連結的服務清單，以查看詳細。

![連結的服務分頁][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> 檢視中的資料處理站的資料集 
在 **資料 FACTORY** 刀鋒伺服器的資料處理站中，按一下 **資料集** 方塊，查看資料 factory 中的所有資料表。

![資料集分頁][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> 檢視資料集的詳細資料
按一下 [資料集] 分頁上資料集清單中的資料集，即可查看資料集的詳細資料。請注意，資料表是具有結構描述的矩形資料集。它是目前唯一支援的資料集類型。

![資料表索引標籤][image-data-factory-table]

在 **資料表** 上述兩者的刀鋒 **最近更新配量** 和 **最近無法配量** 清單會依照 **上次更新時間**。配量的更新時間是在下列情況中變更。

-  您扇區的狀態手動更新，例如，使用 **組 AzureDataFactorySliceStatus** (或) 依序按一下 **執行** 上 **配量** 配量的分頁。
-  配量因為執行的狀態變更 (例如啟動測試回合、 結束，而且無法執行、 測試回合結束，而且已成功，等等)。
 
	
若要檢視的資料配量，而是依照配量的開始/結束時間，請按一下 **(藉由配量的時間) 的資料配量** 並排顯示。
 
![依時間配量的資料配量][DataSlicesBySliceTime]

按一下標題的清單或 **...(橢圓形)** 若要查看較大配量的清單。

![資料表的所有配量][image-data-factory-all-slices]

在 **資料配量** blade 中，按一下 **篩選** 按鈕，查看 **篩選** 分頁，可讓您 **篩選** 配量，以查看您想要檢閱的特定配量。當您按一下時，您會看到類似下列其中一個分頁 **篩選** 上 **資料配量** 與配量的刀鋒 **以更新時間排序**。

![篩選索引標籤][image-data-factory-filter-blade]

 **篩選** 刀鋒伺服器可讓您篩選根據 **上次更新時間** 和 **配量狀態**。下表描述所有的扇區狀態和其描述。
 
配量狀態 | 說明
------------ | ------------
PendingExecution | 資料處理尚未啟動。
InProgress | 資料處理進行中。
Ready | 資料處理已完成，而且資料配量就緒。
Failed | 產生配量的執行無法執行。
Skip | 略過配量處理。
Retry | 重試產生配量的執行。
Timed Out | 配量的資料處理逾時。
PendingValidation | 在開始處理之前，資料配量正在等候依照驗證原則進行驗證。
RetryValidation | 重試配量的驗證。
FailedValidation | 配量的驗證失敗。
LongRetry | 如果已在資料表 JSON 中指定 LongRetry，而且配量的一般重試失敗，則配量將會處於這個狀態。
ValidationInProgress | 正在執行配量的驗證 (根據 JSON 資料表中定義的原則) 。

當您按一下 **篩選** 上 **資料配量** 與配量的刀鋒 **配量時間所排序**, ，您會看到不同類型的 **篩選** 分頁。

![篩選刀鋒 2][image-data-factory-filter-blade-2]


當您啟動 **篩選** blade 中， **至** 欄位會自動設為最近一次 (無條件) 來限制傳回的記錄數目。 **從** 欄位會自動一併設定。您可以變更 **從** 按一下日期 **行事曆** ] 按鈕。 **至** 變更時，會自動變更日期 **從** 日期。

您可以按一下 **Previous**/ * * 下一步 * * 按鈕來檢視在前一個期間/下一段時間配量。時間範圍 **Previous** 和 **下一步** 按鈕設定根據配量頻率和下表所示的間隔。

頻率 | 間隔值範圍 | 產生時間區塊
----------| -------------------- | --------------------
分鐘 | 1-4 | 6 小時
分鐘 | 5-29 | 1 天
分鐘 | 30-180 | 7 天
分鐘 | 180 + | 28 天 (大約。 日曆月)
小時 | 1-3 | 7 天
小時 | 4-11 | 28 天 (大約。 日曆月)
小時 | 12 72 | 182 天 (大約。6 個月)
小時 | 73 + | 1 年
一天 | 1-6 | 1 年
一天 | 7-20 | 5 年
一天 | 21 + | 十年
週 | 1-3 | 5 年
週 | 4 + | 十年
月份 | 任何 | 十年
 
例如，如果您定義 **頻率** 為 **小時** 和 **間隔** 的 **2**, 、 **下一步**/ * * 上一步 * * 按鈕移動時間範圍 **7 天** 任一方向。不論您檢視的是所有配量、最近的配量或問題配量，此邏輯皆適用於 [篩選] 刀鋒視窗。




## <a name="DataFactorySlice"></a> 檢視配量的詳細資料
中，按一下 [在清單中的配量配量上是 **資料表** 分頁或 **資料配量** 分頁，若要查看有關該扇區的詳細資料。

![資料配量][image-data-factory-dataslice]

如果配量不在 **準備** 狀態，您可以看到，未就緒並封鎖目前的配量，從執行中的上游配量 **上游未就緒的配量** 清單。

### <a name="DataFactoryActivtyRuns"></a> 檢視所有的活動執行配量
一個配量可有多次執行。例如，配量失敗時，服務可能會重試幾次。您也可以重新執行所有重試失敗的配量。您可以看到所有在活動執行上 * * 下方清單中的資料配量 * * 分頁。

## <a name="DataFactoryActivtyRunDetails"></a> 檢視執行的活動詳細資料
按一下從清單中的執行上執行某個活動 **資料配量** 分頁以查看執行活動的相關詳細資料。

![活動執行詳細資料][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> 作業鏡頭過去一週中的事件
在 **資料 FACTORY** 分頁 (或首頁)，資料處理站按一下 **過去一週中的事件** 中 **作業** 透鏡以查看在過去一週中的事件。這可協助您取得 Data Factory 在過去一週所執行之作業的高階檢視。它也可協助您對移動/處理資料時發生的任何錯誤進行疑難排解。

![資料處理站事件][image-data-factory-events]


## 另請參閱

文章 | 說明
------ | ---------------
[監視和管理 Azure 資料處理站使用 PowerShell][monitor-manage-using-powershell] | 這篇文章描述如何監視使用 Azure PowerShell cmdlet 的 Azure 資料處理站。 


[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-filter-blade]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade.png

[image-data-factory-filter-blade-2]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade2.png


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
[DataSlicesBySliceTime]: ./media/data-factory-monitor-manage-using-management-portal/DataSlicesBySliceTime.png

<!---HONumber=GIT-SubDir-->