<properties 
	pageTitle="啟用管線以使用內部部署資料 | Azure Data Factory" 
	description="了解如何使用 Azure Data Factory 註冊內部部署資料來源，並將資料複製到資料來源/複製資料來源中的資料。" 
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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# 啟用管線以使用內部部署資料

若要啟用 Azure Data Factory 中的管線以使用內部部署資料來源，您需要運用 Azure 管理入口網站或 Azure PowerShell 將內部部署資料來源新增為 Data Factory 的連結服務。
 
若要將內部部署資料來源新增為 Data Factory 的連結服務，您必須先在內部部署電腦上下載並安裝「Microsoft 資料管理閘道」，然後設定內部部署資料來源的連結服務使用閘道。


## <a href="DMG"></a> 資料管理閘道器

**資料管理閘道器** 是連接至雲端服務安全且管理的方式在內部部署資料來源的軟體。您可以使用「資料管理閘道」來進行下列作業：

- **連接至混合式資料存取的內部資料** – 連受益雲端服務，同時保留在內部部署資料以執行商務的雲端服務的內部資料。
- **定義資料的安全 proxy** – 您可以定義在內部部署資料來源會公開與資料管理閘道器，因此該閘道驗證來自雲端服務的資料要求，並保護的內部部署資料來源。
- **管理您的閘道器的完整管理** – 系統會提供完整的監視和管理與管理的資料管理閘道器內的所有活動的記錄。
- **有效率地將資料移** – 資料傳輸以平行方式，彈性間歇性網路問題，自動重試邏輯。


「資料管理閘道」提供全方位內部部署資料連線功能，包括下列：

- **非侵入式編譯到公司防火牆** – 資料管理閘道器只適用於在安裝之後，而不必開啟防火牆連線或需要侵入式變更為您的公司網路基礎結構。 
- **加密您的憑證認證** – 用來連接到資料來源認證會以完全由使用者所擁有的憑證加密。若沒有憑證，任何人 (包括 Microsoft 在內) 皆無法將認證解密成純文字。

### 使用資料管理閘道的考量
1.	資料管理閘道器的單一執行個體可用於多個內部資料來源，但是請記住， **閘道會繫結至 Azure 資料 factory** 並不能共用與另一個資料處理站。
2.	您可以讓 **只有一個執行個體的資料管理閘道器** 在電腦上安裝。假設您有兩個需要存取內部部署資料來源的 Data Factory，您需要將繫結至不同 Data Factory 的閘道個別安裝在兩部內部部署電腦上。
3.	 **閘道不需要是做為資料來源相同的電腦上**, ，但仍接近資料來源會減少閘道連接到資料來源的時間。我們建議您在不同的裝載在內部部署資料來源，如此閘道器不會爭奪資源與資料來源的電腦上安裝閘道器。
4.	您可以讓 **連接至相同的內部部署資料來源的不同電腦上的多個閘道**。例如，您可能必須提供兩個資料處理站的這兩個閘道，但是相同的內部部署資料來源已向的資料處理站。 
5.	如果您已經安裝在電腦服務的閘道 **Power BI** 案例中，請安裝 **不同 Azure 資料 factory 閘道** 另一部電腦上。

### 連接埠和安全性考量 
- 資料管理閘道器安裝程式會開啟 **8050** 和 **8051** 閘道器電腦上的連接埠。這些連接埠由 **認證管理員** (ClickOnce 應用程式)，可讓您設定內部連結服務的認證，並測試資料來源連接。無法透過網際網路連接這些連接埠，而且您不需要需要這些在公司防火牆中開啟。
- 複製自/至從 Azure SQL database 在內部部署 SQL Server 資料庫的資料時，請確定下列各項：
 
	- 閘道器電腦上的防火牆上允許連出的 TCP 通訊 **TCP** 連接埠 **1433年**
	- 設定 [SQL Azure 防火牆設定](https://msdn.microsoft.com/library/azure/jj553530.aspx) 新增 **的閘道器電腦的 IP 位址** 至 **允許的 IP 位址**。

- 當複製資料至/從內部部署 SQL Server 至任何目的地，而且閘道和 SQL Server 電腦不同時，執行下列動作： [設定 Windows 防火牆](https://msdn.microsoft.com/library/ms175043.aspx) 在 SQL Server 電腦，讓閘道可以存取透過 SQL Server 執行個體所接聽的連接埠的資料庫。預設執行個體中，它是連接埠 1433年。

- 您必須啟動 **認證管理員** 應用程式能夠連接到資料管理閘道器，可以設定資料來源的認證，並測試資料來源連接的電腦上。

### 閘道安裝的必要條件 

1.	支援 **作業系統** 版本是 Windows 7、 Windows 8 8.1、 Windows Server 2008 R2、 Windows Server 2012。
2.	建議 **組態** 閘道的機器是至少 2 GHz，4 核心，8 GB RAM 和 80 GB 的磁碟。
3.	如果主機電腦休眠狀態時，閘道無法回應資料要求。因此，設定適當 **電源計劃** 之前安裝閘道器電腦上。如果電腦已設定為休眠，閘道安裝就會提示訊息。  


 

## 逐步介紹

在本逐步解說中，您可以使用將資料從內部部署 SQL Server 資料庫移至 Azure Blob 的管線，來建立一個 Data Factory。

## 步驟 1： 建立 Azure 的資料處理站
在此步驟中，您可以使用 Azure 管理入口網站建立 Azure 資料 Factory 執行個體名為 **ADFTutorialOnPremDF**。您也可以使用 Azure Data Factory Cmdlet 來建立 Data Factory。

1.	登入之後 [Azure 預覽入口網站][azure-preview-portal], ，按一下 **新增** 從左下角中，選取 **資料分析** 中 **建立** blade 中，然後按一下 [ **資料 Factory** 上 **資料分析** 分頁。

	![新-> DataFactory][image-data-factory-new-datafactory-menu]
  
6. 在 **新的資料處理站** 分頁：
	1. 輸入 **ADFTutorialOnPremDF** 的 **名稱**。
	2. 按一下 **資源群組名稱** ，然後選取 **ADFTutorialResourceGroup** (如果您已經從教學課程 [開始使用 Azure 資料 Factory][adfgetstarted]。您可以選取現有的資源群組，或建立一個新的群組。若要建立新的資源群組：
		1. 按一下 **建立新的資源群組**。
		2. 在 **建立資源群組分頁**, ，輸入 **名稱** 資源群組，然後按一下 **確定**。

7. 請注意， **新增到儀表板** 會在檢查 **新的資料處理站** 分頁。

	![新增至儀表板][image-data-factory-add-to-startboard]

8. 在 **新的資料處理站** blade 中，按一下 **建立**。

	> [AZURE.NOTE]**資料處理站名稱"ADFTutorialOnPremDF"不是使用**  
9. 查詢通知中的建立程序從 **通知** 左邊 」 中樞。按一下 **X** 關閉 **NOTIFCATIONS** 分頁已開啟。

	![通知中樞][image-data-factory-notifications-hub]

11. 建立已完成之後，您會看到 **資料 Factory** 分頁，如下所示：

	![資料處理站首頁][image-data-factory-datafactory-homepage]

## 步驟 2： 建立資料管理閘道器
5.	在 **資料 Factory** 刀鋒伺服器 **ADFTutorialOnPremDF**, ，按一下 **連結服務**。 

	![資料處理站首頁][image-data-factory-home-age]

2.	在 **連結服務** blade 中，按一下 **+ 資料閘道器**。

	![連結的服務-新增閘道 按鈕][image-data-factory-linkedservices-add-gateway-button]

2. 在 **建立** blade 中，輸入 **adftutorialgateway** 的 **名稱**, ，按一下 **確定**。

	![建立閘道分頁][image-data-factory-create-gateway-blade]

3. 在 [**設定**] 分頁中，按一下 [**直接安裝在此電腦上**]。這會下載閘道的安裝套件、在電腦上安裝、設定和註冊閘道。

	> [AzURE.NOTE]請使用 Internet Explorer 或 Microsoft ClickOnce 相容的網頁瀏覽器。

	![閘道-設定分頁][image-data-factory-gateway-configure-blade]

	這是最簡單的方式 (一鍵)，透過單一步驟即可下載、安裝、設定和註冊閘道。您可以看到 **Microsoft 資料管理閘道組態管理員** 應用程式安裝在電腦上。您也可以找到可執行檔 **ConfigManager.exe** 資料夾中： **C:\Program Files\Microsoft 資料管理 Gateway\1.0\Shared**。

	您可以也下載和使用此索引標籤中的連結手動安裝閘道並註冊它使用索引鍵中所示 **註冊金鑰** 文字方塊。
	
	請參閱 [資料管理閘道器](#DMG) 如需詳細資訊的閘道，包括最佳作法和重要的考量 ＞ 一節。

	>[AZURE.NOTE]您必須是系統管理員在本機電腦上的安裝和設定資料管理閘道器成功。您可以將其他使用者加入至資料管理閘道使用者的本機 Windows 群組。此群組的成員可以使用資料管理閘道組態管理員工具來設定閘道器。

4. 按一下 **通知** 左邊 」 中樞。等候，直到您看到 **Express 安裝程式成功 'adftutorialgateway'** 訊息中 **通知** 分頁。

	![Express 安裝程式成功][express-setup-succeeded]
5. 按一下 **確定** 上 **建立** blade 然後在 **新資料閘道器** 分頁。
6. 關閉 **連結的服務** 分頁 (按 **X** 右上角的按鈕) 並重新開啟 **連結服務** 分頁以查看最新的閘道器狀態。 
7. 確認已選取 **狀態** 的閘道是 **線上**。 

	![閘道器狀態][gateway-status]
5. 啟動 **Microsoft 資料管理閘道組態管理員** 應用程式在電腦上。

	![閘道器組態管理員][image-data-factory-gateway-configuration-manager]

6. 請稍候，待這些值設定完成，如下所示：
	1. 如果服務 **狀態** 未設定為 **Started**, ，按一下 **啟動服務** 來啟動服務，並等候一分鐘重新整理的其他欄位。
	2. **閘道器名稱** 設為 **adftutorialgateway**。
	3. **執行個體名稱** 設為 **adftutorialgateway**。
	4. **閘道器金鑰狀態** 設為 **註冊**。
	5. 在狀態列底部顯示 **連接到資料管理閘道器雲端服務** 連同 **綠色核取記號**。
	
7. 在 **連結的服務** blade 中，確認已選取 **狀態** 的閘道是 **良好**。
8. 關閉所有刀鋒，直到到達 **資料 Factory** 首頁。 

## 步驟 2： 建立連結的服務 
在此步驟中，您將建立兩個連結的服務： **StorageLinkedService** 和 **SqlServerLinkedService**。 **SqlServerLinkedService** 連結在內部部署 SQL Server 資料庫和 **StorageLinkedService** 連結的服務連結至 Azure blob 存放區 **ADFTutorialDataFactory**。稍後在本逐步解說中，您將建立可將內部部署 SQL Server 資料庫的資料複製到 Azure Blob 存放區的管線。

### 在內部部署 SQL Server 資料庫中新增連結服務
1.	在 **資料 FACTORY** 分頁、 clcik **作者和部署** 磚來啟動 **編輯器** 資料處理站。

	![[製作和部署] 磚][image-author-deploy-tile]

	> [AZURE.NOTE][資料處理站編輯器][data-factory-editor]
2.	在 **編輯器**, ，按一下 **新的資料存放區** 按鈕工具列，然後選取 **在內部部署 SQL server 資料庫** 從下拉式功能表。 

	![編輯器新增資料存放區] 按鈕][image-editor-newdatastore-onpremsql-button]
    
3.	您應該會看到在右窗格中建立的內部連結的 SQL Server 服務的 JSON 範本。 ![在內部 SQL 連結服務-設定][image-editor-newdatastore-onpremsql-settings]

4.	依下列方式在 JSON] 窗格中：
	1.	針對 **gatewayName** 屬性中，輸入 **adftutorialgateway** 取代置於雙引號內的所有文字。  
	2.	如果您使用 **SQL 驗證**： 
		1.	針對 **connectionString** 屬性，取代 **< 伺服器名稱 >**, ，**< 資料庫 >**, ，**< 使用者名稱 >**, ，和 **< 密碼 >** 名稱在內部部署 SQL server、 資料庫、 使用者帳戶和密碼。	
		2.	移除最後兩個屬性 (* * 使用者名稱 * * 和 **密碼**) 從 JSON 檔案並移除 **逗號 (，)** 從剩餘的 JSON 指令碼的最後一行的結尾字元。
		
				{
	    			"name": "SqlServerLinkedService",
	    			"properties": {
		        		"type": "OnPremisesSqlLinkedService",
		        		"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
		        		"gatewayName": "adftutorialgateway"
	    			}
				}
	3.	如果您使用 **Windows 驗證**：
		1. 針對 **connectionString** 屬性，取代 **< 伺服器名稱 >** 和 **< 資料庫 >** 與您在內部部署 SQL server 和資料庫的名稱。設定 **整合式安全性** 至 **True**。移除 **識別碼** 和 **密碼** 從連接字串。
			
				{
    				"name": "SqlServerLinkedService",
    				"properties": {
        				"type": "OnPremisesSqlLinkedService",
        				"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
		   				"gatewayName": "adftutorialgateway",
				        "username": "<Specify user name if you are using Windows Authentication>",
				        "password": "<Specify password for the user account>"
    				}
				}		
		
6. 按一下 **部署** 部署 SqlServerLinkedService] 工具列上。確認您看到訊息 **連結服務建立成功** 標題列上。您也應該會看到 **SqlServerLinkedService** 左邊的樹狀檢視中。
		   
	![SqlServerLinkedService 部署成功][image-editor-sql-linked-service-successful]
	
  
> [AZURE.NOTE]您也可以建立連結的 SQL Server 服務]，即可 **新的資料存放區** 上的工具列按鈕 **連結服務** 分頁。如果您進入此路由時，您會使用認證管理員 ClickOnce 應用程式存取入口網站的電腦上執行的設定資料來源的認證。如果您從不同於閘道器電腦的電腦存取入口網站，您必須確定認證管理員應用程式可以連接到閘道器電腦。如果應用程式無法連接閘道器電腦，它將不允許您設定資料來源的認證，並測試資料來源連接。

#### 新增 Azure 儲存體帳戶的連結服務
 
1. 在 **編輯器**, ，按一下 **新的資料存放區** 按鈕工具列，然後選取 **Azure 儲存體** 從下拉式功能表。您應該會看到在右窗格中建立連結的 Azure 儲存體服務的 JSON 範本。 

	![編輯器新增資料存放區] 按鈕][image-editor-newdatastore-button]
    
6. 取代 **< accountname >** 和 **< accountkey >** 帳戶名稱與 Azure 儲存體帳戶的帳戶金鑰值。

	![Blob 儲存體 JSON 編輯器][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971) 如需詳細資訊 JSON 內容。

6. 按一下 **部署** 部署 StorageLinkedService] 工具列上。確認您看到訊息 **連結服務建立成功** 標題列上。

	![部署編輯器 Blob 儲存體][image-editor-blob-storage-deploy]

 
## 步驟 3： 建立輸入和輸出資料集
在此步驟中，您會建立代表複製作業的輸入和輸出資料的輸入和輸出資料集 (內部部署 SQL Server 資料庫 => Azure Blob 儲存體)。在建立資料集或資料表 (矩形資料集) 之前，您必須執行下列動作 (清單後列有詳細步驟)：

- 建立了名為 **emp** 在 SQL Server 資料庫您加入做為連結的服務資料處理站和插入的幾個範例項目插入資料表。
- - 如果您尚未經歷從教學課程 [開始使用 Azure 資料 Factory][adfgetstarted] 文件中，建立名為 blob 容器 **adftutorial** 在 Azure blob 儲存體帳戶加入為連結的服務資料的處理站。

### 準備用於教學課程的內部部署 SQL Server

1. 您所指定的內部資料庫中的 SQL Server 連結服務 (* * SqlServerLinkedService * *)，使用下列 SQL 指令碼來建立 **emp** 資料庫資料表中的。


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

1.	在 **資料 Factory 編輯器**, ，按一下 **新的資料集** 命令列，然後按一下 [ **在內部部署 SQL**。 
2.	在右窗格中的 JSON 取代成下列文字：    

        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "SqlServerLinkedService"
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
	
	- 位置 **類型** 設為 **OnPremisesSqlServerTableLocation**。
	- **tableName** 設為 **emp**。
	- **linkedServiceName** 設為 **SqlServerLinkedService** (您必須在步驟 2 中建立此連結的服務)。
	- 針對不由 Azure 資料 Factory 中的另一個管線產生輸入資料表，您必須指定 **waitOnExternal** JSON 中的區段。它代表輸入資料產生於 Azure Data Factory 服務外部。   

	請參閱 [JSON 指令碼參考][json-script-reference] 如需詳細資訊 JSON 內容。

2. 按一下 **部署** 命令列來部署資料集 (資料表是矩形的資料集)。確認您看到的訊息，指出標題列上 **資料表成功部署**。


### 建立輸出資料表

1.	在 **資料 Factory 編輯器**, ，按一下 **新的資料集** 命令列，然後按一下 [ **Azure Blob 儲存體**。
2.	在右窗格中的 JSON 取代成下列文字： 

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
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	請注意：
	
	- 位置 **類型** 設為 **AzureBlobLocation**。
	- **linkedServiceName** 設為 **StorageLinkedService** (您必須在步驟 2 中建立此連結的服務)。
	- **folderPath** 設為 **adftutorial/outfromonpremdf** outfromonpremdf 所在 adftutorial 容器中的資料夾。您只需要建立 **adftutorial** 容器。
	-  **可用性** 設為 **每小時** (* * 頻率 * * 設 **小時** 和 **間隔** 設 **1**)。資料處理站服務將會產生輸出的資料配量中的每個小時 **emp** Azure SQL Database 中的資料表。 

	如果您沒有指定 **fileName** 的 **輸入的資料表**, ，所有檔案/blob 從輸入資料夾 (* * folderPath * *) 會被視為輸入。如果您在 JSON 中指定 fileName，則只有指定的檔案/Blob 會被視為輸入。請參閱範例檔案 [教學課程][adf-tutorial] 範例。
 
	如果您未指定**輸出資料表**的 **fileName**，**folderPath** 中產生的檔案會依照下列格式命名：<Guid>.txt (例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.)。

	若要設定 **folderPath** 和 **fileName** 動態地根據 **SliceStart** 時間，請使用 partitionedBy 屬性。在下列範例中，folderPath 使用 SliceStart (處理配量的開始時間) 中的年、月和日，fileName 使用 SliceStart 中的小時。例如，如果配量產生於 2014-10-20T08:00:00，folderName 設定為 wikidatagateway/wikisampledataout/2014/10/20，而 fileName 設定為 08.csv。

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	請參閱 [JSON 指令碼參考][json-script-reference] 如需詳細資訊 JSON 內容。

2.	按一下 **部署** 命令列來部署資料集 (資料表是矩形的資料集)。確認您看到的訊息，指出標題列上 **資料表成功部署**。
  

## 步驟 4： 建立和執行管線
在此步驟中，您會建立 **管線** 以內 **複製活動** 使用 **EmpOnPremSQLTable** 做為輸入和 **OutputBlobTable** 做為輸出。

1.	按一下 **新管線** 命令列上。如果看不到該按鈕，按一下 **...(省略符號)** 若要展開的命令列。
2.	在右窗格中的 JSON 取代成下列文字：   


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
	        	],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
			}
		}

	請注意：
 
	- 在 [活動] 區段中，則只有活動其 **類型** 設為 **CopyActivity**。
	- **輸入** 活動設定為 **EmpOnPremSQLTable** 和 **輸出** 活動設定為 **OutputBlobTable**。
	- 在 **轉換** ] 區段中， **[sqlsource]** 指定為 **來源類型** 和 **BlobSink **指定為 **sink 類型**。 - SQL 查詢 **選取 * 從 emp** 指定 **sqlReaderQuery** 屬性 **[sqlsource]**。

	> [AZURE.NOTE]取代的值 **開始** 屬性與目前的日期和 **結束** 隔天的值。同時開始和結束日期時間必須在 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如： 2014年-10-14T16:32:41Z。 **結束** 時間是選擇性的但是我們將在本教學課程中使用它。如果您沒有指定值 **結束** 屬性，它會計算為"* * 啟動 + 48 小時 * *"。若要無限期地執行管線，請指定 **9/9/9999** 做為值 **結束** 屬性。您要定義持續時間處理資料配量會根據 **可用性** 每個 Azure 資料 Factory 資料表所定義的屬性。在上述範例中，由於每小時即產生一個資料配量，共會有 24 個資料配量。
	
2. 按一下 **部署** 命令列來部署資料集 (資料表是矩形的資料集)。確認您看到的訊息，指出標題列上 **管線成功部署**。
5. 現在，關閉 **編輯器** 按一下刀鋒 **X**。按一下 **X** ] 以關閉工具列] 和 [樹狀結構檢視與 [ADFTutorialDataFactory 分頁。如果您看到 **將捨棄未儲存的編輯** 訊息中，按一下 **確定**。
6. 您應該要回到 **資料 FACTORY** 刀鋒伺服器 **ADFTutorialOnPremDF**。

**恭喜！** 您已成功建立 Azure Data Factory、連結服務、資料表和管線，以及排定的管線。

### 在圖表檢視中檢視資料 factory 
1. 在 **Azure 預覽入口網站**, ，按一下 **圖表** 在首頁上的磚 **ADFTutorialOnPremDF** 資料處理站。：

	![圖表連結][image-data-factory-diagram-link]

2. 您應該會看到如下所示的圖表：

	![圖表檢視][image-data-factory-diagram-view]

	您可以放大、 縮小、 放大到 100%，縮放以符合、 自動定位管線和資料表，以及顯示歷程資訊 (反白顯示選取之項目的上游和下游的項目)。您可以在 (輸入/輸出資料表或管線)，若要查看其屬性的物件上的雙精確度浮點數 blick。

## 步驟 5： 監視資料集和管線
在此步驟中，您將使用 Azure 入口網站來監視 Azure Data Factory 的運作情形。您也可以使用 PowerShell Cmdlet 來監視資料集和管線。如需有關使用適用於監視 cmdlet 的詳細資訊，請參閱 [監視和管理 Azure 資料處理站使用 PowerShell][monitor-manage-powershell]。

1. 瀏覽至 **Azure 預覽入口網站** (如果您關閉它)
2. 如果針對分頁 **ADFTutorialOnPremDF** 尚未開啟，即可開啟 **ADFTutorialOnPremDF** 上 **儀表板**。
3. 您應該會看到 **計數** 和 **名稱** 的資料表和您在此索引標籤建立的管線。

	![資料處理站首頁][image-data-factory-homepage-2]
4. 現在，按一下 [ **資料集** 並排顯示。
5. 在 **資料集** blade 中，按一下 **EmpOnPremSQLTable**。

	![EmpOnPremSQLTable 配量][image-data-factory-onprem-sqltable-slices]

6. 請注意，資料配量，截至目前時間為止已產生，而且它們是 **準備**。這是因為您已將資料插入 SQL Server 資料庫中，而資料一直留存於其中。確認任何配量顯示在 **問題配量** 底部區段。


	同時 **最近更新配量** 和 **最近無法配量** 清單會依照 **上次更新時間**。配量的更新時間是在下列情況中變更。
    

	-  您扇區的狀態手動更新，例如，使用 **組 AzureDataFactorySliceStatus** (或) 依序按一下 **執行** 上 **配量** 配量的分頁。
	-  配量因為執行的狀態變更 (例如啟動測試回合、 結束，而且無法執行、 測試回合結束，而且已成功，等等)。
 
	按一下標題的清單或 **...(橢圓形)** 若要查看較大配量的清單。按一下 **篩選** 篩選配量] 工具列上。
	
	若要檢視的資料配量，而是依照配量的開始/結束時間，請按一下 **(藉由配量的時間) 的資料配量** 並排顯示。

7. 現在，在 **資料集** blade 中，按一下 **OutputBlobTable**。

	![OputputBlobTable 配量][image-data-factory-output-blobtable-slices]
8. 確認所產生的目前時間配量和 **準備**。到目前的時間配量的狀態會設為等待 **準備**。
9. 按一下從清單中的任何資料配量上，您應該會看到 **資料配量** 分頁。

	![資料配量分頁][image-data-factory-dataslice-blade]

	如果配量不在 **準備** 狀態，您可以看到，未就緒並封鎖目前的配量，從執行中的上游配量 **上游未就緒的配量** 清單。

10. 按一下 **活動執行** 從清單中，若要查看底部 **活動執行詳細資料**。

	![活動執行詳細資料] 分頁][image-data-factory-activity-run-details]

11. 按一下 **X** ，直到您回到首頁刀鋒伺服器關閉所有刀 **ADFTutorialOnPremDF**。
14. (選擇性)按一下 **管線**, ，按一下 **ADFTutorialOnPremDF**, ，並鑽研輸入資料表 (* * 已使用 * *) 或輸出資料表 (* * Produced * *)。
15. 使用工具，例如 **Azure 儲存體總管** 來驗證輸出。

	![Azure 儲存體總管][image-data-factory-stroage-explorer]


## 建立和註冊使用 Azure PowerShell 的閘道 
本節說明如何使用 Azure PowerShell Cmdlet 建立和註冊閘道。

1. 啟動 **Azure PowerShell** 系統管理員模式。 
2. 中可用的 Azure 資料 Factory cmdlet **AzureResourceManager** 模式。執行下列命令以切換到 **AzureResourceManager** 模式。     

        switch-azuremode AzureResourceManager


2. 使用 **新增 AzureDataFactoryGateway** 指令程式可建立的邏輯閘道，如下所示：

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**範例命令，並輸出**：


		PS C:> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded


3. 使用 **新增 AzureDataFactoryGatewayKey** 指令程式來產生新建立的閘道，登錄機碼，並將金鑰存放在本機變數 **$Key**：

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**範例命令的輸出：**


		PS C:> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. 在 Azure PowerShell 中，切換到資料夾： **C:\Program Files\Microsoft 資料管理 Gateway\1.0\PowerShellScript** 和執行 **RegisterGateway.ps1** 與本機變數相關聯的指令碼 **$Key** 您稍早建立下列命令以註冊與邏輯閘道在電腦上安裝用戶端代理程式中所示。

		PS C:> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. 您可以使用 **Get AzureDataFactoryGateway** 指令程式可取得的閘道清單中您資料的處理站。當 **狀態** 顯示 **線上**, ，這表示您的閘道器已準備好使用。

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

您可以移除閘道使用 **移除 AzureDataFactoryGateway** 閘道使用的指令程式並更新描述 **組 AzureDataFactoryGateway** 指令程式。如需這些 Cmdlet 的語法及其他詳細資訊，請參閱 Data Factory Cmdlet 參考文件。



[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-getstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-editor]: data-factory-editor.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: data-factory-get-started.md
[monitor-manage-powershell]: data-factory-monitor-manage-using-powershell.md





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[image-author-deploy-tile]: ./media/data-factory-use-onpremises-datasources/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-use-onpremises-datasources/editor-blob-storage-settings.png
[image-editor-blob-storage-deploy]: ./media/data-factory-use-onpremises-datasources/editor-deploy-blob-linked-service.png
[image-editor-newdatastore-onpremsql-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-onpremsql-button.png
[image-editor-newdatastore-onpremsql-settings]: ./media/data-factory-use-onpremises-datasources/editor-onprem-sql-settings.png
[image-editor-sql-linked-service-successful]: ./media/data-factory-use-onpremises-datasources/editor-sql-linked-service-successful.png

[gateway-status]: ./media/data-factory-use-onpremises-datasources/gateway-status.png
[express-setup-succeeded]: ./media/data-factory-use-onpremises-datasources/express-setup-succeeded.png
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

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

<!---HONumber=GIT-SubDir-->