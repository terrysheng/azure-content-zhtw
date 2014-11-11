<properties linkid="develop-php-website-with-sql-database-and-git" urlDisplayName="Web w/ SQL + Git" pageTitle="PHP website with SQL Database and Git - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in SQL Database and use Git deployment to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="Create a PHP website with a SQL Database and deploy using Git" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# 透過 SQL 資料庫建立 PHP 網站並使用 Git 來部署

本教學課程說明如何搭配 Azure SQL 資料庫建立 PHP Azure 網站及如何使用 Git 來部署。本教學課程假設您的電腦上已安裝 [PHP][PHP]、[SQL Server Express][SQL Server Express]、[Microsoft Drivers for SQL Server for PHP][Microsoft Drivers for SQL Server for PHP]、Web 伺服器和 [Git][Git]。完成本指南後，您將會有一個在 Azure 中執行的 PHP-SQL 資料庫網站。

> [WACOM.NOTE]
> 您可以使用 [Microsoft Web Platform Installer][Microsoft Web Platform Installer] 來安裝和設定 PHP、SQL Server Express、適用於 SQL Server for PHP 的 Microsoft 驅動程式和 Internet Information Services (IIS)。

您將了解：

-   如何使用 Azure 管理入口網站建立 Azure 網站和 SQL 資料庫。由於預設會在 Azure 網站上啟用 PHP，因此您不需要執行任何特殊步驟就能執行 PHP 程式碼。
-   如何使用 Git 來發行與重新發行應用程式到 Azure。

依照本教學課程進行，您將使用 PHP 建置一個簡易的註冊網頁應用程式。該應用程式將在 Azure 網站中託管。完成之應用程式的螢幕擷取畫面如下：

![Azure PHP Web Site][Azure PHP Web Site]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## 建立 Azure 網站並設定 Git 發行

請依照下列步驟來建立 Azure 網站和 SQL 資料庫：

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。
2.  按一下入口網站左下方的 [新增] 圖示。
    ![建立新的 Azure 網站][建立新的 Azure 網站]

3.  依序按一下 [網站] 及 [自訂建立]。

    ![Custom Create a new Web Site][Custom Create a new Web Site]

    輸入 [URL] 的值，然後從 [資料庫] 下拉式清單中選取 [建立新的 SQL 資料庫]，接著在 [地區] 下拉式清單中為您的網站選取資料中心。按一下對話方塊底部的箭頭。

    ![Fill in web site details][Fill in web site details]

4.  輸入資料庫的 [名稱] 值、選取 [版本] [(WEB 或 BUSINESS)][(WEB 或 BUSINESS)]、選取資料庫的 [大小上限]、選擇 [定序]，並選取 [新的 SQL Database 伺服器]。按一下對話方塊底部的箭頭。

    ![Fill in SQL Database settings][Fill in SQL Database settings]

5.  輸入系統管理員名稱和密碼 (並確認密碼)，選擇將要建立新 SQL 資料庫伺服器的區域，然後勾選 `Allow Azure Services to access the server` 方塊。

    ![Create new SQL Database server][Create new SQL Database server]

    建立網站後，您會看到「建立網站 "[SITENAME]" 成功」的字樣。現在，您可以啟用 Git 發佈。

6.  按一下網站清單中顯示的網站名稱，以開啟該網站的 [快速入門] 儀表板。

    ![Open web site dashboard][Open web site dashboard]

7.  在 [快速入門] 頁面底部，選取 [設定從原始檔控制進行部署]。

    ![Set up Git publishing][Set up Git publishing]

8.  當系統詢問您「您的來源程式碼在哪裡？」時，選取 [Local Git repository]，然後按一下該箭頭。

    ![where is your source code][where is your source code]

9.  若要啟用 Git 發佈，您必須提供使用者名稱和密碼。記下您所建立的使用者名稱和密碼。(如果您之前設定過 Git 儲存機制，系統將會略過此步驟。)

    ![Create publishing credentials][Create publishing credentials]

    設定儲存機制需要幾秒鐘的時間。

10. 當您的儲存機制準備就緒時，您會看到將應用程式檔案發佈至該儲存機制的指示。請記下這些指示，以供稍後使用。

    ![Git instructions][Git instructions]

## 取得 SQL Database 連線資訊

若要連接到正在 Azure 網站上執行的 SQL 資料庫執行個體，您將需要連接資訊。若要取得 SQL Database 連接資訊，請依照下列步驟進行：

1.  從 Azure 管理入口網站中，按一下 [已連結的資源]，然後按一下資料庫名稱。

    ![Linked Resources][Linked Resources]

2.  按一下 [View connection strings]。

    ![Connection string][Connection string]

3.  從所產生對話方塊的 [PHP] 區段中，請記下 `SERVER`、`DATABASE` 和 `USERNAME` 的值。

## 在本機建置與測試您的應用程式

註冊應用程式是一項簡單的 PHP 應用程式，您只需提供名稱與電子郵件地址就能註冊活動。先前的註冊者相關資訊會顯示在資料表中。註冊資訊會儲存在 SQL Database 執行個體中。該應用程式包含兩個檔案 (複製/貼上以下提供的程式碼)：

-   **index.php**：顯示註冊表單，以及內含註冊者資訊的資料表。
-   **createtable.php**：建立應用程式的 SQL Database 資料表。只會使用一次此檔案。

若要在本機執行應用程式，請遵循下列步驟。請注意，這些步驟假設您的本機電腦上已設定 PHP、SQL Server Express 和 Web 伺服器，且您已啟用 [SQL Server 的 PDO 延伸模組][SQL Server 的 PDO 延伸模組]。

1.  建立名為 `registration` 的 SQL Server 資料庫。您可以從 `sqlcmd` 命令提示字元中使用下列命令來建立此資料庫：

        >sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
        1> create database registration
        2> GO   

2.  在 Web 伺服器的根目錄中，建立名為 `registration` 的資料夾，並於其中建立兩個檔案，一個名為 `createtable.php`，另一個名為 `index.php`。

3.  在文字編輯器或 IDE 中開啟 `createtable.php` 檔案，加入下列程式碼。此程式碼將會在 `registration` 資料庫中用於建立 `registration_tbl` 資料表。

        <?php
        // DB connection info
        $host = "localhost\sqlexpress";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
            id INT NOT NULL IDENTITY(1,1) 
            PRIMARY KEY(id),
            name VARCHAR(30),
            email VARCHAR(30),
            date DATE)";
            $conn->query($sql);
        }
        catch(Exception $e){
            die(print_r($e));
        }
        echo "<h3>Table created.</h3>";
        ?>

    請注意，您需要將 `$user` 和 `$pwd` 的值更新為您的本機 SQL Server 使用者名稱和密碼。

4.  開啟網頁瀏覽器並瀏覽至 **<http://localhost/registration/createtable.php>**。這會在資料庫中建立 `registration_tbl` 資料表。

5.  在文字編輯器或 IDE 中開啟 **index.php** 檔案，加入頁面的基本 HTML 和 CSS 程式碼 (稍後的步驟中將加入 PHP 程式碼)。

        <html>
        <head>
        <Title>Registration Form</Title>
        <style type="text/css">
            body { background-color: #fff; border-top: solid 10px #000;
                color: #333; font-size: .85em; margin: 20; padding: 20;
                font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
            }
            h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
            h1 { font-size: 2em; }
            h2 { font-size: 1.75em; }
            h3 { font-size: 1.2em; }
            table { margin-top: 0.75em; }
            th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
            td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
        </style>
        </head>
        <body>
        <h1>Register here!</h1>
        <p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
        <form method="post" action="index.php" enctype="multipart/form-data" >
              Name  <input type="text" name="name" id="name"/></br>
              Email <input type="text" name="email" id="email"/></br>
              <input type="submit" name="submit" value="Submit" />
        </form>
        <?php

        ?>
        </body>
        </html>

6.  在 PHP 標籤內，加入用來連線至資料庫的 PHP 程式碼。

        // DB connection info
        $host = "localhost\sqlexpress";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }

    同樣地，您需要將 `$user` 和 `$pwd` 的值更新為您的本機 MySQL 使用者名稱和密碼。

7.  在資料庫連接程式碼後面，加入可將登錄資訊插入至資料庫的程式碼。

        if(!empty($_POST)) {
        try {
            $name = $_POST['name'];
            $email = $_POST['email'];
            $date = date("Y-m-d");
            // Insert data
            $sql_insert = "INSERT INTO registration_tbl (name, email, date) 
                           VALUES (?,?,?)";
            $stmt = $conn->prepare($sql_insert);
            $stmt->bindValue(1, $name);
            $stmt->bindValue(2, $email);
            $stmt->bindValue(3, $date);
            $stmt->execute();
        }
        catch(Exception $e) {
            die(var_dump($e));
        }
        echo "<h3>Your're registered!</h3>";
        }

8.  最後，在上述程式碼後面，加入可從資料庫擷取資料的程式碼。

        $sql_select = "SELECT * FROM registration_tbl";
        $stmt = $conn->query($sql_select);
        $registrants = $stmt->fetchAll(); 
        if(count($registrants) > 0) {
            echo "<h2>People who are registered:</h2>";
            echo "<table>";
            echo "<tr><th>Name</th>";
            echo "<th>Email</th>";
            echo "<th>Date</th></tr>";
            foreach($registrants as $registrant) {
                echo "<tr><td>".$registrant['name']."</td>";
                echo "<td>".$registrant['email']."</td>";
                echo "<td>".$registrant['date']."</td></tr>";
            }
            echo "</table>";
        } else {
            echo "<h3>No one is currently registered.</h3>";
        }

您現在可以瀏覽至 **http://localhost/registration/index.php** 並測試應用程式。

## 發行您的應用程式

當您在本機完成應用程式測試之後，可以使用 Git 將其發行至 Azure 網站。不過，您必須先更新應用程式中的資料庫連接資訊。使用您稍早取得的資料庫連接資訊 (在＜**取得 SQL 資料庫連線資訊**＞一節中），將 `createdatabase.php` 和 `index.php` **兩者**檔案中的下列資訊都更新為適當的值：

    // DB connection info
    $host = "tcp:<value of SERVER>";
    $user = "<value of USERNAME>@<server ID>";
    $pwd = "<your password>";
    $db = "<value of DATABASE>";

> [WACOM.NOTE]
> 在 `$host` 中，SERVER 值的開頭必須加上 `tcp:`，而 `$user` 的值是串連 USERNAME、<'@'> 和伺服器識別碼值。伺服器識別碼是 SERVER 值的前 10 個字元。

現在，您可以準備設定 Git 發行，並發行應用程式。

> [WACOM.NOTE]
> 這些步驟與上述＜建立 Azure 網站＞和＜設定 Git 發行＞一節最後的步驟相同。

1.  開啟 GitBash (如果 Git 位於您的 `PATH`，則為終端機)，將目錄變更為應用程式的根目錄，並執行下列命令：

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    系統會提示您輸入先前建立的密碼。

2.  瀏覽至 **http://[網站名稱].azurewebsites.net/createtable.php**，以建立應用程式的 MySQL 資料表。
3.  瀏覽至 **http://[網站名稱].azurewebsites.net/index.php**，開始使用應用程式。

發行應用程式之後，您可以開始對其進行變更，並使用 Git 來發行它們。

## 將變更發行至您的應用程式

若要將變更發行至應用程式，請依照以下步驟進行：

1.  在本機對您的應用程式進行變更。
2.  開啟 GitBash (如果 Git 位於您的 `PATH`，則為終端機)，將目錄變更為應用程式的根目錄，並執行下列命令：

        git add .
        git commit -m "comment describing changes"
        git push azure master

    系統會提示您輸入先前建立的密碼。

3.  瀏覽至 **http://[網站名稱].azurewebsites.net/index.php** 查看變更。

  [PHP]: http://www.php.net/manual/en/install.php
  [SQL Server Express]: http://www.microsoft.com/zh-TW/download/details.aspx?id=29062
  [Microsoft Drivers for SQL Server for PHP]: http://www.microsoft.com/zh-TW/download/details.aspx?id=20098
  [Git]: http://git-scm.com/
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [Azure PHP Web Site]: ./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [建立新的 Azure 網站]: ./media/web-sites-php-sql-database-deploy-use-git/new_website.jpg
  [Custom Create a new Web Site]: ./media/web-sites-php-sql-database-deploy-use-git/custom_create.png
  [Fill in web site details]: ./media/web-sites-php-sql-database-deploy-use-git/website_details_sqlazure.jpg
  [Fill in SQL Database settings]: ./media/web-sites-php-sql-database-deploy-use-git/database_settings.jpg
  [Create new SQL Database server]: ./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg
  [Open web site dashboard]: ./media/web-sites-php-sql-database-deploy-use-git/go_to_dashboard.png
  [Set up Git publishing]: ./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png
  [where is your source code]: ./media/web-sites-php-sql-database-deploy-use-git/where_is_code.png
  [Create publishing credentials]: ./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png
  [Git instructions]: ./media/web-sites-php-sql-database-deploy-use-git/git-instructions.png
  [Linked Resources]: ./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg
  [Connection string]: ./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg
  [SQL Server 的 PDO 延伸模組]: http://php.net/pdo_sqlsrv
