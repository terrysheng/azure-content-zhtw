<properties 
	pageTitle="移動 Web 資料表的資料 | Azure Data Factory" 
	description="了解如何使用 Azure Data Factory，來移動網頁上內部部署資料表的資料。" 
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
	ms.date="02/16/2016" 
	ms.author="spelluru"/>

# 使用 Azure Data Factory 來移動 Web 資料表的資料
本文章將概述如何使用 Azure 資料處理站中的複製活動，來把網頁上資料表中的資料複製到另一個資料存放區。本文是根據[資料移動活動](data-factory-data-movement-activities.md)一文，該文呈現使用複製活動移動資料的一般概觀以及支援的資料存放區組合。

Data factory 目前只支援把 Web 資料表的資料移動到其他資料存放區，但不支援把其他資料存放區的資料移動到 Web 資料表。

## 範例：把 Web 資料表的資料複製到 Azure Blob

下列範例顯示：

1.	[Web](#web-linked-service-properties) 類型的連結服務。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 類型的連結服務。
3.	[WebTabe](#WebTable-dataset-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
4.	具有使用 [WebSource](#websource-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

範例會每隔一小時就把 Web 資料表的資料複製到 Azure blob。範例後面的各節會說明這些範例中使用的 JSON 屬性。

下列範例示範如何把 Web 資料表的資料複製到 Azure Blob。不過，您可以使用 Azure Data Factory 中的複製活動，把資料直接複製到[資料移動活動](data-factory-data-movement-activities.md)一文中所述的任何接收器。

**Web 連結服務** 這個範例會使用有匿名驗證的 Web 連結服務。請參閱＜[Web 連結服務](#web-linked-service-properties)＞一節，來了解您可以使用的不同驗證類型。

	{
	    "name": "WebLinkedService",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "url" : "http://finance.yahoo.com/q/"
	        }
	    }
	}


**Azure 儲存體連結服務**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

將 **external** 設定為 **True**的**WebTable 輸入資料集**，及指定 **externalData** 原則 (可省略) 即可通知 Data Factory 服務：這是 Data Factory 外部的資料表，且不是由 Data Factory 中的活動所產生。

> [AZURE.NOTE] 如需如何取得 HTML 網頁中資料表索引的步驟，請參閱＜[取得 HTML 網頁中資料表的索引](#get-index-of-a-table-in-an-html-page)＞一節。

	
	{
	    "name": "WebTableInput",
	    "properties": {
	        "type": "WebTable",
	        "linkedServiceName": "WebLinkedService",
	        "typeProperties": {
	            "index": 4,
	            "path": "hp?s=MSFT+Historical+Prices"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}



**Azure Blob 輸出資料集**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。

	{
	    "name": "AzureBlobOutput",
	    "properties":
	    {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties":
	        {
	            "folderPath": "MSFTHistoricalPrices"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}




**具有複製活動的管線**

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。在管線 JSON 定義中，**source** 類型設定為 **WebSource**，且 **sink** 類型設定為 **BlobSink**。

如需 WebSource 支援的屬性清單，請參閱＜[WebSource 類別屬性](#websource-copy-activity-type-properties)＞一節。
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "WebTableToAzureBlob",
	        "description": "Copy from a Web table to an Azure blob",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "WebTableInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "WebSource"
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


## Web 連結服務屬性

下表提供 Web 連結服務專屬 JSON 元素的說明。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- | 
| 類型 | 類型屬性必須設為：**Web** | 是 | 
| Url | Web 來源的 URL | 是 |
| userName | 基本驗證用的使用者名稱 | 是 (用於基本驗證)
| password | 基本驗證用的密碼 | 是 (用於基本驗證)
| authenticationType | 匿名、基本或 WebApi。 | 是 |
| apiKey | WebApi 驗證用的 ApiKey。 | 是 (用於 WebApi 驗證)|   

### 使用匿名驗證

	{
	    "name": "web",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "url" : "http://finance.yahoo.com/q/"
	        }
	    }
	}


### 使用基本驗證
	
	{
	    "name": "web",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "basic",
	            "url" : "http://myit.mycompany.com/",
	            "userName": "Administrator",
	            "password": "password"
	        }
	    }
	}


## WebTable 資料集屬性

如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 **typeProperties** 區段都不同，可提供資料存放區中的資料位置資訊。**WebTable** 類型資料集的 typeProperties 區段有下列屬性

屬性 | 說明 | 必要
:-------- | :----------- | :--------
類型 | 資料集的類型，必須設定為 **WebTable** | 是
路徑 | 包含資料表之資源的相對 URL。 | 否。當路徑未指定時，則只會使用在連結服務定義中指定的 URL。 
index | 資源中資料表的索引。如需如何取得 HTML 網頁中資料表索引的步驟，請參閱＜[取得 HTML 網頁中資料表的索引](#get-index-of-a-table-in-an-html-page)＞一節。 | 是


**範例：**

	{
	    "name": "WebTableInput",
	    "properties": {
	        "type": "WebTable",
	        "linkedServiceName": "WebLinkedService",
	        "typeProperties": {
	            "index": 4,
	            "path": "hp?s=MSFT+Historical+Prices"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}

## WebSource：複製活動類型屬性

如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同，而在複製活動的案例中，可用的屬性會根據來源與接收的類型而有所不同。

在複製活動的案例中，當來源類型為 **WebSource** 時，目前沒有其他的屬性受到支援。

## 取得 HTML 網頁中資料表的索引

1. 啟動 [Excel 2016]，然後切換到 [資料] 索引標籤。  
2. 按一下工具列上的 [開新查詢]、指向 [從其他來源]，然後按一下 [從 Web]。
	
	![Power Query 功能表](./media/data-factory-web-table-connector/PowerQuery-Menu.png) 
3. 在 [從 Web] 對話方塊方塊中，輸入您會在連結服務 JSON 中使用的 [URL] (例如：https://en.wikipedia.org/wiki/)，以及您會為資料集指定的路徑 (例如：AFI%27s\_100\_Years...100\_Movies)，然後按一下 [確定]。 

	![[從 Web] 對話方塊](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

	此範例所使用的 URL：https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies 
4.  當您看到 [存取 Web 內容] 對話方塊時，選取右側的 **URL**、**驗證方式**，然後按一下 [連線]。 

	![[存取 Web 內容] 對話方塊](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5.  按一下樹狀檢視中的某個**資料表**項目來查看資料表內容，然後按一下底部的 [編輯] 按鈕。  

	![[導覽器] 對話方塊](./media/data-factory-web-table-connector/Navigator-DialogBox.png)

5. 在 [查詢編輯器] 視窗中，按一下工具列上的 [進階編輯器] 按鈕。

	![[進階編輯器] 按鈕](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)

6. 在 [進階編輯器] 對話方塊中，「Source」旁的數字就是索引。

	![進階編輯器及索引](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)


如果您使用的是 Excel 2013，請使用 [Microsoft Power Query for Excel](https://www.microsoft.com/download/details.aspx?id=39379) 來取得索引。請參閱[連線到網頁](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8)一文來取得詳細資料。如果您使用 [Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/)，步驟將會很類似。

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

<!---HONumber=AcomDC_0224_2016-->