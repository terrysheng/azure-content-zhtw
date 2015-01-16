<properties urlDisplayName="Web w/ MySQL + FTP" pageTitle="採用 MySQL 和 FTP 的 PHP 網站 - Azure 教學課程" metaKeywords="" description="示範如何建立 PHP 網站將資料儲存於 MySQL 並對於 Azure 使用 FTP 部署的教學課程。" metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure Website and Deploy Using FTP" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/14/2014" ms.author="tomfitz" />


#建立 PHP-MySQL Azure 網站並使用 FTP 部署

本教學課程說明如何建立 PHP-MySQL Azure 網站，以及如何使用 FTP 部署該網站。本教學課程假定您的電腦已經安裝 [PHP][install-php]、[MySQL][install-mysql]、一台 Web 伺服器，以及一個 FTP 用戶端。本教學課程裡的說明可運用在包括 Windows、Mac 與 Linux 的任何作業系統上。完成本指南的步驟後，您將擁有在 Azure 上運作的 PHP/MySQL 網站。
 
您將了解：

* 如何使用 Azure 管理入口網站建立 Azure 網站和 MySQL 資料庫。由於預設會在 Azure 網站上啟用 PHP，因此您不需要執行任何特殊步驟就能執行 PHP 程式碼。
* 如何使用 FTP 將應用程式發行到 Azure。
 
依照本教學課程進行，您將使用 PHP 建置一個簡易的註冊網頁應用程式。該應用程式將在 Azure 網站中託管。完成之應用程式的螢幕擷取畫面如下：

![Azure PHP Web Site][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##建立 Azure 網站並設定 FTP 發行

請依照下列步驟來建立 Azure 網站和 MySQL 資料庫：

1. 登入 [Azure 管理入口網站][management-portal]。
2. 按一下入口網站左下方的 [**+ 新增**] 圖示。

	![Create New Azure Web Site][new-website]

3. 按一下 [**網站**]，然後按一下 [**自訂建立**]。

	![Custom Create a new Web Site][custom-create]
	
	輸入 **URL** 的值，然後從 [**資料庫**] 下拉式清單中選取 [**建立新的 MySQL 資料庫**]，接著在 [**區域**] ] 下拉式清單中為您的網站選取資料中心。按一下對話方塊底部的箭頭。

	![Fill in Web Site details][website-details]

4. 為您的資料庫輸入 [**名稱**] 值，然後在 [**區域**] 下拉式清單中為您的資料庫選取資料中心，接著核取代表您同意法律條款的方塊。按一下對話方塊底部的核取記號。

	![Create new MySQL database][new-mysql-db]

	建立網站後，您會看到「**建立網站 '[SITENAME]' 成功」**的字樣。現在，您可以啟用 FTP 發行功能。

5. 按一下網站清單中顯示的網站名稱，以開啟該網站的 [**快速入門**] 儀表板。

	![Open web site dashboard][go-to-dashboard]


6. 在 [**QUICKSTART**] 頁面下方，按一下 [**Reset deployment credentials**]。 

	![Reset deployment credentials][reset-deployment-credentials]

7. 若要啟用 FTP 發行，您必須提供使用者名稱與密碼。記下您所建立的使用者名稱和密碼。

	![Create publishing credentials][portal-git-username-password]

##在本機建置與測試您的應用程式

註冊應用程式是一項簡單的 PHP 應用程式，您只需提供名稱與電子郵件地址就能註冊活動。先前的註冊者相關資訊會顯示在資料表中。註冊資訊會存放在 MySQL 資料庫。該應用程式包含兩個檔案：

* **index.php**:顯示註冊表單，以及內含註冊者資訊的資料表。
* **createtable.php**:為應用程式建立 MySQL 資料表。只會使用一次此檔案。

若要在本機建置與執行應用程式，請遵循下列步驟。請注意，這些步驟假定您已在本機電腦上安裝 PHP、MySQL 以及 Web 伺服器，而且您已經啟用了[MySQL 的 PDO 延伸功能][pdo-mysql]。

1. 建立名為 'registration' 的 MySQL 資料庫。您可以使用以下命令，從 MySQL 命令提示字元中完成此工作：

		mysql> create database registration;

2. 在 Web 伺服器的根目錄中，建立名為 'registration' 的資料夾，並於其中建立兩個檔案，一個名為 'createtable.php'，另一個名為 'index.php'。

3. 在文字編輯器或 IDE 中開啟 'createtable.php' 檔案，加入下列程式碼。此程式碼將會在 'registration' 資料庫中建立 'registration_tbl'。

		<?php
		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
						id INT NOT NULL AUTO_INCREMENT, 
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

	> [WACOM.NOTE] 
	> 您需要將 <code>$user</code> 和 <code>$pwd</code> 的值，更新為您的本機 MySQL 使用者名稱和密碼。

4. 開啟網頁瀏覽器並瀏覽至 [http://localhost/registration/createtable.php][localhost-createtable]。這會在資料庫中建立 'registration_tbl' 資料表。

5. 在文字編輯器或 IDE 中開啟 **index.php** 檔案，加入頁面的基本 HTML 和 CSS 程式碼 (稍後的步驟中將加入 PHP 程式碼)。

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

6. 在 PHP 標記內， add PHP code for connecting to the database.

		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

	> [WACOM.NOTE]
	> 同樣地，您需要將 <code>$user</code> 和 <code>$pwd</code> 的值，更新為您的本機 MySQL 使用者名稱和密碼。

7. 在資料庫連接程式碼後面，加入可將登錄資訊插入至資料庫的程式碼。

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

8. 最後，在上述程式碼後面，加入可從資料庫擷取資料的程式碼。

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

您現在可以瀏覽至 [http://localhost/registration/index.php][localhost-index] 測試應用程式。

##取得 MySQL 與 FTP 連線資訊

若要連線至在 Azure 網站中執行的 MySQL 資料庫，您需要連線資訊。若要取得 MySQL 連線資訊，請依照以下步驟執行：

1. 從網站的儀表板上，按一下頁面右側的 [**檢視連接字串**] 連結：

	![Get database connection information][connection-string-info]
	
2. 記下 'Database'、'Data Source'、'User Id' 與 'Password' 的值。

3. 從網站的儀表板上，按一下頁面右下方的 [**下載發行設定檔**] 連結：

	![Download publish profile][download-publish-profile]

4. 在 XML 編輯器中開啟 '.publishsettings' 檔案。 

3. 找到帶有類似以下內容之 `publishMethod="FTP"` 程式碼的 `<publishProfile >` 元素：

		<publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
			...
		</publishProfile>
	
記下 `publishUrl`、`userName` 與 `userPWD` 屬性。

##發行您的應用程式

當您在本機完成應用程式測試之後，可以使用 FTP 將其發行至 Azure 網站。不過，您必須先更新應用程式中的資料庫連接資訊。使用您先前取得的資料庫連線資訊 (在 [**取得 MySQL 與 FTP 連線資訊** 區段)，使用適當的值**同時**更新 `createdatabase.php` 和 `index.php` 檔案中的下列資訊：

	// DB connection info
	$host = "value of Data Source";
	$user = "value of User Id";
	$pwd = "value of Password";
	$db = "value of Database";

現在您可以使用 FTP 發行您的應用程式。

1. 開啟您選擇的 FTP 用戶端。

2. 將您在以上步驟中從 `publishUrl` 屬性記下的*主機名稱* 部分輸入到您的 FTP 用戶端。

3. 將您在以上步驟中記下的 `userName` 與 `userPWD` 屬性，原封不動地輸入到您的 FTP 用戶端。

4. 建立連線。

連線建立之後，您就能視需要上傳與下載檔案。務必將檔案上傳至根目錄，亦即 `/site/wwwroot`。

同時將 `index.php` 和 `createtable.php` 上傳完畢後，瀏覽至 **http://[site name].azurewebsites.net/createtable.php** 為應用程式建立 MySQL 資料表，然後瀏覽至 **http://[site name].azurewebsites.net/index.php** 開始使用該應用程式。
 

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_website.jpg
[custom-create]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/custom_create.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/go_to_dashboard.png
[reset-deployment-credentials]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/reset-deployment-credentials.png
[portal-git-username-password]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/git-deployment-credentials.png


[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://manage.windowsazure.com
[download-publish-profile]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/download_publish_profile_2.png

<!--HONumber=35.1-->
