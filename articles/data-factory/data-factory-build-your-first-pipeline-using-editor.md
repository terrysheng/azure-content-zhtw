<properties
	pageTitle="使用 Data Factory 編輯器建置您的第一個 Azure Data Factory 管線。"
	description="在本教學課程中，您將使用 Azure 入口網站中的 Data Factory 編輯器，建立範例 Azure Data Factory 管線。"
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
	ms.date="10/06/2015"
	ms.author="spelluru"/>

# 使用 Data Factory 編輯器 (Azure 入口網站) 建置您的第一個 Azure Data Factory 管線。
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


在本文中，您將了解如何使用 [Azure Preview 入口網站](https://portal.azure.com/)來建立您的第一個管線。本教學課程包含下列步驟：

1.	建立 Data Factory
2.	建立連結服務 (資料存放區、運算) 和資料集
3.	建立管線

本文不提供 Azure Data Factory 服務的概念性概觀。如需有關服務的詳細概觀，請參閱 [Azure Data Factory 簡介](data-factory-introduction.md)一文。

> [AZURE.IMPORTANT]執行本教學課程之前，請先閱讀[教學課程概觀](data-factory-build-your-first-pipeline.md)一文並完成先決條件步驟。

## 步驟 1：建立 Data Factory

1.	登入 [Azure Preview 入口網站](http://portal.azure.com/)之後，執行下列動作：
	1.	按一下左側功能表的 [新增]。 
	2.	按一下 [建立] 刀鋒視窗中的 [資料分析]。
	3.	按一下 [資料分析] 刀鋒視窗上的 [Data Factory]。

		![建立刀鋒視窗](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	在 [新增 Data Factory] 刀鋒視窗中，輸入 **DataFactoryMyFirstPipeline** 做為 [名稱]。

	![新增 Data Factory 刀鋒視窗](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT]Azure Data Factory 名稱必須是全域唯一的。您必須在 Data Factory 的名稱前面加上您的名稱，才能成功建立 Factory。 
3.	如果您尚未建立任何資源群組，您必須建立資源群組。作法：
	1.	按一下 [**資源群組名稱**]。
	2.	在 [**資源群組**] 刀鋒視窗中，選取 [**建立新的資源群組**]。
	3.	在 [**建立資源群組**] 刀鋒視窗中，輸入 **ADF** 做為 [**名稱**]。
	4.	按一下 [確定]。
	
		![建立資源群組](./media/data-factory-build-your-first-pipeline-using-editor/create-resource-group.png)
4.	選取資源群組之後，請確認您使用的是要在其中建立 Data Factory 的正確訂用帳戶。
5.	按一下 [**新增 Data Factory**] 刀鋒視窗上的 [**建立**]。
6.	您會看到 Data Factory 建立在 Azure Preview 入口網站的 [**開始面板**] 中，如下所示：   

	![建立 Data Factory 狀態](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. 恭喜！ 您已成功建立您的第一個 Data Factroy。在 Data Factory 成功建立後，您會看到 Data Factory 頁面，顯示 Data Factory 的內容。 	

	![Data Factory 刀鋒視窗](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

在後續步驟中，您將了解如何建立將在本教學課程使用的連結服務、資料集及管線。

## 步驟 2：建立連結服務和資料集
在此步驟中，您會將您的 Azure 儲存體帳戶和隨選 Azure HDInsight 叢集連結至 Data Factory，然後建立資料集以表示由 Hive 處理的輸出資料。

### 建立 Azure 儲存體連結服務
1.	在 **DataFactoryFirstPipeline** 的 [**DATA FACTORY**] 刀鋒視窗中按一下 [**製作和部署**]。這會啟動 Data Factory 編輯器。 
	 
	![[製作和部署] 磚](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	按一下 [**新增資料存放區**] 並選擇 [**Azure 儲存體**]
	
	![Azure 儲存體連結服務](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

	在編輯器中，您應該會看到用來建立 Azure 儲存體連結服務的 JSON 指令碼。 
4. 使用您的 Azure 儲存體帳戶名稱取代 **account name**，並使用 Azure 儲存體帳戶的存取金鑰取代 **account key**。若要了解如何取得儲存體存取金鑰，請參閱[檢視、複製和重新產生儲存體存取金鑰](../storage/storage-create-storage-account.md/#view-copy-and-regenerate-storage-access-keys)
5. 按一下命令列的 [部署]，部署連結服務。

	![[部署] 按鈕](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

### 建立 Azure HDInsight 連結服務
現在，您將為用來執行 Hive 指令碼的隨選 HDInsight 叢集建立連結服務。

1. 在 [**Data Factory 編輯器**] 中，按一下命令列上的 [**新增運算**]，然後選取 [**隨選 HDInsight 叢集**]。

	![新增運算](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. 複製下列程式碼片段並貼到 Draft-1 視窗。此 JSON 程式碼片段描述將用來建立隨選 HDInsight 叢集的屬性。 

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "jobsContainer": "adfjobs",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}
	
	下表提供程式碼片段中所使用之 JSON 屬性的描述：
	
	屬性 | 說明
	-------- | -----------
	版本 | 指定所建立之 HDInsight 的版本為 3.1。 
	ClusterSize | 建立一個單一節點的 HDInsight 叢集。 
	TimeToLive | 指定 HDInsight 叢集在被刪除之前的閒置時間。
	JobsContainer | 指定將建立來儲存 HDInsight 產生之記錄檔的工作容器的名稱
	linkedServiceName | 指定將用來儲存 HDInsight 產生之記錄檔的儲存體帳戶
3. 按一下命令列的 [部署]，部署連結服務。 
4. 確認您有在左側的樹狀檢視中看到 StorageLinkedService 和 HDInsightOnDemandLinkedService。

	![含連結服務的樹狀檢視](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)
 
### 建立輸出資料集
現在，您將建立輸出資料集來代表 Azure Blob 儲存體中儲存的資料。

1. 在 [**Data Factory 編輯器**] 中，按一下命令列的 [**新增資料集**]，然後選取 [**Azure Blob 儲存體**]。  

	![新增資料集](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. 複製下列程式碼片段並貼到 Draft-1 視窗。在 JSON 程式碼片段中，建立名為 **AzureBlobOutput** 的資料集，並指定將由 Hive 指令碼產生的資料結構。此外，指定結果儲存在名為 **data** 的 blob 容器及名為 **partitioneddata** 的資料夾中。**availability** 區段指定每個月產生一次輸出資料集。
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "data/partitioneddata",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Month",
		      "interval": 1
		    }
		  }
		}

3. 按一下命令列的 [**部署**] 以部署新建立的資料集。
4. 驗證資料集已成功建立。

	![含連結服務的樹狀檢視](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## 步驟 3：建立您的第一個管線
在此步驟中，您將建立您的第一個管線。

1. 在 [**Data Factory 編輯器**] 中，按一下 [**…**] (省略符號) 然後按一下 [**新增管線**]。
	
	![新增管線按鈕](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. 複製下列程式碼片段並貼到 Draft-1 視窗。

	> [AZURE.IMPORTANT]在 JSON 中使用您的儲存體帳戶名稱取代 **storageaccountname**。

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "StorageLinkedService",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
		        "policy": {
		          "concurrency": 1,
		          "retry": 3
		        },
		        "name": "RunSampleHiveActivity",
		        "linkedServiceName": "HDInsightOnDemandLinkedService"
		      }
		    ],
		    "start": "2014-01-01",
		    "end": "2014-01-02"
		  }
		}
 
	您會在 JSON 程式碼片段中建立一個管線，其中包括在 HDInsight 叢集上使用 Hive 處理「資料」的單一活動。
	
	Hive 指令碼檔案 (**partitionweblogs.hql**) 儲存於 Azure 儲存體帳戶 (透過 scriptLinkedService 指定，名為 **StorageLinkedService**) 且在容器中名為 **script**。

	**extendedProperties** 區段用於指定執行階段設定，該設定將會傳遞到 hive 指令碼做為 Hive 組態值 (例如 ${hiveconf:PartitionedData})。

	管線的 **start** 和 **end** 屬性會指定管線的作用中期間。

	在活動 JSON 中，您指定 Hive 指令碼在透過連結的服務 – **HDInsightOnDemandLinkedService** 所指定的運算上執行。
3. 按一下命令列上的 [部署]，部署管線。
4. 確認您在樹狀檢視中看到管線。

	![管線的樹狀檢視](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. 恭喜，您已經成功建立您的第一個管線！
6. 按一下 **X** 關閉 [Data Factory 編輯器] 刀鋒視窗，並瀏覽回 [Data Factory] 刀鋒視窗，然後按一下 [**圖表**]。
  
	![[圖表] 磚](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. 在 [圖表檢視] 中，您會看到管線的概觀，以及在本教學課程中使用的資料集。
	
	![圖表檢視](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. 在 [圖表檢視] 中，按兩下 **AzureBlobOutput** 資料集。您會看到目前正在處理的配量。

	![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. 當處理完成時，您會看到配量處於 [**就緒**] 狀態。請注意，建立隨選 HDInsight 叢集通常需要一些時間。 

	![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)	
10. 當配量處於 [**就緒**] 狀態時，檢查您 blob 儲存體中 **data** 容器內 **partitioneddata** 資料夾的輸出資料。  
 

 

## 後續步驟
在本文中，您已經建立可在隨選 HDInsight 叢集上執行 Hive 指令碼，含有轉換活動 (HDInsight 活動) 的管線。若要了解如何使用「複製活動」從 Azure Blob 複製資料到 Azure SQL，請參閱[教學課程：從 Azure Blob 複製資料到 Azure SQL](./data-factory-get-started.md)。
  

## 傳送意見
非常感謝您對本文的意見反應。請花幾分鐘的時間透過[電子郵件](mailto:adfdocfeedback@microsoft.com?subject=data-factory-build-your-first-pipeline-using-editor.md)提交您的意見反應。

<!---HONumber=Oct15_HO3-->