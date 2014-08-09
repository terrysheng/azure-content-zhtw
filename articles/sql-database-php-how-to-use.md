<properties linkid="develop-php-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (PHP) - Azure feature guides" metaKeywords="Azure SQL Database PHP, SQL Database PHP" description="Learn how to create and connect to an Azure SQL Database from PHP." metaCanonical="" services="sql-database" documentationCenter="PHP" title="How to Access Azure SQL Database from PHP" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" videoId="" scriptId="" />

如何從 PHP 存取 Azure SQL Database
==================================

本指南將示範從 PHP 使用 Azure SQL Database 的基本概念。相關範例是以 PHP 撰寫的。涵蓋的案例包括**建立 SQL Database** 和**連接到 SQL Database**。本指南涵蓋從[管理入口網站](https://manage.windowsazure.com)建立 SQL Database。如需從生產入口網站執行這些工作的相關資訊，請參閱[開始使用 PHP 和 SQL Azure](http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx) (英文)。如需詳細資訊，請參閱[後續步驟](#NextSteps)一節。

什麼是 Azure SQL Database
-------------------------

Azure SQL Database 為 Azure 提供關聯式資料庫管理系統，並以 SQL Server 技術為基礎。在 SQL Database 中，您可以輕鬆地將關聯式資料庫解決方案佈建及部署到雲端，並充分利用分散式資料中心，而該資料中心可提供企業級的可用性、延展性和安全性，以及內建資料防護和自我修復功能。

目錄
----

-   [概念](#Concepts)
-   [作法：設定環境](#Setup)
-   [作法：建立 SQL Database](#CreateServer)
-   [作法：取得 SQL Database 連線資訊](#ConnectionInfo)
-   [作法：連接到 SQL Database 執行個體](#Connect)
-   [後續步驟](#NextSteps)

概念
----

由於 Azure SQL Database 是以 SQL Server 技術為基礎組建的，因此從 PHP 存取 SQL Database 與從 PHP 存取 SQL Server 非常類似。您可以在本機開發應用程式 (使用 SQL Server)，然後藉由只變更連線字串來連接 SQL Database。然而，SQL Database 和 SQL Server 之間存在某些可能會影響應用程式的差異。如需詳細資訊，請參閱 [Azure SQL Database 指導方針和限制](http://msdn.microsoft.com/zh-tw/library/windowsazure/ff394102.aspx)。

從 PHP 存取 SQL Database 的建議方法是使用 [Microsoft Drivers for PHP for SQL Server](http://www.microsoft.com/download/en/details.aspx?id=20098)。(本文中的範例將使用這些驅動程式。)The Microsoft Drivers for PHP for SQL Server 僅適用於 Windows。

作法：設定環境
--------------

設定開發環境的建議方法是使用 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=253447)。Web Platform Installer 可讓您選擇 Web 開發平台的元素，並且會自動安裝及設定這些元素。只要下載 Web Platform Installer 並選擇安裝 WebMatrix、PHP for WebMatrix 及 SQL Server Express，我們將會為您設定完整的開發環境。

您也可以手動設定環境：

-   安裝 PHP 及設定 IIS：<http://php.net/manual/en/install.windows.iis7.php>.
-   下載及安裝 SQL Server Express：<http://www.microsoft.com/en-us/download/details.aspx?id=29062>
-   下載及安裝 Microsoft Drivers for PHP for SQL Server：<http://php.net/manual/en/sqlsrv.requirements.php>.

作法：建立 SQL Database
-----------------------

遵循下列步驟以建立 Azure SQL Database：

1.  登入[管理入口網站](https://manage.windowsazure.com)。
2.  按一下入口網站左下方的 **[新增]**。

    ![Create New Azure Web Site](./media/sql-database-php-how-to-use-sql-database/plus-new.png)

3.  依序按一下**[資料服務]**、**[SQL DATABASE]** 及 **[CUSTOM CREATE]**。

    ![Custom Create a new SQL Database](./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png)

4.  輸入資料庫的 **[名稱]** 值、選取 **[版本]** (WEB 或 BUSINESS)、針對您的資料庫選取 **[最大大小]**，選擇 **[定序]**，然後選取 **[新增 SQL Database 伺服器]**。按一下對話方塊底部的箭頭。(請注意，如果先前已建立 SQL Database，可以從 **[選擇伺服器]** 下拉式清單選擇現有的伺服器)。

    ![Fill in SQL Database settings](./media/sql-database-php-how-to-use-sql-database/new-sql-db.png)

5.  輸入系統管理員名稱和密碼 (並確認密碼)，選擇將要建立新 SQL Database 的區域，然後勾選 [允許 Azure 服務存取伺服器]`` 方塊。

    ![Create new SQL Database server](./media/sql-database-php-how-to-use-sql-database/db-server-settings.png)

若要查看伺服器和資料庫資訊，請按一下管理入口網站中的 **[SQL Database]**。您可以接著按一下 **[資料庫]** 或 **[伺服器]**，查看相關資訊。

![View server and database information](./media/sql-database-php-how-to-use-sql-database/sql-dbs-portal.png)

作法：取得 SQL Database 連線資訊
--------------------------------

若要取得 SQL Database 連線資訊，請按一下入口網站中的 **[SQL DATABASE]**，然後按一下資料庫的名稱。

![View database information](./media/sql-database-php-how-to-use-sql-database/go-to-db-info.png)

接著按一下 **[View SQL Database connection strings for ADO.NET, ODBC, PHP, and JDBC]**。

![Show connection strings](./media/sql-database-php-how-to-use-sql-database/show-connection-string-2.png)

在結果視窗的 PHP 區段中，記下 **SERVER**、**DATABASE** 及 **USERNAME** 的值。您的密碼會是建立 SQL Database 時所用的密碼。

作法：連接到 SQL Database 執行個體
----------------------------------

以下範例示範如何使用 **SQLSRV** 和 **PDO\_SQLSRV** 擴充功能連接名為 `testdb` 的 SQL Database。您將需要從上述區段取得的資訊。將 `SERVER_ID` 取代為 10 位數的伺服器 ID (自上述區段取得之 SERVER 值的前 10 個字元)，然後將正確的值 (您的使用者名稱和密碼) 指派給 `$user` 和 `$pwd` 等變數。

##### SQLSRV

    $server = "tcp:<value of SERVER from section above>";
    $user = "<value of USERNAME from section above>"@SERVER_ID;
    $pwd = "password";
    $db = "testdb";

    $conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

    if($conn === false){
        die(print_r(sqlsrv_errors()));
    }

##### PDO\_SQLSRV

    $server = "tcp:<value of SERVER from section above>";
    $user = "<value of USERNAME from section above>"@SERVER_ID;
    $pwd = "password";
    $db = "testdb";

    try {
        $conn = new PDO( "sqlsrv:Server= $server ; Database = $db ", $user, $pwd);
        $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
    }
    catch(Exception $e){
        die(print_r($e));
    }

後續步驟
--------

如前文提及，SQL Database 的使用方法與 SQL Server 非常類似。一旦建立 SQL Database 連線 (如前文所示) 後，您便可以使用 **SQLSRV** 或 **PDO\_SQLSRV** API 來插入、擷取、更新及刪除資料。如需 **SQLSRV** 和 **PDO\_SQLSRV** API 的相關資訊，請參閱 [Microsoft Drivers for PHP for SQL Server 文件](http://msdn.microsoft.com/zh-tw/library/dd638075(SQL.10).aspx) (英文)。然而，SQL Database 和 SQL Server 之間存在某些可能會影響應用程式的差異。如需詳細資訊，請參閱 [Azure SQL Database 指導方針和限制](http://msdn.microsoft.com/zh-tw/library/windowsazure/ff394102.aspx)。

示範如何在 Azure 上搭配使用 SQL Database 和 PHP 的範例位於 &lt;https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure\>。

