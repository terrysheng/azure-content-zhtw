<properties 
	pageTitle="採用資料表儲存體的 PHP 網站 - Azure 教學課程" 
	description="本教學課程示範如何建立 PHP 網站，以及在後端使用 Azure 資料表儲存體服務。" 
	services="web-sites, storage" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="tomfitz"/>

#使用 Azure 儲存體建立 PHP 網站

本教學課程示範如何建立 PHP 網站，以及在後端使用 Azure 資料表儲存體服務。本教學課程假定您的電腦已經安裝 [PHP][install-php] 與一部 Web 伺服器。本教學課程裡的說明可運用在包括 Windows、Mac 與 Linux 的任何作業系統上。完成本指南的步驟後，您將擁有一台在 Azure 上執行的 PHP 網站，並能夠存取資料表儲存體服務。
 
您將學到：

* 如何安裝 Azure 用戶端程式庫並將其納入您的應用程式。
* 如何使用用戶端程式庫來建立資料表，以及建立、查詢與刪除資料表實體。
* 如何建立 Azure 儲存體帳戶並設定您的應用程式來使用它。
* 如何建立 Azure 網站並使用 Git 來部署到該網站
 
您將在 PHP 中建立簡單的「工作清單」Web 應用程式。完成之應用程式的螢幕擷取畫面如下：

![Azure PHP web site][ws-storage-app]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##安裝 Azure 用戶端程式庫

若要透過 Composer 安裝 PHP Client Libraries for Azure，請依照下列步驟進行：

1. [安裝 Git][install-git]

	> [AZURE.NOTE]
	> 在 Windows 中，您也需要將 Git 可執行檔新增至 PATH 環境變數。

2. 在專案的根目錄中建立名為 **composer.json** 的檔案，並新增下列程式碼：

		{
			"require": {
				"microsoft/windowsazure": "*"
			},			
			"repositories": [
				{
					"type": "pear",
					"url": "http://pear.php.net"
				}
			],
			"minimum-stability": "dev"
		}

3. 下載 **[composer.phar][composer-phar]** 到專案根目錄中。

4. 開啟命令提示字元，在專案根目錄中執行此命令

		php composer.phar install

##開始使用用戶端程式庫

在您使用程式庫呼叫 Azure API 之前，必須先執行以下四個基本步驟。您將建立初始化指令碼以執行這些步驟。

* 建立名為 **init.php** 的檔案。

* 首先，加入自動換片器指令碼：

		require_once 'vendor\autoload.php'; 
	
* 加入您即將使用的命名空間。

	若要建立任何 Azure 服務用戶端，您必須使用 **ServicesBuilder** 類別：

		use WindowsAzure\Common\ServicesBuilder;

	若要捕捉任何一次 API 呼叫所產生的例外，您需要 **ServiceException** 類別：

		use WindowsAzure\Common\ServiceException;
	
* 若要具現化服務用戶端，您還需要有效的連接字串。資料表服務的連接字串格式為：

	用於存取即時服務：
	
		DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
	
	用於存取模擬器儲存體：
	
		UseDevelopmentStorage=true

* 使用  `ServicesBuilder::createTableService` 原廠方法在資料表服務呼叫周圍具現化包裝函式。

		$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	`$tableRestProxy` contains a method for every REST call available on Azure Tables.


## 建立資料表

儲存資料之前，您必須先建立資料容器，亦即資料表。 

* 建立名為 **createtable.php** 的檔案。

* 首先，加入您剛建立的初始化指令碼。您將在每一個存取 Azure 的檔案中加入此指令碼：

		<?php
		require_once "init.php";

* 接著，呼叫  *createTable* 以便傳入資料表名稱。就像其他 NoSQL 資料表儲存庫一樣，Azure 資料表不需要任何結構描述。
	
		try	{
			$tableRestProxy->createTable('tasks');
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		?>

	您可在此找到錯誤代碼與訊息掃描： [http://msdn.microsoft.com/library/windowsazure/dd179438.aspx][msdn-errors]


##查詢資料表

「工作清單」應用程式首頁會列出所有現有工作，並允許插入新的工作。

* 建立名為 **index.php** 的檔案，並插入構成頁面標頭的下列 HTML 與 PHP 程式碼：
	
		<html>
		<head>
			<title>Index</title>
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
		<h1>My ToDo List <font color="grey" size="5">(powered by PHP and Azure Tables) </font></h1>
		<?php		
		require_once "init.php";

* 若要查詢 Azure 資料表以瞭解  *tasks* 資料表中儲存的「所有實體」****，請呼叫  *queryEntities* 方法以便單純傳遞資料表名稱。在以下的「更新實體」****小節中，您將同時瞭解如何傳遞一項可查詢特定實體的篩選。

		try {
		    $result = $tableRestProxy->queryEntities('tasks');
		}
		catch(ServiceException $e){
		    $code = $e->getCode();
		    $error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		
* 逐一檢測結果集合中的實體：

		$entities = $result->getEntities();
			
		for ($i = 0; $i < count($entities); $i++) {

* 取得  `Entity` 之後，讀取資料的模型即為  `Entity->getPropertyValue('[name]')`：

			if ($i == 0) {
				echo "<table border='1'>
				<tr>
					<td>Name</td>
					<td>Category</td>
					<td>Date</td>
					<td>Mark Complete?</td>
					<td>Delete?</td>
				</tr>";
			}
			echo "
				<tr>
					<td>".$entities[$i]->getPropertyValue('name')."</td>
					<td>".$entities[$i]->getPropertyValue('category')."</td>
					<td>".$entities[$i]->getPropertyValue('date')."</td>";
					if ($entities[$i]->getPropertyValue('complete') == false)
						echo "<td><a href='markitem.php?complete=true&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Mark Complete</a></td>";
					else
						echo "<td><a href='markitem.php?complete=false&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Unmark Complete</a></td>";
					echo "
					<td><a href='deleteitem.php?pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Delete</a></td>
				</tr>";
		}
	
		if ($i > 0)
			echo "</table>";
		else
			echo "<h3>No items on list.</h3>";
		?>

* Last, you must insert the form that feeds data into the task insertion script and complete the HTML:

			<hr/>
			<form action="additem.php" method="post">
				<table border="1">
					<tr>
						<td>Item Name: </td>
						<td><input name="itemname" type="textbox"/></td>
					</tr>
					<tr>
						<td>Category: </td>
						<td><input name="category" type="textbox"/></td>
					</tr>
					<tr>
						<td>Date: </td>
						<td><input name="date" type="textbox"/></td>
					</tr>
				</table>
				<input type="submit" value="Add item"/>
			</form>
		</body>
		</html>

## 將實體插入資料表

您的應用程式現在可以讀取資料表上儲存的所有項目。由於一開始並沒有任何項目，因此我們可以新增可將資料寫入資料庫的函數。

* 建立名為 **additem.php** 的檔案。

* 將下列程式碼新增至檔案：

		<?php		
		require_once "init.php";		
		use WindowsAzure\Table\Models\Entity;
		use WindowsAzure\Table\Models\EdmType;		

* 插入實體的第一步，就是具現化  `Entity` 物件並在其上設定屬性：
		
		$entity = new Entity();
		$entity->setPartitionKey('p1');
		$entity->setRowKey((string) microtime(true));
		$entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
		$entity->addProperty('category', EdmType::STRING, $_POST['category']);
		$entity->addProperty('date', EdmType::STRING, $_POST['date']);
		$entity->addProperty('complete', EdmType::BOOLEAN, false);

* 接著您可以將剛建立的 `$entity` 傳遞至  `insertEntity` 方法：

		try{
			$tableRestProxy->insertEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* 最後，在插入實體後讓頁面返回首頁：

		header('Location: index.php');		
		?>
	
## 更新實體

工作清單應用程式能夠將項目標記為完成，也能取消標記。首頁會傳入實體的  *RowKey* 和  *PartitionKey* 與目標狀態 (marked==1, unmarked==0)。

* 建立名為 **markitem.php** 的檔案，並新增初始化部分：

		<?php		
		require_once "init.php";
		

* 更新實體的第一步是從資料表加以擷取：
		
		$result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq \''.$_GET['pk'].'\' and RowKey eq \''.$_GET['rk'].'\'');		
		$entities = $result->getEntities();		
		$entity = $entities[0];

	如您所見，傳入的查詢篩選形式為 `Key eq 'Value'`。查詢語法的完整描述可[在此][msdn-table-query-syntax]取得。

* 接著您可以變更任何屬性：

		$entity->setPropertyValue('complete', ($_GET['complete'] == 'true') ? true : false);

* 而且  `updateEntity` 方法會執行更新：

		try{
			$result = $tableRestProxy->updateEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* 在插入實體後讓頁面返回首頁：

		header('Location: index.php');		
		?>


## 刪除實體

單次呼叫  `deleteItem` 即可刪除項目。傳入值為 **PartitionKey** 與 **RowKey**，兩者共同構成了實體的主要金鑰。建立名為 **deleteitem.php** 的檔案，並插入下列程式碼：

		<?php
		
		require_once "init.php";		
		$tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);		
		header('Location: index.php');
		
		?>


## 建立 Azure 儲存體帳戶

若要讓您的應用程式將資料儲存至雲端，首先您需要在 Azure 建立儲存體帳戶，然後將適當的驗證資訊傳遞至  *Configuration* 類別。

1. 登入 [Azure 管理入口網站][management-portal]。

2. 按一下入口網站左下方的 [+ 新增]**** 圖示。

	![Create New Azure web site][new-website]

3. 依序按一下 [資料服務]****、[儲存體]**** 及 [快速建立]****。

	![Custom Create a new web site][storage-quick-create]
	
	輸入 **URL** 的值，然後在 [區域]**** 下拉式清單中為您的網站選取資料中心。按一下對話方塊底部的 [建立儲存體帳戶]**** 按鈕。

	![Fill in web site details][storage-quick-create-details]

	建立儲存體帳戶後，您會看到「建立儲存體帳戶[名稱]已順利完成****」的字樣。

4. 確保已經選取 [儲存體]**** 索引標籤，然後從清單中選取您剛建立的儲存體帳戶。

5. 從下方的應用程式列，按一下 [管理存取金鑰]****。

	![Select Manage Keys][storage-manage-keys]

6. 請記下您剛建立的儲存體帳戶名稱以及主要金鑰。

	![Select Manage Keys][storage-access-keys]

7. 開啟 **init.php** 並使用您在上個步驟記下的帳戶名稱與金鑰來取代 `[YOUR_STORAGE_ACCOUNT_NAME]` 與 `[YOUR_STORAGE_ACCOUNT_KEY]`。儲存檔案。


## 建立 Azure 網站並設定 Git 發行

遵循下列步驟以建立 Azure 網站：

1. 登入 [Azure 管理入口網站][management-portal]。
2. 按一下入口網站左下方的 [+ 新增]**** 圖示。

	![Create New Azure Web Site][new-website]

3. 依序按一下 [運算]****、[網站]**** 及 [快速建立]****。

	![Custom Create a new web site][website-quick-create]
	
	輸入 **URL** 的值，然後在 [區域]**** 下拉式清單中為您的網站選取資料中心。按一下對話方塊底部的 [建立新網站]**** 按鈕。

	![Fill in web site details][website-quick-create-details]

	建立網站後，您會看到「[網站名稱] 網站建立成功****」的字樣。現在，您可以啟用 Git 發行。

5. 按一下網站清單中顯示的網站名稱，以開啟該網站的 [快速入門]**** 儀表板。

	![Open web site dashboard][go-to-dashboard]


6. 在快速入門頁面右下方，選取 [從原始檔控制設定部署]****。

	![Set up Git publishing][setup-git-publishing]

6. 當系統詢問「您的原始程式碼在哪裡？」時，請選取 [本機 Git 儲存機制]****，然後按一下箭號。

	![where is your source code][where-is-code]

7. 若要啟用 Git 發行，您必須提供使用者名稱和密碼。記下您所建立的使用者名稱和密碼。(如果您之前設定過 Git 儲存機制，系統將會略過此步驟。)

	![Create publishing credentials][credentials]

	設定儲存機制需要幾秒鐘的時間。

8. 一旦 Git 儲存機制就緒，系統會顯示 Git 命令的使用指示，以便設定本機儲存機制並將檔案發送至 Azure。

	![Git deployment instructions returned after creating a repository for the website.][git-instructions]

	記下相關指示，以便在下一節中用來發行應用程式。

##發行您的應用程式

若要使用 Git 發行您的應用程式，請遵循下列步驟。

1. 開啟應用程式根目錄下方的 **vendor/microsoft/windowsazure** 資料夾，並刪除下列檔案與資料夾：
	* .git
	* .gitattributes
	* .gitignore
			
	當 Composer 封裝管理員下載 Azure 用戶端程式庫與其相依性時，它會複製其所在的 GitHub 儲存機制來達到這個目的。在下一個步驟中，Git 將於應用程式根目錄資料夾建立儲存機制，藉以部署該應用程式。除非已經移除儲存機制特定的檔案，否則 Git 會忽略用戶端程式庫所在的子儲存機制。

2. 開啟 GitBash (如果 Git 位於您的  `PATH`，則為終端機)，將目錄變更為您應用程式的根目錄，然後執行下列命令 (**注意：**這些步驟與＜建立 Azure 網站並設定 Git 發行＞****一節結尾處所述相同)：

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	系統會提示您輸入先前建立的密碼。

3. 瀏覽至 **http://[您的網站網域]/createtable.php**，為應用程式建立資料表。
4. 瀏覽至 **http://[您的網站網域]/index.php**，開始使用應用程式。

發行應用程式之後，您可以開始對其進行變更，並使用 Git 來發行它們。 

##將變更發行至您的應用程式

若要將變更發行至應用程式，請依照以下步驟進行：

1. 在本機對您的應用程式進行變更。
2. 開啟 GitBash (如果 Git 位於您的  `PATH`，則為終端機)，將目錄變更為應用程式的根目錄，並執行下列命令：

		git add .
		git commit -m "comment describing changes"
		git push azure master

	系統會提示您輸入先前建立的密碼。

3. 瀏覽至 **http://[您的網站網域]/index.php**，查看您的變更。 

[install-php]: http://www.php.net/manual/en/install.php


[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer-phar]: http://getcomposer.org/composer.phar

[msdn-errors]: http://msdn.microsoft.com/library/windowsazure/dd179438.aspx



[msdn-table-query-syntax]: http://msdn.microsoft.com/library/windowsazure/dd894031.aspx
[ws-storage-app]: ./media/web-sites-php-storage/ws-storage-app.png
[management-portal]: https://manage.windowsazure.com
[new-website]: ./media/web-sites-php-storage/new_website.jpg

[website-quick-create]: ./media/web-sites-php-storage/createsite.png
[website-quick-create-details]: ./media/web-sites-php-storage/sitedetails.png
[storage-quick-create]: ./media/web-sites-php-storage/createstorage.png
[storage-quick-create-details]: ./media/web-sites-php-storage/provideurl.png
[storage-manage-keys]: ./media/web-sites-php-storage/accesskeys.png
[storage-access-keys]: ./media/web-sites-php-storage/keydetails.png

[go-to-dashboard]: ./media/web-sites-php-storage/selectsite.png
[setup-git-publishing]: ./media/web-sites-php-storage/setup_git_publishing.png
[credentials]: ./media/web-sites-php-storage/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-storage/git-instructions.png
[where-is-code]: ./media/web-sites-php-storage/where_is_code.png


<!--HONumber=42-->
