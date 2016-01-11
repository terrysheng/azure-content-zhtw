<properties
	pageTitle="開始使用 Azure Data Factory (Azure 入口網站)"
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
	ms.topic="hero-article" 
	ms.date="12/18/2015"
	ms.author="spelluru"/>

# 開始使用 Azure Data Factory (Data Factory 編輯器)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)

在本文中，您將了解如何使用 [Azure 入口網站](https://portal.azure.com/)來建立您的第一個 Azure Data Factory。

## 先決條件

1. 您**必須**詳讀[教學課程概觀](data-factory-build-your-first-pipeline.md)一文，並完成必要的步驟，再進一步繼續。
2. 本文不提供 Azure Data Factory 服務的概念性概觀。建議您詳讀 [Azure Data Factory 簡介](data-factory-introduction.md)一文，了解服務的詳細概觀。  

## 步驟 1：建立 Data Factory
資料處理站可以有一或多個管線。其中的管線可以有一或多個活動。例如，「複製活動」會從來源複製資料到目的地資料存放區，HDInsight Hive 活動則是執行 Hive 指令碼轉換輸入資料以產生輸出資料。讓我們在這個步驟中開始建立 Data Factory。

1.	登入 [Azure 入口網站](http://portal.azure.com/)之後，執行下列動作：
	1.	按一下左側功能表上的 [新增]。 
	2.	按一下 [建立] 刀鋒視窗中的 [資料分析]。
	3.	按一下 [資料分析] 刀鋒視窗上的 [Data Factory]。

		![建立刀鋒視窗](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	在 [新增 Data Factory] 刀鋒視窗中，輸入 **GetStartedDF** 做為 [名稱]。

	![新增 Data Factory 刀鋒視窗](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT]Azure Data Factory 的名稱在全域必須是唯一的。如果您收到錯誤：**Data Factory 名稱 “GetStartedDF” 無法使用**，請變更 Data Factory 名稱 (例如 yournameGetStartedDF)，然後嘗試重新建立。請參閱 [Data Factory - 命名規則](data-factory-naming-rules.md)主題，以了解 Data Factory 成品的命名規則。
	>  
	> Data Factory 的名稱未來可能會註冊為 DNS 名稱，因此會變成公開可見的名稱。

3.	選取您想要建立 Data Factory 的 [Azure 訂用帳戶]。
4.	請選取現有的 **資源群組**，或建立新的資源群組。在教學課程中，建立名稱如下的資源群組：**ADFGetStartedRG**。    
5.	按一下 [新增 Data Factory] 刀鋒視窗上的 [建立]。
6.	您會看到 Data Factory 建立在 Azure 入口網站的「開始面板」 中，如下所示：   

	![建立 Data Factory 狀態](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. 恭喜！ 您已成功建立您的第一個 Data Factroy。在 Data Factory 成功建立後，您會看到 Data Factory 頁面，顯示 Data Factory 的內容。 	

	![Data Factory 刀鋒視窗](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

建立管線之前，您必須先建立一些 Data Factory 項目。首先，您要先建立連結的服務，以便將資料存放區/電腦連結到您的資料存放區；並定義輸入和輸出資料集，表示資料位於連結的資料存放區中，再建立具有使用這些資料集的活動之管線。

## 步驟 2：建立連結服務
在此步驟中，您將您的 Azure 儲存體帳戶和隨選 Azure HDInsight 叢集連結到您的 Data Factory。Azure 儲存體帳戶會保留此範例中管線的輸入和輸出資料。HDInsight 連結服務會用來執行此範例中管線活動指定的 Hive 指令碼。您必須識別案例中使用的資料存放區/計算服務，並建立連結的服務將這些服務連結到 Data Factory。

### 建立 Azure 儲存體連結服務
在此步驟中，您將您的 Azure 儲存體帳戶連結到您的 Data Factory。在本教學課程中，您會使用相同的 Azure 儲存體帳戶來存放輸入/輸出資料及 HQL 指令碼檔案。

1.	在適用於 **GetStartedDF** 的 [DATA FACTORY] 刀鋒視窗中，按一下 [製作和部署]。這會啟動 Data Factory 編輯器。 
	 
	![[製作和部署] 磚](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	按一下 [新增資料存放區] 並選擇 [Azure 儲存體]
	
	![Azure 儲存體連結服務](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

	在編輯器中，您應該會看到用來建立 Azure 儲存體連結服務的 JSON 指令碼。 
4. 使用您的 Azure 儲存體帳戶名稱取代帳戶名稱，並使用 Azure 儲存體帳戶的存取金鑰取代帳戶金鑰。若要了解如何取得儲存體存取金鑰，請參閱[檢視、複製和重新產生儲存體存取金鑰](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
5. 按一下命令列的 [部署]，部署連結服務。

	![[部署] 按鈕](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   成功部署連結的服務之後，應該會出現 **Draft-1** 視窗，而且您會在左側的樹狀檢視中看到 **StorageLinkedService**。![功能表中的儲存體連結服務](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)

 
### 建立 Azure HDInsight 連結服務
在此步驟中，您會將隨選 HDInsight 叢集與您的 Data Factory 連結。HDInsight 叢集會在執行階段自動建立，並在處理完成之後刪除，且會閒置一段時間。您可以使用自己的 HDInsight 叢集，不必使用隨選的 HDInsight 叢集。請參閱[計算連結服務](data-factory-compute-linked-services.md)以取得詳細資料。

1. 在 [Data Factory 編輯器] 中，按一下命令列上的 [新增計算]，然後選取 [隨選 HDInsight 叢集]。

	![新增計算](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. 複製下列程式碼片段並貼到 **Draft-1** 視窗。此 JSON 程式碼片段描述將用來建立隨選 HDInsight 叢集的屬性。 

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}
	
	下表提供程式碼片段中所使用之 JSON 屬性的描述：
	
	| 屬性 | 說明 |
	| :------- | :---------- |
	| 版本 | 指定所建立的 HDInsight 版本為 3.2。 | 
	| ClusterSize | 建立一個單一節點的 HDInsight 叢集。 | 
	| TimeToLive | 指定 HDInsight 叢集在被刪除之前的閒置時間。 |
	| linkedServiceName | 指定將用來儲存 HDInsight 產生之記錄檔的儲存體帳戶 |
3. 按一下命令列的 [部署]，部署連結服務。 
4. 確認您有在左側的樹狀檢視中看到 **StorageLinkedService** 和 **HDInsightOnDemandLinkedService**。

	![含連結服務的樹狀檢視](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## 步驟 3：建立資料集
在此步驟中，您將建立資料集來代表 Hive 處理的輸入和輸出資料。這些資料集是您稍早在本教學課程中建立的 **StorageLinkedService**。連結的服務會指向 Azure 儲存體帳戶，而資料集則會指定保留輸入和輸出資料儲存體中的容器、資料夾和檔案名稱。

### 建立輸入資料集

1. 在 [**Data Factory 編輯器**] 中，按一下命令列的 [**新增資料集**]，然後選取 [**Azure Blob 儲存體**]。

	![新增資料集](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. 複製下列程式碼片段並貼到 Draft-1 視窗。在 JSON 程式碼片段中，您會建立名為 **AzureBlobInput** 的資料集，代表管線中活動的輸入資料。此外，您指定將輸入資料放在名為 **adfgetstarted** 的 Blob 容器及名為 **inputdata** 的資料夾中。
		
		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
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
	| linkedServiceName | 表示您稍早建立的 StorageLinkedService。 |
	| fileName | 這是選用屬性。如果您省略此屬性，會挑選位於 folderPath 的所有檔案。在這種情況下，只會處理 input.log。 |
	| 類型 | 記錄檔為文字格式，因此我們會使用 TextFormat。 | 
	| columnDelimiter | 記錄檔案中的資料行會以 , (逗號) 分隔 |
	| frequency/interval | 頻率設為「每月」且間隔為 1，表示每個月都會可取得輸入配量。 | 
	| external | 如果輸入資料不是由 Data Factory 服務產生，此屬性會設為 true。 | 
	  
	
3. 按一下命令列的 [部署] 以部署新建立的資料集。您應該會看到左側樹狀檢視中的資料集。


### 建立輸出資料集
現在，您將建立輸出資料集來代表 Azure Blob 儲存體中儲存的輸出資料。

1. 在 [Data Factory 編輯器] 中，按一下命令列的 [新增資料集]，然後選取 [Azure Blob 儲存體]。  
2. 複製下列程式碼片段並貼到 Draft-1 視窗。在 JSON 程式碼片段中，建立名為 **AzureBlobOutput** 的資料集，並指定將由 Hive 指令碼產生的資料結構。此外，指定將結果儲存在名為 **adfgetstarted** 的 Blob 容器及名為 **partitioneddata** 的資料夾中。**availability** 區段指定每個月產生一次輸出資料集。
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
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
3. 按一下命令列的 [部署] 以部署新建立的資料集。
4. 驗證資料集已成功建立。

	![含連結服務的樹狀檢視](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## 步驟 4：建立您的第一個管線
在此步驟中，您將建立第一個具有 **HDInsightHive** 活動的管線。請注意，您每個月都可取得輸入配量資訊 (頻率：每月，間隔：1)，輸出配量則是每用產生，而活動的排程器屬性也設為每用 (如下所示)。輸出資料集設定及活動排程器必須相符。這時，輸出資料集會影響排程，因此即使活動並未產生任何輸出，您都必須建立輸出資料集。如果活動沒有任何輸入，您可以略過建立輸入資料集。本節結尾會說明下列 JSON 中使用的屬性。

1. 在 [Data Factory 編輯器] 中，按一下 [省略符號…]，然後按一下 [新增管線]。
	
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
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
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
	
	Hive 指令碼檔案 **partitionweblogs.hql** 儲存於 Azure 儲存體帳戶 (透過 scriptLinkedService 指定，名為 **StorageLinkedService**)，且位於 **adfgetstarted**容器的 **script** 資料夾中。

	**defines** 區段可用來指定執行階段設定，該設定將傳遞到 Hive 指令碼做為 Hive 組態值 (例如 ${hiveconf:inputtable}、${hiveconf:partitionedtable})。

	管線的 **start** 和 **end** 屬性會指定管線的作用中期間。

	在活動 JSON 中，您會指定 Hive 指令碼要在透過 **linkedServiceName** – **HDInsightOnDemandLinkedService** 指定的計算上執行。


3. 確認您在 Azure Blob 儲存體的 adfgetstarted/inputdata 資料夾中看到了 input.log 檔案，並按一下命令列中的 [部署]，以部署管線。由於 **start** 和 **end** 時間設定在過去，且 **isPaused** 設為 false，管線 (管線中的活動) 會在部署之後立即執行。
4. 確認您在樹狀檢視中看到管線。

	![管線的樹狀檢視](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. 恭喜，您已經成功建立您的第一個管線！

## 步驟 4：監視管線

6. 按一下 **X** 以關閉 [Data Factory 編輯器] 刀鋒視窗、瀏覽回 [Data Factory] 刀鋒視窗，然後按一下 [圖表]。
  
	![[圖表] 磚](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. 在 [圖表檢視] 中，您會看到管線的概觀，以及在本教學課程中使用的資料集。
	
	![圖表檢視](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. 若要檢視管線中的所有活動，以滑鼠右鍵按一下圖表中的管線，再按一下 [開啟管線]。 

	![開啟管線功能表](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)
9. 確認您在管線中看到了 HDInsightHive 活動。 
  
	![開啟管線檢視](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

	若要瀏覽回上一個檢視，請按一下上方麵包屑導航功能表中的 [Data Factory]。 
10. 在 [圖表檢視] 中，按兩下 [AzureBlobInput] 資料集。確認配量為**就緒**狀態。可能需要數分鐘的時間，配量才會顯示為「就緒」狀態。如果一段時間之後還未顯示，請查看輸入檔案 (input.log) 是否放置在正確的容器 (adfgetstarted) 和資料夾 (inputdata) 中。

	![輸入配量處於就緒狀態](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)
11. 按一下 **X** 關閉 **AzureBlobInput** 刀鋒視窗。 
12. 在 [圖表檢視] 中，按兩下 [AzureBlobOutput] 資料集。您會看到目前正在處理的配量。

	![資料集](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. 處理完成時，您會看到配量處於 [就緒] 狀態。
	>[AZURE.IMPORTANT]建立隨選 HDInsight 叢集通常需要一些時間 (大約 20 分鐘)。  

	![資料集](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)
	
10. 當配量處於**就緒**狀態時，檢查您 Blob 儲存體中 **adfgetstarted** 容器內 **partitioneddata** 資料夾的輸出資料。
 
	![輸出資料](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

## 後續步驟
在本文中，您已經建立可在隨選 HDInsight 叢集上執行 Hive 指令碼，含有轉換活動 (HDInsight 活動) 的管線。若要了解如何使用「複製活動」從 Azure Blob 將資料複製到 Azure SQL，請參閱[教學課程：從 Azure Blob 將資料複製到 Azure SQL](./data-factory-get-started.md)。

### 參考
| 主題 | 說明 |
| :---- | :---- |
| [管線](data-factory-create-pipelines.md) | 本文將協助您了解 Azure Data Factory 中的管線和活動，以及如何運用這些來為您的案例或業務建構端對端的資料導向工作流程。 |
| [資料集](data-factory-create-datasets.md) | 他的文章會協助您了解 Azure Data Factory 中的資料集。
| [排程和執行](data-factory-scheduling-and-execution.md) | 本文說明 Azure Data Factory 應用程式模型的排程和執行層面。 |
| [監視與管理管線](data-factory-monitor-manage-pipelines.md) | 本文描述如何監視、管理和偵錯您的管線。同時也會提供如何建立警示和取得失敗通知的詳細資訊。 |


  

<!---HONumber=AcomDC_1223_2015-->