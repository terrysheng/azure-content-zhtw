<properties
	pageTitle="使用 Azure Data Factory 載入資料 | Microsoft Azure"
	description="了解如何使用 Azure Data Factory 載入資料"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/20/2015"
   ms.author="lodipalm"/>

# 使用 Azure Data Factory 載入資料

 遵循此教學課程將說明您如何在 Azure Data Factory 建立管線，將資料從 Azure 儲存體 Blob 移至 SQL 資料倉儲。使用下列步驟，您將：

+ 在 Azure 儲存體 Blob 中設定範例資料。
+ 將資源連接到 Azure Data Factory。
+ 建立管線來將資料從儲存體 Blob 移至 SQL 資料倉儲。

## 資源
對於本教學課程，您將需要下列資源：

   + **Azure 儲存體 Blob**：您的 Azure 儲存體 Blob 將是管線的資料來源。您可以使用現有的 blob 或[佈建新的 blob](../storage/storage-create-storage-account/)。

   + **SQL 資料倉儲**：在本教學課程中，您可將資料移到 SQL 資料倉儲。如果尚未設定執行個體，您可以在[這裡](sql-data-warehouse-get-started-provision.md)了解如何設定。此外，也將需要搭配我們的 AdventureWorks DW 資料集設定您的執行個體。如果您未佈建資料倉儲與範例資料，您可以[手動載入它](sql-data-warehouse-get-started-manually-load-samples.md)。

   + **Azure Data Factory**：Azure Data Factory 將完成實際負載，而且如果您需要如何設定 Azure Data Factory 或建立管線的詳細資訊，您可以參閱[這裡](../data-factory/data-factory-build-your-first-pipeline-using-editor/)。

一旦準備好所有片段，您就可以開始準備資料，並建立 Azure Data Factory 管線。

## 範例資料
除了管線的不同片段外，我們也會需要一些範例資料，用來練習如何在 Azure Data Factory 載入資料。

1. 首先，[下載範例資料](https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv)。此資料會搭配已在您的範例資料中的範例資料 (提供另一個三年銷售資料) 一起使用。

2. 一旦下載了資料，您就可以在 AZCopy 執行以下指令碼，將它移至 blob 儲存體：

        AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv

	如需如何安裝及使用 AZCopy 的相關資訊，請參閱 [AZCopy 文件](../storage/storage-use-azcopy/)。

既然有了適當的資料，我們就可以移至您的 Data Factory 以建立管線，將資料從儲存體帳戶移至您的 SQL 資料倉儲。

## 使用 Azure Data Factory
既然已設定所有片段，我們就可以瀏覽至您在 Azure Preview 入口網站的 Azure Data Factory 執行個體，開始設定管線您可以移至 [Azure 入口網站](portal.azure.com)，並從左側功能表中選取您的 Data Factory 來完成此作業。

從這裡將有三個步驟來設定 Azure Data Factory 管線，以將資料傳送到您的資料倉儲：連結您的服務、定義您的資料集，以及建立您的管線。

### 建立連結服務
第一個步驟就是將您的 Azure 儲存體帳戶和 SQL 資料倉儲連結到您的 Data Factory。

1. 首先，按一下 Data Factory 的 [連結服務] 區段，然後按一下 [新增資料存放區]，開始註冊程序 接著，選擇要在其下註冊 Azure 儲存體的名稱、選取 Azure 儲存體做為類型，然後輸入您的帳戶名稱和帳戶金鑰。

2. 若要註冊 SQL 資料倉儲，您必須瀏覽至 [作者與部署] 區段，然後依序選取 [新增資料存放區] 和 [Azure SQL 資料倉儲]。然後，您必須填寫以下範本：

		{
		    "name": "<Linked Service Name>",
		    "properties": {
		        "description": "",
		        "type": "AzureSqlDW",
		        "typeProperties": {
		            "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
		        }
		    }
		}

### 註冊資料集
建立連結服務之後，我們必須定義資料集。在這裡，此舉表示將定義要從儲存體移至資料倉儲的資料結構。您可以閱讀如何建立的相關資訊。

1. 若要開始此程序，請瀏覽至 Data Factory 的 [作者與部署] 區段。

2. 依序按一下 [新增資料集] 和 [Azure Blob 儲存體]，以將儲存體連結至 Data Factory。您可以使用以下指令碼，在 Azure Blob 儲存體中定義您的資料：

		{
			"name": "<Dataset Name>",
			"properties": {
				"type": "AzureBlob",
				"linkedServiceName": "<linked storage name>",
				"typeProperties": {
					"folderPath": "<containter name>",
					"fileName": "FactInternetSales.csv",
					"format": {
					"type": "TextFormat",
					"columnDelimiter": ",",
					"rowDelimiter": "\n"
					}
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



3. 現在，我們也會對 SQL 資料倉儲定義我們的資料集。我們以相同方式開始，即依序按一下 [新增資料集] 和 [Azure SQL 資料倉儲]。

		{
		  "name": "<dataset name>",
		  "properties": {
		    "type": "AzureSqlDWTable",
		    "linkedServiceName": "<linked data warehouse name>",
		    "typeProperties": {
		      "tableName": "FactInternetSales"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		{
		  "name": "DWDataset",
		  "properties": {
			"type": "AzureSqlDWTable",
			"linkedServiceName": "AzureSqlDWLinkedService",
			"typeProperties": {
			  "tableName": "FactInternetSales"
			},
			"availability": {
			  "frequency": "Hour",
			  "interval": 1
			}
		  }
		}

### 設定管線
最後，我們將在 Azure Data Factory 中安裝並執行管線。這是將完成實際資料移動的作業。您可以在[這裡](../data-factory/data-factory-azure-sql-data-warehouse-connector/)找到您可以使用 SQL 資料倉儲和 Azure Data Factory 完成之作業的完整檢視。

現在。在 [作者與部署] 區段中。依序按一下 [其他命令] 和 [新增管線]。建立管線之後，您可以使用以下程式碼，將資料傳送到資料倉儲：

	{
	"name": "<Pipeline Name>",
	"properties": {
		"description": "<Description>",
		"activities": [
			{
				"type": "Copy",
				"typeProperties": {
					"source": {
						"type": "BlobSource",
						"skipHeaderLineCount": 1
					},
					"sink": {
						"type": "SqlDWSink",
						"writeBatchSize": 0,
						"writeBatchTimeout": "00:00:10"
					}
				},
				"inputs": [
					{
						"name": "<Storage Dataset>"
					}
				],
				"outputs": [
					{
						"name": "<Data Warehouse Dataset>"
					}
				],
				"policy": {
					"timeout": "01:00:00",
					"concurrency": 1
				},
				"scheduler": {
					"frequency": "Hour",
					"interval": 1
				},
				"name": "Sample Copy",
				"description": "Copy Activity"
			}
		],
		"start": "<Date YYYY-MM-DD>",
		"end": "<Date YYYY-MM-DD>",
		"isPaused": false
	}
	}
	

<!---HONumber=Oct15_HO4-->