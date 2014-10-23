<properties linkid="develop-php-website-with-mysql-and-git" urlDisplayName="Web w/ MySQL + Git" pageTitle="PHP website with MySQL and Git - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use Git deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure website and deploy using Git" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# 建立 PHP-MySQL Azure 網站並使用 Git 部署

本教學課程說明如何建立 PHP-MySQL Azure 網站，以及如何使用 Git 部署該網站。您會使用 [PHP][]、MySQL 命令列工具 ([MySQL][install-mysql] 裡的一項工具)、Web 伺服器，以及安裝在您的電腦上的 [Git][]。本教學課程裡的說明可運用在包括 Windows、Mac 與 Linux 的任何作業系統上。完成本指南的步驟後，您將擁有在 Azure 上運作的 PHP/MySQL 網站。

您將了解：

-   如何使用 Azure 管理入口網站建立 Azure 網站和 MySQL 資料庫。由於預設會在 Azure 網站上啟用 PHP，因此您不需要執行任何特殊步驟就能執行 PHP 程式碼。
-   如何使用 Git 來發行與重新發行應用程式到 Azure。

依照本教學課程進行，您將使用 PHP 建置一個簡易的註冊網頁應用程式。該應用程式將在 Azure 網站中託管。完成之應用程式的螢幕擷取畫面如下：

![Azure PHP web site][]

<div class="dev-callout"><strong>注意</strong> <p>若要完成本教學課程，您需要已啟用 Azure 網站功能的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">Azure 免費試用</a>。</p> </div>

## 設定開發環境

本教學課程假設您已經具備 [PHP][]、MySQL 命令列工具 ([MySQL][install-mysql] 裡的一項工具)、Web 伺服器，以及安裝在您的電腦上的 [Git][]。

> [WACOM.NOTE]
> 如果您是在 Windows 電腦上執行本教學課程，則您可以安裝 [Azure SDK for PHP][] 來設定電腦使用 PHP 並自動設定 IIS (Windows 內建的 Web 伺服器)。

## <span id="create-web-site-and-set-up-git"></span></a>建立 Azure 網站並設定 Git 發行

依照以下步驟建立 Azure 網站與 MySQL 資料庫：

1.  登入 [Azure 管理入口網站][]。
2.  按一下入口網站左下方的 [新增] 圖示。

    ![Create New Azure web site][]

3.  依序按一下 [網站] 及 [自訂建立]。

    ![Custom Create a new web site][]

    輸入 **URL** 的值，然後從 [資料庫] 下拉式清單中選取 [建立新的 MySQL 資料庫]，接著在 [區域] 下拉式清單中為您的網站選取資料中心。按一下對話方塊底部的箭頭。

    ![Fill in web site details][]

4.  輸入資料庫的 [名稱] 值，然後在 [區域] 下拉式清單中選取資料庫的資料中心，接著核取代表您同意法律條款的方塊。按一下對話方塊底部的核取記號。

    ![建立新的 MySQL 資料庫][]

    建立網站後，您會看到「建立網站 "[SITENAME]" 成功」的字樣。現在，您可以啟用 Git 發佈。

5.  按一下網站清單中顯示的網站名稱，以開啟該網站的 [快速入門] 儀表板。

    ![Open web site dashboard][]

6.  在 [快速啟動] 下方，按一下 [設定 Git 發行]。

    ![Set up Git publishing][]

7.  若要啟用 Git 發佈，您必須提供使用者名稱和密碼。記下您所建立的使用者名稱和密碼。(如果您之前設定過 Git 儲存機制，系統將會略過此步驟。)

    ![Create publishing credentials][]

    設定儲存機制需要幾秒鐘的時間。

8.  當您的儲存機制準備就緒時，您會看到將應用程式檔案發佈至該儲存機制的指示。請記下這些指示，以供稍後使用。

    ![Git instructions][]

## 取得遠端 MySQL 連線資訊

若要連線至在 Azure 網站中執行的 MySQL 資料庫，您需要連線資訊。若要取得 MySQL 連線資訊，請依照以下步驟執行：

1.  從網站的儀表板上，按一下頁面右側的 [檢視連接字串] 連結：

    ![取得資料庫連線資訊][]

2.  記下 `Database`、`Data Source`、`User Id` 和 `Password` 的值。

## 在本機建置與測試您的應用程式

建立好 Azure 網站後，您可以在本機開發自己的應用程式，並在測試後進行部署。

註冊應用程式是一項簡單的 PHP 應用程式，您只需提供名稱與電子郵件地址就能註冊活動。先前的註冊者相關資訊會顯示在資料表中。註冊資訊會存放在 MySQL 資料庫。該應用程式包含一個檔案 (複製/貼上以下提供的程式碼)：

-   **index.php**：顯示註冊表單，以及內含註冊者資訊的資料表。

若要在本機建置與執行應用程式，請遵循下列步驟。請注意，這些步驟假設您具有 PHP、MySQL 命令列工具 (MySQL 的一部分)，以及一部在本機電腦上設定的 Web 伺服器，而且您已經啟用了 [MySQL 的 PDO 延伸功能][]。

1.  使用您先前擷取的 `Data Source`、`User Id`、`Password` 和 `Database` 值，連線到遠端 MySQL 伺服器：

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2.  MySQL 命令提示字元會顯示：

        mysql>

3.  貼上下列 `CREATE TABLE` 命令，以在您的資料庫中建立 `registration_tbl` 資料表：

        mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4.  在 Web 伺服器的根目錄中，建立名為 `registration` 的資料夾，然後在其中建立一個名為 `index.php` 的檔案。

5.  在文字編輯器或 IDE 中開啟 **index.php** 檔案並加入下列程式碼，然後完成具有 `//TODO:` 註解的必要變更。

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
            // DB connection info
            //TODO: Update the values for $host, $user, $pwd, and $db
            //using the values you retrieved earlier from the portal.
            $host = "value of Data Source";
            $user = "value of User Id";
            $pwd = "value of Password";
            $db = "value of Database";
            // Connect to database.
            try {
                $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
                $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            }
            catch(Exception $e){
                die(var_dump($e));
            }
            // Insert registration info
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
            // Retrieve data
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
        ?>
        </body>
        </html>

您現在可以瀏覽至 **<http://localhost/registration/index.php>** 並測試應用程式。

## 發行您的應用程式

當您在本機完成應用程式測試之後，可以使用 Git 將其發行至 Azure 網站。您將初始化本機 Git 儲存機制並發行該應用程式。

> [WACOM.NOTE]
> 這些步驟與上述「建立 Azure 網站」及「設定 Git 發行」小節末尾處的入口網站步驟相同。

1.  (選用) 如果您忘記或是錯置了 Git 遠端儲存機制 URL，請瀏覽至入口網站上的 [部署] 索引標籤。

    ![取得 Git URL][Git instructions]

2.  開啟 GitBash (如果 Git 位於您的 `PATH`，則為終端機)，將目錄變更為應用程式的根目錄，並執行以下命令：

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    系統會提示您輸入先前建立的密碼。

    ![透過 Git 初始發送至 Azure][]

3.  瀏覽至 **http://[site name].azurewebsites.net/index.php**，以開始使用該應用程式 (此項資訊會儲存在您的帳戶儀表板上)：

    ![Azure PHP web site][]

發行應用程式之後，您可以開始對其進行變更，並使用 Git 來發行它們。

## 將變更發行至您的應用程式

若要將變更發行至應用程式，請依照以下步驟進行：

1.  在本機對您的應用程式進行變更。
2.  開啟 GitBash (如果 Git 位於您的 `PATH`，則為終端機)，將目錄變更為應用程式的根目錄，並執行以下命令：

        git add .
        git commit -m "comment describing changes"
        git push azure master

    系統會提示您輸入先前建立的密碼。

    ![透過 Git 將網站變更發送至 Azure][]

3.  瀏覽至 **http://[site name].azurewebsites.net/index.php**，以查看您的應用程式以及您所做的任何變更：

    ![Azure PHP web site][]

4.  您也可以在 Azure 管理入口網站的 [部署] 索引標籤中看到新的部署項目：

    ![網站部署項目清單][]

  [PHP]: http://www.php.net/manual/en/install.php
  [Git]: http://git-scm.com/
  [Azure PHP web site]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
  [Azure 免費試用]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A74E0F923
  [Azure SDK for PHP]: http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids
  [Azure 管理入口網站]: https://manage.windowsazure.com
  [Create New Azure web site]: ./media/web-sites-php-mysql-deploy-use-git/new_website.jpg
  [Custom Create a new web site]: ./media/web-sites-php-mysql-deploy-use-git/custom_create.png
  [Fill in web site details]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
  [建立新的 MySQL 資料庫]: ./media/web-sites-php-mysql-deploy-use-git/new_mysql_db.jpg
  [Open web site dashboard]: ./media/web-sites-php-mysql-deploy-use-git/go_to_dashboard.png
  [Set up Git publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
  [Create publishing credentials]: ./media/web-sites-php-mysql-deploy-use-git/git-deployment-credentials.png
  [Git instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
  [取得資料庫連線資訊]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
  [MySQL 的 PDO 延伸功能]: http://www.php.net/manual/en/ref.pdo-mysql.php
  [透過 Git 初始發送至 Azure]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
  [透過 Git 將網站變更發送至 Azure]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
  [網站部署項目清單]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
