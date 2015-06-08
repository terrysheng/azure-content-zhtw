<properties 
	pageTitle="Data Factory - 使用 Data Factory 和 Machine Learning 來建立預測管線 | Azure" 
	description="描述如何建立建立預測管線使用 Azuer 資料處理站和 Azure 機器學習" 
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
	ms.date="05/05/2015" 
	ms.author="spelluru"/>

# 使用 Azure Data Factory 和 Azure Machine Learning 來建立預測管線 
## 概觀
您可以 operationalize 發行 [Azure 機器學習][azure-machine-learning] Azure 資料 Factory 管線內的模型。這些管線稱為預測的管線。若要建立預測管線，您將需要：

-	已發佈之工作區模型的「API 金鑰」和批次評分 URL (請參閱下圖)。
-	Azure blob 儲存持有輸入的 CSV 檔案 (或) Azure SQL 資料庫，其中包含輸入資料計分。 
-	Azure blob 儲存體計分結果 CSV 檔案 (或) Azure SQL database 會保留的輸出資料會保留。 

	![機器學習儀表板][machine-learning-dashboard]

	批次 AzureMLLinkedService 取自做為映像上面所示的計分 URL 減去 ' * * 說明 * * '： https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

A **預測管線** 包含下列部分：

-	輸入和輸出資料表
-	Azure 儲存體/Azure SQL 和 Azure ML 連結服務
-	具有「Azure ML 批次評分活動」的管線

> [AZURE.NOTE]您可以使用在 Azure 資料 Factory (ADF) 管線中已發行的 Azure 機器學習 Web 服務所公開的 Web 服務參數。如需詳細資訊，請參閱本文中的 [Web 服務參數] 區段。

## 範例
此範例會使用 Azure 儲存體，以保存輸入和輸出資料。您也可以使用 Azure SQL Database，而不使用 Azure 儲存體。

我們建議您瀏覽 [開始使用 Azure 資料 Factory][adf-getstarted] 教學課程之前在此範例中會透過此範例，並使用資料 Factory 編輯器，以建立資料處理站成品 (連結的服務、 表格、 管線)。
 

1. 為您的 Azure 儲存體建立連結的服務如果評分輸入和輸出檔案會在不同的儲存體帳戶中，您將需要兩個連結的服務。以下是 JSON 範例：

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. 建立輸入和輸出 Azure Data Factory 資料表。請注意不同於一些其他資料處理站的資料表，這些必須同時包含兩者 **folderPath** 和 **fileName** 值。您可以使用資料分割，讓每個批次執行 (每一個資料配量) 處理或產生唯一的輸入和輸出檔案。您將可能需要包含某個上游活動，以將輸入轉換成 CSV 檔案格式，並將它放在每個配量的儲存體帳戶中。在該情況下，您將不會包含下面範例所示的 “waitOnExternal” 設定，而您的 ScoringInputBlob 將會是不同「活動」的輸出資料表。

		{  
			"name":"ScoringInputBlob",
			"properties":
			{  
					"location":
					{  
						"type":"AzureBlobLocation",
						"folderPath":"azuremltesting/input",
						"fileName":"in.csv",
						"format":
						{ 
							"type":"TextFormat",
							"columnDelimiter":","
						},
						"linkedServiceName":"StorageLinkedService"
					},
					"availability":
					{  
						"frequency":"Day",
						"interval":1,
						"waitOnExternal":
						{
		                	"retryInterval": "00:01:00",
		                	"retryTimeout": "00:10:00",
		                	"maximumRetry": 3
		            	}
		      		}
		   		}
			}
	
	您的批次計分 csv 檔案必須具有資料行的標頭資料列。如果您使用 **複製活動** 建立或移動 csv 到 blob 儲存體，您應該設定接收屬性 **blobWriterAddHeader** 至 **true**。例如：
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	如果 csv 檔案並沒有標頭資料列，您可能會看到下列錯誤： **活動中的錯誤： 讀取字串時發生錯誤。預期的語彙基元： StartObject。路徑 '、 行 1、 位置 1**。
3. 此輸出範例使用資料分割來為每一個配量執行建立一個唯一輸出路徑。如果沒有此唯一輸出路徑，活動就會覆寫檔案。

		{  
		   "name":"ScoringResultBlob",
		   "properties":
			{  
		        "location":
				{  
		            "type":"AzureBlobLocation",
		            "folderPath": "azuremltesting/scored/{folderpart}/",
		            "fileName": "{filepart}result.csv",
		            "partitionedBy": [ 
		                 { "name": "folderpart", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMdd" } },
		                 { "name": "filepart", "value": { "type": "DateTime", "date": "SliceStart", "format": "HHmmss" } } 
		             ], 
		            "format":{  
		              "type":"TextFormat",
		              "columnDelimiter":","
		            },
		            "linkedServiceName":"StorageLinkedService"
		        },
		        "availability":
				{  
		            "frequency":"Day",
		            "interval":15
		        }
		   }
		}


4. 建立連結之型別的服務： **AzureMLLinkedService**, ，並提供 API 金鑰與模型的批次計分的 URL。
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. 最後，撰寫一個管線，其中包含 **AzureMLBatchScoringActivity**。它將會從輸入資料表取得輸入檔案的位置、呼叫 AzureML 批次評分 API，然後將批次評分輸出複製到輸出資料表中指定的 Blob。不同於一些其他 Data Factory 活動，AzureMLBatchScoringActivity 只能有一個輸入和一個輸出資料表。

		 {
		    "name": "PredictivePipeline",
		    "properties":
		    {
		        "description" : "use AzureML model",
		        "activities":
		        [
		         {  
		            "name":"MLActivity",
		            "type":"AzureMLBatchScoringActivity",
		            "description":"prediction analysis on batch input",
		            "inputs": [ { "name": "ScoringInputBlob" } ],
		            "outputs":[ { "name": "ScoringResultBlob" } ],
		            "linkedServiceName":"MyAzureMLLinkedService",
		            "policy":{  
		               "concurrency":3,
		               "executionPriorityOrder":"NewestFirst",
		               "retry":1,
		               "timeout":"02:00:00"
		            }
		         }
		        ]
		    }
		}


## Web 服務參數
您可以使用在 Azure 資料 Factory (ADF) 管線中已發行的 Azure 機器學習 Web 服務所公開的 Web 服務參數。您可以在 Azure 機器學習中建立試驗並發佈為 web 服務，，然後使用該 web 服務，在多個 ADF 管線或活動，透過 Web 服務參數傳入不同的輸入。

### 將 Web 服務參數的值傳遞
新增 **轉換** 區段 **AzureMLBatchScoringActivty** 管線中該區段，如下列範例所示的 Web 服務參數指定值的 JSON 中的區段：

	transformation: {
		webServiceParameters: {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


您也可以使用 [資料 Factory 函式](https://msdn.microsoft.com/library/dn835056.aspx) 在將值傳遞為 Web 服務參數如下列範例所示：

	transformation: {
    	webServiceParameters: {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = '{0:yyyy-MM-dd HH:mm:ss}'', Time.AddHours(SliceStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Web 服務參數會區分大小寫，因此請確定您在活動中指定的名稱，JSON 符合 Web 服務所公開的項目。

### Azure SQL 讀取器和寫入器
使用 Web 服務參數的常見案例是使用 Azure SQL 讀取器和寫入器。讀取器模組用來將資料載入 Azure 機器學習 Studio 外部的資料管理服務從實驗，而寫入器模組是從您的實驗到 Azure 機器學習 Studio 外部的資料管理服務中儲存資料。如需 Azure Blob/Azure SQL 讀取器/寫入器的詳細資訊，請參閱 [讀取器](https://msdn.microsoft.com/library/azure/dn905997.aspx) 和 [寫入器](https://msdn.microsoft.com/library/azure/dn905984.aspx) MSDN Library 上的主題。上一節中的範例會使用 Azure Blob 讀取器與 Azure Blob 寫入器。本節討論如何使用 Azure SQL 讀取器和 Azure SQL 寫入器。

#### Azure SQL 讀取器
在 Azure ML Studio 中，您可以建置實驗並發佈 Web 服務與 Azure SQL 讀取器的輸入。Azure SQL 讀取器的連接屬性可以公開為 Web 服務參數，允許在計分要求批次中的執行階段傳遞的連接屬性的值。

在執行階段，從輸入資料 Factory 資料表詳細資料將用於資料處理站服務所填入的 Web 服務參數。請注意您必須使用的 Web 服務參數 (資料庫伺服器名稱、 資料庫名稱、 伺服器使用者帳戶名稱、 伺服器使用者帳戶密碼) 的預設名稱為這項整合與資料處理站服務運作。

如果您有任何額外的 Web 服務的參數，請使用 **webServiceParameters** 活動 JSON 的區段。如果您在此區段指定 Azure SQL 讀取器參數的值，這些值會覆寫從輸入連結的 Azure SQL 服務挑選的值。我們不建議您直接在 webServiceParameters ＞ 一節中的 Azure SQL 讀取器指定的值。使用區段來傳遞任何額外的參數的值。

若要使用 Azure SQL 讀取器，經由 Azure 資料 Factory 管線，請執行下列動作：

- 建立 **Azure SQL 連結服務**。 
- 建立資料處理站 **資料表** 使用 **AzureSqlTableLocation**。
- 設定該資料 Factory **資料表** 為 **輸入** 的 **AzureMLBatchScoringActivity** 管線 JSON 中。 



#### Azure SQL 寫入器
做為 Azure SQL 讀取器，與 Azure SQL 寫入器也可以讓它公開為 Web 服務參數的屬性。Azure SQL 寫入器會使用從設定輸入或輸出目錄相關聯的服務連結。下表描述的輸入與輸出使用連結的服務連結服務時。

<table>
<tr>
<td>輸出/輸入</td>
<td><b>輸入是 Azure SQL</b></td>
<td><b>輸入是 Azure Blob</b></td>
</tr>
<tr>
<td><b>輸出是 Azure SQL</b></td>
<td><p>資料處理站服務使用連結的輸入服務的連接字串資訊來產生 web 服務參數的名稱： 資料庫伺服器名稱]、 [資料庫名稱]、 伺服器使用者帳戶名稱]、 [伺服器使用者帳戶密碼]。請注意您必須在 Azure ML Studio 中使用這些 Web 服務參數的預設名稱。</p>
<p>如果讀取器-SQL Azure 和 Azure ML 模型中的 Azure SQL 寫入器共用相同的 Web 服務參數前面所提到的您是正常的。如果它們不會共用相同的 Web 服務 paramers，比方說，如果 Azure SQL 寫入器使用的參數名稱： 資料庫伺服器 name1、 資料庫 name1、 伺服器使用者帳戶 name1、 伺服器使用者帳戶 password1 (以 '1' 結尾)，您必須將這些輸出 web 服務參數的值傳遞活動 JSON 的 webServiceParameters 區段中。</p>
<p>
您可以傳遞任何其他 Web 服務參數值使用的活動 JSON webServiceParameters ＞ 一節。 
</p>

</td>
<td>
<p>資料處理站服務用來產生 web 服務參數名稱的輸出連結服務的連接字串資訊： 資料庫伺服器名稱]、 [資料庫名稱]、 伺服器使用者帳戶名稱]、 [伺服器使用者帳戶密碼]。請注意您必須在 Azure ML Studio 中使用這些 Web 服務參數的預設名稱。</p>
<p>您可以傳遞任何其他 Web 服務參數值使用的活動 JSON webServiceParameters ＞ 一節。<p>輸入的 blob 將當做輸入的位置。</p>
</td>
</tr>
<tr>
<td><b>輸出是 Azure Blob</b></td>
<td>資料處理站服務使用連結的輸入服務的連接字串資訊來產生 web 服務參數的名稱： 資料庫伺服器名稱]、 [資料庫名稱]、 伺服器使用者帳戶名稱]、 [伺服器使用者帳戶密碼]。請注意您必須在 Azure ML Studio 中使用這些 Web 服務參數的預設名稱。
</td>
<td>
<p>您必須傳遞使用 WebServiceParameters 區段活動 JSON 的任何 Web 服務參數的值。</p> 

<p>Blob 會當作輸入和輸出的位置。</p>

</td>
<tr>

</table>

> [AZURE.NOTE]Azure SQL 寫入器可能會遇到的索引鍵違規，如果它覆寫的識別資料行。您應該確定您組織您的輸出資料表，若要避免這種情況。
> 
> 若要合併的資料列，或在計分截斷資料，您可以使用暫存資料表與預存程序活動。如果您使用這種方法，設定執行原則的並行設定為 1。

### 使用 Web 服務參數的範例
#### 使用與 Web 服務參數 AzureMLBatchScoringActivity 管線

	{
		"name": "MLWithSqlReaderSqlWriter",
	  	"properties": {
		    "description": "Azure ML model with sql azure reader/writer",
		    "activities": [
		    	{
		    	    "name": "MLSqlReaderSqlWriterActivity",
		    	    "type": "AzureMLBatchScoringActivity",
		    	    "description": "test",
		        	"inputs": [ { "name": "MLSqlInput" } ],
		        	"outputs": [ { "name": "MLSqlOutput" } ],
		        	"linkedServiceName": "MLSqlReaderSqlWriterScoringModel",
		        	"policy": {
		          		"concurrency": 1,
			          	"executionPriorityOrder": "NewestFirst",
			          "retry": 1,
			          "timeout": "02:00:00"
			        },
			        transformation: {
			        	webServiceParameters: {
		            		"Database server name1": "output.database.windows.net",
				            "Database name1": "outputDatabase",
		            		"Server user account name1": "outputUser",
		            		"Server user account password1": "outputPassword",
			           		"Comma separated list of columns to be saved": "CustID, Scored Labels, Scored Probabilities",
		    		        "Data table name": "BikeBuyerPredicted" 
		          		}  
		        	}
		      	}
	    	]
		}
	}
 
在上述的 JSON 範例中：

- Azure ML 模型會使用 Azure SQL 讀取器和 Azure SQL 寫入器
- 當透過 Web 服務公開，將預設名稱用於參數
	- 針對 **讀取器**： 資料庫伺服器名稱、 資料庫名稱、 伺服器使用者帳戶名稱，以及伺服器的使用者帳戶密碼。
	- 針對 **寫入器**： 資料庫伺服器 name1、 資料庫 name1、 伺服器使用者帳戶 name1，以及伺服器的使用者帳戶 password1。
	
		請注意讀取器和寫入器不會共用參數在此情況下。  
- 資料處理站服務會自動產生 Web 服務參數的名稱值 **資料庫伺服器名稱**, ，**資料庫名稱**, ，**伺服器的使用者帳戶名稱**, ，和 **伺服器的使用者帳戶密碼**, ，符合輸入讀取器的名稱。因此，您不需要明確傳遞透過這些參數的值 **webServiceParameters** JSON 下方的活動。  
- 寫入器 (以 '1' 的後置字元的項目) 的參數會不會自動填入資料處理站服務。因此，您需要指定這些參數中的值 **webServiceParameters** 活動 JSON 的區段。  
- **客戶 ID**, ，**計分標籤**, ，和 **計分機率** 儲存為逗號分隔資料行。 
-  **資料表名稱** 在此範例會對應到輸出資料庫中的資料表。




## 另請參閱

文章 | 說明
------ | ---------------
[Azure 資料工廠開發人員參考資料][developer-reference] | 開發人員參考資料具有完整的參考內容 cmdlet、 JSON 指令碼、.NET 類別庫、 函式等等... 

[adf-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[developer-reference]: http://go.microsoft.com/fwlink/p/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png

<!---HONumber=GIT-SubDir-->