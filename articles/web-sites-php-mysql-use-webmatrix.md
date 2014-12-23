<properties urlDisplayName="Web w/ WebMatrix" pageTitle="採用 MySQL 和 FTP 的 WebMatrix 網站 - Azure 教學課程" metaKeywords="" description="A tutorial that demonstrates how to use the free WebMatrix IDE to create and deploy a PHP website that stores data in MySQL." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create and deploy a PHP-MySQL Azure Website using WebMatrix" authors="cephalin" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />





#使用 WebMatrix 建立並部署 PHP-MySQL Azure 網站

本教學課程說明如何使用 WebMatrix 來開發 PHP-MySQL 應用程式並部署至 Azure 網站。WebMatrix 是 Microsoft 提供的免費 Web 開發工具，包含網站開發所需的一切。WebMatrix 支援 PHP 且包含 PHP 開發的 intellisense。

本教學課程假設您已在電腦上安裝 [MySQL][install-mysql]，以便在本機測試應用程式。不過，您也可以在沒有安裝 MySQL 的情況下完成本教學課程。取而代之，您可以將應用程式直接部署至 Azure 網站。

完成本指南的步驟後，您將擁有在 Azure 上運作的 PHP-MySQL 網站。
 
您將了解：

* 如何使用管理入口網站建立 Azure 網站和 MySQL 資料庫。由於預設會在 Azure 網站上啟用 PHP，因此您不需要執行任何特殊步驟就能執行 PHP 程式碼。
* 如何使用 WebMatrix 開發 PHP 應用程式。
* 如何使用 WebMatrix 將應用程式發行與重新發行到 Azure。
 
依照本教學課程進行，您將使用 PHP 建立一個簡易的「工作清單」Web 應用程式。該應用程式將在 Azure 網站中託管。以下螢幕擷取畫面顯示執行中的應用程式：

![Azure PHP Web Site][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##必要條件

1. [下載][tasklist-mysql-download]「工作清單」應用程式檔案。工作清單應用程式是一個簡單的 PHP 應用程式，可讓您在工作清單中加入、標示完成和刪除項目。工作清單項目會儲存在 MySQL 資料庫中。此應用程式包含下列檔案：

	* **additem.php**：將項目加入至清單。
	* **createtable.php**：為應用程式建立 MySQL 資料表。只會呼叫一次此檔案。
	* **deleteitem.php**：刪除項目。
	* **getitems.php**：取得資料庫中的所有項目。
	* **index.php**：顯示工作並提供表單將項目加入至清單。
	* **markitemcomplete.php**：將項目的狀態變更為完成。
	* **taskmodel.php**：包含函數讓您在資料庫中加入、取得、更新和刪除項目。

1.  建立名為 'tasklist` 的本機 MySQL 資料庫。您可以在 WebMatrix 中的資料庫工作區 (於下方教學課程中安裝後) 執行此步驟，或使用以下命令從 MySQL 命令提示字元中執行：

		mysql> create database tasklist;

	只有當您想要在本機測試應用程式時，才需要執行此步驟。

<h2><a id="CreateWebsite"></a>建立 Azure 網站和 MySQL 資料庫</h2>

1. 登入[管理入口網站][preview-portal]。
1. 按一下入口網站左下方的 [**+ 新增**]。

	![Create New Azure Web Site][NewWebSite1]

1. 按一下 [**網站**]，然後按一下 [**自訂建立**]。

	![Custom Create a new Web Site][NewWebSite2]

	> [WACOM.NOTE]
	> 建立網站後，您就不能建立此網站的 MySQL 資料庫。您必須依照下列步驟說明，建立網站和 MySQL 資料庫。

1. 	輸入 **URL** 的值，然後從 [**資料庫**] 下拉式清單中選取 [**建立新的 MySQL 資料庫**]，接著在 [**區域**] 下拉式清單中為您的網站選取資料中心。按一下對話方塊底部的箭頭。

	![Fill in web site details][NewWebSite3]

5. 輸入資料庫的 [**名稱**] 值，然後在 [**區域**] 下拉式清單中選取資料庫的資料中心，接著核取代表您同意法律條款的方塊。按一下對話方塊底部的核取記號。

	![Create new MySQL database][NewWebSite4]

	建立網站後，您會看到「**建立網站 '[SITENAME]' 成功**」的字樣。

	接下來，您需要取得 MySQL 連接資訊。


6. 按一下網站清單中顯示的網站名稱，以開啟該網站的 [快速入門] 頁面。

	![Open web site dashboard][NewWebSite5]

7. 按一下 [**設定**] 索引標籤：

	![Configure tab][NewWebSite6]

8. 向下捲動到 [**連接字串**] 區段。`Database`、`Data Source`、`User Id` 和 `Password` 的值 (分別) 是資料庫名稱、伺服器名稱、使用者名稱和使用者密碼。請記下資料庫連結資訊，稍後將會用到。

	![Connection string][ConnectionString]

##安裝 WebMatrix 和開發應用程式

您可以從[管理入口網站][preview-portal]安裝 WebMatrix。 

1. 登入之後，瀏覽至網站的 [快速入門] 頁面，按一下頁面底部的 WebMatrix 圖示：

	![Install WebMatrix][InstallWebMatrix]

	依照提示來安裝 WebMatrix。

2. 安裝 WebMatrix 之後，將嘗試以 WebMatrix 專案來開啟您的網站。您可以選擇直接編輯即時網站，或下載本機複本。在本教學課程中，請選取 [Edit local copy]。 

3. 提示您下載網站時，請選擇 [**Yes, install from the Template Gallery**]。

	![Download web site][download-site]

4. 從可用的範本中，選擇 [**PHP**]。

	![Site from template][site-from-template]

5. 選取 [**空白網站**] 範本。提供網站的名稱，然後按 [**下一個**]。

	![Provide name for site][site-from-template-2]

	您的網站將在 WebMatrix 中開啟，且備妥一些預設檔案。

	接下來幾個步驟，您將會加入稍早下載的檔案並做一些修改，以開發「工作清單」應用程式。不過，可以加入您自己現有的檔案或建立新的檔案。

6. 按一下 [**新增現有的**] 來新增應用程式檔案：

	![WebMatrix - Add existing files][edit_addexisting]

	在結果對話方塊中，瀏覽至您稍早下載的檔案，全部選取，然後按一下 [開啟舊檔]。出現提示時，選擇取代 `index.php` 檔案。 

7. 接下來，您需要將本機 MySQL 資料庫連接資訊加入至 `taskmodel.php` 檔案。按兩下以開啟 `taskmodel.php` 檔案，然後在 `connect` 函數中更新資料庫連接資訊。(**注意**：如果不要在本機測試應用程式，而想要直接發行至 Azure 網站，請跳至[發行應用程式](#Publish)。)

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	儲存 'taskmodel.php' 檔案。

8. 對於要執行的應用程式，需要建立 `items` 資料表。以滑鼠右鍵按一下 `createtable.php` 檔案，然後選取 [**在瀏覽器中啟動**]。這樣會在瀏覽器中啟動 `createtable.php`，並執行程式碼在 `tasklist 資料庫中建立 `items` 資料表。

	![WebMatrix - Launch createtable.php in browser][edit_run]

9. 現在，您可以在本機測試應用程式。以滑鼠右鍵按一下 `index.php` 檔案，然後選取 [**在瀏覽器中啟動**]。請開始測試應用程式，包括加入項目、將項目標示為完成，然後刪除項目。  


<h2><a id="Publish"></a>發行您的應用程式</h2>

將應用程式發行至 Azure 網站之前，`taskmodel.php` 中的資料庫連接資訊需要更新為您稍早取得的連接資訊 (在＜[建立 Azure 網站和 MySQL 資料庫](#CreateWebsite)＞這一節中）。

1. 按兩下以開啟 `taskmodel.php` 檔案，然後在 `connect` 函數中更新資料庫連接資訊。

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	儲存 'taskmodel.php' 檔案。

2. 按一下 WebMatrix 中的 [**發佈**]，然後按一下 [**發佈預覽**] 對話方塊中的 [**繼續**]。

	![WebMatrix - Publish][edit_publish]

3. 瀏覽至 http://[您的網站名稱].azurewebsites.net/createtable.php 來建立 `items` 資料表。

4. 最後，瀏覽至 http://[您的網站名稱].azurewebsites.net/index.php 來使用應用程式。
	
##修改和重新發行應用程式

您可以編輯稍早下載的網站本機複本，輕鬆地修改應用程式，然後重新發行，或者，您也可以在「遠端」模式下直接編輯。在此，您將在 `index.php` 檔案中簡單地變更標題，並直接儲存至即時網站。

1. 在 WebMatrix 中按一下 [遠端] 索引標籤，然後選取 [**開啟遠端檢視**]。這樣會開啟遠端網站來直接編輯。
	 ![WebMatrix - Open Remote View][OpenRemoteView]
 
2. 按兩下以開啟 'index.php' 檔案。
	![WebMatrix - Open index file][Remote_editIndex]

3. 在 **title** 和 **h1** 標記中，將 **My ToDo List** 變更為 **My Task List**，然後儲存檔案。


4. 儲存完成時，按一下 [執行] 按鈕，在即時網站上查看變更結果。
	![WebMatrix - Launch site in Remote][Remote_run]


# 後續步驟

您已了解如何建立網站並從 WebMatrix 部署至 Azure。若要深入了解 WebMatrix，請參考下列資源：

* [WebMatrix for Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [WebMatrix 網站](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[running-app]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
[tasklist-mysql-download]: http://go.microsoft.com/fwlink/?LinkId=252506
[NewWebSite1]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
[NewWebSite3]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
[NewWebSite4]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
[NewWebSite5]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
[NewWebSite6]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
[ConnectionString]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
[InstallWebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png













[preview-portal]: https://manage.windowsazure.com













