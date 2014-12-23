
# 使用 SQL Server Management Studio 管理 Azure SQL Database 

您可以使用 Azure SQL Database 管理入口網站或 SQL Server Management Studio (SSMS) 用戶端應用程式，來管理 SQL Database 訂閱以及建立和管理相關的邏輯伺服器和資料庫。本指引將說明如何使用 Management Studio 來管理 SQL Database 邏輯伺服器和資料庫。

<div class="dev-callout-new-collapsed">
<strong>注意 <span>Click to collapse</span></strong>
<div class="dev-callout-content">
<p>您可以使用 SQL Server 2014、SQL Server 2012 或 SQL Server 2008 R2 版本的 SSMS。不支援舊版。</p>
</div>
</div>

本主題包含下列步驟：

-   [步驟 1：取得 SQL Server Management Studio][]
-   [步驟 2：連線到 SQL Database][]
-   [步驟 3：建立和管理資料庫][]
-   [步驟 4：建立和管理登入][]
-   [步驟 5：使用動態管理檢視監視 SQL Database][]

<h2><a id="Step1" name="Step1"> </a>步驟 1：取得 Management Studio</h2>

Management Studio 是一個整合式環境，用於
管理 SQL 資料庫。管理
Azure 上的資料庫時，您可以使用與 SQL Server 一起安裝的 Management Studio 應用程式，
或下載免費的 SQL Server 2012 Management Studio Express (SSMSE) 版本。以下步驟說明
如何安裝 SSMSE。

1.  在 [Microsoft SQL Server 2012 Express][] 頁面上，如果您執行的是 32 位元的作業系統，請選取 x86 版本的 Management Studio，如果執行的是 64 位元的作業系統，請選取 x64 版本的 Management Studio。按一下 [下載]****，並在系統出現提示時執行安裝程式。

2.  按一下 [新的 SQL Server 獨立安裝或將功能加入到現有安裝]**
**，然後按一下 [確定]****。

3.  接受授權條款，然後按 [下一步]****。

4. 按一下 [安裝]**** 安裝 SQL Server 安裝程式所需的檔案。

5.  在 [功能選擇]**** 畫面上，已預先選取 [管理工具 - 基本]**
**。這是因為您執行的是 Management Studio 的安裝程式。如果您執行的是所有 SQL Server Express 的安裝程式，請選擇 [管理工具 - 基本]****，然後按 [下一步]****。

6.  在 [錯誤報告]**** 畫面上，您可以選擇性地選擇傳送
錯誤報告給 Microsoft。這不是使用 SSMSE 的必要選項。按 [下一步]
    **** 開始安裝。

7.  完成安裝時，您將會看到 [完成]****
 頁面。按一下 [關閉]****。 


<h2><a id="Step2" name="Step2"> </a>步驟 2：連線到 SQL Database</h2>

若要連線到 SQL Database，您需要知道 Azure 上的伺服器名稱。您可能需要登入入口網站才能取得此資訊。

1.  登入 [Azure 管理入口網站][]。

2.  在左窗格中，按一下 [SQL Databases]****。

3.  在 SQL Databases 首頁上，按一下頁面頂端的 [伺服器]****，以列出與訂閱相關的所有伺服器。尋找想要連線的伺服器名稱，並將它複製到剪貼簿上。

	接著，設定您的 SQL Database 防火牆
允許來自本機電腦的連線。若要這麼做，請將本機電腦 IP 位址加入防火牆例外清單。

1.  在 SQL Databases 首頁上，按一下 [伺服器]****，然後按一下想要與其連線的伺服器。

2.  按一下頁面頂端的 [設定]****。

3.  複製 [目前用戶端 IP 位址] 中的 IP 位址。

4.  在 [設定] 頁面中，[允許的 IP 位址]**** 包括三個方塊，您可以在此處指定規則名稱與作為起始和結束值的 IP 位址範圍。在規則名稱中，您可以輸入您的電腦名稱。在起始範圍和結束範圍中，將您的電腦 IP 位址貼入這兩個方塊，然後按一下出現的核取方塊。

	規則名稱必須是唯一的。如果這是您的開發電腦，您可以在 IP 範圍起始方塊和 IP 範圍結束方塊中輸入 IP 位址。否則，您可能需要輸入較廣泛的 IP 位址範圍，以容納來自組織中其他電腦的連線。
 
5. 按一下頁面底端的 [儲存]****。

    **注意：** 最長可能會延遲五分鐘，
    防火牆設定的變更才會生效。

	您現在可以使用 Management Studio 來連線到 SQL Database。

1.  在工作列上，按一下 [開始]****，並依序指向 [所有程式]**** 和
    [Microsoft SQL Server 2012]****，然後按一下 [SQL Server**
 Management Studio]**。

2.  在 [連線到伺服器]**** 中，指定完整
    伺服器名稱做為 *serverName*.database.windows.net。在 Azure 上，伺服器名稱是由英數字元組成的自動產生字串。

3.  選取 [SQL Server 驗證]****。

4.  在 [登入]**** 方塊中，輸入 SQL Server 系統管理員登入，
    而您是在建立伺服器時以下列格式於入口網站中指定此登入：
    *login*@*yourServerName*。

5.  在 [密碼]**** 方塊中輸入密碼，
    而您是在建立伺服器時於入口網站中指定此密碼。

8.  按一下 [連線]**** 以建立連線。

在 Azure 上，每個 SQL Database 邏輯伺服器都是一個定義資料庫群組的抽象名詞。每個資料庫的實體位置可以是在資料中心的任何電腦上。 

在舊版的 Management Studio 中設定連線時，您必須直接連線到 [master]****。現在不再需要此步驟。根據伺服器名稱、驗證類型及管理員認證即可連線。

許多 SSMS 精靈 (可用於
在 SQL Server 資料庫上建立與修改登入和資料庫這類工作) 無法用於
Azure 上的 SQL 資料庫，因此您將需要利用
Transact-SQL 陳述式來完成這些工作。以下步驟
提供這些陳述式的範例。如需搭配使用
Transact-SQL 與 SQL Database 的詳細資訊 (包括所支援命令的詳細資料)，
請參閱 [Transact-SQL 參考 (SQL Database)][]。

<h2><a id="Step3" name="Step3"> </a>步驟 3：建立和管理資料庫</h2>

連線到 **master** 資料庫時，您可以在伺服器上建立新的
資料庫，並修改或卸除現有資料庫。以下步驟
說明如何透過 Management Studio 完成幾個常見的資料庫管理
工作。若要執行這些工作，請確定您使用伺服器層級主體登入連線到
**master** 資料庫，而伺服器層級主體登入是在您在設定伺服器時
所建立。

若要在 Management Studio 中開啟查詢視窗，請開啟 [資料庫] 資料夾、展開 [系統資料庫]**** 資料夾、在 [master]**** 上按一下滑鼠右鍵，然後按一下 [新增查詢]****。

-   使用 **CREATE DATABASE** 陳述式來建立新的資料庫。如需
詳細資訊，請參閱 [CREATE DATABASE (SQL Database)][]。以下陳述式建立名為
    **myTestDB** 的新資料庫，並指定它是 Web Edition 資料庫
    (大小上限為 1 GB)。

        CREATE DATABASE myTestDB
        (MAXSIZE=1GB,
         EDITION='web');

按一下 [執行]**** 來執行查詢。

-   使用 **ALTER DATABASE** 陳述式來修改現有資料庫，
    例如，如果您要變更資料庫的名稱、大小上限或版本
 (商務或 Web)。如需詳細資訊，請參閱 [ALTER DATABASE (SQL Database)][]。
    以下陳述式修改您在先前步驟中建立的資料庫
    以將大小上限變更為 5 GB。

        ALTER DATABASE myTestDB
        MODIFY
        (MAXSIZE=5GB,
         EDITION='web');

-   使用 **DROP DATABASE** 陳述式來刪除現有資料庫。
如需詳細資訊，請參閱 [DROP DATABASE (SQL Database)][]。以下陳述式將刪除 **myTestDB**
    資料庫，但現在不會立即予以卸除，因為您將在下一個步驟中使用它來建立登入。

        DROP DATABASE myTestBase;

-   master 資料庫具有 **sys.databases** 檢視，而您可以使用此檢視
來檢視所有資料庫的詳細資料。若要檢視所有現有資料庫，
    請執行下列陳述式：

        SELECT * FROM sys.databases;

-   在 SQL Database 中，**USE** 陳述式不支援
資料庫之間的切換。相反地，您需要建立
    直接與目標資料庫的連線。

<div class="dev-callout-new">
 <strong>注意 <span>Click to collapse</span></strong>
 <div class="dev-callout-content">
   <p>許多可建立或修改資料庫的 Transact-SQL 陳述式
必須在自己的批次中執行，且無法
與其他 Transact-SQL 陳述式群組在一起。如需詳細資訊，請參閱陳述式特定資訊 (可透過上面列出的連結取得)。</p>
</div>
</div>

<h2><a id="Step4" name="Step4"> </a>步驟 4：建立和管理登入</h2>

**master** 資料庫會追蹤登入，以及哪些登入具有
建立資料庫或其他登入的權限。管理登入的方式是
使用伺服器層級主體登入連線到 **master** 資料庫，
而伺服器層級主體登入是在設定伺服器時所建立。您可以使用
**CREATE LOGIN**、**ALTER LOGIN** 或 **DROP LOGIN** 陳述式
針對 master 資料庫執行查詢，而 master 資料庫將管理
整個伺服器的登入。如需詳細資訊，請參閱[管理 SQL Database 中的資料庫和登入][]。 


-   使用 **CREATE LOGIN** 陳述式來建立新的伺服器層級
登入。如需詳細資訊，請參閱 [CREATE LOGIN (SQL Database)][]。以下陳述式建立
稱為 **login1** 的新登入。將 **password1** 取代為您
    選擇的密碼。

        CREATE LOGIN login1 WITH password='password1';

-   使用 **CREATE USER** 陳述式來授與資料庫層級
權限。所有登入都必須建立於 **master** 資料庫中，
    但是針對連線到不同資料庫的登入，您
必須在該資料庫上使用下列陳述式，將資料庫層級權限授與它：**CREATE USER**
陳述式。如需詳細資訊，請參閱 [CREATE USER (SQL Database)][]。 

-   若要將
稱為 **myTestDB** 的資料庫的權限授與 login1，請完成下列
    步驟：

 1. 若要重新整理 [物件總管] 以檢視剛剛建立的 **myTestDB** 資料庫，請在 [物件總管] 中以滑鼠右鍵按一下伺服器名稱，然後按一下 [重新整理]****。  

如果連線已關閉，您可以透過在 [檔案] 功能表上選取 [連接物件總管]****。重複[步驟 2：連線到 SQL Database][] 中的指示來與資料庫連線。

 2. 在 **myTestDB** 資料庫上按一下滑鼠右鍵，並選取 [新增查詢]****。

    3. 針對 myTestDB 資料庫執行下列陳述式，以
建立名為 **login1User** 的資料庫使用者，而其對應至
伺服器層級登入 **login1**。

            CREATE USER login1User FROM LOGIN login1;

-   使用 **sp\_addrolemember** 預存程序，
將資料庫的適當權限等級授與使用者帳戶。如需
詳細資訊，請參閱 [sp_addrolemember (Transact-SQL)][]。以下陳述式透過下列方式將資料庫的唯讀權限授與 **login1User**：
將 **login1User** 新增至
**db\_datareader** 角色。

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   使用 **ALTER LOGIN** 陳述式來修改現有登入，
例如，如果您要變更登入的密碼。如需
詳細資訊，請參閱 [ALTER LOGIN (SQL Database)][]。應該針對下列資料庫執行 **ALTER LOGIN** 陳述式：
**master** 資料庫。切換回連線到該資料庫的查詢視窗。 

以下陳述式將修改 **login1** 登入以重設密碼。
將 **newPassword** 取代為您選擇的密碼，並將
    **oldPassword** 取代為登入的目前密碼。

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   使用 **the DROP LOGIN** 陳述式來刪除現有登入。
    刪除伺服器層級的登入也會同時刪除任何相關
資料庫使用者帳戶。如需詳細資訊，
請參閱 [DROP DATABASE (SQL Database)][]。**DROP LOGIN**
 陳述式應該針對 **master** 資料庫執行。
以下陳述式會刪除 **login1** 登入。

        DROP LOGIN login1;

-   master 資料庫具有 **sys.sql\_logins** 檢視，而您可以使用此檢視
來檢視登入。若要檢視所有現有的登入，請執行
    下列陳述式：

        SELECT * FROM sys.sql_logins;

<h2><a id="Step5" name="Step5"> </a>步驟 5：使用動態管理檢視監視 SQL Database</h2>

SQL Database 支援數個動態管理檢視，而您可以使用這些檢視
來監視個別資料庫。以下是下列項目的幾個範例：
您可以透過這些檢視擷取的監視器資料類型。如需
完整的詳細資料和更多使用方式範例，請參閱[使用動態管理檢視監視 SQL Database][]。

-   查詢動態管理檢視需要 **VIEW DATABASE STATE**
權限。若要將 **VIEW DATABASE STATE** 權限授與
    特定資料庫使用者，請連線到您想要使用伺服器層級主體登入所管理的資料庫，
    並針對資料庫執行下列
    陳述式：

        GRANT VIEW DATABASE STATE TO login1User;

-   使用下列檢視來計算資料庫大小：**sys.dm\_db\_partition\_stats**
 檢視。**sys.dm\_db\_partition\_stats** 檢視會傳回下列項目的頁面和
    資料列計數資訊：資料庫中的每個資料分割，而您
可以使用這些資訊來計算資料庫大小。下列查詢會傳回
    資料庫的大小 (MB)：

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   使用 **sys.dm\_exec\_connections** 和 **sys.dm\_exec\_sessions**
    檢視，以擷取下列項目的相關資訊：目前使用者連線以及
與資料庫相關聯的內部工作。下列查詢
    會傳回目前連線的相關資訊：

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   使用 **sys.dm\_exec\_query\_stats** 檢視來擷取彙總
已快取查詢計畫的效能統計資料。下列查詢
    會傳回依下列項目排名之前五項查詢的相關資訊：平均 CPU
    時間。

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;

<h2>其他資源</h2>

* [SQL Database 簡介][]   
* [管理 SQL Database 中的資料庫和登入][]   
* [使用動態管理檢視監視 SQL Database][]   
* [SQL Database 佈建模型][]   
* [將使用者加入 SQL Database][]   
* [Transact-SQL 參考 (SQL Database)][]

  [如何使用 Azure SQL Database]: http://www.windowsazure.com/zh-tw/develop/net/how-to-guides/sql-azure/
  [步驟 1：取得 SQL Server Management Studio]: #Step1
  [步驟 2：連線到 SQL Database]: #Step2
  [步驟 3：建立和管理資料庫]: #Step3
  [步驟 4：建立和管理登入]: #Step4
  [步驟 5：使用動態管理檢視監視 SQL Database]：
    #步驟 5
  [Microsoft SQL Server 2012 Express]: http://www.microsoft.com/zh-tw/download/details.aspx?id=29062
  [SSMS 安裝程式 - 選取安裝類型]：/media/installer_installation_type.png
  [SSMS 安裝程式 - 選取功能]：/media/installer_feature_selection.png
  [SSMS 安裝程式 - 安裝完成]：/media/installer_completed.png
  [Azure 管理入口網站]: http://manage.windowsazure.com/
  [從管理入口網站取得 SQL Database 伺服器名稱]：/media/portal_get_database_name.png
  [連線到 SSMS]：/media/ssms_connect.png
  [連線到 SSMS -- 屬性]：/media/ssms_connect_properties.png
  [Transact-SQL 參考 (SQL Database)]: http://msdn.microsoft.com/zh-tw/library/bb510741(v=sql.120).aspx
  [CREATE DATABASE (SQL Database)]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee336274.aspx
  [ALTER DATABASE (SQL Database)]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ff394109.aspx
  [DROP DATABASE (SQL Database)]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee336259.aspx
  [管理 SQL Database 中的資料庫和登入]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee336235.aspx
  [CREATE LOGIN (SQL Database)]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee336268.aspx
  [CREATE USER (SQL Database)]: http://msdn.microsoft.com/zh-tw/library/ee336277.aspx
  [sp_addrolemember (Transact-SQL)]: http://msdn.microsoft.com/zh-tw/library/ms187750.aspx
  [ALTER LOGIN (SQL Database)]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee336254.aspx
  [使用動態管理檢視監視 SQL Database]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ff394114.aspx
  [SQL Database 簡介]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee336230.aspx
  [SQL Database 佈建模型]: http://msdn.microsoft.com/zh-tw/library/ee336227.aspx
  [將使用者加入 SQL Database]: http://blogs.msdn.com/b/sqlazure/archive/2010/06/21/10028038.aspx
