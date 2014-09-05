<properties linkid="manage-services-how-to-configure-websites" urlDisplayName="How to configure" pageTitle="How to configure web sites - Azure service management" metaKeywords="Azure websites, configuring Azure websites, Azure SQL database, Azure MySQL" description="Learn how to configure web sites in Azure, including how to configure a web site to use a SQL Database or MySQL database." metaCanonical="" services="web-sites" documentationCenter="" title="How to Configure Web Sites" authors="timamm" solutions="" manager="" editor="mollybos" />

如何設定網站
============

在 Azure 管理入口網站中，您可以變更網站的組態選項，並將網站連結至其他 Azure 資源。例如，您可以將網站連結至 SQL Database 以提供更多功能。您也可以設定網站使用新的或現有 MySQL 資料庫。

目錄
----

-   [作法：變更網站組態選項](#howtochangeconfig)
-   [作法：設定網站使用 SQL 資料庫](#howtoconfigSQL)
-   [作法：設定網站使用 MySQL 資料庫](#howtoconfigMySQL)
-   [作法：設定自訂網域名稱](#howtodomain)
-   [作法：設定網站使用 SSL](#howtoconfigSSL)
-   [後續步驟](#next)

作法：變更網站組態選項
----------------------

請依照以下步驟，變更網站的組態選項。

1.  在管理入口網站中，開啟網站的管理頁面。
2.  按一下 **[設定]** 索引標籤，以開啟 **[設定]** 管理頁面。
3.  適當地設定下列網站組態選項：
    -   **一般**
        -   **.NET Framework 版本** - 如果 Web 應用程式使用的是 .NET Framework，請設定 Web 應用程式所需的 Framework 版本。
        -   **PHP 版本** - 如果您的 Web 應用程式使用 PHP，設定該 Web 應用程式所需的 PHP 版本。
        -   **Java 版本** - 選取顯示的 Java 版本，以在您的 Web 應用程式上啟用此功能，或是選取 [關閉] 停用 Java **功能**。如果您在 Web 應用程式上啟用了 Java，則 **[Web Container]** 選項可讓您選擇 Tomcat 或 Jetty 版本。

            **注意**：在技術上，針對您的網站啟用 Java 會停用 .NET、PHP 與 Python 選項。

        -   **Python 版本** - 顯示 Python 版本 (不可設定)。
        -   **Managed 管線模式** - 其中有兩個選擇 ([傳統] 與 **[整合式]**)，而「整合式」為預設模式。如果您的舊式網站仍舊只能在舊版的 IIS 中執行的話，請使用 [傳統] 選項。
        -   **平台** - 在 [標準] 模式下的網站，您可以選擇是否要讓應用程式在 32 位元或 64 位元環境中執行。[自由] 與 [共用] 模式下的網站一律於 32 位元環境中執行。
        -   **Web 通訊端** - 選擇 **[開啟]** 以啟用您的網站使用即時要求交談等模式應用程式。
        -   **Always On** - 依預設，網站如果閒置一段時間，就會卸載。此舉有助於系統保留資源。您可以在 [標準] 模式下啟用 **[Always On]** 設定，以讓網站全天候載入。因為當 **[Always On]** 選項停用時，連續的 Web 工作可能無法可靠地執行，因此當您的網站上正在執行連續的 Web 工作，請啟用 **[Always On]**。
        -   **Edit in Visual Studio Online** - 選取 **[開啟]** 可在 Visual Studio Online 狀態下啟用即時程式碼編輯。儲存此組態變更後，[儀表板] 索引標籤的 **[Quick Glance]** 區段會顯示 [Edit in Visual Studio Online] **連結**。按一下該連結，可直接在線上編輯網站。如果您需要驗證，可以使用基本的部署憑證。

            **注意**：如果您已啟用 [deployment from source control]，則部署作業可能會覆寫您在 Visual Studio Online 編輯器中所做的變更。因此，如果您想要直接在 Visual Studio Online 上編輯網站內容，最好不要使用 [deployment from source control]。

    -   **憑證** - 此為 [標準] 模式下獨有的選項，您只要按一下 **[上傳]** 即可上傳自訂網域的 SSL 憑證。您上傳的憑證會列示在此。系統支援萬用字元 (「星號」) 憑證 (帶有星號的憑證)。憑證上傳完畢後，可以將其指派給訂閱及區域中的任何一個網站。帶有星號的憑證只需上傳一次，就能用於接受此憑證之網域內的任何網站上。只有當特定憑證未與任何網站具有有效繫結關係時，才能刪除該憑證。
        **注意：** 自訂網域僅適用於 [共用] 及 [標準] 模式，而且只有在 [標準] 模式下才會對自訂網域提供 SSL 支援。如需在 Azure 上設定自訂網域的 SSL 詳細資訊，請參閱[對 Azure 網站啟用 HTTPS](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-configure-ssl-certificate/)。
    -   **網域名稱** - 在此檢視或新增網站的其他網域名稱。您可以按一下 [管理網域]，**新增自訂網域**。自訂網域僅適用於 **[共用]** 與 **[標準]** 模式。您可以在 **[標尺]** 管理頁面上，變更網站模式。自訂網域不適用 [自由] 模式。如需設定自訂網域的詳細資訊，請參閱[設定 Azure 網站的自訂網域名稱](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-custom-domain-name/)。
    -   **SSL 繫結** - 對自訂網域的 SSL 繫結功能僅適用於 [標準] 模式。針對特定網域名稱選擇 SSL 模式 (**[SNI]**、**[IP]** 或 **[沒有 SSL]**)。如果您選擇 SNI 或 IP，則您可從上傳的憑證中，為網域指定憑證。如需在 Azure 上設定自訂網域的 SSL 詳細資訊，請參閱[對 Azure 網站啟用 HTTPS](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-configure-ssl-certificate/)。如需 SNI 的詳細資訊，請參閱[伺服器名稱指示](http://en.wikipedia.org/wiki/Server_Name_Indication)。
    -   **部署** - 請使用以下設定來設定部署。
        -   **Git URL** - 如果您已在 Azure 網站上建立 Git 儲存機制，那就是它的 URL，您可將內容推送到該位置。
        -   **Deployment Trigger URL** - 您可在 GitHub、CodePlex、Bitbucket 或其他儲存機制上設定此 URL，以便在認可動作推送至儲存機制時觸發部署。
        -   **要部署的分支** - 此選項可讓您指定當您將內容推送至分支時要顯示的分支。
    -   **應用程式診斷** - 您可以設定選項，以便從使用追蹤檢測的程式碼所屬 Web 應用程式中收集診斷追蹤。應用程式診斷的記錄選項包括：
        -   **Application Logging (File System)** - 選擇 **[開啟]** 將應用程式記錄寫入網站的檔案系統。檔案系統記錄功能一經啟用，將持續運作 12 小時。您可以從 FTP 共用存取網站的記錄。可在 **[儀表板]** 上找到 FTP 共用連結。在 **[Quick Glance]** 下方，選擇 **[FTP Diagnostic Logs]** 或 **[FTPS Diagnostic Logs]**。
        -   **Application Logging (Storage)** - 選擇 **[開啟]**，將您的應用程式記錄寫入 Azure 儲存帳戶。儲存體帳戶的記錄功能沒有時限，除非您加以停用，否則會一直啟用。依預設，所有記錄都會儲存到 WAWSAppLogTable 資料表中。
        -   **記錄層級** - 記錄功能一經啟用，此選項便會指定要記錄的資訊數量 (錯誤、警告、資訊或詳細資訊)。
        -   **診斷儲存體** - 按一下 **[Manage Connection]** 會開啟 **[Manage diagnostic storage]** 對話方塊與下列選項，供您將記錄儲存至您的 Azure 儲存體帳戶：
            -   **儲存體帳戶名稱** - 選擇您要用來儲存記錄的儲存體帳戶。
            -   **儲存體存取金鑰** - 顯示選定的儲存體帳戶金鑰。如果您為儲存體帳戶重新產生金鑰，請在此手動輸入該新金鑰，或是使用其中一個 **[同步]** 按鈕。只有當目前登入的使用者對選取的儲存體帳戶具有存取權限時，才能使用這些同步按鈕。
            -   **Synchronize Primary Key** - 從您的 Azure 儲存體帳戶中擷取最新的主要金鑰。
            -   **Synchronize Secondary Key** - 從您的 Azure 儲存體帳戶中擷取最新的次要金鑰。
                **注意：** 如需 Azure 儲存體存取金鑰的詳細資訊，請參閱[作法：檢視、複製與重新產生儲存體儲存金鑰](http://www.windowsazure.com/zh-tw/documentation/articles/storage-manage-storage-account/#regeneratestoragekeys)。
    -   **網站診斷** - 為您的網站設定收集診斷資訊的選項，包括：
        -   **Web 伺服器記錄** - 指定是否要為網站啟用 Web 伺服器記錄。Web 伺服器記錄會儲存為 W3C 延伸的記錄檔格式。您可以將記錄儲存至 Azure 儲存體或檔案系統。
            **秘訣**：檔案系統中記錄儲存體的最大大小為 100 MB。如果您需要保留的記錄超過這個大小，請使用 Azure 儲存體，後者可提供您大上很多的儲存容量。
            -   若要將 Web 伺服器記錄儲存至 Azure 儲存體帳戶，請依序選擇 **[儲存體]** 及**[管理儲存體]**。在 **[Manage Storage for Site Diagnostics]** 對話方塊中，使用 **[儲存體帳戶]** 選項來選擇 Azure 儲存體帳戶，以因應保存記錄的容器需要。使用 **[Azure Blob Container]** 選項，選擇將保留記錄的容器，或是選取 **[Create a new blob container]** 來啟用 **[Blob Name]** 方塊，以方便您指定新容器的名稱。
                **注意**：如果您還沒有儲存體帳戶，請前往 Azure 入口網站的 [儲存體] 區段****，然後按一下 **[新增]** 建立帳戶。
            -   如果您選擇 **[檔案系統]**，那麼所有記錄都會儲存到 [儀表板] 管理頁面上的 [Blob Name] 下方列示的 FTP 網站****。啟用檔案系統儲存體會同時啟用 **[配額]** 方塊，方便您為記錄檔設定最大的磁碟空間量。最小值為 25MB，而最大值則是 100MB。預設值為 35MB。到達配額時，最新的檔案會覆寫最舊的檔案。
            -   依預設，Azure 儲存體帳戶中的 Web 伺服器記錄一律不會遭到刪除。若要指定自動刪除記錄的時間，選取 **[Set Retention]** 並在 [保留週期] 方塊內輸入記錄的保留天數****。您也可以針對檔案系統儲存體使用 [Set Retention] 選項****。
        -   **詳細的錯誤訊息** - 指定是否要為網站記錄詳細的錯誤訊息。一經啟用，刪除的錯誤訊息將以 .htm 檔案格式儲存到 [儀表板] 管理頁面上 [FTP Diagnostic Logs] 下方的 FTP 網站。連線至指定的 FTP 網站後，請瀏覽至 /LogFiles/DetailedErrors/ 以擷取內含詳細錯誤訊息的 .htm 檔案。
        -   **失敗要求追蹤** - 指定是否要啟用失敗要求追蹤。一經啟用，失敗要求追蹤輸出將寫入 XML 檔案並儲存至 [儀表板] 管理頁面上 [FTP Diagnostic Logs] 下方的 FTP 網站。連線至指定的 FTP 網站後，請瀏覽至 /LogFiles/W3SVC########\# (其中 ########\# 代表唯一的網站識別碼) 以擷取內含失敗要求追蹤輸出的 XML 檔案。
            **Important**
            The /LogFiles/W3SVC########\#/ folder contains an XSL file and one or more XML files.請確保將 XSL 檔案下載至 XML 檔案所在的相同目錄，因為 XSL 檔案可提供格式化功能，讓您在 Internet Explorer 中檢視時能夠篩選 XML 檔案內容。
        -   **遠端偵錯** - 將此選項設為 **[開啟]** 可在您所選用的 Visual Studio 2012 或 Visual Studio 2013 中啟用遠端偵錯功能。此功能一經啟用，您就能使用 Visual Studio 中的遠端偵錯程式直接連線到您的 Azure 網站。
             **注意**：遠端偵錯功能只能啟用 48 小時，而且無法在超過 20 個字元的網站名稱或使用者名稱上運作。
    -   **監視** - 對於標準模式下的網站而言，可測試 HTTP 或 HTTPS 端點的可用性。您最多可以針對三個地理位置上極為分散的地點測試其端點。如果 HTTP 回應碼大於或等於 400，或是當回應時間超過 30 秒時，監視測試即告失敗。如果所有指定位置上的端點監視測試全都成功，表示該端點可用。
    -   **開發人員分析** - 選擇 **[附加元件]** 以從清單中選取分析附加元件，或是前往 Azure 商店選擇。選擇 **[自訂]** 以從清單選取分析資料提供者，例如 New Relic。如果您使用自訂提供者，必須在 **[提供者金鑰]** 方塊中輸入授權金鑰。
         **注意**：如需使用 New Relic 搭配 Azure 網站的詳細資訊，請參閱[在 Azure 網站上進行 New Relic 應用程式效能管理](http://www.windowsazure.com/zh-tw/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/)。
    -   **應用程式設定** - 指定 Web 應用程式啟動時載入的名稱/值組。如果是 .NET 網站，這些設定會在執行階段插入 .NET 組態的 AppSettings 中，並請覆蓋現有的設定。如果是 PHP 和 Node 網站，您可以在執行階段將這些設定視為環境變數使用。
    -   **連線字串** - 檢視連結資源的連線字串。如果是 .NET 網站，這些連接字串會在執行階段插入 .NET 組態的 connectionStrings 設定中，並覆蓋索引鍵等於所連結資料庫名稱的現有項目。如果是 PHP 和 Node 網站，您可以在執行階段將這些設定視為環境變數使用，並加上連線類型首碼。環境變數首碼如以下所示：
        -   SQL Server：SQLCONNSTR\_
        -   MySQL：MYSQLCONNSTR\_
        -   SQL Database：SQLAZURECONNSTR\_
        -   自訂：CUSTOMCONNSTR\_

        例如，如果 MySql 連線字串命名為 connectionstring1，則可透過環境變數 `MYSQLCONNSTR_connectionString1` 加以存取。
        **注意**：連線字串是在您連結資料庫資源與網站時建立，當您在組態管理頁面上檢視時只能加以讀取。
    -   **預設文件** - 將網站的預設文件 (如果尚不存在) 新增至此清單。當使用者瀏覽網站，且未指定網站的特定頁面時，所顯示的網頁即是網站的預設文件。因此，假定網站是 http://contoso.com，如果預設文件設為 default.htm，當使用者將瀏覽器指向 http://www.contoso.com 時，系統會將使用者導向 http://www.contoso.com/default.htm。如果您的網站內含不只一個清單中的檔案時，請變更清單中的檔案順序，確保您的網站預設文件位於清單上方。
    -   **處理常式對應** - 新增自訂指令碼處理器，以處理特定副檔名的要求。在 **[副檔名]** 方塊中指定要處理的副檔名 (例如，\*.php 或 handler.fcgi)。符合此模式的檔案要求，將由 [Script Processor Path] 方塊中指定的指令碼來處理。****指令碼處理器需要絕對路徑 (D:\\home\\site\\wwwroot 路徑可用來指出網站的根目錄)。您可於 **[Additional Arguments (Optional)]** 方塊中指定該指令碼處理器選用的命令列引數。
    -   **虛擬應用程式與目錄** - 若要設定與您的網站相關聯的虛擬應用程式，請指定個別虛擬目錄及其和網站根目錄相對的實體路徑。或者，您可以選取 **[應用程式]** 核取方塊，勾選虛擬目錄做為網站組態中的應用程式。

4 .  按一下 [設定] 管理頁面下方的 **[儲存]**，儲存組態變更。

## 作法：設定網站使用 SQL 資料庫

請遵循以下步驟，將網站連結至 SQL Database：

1.  在[管理入口網站](http://manage.windowsazure.com)中，選取 **[網站]** 以顯示由目前登入儲存體所建立的網站清單。

2.  從網站清單中選取一個網站，以開啟該網站的 **[管理]** 頁面。

3.  按一下 **[Linked Resources]** 索引標籤，這時 **[Linked Resources]** 頁面上會顯示一則訊息，指出**You have no linked resources**。

4.  按一下 **[Link a Resource]** 以開啟 **[Link a Resource]** 精靈。

5.  按一下 **[建立新的資源]** 以顯示可連結至您的網站的資源類型清單。

6.  按一下 **[SQL Database]** 以顯示 **[Link Database]** 精靈。

7.  完成 **Link Database** 精靈第 3 與第 4 頁上的必填欄位，然後按一下第 4 頁上的 **完成** 核取記號。

Azure 會使用指定的參數建立 SQL 資料庫，然後將該資料庫連結至網站。

## 作法：設定網站使用 MySQL 資料庫
 若要設定網站使用 MySQL 資料庫，請遵循使用 SQL 資料庫的相同步驟，但在 [Link a Resource]** 精靈中，選擇 **[MySQL Database]** 而非 **[SQL Database]**。

或者，您可以使用 **[自訂建立]** 選項來建立網站。在 **[資料庫]** 下拉式清單中，選擇 **[建立新的 MySQL 資料庫]** 或 **[Use an existing MySQL database]**。

## 作法：設定自訂網域名稱
----------------------

如需設定網站使用自訂網域名稱的詳細資訊，請參閱[設定 Azure 網站的自訂網域名稱](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-custom-domain-name/)。

作法：設定網站使用 SSL
----------------------

如需在 Azure 上設定自訂網域的 SSL 詳細資訊，請參閱[對 Azure 網站啟用 HTTPS](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-configure-ssl-certificate/)。

後續步驟
--------

-   [如何調整網站大小](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-scale/)

-   [如何監視網站](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-monitor/)


