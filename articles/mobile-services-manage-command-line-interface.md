<properties linkid="develop-mobile-tutorials-command-line-administration" urlDisplayName="Command Line Administration" pageTitle="Administering a Mobile Service at the command line - Azure tutorial" metaKeywords="" description="Learn how to create, deploy, and manage your Azure Mobile Service using command-line tools." metaCanonical="" services="" documentationCenter="Mobile" title="Automate mobile services with command-line tools" authors="glenga" solutions="" manager="" editor="" />

使用命令列工具將行動服務自動化
==============================

本主題將示範如何使用 Azure 命令列工具將 Azure 行動服務的建立和管理自動化。本主題將示範如何安裝並開始使用命令列工具來執行下列行動服務工作：

-   [建立新的行動服務](#create-service)
-   [建立新資料表](#create-table)
-   [將指令碼註冊到資料表作業](#register-script)
-   [列出資料表](#list-tables)
-   [刪除現有資料表](#delete-table)
-   [列出行動服務](#list-services)
-   [刪除現有行動服務](#delete-service)

這些個別的命令結合為一個指令碼或批次檔後，即可將行動服務的建立、確認和刪除程序自動化。

若要使用 Azure 命令列工具管理行動服務，您需要已啟用 Azure 行動服務功能的 Azure 帳戶。

-   如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/zh-tw/pricing/free-trial/)。

-   如果您現已有帳戶，但需要啟用 Azure 行動服務預覽版，則請參閱[啟用 Azure 預覽功能](../create-a-windows-azure-account/#enable)。

本主題涵蓋 Azure 命令列工具支援的一些常用管理工作。如需詳細資訊，請參閱 [Azure 命令列工具文件](/en-us/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services)。

安裝工具安裝 Azure 命令列工具
-----------------------------

下列清單包含安裝命令列工具的相關資訊，視作業系統而定：

-   **Windows**：下載 [Azure 命令列工具安裝程式](http://go.microsoft.com/fwlink/p?LinkID=275464)。開啟已下載的 .msi 檔案，並依照提示完成安裝步驟。

-   **Mac**：下載 [Azure SDK 安裝程式](http://go.microsoft.com/fwlink/p?LinkId=252249)。開啟已下載的 .pkg 檔案，並依照提示完成安裝步驟。

-   **Linux**：安裝最新版本的 [Node.js](http://nodejs.org/) (請參閱[透過封裝管理員來安裝 Node.js](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager))，然後執行下列命令：

        npm install azure-cli -g

若要測試安裝，請在命令提示字元中輸入 `azure`。安裝成功時，您會看到所有可用的 `azure` 命令的清單。

匯入設定如何下載和匯入發行設定
------------------------------

若要開始進行，您必須先下載並匯入發行設定。接著您可以使用工具來建立和管理 Azure 服務。若要下載發行設定，請使用 `account download` 命令：

    azure account download

這樣會開啟預設瀏覽器，並提示您登入管理入口網站。登入之後，就會下載您的 `.publishsettings` 檔案。請記下此儲存的檔案位置。

接下來，執行下列命令 (其中將 `<path-to-settings-file>` 改為 `.publishsettings` 檔案的路徑)，以匯入 `.publishsettings` 檔案：

    azure account import <path-to-settings-file>

您可以使用 `account clear` 命令，以移除 `import` 命令儲存的所有資訊：

    azure account clear

若要查看 `account` 命令的選項清單，請使用 `-help` 選項：

    azure account -help

匯入發行設定之後，基於安全的考量，請刪除 `.publishsettings` 檔案。如需詳細資訊，請參閱[如何安裝適用於 Mac 和 Linux 的 Azure 命令列工具](http://go.microsoft.com/fwlink/p/?LinkId=275795) (英文)。您現在即可開始從命令列或批次檔建立和管理 Azure 行動服務。

建立服務如何建立行動服務
------------------------

您可以使用命令列工具建立新的行動服務執行個體。建立行動服務時，您也可以在新的伺服器中建立 SQL Database 執行個體。

下列命令將在您的訂閱中建立新的行動服務執行個體，其中 `<service-name>` 是新行動服務的名稱，`<server-admin>` 是新伺服器的名稱，`<server-password>` 是新登入的密碼：

    azure mobile create <service-name> <server-admin> <server-password>

當指定的行動服務存在時，`mobile create` 命令將失敗。在您的自動化指令碼中，您應該嘗試先刪除再重新建立行動服務。

列出服務如何列出訂閱中現有的行動服務
------------------------------------

下列命令將傳回 Azure 訂閱中所有行動服務的清單：

    azure mobile list

此命令也將顯示每個行動服務的目前狀態和 URL。

刪除服務如何刪除現有行動服務
----------------------------

您可以使用命令列工具刪除現有行動服務以及相關的 SQL Database 和 SQL Server。下列命令將刪除行動服務，其中 `<service-name>` 是所要刪除行動服務的名稱：

    azure mobile delete <service-name> -a -q

這個命令也可以加上 `-a` 和 `-q` 參數，以直接刪除行動服務使用的 SQL Database 和 SQL Server，而不顯示提示。

**注意**

如果不指定 `-q` 參數以及 `-a` 或 `-d`，執行將暫停，而且系統會提示您對 SQL Database 選取刪除選項。請只在沒有其他服務使用該資料庫或伺服器的情況下使用 `-a` 參數；否則請使用 `-d` 參數僅刪除屬於所要刪除行動服務的資料。

建立資料表如何在行動服務中建立資料表
------------------------------------

下列命令將在指定的行動服務中建立資料表，其中 `<service-name>` 是行動服務的名稱，`<table-name>` 是要建立之資料表的名稱：

    azure mobile table create <service-name> <table-name>

這會建立含預設權限的新資料表 `application` 來允許下列資料表作業：`insert`、`read`、`update` 和 `delete`。

下列命令將建立新資料表，並使該資料表具有公用 `read` 權限和僅授予管理員的 `delete` 權限：

    azure mobile table create <service-name> <table-name> -p read=public,delete=admin

下表顯示指令碼權限值與 [Azure 管理入口網站](https://manage.windowsazure.com/)中之權限值的比較。

<table border="1" width="100%"><tr><th>指令碼值</th><th>管理入口網站值</th></tr>
<tr><td><code>public</code></td><td>所有人</td></tr>
<tr><td><code>application</code> (預設值)</td><td>具有應用程式金鑰的任何人</td></tr>
<tr><td><code>user</code></td><td>僅限通過驗證的使用者</td></tr>
<tr><td><code>admin	</code></td><td>僅限指令碼和管理員</td></tr></table>

當指定的資料表已經存在時，`mobile create` 命令將失敗。在您的自動化指令碼中，您應該嘗試先刪除再重新建立資料表。

列出資料表如何列出行動服務中現有的資料表
----------------------------------------

下列命令將傳回行動服務中所有資料表的清單，其中 `<service-name>` 是行動服務的名稱：

    azure mobile table list <service-name>

此命令也顯示每個資料表上的索引數，以及目前在資料表中的資料列數。

刪除資料表如何刪除行動服務中的現有資料表
----------------------------------------

下列命令將從行動服務中刪除資料表，其中 `<service-name>` 是行動服務的名稱，`<table-name>` 是所要刪除資料表的名稱：

    azure mobile table delete <service-name> <table-name> -q

在自動化指令碼中，使用 `-q` 參數以刪除資料表而不顯示阻礙執行的確認提示。

註冊指令碼將指令碼註冊到資料表作業
----------------------------------

下列命令將上傳並註冊函數到資料表上的作業，其中 `<service-name>` 是行動服務的名稱，`<table-name>` 是資料表的名稱，`<operation>` 是資料表作業，這可以是 `read`、`insert`、`update` 或 `delete`：

    azure mobile script upload <service-name> table/<table-name>.<operation>.js

請注意，此作業將從本機電腦上傳 JavaScript (.js) 檔案。檔案的名稱必須由資料表和作業名稱組成，而且檔案必須位在與執行命令的位置相對的 `table` 子資料夾中。例如，下列作業將上傳並註冊屬於 `TodoItems` 資料表的新 `insert` 指令碼。

    azure mobile script upload todolist table/todoitems.insert.js

指令碼檔案中的函數宣告也必須符合註冊的資料表作業。這表示，對於 `insert` 指令碼而言，上傳的指令碼要包含具有下列特徵碼的函數：

    function insert(item, user, request) {
            ...
    } 

如需關於註冊指令碼的詳細資訊，請參閱[行動服務伺服器指令碼參照](http://go.microsoft.com/fwlink/p?LinkId=262293)。

