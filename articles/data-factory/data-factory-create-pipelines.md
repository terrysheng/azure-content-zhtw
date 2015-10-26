<properties 
	pageTitle="建立管線" 
	description="了解 Azure Data Factory 管線並學習如何建立管理來移動和轉換資料，以產生可用來獲取深入見解的資訊" 
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
	ms.topic="article" y
	ms.date="07/28/2015" 
	ms.author="spelluru"/>

# 了解管線和活動
本文將協助您了解 Azure Data Factory 中的管線和活動，以及如何運用這些來為您的案例或業務建構端對端的資料導向工作流程。本文假設您已經完成在此之前的[概觀](data-factory-introduction.md)和[建立資料集](data-factory-create-datasets.md)文章。

## 什麼是管線？
**管線是活動的邏輯群組**。可用來將活動群組成一個單位，共同執行任務。若要進一步了解管線，您需要先了解活動。

### 什麼是活動？
活動會定義在您資料上執行的動作。每個活動會取得零或多個[資料集](data-factory-create-datasets.md)做為輸入，並產生一或多個資料集做為輸出。**活動代表 Azure Data Factory 中的協調流程單位。**

例如，您可能會使用複製活動來協調從一個資料集複製資料到另一個資料集的流程。同樣地，您可能會使用在 Azure HDInsight 叢集上執行 Hive 查詢的 Hive 活動，來轉換或分析您的資料。Azure Data Factory 提供各種[資料轉換、分析](data-factory-data-transformation-activities.md)和[資料移動活動](data-factory-data-movement-activities.md)。您也可以選擇建立自訂 .NET 活動來執行自己的程式碼。

請考慮下列 2 個資料集：

**Azure SQL 資料集**

資料表 'MyTable' 包含 'timestampcolumn' 資料行，其有助於指定資料插入資料庫時的日期時間。

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Azure Blob 資料集**

資料會每小時利用可反映特定日期時間及小時資料粒度的 Blob 路徑，複製到新的 Blob。

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


以下管線中的複製活動會將資料從 Azure SQL 複製到 Azure Blob 儲存體。它會以每小時的頻率採用 Azure SQL 資料表做為輸入資料集，並將資料寫入至由 'AzureBlobOutput' 資料集所表示的 Azure Blob 儲存體。輸出資料集也有每小時頻率。如需了解如何透過時間單位複製資料，請參閱「排程和執行」一節。此管線有 3 小時作用中期間：從 “2015-01-01T08:00:00” 到 “2015-01-01T11:00:00”。

**管線：**
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

既然我們已初步了解何謂活動，讓我們一起回顧管線。
 
管線是活動的邏輯群組。可用來將活動群組成一個單位，共同執行任務。**管線也是部署和管理活動的單位。** 例如，您可能希望將邏輯上相關的活動放在一起成為一個管線，而使這些活動可以一起處於作用中或已暫停的狀態。

定義活動之間的相依性，管線中活動的輸出資料集可以成為相同或不同管線中另一個活動的輸入資料集。[排程和執行](#scheduling-and-execution)一節將詳加說明。

在 Azure Data Factory 中建立管線時的一般步驟如下：

1.	建立 Data Factory (若未建立)。
2.	建立每個資料存放區或運算的連結服務。
3.	建立輸入和輸出資料集。
4.	使用在以上定義的資料集上運作的活動建立管線。

![Data Factory 實體](./media/data-factory-create-pipelines/entities.png)

讓我們來深入探討管線的定義方式。

## 管線的剖析  

管線的一般結構如下所示：

	{
	    "name": "PipelineName",
	    "properties": 
	    {
	        "description" : "pipeline description",
	        "activities":
	        [
	
	        ],
			"start": "<start date-time>",
			"end": "<end date-time>"
	    }
	}

[活動] 區段內可以有一或多個已定義的活動。每個活動都有下列最上層結構：

	{
	    "name": "ActivityName",
	    "description": "description", 
	    "type": "<ActivityType>",
	    "inputs":  "[]",
	    "outputs":  "[]",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties":
	    {
	
	    },
	    "policy":
	    {
	    }
	    "scheduler":
	    {
	    }
	}

下表說明活動和管線 JSON 定義的屬性：

標記 | 說明 | 必要
--- | ----------- | --------
名稱 | 活動或管線的名稱。指定一個名稱，以表示活動或管線設定要進行的動作<br/><ul><li>字元數上限：260</li><li>必須以字母、數字或底線 (\_) 開頭</li><li>不允許下列字元：“.”、“+”、“?”、“/”、“<”、”>”、”*”、”%”、”&”、”:”、”\\”</li></ul> | 是 
description | 描述活動或管線用途的文字 | 是 
type | 指定活動的類型。如需不同類型的活動，請參閱[資料移動活動](data-factory-data-movement-activities.md)和[資料轉換活動](data-factory-data-transformation-activities.md)文章。 | 是
inputs | 活動所使用的輸入資料表<p>// 一個輸入資料表<br/>"inputs": [ { "name": "inputtable1" } ],</p><p>// 兩個輸入資料表<br/>"inputs": [ { "name": "inputtable1" }, { "name": "inputtable2" } ],</p>|是
outputs | 活動所使用的輸出資料表。<p>// 一個輸出資料表<br/>"outputs": [ { "name": “outputtable1” } ],</p><p>// 兩個輸出資料表<br/>"outputs": [ { "name": “outputtable1” }, { "name": “outputtable2” } ],</p> | 是
linkedServiceName | 活動所使用的連結服務名稱。<p>活動可能會要求您指定可連結至所需運算環境的連結服務。</p> | 是：適用於 HDInsight 活動和 Azure Machine Learning Batch 評分活動 <p>否：所有其他活動</p>
typeProperties | TypeProperties 區段中的屬性會視活動的類型而定。若要深入了解，請參閱各個活動的相關文章 |否
policy | 會影響活動之執行階段行為的原則。如果未指定，則會使用預設原則。向下捲動以取得詳細資訊 |否
start | 管線的開始日期時間。必須使用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。管線的 start 和 end 屬性共同指定管線的作用中期間。輸出配量只會在此作用中期間內產生。 | 否<p>使用 Set-AzureDataFactoryPipelineActivePeriod Cmdlet 也可以設定管線的作用中期間</p>
End | 管線的結束日期時間。如果已指定，則必須使用 ISO 格式。例如：2014-10-14T17:32:41Z <p>如果未指定，則會計算為 "start + 48 小時"。若要無限期執行管線，請指定 9999-09-09 做為 end 屬性的值。</p>| 否
isPaused | 如果設為 true，將不會執行管線。預設值 = false。您可以使用此屬性來啟用或停用。|否
scheduler | “scheduler” 屬性用來定義所要的活動排程。其子屬性與[資料集中的可用性屬性](data-factory-create-datasets.md#Availability)相同。| 否 | 

### 活動類型
Azure Data Factory 提供各種[資料移動](data-factory-data-movement-activities.md)和[資料轉換](data-factory-data-transformation-activities.md)活動。

### 原則
原則會影響活動的執行階段行為，特別是在處理資料表配量的時候。下表提供詳細資料。

屬性 | 允許的值 | 預設值 | 說明
-------- | ----------- | -------------- | ---------------
並行 | 整數 <p>最大值：10</p> | 1 | 活動的並行執行數目。<p>它可決定不同配量上可以發生的平行活動執行數目。比方說，如果活動需要處理大量可用的資料，具有較大的並行處理就會加快資料處理。</p> 
executionPriorityOrder | NewestFirst<p>OldestFirst</p> | OldestFirst | 決定正在處理之資料配量的順序。<p>例如，如果您有 2 個配量 (一個發生在下午 4 點，另一個發生在下午 5 點)，而兩者都暫停執行。如果您將 executionPriorityOrder 設為 NewestFirst，則會先處理下午 5 點的配量。同樣地，如果您將 executionPriorityOrder 設為 OldestFIrst，則會處理下午 4 點的配量。</p> 
retry | 整數<p>最大值可以是 10</p> | 3 | 在配量的資料處理標示為 [失敗] 前的重試次數。資料配量的活動執行會一直重試，直到指定的重試計數為止。在失敗後會儘速完成重試。
timeout | TimeSpan | 00:00:00 | 活動的逾時。範例：00:10:00 (意指逾時 10 分鐘)<p>如果未指定一個值或為 0，則無限期逾時。</p><p>如果配量的資料處理時間超過逾時值，則會予以取消，而系統會嘗試重試處理。重試次數取決於 retry 屬性。若發生逾時，狀態會是 TimedOut。</p>
delay | TimeSpan | 00:00:00 | 指定開始處理配量資料之前的延遲。<p>資料配量的活動會在 Delay 超出預期執行時間後開始執行。</p><p>範例：00:10:00 (意指延遲 10 分鐘)</p>
longRetry | 整數<p>最大值：10</p> | 1 | 配量執行失敗前的長時間重試次數。<p>longRetry 嘗試是以 longRetryInterval 隔開。所以如果您需要指定重試嘗試之間的時間，請使用 longRetry。如果已指定 Retry 和 longRetry，則每個 longRetry 嘗試都會包含 Retry 嘗試，而最大嘗試次數會是 Retry * longRetry。</p><p>例如，如果我們的活動原則如下：<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/></p><p>假設只有一個要執行的配量 (狀態是 PendingExecution) 且活動執行每次都失敗。一開始會有 3 次連續執行嘗試。在每次嘗試之後，配量狀態會是 Retry。在前 3 次嘗試結束之後，配量狀態會是 LongRetry。</p><p>一個小時 (也就是 longRetryInteval 的值) 之後，會有另一組 3 次連續執行嘗試。在那之後，配量狀態會是 Failed，不會再嘗試重試。因此全部已進行 6 次嘗試。</p><p>注意：如果任何執行成功，配量狀態會是 Ready 且不會再嘗試重試。</p><p>longRetry 可能用於下列情況：相依資料達到不具決定性的次數，或進行資料處理的整體環境相當脆弱。在這類情況下逐一進行重試並沒有幫助，而在一段時間後進行重試則會導致所要的結果。</p><p>提醒：請勿設定較大的 longRetry 或 longRetryInterval 值。較大的值通常表示正在排除的其他系統問題</p> 
longRetryInterval | TimeSpan | 00:00:00 | 長時間重試嘗試之間的延遲 

## 製作和管理管線
Azure Data Factory 提供各種機制來製作及部署管線 (其中包含一或多個活動)。

### 使用 Azure Preview 入口網站

1. 登入 [Azure Preview 入口網站](https://portal.azure.com/)。
2. 瀏覽至您想在其中建立管線的 Azure Data Factory 執行個體
3. 按一下 [摘要] 透鏡中的 [製作和部署] 磚。 
 
	![[製作和部署] 磚](./media/data-factory-create-pipelines/author-deploy-tile.png)

4. 按一下命令列的 [新增管線]。

	![新增管線按鈕](./media/data-factory-create-pipelines/new-pipeline-button.png)

5. 您應會看見編輯器視窗與管線 JSON 範本。

	![管線編輯器](./media/data-factory-create-pipelines/pipeline-in-editor.png)

6. 在您完成管線製作之後，接著按一下命令列上的 [部署] 來部署管線。

	**注意：**在部署期間，Azure Data Factory 服務會執行一些驗證檢查，協助修正一些常見問題。萬一發生錯誤，將顯示對應的資訊。採取更正動作並重新部署所製作的管線。您可以使用編輯器來更新及刪除管線。

### 使用 Visual Studio 外掛程式
您可以使用 Visual Studio 來製作管線及部署至 Azure Data Factory。若要深入了解，請參閱[教學課程：從 Azure 儲存體複製資料到 Azure SQL (Visual Studio)](data-factory-get-started-using-vs.md)。

### 使用 Azure PowerShell
您可以使用 Azure PowerShell 在 Azure Data Factory 中建立管線。假設您已在檔案 (位於 c:\\DPWikisample.json) 中定義管線 JSON。您可以將它上傳到如下列範例所示的 Azure Data Factory 執行個體。

	New-AzureDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

若要深入了解這個 Cmdlet，請參閱 [New-AzureDataFactoryPipeline cmdlet](https://msdn.microsoft.com/library/dn820227.aspx)。

### 使用 REST API
您也可以使用 REST API 建立和部署管線。這項機制可用來以程式設計的方式建立管線。若要深入了解，請參閱[建立或更新管線](https://msdn.microsoft.com/library/azure/dn906741.aspx)。

### 使用 .NET SDK
您也可以透過 .NET SDK 建立和部署管線。這項機制可用來以程式設計的方式建立管線。若要深入了解，請參閱[以程式設計方式建立、管理和監視 Data Factory](data-factory-create-data-factories-programmatically.md)。


## 排程和執行
到目前為止，您已了解什麼是管線和活動。您也已大致了解其定義方式以及 Azure Data Factory 中的高階活動觀點。現在讓我們了解一下其執行方式。

管線僅在其開始時間與結束時間之間有作用。在開始時間之前或結束時間之後就不會執行。如果管線已暫停，不論其開始和結束時間為何，都不會執行。若要執行管線，則不該將它暫停。

事實上，並不是執行管線。而是執行管線中的活動。不過，活動會在管線的整體內容中執行。請參閱[排程和執行](data-factory-scheduling-and-execution.md)，以了解如何在 Azure Data Factory 中排程和執行。

## 管理和監視  
一旦部署管線，您即可管理和監視管線、配量和執行。如需詳細資訊，請參閱〈[監視和管理管線](data-factory-monitor-manage-pipelines.md)〉。

## 後續步驟

- 了解[在 Azure Data Factory 中排程和執行](data-factory-scheduling-and-execution.md)。  
- 深入了解 Azure Data Factory 中的[資料移動](data-factory-data-movement-activities.md)和[資料轉換功能](data-factory-data-transformation-activities.md)
- 了解[在 Azure Data Factory 中管理和監視](data-factory-monitor-manage-pipelines.md)。
- [建置和部署第一個管線](data-factory-build-your-first-pipeline.md)。 

## 傳送意見
非常感謝您對本文的意見反應。請花幾分鐘的時間透過[電子郵件](mailto:adfdocfeedback@microsoft.com?subject=data-factory-create-pipelines.md)提交您的意見反應。
 

   













 
 


 

 

<!---HONumber=Oct15_HO3-->