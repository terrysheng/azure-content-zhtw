<properties 
	pageTitle="教學課程：使用 Visual Studio 建立具有複製活動的管線" 
	description="在本教學課程中，您將使用 Visual Studio，建立具有複製活動的 Azure Data Factory 管線。" 
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
	ms.date="03/07/2016" 
	ms.author="spelluru"/>

# 教學課程：使用 Visual Studio 建立具有複製活動的管線
> [AZURE.SELECTOR]
- [教學課程概觀](data-factory-get-started.md)
- [使用 Data Factory 編輯器](data-factory-get-started-using-editor.md)
- [使用 Visual Studio](data-factory-get-started-using-vs.md)
- [使用 PowerShell](data-factory-monitor-manage-using-powershell.md)
- [使用複製精靈](data-factory-copy-data-wizard-tutorial.md)

在本教學課程中，您會使用 Visual Studio 2013 執行下列作業：

1. 建立兩個連結的服務：**AzureStorageLinkedService1** 和 **AzureSqlinkedService1**。AzureStorageLinkedService1 連結 Azure 儲存體，而 AzureSqlLinkedService1 連結 Azure SQL 資料庫至 Data Factory：**ADFTutorialDataFactoryVS**。管線的輸入資料位於 Azure Blob 儲存體的 Blob 容器中，輸出資料則儲存在 Azure SQL Database 的資料表中。因此，您可以將這兩個資料存放區以連結服務的形式新增至 Data Factory。
2. 建立兩個 Data Factory 資料表：**EmpTableFromBlob** 和 **EmpSQLTable**，它們分別代表儲存在資料存放區的輸入/輸出資料。您將針對 EmpTableFromBlob，指定所含 Blob 具有來源資料的 Blob 容器，而針對 EmpSQLTable 指定可儲存輸出資料的 SQL 資料表。您也將指定其他屬性 (例如資料結構、資料可用性等)。
3. 在 ADFTutorialDataFactoryVS 中建立名為 **ADFTutorialPipeline** 的管線。管線會有一個複製活動，將輸入資料從 Azure Blob 複製到輸出 Azure SQL 資料表。複製活動會在 Azure Data Factory 中執行資料移動，而此活動是由全域可用的服務所提供，可以使用安全、可靠及可調整的方式，在各種不同的資料存放區之間複製資料。如需複製活動的詳細資訊，請參閱[資料移動活動](data-factory-data-movement-activities.md)文章。 
4. 建立 Data Factory，以及部署連結的服務、資料表和管線。    

## 必要條件
請檢閱[教學課程概觀](data-factory-get-started.md)文章，並在執行本教學課程之前完成必要的步驟。

您必須已在電腦上安裝下列項目：
- Visual Studio 2013
- 下載 Azure SDK for Visual Studio 2013。瀏覽至 [Azure 下載頁面](https://azure.microsoft.com/downloads/)並按一下 **.NET** 區段中的 [VS 2013 安裝]。

## 建立 Visual Studio 專案 
1. 啟動 **Visual Studio 2013**。按一下 [**檔案**]，指向 [**新增**]，然後按一下 [**專案**]。您應該會看到 [新增專案] 對話方塊。  
2. 在 [新增專案] 對話方塊中，選取 **DataFactory** 範本，然後按一下 [空白 Data Factory 專案]。如果您沒有看到 DataFactory 範本，請關閉 Visual Studio、安裝 Azure SDK for Visual Studio 2013，並重新開啟 Visual Studio。  

	![[新增專案] 對話方塊](./media/data-factory-get-started-using-vs/new-project-dialog.png)

3. 輸入專案的**名稱**、**位置**和**方案**的名稱，然後按一下 [確定]。

	![Solution Explorer](./media/data-factory-get-started-using-vs/solution-explorer.png)

## 建立連結服務
連結服務會將資料存放區或計算服務連結至 Azure Data Factory。資料存放區可以是 Azure 儲存體、Azure SQL Database 或在內部部署 SQL Server 資料庫。

在此步驟中，您將建立兩個連結服務：**AzureStorageLinkedService1** 和 **AzureSqlLinkedService1**。AzureStorageLinkedService1 連結服務會連結 Azure 儲存體帳戶，而 AzureSqlLinkedService 會將 Azure SQL 資料庫連結至 Data Factory：**ADFTutorialDataFactory**。

### 建立 Azure 儲存體連結服務

4. 以滑鼠右鍵按一下 [方案總管] 中的 [連結服務]，指向 [加入]，然後按一下 [新增項目]。      
5. 在 [加入新項目] 對話方塊中，從清單選取 [Azure 儲存體連結服務]，然後按一下 [加入]。 

	![新的連結服務](./media/data-factory-get-started-using-vs/new-linked-service-dialog.png)
 
3. 將 **accountname** 和 **accountkey** 取代為 Azure 儲存體帳戶和其金鑰的名稱。

	![Azure 儲存體連結服務](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. 儲存 **AzureStorageLinkedService1.json** 檔案。

### 建立 Azure SQL 連結服務。

5. 再次以滑鼠右鍵按一下 [方案總管] 中的 [連結服務] 節點，指向 [加入]，然後按一下 [新增項目]。 
6. 這次，請選取 [Azure SQL 連結服務]，然後按一下 [加入]。 
7. 在 **AzureSqlLinkedService1.json 檔案**中，將**伺服器名稱**、**資料庫名稱**、****username@servername** 和**密碼**替換為您的 Azure SQL 伺服器和資料庫名稱、使用者帳戶和密碼。
8.  儲存 **AzureSqlLinkedService1.json** 檔案。 


## 建立資料集
在上一個步驟中，您已建立連結服務 **AzureStorageLinkedService1** 和 **AzureSqlLinkedService1**，將 Azure 儲存體帳戶和 Azure SQL 資料庫連結至 Data Factory：**ADFTutorialDataFactory**。在此步驟中，您將定義兩個 Data Factory 資料表：**EmpTableFromBlob** 和 **EmpSQLTable**，他們分別代表 AzureStorageLinkedService1 和 AzureSqlLinkedService1 所參考資料存放區中儲存的輸入/輸出資料。您將針對 EmpTableFromBlob 指定所含 Blob 具有來源資料的 Blob 容器，而針對 EmpSQLTable 指定可儲存輸出資料的 SQL 資料表。

### 建立輸入資料集

9. 以滑鼠右鍵按一下 [方案總管] 中的 [資料表]，指向 [加入]，然後按一下 [新增項目]。
10. 在 [加入新項目] 對話方塊中，選取 [Azure Blob]，然後按一下 [加入]。   
10. 將 JSON 文字取代為下列文字並儲存 **AzureBlobLocation1.json** 檔案。 

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
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
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

### 建立輸出資料集

11. 再次以滑鼠右鍵按一下 [**方案總管**] 中的 [**資料表**]，指向 [**新增**]，然後按一下 [**新增項目**]。
12. 在 [加入新項目] 對話方塊中，選取 [Azure SQL]，然後按一下 [加入]。 
13. 將 JSON 文字取代成下列 JSON 並儲存 **AzureSqlTableLocation1.json** 檔案。

		{
		  "name": "EmpSQLTable",
		  "properties": {
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
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService1",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

## 建立管線 
您到目前為止已建立輸入/輸出連結服務和資料表。現在，您將使用 [複製活動] 建立管線，將資料從 Azure Blob 複製到 Azure SQL 資料庫。


1. 以滑鼠右鍵按一下 [方案總管] 中的 [管線]，指向 [加入]，然後按一下 [新增項目]。  
15. 選取 [加入新項目] 對話方塊中的 [複製資料管線]，並按一下 [加入]。 
16. 將 JSON 取代為下列 JSON 並儲存 **CopyActivity1.json** 檔案。
			
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z",
		    "isPaused": false
		  }
		}

## 發佈/部署 Data Factory 實體
  
18. 在 [方案總管] 中，以滑鼠右鍵按一下專案，再按一下 [發佈]。 
19. 如果您看到 [登入您的 Microsoft 帳戶] 對話方塊，請輸入具有 Azure 訂用帳戶的帳戶認證，然後按一下 [登入]。
20. 您應該會看到下列對話方塊：

	![發佈對話方塊](./media/data-factory-get-started-using-vs/publish.png)

21. 在 [設定 Data Factory] 頁面中，執行下列作業：
	1. 選取 [建立新的 Data Factory] 選項。
	2. 針對 [名稱] 輸入 **VSTutorialFactory**。  
	
		> [AZURE.NOTE]  
		Azure Data Factory 的名稱在全域必須是唯一的。如果您在發佈時收到與 Data Factory 名稱有關的錯誤，請變更 Data Factory 的名稱 (例如 yournameVSTutorialFactory) 並嘗試再發佈一次。請參閱 [Data Factory - 命名規則](data-factory-naming-rules.md)主題，以了解 Data Factory 成品的命名規則。
		> 
		> Data Factory 的名稱未來可能會註冊為 DNS 名稱，因此會變成公開可見的名稱。
	3. 針對 [訂用帳戶] 欄位選取適當的訂用帳戶。 
	4. 針對要建立的 Data Factory 選取 [資源群組]。 
	5. 選取 Data Factory 的 [區域]。 
	6. 按 [下一步]，切換至 [發佈項目] 頁面。 
23. 在 [發佈項目] 頁面上，確認所有 Data Factory 實體都已選取，並按 [下一步] 切換至 [摘要] 頁面。     
24. 檢閱摘要，然後按 [下一步] 開始部署程序，並檢視 [部署狀態]。
25. 在 [部署狀態] 頁面上，您應該會看到部署程序的狀態。部署完成後按一下 [完成]。 


## 使用伺服器總管檢視 Data Factory

1. 在 **Visual Studio** 中，按一下功能表上的 [檢視]，然後按一下 [伺服器總管]。
2. 在 [伺服器總管] 視窗中，依序展開 **Azure** 和 **Data Factory**。如果您看到 [登入 Visual Studio]，請輸入和 Azure 訂用帳戶相關聯的**帳戶**，然後按一下 [繼續]。輸入**密碼**，然後按一下 [登入]。Visual Studio 會嘗試取得訂用帳戶中所有 Azure Data Factory 的相關資訊。您會在 [Data Factory 工作清單] 視窗中看到這項作業的狀態。![Server Explorer](./media/data-factory-get-started-using-vs/server-explorer.png)
3. 您可以在 Data Factory 上按一下滑鼠右鍵，並選取 [將 Data Factory 匯出至新的專案]，以便根據現有的 Data Factory 建立 Visual Studio 專案。![匯出 Data Factory 至 VS 專案](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## 更新 Visual studio 的 Data Factory 工具
若要更新 Visual Studio 的 Azure Data Factory 工具，請執行下列作業：

1. 按一下功能表上的 [工具]，然後選取 [擴充功能和更新]。 
2. 選取左窗格中的 [更新]，然後選取 [Visual Studio 組件庫]。
4. 選取 [Visual Studio 的 Azure Data Factory 工具] 並按一下 [更新]。如果您看不到此項目，代表您已經有最新版本的工具。 

如需如何使用 Azure 入口網站來監視您在本教學課程中建立的管線和資料集的指示，請參閱[監視資料集和管線](data-factory-get-started-using-editor.md#monitor-pipeline)。

## 另請參閱
如需 Azure Data Factory 中複製活動的詳細資訊，請參閱[資料移動活動](data-factory-data-movement-activities.md)文章。

<!---HONumber=AcomDC_0323_2016-->