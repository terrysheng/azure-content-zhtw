<properties linkid="manage-services-how-to-configure-websites" urlDisplayName="How to configure" pageTitle="How to configure websites - Azure service management" metaKeywords="Azure websites, configuring Azure websites, Azure SQL database, Azure MySQL" description="Learn how to configure websites in Azure, including how to configure a website to use a SQL Database or MySQL database." metaCanonical="" services="web-sites" documentationCenter="" title="How to Configure Websites" authors="mwasson" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/18/2014" ms.author="mwasson"></tags>

# 如何設定網站

在 Azure 管理入連接字串網站中，您可以將網站和連結的組態選項變更為其他 Azure 資源，例如資料庫。

## 目錄

-   [作法：變更網站組態選項][]
-   [作法：設定網站使用 SQL 資料庫][]
-   [作法：設定網站使用 MySQL 資料庫][]
-   [作法：設定自訂網域名稱][]
-   [作法：設定網站使用 SSL][]
-   [後續步驟][]

## <a name="howtochangeconfig"></a>作法：變更網站組態選項

<!-- HOW TO: CHANGE CONFIGURATION OPTIONS FOR A WEBSITE -->

設定網站組態選項：

1.  在[管理入口網站][]中，開啟網站的管理頁面。
2.  按一下 [設定] 索引標籤。

[設定] 索引標籤具有下列區段：

### 一般

**Framework 版本**。如果您的應用程式使用下列任何 Framework，請設定下列選項：

-   **.NET Framework**：設定 .NET Framework 版本。
-   **PHP**：設定 PHP 版本，或設為 [關閉] 以停用 PHP。
-   **Java**：選取已顯示的版本以啟用 Java，或設為 [關閉] 以停用 Java。
-   如果啟用 Java，請使用 [Web 容器] 選項以在 Tomcat 和 Jetty 版本之間選擇。
-   **Python**：選取 Python 版本，或設為 [關閉] 以停用 Python。

在技術上，針對您的網站啟用 Java 會停用 .NET、PHP 與 Python 選項。

**Managed 管線模式**。設定 IIS [管線模式][]。請保留 [已整合 (預設)] 的設定，除非您擁有的舊版網站需要舊版 IIS。

**平台**。選取應用程式是否在 32 位元或 64 位元環境中執行。64 位元環境需要 [基本] 或 [標準] 模式。[免費] 與 [共用] 模式一律於 32 位元環境中執行。

**Web 通訊端**。設為 [開啟] 以啟用 WebSocket 通訊協定，例如倘若您的網站使用 [ASP.NET SignalR][] 或 [socket.io][]。

**永遠開啟**。根據預設，網站如果閒置一段時間，就會卸載。此舉有助於系統保留資源。在 [基本] 或 [標準] 模式中，您可以啟用 [永遠開啟]，以讓網站全天候載入。如果您的網站執行連續的網路工作，應啟用 [永遠開啟]，否則這些網路工作可能無法可靠地執行

**在 Visual Studio Online 中編輯**。透過 Visual Studio Online 啟用即時程式碼編輯。一經啟用，[儀表板] 索引標籤就會在 [快速概覽] 區段之下顯示一個名為 [在 Visual Studio Online 中編輯] 的連結。按一下此連結，可直接在線上編輯網站。如果您需要驗證，可以使用基本的部署憑證。

注意：如果您已啟用從原始檔控制進行部署，則部署作業可能會覆寫您在 Visual Studio Online 編輯器中所做的變更。

### 憑證

在 [基本] 或 [標準] 模式中，您可以上傳自訂網域的 SSL 憑證。如需詳細資訊，請參閱[對 Azure 網站啟用 HTTPS][]。

您上傳的憑證會列示在此。憑證上傳完畢後，可以將其指派給訂閱及區域中的任何一個網站。萬用字元憑證可用於接受此憑證之網域內的任何網站上。只有當該憑證不具有有效繫結關係時，才能刪除憑證。

### 網域名稱

檢視或新增網站的其他網域名稱。如需詳細資訊，請參閱[設定 Azure 網站的自訂網域名稱][]。

### SSL 繫結

如果您已上傳 SSL 憑證，則可以將該憑證繫結至自訂網域名稱。如需詳細資訊，請參閱[對 Azure 網站啟用 HTTPS][]

### 部署

在啟用從原始檔控制進行部署時才會出現這個區段。請使用這些設定以設定部署。

-   **Git URL**。如果您已為 Azure 網站建立 Git 儲存機制，那麼 Git URL 就是您推送內容的 URL。
-   **部署觸發程序 URL**。您可在 GitHub、CodePlex、Bitbucket 或其他儲存機制上設定此 URL，以便在認可動作推送至儲存機制時觸發部署。
-   **要部署的分支**。指定當您推送內容時要部署的分支。

若要設定從原始檔控制進行部署，請檢視 [儀表板] 索引標籤，然後按一下 [設定從原始檔控制進行部署]。

### 應用程式診斷

從支援記錄功能的 Web 應用程式寫入診斷記錄的選項：

-   **檔案系統**。將記錄寫入網站的檔案系統。檔案系統記錄功能會持續運作 12 小時。您可以從 FTP 共用存取網站的記錄。(請參閱 [FTP 認證][])。
-   **資料表儲存體**。將記錄寫入 Azure 資料表儲存體。該儲存體中沒有時限，除非您停用記錄，否則記錄會一直啟用。
-   **Blob 儲存體**。將記錄寫入 Azure Blob 儲存體。該儲存體中沒有時限，除非您停用記錄，否則記錄會一直啟用。

**記錄層次**。記錄功能一經啟用，此選項便會指定要記錄的資訊數量 (錯誤、警告、資訊或詳細資訊)。

**管理資料表儲存體**。啟用資料表儲存體時，按一下此按鈕可設定儲存體帳戶和資料表名稱。

**管理 Blob 儲存體。**啟用 Blob 儲存體時，按一下此按鈕可設定儲存體帳戶和 Blob 儲存體名稱。

### 網站診斷

用於收集網站診斷資訊的選項。

**Web 伺服器記錄**。啟用 Web 伺服器記錄。記錄會儲存為 W3C 延伸的記錄檔格式。您可以將記錄儲存至 Azure 儲存體或網站的檔案系統。

-   如果您選擇 [檔案系統]，那麼所有記錄都會儲存到 [儀表板] 頁面上的 [FTP 診斷記錄] 下方列示的 FTP 網站。(請參閱 [FTP 認證][]。)
-   如果您選擇 [檔案系統]，請使用 [配額] 方塊以為記錄檔設定最大的磁碟空間量。最小值為 25MB，而最大值則是 100MB。預設值為 35MB。到達配額時，最新的檔案會覆寫最舊的檔案。如果您需要保留的記錄超過 100MB，請使用 Azure 儲存體，其提供您大上很多的儲存容量。
-   選擇性地按一下 [設定保留] 可在一段時間之後自動刪除檔案。依預設，系統永遠不會刪除記錄。

**詳細的錯誤訊息**。一經啟用，詳細的錯誤訊息會另存為 .htm 檔案。若要檢視檔案，請移至 [儀表板] 頁面上的 [FTP 診斷記錄] 下方列示的 FTP 網站。檔案都會儲存在 FTP 網站中的 /LogFiles/DetailedErrors 之下。(請參閱 [FTP 認證][]。)

**失敗要求的追蹤**。一經啟用，失敗要求就會記錄為 XML 檔案。若要檢視檔案，請移至 [儀表板] 頁面上的 [FTP 診斷記錄] 下方列示的 FTP 網站。(請參閱 [FTP 認證][]。)檔案會儲存在 /LogFiles/W3SVC*xxx* 之下，其中 xxx 是唯一識別碼。此資料夾內含有一個 XSL 檔案和一或多個 XML 檔案。請務必下載 XSL 檔案，因為 XSL 檔案可提供格式化和篩選 XML 檔案內容的功能。

**遠端偵錯**可啟用遠端偵錯。一經啟用，您就可以使用 Visual Studio 中的遠端偵錯工具直接連線至 Azure 網站。遠端偵錯將保持啟用達 48 小時。

**注意**：遠端偵錯無法在超過 20 個字元的網站名稱或使用者名稱上運作。

### 監控

在 [基本] 或 [標準] 模式中，您可以測試 HTTP 或 HTTPS 端點的可用性，最多可測試三個分散的地理位置。如果 HTTP 回應碼為錯誤 (4xx 或 5xx)，或是當回應時間超過 30 秒時，監視測試即告失敗。如果所有指定位置上的端點監視測試全都成功，表示該端點可用。

如需詳細資訊，請參閱 [如何：監視 Web 端點狀態][]。

### 開發人員分析

選擇 [附加元件] 以從清單中選取分析附加元件，或是前往 Azure 市集選擇。選擇 [自訂] 以從清單選取分析資料提供者，例如 New Relic。如果您使用自訂提供者，必須在 [提供者金鑰] 方塊中輸入授權金鑰。

如需使用 New Relic 搭配 Azure 網站的詳細資訊，請參閱 [Azure 網站上的 New Relic 應用程式效能管理][]。

### 應用程式設定

Web 應用程式啟動時載入的名稱/值組。

-   如果是 .NET 網站，這些設定會在執行階段插入至 .NET 組態的 AppSettings，並覆蓋現有的設定。

-   PHP、Python、Java 和 Node 應用程式可以在執行階段以環境變數的形式存取這些設定。系統會為每個應用程式設定建立兩個環境變數，一個變數具有由應用程式設定項目指定的名稱，另一個則具有 APPSETTING\_ 前置詞。這兩個變數都包含相同的值。

### 連接字串

連結資源的連接字串。

如果是 .NET 網站，這些連接字串會在執行階段插入至 .NET 組態的 connectionStrings 設定，並覆寫索引鍵等於所連結資料庫名稱的現有項目。

如果是 PHP、Python、Java 及 Node 應用程式，您可以在執行階段將這些設定視為環境變數使用，並加上連線類型前置詞。環境變數首碼如以下所示：

-   SQL Server：SQLCONNSTR\_
-   MySQL：MYSQLCONNSTR\_
-   SQL Database：SQLAZURECONNSTR\_
-   自訂：CUSTOMCONNSTR\_

例如，如果 MySql 連接字串命名為 connectionstring1，則可透過環境變數 `MYSQLCONNSTR_connectionString1` 加以存取。

**注意**：將資料庫資源連結至網站時也會建立連接字串。以此方式建立的連接字串會在於組態管理頁面上檢視時為唯讀狀態。

### 預設文件

網站的預設文件是所顯示未指定網站特定頁面的網頁。如果網站包含清單中的多個檔案，請務必讓預設文件在清單中置頂。

Web 應用程式可能會使用根據 URL 路由的模組，而非處理靜態內容，若是如此，就不會有像這樣的預設文件了。

### 處理常式對應

使用此區域可新增自訂指令碼處理器，以處理特定副檔名的要求。

-   **副檔名**。要處理的副檔名，例如 \*.php 或 handler.fcgi。
-   **指令碼處理器路徑**。指令碼處理器的絕對路徑。符合該副檔名的檔案要求，將由指令碼處理器來處理。使用路徑 `D:\home\site\wwwroot` 可指出網站的根目錄。
-   **其他引數**。指令碼處理器選用的命令列引數

### 虛擬應用程式與目錄

若要設定與您的網站相關聯的虛擬應用程式與目錄，請指定個別虛擬目錄及其相對於網站根目錄的對應實體路徑。或者，您可以選取 [應用程式] 核取方塊，勾選虛擬目錄作為網站組態中的應用程式。

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A SQL DATABASE -->

## <a name="howtoconfigSQL"></a>作法：設定網站以使用 SQL 資料庫

請遵循以下步驟，將網站連結至 SQL 資料庫：

1.  在[管理入口網站][1]中，選取 [網站] 以顯示由目前登入帳戶所建立的網站清單。

2.  從網站清單中選取一個網站，以開啟該網站的 [管理] 頁面。

3.  按一下 [Linked Resources] 索引標籤，這時 [Linked Resources] 頁面上會顯示一則訊息，指出「You have no linked resources」。

4.  按一下 [Link a Resource] 以開啟 [Link a Resource] 精靈。

5.  按一下 [建立新的資源] 以顯示可連結至您網站的資源類型清單。

6.  按一下 [SQL Database] 以顯示 [Link Database] 精靈。

7.  完成 [Link Database]] 精靈第 3 與第 4 頁上的必填欄位，然後按一下第 4 頁上的 [完成 核取記號。

Azure 會使用指定的參數建立 SQL 資料庫，然後將該資料庫連結至網站。

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A MYSQL DATABASE -->

## <a name="howtoconfigMySQL"></a>作法：設定網站以使用 MySQL 資料庫

若要設定網站以使用 MySQL 資料庫，請遵循使用 SQL 資料庫的相同步驟，但在 [連結資源精靈] 中，選擇 [MySQL 資料庫] 而非 [SQL 資料庫]。

或者，您可以使用 [自訂建立] 選項來建立網站。在 [資料庫] 下拉式清單中，選擇 [建立新的 MySQL 資料庫] 或 [Use an existing MySQL database]。

## <a name="howtodomain"></a>作法：設定自訂網域名稱

如需設定網站以使用自訂網域名稱的相關資訊，請參閱[設定 Azure 網站的自訂網域名稱][]。

## <a name="howtoconfigSSL"></a>作法：設定網站以使用 SSL

如需在 Azure 上設定自訂網域的 SSL 詳細資訊，請參閱[對 Azure 網站啟用 HTTPS][2]。

## <a name="next"></a>後續步驟

-   [如何調整網站大小][]

-   [如何監視網站][]

  [作法：變更網站組態選項]: #howtochangeconfig
  [作法：設定網站使用 SQL 資料庫]: #howtoconfigSQL
  [作法：設定網站使用 MySQL 資料庫]: #howtoconfigMySQL
  [作法：設定自訂網域名稱]: #howtodomain
  [作法：設定網站使用 SSL]: #howtoconfigSSL
  [後續步驟]: #next
  [管理入口網站]: https://manage.windowsazure.com/
  [管線模式]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [socket.io]: http://azure.microsoft.com/zh-TW/documentation/articles/web-sites-nodejs-chat-app-socketio/
  [對 Azure 網站啟用 HTTPS]: href="http://www.windowsazure.com/zh-TW/documentation/articles/web-sites-configure-ssl-certificate/
  [設定 Azure 網站的自訂網域名稱]: http://www.windowsazure.com/zh-TW/documentation/articles/web-sites-custom-domain-name/
  [FTP 認證]: http://azure.microsoft.com/zh-TW/documentation/articles/web-sites-manage#ftp-credentials
  [如何：監視 Web 端點狀態]: http://go.microsoft.com/fwLink/?LinkID=279906&clcid=0x409
  [Azure 網站上的 New Relic 應用程式效能管理]: http://www.windowsazure.com/zh-TW/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/
  [1]: http://manage.windowsazure.com
  [2]: http://www.windowsazure.com/zh-TW/documentation/articles/web-sites-configure-ssl-certificate/
  [如何調整網站大小]: http://www.windowsazure.com/zh-TW/documentation/articles/web-sites-scale/
  [如何監視網站]: http://www.windowsazure.com/zh-TW/documentation/articles/web-sites-monitor/
