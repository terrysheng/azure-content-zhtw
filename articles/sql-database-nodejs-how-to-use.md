<properties linkid="develop-node-how-to-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (Node.js) - Azure feature guide" metaKeywords="" description="Learn how to use Azure SQL Database from Node.js." metaCanonical="" services="sql-database" documentationCenter="nodejs" title="How to Access Azure SQL Database from Node.js" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

# 如何從 Node.js 存取 Azure SQL Database

本指南說明使用 Microsoft Driver for Node.JS for SQL Server 存取 Azure SQL Database 的基本概念。涵蓋的案例包括**建立 SQL Database** 和**連接到 SQL Database**。本指南涵蓋從 [Azure 管理入口網站][]建立 SQL 資料庫。

## 目錄

-   [概念][]
-   [作法：設定環境][]
-   [作法：建立 SQL Database][]
-   [作法：取得 SQL Database 連線資訊][]
-   [作法：連接到 SQL Database 執行個體][]
-   [Azure 部署考量][]
-   [後續步驟][]

## <span id="Concepts"></span></a> 概念

### 什麼是 Azure SQL Database

Azure SQL Database 為 Azure 提供關聯式資料庫管理系統，並以 SQL Server 技術為基礎。在 SQL Database 中，您可以輕鬆地將關聯式資料庫解決方案佈建及部署到雲端，並充分利用分散式資料中心，而該資料中心可提供企業級的可用性、延展性和安全性，以及內建資料防護和自我修復功能。

## 什麼是 Microsoft Driver for Node.JS for SQL Server

Microsoft Driver for Node.JS for SQL Server 可讓開發人員從 Node.js 應用程式存取儲存在 Microsoft SQL Server 或 Azure SQL Database 中的資料。該驅動程式目前僅是預覽版本；其他功能將在完成時整合到專案中。如需該驅動程式的詳細資訊，請參閱 Microsoft Driver for Node.JS for SQL Server 專案的 [Github 頁面][] (英文) 和相關聯的 [Wiki][] (英文)。

<div class="dev-callout">
<b>注意</b>
<p>Microsoft Driver for Node.JS for SQL Server 目前只有預覽版本，並依賴僅適用於 Microsoft Windows 和 Azure 作業系統的執行階段元件。</p>
</div>

## <span id="Setup"></span></a>作法：設定環境

### 安裝 SQL Server 原生用戶端

Microsoft SQL Server Driver for Node.js 依賴 SQL Server 原生用戶端。雖然原生用戶端會在應用程式部署到 Azure 時自動取得，但不會出現在您的本機開發環境中。您可以從 [Microsoft SQL Server 2012 功能套件][]下載頁面安裝 SQL Server 原生用戶端。

<div class="dev-callout">
<b>注意</b>
<p>SQL Server 原生套件僅適用於 Microsoft Windows 作業系統。雖然此驅動程式原本可在 Azure 上取得，但如果您在 Microsoft Windows 以外的作業系統上進行開發，將無法使用本文中的資訊在本機測試您的應用程式。</p>
</div>

### 安裝 Node.js

Node.js 可透過 [][]<http://nodejs.org/#download></a> 進行安裝。如果安裝封裝不適用於您的作業系統，可以根據原始碼建置 Node.js。

## <span id="CreateServer"></span></a>作法：建立 SQL Database

遵循下列步驟以建立 Azure SQL Database：

1.  登入[管理入口網站][Azure 管理入口網站]。
2.  按一下入口網站左下方的 [+ 新增] 圖示。

    ![Create New Azure Website][]

3.  按一下 [SQL Database]，然後按一下 [Custom Create]。

    ![Custom Create a new SQL Database][]

4.  輸入資料庫的 [名稱] 值、選取 \[版本] (WEB 或 BUSINESS)、針對您的資料庫選取 [最大大小]，選擇 [定序]，然後選取 [新增 SQL Database 伺服器]。按一下對話方塊底部的箭頭。(請注意，如果先前已建立 SQL Database，可以從 [選擇伺服器] 下拉式清單選擇現有的伺服器)。

    ![Fill in SQL Database settings][]

5.  輸入系統管理員名稱和密碼 (並確認密碼)，選擇將要建立新 SQL 資料庫的區域，然後勾選 `Allow Azure Services to access the server` 方塊。

    ![Create new SQL Database server][]

若要查看伺服器和資料庫資訊，請按一下 Azure 管理入口網站中的 [SQL 資料庫]。您可以接著按一下 [資料庫] 或 [伺服器]，查看相關資訊。

![View server and database information][]

## <span id="ConnectionInfo"></span></a>作法：取得 SQL Database 連線資訊

若要取得 SQL Database 連線資訊，請按一下入口網站中的 [SQL DATABASE]，然後按一下資料庫的名稱。

![View database information][]

接著，按一下 [Show connection strings]。

![Show connection strings][]

在結果視窗的 ODBC 區段中，記下連接字串的值。這就是從節點應用程式連接到 SQL Database 時將會使用的連接字串。您的密碼會是建立 SQL Database 時所用的密碼。

## <span id="Connect"></span></a>作法：連接到 SQL Database 執行個體

### 安裝 node-sqlserver

Microsoft Driver for Node.JS for SQL Server 是以 node-sqlserver 原生模組提供。從[下載中心][]可以取得此模組的二進位版本。若要使用二進位版本，請執行下列步驟：

1.  將二進位封存檔解壓縮至應用程式的 **node\_modules** 目錄。
2.  執行從封存檔解壓縮的 **node-sqlserver-install.cmd** 檔案。這樣會在 **node\_modules** 之下建立 **node-sqlserver** 子目錄，並將驅動程式檔案移到此新目錄結構中。
3.  不再需要 **node-sqlserver-install.cmd** 檔案，請予以刪除。

<div class="dev-callout">
<b>注意</b>
<p>您也可以使用 npm 公用程式來安裝 node-sqlserver 模組；不過，這麼做會叫用 node-gyp 以在您的系統上建置該模組的二進位版本。</p>
</div>

### 指定連接字串

若要使用 node-sqlserver，您必須在您的應用程式中提出要求並指定連接字串。連接字串應是本文的[作法：取得 SQL Database 連接線資訊][作法：取得 SQL Database 連線資訊]一節中傳回的 ODBC 值。程式碼應該類似如下範例：

    var sql = require('node-sqlserver');
    var conn_str = "Driver={SQL Server Native Client 10.0};Server=tcp:{dbservername}.database.windows.net,1433;Database={database};Uid={username};Pwd={password};Encrypt=yes;Connection Timeout=30;";

### 查詢資料庫

利用 **query** 方法指定 Transact-SQL 陳述式，即可執行查詢。下列程式碼可建立 HTTP 伺服器，並且在您檢視網頁時，從 [測試] 資料表的 [ID]、[Column1] 和 [Column2] 資料列傳回資料：

    var http = require('http')
    var port = process.env.port||3000;
    http.createServer(function (req, res) {
        sql.query(conn_str, "SELECT * FROM TestTable", function (err, results) {
            if (err) {
                res.writeHead(500, { 'Content-Type': 'text/plain' });
                res.write("Got error :-( " + err);
                res.end("");
                return;
            }
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            for (var i = 0; i < results.length; i++) {
                res.write("ID: " + results[i].ID + " Column1: " + results[i].Column1 + " Column2: " + results[i].Column2);
            }
            res.end("; Done.");
        });
    }).listen(port);

雖然上述範例說明如何在結果集合中一次傳回所有的資料列，但您也可以傳回陳述式物件，以便訂閱事件。如此一來，您即可在個別的資料列和資料欄傳回時收到這些資料。下列範例示範如何執行此作業：

    var stmt = sql.query(conn_str, "SELECT * FROM TestTable");
    stmt.on('meta', function (meta) { console.log("We've received the metadata"); });
    stmt.on('row', function (idx) { console.log("We've started receiving a row"); });
    stmt.on('column', function (idx, data, more) { console.log(idx + ":" + data);});
    stmt.on('done', function () { console.log("All done!"); });
    stmt.on('error', function (err) { console.log("We had an error: " + err); });

## <span id="Deploy"></span></a>Azure 部署考量

<div class="dev-callout">
<b>注意</b>
<p>下列資訊是以 Microsoft Driver for Node.JS for SQL Server 的預覽版本為基礎。這一節中的資訊可能不是在 Azure 中使用 node-sqlserver 模組的最新資訊。如需最新資訊，請參閱 Github 上的 <a href="https://github.com/WindowsAzure/node-sqlserver">Microsoft Driver for Node.JS for SQL Server 專案頁面</a> (英文)。</p>
</div>

Azure 不會在執行階段動態安裝 node-sqlserver 模組，所以您必須確保應用程式部署包含該模組的二進位版本。確定下列目錄結構存在並包含以下所述的檔案，即可確認您的部署包含該模組的二進位版本：

    application directory
        node_modules
            node-sqlserver
                lib

**node-sqlserver** 目錄應包含 **package.json** 檔案。**lib** 目錄應包含 **sql.js** 和 **sqlserver.node** 檔案，這是 node-sqlserver 模組的編譯後形式。

如需將 Node.js 應用程式部署到 Azure 的詳細資訊，請參閱[建立 Node.js 應用程式並將其部署到 Azure 網站][]和 [Node.js 雲端服務][]。

## <span id="NextSteps"></span></a>後續步驟

-   [Microsoft Driver for Node.JS for SQL Server 簡介][]
-   [Github.com 上的 Microsoft Driver for Node.js for SQL Server][Github 頁面]

  [Azure 管理入口網站]: https://manage.windowsazure.com
  [概念]: #Concepts
  [作法：設定環境]: #Setup
  [作法：建立 SQL Database]: #CreateServer
  [作法：取得 SQL Database 連線資訊]: #ConnectionInfo
  [作法：連接到 SQL Database 執行個體]: #Connect
  [Azure 部署考量]: #Deploy
  [後續步驟]: #NextSteps
  [Github 頁面]: https://github.com/WindowsAzure/node-sqlserver
  [Wiki]: https://github.com/WindowsAzure/node-sqlserver/wiki
  [Microsoft SQL Server 2012 功能套件]: http://www.microsoft.com/zh-TW/download/details.aspx?id=29065
  []: http://nodejs.org/#download
  [Create New Azure Website]: ./media/sql-database-nodejs-how-to-use/new_website.jpg
  [Custom Create a new SQL Database]: ./media/sql-database-nodejs-how-to-use/create_custom_sql_db.jpg
  [Fill in SQL Database settings]: ./media/sql-database-nodejs-how-to-use/new-sql-db.png
  [Create new SQL Database server]: ./media/sql-database-nodejs-how-to-use/db-server-settings.png
  [View server and database information]: ./media/sql-database-nodejs-how-to-use/sql-dbs-portal.png
  [View database information]: ./media/sql-database-nodejs-how-to-use/go-to-db-info.png
  [Show connection strings]: ./media/sql-database-nodejs-how-to-use/show-connection-string.png
  [下載中心]: http://www.microsoft.com/zh-TW/download/details.aspx?id=29995
  [建立 Node.js 應用程式並將其部署到 Azure 網站]: /zh-TW/develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js 雲端服務]: /zh-TW/develop/nodejs/tutorials/getting-started/
  [Microsoft Driver for Node.JS for SQL Server 簡介]: http://blogs.msdn.com/b/sqlphp/archive/2012/06/08/introducing-the-microsoft-driver-for-node-js-for-sql-server.aspx
