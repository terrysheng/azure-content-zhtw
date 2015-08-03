<properties 
	pageTitle="開始使用 Azure Data Factory" 
	description="此教學課程說明如何建立將資料從 Blob 複製到 Azure SQL 資料庫儲存個體的範例資料管線。" 
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
	ms.date="07/17/2015" 
	ms.author="spelluru"/>

# 教學課程：使用 Visual Studio 建立和監視 Data Factory
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)


##本教學課程內容
在本教學課程中，您會先使用 Azure Preview 入口網站建立名為 **ADFTutorialDataFactoryVS** 的 Azure Data Factory，然後使用 Visual Studio 2013 執行下列動作：

1. 建立兩個連結的服務：**AzureStorageLinkedService1** 和 **AzureSqlinkedService1**。AzureStorageLinkedService1 連結 Azure 儲存體而 AzureSqlLinkedService1 連結 Azure SQL Database 至 Data Factory：**ADFTutorialDataFactoryVS**。管線的輸入資料位於 Azure Blob 儲存體的 Blob 容器中，輸出資料則儲存在 Azure SQL Database 的資料表中。因此，您可以將這兩個資料存放區以連結服務的形式新增至 Data Factory。
2. 建立兩個 Data Factory 資料表：**EmpTableFromBlob** 和 **EmpSQLTable**，它們分別代表儲存在資料存放區的輸入/輸出資料。您將針對 EmpTableFromBlob，指定所含 Blob 具有來源資料的 Blob 容器，而針對 EmpSQLTable 指定可儲存輸出資料的 SQL 資料表。您也將指定其他屬性 (例如資料結構、資料可用性等)。
3. 在 ADFTutorialDataFactoryVS 中建立名為 **ADFTutorialPipeline** 的管線。管線會有一個**複製活動**，將輸入資料從 Azure Blob 複製到輸出 Azure SQL 資料表 


## <a name="CreateDataFactory"></a>建立 Azure Data Factory
在此步驟中，您會使用 Azure Preview 入口網站建立名為 **ADFTutorialDataFactoryVS** 的 Azure Data Factory。

1.	登入 [Azure Preview 入口網站](http://portal.azure.com)之後，請按一下左下角的 [新增]，並選取 [建立] 刀鋒視窗中的 [資料分析]，然後按一下 [資料分析] 刀鋒視窗中的 [Data Factory]。 

	![新增->DataFactory](./media/data-factory-get-started-using-vs/NewDataFactoryMenu.png)

6. 在 [**新增 Data Factory**] 刀鋒視窗中：
	1. 輸入 **ADFTutorialDataFactoryVS** 做為**名稱**。 
	
  		![新增 Data Factory 刀鋒視窗](./media/data-factory-get-started-using-vs/getstarted-new-data-factory.png)
	2. 按一下 [資源群組名稱]，然後執行下列動作：
		1. 按一下 [建立新的資源群組]。
		2. 在 [建立資源群組] 刀鋒視窗中，輸入 **ADFTutorialResourceGroup** 做為資源群組的名稱，然後按一下 [確定]。 

			![建立資源群組](./media/data-factory-get-started-using-vs/CreateNewResourceGroup.png)

		本教學課程的某些步驟是假設您使用 **ADFTutorialResourceGroup** 做為資源群組名稱。若要了解資源群組，請參閱[使用資源群組管理您的 Azure 資源](resource-group-overview.md)。  
7. 在 [新增 Data Factory] 刀鋒視窗中，請留意是否已選取 [新增至開始面板]。
8. 按一下 [新增 Data Factory] 刀鋒視窗中的 [建立]。

	Azure Data Factory 的名稱在全域必須是唯一的。如果您收到錯誤：**Data Factory 名稱 “ADFTutorialDataFactoryVS” 無法使用**，請變更 Data Factory 名稱 (例如 yournameADFTutorialDataFactoryVS)，然後試著重新建立。執行本教學課程中的其餘步驟時，請使用此名稱來取代 ADFTutorialFactory。請參閱 [Data Factory - 命名規則] [data-factory-naming-rules] 主題，以了解 Data Factory 成品的命名規則。
	 
	![Data Factory 名稱無法使用](./media/data-factory-get-started-using-vs/getstarted-data-factory-not-available.png)

9. 按一下左側的 [通知] 中樞，然後從建立程序中尋找通知。按一下 **X** 關閉 [通知] 刀鋒視窗 (若已開啟)。
10. 建立完成之後，您會看到 [DATA FACTORY] 刀鋒視窗，如下所示。

    ![Data Factory 首頁](./media/data-factory-get-started-using-vs/getstarted-data-factory-home-page.png)

## 使用 Visual Studio 建立和部署 Data Factory 實體 

### 必要條件
您必須在電腦上安裝下列項目：- Visual Studio 2013 - 下載 Azure SDK for Visual Studio 2013。瀏覽至 [Azure 下載頁面](http://azure.microsoft.com/downloads/) 並按一下 [**.NET**] 區段中的 [**VS 2013 安裝**]。

### 逐步介紹

#### 建立 Visual Studio 專案 
1. 啟動 **Visual Studio 2013**。按一下 [**檔案**]，指向 [**新增**]，然後按一下 [**專案**]。您應該會看到 [**新增專案**] 對話方塊。  
2. 在 [**新增專案**] 對話方塊中，選取 **DataFactory** 範本，然後按一下 [**空白 Data Factory 專案**]。如果您沒有看到 DataFactory 範本，請關閉 Visual Studio、安裝 Azure SDK for Visual Studio 2013，並重新開啟 Visual Studio。  

	![[新增專案] 對話方塊](./media/data-factory-get-started-using-vs/new-project-dialog.png)

3. 輸入專案的**名稱**、**位置**，和**解決方案**的名稱，然後按一下 [**確定**]。

	![Solution Explorer](./media/data-factory-get-started-using-vs/solution-explorer.png)

#### 建立連結服務
連結服務會將資料存放區或計算服務連結至 Azure Data Factory。資料存放區可以是 Azure 儲存體、Azure SQL Database 或在內部部署 SQL Server 資料庫。

在此步驟中，您將建立兩個連結服務：**AzureStorageLinkedService1** 和 **AzureSqlLinkedService1**。AzureStorageLinkedService1 連結服務會連結 Azure 儲存體帳戶，而 AzureSqlLinkedService 會將 Azure SQL Database 連結至 Data Factory：**ADFTutorialDataFactory**。

##### 建立 Azure 儲存體連結服務

4. 以滑鼠右鍵按一下 [方案總管] 中的 [**連結服務**]，指向 [**新增**]，然後按一下 [**新增項目**]。      
5. 在 [**新增項目**] 對話方塊中，從清單選取 [**Azure 儲存體連結服務**]，然後按一下 [**新增**]。 

	![新的連結服務](./media/data-factory-get-started-using-vs/new-linked-service-dialog.png)
 
3. 將 **accountname** 和 **accountkey** 取代為 Azure 儲存體帳戶和其金鑰的名稱。

	![Azure 儲存體連結服務](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. 儲存 **AzureStorageLinkedService1.json** 檔案。

#### 建立 Azure SQL 連結服務。

5. 再次以滑鼠右鍵按一下 [**方案總管**] 中的 [**連結服務**] 節點，指向 [**新增**]，然後按一下 [**新增項目**]。 
6. 這次，請選取 [**Azure SQL 連結服務**]，然後按一下 [**新增**]。 
7. 在 **AzureSqlLinkedService1.json 檔案**中，將 **servername**、**databasename**、**username@servername** 和 **password** 替換為您的 Azure SQL 伺服器名稱、資料庫、使用者帳戶和密碼。8.  儲存 **AzureSqlLinkedService1.json** 檔案。 


### 建立輸入和輸出資料表
在上一個步驟中，您已建立連結服務 **AzureStorageLinkedService1** 和 **AzureSqlLinkedService1**，將 Azure 儲存體帳戶和 Azure SQL Database 連結至 Data Factory：**ADFTutorialDataFactory**。在此步驟中，您將定義兩個 Data Factory 資料表：**EmpTableFromBlob** 和 **EmpSQLTable**AzureStorageLinkedService1 和 AzureSqlLinkedService1 所參照資料存放區中所儲存的輸入/輸出資料。您將針對 EmpTableFromBlob 指定所含 Blob 具有來源資料的 Blob 容器，而針對 EmpSQLTable 指定可儲存輸出資料的 SQL 資料表。

#### 建立輸入資料表

9. 以滑鼠右鍵按一下 [**方案總管**] 中的 [**資料表**]，指向 [**新增**]，然後按一下 [**新增項目**]。
10. 在 [**新增項目**] 對話方塊中，選取 [**Azure Blob**]，然後按一下 [**新增**]。   
10. 將 JSON 文字取代為下列文字並儲存 **AzureBlobLocation1.json** 檔案。 

		{
    		"name": "EmpTableFromBlob",
        	"properties":
        	{
            	"structure":  
                [ 
	                { "name": "FirstName", "type": "String"},
	                { "name": "LastName", "type": "String"}
	            ],
	            "location": 
	            {
	                "type": "AzureBlobLocation",
	                "folderPath": "adftutorial/",
	                "format":
	                {
	                    "type": "TextFormat",
	                    "columnDelimiter": ","
	                },
	                "linkedServiceName": "AzureStorageLinkedService1"
	            },
	            "availability": 
	            {
	                "frequency": "Hour",
	                "interval": 1,
	                "waitOnExternal": {}
                }
	        }
		}

#### 建立輸出資料表

11. 再次以滑鼠右鍵按一下 [**方案總管**] 中的 [**資料表**]，指向 [**新增**]，然後按一下 [**新增項目**]。
12. 在 [**新增項目**] 對話方塊中，選取 [**Azure SQL**]，然後按一下 [**新增**]。 
13. 將 JSON 文字取代成下列 JSON 並儲存 **AzureSqlTableLocation1.json** 檔案。

		{
		    "name": "EmpSQLTable",
		    "properties":
		    {
		        "structure":
		        [
		            { "name": "FirstName", "type": "String"},
		            { "name": "LastName", "type": "String"}
		        ],
		        "location":
		        {
		            "type": "AzureSqlTableLocation",
		            "tableName": "emp",
		            "linkedServiceName": "AzureSqlLinkedService1"
		        },
		        "availability": 
		        {
		            "frequency": "Hour",
		            "interval": 1            
		        }
		    }
		}

#### 建立管線 
您到目前為止已建立輸入/輸出連結服務和資料表。現在，您將使用**複製活動**建立管線，將資料從 Azure blob 複製到 Azure SQL Database。


1. 以滑鼠右鍵按一下 [**方案總管**] 中的 [**管線**]，指向 [**新增**]，然後按一下 [**新增項目**]。  
15. 選取 [**新增項目**] 對話方塊中的 [**複製資料管線**] 並按一下 [**新增**]。 
16. 將 JSON 取代為下列 JSON 並儲存 **CopyActivity1.json** 檔案..
			
		 {
		    "name": "ADFTutorialPipeline",
		    "properties":
		    {   
		        "description" : "Copy data from a blob to Azure SQL table",
		        "activities":   
		        [
		            {
		                "name": "CopyFromBlobToSQL",
		                "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		                "type": "CopyActivity",
		                "inputs": [ {"name": "EmpTableFromBlob"} ],
		                "outputs": [ {"name": "EmpSQLTable"} ],     
		                "transformation":
		                {
		                    "source":
		                    {                               
		                        "type": "BlobSource"
		                    },
		                    "sink":
		                    {
		                        "type": "SqlSink",
		                        "writeBatchSize": 10000,
		                        "writeBatchTimeout": "60:00:00"
		                    }   
		                },
		                "Policy":
		                {
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

#### 發佈/部署 Data Factory 實體
  
18. 在工具列區域中，如果 Data Factory 工作列尚未啟用，請按一下滑鼠右鍵並選取 [**Data Factory**] 加以啟用。 
19. 在 **Data Factory 工具列**中，按一下**下拉式方塊**以查看 Azure 訂用帳戶中的所有 Data Factory。如果您看到 [**登入 Visual Studio**] 對話方塊： 
	20. 輸入和您想要在其中建立 Data Factory 之 Azure 訂用帳戶相關聯的**電子郵件帳戶**，輸入**密碼**，然後按一下 [**登入**]。
	21. 一旦登入成功，您應該會看到 Azure 訂用帳戶中的所有 Data Factory。在本教學課程中，您將建立新的 Data Factory。       
22. 在下拉式清單中，選取 [**ADFTutorialFactoryVS**]，然後按一下 [**發佈**] 按鈕以部署/發佈連結服務、資料集和管線。    

	![發佈按鈕](./media/data-factory-get-started-using-vs/publish.png)

23. 您應該會看到上圖所示之 [Data Factory 工作清單] 視窗中的發佈狀態。確認已成功發佈。

## 使用 [伺服器總管] 檢視 Data Factory 實體

1. 在 **Visual Studio** 中，按一下功能表上的 [**檢視**]，然後按一下 [**伺服器總管**]。
2. 在 [伺服器總管] 視窗中，依序展開 **Azure** 和 **Data Factory**。如果您看到**登入 Visual Studio**，請輸入和 Azure 訂用帳戶相連的**帳戶**，然後按一下 [**繼續**]。輸入**密碼**，然後按一下 [**登入**]。Visual Studio 會嘗試取得訂用帳戶中所有 Azure Data Factory 的相關資訊。您會在 [**Data Factory 工作清單**] 視窗中看到這項作業的狀態。![Server Explorer](./media/data-factory-get-started-using-vs/server-explorer.png)
3. 您可以在 Data Factory 上按一下滑鼠右鍵，並選取 [將 Data Factory 匯出至新的專案]，以便根據現有的 Data Factory 建立 Visual Studio 專案。![匯出 Data Factory 至 VS 專案](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## 更新 Visual studio 的 Data Factory 工具
若要更新 Visual Studio 的 Azure Data Factory 工具，請執行下列作業：

1. 按一下功能表上的 [**工具**]，然後選取 [**擴充功能和更新**]。 
2. 選取左窗格中的 [**更新**]，然後選取 [**Visual Studio 組件庫**]。
4. 選取 [**Visual Studio 的 Azure Data Factory 工具**] 並按一下 [**更新**]。如果您看不到此項目，代表您已經有最新版本的工具。 

如需如何使用 Azure Preview 入口網站來監視您在本教學課程中建立的管線和資料集的指示，請參閱[監視資料集和管線](data-factory-get-started-using-editor.md/#MonitorDataSetsAndPipeline)。

<!---HONumber=July15_HO4-->