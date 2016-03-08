<properties
	pageTitle="開始使用 Azure Data Factory (Visual Studio)"
	description="在本教學課程中，您將使用 Visual Studio，建立範例 Azure Data Factory 管線。"
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
	ms.topic="hero-article" 
	ms.date="02/16/2016"
	ms.author="spelluru"/>

# 開始使用 Azure Data Factory (Visual Studio)
> [AZURE.SELECTOR]
- [教學課程概觀](data-factory-build-your-first-pipeline.md)
- [使用 Data Factory 編輯器](data-factory-build-your-first-pipeline-using-editor.md)
- [使用 PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [使用 Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [使用資源管理員範本](data-factory-build-your-first-pipeline-using-arm.md)


在本文中，您將了解如何使用 Microsoft Visual Studio 來建立您的第一個 Azure Data Factory。

## 先決條件

1. 您**必須**詳讀[教學課程概觀](data-factory-build-your-first-pipeline.md)一文，並完成必要的步驟，再進一步繼續。
2. 本文不提供 Azure Data Factory 服務的概念性概觀。建議您詳讀 [Azure Data Factory 簡介](data-factory-introduction.md)一文，了解服務的詳細概觀。  

## 逐步解說：使用 Visual Studio 建立及部署 Data Factory 實體 

### 必要條件

您必須已在電腦上安裝下列項目：

- Visual Studio 2013 或 Visual Studio 2015
- 下載 Azure SDK for Visual Studio 2013 或 Visual Studio 2015。瀏覽至 [Azure 下載頁面](https://azure.microsoft.com/downloads/)，然後按一下 [.NET] 區段中的 [VS 2013] 或 [VS 2015]。
- 下載適用於 Visual Studio 的最新 Azuer Data Factory 外掛程式：[VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) 或 [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)。如果您使用的是 Visual Studio 2013，也可以執行下列動作來更新外掛程式：在功能表中按一下 [工具] -> [擴充功能和更新] -> [線上] -> [Visual Studio 組件庫] -> [Microsoft Azure Data Factory Tools for Visual Studio] -> [更新]。 
	
	

### 建立 Visual Studio 專案 
1. 啟動 **Visual Studio 2013** 或 **Visual Studio 2015**。按一下 [**檔案**]，指向 [**新增**]，然後按一下 [**專案**]。您應該會看到 [新增專案] 對話方塊。  
2. 在 [新增專案] 對話方塊中，選取 [DataFactory] 範本，然後按一下 [空白 Data Factory 專案]。   

	![[新增專案] 對話方塊](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. 輸入專案的**名稱**、**位置**和**方案**的名稱，然後按一下 [確定]。

	![Solution Explorer](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### 建立連結服務
資料處理站可以有一或多個管線。其中的管線可以有一或多個活動。例如，「複製活動」會從來源複製資料到目的地資料存放區，HDInsight Hive 活動則是執行 Hive 指令碼轉換輸入資料以產生輸出資料。您可在稍後發佈 Data Factory 解決方案時，指定 Data Factory 的名稱和設定。

在此步驟中，您將您的 Azure 儲存體帳戶和隨選 Azure HDInsight 叢集連結到您的 Data Factory。Azure 儲存體帳戶會保留此範例中管線的輸入和輸出資料。HDInsight 連結服務會用來執行此範例中管線活動指定的 Hive 指令碼。您必須識別案例中使用的資料存放區/計算服務，並建立連結的服務將這些服務連結到 Data Factory。

#### 建立 Azure 儲存體連結服務
在此步驟中，您將您的 Azure 儲存體帳戶連結到您的 Data Factory。在本教學課程中，您會使用相同的 Azure 儲存體帳戶來存放輸入/輸出資料及 HQL 指令碼檔案。

4. 以滑鼠右鍵按一下 [方案總管] 中的 [連結服務]，指向 [加入]，然後按一下 [新項目]。      
5. 在 [加入新項目] 對話方塊中，從清單中選取 [Azure 儲存體連結服務]，然後按一下 [加入]。 
3. 使用 Azure 儲存體帳戶的名稱及其金鑰來取代 **accountname** 和 **accountkey**。若要了解如何取得儲存體存取金鑰，請參閱[檢視、複製和重新產生儲存體存取金鑰](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)

	![Azure 儲存體連結服務](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. 儲存 **AzureStorageLinkedService1.json** 檔案。

#### 建立 Azure HDInsight 連結服務
在此步驟中，您會將隨選 HDInsight 叢集與您的 Data Factory 連結。HDInsight 叢集會在執行階段自動建立，並在處理完成之後刪除，且會閒置一段時間。您可以使用自己的 HDInsight 叢集，不必使用隨選的 HDInsight 叢集。請參閱[計算連結服務](data-factory-compute-linked-services.md)以取得詳細資料。

1. 以滑鼠右鍵按一下 [方案總管] 中的 [連結服務]，指向 [加入]，然後按一下 [新項目]。
2. 選取 [HDInsight 隨選連結服務]，然後按一下 [加入]。 
3. 使用下列程式碼來取代 **JSON**：

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "AzureStorageLinkedService1"
		    }
		  }
		}
	
	下表提供程式碼片段中所使用之 JSON 屬性的描述：
	
	屬性 | 說明
	-------- | -----------
	版本 | 指定所建立的 HDInsight 版本為 3.2。 
	ClusterSize | 建立一個單一節點的 HDInsight 叢集。 
	TimeToLive | 指定 HDInsight 叢集在被刪除之前的閒置時間。
	linkedServiceName | 指定將用來儲存 HDInsight 產生之記錄檔的儲存體帳戶

	請注意：
	
	- Data Factory 會以上述 JSON 為您建立**以 Windows 為基礎的** HDInsight 叢集。您也可以讓它建立**以 Linux 為基礎的** HDInsight 叢集。如需詳細資訊，請參閱 [HDInsight 隨選連結服務](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)。 
	- 您可以使用**自己的 HDInsight 叢集**，不必使用隨選的 HDInsight 叢集。如需詳細資訊，請參閱 [HDInsight 連結服務](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)。
	- HDInsight 叢集會在您於 JSON 中指定的 Blob 儲存體 (**linkedServiceName**) 建立**預設容器**。HDInsight 不會在刪除叢集時刪除此容器。原先的設計就是如此。在使用 HDInsight 隨選連結服務時，除非有現有的即時叢集 (**timeToLive**)，否則每當需要處理配量時，就會建立 HDInsight 叢集，並在處理完成時予以刪除。
	
		隨著處理的配量越來越多，您會在 Azure Blob 儲存體中看到許多容器。如果在疑難排解作業時不需要這些容器，建議您加以刪除以降低儲存成本。這些容器的名稱遵循下列模式："adf**yourdatafactoryname**-**linkedservicename**-datetimestamp"。請使用 [Microsoft 儲存體總管](http://storageexplorer.com/)之類的工具刪除 Azure Blob 儲存體中的容器。

	如需詳細資訊，請參閱 [HDInsight 隨選連結服務](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)。 
4. 儲存 **HDInsightOnDemandLinkedService1.json** 檔案。

### 建立資料集
在此步驟中，您將建立資料集來代表 Hive 處理的輸入和輸出資料。這些資料集是您稍早在本教學課程中建立的 **AzureStorageLinkedService1**。連結的服務會指向 Azure 儲存體帳戶，而資料集則會指定保留輸入和輸出資料儲存體中的容器、資料夾和檔案名稱。

#### 建立輸入資料集

1. 以滑鼠右鍵按一下 [方案總管] 中的 [資料表]，指向 [加入]，然後按一下 [新增項目]。 
2. 從清單中選取 [Azure Blob]，將檔案名稱變更為 **InputDataSet.json**，再按一下 [加入]。
3. 使用下列文字取代編輯器中的 **JSON**： 

	在 JSON 程式碼片段中，您會建立名為 **AzureBlobInput** 的資料集，代表管線中活動的輸入資料。此外，您指定將輸入資料放在名為 **adfgetstarted** 的 Blob 容器及名為 **inputdata** 的資料夾中
		
		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "AzureStorageLinkedService1",
		        "typeProperties": {
		            "fileName": "input.log",
		            "folderPath": "adfgetstarted/inputdata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            }
		        },
		        "availability": {
		            "frequency": "Month",
		            "interval": 1
		        },
		        "external": true,
		        "policy": {}
		    }
		} 

	下表提供程式碼片段中所使用之 JSON 屬性的描述：

	| 屬性 | 說明 |
	| :------- | :---------- |
	| 類型 | 類型屬性設為 AzureBlob，因為資料位於 Azure Blob 儲存體。 |  
	| linkedServiceName | 表示您稍早建立的 AzureStorageLinkedService1。 |
	| fileName | 這是選用屬性。如果您省略此屬性，會挑選位於 folderPath 的所有檔案。在這種情況下，只會處理 input.log。 |
	| 類型 | 記錄檔為文字格式，因此我們會使用 TextFormat。 | 
	| columnDelimiter | 記錄檔案中的資料行會以 , (逗號) 分隔 |
	| frequency/interval | 頻率設為「每月」且間隔為 1，表示每個月都會可取得輸入配量。 | 
	| external | 如果輸入資料不是由 Data Factory 服務產生，此屬性會設為 true。 | 
	  
	
3. 儲存 **InputDataset.json** 檔案。

 
#### 建立輸出資料集
現在，您將建立輸出資料集來代表 Azure Blob 儲存體中儲存的輸出資料。

1. 以滑鼠右鍵按一下 [方案總管] 中的 [資料表]，指向 [加入]，然後按一下 [新增項目]。 
2. 從清單中選取 [Azure Blob]，將檔案名稱變更為 **OutputDataset.json**，再按一下 [加入]。 
3. 使用下列文字取代編輯器中的 **JSON**： 

	在 JSON 程式碼片段中，建立名為 **AzureBlobOutput** 的資料集，並指定將由 Hive 指令碼產生的資料結構。此外，指定將結果儲存在名為 **adfgetstarted** 的 Blob 容器及名為 **partitioneddata** 的資料夾中。**availability** 區段指定每個月產生一次輸出資料集。
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "adfgetstarted/partitioneddata",
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

	請參閱 **建立輸入資料集**一節，了解這些屬性的說明。您並未在輸出資料集上設定外部屬性，因為資料集是由 Data Factory 服務產生。

4. 儲存 **OutputDataset.json** 檔案。


### 建立您的第一個管線
在此步驟中，您將建立第一個具有 **HDInsightHive** 活動的管線。請注意，您每個月都可取得輸入配量資訊 (頻率：每月，間隔：1)，輸出配量則是每用產生，而活動的排程器屬性也設為每用 (如下所示)。輸出資料集設定及活動排程器必須相符。這時，輸出資料集會影響排程，因此即使活動並未產生任何輸出，您都必須建立輸出資料集。如果活動沒有任何輸入，您可以略過建立輸入資料集。本節結尾會說明下列 JSON 中使用的屬性。

1. 以滑鼠右鍵按一下 [方案總管] 中的 [管線]，指向 [加入]，然後按一下 [新項目]。 
2. 從清單中選取 [Hive 轉換管線]，然後按一下 [加入]。 
3. 使用下列程式碼片段來取代 **JSON**。

	> [AZURE.IMPORTANT] 使用您的儲存體帳戶名稱來取代 **storageaccountname**。

		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "AzureStorageLinkedService1",
		                    "defines": {
		                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "inputs": [
		                    {
		                        "name": "AzureBlobInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "AzureBlobOutput"
		                    }
		                ],
		                "policy": {
		                    "concurrency": 1,
		                    "retry": 3
		                },
		                "scheduler": {
		                    "frequency": "Month",
		                    "interval": 1
		                },
		                "name": "RunSampleHiveActivity",
		                "linkedServiceName": "HDInsightOnDemandLinkedService"
		            }
		        ],
		        "start": "2014-02-01T00:00:00Z",
		        "end": "2014-02-02T00:00:00Z",
		        "isPaused": false
		    }
		}

 	您會在 JSON 程式碼片段中建立一個管線，其中包括在 HDInsight 叢集上使用 Hive 處理「資料」的單一活動。
	
	您會在 JSON 程式碼片段中建立一個管線，其中包括在 HDInsight 叢集上使用 Hive 處理「資料」的單一活動。
	
	Hive 指令碼檔案 **partitionweblogs.hql** 儲存於 Azure 儲存體帳戶 (透過 scriptLinkedService，名為 **AzureStorageLinkedService1**)，且位於 **adfgetstarted**容器的 **script** 資料夾中。

	**defines** 區段可用來指定執行階段設定，該設定將傳遞到 Hive 指令碼做為 Hive 組態值 (例如 ${hiveconf:inputtable}、${hiveconf:partitionedtable})。

	管線的 **start** 和 **end** 屬性會指定管線的作用中期間。

	在活動 JSON 中，您會指定 Hive 指令碼要在透過 **linkedServiceName** – **HDInsightOnDemandLinkedService** 指定的計算上執行。

3. 儲存 **HiveActivity1.json** 檔案。

### 將 partitionweblogs.hql 新增為相依性 

1. 以滑鼠右鍵按一下 [方案總管] 視窗中的 [相依性]，指向 [加入]，按一下 [現有項目]。  
2. 瀏覽至 **C:\\ADFGettingStarted**、選取 [partitionweblogs.hql] 及 [input.log] 檔案，然後按一下 [加入]。您已建立兩個檔案，做為一部分的[教學課程概觀](data-factory-build-your-first-pipeline.md)必要條件。

當您在下一個步驟中發佈方案時，已將 **partitionweblogs.hql** 檔案上傳到 Blob 儲存體 **adfgetstarted** 中的指令碼資料夾。

### 發佈/部署 Data Factory 實體

18. 在 [方案總管] 中，以滑鼠右鍵按一下專案，再按一下 [發佈]。 
19. 如果您看到 [登入您的 Microsoft 帳戶] 對話方塊，請輸入具有 Azure 訂用帳戶的帳戶認證，然後按一下 [登入]。
20. 您應該會看到下列對話方塊：

	![發佈對話方塊](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. 在 [設定 Data Factory] 頁面中，執行下列作業：
	1. 選取 [建立新的 Data Factory] 選項。
	2. 輸入 FirstDataFactoryUsingVS 當做名稱。 
	
		> [AZURE.IMPORTANT] Azure Data Factory 的名稱在全域必須是唯一的。如果您在發佈時收到錯誤：「Data Factory 名稱 “FirstDataFactoryUsingVS” 無法使用」，請變更名稱 (例如 yournameFirstDataFactoryUsingVS)。請參閱 [Data Factory - 命名規則](data-factory-naming-rules.md)主題，以了解 Data Factory 成品的命名規則。
		> 
		> Data Factory 的名稱未來可能會註冊為 DNS 名稱，因此會變成公開可見的名稱。
	3. 針對 [訂用帳戶] 欄位選取適當的訂用帳戶。 
	4. 針對要建立的 Data Factory 選取 [資源群組]。 
	5. 選取 Data Factory 的 [區域]。 
	6. 按 [下一步]，切換至 [發佈項目] 頁面。(如果 [下一步] 按鈕已停用，請按 **TAB** 來移出 [名稱] 欄位)。 
23. 在 [發佈項目] 頁面上，確認所有 Data Factory 實體都已選取，並按 [下一步] 切換至 [摘要] 頁面。     
24. 檢閱摘要，然後按 [下一步] 開始部署程序，並檢視 [部署狀態]。
25. 在 [部署狀態] 頁面上，您應該會看到部署程序的狀態。部署完成後按一下 [完成]。 
 
## 步驟 4：監視管線

6. 登入 [Azure 入口網站](https://portal.azure.com/)，執行下列動作。
	1. 按一下 [瀏覽]，選取 [資料處理站]。![瀏覽 Data Factory](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png) 
	2. 從 Data Factory 清單中選取 **FirstDataFactoryUsingVS**。 
7. 在您 Data Factory 的首頁中，按一下 [圖表]。
  
	![[圖表] 圖格](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
7. 在 [圖表檢視] 中，您會看到管線的概觀，以及在本教學課程中使用的資料集。
	
	![圖表檢視](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png) 
8. 若要檢視管線中的所有活動，以滑鼠右鍵按一下圖表中的管線，再按一下 [開啟管線]。 

	![開啟管線功能表](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
9. 確認您在管線中看到了 HDInsightHive 活動。 
  
	![開啟管線檢視](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

	若要瀏覽回上一個檢視，請按一下上方麵包屑導航功能表中的 [Data Factory]。 
10. 在 [圖表檢視] 中，按兩下 [AzureBlobInput] 資料集。確認配量為**就緒**狀態。可能需要數分鐘的時間，配量才會顯示為「就緒」狀態。如果一段時間之後還未顯示，請查看輸入檔案 (input.log) 是否放置在正確的容器 (adfgetstarted) 和資料夾 (inputdata) 中。

	![輸入配量處於就緒狀態](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
11. 按一下 **X** 關閉 **AzureBlobInput** 刀鋒視窗。 
12. 在 [圖表檢視] 中，按兩下 [AzureBlobOutput] 資料集。您會看到目前正在處理的配量。

	![Dataset](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. 處理完成時，您會看到配量處於 [就緒] 狀態。
	>[AZURE.IMPORTANT] 建立隨選 HDInsight 叢集通常需要一些時間 (大約 20 分鐘)。  

	![Dataset](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)
	
10. 當配量處於**就緒**狀態時，檢查您 Blob 儲存體中 **adfgetstarted** 容器內 **partitioneddata** 資料夾的輸出資料。
 
	![輸出資料](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)

如需如何使用 Azure 入口網站來監視您在本教學課程中建立的管線和資料集的指示，請參閱[監視資料集和管線](data-factory-monitor-manage-pipelines.md)。

## 使用 [伺服器總管] 檢視 Data Factory 實體

1. 在 **Visual Studio** 中，按一下功能表上的 [檢視]，然後按一下 [伺服器總管]。
2. 在 [伺服器總管] 視窗中，依序展開 **Azure** 和 **Data Factory**。如果您看到 [登入 Visual Studio]，請輸入和 Azure 訂用帳戶相關聯的**帳戶**，然後按一下 [繼續]。輸入**密碼**，然後按一下 [登入]。Visual Studio 會嘗試取得訂用帳戶中所有 Azure Data Factory 的相關資訊。您會在 [Data Factory 工作清單] 視窗中看到這項作業的狀態。

	![Server Explorer](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. 您可以在 Data Factory 上按一下滑鼠右鍵，並選取 [將 Data Factory 匯出至新的專案]，以便根據現有的 Data Factory 建立 Visual Studio 專案。

	![匯出 Data Factory](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## 更新 Visual studio 的 Data Factory 工具

若要更新 Visual Studio 的 Azure Data Factory 工具，請執行下列作業：

1. 按一下功能表上的 [工具]，然後選取 [擴充功能和更新]。
2. 選取左窗格中的 [更新]，然後選取 [Visual Studio 組件庫]。
3. 選取 [Visual Studio 的 Azure Data Factory 工具] 並按一下 [更新]。如果您看不到此項目，代表您已經有最新版本的工具。 

## 使用組態檔
您可以在 Visual Studio 中使用組態檔，針對各個環境分別設定連結服務/資料表/管線的屬性。

請針對 Azure 儲存體連結服務考量下列 JSON 定義。根據您部署 Data Factory 實體的環境 (開發/測試/生產)，針對 accountname 和 accountkey 指定具有不同值的 **connectionString**。您可以針對每個環境使用個別的組態檔來執行這項操作。

	{
	    "name": "StorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "description": "",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	        }
	    }
	} 

### 新增組態檔
藉由執行下列步驟來新增每個環境的組態檔：

1. 在 Visual Studio 解決方案中以滑鼠右鍵按一下 Data Factory 專案，指向 [新增]，然後按一下 [新增項目]。
2. 從左側的已安裝範本中選取 [組態]，選取 [組態檔]，輸入組態檔的 [名稱]，然後按一下 [新增]。

	![新增組態檔](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. 以下列格式新增組態參數和其值：

		{
		    "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
		    "AzureStorageLinkedService1": [
		        {
		            "name": "$.properties.typeProperties.connectionString",
		            "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		        }
		    ],
		    "AzureSqlLinkedService1": [
		        {
		            "name": "$.properties.typeProperties.connectionString",
		            "value":  "Server=tcp:spsqlserver.database.windows.net,1433;Database=spsqldb;User ID=spelluru;Password=Sowmya123;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
		        }
		    ]
		}

	這個範例會設定 Azure 儲存體連結服務和 Azure SQL 連結服務的 connectionString 屬性。請注意，指定名稱的語法是 [JsonPath](http://goessner.net/articles/JsonPath/)。

	如果 JSON 具有屬性，該屬性具有如下所示的值陣列：

		"structure": [
	  		{
	  			"name": "FirstName",
	    		"type": "String"
	  		},
	  		{
	    		"name": "LastName",
	    	    "type": "String"
			}
		],
	
	您必須如下所示在組態檔中進行設定 (使用以零為起始的索引)：
		
		{
            "name": "$.properties.structure[0].name",
            "value": "FirstName"
        }
        {
            "name": "$.properties.structure[0].type",
            "value": "String"
        }
        {
            "name": "$.properties.structure[1].name",
            "value": "LastName"
        }
        {
            "name": "$.properties.structure[1].type",
            "value": "String"
        }

### 包含空格的屬性名稱
如果屬性名稱內含空格，請使用如下列範例 (資料庫伺服器名稱) 中所示的方括號：

     {
         "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
         "value": "MyAsqlServer.database.windows.net"
     }


### 使用組態部署解決方案
當您在 VS 中發佈 Azure Data Factory 實體時，您可以指定想要用於該發佈作業的組態。

若要使用組態檔在 Azure Data Factory 專案中發佈實體：

1. 以滑鼠右鍵按一下 Data Factory 專案，然後按一下 [發佈] 以查看 [發佈項目] 對話方塊。 
2. 選取現有的 Data Factory，或指定值以在 [設定 Data Factory] 頁面上建立新的 Data Factory，然後按 [下一步]。   
3. 在 [發佈項目] 頁面：您會看到下拉式清單，具有 [選取部署設定] 欄位的可用組態。

	![選取組態檔](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)

4. 選取您想要使用的 [組態檔]，然後按 [下一步]。
5. 確認您在 [摘要] 頁面上看到 JSON 檔案的名稱，然後按 [下一步]。 
6. 部署作業完成之後按一下 [完成]。 

部署時，在將實體部署至 Azure Data Factory 服務之前，會使用組態檔的值以設定 Data Factory 實體 (連結服務、資料表或管線) 的 JSON 檔案中的屬性。

## 後續步驟
在本文中，您已經建立可在隨選 HDInsight 叢集上執行 Hive 指令碼，含有轉換活動 (HDInsight 活動) 的管線。若要了解如何使用「複製活動」從 Azure Blob 將資料複製到 Azure SQL，請參閱[教學課程：從 Azure Blob 將資料複製到 Azure SQL](data-factory-get-started.md)。
  

<!---HONumber=AcomDC_0302_2016-->