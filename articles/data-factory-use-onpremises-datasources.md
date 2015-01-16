<properties title="Enable your pipelines to work with on-premises data" pageTitle="啟用管線以使用內部部署資料 | Azure Data Factory" description="了解如何使用 Azure Data Factory 註冊內部部署資料來源，並將資料複製到資料來源/複製資料來源中的資料。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# 啟用管線以使用內部部署資料

若要啟用 Azure Data Factory 中的管線以使用內部部署資料來源，您需要運用 Azure 管理入口網站或 Azure PowerShell 將內部部署資料來源新增為 Data Factory 的連結服務。
 
若要將內部部署資料來源新增為 Data Factory 的連結服務，您必須先在內部部署電腦上下載並安裝「Microsoft 資料管理閘道」，然後設定內部部署資料來源的連結服務使用閘道。
 
> [WACOM.NOTE] 目前支援的內部部署資料來源只有 SQL Server。

## <a href="DMG"></a> 資料管理閘道

**資料管理閘道**是一套透過安全且可管理的方式，將內部部署資料來源連結至雲端服務的軟體。您可以使用「資料管理閘道」來進行下列作業：

- **連接至內部部署資料以供混合式資料存取** - 您可以將內部部署資料連結至雲端服務，即可運用雲端服務，同時不影響企業使用內部部署資料的營運。
- **定義安全的資料 Proxy** - 您可以定義要對「資料管理閘道」公開哪些內部部署資料來源，閘道會依此驗證來自雲端服務的資料要求，並保護內部部署資料來源。
- **管理您的閘道以達到完整控管** - 您將可完整地監視與記錄「資料管理閘道」內的所有活動，以便您加以管控。
- **有效率地移動資料** - 資料會以平行方式傳輸，且系統會採用自動重試邏輯，修復間歇性網路問題。


「資料管理閘道」提供全方位內部部署資料連線功能，包括下列：

- **不會干擾公司防火牆運作** - 資料管理閘道即裝即用，無需開啟防火牆連線或變更您公司網路基礎結構的配置。
- **透過您的憑證加密認證** - 用來連線到資料來源的認證會以完全由使用者所擁有的憑證加密。若沒有憑證，任何人 (包括 Microsoft 在內) 皆無法將認證解密成純文字。

### 閘道安裝 - 最佳作法

1.	如果主機電腦休眠，閘道即無法回應資料要求。因此，安裝閘道之前，請先在電腦上設定適當的**電源計劃**。 
2.	資料管理閘道應該能夠連線到將向 Azure Data Factory 服務註冊的內部部署資料來源。雖然不需要在相同的電腦上，但**接近資料來源**可縮短閘道連線到資料來源的時間。

### 使用資料管理閘道的考量
1.	資料管理閘道的單一執行個體可用於多個內部資料來源，但是請記住**閘道會繫結至 Azure Data Factory**，且不能與另一個 Data Factory 共用閘道。
2.	電腦上**只能安裝一個資料管理閘道的執行個體**。假設您有兩個需要存取內部部署資料來源的 Data Factory，您需要將繫結至不同 Data Factory 的閘道個別安裝在兩部內部部署電腦上。
3.	若您已在電腦上安裝用於 **Power BI** 案例的閘道，請於另一台電腦上安裝**另一個用於 Azure Data Factory 的閘道**。 
 

## 逐步介紹

在本逐步解說中，您可以使用將資料從內部部署 SQL Server 資料庫移至 Azure Blob 的管線，來建立一個 Data Factory。 

### 步驟 1：建立 Azure Data Factory
在此步驟中，您可以使用 Azure 管理入口網站來建立名為 **ADFTutorialOnPremDF** 的 Azure Data Factory 執行個體。您也可以使用 Azure Data Factory Cmdlet 來建立 Data Factory。 

1.	登入 [Azure 預覽入口網站][
2.	azure-preview-portal] 之後，從左下角按一下 [**新增**]，然後在 [**新增**] 分頁上按一下 [**Data Factory**]。

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	如果您在 [**新增**] 分頁上沒看見 [**Data Factory**]，請向下捲動。  
6. 在 [**新增 Data Factory**] 分頁中：
	1. 輸入 **ADFTutorialOnPremDF** 做為**名稱**。
	2. 按一下**資源群組名稱**，然後選取 **ADFTutorialResourceGroup** (如果您已經完成教學課程[開始使用 Azure Data Factory][adfgetstarted]。您可以選取現有的資源群組，或建立一個新的群組。若要建立新的資源群組：
		1. 按一下 [**建立新的資源群組**]。
		2. 在**建立資源群組分頁**中，輸入資源群組的**名稱**，然後按一下 [**確定**]。

7. 請注意 [**新增 Data Factory**] 分頁上已核取 [**Add to Startboard**]。

	![Add to Startboard][image-data-factory-add-to-startboard]

8. 在 [**新增 Data Factory**] 分頁中，按一下 [**建立**]。
9. 尋找左側**通知**中心中來自建立程序的通知。

	![NOTIFICATIONS hub][image-data-factory-notifications-hub]

11. 建立完成之後，您會看到 Data Factory 分頁，如下所示：

	![Data Factory Home Page][image-data-factory-datafactory-homepage]

12. 您應該也會看到它在 [**開始面板**] 中，如下所示。若其尚未開啟，按一下即可開啟 **Data Factory 分頁**。

	![Startboard][image-data-factory-startboard]

### 步驟 2：建立連結服務 
在此步驟中，您將建立兩個連結服務：**MyBlobStore** 和 **OnPremSqlLinkedService**。**OnPremSqlLinkedService** 連結內部部署 SQL Server 資料庫，而 **MyBlobStore** 連結服務會將 Azure Blob 存放區連結至 **ADFTutorialDataFactory**。稍後在本逐步解說中，您將建立可將內部部署 SQL Server 資料庫的資料複製到 Azure Blob 存放區的管線。 

### 在內部部署 SQL Server 資料庫中新增連結服務
1.	在 **ADFTutorialOnPremDF** 的 **Data Factory** 分頁上，按一下 [**連結服務**]。 

	![Data Factory Home Page][image-data-factory-home-age]

2.	在 [**連結服務**] 分頁上，按一下 [**+ 資料閘道**]。

	![Linked Services - Add a Gateway button][image-data-factory-linkedservices-add-gateway-button]

2. 在 [**建立**] 分頁上，輸入 **adftutorialgateway** 做為**名稱**，然後按一下 [**確定**]。 	

	![Create Gateway blade][image-data-factory-create-gateway-blade]

3. 在 [**設定**] 分頁中，按一下 [**Install directly on this computer**]。這會下載閘道的安裝套件、在電腦上安裝、設定和註冊閘道。

	![Gateway - Configure blade][image-data-factory-gateway-configure-blade]

	這是最簡單的方式 (一鍵)，透過單一步驟即可下載、安裝、設定和註冊閘道。您可以看到 **Microsoft 資料管理閘道組態管理員**應用程式已安裝在電腦上。您也可以在下列資料夾中找到可執行檔 **ConfigManager.exe**：**C:\Program Files\Microsoft Data Management Gateway\1.0\Shared**。

	您也可以使用此分頁中的連結手動下載與安裝閘道，並使用 [**REGISTER WITH KEY**] 文字方塊中顯示的金鑰來加以註冊。
	
	如需閘道的相關詳細資訊 (包括最佳作法和重要考量)，請參閱＜[資料管理閘道](#DMG)＞一節。 

4. 在 [**新增資料閘道**] 分頁上，按一下 [**確定**]。
5. 在您的電腦上，啟動 **Microsoft 資料管理閘道組態管理員**應用程式  。

	![Gateway Configuration Manager][image-data-factory-gateway-configuration-manager]

6. 請稍候，待這些值設定完成，如下所示：
	1. 如果服務**狀態**未設為 [**已啟動**]，請按一下 [**啟動服務**] 來啟動服務，並等候一分鐘，待其他欄位重新整理完成。
	2. **閘道名稱**設為 **adftutorialgateway**。
	3. **執行個體名稱**設為 **adftutorialgateway**。
	4. **閘道金鑰狀態**設定為 [**已註冊**]。
	5. 底部的狀態列會顯示「**已連線到資料管理閘道雲端服務**」和一個**綠色的核取記號**。  

7. 在 [**連結服務**] 分頁上，確認閘道的**狀態**為**良好**，然後按一下 [**+ 資料存放區**]。您可能需要關閉並重新開啟分頁，來重新整理分頁。 

	![Add Data Store button][image-data-factory-add-datastore-SQL-button]

8. 在 [**新增資料存放區**] 分頁中，輸入 **OnPremSqlLinkedService** 做為**名稱**。
9. 按一下**類型 (所需的設定)**，並選取 **SQL Server**。 
10. 在 [**新增資料存放區**] 分頁中，按一下**資料閘道 (配置所需的設定)**。

	![Data Gateway Configure link][image-data-factory-gateway-configure-link]
  
11. 選取您稍早建立的 **adftutorialgateway**。 
12.	在 [**新增資料存放區**] 分頁中，按一下**認證**以查看 [**認證**] 分頁。

	![Data Source Credentials Blade][image-data-factory-credentials-blade]

13.	在 [**認證**] 分頁上，按一下 [**Click here to set Credentials securely**]。它會啟動以下快顯對話方塊。

	![One Click application install][image-data-factory-oneclick-install]

14. 按一下 [**執行**] 以安裝 [**認證管理員**] 應用程式，您即會看到 [設定認證] 對話方塊。 
15.	在 [**設定認證**] 對話方塊中，輸入服務可用來存取內部部署 SQL Server 資料庫的**使用者名稱**和**密碼**，然後按一下 [**確定**]。此對話方塊僅支援 **SQL 驗證**。請稍候，待對話方塊關閉。
16.	在 [**認證**] 分頁上，按一下 [**確定**]，然後在 [**新增資料存放區**] 分頁上按一下 [**確定**]。您應該會看到您在 [連結服務] 分頁上新增的 **OnPremSqlLinkedService** 連結服務。

	![Linked Services Blade with OnPrem Store][image-data-factory-linkedservices-blade-onprem]

	
   


#### 新增 Azure 儲存體帳戶的連結服務

1.	在 [**Data Factory**] 分頁中，按一下 [**連結服務**] 磚，以啟動 [**連結服務**] 分頁。
2. 在 [**連結服務**] 分頁中，按一下 [**Add data store**]。	
3. 在 [**新增資料存放區**] 分頁中：
	1. 輸入資料存放區的**名稱**。依據本教學課程的用途，輸入 **MyBlobStore** 做為**名稱**。
	2. 輸入資料存放區的**說明 (選用)**。
	3. 按一下 [**類型**]。
	4. 選取 [**Azure 儲存體帳戶**]，然後按一下 [**確定**]。
		
		![Azure Storage Account][image-data-factory-azure-storage-account]

4. 現在，您已回到 [**新增資料存放區**] 分頁，如下所示：

	![Azure Storage settings][image-data-factory-azure-storage-settings]

5. 輸入您 Azure 儲存體帳戶的 [**帳戶名稱**] 和 [**帳戶金鑰**]，然後按一下 [**確定**]。

  
6. 在 [**新增資料存放區**] 分頁上按一下 [**確定**] 之後，您應該會在 [**連結服務**] 分頁的 [**資料存放區**] 清單中看到 **MyBlobStore**。查看 [**通知中樞**] (位於左側) 中是否有任何訊息。

	![Linked Services blade with MyBlobStore][image-data-factory-linkedservices-with-storage]

 
## 步驟 3：建立輸入和輸出資料集
在此步驟中，您會建立代表複製作業的輸入和輸出資料的輸入和輸出資料集 (內部部署 SQL Server 資料庫 => Azure Blob 儲存體)。Azure 入口網站尚未支援建立資料集和管線，因此會使用 Azure PowerShell Cmdlet 來建立資料表和管線。在建立資料集或資料表 (矩形資料集) 之前，您必須執行下列動作 (清單後列有詳細步驟)：

- 在您新增為 Data Factory 連結服務的 SQL Server 資料庫中，建立名為 **emp** 的資料表，並在資料表中插入幾個範例項目。
- - 如果您尚未從[開始使用 Azure Data Factory][adfgetstarted] 一文完成教學課程，請在您新增為 Data Factory 連結服務的 Azure blob 儲存體帳戶中，建立名為 **adftutorial** 的 Blob 容器。

### 準備用於教學課程的內部部署 SQL Server

1. 在您指定用於內部部署 SQL Server 的資料庫中連結服務 (**OnPremSqlLinkedService**) 的資料庫中，使用下列 SQL 指令碼在資料庫中建立 **emp** 資料表。


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. 在資料表中插入一些範例： 


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### 建立輸入資料表

1.	建立 Data Factory 資料表的 JSON 檔，代表來自 SQL Server 資料庫中的 **emp** 資料表的資料。啟動 [**記事本**]，複製下列 JSON 指令碼，並將它儲存至 C:\ADFGetStarted\**OnPrem** 資料夾中，命名為 **EmpOnPremSQLTable.json**。如果資料夾不存在，請在 **C:\ADFGetStarted** 資料夾中建立 **OnPrem** 子資料夾。 


        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "OnPremSqlLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1,       
	    			"waitOnExternal":
	    			{
        				"retryInterval": "00:01:00",
	        			"retryTimeout": "00:10:00",
	        			"maximumRetry": 3
	    			}
		  
        		}
    		}
		}

	請注意： 
	
	- 位置**類型**設定為 **OnPremisesSqlServerTableLocation**。
	- **tableName** 設定為 **emp**。
	- **linkedServiceName** 設定為 **OnPremSqlLinkedService** (您已在步驟 2 中建立此連結服務)。
	- 針對由 Azure Data Factory 中另一個管線產生的輸入資料表，您必須指定 JSON 中的 **waitOnExternal** 區段。它代表輸入資料產生於 Azure Data Factory 服務外部。   

	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考][json-script-reference]。

2. **AzureResourceManager** 模式中可以使用 Azure Data Factory Cmdlet。啟動 **Azure PowerShell**，然後執行下列命令以切換至 **AzureResourceManager** 模式。     

        switch-azuremode AzureResourceManager

	若電腦上尚未安裝，請下載 [Azure PowerShell][azure-powershell-install]。
3. 使用 **New-AzureDataFactoryTable** Cmdlet 來建立使用 **EmpOnPremSQLTable.json** 檔的輸入資料表。

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\EmpOnPremSQLTable.json  
	
	如果您使用不同資源群組，請更新命令。

### 建立輸出資料表

1.	建立 Data Factory 資料表的 JSON 檔，做為您將在下一個步驟中建立的管線輸出。啟動 [記事本]，複製下列 JSON 指令碼，並將它儲存至 **C:\ADFGetStarted\OnPrem** 資料夾中，命名為 **OutputBlobTable.json**。

		{
    		"name": "OutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/outfromonpremdf",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	請注意： 
	
	- 位置**類型**設定為 **AzureBlobLocation**。
	- **linkedServiceName** 設定為 **MyBlobStore** (您已在步驟 2 中建立此連結服務)。
	- **folderPath** 設定為 **adftutorial/outfromonpremdf**，其中 outfromonpremdf 是 adftutorial 容器中的資料夾。您只需要建立 **adftutorial** 容器。
	- **可用性**設定為**每小時** (**頻率**設定為 **小時** 和**間隔**設定為 **1**)。  Data Factory 服務將會每小時在 Azure SQL Database 內 **emp** 資料表中產生輸出的資料配量。 

	如果您沒有指定**輸入資料表**的 **fileName**，則輸入資料夾 (**folderPath**) 中的所有檔案都會被視為輸入。如果您在 JSON 中指定 fileName，則只有指定的檔案/Blob 會被視為輸入。如需範例，請參閱[教學課程][adf-tutorial] 中的範例檔案。
 
	如果您未指定**輸出資料表**的 **fileName**，**folderPath** 中產生的檔案會依照下列命名格式：Data.<Guid>.txt (例如：: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.)。

	若要根據 **SliceStart** 時間動態設定 **folderPath** 和 **fileName**，請使用 partitionedBy 屬性。在下列範例中，folderPath 使用 SliceStart (處理配量的開始時間) 中的年、月和日，fileName 使用 SliceStart 中的小時。例如，如果配量產生於 2014-10-20T08:00:00，folderName 設定為 wikidatagateway/wikisampledataout/2014/10/20，而 fileName 設定為 08.csv。 

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考][json-script-reference]。

2.	在 **Azure PowerShell** 中，執行下列命令以建立另一個 Data Factory 資料表，來代表 Azure SQL Database 中的資料表。

		New-AzureDataFactoryTable -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\OutputBlobTable.json -ResourceGroupName ADFTutorialResourceGroup  

## 步驟 4：建立及執行管線
在此步驟中，您建立有一個**複製活動**，且使用 **EmpOnPremSQLTable** 做為輸入和 **OutputBlobTable** 做為輸出的**管線**。

1.	建立管線的 JSON 檔案。啟動 [記事本]，複製下列 JSON 指令碼，並將它儲存至**C:\ADFGetStarted\OnPrem** 資料夾中，命名為 **ADFTutorialPipelineOnPrem.json**。
	 

        {
			"name": "ADFTutorialPipelineOnPrem",
    		"properties":
    		{
        		"description" : "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
	       		 "activities":
	        	[
			    	{
						"name": "CopyFromSQLtoBlob",
						"description": "Copy data from on-prem SQL server to blob",		
						"type": "CopyActivity",
						"inputs": [ {"name": "EmpOnPremSQLTable"} ],
						"outputs": [ {"name": "OutputBlobTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "SqlSource",
								"sqlReaderQuery": "select * from emp"
							},
							"sink":
							{
								"type": "BlobSink"
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
 
	- 在活動區段中，沒有**類型**設定為 **CopyActivity** 的活動。
	- 活動的**輸入**設定為 **EmpOnPremSQLTable** 和活動的**輸出**設定為 **OutputBlobTable**。
	- 在**轉換**區段中，[**sqlsource**] 指定為**來源類型**和 **BlobSink ** 指定為**接收類型**。
	- 指定針對 **SqlSource** 的 **sqlReaderQuery** 屬性指定 SQL 查詢 **select * from emp**。

2. 使用 **New-AzureDataFactoryPipeline** Cmdlet，透過您已建立的 **ADFTutorialPipeline.json** 檔案來建立管線。

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\ADFTutorialPipelineOnPrem.json -ResourceGroupName ADFTutorialResourceGroup  

3. 一旦建立管線，您可以指定將用於資料處理的持續時間。指定管線作用中期間，即定義處理資料配量的持續時間 (根據為每個 Azure Data Factory 資料表定義的**可用性**屬性)。  執行下列 PowerShell 命令以在管線上設定作用中期間，然後輸入 Y 表示確認。 


		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineOnPrem  

	> [WACOM.NOTE] 以目前的日期取代 **StartDateTime** 值，以隔天的日期取代 **EndDateTime** 值。StartDateTime 和 EndDateTime 都是 UTC 時間，且必須採用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。EndDateTime 是選擇性的，但是我們將在本教學課程中使用此值。
	> 如果您未指定 **EndDateTime**，系統會計算為 "**StartDateTime + 48 小時**"。若要無限期地執行管線， 請將 **EndDateTime** 指定為 **9/9/9999**。

	在上述範例中，由於每小時即產生一個資料配量，共會有 24 個資料配量。
4. 在 **Azure 預覽入口網站**中，針對 **ADFTutorialOnPremDF** Data Factory 在首頁上按一下 [**圖表**] 磚。 :

	![Diagram Link][image-data-factory-diagram-link]

5. 您應該會看到如下所示的圖表：

	![Diagram View][image-data-factory-diagram-view]

	**恭喜！**您已成功建立 Azure Data Factory、連結服務、資料表和 	管線，並已啟動工作流程。

## 步驟 5：監視資料集和管線
在此步驟中，您將使用 Azure 入口網站來監視 Azure Data Factory 的運作情形。您也可以使用 PowerShell Cmdlet 來監視資料集和管線。如需使用 Cmdlet 進行監視的詳細資訊，請參閱[使用 PowerShell 監視和管理 Azure Data Factory][monitor-manage-powershell]。

1. 瀏覽至 **Azure 預覽入口網站** (如果您已關閉它)
2. 如果 [**ADFTutorialOnPremDF**] 分頁尚未開啟，請按一下 [**開始面板**] 上的 **ADFTutorialOnPremDF** 即可加以開啟。
3. 您應該會看到的在此分頁上建立之資料表及管線的**計數**和**名稱**。

	![Data Factory Home Page][image-data-factory-homepage-2]
4. 現在，按一下 [**資料集**] 磚。
5. 在 [**資料集**] 分頁上，按一下 **EmpOnPremSQLTable**。

	![EmpOnPremSQLTable slices][image-data-factory-onprem-sqltable-slices]

6. 請注意，到目前為止已經產生資料配量，且它們的狀態為 [**就緒**]。這是因為您已將資料插入 SQL Server 資料庫中，而資料一直留存於其中。確認下方的 [**問題配量**] 區段中沒有任何配量。
7. 現在，在 [**資料集**] 分頁中，按一下 **OutputBlobTable**。

	![OputputBlobTable slices][image-data-factory-output-blobtable-slices]
8. 確認到目前為止已經產生配量，且其狀態為 [**就緒**]。
9. 按一下清單中的任何資料配量，您應該會看到 [**資料配量**] 分頁。

	![Data Slice Blade][image-data-factory-dataslice-blade]
10. 按一下底部清單中的**活動執行**，可查看**活動執行詳細資料**。

	![Activity Run Details blade][image-data-factory-activity-run-details]

11. 按一下 **X** 關閉所有分頁，直到您回到 **ADFTutorialOnPremDF** 的起始分頁。
14. (選用) 依序按一下 [**管線**] 及 **ADFTutorialOnPremDF**，然後逐步深入輸入資料表 (**已取用**) 或輸出資料表 (**已產生**)。
15. 使用 **Azure 儲存體總管** 之類的工具來驗證輸出。

	![Azure Storage Explorer][image-data-factory-stroage-explorer]


## 使用 Azure PowerShell Cmdlet 建立和註冊閘道
本節說明如何使用 Azure PowerShell Cmdlet 建立和註冊閘道。 

1. 在系統管理員模式下啟動 **Azure PowerShell**。 
2. **AzureResourceManager** 模式中可以使用 Azure Data Factory Cmdlet。執行下列命令以切換至 **AzureResourceManager** 模式。     

        switch-azuremode AzureResourceManager


2. 使用 **New-AzureDataFactoryGateway** Cmdlet 來建立邏輯閘道，如下所示：

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description <desc>

	**範例命令和輸出**：


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description "gateway for walkthrough"

		Name            : MyGateway
		Location        : West US
		Description     : gateway for walkthrough
		Version         :
		Status          : NeedRegistration
		VersionStatus   : None
		CreateTime      : 9/28/2014 10:58:22
		RegisterTime    :
		LastConnectTime :
		ExpiryTime      :


3. 使用 **New-AzureDataFactoryGatewayKey** Cmdlet 來為新建立的閘道產生註冊金鑰，並使用本機變數 **$Key** 儲存該金鑰：

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName $df 

	
	**範例命令輸出：**


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. 在 Azure PowerShell 中，切換到資料夾：**C:\Program Files\Microsoft Data Management Gateway\1.0\PowerShellScript\**，然後執行與本機變數 **$Key** 相關聯的 **RegisterGateway.ps1** 指令碼，如以下命令所示，以註冊您電腦上使用邏輯閘道 (稍早建立) 安裝的用戶端代理程式。

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. 若要取得 Data Factory 中的閘道清單，您可以使用 **Get-AzureDataFactoryGateway** Cmdlet。當 [**狀態**] 顯示為**線上**時，表示您的閘道已就緒可供使用。

		Get-AzureDataFactoryGateway -DataFactoryName $df -ResourceGroupName ADF

您可以使用 **Remove-AzureDataFactoryGateway** Cmdlet 移除閘道，並使用 **Set-AzureDataFactoryGateway** Cmdlet 更新閘道的說明。如需這些 Cmdlet 的語法及其他詳細資訊，請參閱 Data Factory Cmdlet 參考文件。  




## 另請參閱

文章 | 描述
------ | ---------------
[開始使用 Azure Data Factory][adf-getstarted] | 本文章提供端對端教學課程，示範如何建立範例 Azure Data Factory 將資料從 Azure Blob 複製至 Azure SQL Database。
[搭配使用 Pig 和 Hive 與 Azure Data Factory][use-pig-and-hive-with-data-factory] | 本文逐步解說如何使用 HDInsight 活動執行 hive/pig 指令碼來處理輸入的資料並產生輸出資料。 
[教學課程：使用 Data Factory 移動和處理記錄檔 ][adf-tutorial] | 本文章提供端對端逐步解說，示範如何實作類似真實的案例，使用 Azure Data Factory 將記錄檔的資料轉換成見解。
[使用 Data Factory 中的自訂活動][use-custom-activities] | 本文章提供逐步解說，包含建立自訂活動和在管線中加以使用的逐步指示。 
[疑難排解 Data Factory 問題][troubleshoot] | 本文章說明如何疑難排解 Azure Data Factory 問題。  
[Azure Data Factory 開發人員參考資料][developer-reference] | 開發人員參考資料提供 Cmdlet、JSON 指令碼、函式... 等完整的參考內容。 



[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[adf-getstarted]: ../data-factory-get-started
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: ../data-factory-get-started
[monitor-manage-powershell]: ../data-factory-monitor-manage-using-powershell





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/zh-tw/documentation/articles/install-configure-powershell/


[image-data-factory-onprem-new-everything]: ./media/data-factory-use-onpremises-datasources/OnPremNewEverything.png

[image-data-factory-onprem-datastorage-cache-backup]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageCacheBackup.png

[image-data-factory-onprem-datastorage-see-all]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageSeeAll.png

[image-data-factory-onprem-dataservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremDataServicesBlade.png

[image-data-factory-onprem-datafactory-preview-blade]: ./media/data-factory-use-onpremises-datasources/DataFactoryPreviewBlade.png

[image-data-factory-onprem-create-resource-group]: ./media/data-factory-use-onpremises-datasources/OnPremCreateResourceGroup.png

[image-data-factory-add-to-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataFactoryAddToStartboard.png

[image-data-factory-notifications-hub]: ./media/data-factory-use-onpremises-datasources/OnPremNotificationsHub.png

[image-data-factory-datafactory-homepage]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage.png

[image-data-factory-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremStartboard.png

[image-data-factory-linkedservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesBlade.png

[image-data-factory-linkedservices-add-datastore-button]: ./media/data-factory-use-onpremises-datasources/ONPremLinkedServicesAddDataStoreButton.png

[image-data-factory-new-datastore-blade]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreBlade.png

[image-data-factory-azure-storage-account]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageAccount.png

[image-data-factory-azure-storage-settings]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageSettings.png

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-with-storage]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesWithMyBlobStore.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-add-datastore-SQL-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddSQLDataStoreButton.png

[image-data-factory-gateway-configure-link]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreDataGatewayConfigureLink.png

[image-data-factory-credentials-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCredentionlsBlade.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-linkedservices-blade-onprem]: ./media/data-factory-use-onpremises-datasources/LinkedServiceBladeWithOnPremSql.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png
