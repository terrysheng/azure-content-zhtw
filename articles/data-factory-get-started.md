<properties title="Get started using Azure Data Factory" pageTitle="開始使用 Azure Data Factory" description="本教學課程會示範如何建立將資料從 Blob 複製到 Azure SQL Database 執行個體的範例資料管線。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="spelluru" />

# 開始使用 Azure Data Factory
本文可協助您開始使用 Azure Data Factory。本文中的教學課程將示範如何建立 Azure Data Factory，以及在 Data Factory 中建立管線，以將範例資料從 Azure Blob 儲存體複製到 Azure SQL Database。

下列清單提供開發人員所需執行的一般步驟： 

1.	建立 **Azure Data Factory**。
2.	建立「連結服務」****，以將資料存放區和計算服務連結至 Data Factory。例如，連結服務可將 Azure SQL Database 或 HDInsight 叢集連結至 Data Factory。
3.	建立描述管線的輸入資料和輸出資料的「資料表」****。資料表也會指定資料在連結至 Data Factory 的資料存放區中實際的所在位置。例如，資料表可指定 Azure SQL Database 中或 Azure Blob 之 Blob 容器中的 SQL 資料表名稱。 
4.	建立「管線」****。管線由一或多個取用輸入資料並產生輸出資料的活動所組成。「複製」活動會將資料從來源資料存放區複製到目的地資料存放區，而 HDInsight 活動則會使用 Hive/Pig 指令碼來處理輸入資料。  
5.	指定管線的「作用中期間」**** (用於資料處理)。作用中期間會定義將產生資料配量的持續時間。您可以指定管線的開始日期時間和結束日期時間，或是讓它持續執行。

在本教學課程中，您將： 

1.	使用「Azure Preview 入口網站」****來建立資料存放區的 Azure Data Factory 和連結服務。
2.	使用 **Azure PowerShell** 來建立資料表和管線。該入口網站不支援在此版本中建立資料表和管線。


##必要條件
開始進行本教學課程之前，您必須具備下列條件：

- Azure 訂用帳戶。如需有關取得訂閱帳戶的詳細資訊，請參閱[購買選項] [azure-purchase-options], [成員優惠][azure-member-offers]，或[免費試用][azure-free-trial]。
- 在您的電腦上下載並安裝 [Azure PowerShell][download-azure-powershell]。
- 詳細閱讀 [Azure Data Factory 簡介][data-factory-introduction]主題。
- Azure 儲存體帳戶。在本教學課程中，您將使用 Blob 儲存體做為來源資料存放區。請參閱[關於儲存體帳戶][data-factory-create-storage]，以了解建立 Azure 儲存體的步驟。
- Azure SQL Database。在本教學課程中，您將建立一個範例資料庫，並使用它做為目的地資料存放區。請參閱[如何建立及設定 Azure SQL Database][data-factory-create-sql-database]，以了解建立 Azure SQL Database 的步驟。

##本教學課程內容

步驟 | 描述
-----| -----------
[步驟 1：建立 Azure Data Factory](#CreateDataFactory) | 在此步驟中，您將建立名為 **ADFTutorialDataFactory** 的 Azure Data Factory。 
[步驟 2：建立連結服務](#CreateLinkedServices) | 在此步驟中，您將建立兩個連結服務：**MyBlobStore** 和 **MyAzureSQLStore**。MyBlobStore 會連結 Azure 儲存體，而 MyAzureSQLStore 會將 Azure SQL Database 連結至 ADFTutorialDataFactory。
[步驟 3：建立輸入和輸出資料集](#CreateInputAndOutputDataSets) | 在此步驟中，您將定義兩個用來做為「複製活動」**** 之輸入和輸出的資料集 (**EmpTableFromBlob** 和 **EmpSQLTable**)，此活動包含在您將於下一個步驟建立的 ADFTutorialPipeline 中。
[步驟 4：建立及執行管線](#CreateAndRunAPipeline) | 在此步驟中，您將建立一個名為 **ADFTutorialPipeline** 的管線。此管線將有一個「複製活動」****，此活動會將資料從 Azure Blob 複製到輸出 Azure 資料庫資料表中。
[步驟 5：監視資料集和管線](#MonitorDataSetsAndPipeline) | 在此步驟中，您將使用 Azure Management Studio 來監視資料集和管線。
 


## <a name="CreateDataFactory"></a>步驟 1：建立 Azure Data Factory
在此步驟中，您將使用「Azure Preview 入口網站」來建立名為 **ADFTutorialDataFactory** 的 Azure Data Factory。

1.	登入 [Azure Preview 入口網站][azure-preview-portal]之後，請按一下左下角的 [新增]****，然後按一下 [新增]**** 刀鋒視窗上的 [Data Factory]****。 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	如果在 [新增]**** 刀鋒視窗上沒有看到 [Data Factory]****，請向下捲動。  


6. 在 [新增 Data Factory]**** 刀鋒視窗中：
	1. 輸入 **ADFTutorialDataFactory** 做為「名稱」****。 
	
  		![New data factory blade][image-data-factory-getstarted-new-data-factory-blade]
	2. 按一下 [資源群組名稱]****，然後執行下列動作：
		1. 按一下 [建立新的資源群組]****。
		2. 在 [建立資源群組]**** 刀鋒視窗中，輸入 **ADFTutorialResourceGroup** 做為資源群組的「名稱」****，然後按一下 [確定]****。 

			![Create Resource Group][image-data-factory-create-resource-group]

		本教學課程中的某些步驟是假設您使用名為 **ADFTutorialResourceGroup** 的資源群組。如果您使用不同的資源群組，將必須使用您在這裡選取的資源群組來取代 ADFTutorialResourceGroup。  
7. 在 [新增 Data Factory]**** 刀鋒視窗中，請注意 [新增到開始面板]**** 已被選取。
8. 在 [新增 Data Factory]**** 刀鋒視窗中，按一下 [建立]****。

	> [WACOM.NOTE] Azure Data Factory 的名稱在全域必須是唯一的。如果您收到下列錯誤：**Data Factory 名稱 "ADFTutorialDataFactory" 無法使用**，請變更名稱 (例如 yournameADFTutorialDataFactory)。執行本教學課程中的步驟時，請使用此名稱來取代 ADFTutorialFactory。  
	 
	![Data Factory name not available][image-data-factory-name-not-available]

9. 按一下位於左側的 [通知]**** 中樞，然後尋找來自建立程序的通知。
10. 建立完成之後，您會看到 [Data Factory] 刀鋒視窗，如下所示：
    ![Data factory home page][image-data-factory-get-stated-factory-home-page]

11. 您也可以從 [開始面板]**** 按一下 [ADFTutorialFactory]**** 來開啟它，如下所示： 

    ![Startboard][image-data-factory-get-started-startboard]    

## <a name="CreateLinkedServices"></a>步驟 2：建立連結服務
連結服務會將資料存放區或計算服務連結至 Azure Data Factory。資料存放區可以是 Azure 儲存體、Azure SQL Database 或在內部部署的 SQL Server 資料庫。

在此步驟中，您將建立兩個連結服務：**MyBlobStore** 和 **MyAzureSQLStore**。MyBlobStore 連結服務會連結 Azure 儲存體帳戶，而 MyAzureSQLStore 會將 Azure SQL Database 連結至 **ADFTutorialDataFactory**。您稍後將在本教學課程中建立管線，此管線會將資料從 MyBlobStore 中的 Blob 容器複製到 MyAzureSQLStore 中的 SQL 資料表。

### 建立 Azure 儲存體帳戶的連結服務
1.	在 [Data Factory]**** 刀鋒視窗中，按一下 [連結服務]**** 磚來啟動 [連結服務]**** 刀鋒視窗。

    ![Linked services link][image-data-factory-get-started-linked-services-link]

2. 在 [連結服務]**** 刀鋒視窗中，按一下 [新增資料存放區]****。

    ![Linked services add store][image-data-factory-get-started-linked-services-add-store-button]

3. 在 [新增資料存放區]**** 刀鋒視窗中：  
	1. 輸入資料存放區的「名稱」****。針對本教學課程的用途，請輸入 **MyBlobStore** 做為「名稱」****。
	2. 輸入資料存放區的 [描述]**** (選擇性)。
	3. 按一下 [類型]****。
	4. 選取 [Azuer 儲存體帳戶]****，然後按一下 [確定]****。
	
    ![New data store button][image-data-factory-linked-services-get-started-new-data-store]
  
4.  現在，您會回到 [新增資料存放區]**** 刀鋒視窗，如下所示：

    ![New data store blade][image-data-factory-get-started-new-data-store-with-storage]

5. 輸入您 Azure 儲存體帳戶的 [帳戶名稱]**** 和 [帳戶金鑰]****，然後按一下 [確定]****。   

6. 在 [新增資料存放區]**** 刀鋒視窗上按一下 [確定]**** 之後，您應該會在 [連結服務]**** 刀鋒視窗上的 [資料存放區]**** 清單中，看到 **myblobstore**。查看 [通知]**** 中樞 (位於左側) 中是否有任何訊息。

    ![linked services with blob store][image-data-factory-get-started-linked-services-list-with-myblobstore]

### 建立 Azure SQL Database 的連結服務
1. 在 [連結服務]**** 刀鋒視窗中，按一下工具列上的 [新增資料存放區]**** 以新增另一個資料來源 (Azure SQL Database)。
2. 在 [新增資料存放區] 刀鋒視窗中：
	1. 輸入資料存放區的「名稱」****。針對本教學課程的用途，請輸入 **MyAzureSQLStore** 做為「名稱」****。 
	2. 輸入存放區的 [描述] (選擇性)****。
	3. 按一下 [類型]****，然後選取 [Azure SQL Database]****。
3. 輸入 Azure SQL Database 的 [伺服器]****、[資料庫]****、[使用者名稱]**** 及 [密碼]****，然後按一下 [確定]****。

    ![Azure SQL properties][image-data-factory-get-started-linked-azure-sql-properties]

	
4. 在 [新增資料存放區]**** 刀鋒視窗上按一下 [確定]**** 之後，您應該就會在 [連結服務]**** 刀鋒視窗中看見那兩個存放區。

    ![Linked services with two stores][image-data-factory-get-started-linked-services-list-two-stores]
    

## <a name="CreateInputAndOutputDataSets"></a>步驟 3：建立輸入和輸出資料表

在上一個步驟中，您已建立 **MyBlobStore** 和 **MyAzureSQLStore** 連結服務，以將 Azure 儲存體帳戶和 Azure SQL Datbase 連結至資料存放區：**ADFTutorialDataFactory**。在此步驟中，您將建立代表「複製」活動之輸入和輸出資料的資料表，此活動包含在您將於下一個步驟建立的管線中。 

資料表是矩形的資料集，而且是目前唯一受支援的資料集類型。輸入資料表係指 MyBlobStore 所指向之 Azure 儲存體中的 Blob 容器，輸出資料表則是 MyAzureSQLStore 所指向之 Azure SQL 資料庫中的 SQL 資料表。  
 
Azure 預覽入口網站目前不支援建立資料集和管線，因此您將使用 Azure PowerShell Cmdlet 來建立資料表和管線。建立資料表之前，您必須先執行下列動作 (詳細步驟隨附於清單後)。

* 在 MyBlobStore 所指向的 Azure Blob 儲存體中，建立名為 **adftutorial** 的 Blob 容器。 
* 建立文字檔 **emp.txt** 並以 Blob 形式上傳至 **adftutorial** 容器。 
* 在 MyAzureSQLStore 所指向的 Azure SQL Database 中，建立名為 **emp** 的資料表。
* 在您的硬碟上，建立名為 **ADFGetStarted** 的資料夾。  

### 準備教學課程所需的 Azure Blob 儲存體和 Azure SQL 資料庫
1. 啟動「記事本」，貼上下列文字，然後將它在您硬碟上的 **C:\ADFGetStarted** 資料夾中儲存為 **emp.txt**。 

        John, Doe
		Jane, Doe
				
2. 使用 [Azure 儲存體總管](https://azurestorageexplorer.codeplex.com/)之類的工具來建立 **adftutorial** 容器，然後將 **emp.txt** 檔案上傳至該容器。

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
3. 使用下列 SQL 指令碼，在您的 Azure SQL Database 中建立 **emp** 資料表。您可以使用「Azure SQL 管理主控台」來連線到 Azure SQL Database，以及執行 SQL 指令碼。您也可以使用 SQL Server Management Studio 來執行這項工作。 


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 
		GO
				
	若要啟動「Azure SQL 管理主控台」，請按一下 [管理]****，如下圖所示：
 
	![Launch Azure SQL Management Console][image-data-factory-sql-management-console]

	![Azure SQL Management Console][image-data-factory-sql-management-console-2]
### 建立輸入資料表 
資料表是矩形的資料集，並具有的結構描述。在此步驟中，您將建立一個名為 **EmpBlobTable** 的資料表，此資料表指向 **MyBlobStore** 連結服務所代表之 Azure 儲存體中的 Blob 容器。


1. 在 Blob 的 emp.txt 中，為代表資料的 Data Factory 資料表建立 JSON 檔案。啟動 [記事本]，複製下列 JSON 指令碼，然後在 C:\ADFGetStarted 資料夾中將它儲存為 EmpBlobTable.json。


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
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1,
            		"waitonexternal": {}
       		 	}
    		}
		}

		
     請注意： 
	
	- location **type** 是設定為 **AzureBlobLocation**。
	- **linkedServiceName** 是設定為 **MyBlobStore**。您已在步驟 2 中建立此連結服務)。
	- **folderPath** 是設定為 **adftutorial** 容器。您也可以指定資料夾內的 Blob 的名稱。由於您未指定 Blob 的名稱，因此來自容器中所有 Blob 的資料都會被視為輸入資料。  
	- format **type** 是設定為 **TextFormat**。
	- - 文字檔中有兩個欄位 (**FirstName** 和 **ColumnName**)，以逗號字元 (columDelimiter) 分隔。	
	- **availability** 是設定為 **hourly** (**frequency** 是設定為 **hour**，而 **interval** 是設定為 **1**)。Data Factory 服務會每小時都在您指定之 Blob 容器 (**adftutorial**) 的根資料夾中尋找輸入資料。

	如果您未指定「輸入資料表」****的 **fileName**，則來自輸入資料夾 (**folderPath**) 的所有檔案/Blob 都會被視為輸入。如果您在 JSON 中指定 fileName，則只有指定的檔案/Blob 會被視為輸入。如需範例，請參閱[教學課程][adf-tutorial]中的範例檔案。
 
	如果您未指定「輸出資料表」**** 的 **fileName**，**folderPath** 中產生的檔案會以下列格式命名：Data.<Guid>.txt (例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.)。

	若要根據 **SliceStart** 時間動態設定 **folderPath** 和 **fileName**，請使用 partitionedBy 屬性。在下列範例中，folderPath 使用 SliceStart (處理配量的開始時間) 中的年、月和日，fileName 使用 SliceStart 中的小時。例如，若已針對 2014-10-20T08:00:00 產生配量，folderName 會設定為 wikidatagateway/wikisampledataout/2014/10/20 而 fileName 會設定為 08.csv。 

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	如需有關 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971)。

2. 啟動 **Azure PowerShell** 並執行下列命令，以切換至 **AzureResourceManager** 模式。在 **AzureResourceManager** 模式中可使用 Azure Data Factory Cmdlet。

         switch-azuremode AzureResourceManager
		
	If you haven't already done so, do the following:


	- 執行 **Add-AzureAccount**，並輸入與您用來登入「Azure Preview 入口網站」相同的使用者名稱和密碼。  
	- 執行 **Get-AzureSubscription** 以檢視此帳戶的所有訂用帳戶。
	- 執行 **Select-AzureSubscription** 以選取您您想要使用的訂用帳戶。此訂用帳戶應該與您在「Azure Preview 入口網站」中使用的相同。

3. 使用 **New-AzureDataFactoryTable** Cmdlet，以 **EmpBlobTable.json** 檔案建立輸入資料表。


         New-AzureDataFactoryTable  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpBlobTable.json 

	如需有關此 Cmdlet 及其他 Data Factory Cmdlet 的詳細資料，請參閱 [Data Factory Cmdlet 參考][cmdlet-reference]。
 
### 建立輸出資料表
在此步驟的這個部分，您將建立一個名為 **EmpSQLTable** 的輸出資料表，此資料表指向 **MyAzureSQLStore** 連結服務所代表之 Azure SQL Database 中的 SQL 資料表。 

1. 為代表 Azure SQL Database 中資料的 Data Factory 資料表建立一個 JSON 檔案。請啟動「記事本」，複製下列 JSON 指令碼，然後將它在 **C:\ADFGetStarted** 資料夾中儲存為 **EmpSQLTable.json**。



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
            		"type": "AzureSQLTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "MyAzureSQLStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1            
        		}
    		}
		}


		
     請注意： 
	
	* location **type** 是設定為 **AzureSQLTableLocation**。
	* **linkedServiceName** 是設定為 **MyAzureSQLStore** (您已在步驟 2 中建立此連結服務)。
	* **tablename** 是設定為 **emp**。
	* 資料庫的 emp 資料表中有三個資料行：**ID**、**FirstName** 及 **LastName**，但是由於 ID 是身分識別資料行，因此在這裡您只需要指定 **FirstName** 和 **LastName**。
	* **availability** 是設定為 **hourly** (frequency 是設定為 hour，而 interval 是設定為 1)。Data Factory 服務會每小時都在 Azure SQL Database 的 **emp** 資料表中產生輸出資料配量。


2. 在 **Azure PowerShell** 中執行下列命令，以建立另一個 Data Factory 資料表來代表 Azure SQL Database 中的 **emp** 資料表。



         New-AzureDataFactoryTable -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpSQLTable.json -ResourceGroupName ADFTutorialResourceGroup 



## <a name="CreateAndRunAPipeline"></a>步驟 4：建立及執行管線
在此步驟中，您將建立一個含有「複製活動」****的管線，此活動會以 **EmpTableFromBlob** 做為輸入並以 **EmpSQLTable** 做為輸出。

1. 建立管線的 JSON 檔案。請啟動「記事本」，複製下列 JSON 指令碼，然後將它在 **C:\ADFGetStarted** 資料夾中儲存為 **ADFTutorialPipeline.json**。


         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Sql Azure",
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
								"type": "SqlSink"
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
        		]
      		}
		} 

	請注意：

	- 在 activities 區段中，只有一個活動，其 **type** 是設定為 **CopyActivity**。
	- 此活動的輸入是設定為 **EmpTableFromBlob**，而此活動的輸出是設定為 **EmpSQLTable**。
	- 在 **transformation** 區段中，**BlobSource** 是指定為 source 類型，而 **SqlSink** 是指定為 sink 類型。

2. 使用 **New-AzureDataFactoryPipeline** Cmdlet，以您已建立的 **ADFTutorialPipeline.json** 檔案建立管線。



         New-AzureDataFactoryPipeline  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\ADFTutorialPipeline.json  

3. 管線建立之後，您可以指定將進行資料處理的期間。藉由指定管線的作用中期間，您會定義將根據為每個 Azure Data Factory 資料表定義的可用性屬性來處理資料配量的持續時間。執行下列 PowerShell 命令以設定管線的作用中期間，然後輸入 Y 以進行確認。 



         Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline  

	> [WACOM.NOTE] 請以目前的日期取代 **StartDateTime** 值，以隔天的日期取代 **EndDateTime** 值。StartDateTime 和 EndDateTime 都是 UTC 時間，且必須採用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。**EndDateTime** 為選擇性，但是我們會在本教學課程中使用。 
	> 如果您未指定 **EndDateTime**，系統會將其計算為「StartDateTime + 48 小時」****。若要無限期執行管線，請指定 **9/9/9999** 做為 **EndDateTime**。  
	
	在上述範例中，由於每小時即產生一個資料配量，共會有 24 個資料配量。

4. 在「Azure 入口網站」****中，於 **ADFTutorialDataFactory** 的 [Data Factory]**** 刀鋒視窗中，按一下 [圖表]****。

	![Diagram link][image-data-factory-get-started-diagram-link]
  
5. 您應該會看到如下所示的圖表：(按兩下標題，即可查看刀鋒視窗所代表之成品的相關詳細資訊)。

	![Diagram view][image-data-factory-get-started-diagram-blade]

**恭喜！**您已成功建立 Azure Data Factory、連結服務、資料表及管線，並已排定管線。

## <a name="MonitorDataSetsAndPipeline"></a>步驟 5：監視資料集和管線
在此步驟中，您將使用 Azure 入口網站來監視 Azure Data Factory 的運作情形。您也可以使用 PowerShell Cmdlet 來監視資料集和管線。如需有關使用 Cmdlet 進行監視的詳細資訊，請參閱[使用 PowerShell Cmdlet 來監視和管理 Data Factory][monitor-manage-using-powershell]。

1. 瀏覽到 [Azure 入口網站 (Preview)][azure-preview-portal] (如果尚未開啟)。 
2. 如果 **ADFTutorialDataFactory** 的刀鋒視窗未開啟，請在 [開始面板]**** 上按一下 [ADFTutorialDataFactory]****。 
3. 您應該會看到您在此分頁上建立之資料表和管線的計數和名稱。

	![home page with names][image-data-factory-get-started-home-page-pipeline-tables]

4. 現在，按一下 [資料集]**** 磚。
5. 在 [資料集]**** 刀鋒視窗中，按一下 [EmpTableFromBlob]****。這是 **ADFTutorialPipeline** 的輸入資料表。

	![Datasets with EmpTableFromBlob selected][image-data-factory-get-started-datasets-emptable-selected]   
5. 請注意，截至目前為止的資料配量都已產生，並且都已 [就緒]****，因為 **emp.txt** 檔案一直都存在於 Blob 容器中：**adftutorial\input**。請確認沒有任何配量出現在下方的 [問題配量]**** 區段中。
6. 現在，在 [資料集]**** 刀鋒視窗中，按一下 [EmpSQLTable]****。這是 **ADFTutorialPipeline** 的輸出資料表。

	![data sets blade][image-data-factory-get-started-datasets-blade]

6. 您應該會看到 [EmpSQLTable]**** 刀鋒視窗，如下所示：

	![table blade][image-data-factory-get-started-table-blade]
 
7. 請注意，截至目前為止的資料配量都已產生，並且都已 [就緒]****。沒有任何配量出現在下方的 [問題配量]**** 區段中。
8. 按一下 [...] (省略符號)**** 以查看所有配量。

	![data slices blade][image-data-factory-get-started-dataslices-blade]

9. 從清單中按一下任何資料配量，您應該會看到 [資料配量]**** 刀鋒視窗。

	![data slice blade][image-data-factory-get-started-dataslice-blade]
  
11. 在 [資料配量]**** 刀鋒視窗中，您應該會在底部清單中看到所有活動執行項目。按一下 [活動執行]**** 以查看 [活動執行詳細資料]**** 刀鋒視窗。 

	![Activity Run Details][image-data-factory-get-started-activity-run-details]

12. 按一下 [X]**** 以關閉所有刀鋒視窗，直到您回到 **ADFTutorialDataFactory** 的首頁刀鋒視窗。
14. (選擇性) 在 **ADFTutorialDataFactory** 的首頁上按一下 [管線]****，在 [管線]**** 刀鋒視窗中按一下 [ADFTutorialPipeline]****，然後鑽研輸入資料表 (已取用****) 或輸出資料表 (已產生****)。
15. 啟動 **SQL Server Management Studio**、連線到 Azure SQL Database，然後確認資料列已插入資料庫的 **emp** 資料表中。

	![sql query results][image-data-factory-get-started-sql-query-results]


## 摘要 
在本教學課程中，您已建立要將資料從 Azure Blob 複製到 Azure SQL 資料庫的 Azure Data Factory。您使用 Azure 預覽入口網站建立了 Data Factory 和連結服務。您使用 Azure PowerShell Cmdlet 建立了資料表和管線，然後完成管線的排程。以下是您在本教學課程中執行的概略步驟：  

1.	建立 Azure **Data Factory**。
2.	建立將資料存放區和計算服務 (稱為「連結服務」****) 連結到 Data Factory 的「連結服務」****。
3.	建立描述管線之輸入資料和輸出資料的「資料表」****。
4.	建立「管線」****。管線是由一或多個活動所組成，會處理輸入並產生輸出。 
5.	指定管線的「作用中期間」**** (用於資料處理)。作用中期間會定義將產生資料配量的持續時間。

## 後續步驟

文章 | 描述
------ | ---------------
[教學課程：使用 Data Factory 來移動和處理記錄檔][adf-tutorial] | 本文提供「端對端逐步解說」****，示範如何使用 Azure Data Factory 將記錄檔中的資料轉換成深入資訊，來實作「真實世界案例」****。
[使用 Azure Data Factory 來複製資料 - 複製活動][copy-activity] | 本文提供在本教學課程中使用之「複製活動」****的詳細描述。
[讓您的管線能夠與內部部署資料搭配使用][use-onpremises-datasources] | 本文提供逐步解說，示範如何將資料從「內部部署 SQL Server 資料庫」****複製到 Azure Blob。
[使用 Pig 和 Hive 搭配 Data Factory][use-pig-and-hive-with-data-factory] | 本文提供逐步解說，示範如何使用「HDInsight 活動」****來執行 **hive/pig** 指令碼，以處理輸入資料來產生輸出資料。
[在 Data Factory 中使用自訂活動][use-custom-activities] | 本文提供逐步解說，內含建立「自訂活動」****並在管線中使用此活動的逐步指示。
[使用 PowerShell 來監視和管理 Azure Data Factory][monitor-manage-using-powershell] | 本文說明如何使用 **Azure PowerShell Cmdlet** 來「監視和管理」****Azure Data Factory。您可以在 ADFTutorialDataFactory 上試試在這篇文章中的範例。
[Data Factory 問題疑難排解][troubleshoot] | 本文說明如何對 Azure Data Factory 問題進行「疑難排解」****。您可以透過產生一個錯誤 (刪除 Azure SQL Database 中的資料表)，嘗試本文中針對 ADFTutorialDataFactory 所做的逐步解說 
[Azure Data Factory Cmdlet 參考][cmdlet-reference] | 本參考內容提供所有 **Data Factory Cmdlet** 的相關詳細資料。
[Azure Data Factory 開發人員參考][developer-reference] |＜開發人員參考＞提供 Cmdlet、JSON 指令碼、函式等等的完整參考內容。 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/zh-tw/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/zh-tw/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/zh-tw/pricing/free-trial/

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/zh-tw/documentation/articles/install-configure-powershell

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[copy-activity]: ../data-factory-copy-activity/
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction
[data-factory-create-storage]: ../storage-whatis-account
[data-factory-create-sql-database]: ../sql-database-create-configure/


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png

<!--HONumber=35.2-->
