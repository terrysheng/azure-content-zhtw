<properties
	pageTitle="如何使用適用於 Mobile Apps 的 Node.js 後端伺服器 SDK | Azure App Service"
	description="了解如何使用適用於 Azure App Service Mobile Apps 的 Node.js 後端伺服器 SDK。"
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="node"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="adrianhall"/>

# 如何使用 Azure Mobile Apps Node.js SDK

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

本文提供詳細的資訊及範例，說明如何在 Azure App Service Mobile Apps 中使用 Node.js 後端。

## <a name="Introduction"></a>簡介

Azure App Service Mobile Apps 可讓您將行動最佳化資料存取 Web API 新增至 Web 應用程式。提供的 Azure App Service Mobile Apps SDK 適用於 ASP.NET 和 Node.js Web 應用程式。此 SDK 提供下列作業：

- 資料存取的資料表作業 (讀取、插入、更新、刪除)
- 自訂 API 作業

這兩種作業都可用於 Azure App Service 所允許的所有身分識別提供者 (包括 Facebook、Twitter、Google 和 Microsoft 等社交身分識別提供者，以及用於企業身分識別的 Azure Active Directory) 之間的驗證。

您可以在 [GitHub 上的範例目錄]中找到每個使用案例的範例。

### <a name="howto-cmdline-basicapp"></a>作法：使用命令列建立基本 Node.js 後端

每個 Azure App Service Mobile App Node.js 後端都會以 ExpressJS 應用程式的形式啟動。在適用於 Node.js 的 Web 服務架構中，ExpressJS 最廣為使用。您可以依照下列方式建立基本的 [Express] 應用程式：

1. 在命令或 PowerShell 視窗中，為您的專案建立新的目錄。

        mkdir basicapp

2. 執行 npm init 以初始化封裝結構。

        cd basicapp
        npm init

    Npm init 命令會詢問一組問題以初始化專案。請參閱以下的範例輸出

    ![npm init 輸出][0]

3. 從 npm 儲存機制安裝 express 和 azure-mobile-apps 資源庫。

        npm install --save express azure-mobile-apps

4. 建立 app.js 檔案以實作基本行動伺服器。

		var express = require('express'),
			azureMobileApps = require('azure-mobile-apps');

		var app = express(),
			mobile = azureMobileApps();

		// Define a TodoItem table
		mobile.tables.add('TodoItem');

		// Add the mobile API so it is accessible as a Web API
		app.use(mobile);

		// Start listening on HTTP
		app.listen(process.env.PORT || 3000);

此應用程式會建立簡單而具有單一端點 (`/tables/TodoItem`) 的行動最佳化 WebAPI，讓使用者可使用動態結構描述存取基礎 SQL 資料存放區，不需經過驗證。它適用於下列用戶端程式庫快速入門：

- [Android 用戶端快速入門]
- [Apache Cordova 用戶端快速入門]
- [iOS 用戶端快速入門]
- [Windows 市集用戶端快速入門]
- [Xamarin.iOS 用戶端快速入門]
- [Xamarin.Android 用戶端快速入門]
- [Xamarin.Forms 用戶端快速入門]


您可以在 [GitHub 上的 basicapp 範例]中找到此基本應用程式的程式碼。

### <a name="howto-vs2015-basicapp"></a>作法：使用 Visual Studio 2015 建立 Node 後端

Visual Studio 2015 需要延伸模組才能在整合式開發環境 (IDE) 內開發 Node.js 應用程式。首先，請下載並安裝 [Node.js Tools 1.1 for Visual Studio]。安裝 Node.js Tools for Visual Studio 後，請建立 Express 4.x 應用程式：

1. 開啟 [新增專案] 對話方塊 (從 [檔案] > [新增] > [專案...])。

2. 展開 [範本] > [JavaScript] > [Node.js]。

3. 選取 [基本 Azure Node.js Express 4 應用程式]。

4. 填入專案名稱。按一下 [確定]。

	![Visual Studio 2015 新專案][1]

5. 以滑鼠右鍵按一下 **npm** 節點，然後選取 [安裝新的 npm 封裝...]。

6. 建立第一個 Node.js 應用程式時，您可能必須重新整理 npm 目錄。如有必要，系統將提示您 - 按一下 [**重新整理**]。

7. 在搜尋方塊中輸入 _azure-mobile-apps_。按一下 **azure-mobile-apps 2.0.0** 封裝，然後按一下 [安裝封裝]。

	![安裝新的 npm 封裝][2]

8. 按一下 [關閉]。

9. 開啟 _app.js_ 檔案，以新增 Azure Mobile Apps SDK 的支援。在程式庫 require 陳述式底部的第 6 行，加入下列程式碼：

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    在其他 app.use 陳述式之後大約第 27 行，加入下列程式碼：

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

    儲存檔案。

10. 在本機執行應用程式 (API 將在 http://localhost:3000 上提供)，或發佈至 Azure。

### <a name="download-quickstart"></a>作法：使用 Git 下載 Node.js 後端快速入門程式碼專案

當您使用入口網站的 [快速入門] 刀鋒視窗建立新的 Node.js 行動應用程式後端時，系統會為您建立新的 Node.js 專案並將其部署至您的網站。您可以在入口網站中新增資料表和 API，並編輯 Node.js 後端的程式碼檔案。您也可以使用各種部署工具的其中一個來下載後端專案，以便新增或修改資料表和 API，然後重新發佈專案。如需詳細資訊，請參閱 [Azure App Service 部署指南]。下列程序使用 Git 儲存機制來下載快速入門專案程式碼。

1. 如果尚未安裝 Git，請先安裝。安裝 Git 所需的步驟會因作業系統而有所不同。如需作業系統特定的發佈和安裝指引，請參閱[安裝 Git](http://git-scm.com/book/en/Getting-Started-Installing-Git)。

2. 請依照[啟用 Web 應用程式儲存機制](../app-service-web/web-sites-publish-source-control.md#Step4)中的步驟，為您的後端網站啟用 Git 儲存機制，並記下部署使用者名稱和密碼。

3. 在您的行動應用程式後端的刀鋒視窗中，記下 [Git 複製 URL] 設定。

4.  在 Git 感知命令列工具中使用 Git 複製 URL 執行 `git clone` 命令，並在需要時輸入您的密碼，如下列範例所示：

		$ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. 瀏覽至本機目錄 (在上述範例中為 /todolist)，並注意專案檔案已下載。在 /tables 子資料夾中，您會發現 todoitem.json 檔案 (其定義資料表的權限) 和 todoitem.js 檔案 (其定義資料表的 CRUD 作業指令碼)。

6. 在您變更專案檔案之後，請執行下列命令來新增、認可變更，然後將變更上傳至網站：

		$ git commit -m "updated the table script"
		$ git push origin master

	當您將新檔案加入至專案時，您必須先執行 `git add .` 命令。

每次有一組新的認可推送至網站時，就會重新發佈網站。

### <a name="howto-publish-to-azure"></a>作法：將您的 Node.js 後端發佈至 Azure

Microsoft Azure 提供許多將 Azure App Service Mobile Apps Node.js 後端發佈至 Azure 服務的機制。其中包括使用整合至 Visual Studio 中的部署工具、命令列工具，和以原始檔控制為基礎的連續部署選項。如需有關本主題的詳細資訊，請參閱 [Azure App Service 部署指南]。

Azure App Service 提供 Node.js 應用程式方面的具體建議，您應該在部署之前先檢閱：

- 如何[指定 Node 版本]
- 如何[使用 Node 模組]

### <a name="howto-enable-homepage"></a>做法：啟用應用程式的首頁

許多應用程式是 Web 和行動應用程式的組合，ExpressJS 架構可讓您結合兩方面。但是有時候，您可能只想要實作行動介面。它對於提供登陸頁面以確保 App Service 已啟動並執行很有用。您可以提供您自己的首頁，或啟用暫時的首頁。若要啟用暫時的首頁，將行動應用程式建構函式調整為以下內容：

    var mobile = azureMobileApps({ homePage: true });

如果您想要讓此選項僅在本機開發時可供使用，您可以將此設定新增至您的 `azureMobile.js` 檔案。

## <a name="TableOperations"></a>資料表作業

azure-mobile-apps Node.js Server SDK 提供將儲存在 Azure SQL Database 中的資料表公開為 WebAPI 的機制。提供的作業有五種。

| 作業 | 說明 |
| --------- | ----------- |
| GET /tables/\_tablename\_ | 取得資料表中的所有記錄 |
| GET /tables/\_tablename\_/:id | 取得資料表中的特定記錄 |
| POST /tables/\_tablename\_ | 在資料表中建立新記錄 |
| PATCH /tables/\_tablename\_/:id | 更新資料表中的現有記錄 |
| DELETE /tables/\_tablename\_/:id | 刪除資料表中的記錄 |

此 WebAPI 支援 [OData]，而且擴充資料表結構描述以支援[離線資料同步]。

### <a name="howto-dynamicschema"></a>作法：使用動態結構描述定義資料表

資料表必須先經過定義才能使用。資料表可用靜態結構描述來定義 (開發人員在結構描述中定義資料行)，或以動態方式定義 (SDK 會根據傳入的要求控制結構描述)。此外，開發人員可將 Javascript 程式碼新增至定義，以控制 WebAPI 的特定層面。

根據最佳作法，您應在資料表目錄中的 Javascript 檔案內定義每個資料表，然後使用 tables.import() 方法匯入資料表。擴充基本應用程式後，會調整 app.js 檔案：

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
	    mobile = azureMobileApps();

    // Define the database schema that is exposed
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

在 ./tables/TodoItem.js 中定義資料表：

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here

    module.exports = table;

資料表依預設會使用動態結構描述。若要全域關閉動態結構描述，請在 Azure 入口網站中將應用程式設定 **MS\_DynamicSchema** 設為 false。

您可以在 [GitHub 上的待辦事項範例]中找到完整範例。

### <a name="howto-staticschema"></a>作法：使用靜態結構描述定義資料表

您可以將資料行明確定義為要透過 WebAPI 公開。azure-mobile-apps Node.js SDK 會自動將離線資料同步所需的任何其他資料行新增至您所提供的清單。例如，快速入門用戶端應用程式需要具有兩個資料行的資料表：文字 (字串) 和完整 (布林值)。這可以定義在資料表定義 JavaScript 檔案中 (位於資料表目錄中)，如下所示：

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    module.exports = table;

如果您以靜態方式定義資料表，則您還必須呼叫 tables.initialize() 方法，以在啟動時建立資料庫結構描述。tables.initialize() 方法會傳回 [Promise]，用以確保 Web 服務不會在資料庫初始化之前處理要求。

### <a name="howto-sqlexpress-setup"></a>作法：以 SQL Express 作為本機電腦上的開發資料存放區

Azure Mobile Apps AzureMobile Apps Node SDK 提供三種現成可用的資料提供選項：SDK 提供三種現成可用的資料提供選項：

- 使用**記憶體**驅動程式，可提供非持續性的範例存放區
- 使用 **mssql** 驅動程式，可提供可供開發使用的 SQL Express 資料存放區
- 使用 **mssql** 驅動程式，可提供可供生產使用的 Azure SQL Database 資料存放區

Azure Mobile Apps Node.js SDK 會使用 [mssql Node.js 封裝]來建立及使用 SQL Express 和 SQL Database 的連線。要使用此封裝，您必須在 SQL Express 執行個體上啟用 TCP 連線。

> [AZURE.TIP] 記憶體驅動程式未提供完整的測試工具集。如果您想要在本機上測試後端，建議您使用 SQL Express 資料存放區和 mssql 驅動程式。

1. 下載並安裝 [Microsoft SQL Server 2014 Express]。請確實安裝 SQL Server 2014 Express with Tools 版。除非您明確需要 64 位元支援，32 位元版本在執行時會耗用較少的記憶體。

2. 執行 SQL Server 2014 組態管理員。

  1. 在左側的樹狀結構功能表中，展開 [SQL Server 網路組態] 節點。
  2. 按一下 [SQLEXPRESS 的通訊協定]。
  3. 以滑鼠右鍵按一下 [TCP/IP]，然後選取 [啟用]。在快顯對話方塊中按一下 [確定]。
  4. 以滑鼠右鍵按一下 [TCP/IP]，然後選取 [內容]。
  5. 按一下 [IP 位址] 索引標籤。
  6. 尋找 **IPAll** 節點。在 [TCP 連接埠] 欄位中，輸入 **1433**。

	 	 ![Configure SQL Express for TCP/IP][3]
  7. 按一下 [確定]。在快顯對話方塊中按一下 [確定]。
  8. 在左側的樹狀結構功能表中，按一下 [SQL Server 服務]。
  9. 以滑鼠右鍵按一下 [SQL Server (SQLEXPRESS)]，然後選取 [重新啟動]。
  10. 關閉 SQL Server 2014 組態管理員。

3. 執行 SQL Server 2014 Management Studio 並連接到您的本機 SQL Express 執行個體

  1. 以滑鼠右鍵按一下您在 [物件總管] 中的執行個體，然後選取 [屬性]。
  2. 選取 [安全性] 頁面。
  3. 確定已選取 [SQL Server 及 Windows 驗證模式]。
  4. 按一下 [確定]。

  		![Configure SQL Express Authentication][4]

  5. 在 [物件總管] 中展開 [安全性] > [登入]。
  6. 以滑鼠右鍵按一下 [登入]，然後選取 [新增登入...]。
  7. 輸入登入名稱。選取 [SQL Server 驗證]。輸入密碼，然後在 [確認密碼] 中輸入相同的密碼。請注意，密碼必須符合 Windows 複雜性需求。
  8. 按一下 [確定]。

  		![Add a new user to SQL Express][5]

  9. 以滑鼠右鍵按一下新的登入，然後選取 [屬性]。
  10. 選取 [伺服器角色] 頁面。
  11. 核取 **dbcreator** 伺服器角色旁的方塊。
  12. 按一下 [確定]。
  13. 關閉 SQL Server 2015 Management Studio

請確實記下您選取的使用者名稱和密碼。您可能需要根據您特定的資料庫需求指派其他伺服器角色或權限。

Node.js 應用程式會讀取 **SQLCONNSTR\_MS\_TableConnectionString** 環境變數，以讀取此資料庫的連接字串。您可以將它設定在您的環境中。例如，您可以使用 PowerShell 來設定此環境變數：

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

請注意，您必須透過 TCP/IP 連線存取資料庫，並提供連線的使用者名稱和密碼。

### <a name="howto-config-localdev"></a>作法：設定專案以在本機上進行開發

Azure Mobile Apps 會從本機檔案系統讀取名為 _azureMobile.js_ 的 JavaScript 檔案。您不應使用此檔案在生產環境中設定 Azure Mobile Apps SDK，請改用 [Azure 入口網站]中的 [應用程式設定]。_azureMobile.js_ 檔案應會匯出組態物件。最常見的設定如下：

- 資料庫設定
- 診斷記錄設定
- 替代 CORS 設定

以下是實作上述資料庫設定的 _azureMobile.js_ 檔案範例：

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

建議您將 _azureMobile.js_ 新增至您的 _.gitignore_ 檔案 (或其他原始程式碼控制忽略檔案)，以防止密碼儲存在雲端中。請一律在 [Azure 入口網站]內的 [應用程式設定] 中設定生產設定。

### <a name="howto-appsettings"></a>設定行動應用程式的應用程式設定

_azureMobile.js_ 檔案中的大部分設定在 [Azure 入口網站]中都有對等的「應用程式設定」。請使用下列清單在 [應用程式設定] 中設定您的應用程式：

| 應用程式設定 | _azureMobile.js_ 設定 | 說明 | 有效值 |
| :-------------------------- | :------------------------ | :---------------------------------------- | :------------------------------------------ |
| **MS\_MobileAppName** | 名稱 | 應用程式的名稱 | 字串 |
| **MS\_MobileLoggingLevel** | logging.level | 要記錄的訊息的最小記錄層級 | error、warning、info、verbose、debug、silly |
| **MS\_DebugMode** | debug | 啟用或停用偵錯模式 | true、false |
| **MS\_TableSchema** | data.schema | SQL 資料表的預設結構描述名稱 | 字串 (預設值：dbo) |
| **MS\_DynamicSchema** | data.dynamicSchema | 啟用或停用偵錯模式 | true、false |
| **MS\_DisableVersionHeader** | version (設定為未定義)| 停用 X-ZUMO-Server-Version 標頭 | true、false |
| **MS\_SkipVersionCheck** | skipversioncheck | 停用用戶端 API 版本檢查 | true、false |

設定「應用程式設定」：

1. 登入 [Azure 入口網站]。
2. 選取 [所有資源] 或 [應用程式服務]，然後按一下您「行動應用程式」的名稱。
3. 預設會開啟 [設定] 刀鋒視窗，如果沒有的話，請按一下 [設定]。
4. 按一下 [一般] 功能表中的 [應用程式設定]。
5. 捲動至 [應用程式設定] 區段。
6. 如果您的應用程式設定已經存在，請按一下應用程式設定的值來編輯該值。
7. 如果您的應用程式設定不存在，請在 [機碼] 方塊中輸入「應用程式設定」及在 [值] 方塊中輸入值。
8. 完成後，按一下 [儲存]。

變更大部分的應用程式設定都需要重新啟動服務。

### <a name="howto-use-sqlazure"></a>做法：使用 SQL Database 做為您的實際執行資料存放區

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

無論是何種 Azure App Service 應用程式類型，以 SQL Database 作為資料存放區的程序都是相同的。如果您尚未執行，請依照下列步驟建立新的行動應用程式後端。

1. 登入 [Azure 入口網站]。

2. 在視窗左上方，依序按一下 [+新增] 按鈕 > [Web + 行動] > [行動應用程式]，然後為您的「行動應用程式」後端命名。

3. 在 [資源群組] 方塊中，輸入與您應用程式相同的名稱。

4. 系統將會選取預設應用程式服務方案。如果您想要變更 App Service 方案，請按一下 [App Service 方案] > [+ 建立新方案]。為新的應用程式服務方案命名，並選取適當位置。按一下 [定價層]，並選取適當的服務定價層。選取 [檢視全部] 以檢視其他價格選項，例如 [免費] 和 [共用]。選取定價層後，按一下 [選取] 按鈕。回到 [App Service 方案] 刀鋒視窗，然後按一下 [確定]。

5. 按一下 [建立]。如此即會建立您稍後將部署伺服器專案的行動應用程式後端。佈建行動應用程式後端可能需要幾分鐘。「行動應用程式」後端佈建完畢後，入口網站會開啟「行動應用程式」後端的 [設定] 刀鋒視窗。

行動應用程式後端建立後，您可以選擇將現有的 SQL Database 連接到您的行動應用程式後端，或建立新的 SQL Database。在這一節中，您將建立新的 SQL Database。

> [AZURE.NOTE] 如果在與新行動應用程式後端相同的位置中已經有資料庫，您可以改為選取 [使用現有的資料庫]，然後選取該資料庫。不建議您使用位在不同位置的資料庫，因為這會需要額外的頻寬成本和產生更高的延遲。

6. 在新「行動應用程式」後端中，依序按一下 [設定] > [行動應用程式] > [資料] > [+加入]。

7. 在 [新增資料連接] 刀鋒視窗中，依序按一下 [SQL Database - 設定必要設定] > [建立新的資料庫]。在 [名稱] 欄位中輸入新資料庫的名稱。

8. 按一下 [伺服器]。在 [新增伺服器] 刀鋒視窗中，於 [伺服器名稱] 欄位中輸入唯一的伺服器名稱，然後提供合適的 [伺服器管理登入] 和 [密碼]。確定已勾選 [允許 Azure 服務存取伺服器]。按一下 [確定]。

	![建立 Azure SQL Database][6]

9. 在 [新增資料庫] 刀鋒視窗中，按一下 [確定]。

10. 返回 [加入資料連接] 刀鋒視窗，選取 [連接字串]，輸入您建立資料庫時提供的登入與密碼。如果您使用現有的資料庫，請提供該資料庫的登入認證。輸入完成後，按一下 [確定]。

11. 再次返回 [**新增資料連接**] 刀鋒視窗，按一下 [**確定**] 以建立資料庫。

<!--- END OF ALTERNATE INCLUDE -->

建立資料庫可能需要幾分鐘的時間。使用 [通知] 區域來監視部署的進度。在資料庫成功部署之前，請勿繼續進行。成功部署後，將會在您行動後端的 [應用程式設定] 中建立 SQL 資料庫執行個體的連接字串。您可以在 [設定] > [應用程式設定] > [連接字串] 中看到此應用程式設定。

### <a name="howto-tables-auth"></a>做法：需經過驗證才能存取資料表

如果您想要在資料表端點使用「App Service 驗證」，就必須先在 [Azure 入口網站]中設定「App Service 驗證」。如需關於在 Azure App Service 中設定驗證的詳細資訊，請參閱您要使用的身分識別提供者所提供的組態指南：

- [如何設定 Azure Active Directory 驗證]
- [如何設定 Facebook 驗證]
- [如何設定 Google 驗證]
- [如何設定 Microsoft 驗證]
- [如何設定 Twitter 驗證]

每個資料表都有一個存取屬性可用來控制對資料表的存取。下列範例說明以靜態方式定義且需要驗證的資料表。

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

存取屬性接受三種值

  - *anonymous* 表示允許用戶端應用程式未經驗證就可讀取資料
  - *authenticated* 表示用戶端應用程式必須隨要求傳送有效的驗證權杖
  - *disabled* 表示此資料表目前已停用

如果未定義存取屬性，則會允許未經驗證的存取。

### <a name="howto-tables-getidentity"></a>作法：如何透過資料表使用驗證宣告

您可以設定驗證設定時所要求的多個宣告。這些宣告通常無法透過 `context.user` 物件取得，但可使用 `context.user.getIdentity()` 方法擷取。`getIdentity()` 方法會傳回會解析該物件的 Promise。物件會以驗證方法 (facebook、google、twitter、microsoftaccount 或 aad) 建立索引。

例如，如果您設定 Microsoft 帳戶驗證並要求電子郵件地址宣告時，可以利用下列來將電子郵件地址加入記錄：

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    table.columns = {
        "emailAddress": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;
    table.access = 'authenticated';

    /**
    * Limit the context query to those records with the authenticated user email address
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function queryContextForEmail(context) {
        return context.user.getIdentity().then((data) => {
            context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
            return context.execute();
        });
    }

    /**
    * Adds the email address from the claims to the context item - used for
    * insert operations
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function addEmailToContext(context) {
        return context.user.getIdentity().then((data) => {
            context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
            return context.execute();
        });
    }

    // Configure specific code when the client does a request
    // READ - only return records belonging to the authenticated user
    table.read(queryContextForEmail);

    // CREATE - add or overwrite the userId based on the authenticated user
    table.insert(addEmailToContext);

    // UPDATE - only allow updating of record belong to the authenticated user
    table.update(queryContextForEmail);

    // DELETE - only allow deletion of records belong to the authenticated uer
    table.delete(queryContextForEmail);

    module.exports = table;

若要查看哪些宣告可用，請使用網頁瀏覽器來檢視網站的 `/.auth/me` 端點。

### <a name="howto-tables-disabled"></a>做法：停用對特定資料表作業的存取權

除了會出現在資料表上以外，存取屬性也可用來控制個別作業。共有四種作業：

  - *read* 是在資料表上執行的 RESTful GET 作業
  - *insert* 是在資料表上執行的 RESTful POST 作業
  - *update* 是在資料表上執行的 RESTful PATCH 作業
  - *delete* 是在資料表上執行的 RESTful DELETE 作業

比方說，您可能會想要提供未經驗證的唯讀資料表。這可由以下資料表定義來提供：

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>做法：調整與資料表作業搭配使用的查詢

資料表作業的常見需求是提供受限制的資料檢視。例如，您可能會提供標有已驗證之使用者 ID 的資料表，而讓使用者只能讀取或更新自己的記錄。下列資料表定義將提供這項功能：

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
		context.query.where({ userId: context.user.id });
		return context.execute();
	});

    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
	    context.item.userId = context.user.id;
	    return context.execute();
    }

    module.exports = table;

正常執行的查詢作業，會有可使用 Where 子句來調整的查詢屬性。查詢屬性是一種 [QueryJS] 物件，可用來將 OData 查詢轉換成資料後端可以處理的項目。在簡單的等號比較案例中 (如同上例)，可以使用對應。加入特定的 SQL 子句，相對而言也較簡單：

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>做法：設定資料表上的虛刪除

虛刪除並不會實際刪除記錄。它會將已刪除的資料行設定為 true，而將記錄標示為已在資料庫中刪除。Azure Mobile Apps SDK 會自動從結果中移除已虛刪除的記錄，除非 Mobile Client SDK 使用 IncludeDeleted()。若要為資料表設定虛刪除，請在資料表定義檔中設定 softDelete 屬性。可能的範例如下：

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
		"complete": "boolean"
	};

	// Turn off dynamic schema
	table.dynamicSchema = false;

	// Turn on Soft Delete
	table.softDelete = true;

	// Require authentication to access the table
	table.access = 'authenticated';

	module.exports = table;

您將必須建立清除記錄的機制 - 從用戶端應用程式、透過 WebJob 或透過自訂機制。

### <a name="howto-tables-seeding"></a>做法：在您的資料庫中植入資料

在建立新的應用程式時，您可能會想要在資料表中植入資料。這可以在資料表定義 JavaScript 檔案中完成，如下所示：

	var azureMobileApps = require('azure-mobile-apps');

	var table = azureMobileApps.table();

	// Define the columns within the table
	table.columns = {
		"text": "string",
		"complete": "boolean"
	};
	table.seed = [
		{ text: 'Example 1', complete: false },
		{ text: 'Example 2', complete: true }
	];

	// Turn off dynamic schema
	table.dynamicSchema = false;

	// Require authentication to access the table
	table.access = 'authenticated';

	module.exports = table;

請務必注意，只有在 Azure Mobile Apps SDK 所建立的資料表中，才可執行資料植入。如果資料表已存在於資料庫中，則不會在資料表中插入任何資料。如果開啟動態結構描述，則會從植入的資料推斷結構描述。

建議您明確呼叫 initialize () 方法，以在服務開始執行時建立資料表。

### <a name="Swagger"></a>啟用 Swagger 支援

Azure App Service Mobile Apps 隨附內建 [Swagger] 支援。若要啟用 Swagger 支援，請先安裝 swagger-ui 做為相依性：

    npm install --save swagger-ui

安裝之後，您可以在 Azure Mobile Apps 建構函式中啟用 Swagger 支援：

    var mobile = azureMobileApps({ swagger: true });

您可能只想要在開發版本中啟用 Swagger 支援。您可以使用 `NODE_ENV` 應用程式設定來執行這項工作：

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

Swagger 端點將會位於 http://_yoursite_.azurewebsites.net/swagger。您可以透過 `/swagger/ui` 端點存取 Swagger UI。請注意，如果您選擇需要跨整個應用程式驗證，Swagger 會產生 / 端點的錯誤。為了獲得最佳結果，請在「Azure App Service 驗證/授權」設定中選擇允許未經驗證的要求通過，然後使用 `table.access` 屬性來控制驗證。

如果您想要只在本機進行開發時才使用 Swagger 支援，您也可以將 Swagger 選項新增到您的 `azureMobile.js` 檔案中。

## <a name="CustomAPI"></a>自訂 API

除了透過 /tables 端點的資料存取 API 以外，Azure Mobile Apps 也可提供自訂 API 涵蓋範圍。自訂 API 會以類似於資料表定義的方法定義，並且可存取所有相同功能，包括驗證。

如果您想要在「自訂 API」使用「App Service 驗證」，就必須先在 [Azure 入口網站]中設定「App Service 驗證」。如需關於在 Azure App Service 中設定驗證的詳細資訊，請參閱您要使用的身分識別提供者所提供的組態指南：

- [如何設定 Azure Active Directory 驗證]
- [如何設定 Facebook 驗證]
- [如何設定 Google 驗證]
- [如何設定 Microsoft 驗證]
- [如何設定 Twitter 驗證]

### <a name="howto-customapi-basic"></a>做法：定義簡單的自訂 API

定義自訂 API 的方法與資料表 API 很類似。

1. 建立 [api] 目錄。
2. 在 [api] 目錄中建立 API 定義 JavaScript 檔案。
3. 使用匯入方法將 **api** 目錄匯入。

以下是根據我們先前使用的基本應用程式範例所做的原型 API 定義。

	var express = require('express'),
		azureMobileApps = require('azure-mobile-apps');

	var app = express(),
		mobile = azureMobileApps();

	// Import the Custom API
	mobile.api.import('./api');

	// Add the mobile API so it is accessible as a Web API
	app.use(mobile);

	// Start listening on HTTP
	app.listen(process.env.PORT || 3000);

讓我們使用一個會透過 _Date.now()_ 方法傳回伺服器日期的簡單 API。以下是 api/date.js 檔案：

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		});
	};

	module.exports = api;

每個參數都是標準 RESTful 動詞之一 - GET、POST、PATCH 或 DELETE。此方法是會傳送必要輸出的標準 [ExpressJS 中介軟體]函式。

### <a name="howto-customapi-auth"></a>做法：需經過驗證才能存取自訂 API

Azure Mobile Apps SDK 對於資料表端點和自訂 API 會使用相同的方式實作驗證。若要在前一節開發的 API 中新增驗證，請新增 **access** 屬性：

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		});
	};
	// All methods must be authenticated.
	api.access = 'authenticated';

	module.exports = api;

您也可以指定特定作業的驗證：

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		}
	};
	// The GET methods must be authenticated.
	api.get.access = 'authenticated';

	module.exports = api;

對於需要驗證的自訂 API，必須使用資料表端點所使用的相同權杖。

### <a name="howto-customapi-auth"></a>做法：處理大型檔案上傳

Azure Mobile Apps SDK 使用[本文剖析器中介軟體](https://github.com/expressjs/body-parser)來接受您提交的本文內容並加以解碼。您可以預先設定讓本文剖析器接受較大的檔案上傳：

	var express = require('express'),
        bodyParser = require('body-parser'),
		azureMobileApps = require('azure-mobile-apps');

	var app = express(),
		mobile = azureMobileApps();

    // Set up large body content handling
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

	// Import the Custom API
	mobile.api.import('./api');

	// Add the mobile API so it is accessible as a Web API
	app.use(mobile);

	// Start listening on HTTP
	app.listen(process.env.PORT || 3000);

您可以調整上方所示的 50 Mb 限制。請注意，檔案在傳輸前會是 base-64 編碼，這會增加實際上傳大小。

### <a name="howto-customapi-sql"></a>作法：執行自訂 SQL 陳述式

Azure 行動應用程式 SDK 允許透過要求物件存取整個「內容」，讓您能輕鬆地針對定義的資料提供者執行參數化的 SQL 陳述式：

    var api = {
        get: function (request, response, next) {
            // Check for parameters - if not there, pass on to a later API call
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query - anything that can be handled by the mssql
            // driver is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query.  The context for Azure Mobile Apps is available through
            // request.azureMobile - the data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

S 可以存取此端點
## <a name="Debugging"></a>偵錯與疑難排解

Azure App Service 提供數個適用於 Node.js 應用程式的偵錯和疑難排解技術。這些技術全都可以使用。

- [監視 Azure App Service]
- [在 Azure App Service 中啟用診斷記錄]
- [在 Visual Studio 中疑難排解 Azure App Service]

### <a name="howto-diagnostic-logs"></a>做法：寫入至 Azure Mobile Apps 診斷記錄

Node.js 應用程式可存取多種不同的診斷記錄工具。在內部，Azure Mobile Apps Node.js SDK 會使用 [Winston] 進行診斷記錄。啟用偵錯模式，或是在 [Azure 入口網站]中將 **MS\_DebugMode** 應用程式設定設為 true，即會自動啟用此功能。產生的記錄檔會顯示在 [Azure 入口網站]上的「診斷記錄」中。

## <a name="in-portal-editing"></a>入口網站中的程式碼編輯經驗

Azure 入口網站中的特殊工具可讓您輕鬆地使用 Node.js 後端專案，而不需下載程式碼專案。入口網站中的簡單資料表和簡單 API，可讓您直接在入口網站中建立及使用資料表和自訂 API。您甚至可以直接在入口網站中使用 Visual Studio Team Services "Monaco" 編輯器，來編輯資料表作業和 API 指令碼。

### <a name="work-easy-tables"></a>做法：在 Azure 入口網站中使用簡單資料表

當您按一下後端網站設定中的 [簡單資料表] 時，您可以加入新的資料表，或是修改或刪除現有的資料表。您也可以查看資料表中的資料。

![使用簡單資料表](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

下列命令可用於資料表的命令列：

+ **變更權限** - 修改在資料表上讀取、插入、更新和刪除作業的權限。選項包括允許匿名存取、要求驗證，或停用作業的所有存取。這可修改 table.json 專案程式碼檔案。
+ **編輯指令碼** - 會在 Visual Studio Team Services 編輯器中開啟資料表的指令碼檔案。
+ **管理結構描述** - 新增或刪除資料行，或變更資料表索引。
+ **清除資料表** - 截斷現有的資料表可能會刪除所有資料列，但結構描述會維持不變。
+ **刪除資料列** - 刪除個別的資料列。
+ **檢視資料流記錄** - 將您連線到您網站的資料流記錄服務。

###<a name="work-easy-apis"></a>做法：在 Azure 入口網站中使用簡單 API

當您按一下後端網站設定中的 [簡單 API] 時，您可以加入新的自訂 API 端點，或是修改或刪除現有的 API 端點。

![使用簡單 API](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

在入口網站中，您可以變更指定之 HTTP 動作的存取權限、在 Visual Studio Team Services 編輯器中編輯 API 指令碼檔案，或檢視資料流記錄。

###<a name="online-editor"></a>做法：在 Visual Studio Team Services 中編輯程式碼

Azure 入口網站可讓您在 Visual Studio Team Services 中編輯 Node.js 後端指令碼檔案，而不需將專案下載到本機電腦。若要在線上編輯器中編輯指令碼檔案：

1. 在您的「行動應用程式」後端刀鋒視窗中，按一下 [所有設定] > [簡單資料表] 或 [簡單 API]，按一下資料表或 API，然後按一下 [編輯指令碼]。將會在 Visual Studio Team Services 編輯器中開啟指令碼檔案。

	![Visual Studio Team Services 程式碼編輯器](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)

2. 在線上編輯器中變更程式碼檔案。當您輸入資料時，會自動儲存變更。

您也可以在網站上，從編輯器執行程式碼。


<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ../../includes/media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png

<!-- URLs -->
[Android 用戶端快速入門]: app-service-mobile-android-get-started.md
[Apache Cordova 用戶端快速入門]: app-service-mobile-cordova-get-started.md
[iOS 用戶端快速入門]: app-service-mobile-ios-get-started.md
[Xamarin.iOS 用戶端快速入門]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android 用戶端快速入門]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms 用戶端快速入門]: app-service-mobile-xamarin-forms-get-started.md
[Windows 市集用戶端快速入門]: app-service-mobile-windows-store-dotnet-get-started.md
[HTML/Javascript Client QuickStart]: app-service-html-get-started.md
[離線資料同步]: app-service-mobile-offline-data-sync.md
[如何設定 Azure Active Directory 驗證]: app-service-mobile-how-to-configure-active-directory-authentication.md
[如何設定 Facebook 驗證]: app-service-mobile-how-to-configure-facebook-authentication.md
[如何設定 Google 驗證]: app-service-mobile-how-to-configure-google-authentication.md
[如何設定 Microsoft 驗證]: app-service-mobile-how-to-configure-microsoft-authentication.md
[如何設定 Twitter 驗證]: app-service-mobile-how-to-configure-twitter-authentication.md
[Azure App Service 部署指南]: ../app-service-web/web-sites-deploy.md
[監視 Azure App Service]: ../app-service-web/web-sites-monitor.md
[在 Azure App Service 中啟用診斷記錄]: ../app-service-web/web-sites-enable-diagnostic-log.md
[在 Visual Studio 中疑難排解 Azure App Service]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[指定 Node 版本]: ../nodejs-specify-node-version-azure-apps.md
[使用 Node 模組]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[Azure 入口網站]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise
[GitHub 上的 basicapp 範例]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[GitHub 上的待辦事項範例]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[GitHub 上的範例目錄]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js 封裝]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/zh-TW/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS 中介軟體]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=AcomDC_0302_2016-------->