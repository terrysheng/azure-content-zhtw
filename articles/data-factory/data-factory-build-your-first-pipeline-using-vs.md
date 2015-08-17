<properties
	pageTitle="使用 Azure Data Factory 建置您的第一個管線"
	description="本教學課程示範如何利用 Visual Studio 建立使用 Azure HDInsight 轉換資料的範例資料管線"
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
	ms.topic="get-started-article" 
	ms.date="07/27/2015"
	ms.author="spelluru"/>

# 使用 Azure Data Factory 建置您的第一個管線
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


在本文中，您將了解如何使用 Visual Studio 來建立您的第一個管線。本教學課程包含下列步驟：

1.	建立 Data Factory
2.	建立連結服務 (資料存放區、運算) 和資料集
3.	建立管線

本文不提供 Azure Data Factory 服務的概念性概觀。如需有關服務的詳細概觀，請參閱 [Azure Data Factory 簡介](data-factory-introduction.md)一文。

## 步驟 1：建立 Data Factory

1.	登入 [Azure Preview 入口網站](http://portal.azure.com/)之後，執行下列動作：
	1.	按一下左下角的 [新增]。 
	2.	按一下 [建立] 刀鋒視窗中的 [資料分析]。
	3.	按一下 [資料分析] 刀鋒視窗上的 [Data Factory]。

		![建立刀鋒視窗](./media/data-factory-build-your-first-pipeline-using-vs/create-blade.png)

2.	在 [新增 Data Factory] 刀鋒視窗中，輸入 **DataFactoryMyFirstPipeline** 做為 [名稱]。

	![新增 Data Factory 刀鋒視窗](./media/data-factory-build-your-first-pipeline-using-vs/new-data-factory-blade.png)

	> [AZURE.IMPORTANT]Azure Data Factory 名稱必須是全域唯一的。您必須在 Data Factory 的名稱前面加上您的名稱，才能成功建立 Factory。 
3.	如果您尚未建立任何資源群組，您必須建立資源群組。作法：
	1.	按一下 [**資源群組名稱**]。
	2.	在 [**資源群組**] 刀鋒視窗中，選取 [**建立新的資源群組**]。
	3.	在 [**建立資源群組**] 刀鋒視窗中，輸入 **ADF** 做為 [**名稱**]。
	4.	按一下 [確定]。
	
		![建立資源群組](./media/data-factory-build-your-first-pipeline-using-vs/create-resource-group.png)
4.	選取資源群組之後，請確認您使用的是要在其中建立 Data Factory 的正確訂用帳戶。
5.	按一下 [**新增 Data Factory**] 刀鋒視窗上的 [**建立**]。
6.	您會看到 Data Factory 建立在 Azure Preview 入口網站的 [**開始面板**] 中，如下所示：   

	![建立 Data Factory 狀態](./media/data-factory-build-your-first-pipeline-using-vs/creating-data-factory-image.png)
7. 恭喜！ 您已成功建立您的第一個 Data Factroy。在 Data Factory 成功建立後，您會看到 Data Factory 頁面，顯示 Data Factory 的內容。 	

	![Data Factory 刀鋒視窗](./media/data-factory-build-your-first-pipeline-using-vs/data-factory-blade.png)

在後續步驟中，您將了解如何建立將在本教學課程使用的連結服務、資料集及管線。

## 逐步解說：使用 Visual Studio 建立及部署 Data Factory 實體 

### 必要條件

您必須在電腦上安裝下列項目：- Visual Studio 2013 - 下載 Azure SDK for Visual Studio 2013。瀏覽至 [Azure 下載頁面](http://azure.microsoft.com/downloads/) 並按一下 [**.NET**] 區段中的 [**VS 2013 安裝**]。


### 建立 Visual Studio 專案 
1. 啟動 **Visual Studio 2013**。按一下 [**檔案**]，指向 [**新增**]，然後按一下 [**專案**]。您應該會看到 [**新增專案**] 對話方塊。  
2. 在 [**新增專案**] 對話方塊中，選取 **DataFactory** 範本，然後按一下 [**空白 Data Factory 專案**]。如果您沒有看到 DataFactory 範本，請關閉 Visual Studio、安裝 Azure SDK for Visual Studio 2013，並重新開啟 Visual Studio。  

	![[新增專案] 對話方塊](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. 輸入專案的**名稱**、**位置**，和**解決方案**的名稱，然後按一下 [**確定**]。

	![Solution Explorer](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### 建立連結服務
在此步驟中，您會將您的 Azure 儲存體帳戶和隨選 Azure HDInsight 叢集連結至 Data Factory，然後建立資料集以表示由 Hive 處理的輸出資料。


#### 建立 Azure 儲存體連結服務


4. 以滑鼠右鍵按一下 [方案總管] 中的 [**連結服務**]，指向 [**新增**]，然後按一下 [**新增項目**]。      
5. 在 [**新增項目**] 對話方塊中，從清單選取 [**Azure 儲存體連結服務**]，然後按一下 [**新增**]。 

	![新的連結服務](./media/data-factory-build-your-first-pipeline-using-vs/new-linked-service-dialog.png)
 
3. 將 **accountname** 和 **accountkey** 取代為 Azure 儲存體帳戶和其金鑰的名稱。

	![Azure 儲存體連結服務](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. 儲存 **AzureStorageLinkedService1.json** 檔案。

#### 建立 Azure HDInsight 連結服務
現在，您將為用來執行 Hive 指令碼的隨選 HDInsight 叢集建立連結服務。

1. 以滑鼠右鍵按一下 [**方案總管**] 中的 [**連結服務**]，指向 [**新增**]，然後按一下 [**新增項目**]。
2. 選取 [**HDInsight 隨選連結服務**]，然後按一下 [**新增**]。 
3. 使用下列程式碼來取代 **JSON**：

		{
		    "name": "HDInsightOnDemandLinkedService",
		    "properties": {
		        "version": "3.1",
		        "clusterSize": 1,
		        "timeToLive": "00:05:00",
		        "jobsContainer": "adfjobs",
		        "linkedServiceName": "StorageLinkedService",
		        "type": "HDInsightOnDemandLinkedService"
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

4. 儲存 **HDInsightOnDemandLinkedService1.json** 檔案。
 
### 建立輸出資料集
現在，您將建立輸出資料集來代表 Azure Blob 儲存體中儲存的資料。

1. 以滑鼠右鍵按一下指向 [**方案總管**] 中的 [**新增**]，然後按一下 [**新增項目**]。 
2. 從清單中選取 [**Azure Blob**]，然後按一下 [**新增**]。 
3. 在編輯器中以下列方式取代 **JSON**：在 JSON 片段中，建立名為 **AzureBlobOutput** 的資料集，並指定將由 Hive 指令碼產生的資料結構。此外，指定結果儲存在名為 **data** 的 blob 容器及名為 **partitioneddata** 的資料夾中。**availability** 區段指定每個月產生一次輸出資料集。
	
		{
		    "name": "AzureBlobOutput",
		    "properties": {
		        "location": {
		            "type": "AzureBlobLocation",
		            "folderPath": "data/partitioneddata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            },
		            "linkedServiceName": "StorageLinkedService"
		        },
		        "availability": {
		            "frequency": "Month",
		            "interval": 1
		        }
		    }
		}

4. 儲存 **AzureBlobLocation1.json** 檔案。


### 建立您的第一個管線
在此步驟中，您將建立您的第一個管線。

1. 以滑鼠右鍵按一下 [**方案總管**] 中的 [**管線**]，指向 [**新增**]，然後按一下 [**新增項目**]。 
2. 從清單中選取 [**Hive 轉換管線**]，然後按一下 [**新增**]。 
3. 以下列程式碼片段取代 **JSON**，並使用您的儲存體帳戶名稱取代 **storageaccountname**。

		{
			"name": "MyFirstPipeline",
			"properties": {
			"description": "My first Azure Data Factory pipeline",
		 	"activities": [
		      {
		            "type": "HDInsightActivity",
		            "transformation": {
		                    "scriptPath": "script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "type": "Hive",
		                    "extendedProperties": {
		                        "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "outputs": [   {  "name": "AzureBlobOutput"    }   ],
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

	**extendedProperties** 區段用於指定執行階段設定，該設定將會傳遞到 hive 指令碼作為 Hive 設定值 (例如 ${hiveconf:PartitionedData})。

	管線的 **start** 和 **end** 屬性會指定管線的作用中期間。

	在活動 JSON 中，您指定 Hive 指令碼在透過連結的服務 – **HDInsightOnDemandLinkedService** 所指定的運算上執行。
3. 儲存 **HiveActivity1.json** 檔案。 

### 發佈/部署 Data Factory 實體
  
1. 在工具列區域中，如果 Data Factory 工具列尚未啟用，請按一下滑鼠右鍵並選取 [**Data Factory**] 加以啟用。 
19. 在 **Data Factory 工具列**中，按一下**下拉式方塊**以查看 Azure 訂用帳戶中的所有 Data Factory。如果您看到 [**登入 Visual Studio**] 對話方塊： 
	20. 輸入和您想要在其中建立 Data Factory 之 Azure 訂用帳戶相關聯的**電子郵件帳戶**，輸入**密碼**，然後按一下 [**登入**]。
	21. 一旦登入成功，您應該會看到 Azure 訂用帳戶中的所有 Data Factory。在本教學課程中，您將建立新的 Data Factory。       
22. 在下拉式清單中，選取 [**DataFactoryMyFirstPipeline**]，然後按一下 [**發佈**] 按鈕以部署/發佈連結服務、資料集和管線。    

	![發佈按鈕](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

23. 您應該會看到上圖所示之 [**Data Factory 工作清單**] 視窗中的發佈狀態。確認已成功發佈。


## 使用 [伺服器總管] 檢視 Data Factory 實體

1. 在 **Visual Studio** 中，按一下功能表上的 [**檢視**]，然後按一下 [**伺服器總管**]。
2. 在 [伺服器總管] 視窗中，依序展開 **Azure** 和 **Data Factory**。如果您看到**登入 Visual Studio**，請輸入和 Azure 訂用帳戶相連的**帳戶**，然後按一下 [**繼續**]。輸入**密碼**，然後按一下 [**登入**]。Visual Studio 會嘗試取得訂用帳戶中所有 Azure Data Factory 的相關資訊。您會在 [**Data Factory 工作清單**] 視窗中看到這項作業的狀態。

	![Server Explorer](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. 您可以在 Data Factory 上按一下滑鼠右鍵，並選取 [**將 Data Factory 匯出至新的專案**]，以便根據現有的 Data Factory 建立 Visual Studio 專案。

	![匯出 Data Factory](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## 更新 Visual studio 的 Data Factory 工具

若要更新 Visual Studio 的 Azure Data Factory 工具，請執行下列作業：

1. 按一下功能表上的 [**工具**]，然後選取 [**擴充功能和更新**]。
2. 選取左窗格中的 [**更新**]，然後選取 [**Visual Studio 資源庫**]。
3. 選取 [**Visual Studio 的 Azure Data Factory 工具**] 並按一下 [**更新**]。如果您看不到此項目，代表您已經有最新版本的工具。 

如需如何使用 Azure Preview 入口網站來監視您在本教學課程中建立的管線和資料集的指示，請參閱[監視資料集和管線](data-factory-monitor-manage-pipelines.md)。
 

## 後續步驟
在本文中，您已經建立可在隨選 HDInsight 叢集上執行 Hive 指令碼，含有轉換活動 (HDInsight 活動) 的管線。若要了解如何使用「複製活動」從 Azure Blob 複製資料到 Azure SQL，請參閱[教學課程：從 Azure Blob 複製資料到 Azure SQL](data-factory-get-started.md)。
  

<!---HONumber=August15_HO6-->