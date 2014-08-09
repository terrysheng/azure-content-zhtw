<properties linkid="develop-nodejs-tutorials-web-site-with-sql-database" urlDisplayName="Web site with SQL Database" pageTitle="Node.js web site with SQL Database - Azure tutorial" metaKeywords="" description="Learn how to create a Node.js website that accesses a SQL Database and is deployed to Azure" metaCanonical="" services="web-sites,sql-database" documentationCenter="Node.js" title="Node.js Web Application using the Azure SQL Database" authors="" solutions="" manager="" editor="" />

使用 Azure SQL Database 的 Node.js Web 應用程式
===============================================

本教學課程將示範如何使用 Azure 資料管理所提供的 SQL Database，進而從 Azure 上所代管的 [node](http://nodejs.org) 應用程式來儲存與存取資料。本教學課程假設您先前有使用節點和 [Git](http://git-scm.com) 的些許經驗。

您將了解：

-   如何使用 Azure 管理入口網站來建立 Azure 網站和 SQL Database

-   如何使用 npm (節點封裝管理員) 來安裝節點模組

-   如何使用 node-sqlserver 模組來與 SQL Database 搭配使用

-   如何使用應用程式設定來指定應用程式的執行階段值

透過依照本教學課程的步驟進行，您將建置一個可讓您建立、擷取和完成工作的簡單 Web 架構工作管理應用程式。這些工作會儲存在 SQL Database 中。

本教學課程的專案檔案將會儲存在名為 **tasklist** 的目錄中，而已完成的應用程式看起來會與下圖類似：

![A web page displaying an empty tasklist](./media/sql-database-nodejs-use-web-site/sql_todo_final.png)

**注意**

本教學課程中所使用的 Microsoft Driver for Node.JS for SQL Server 目前提供預覽版本，且需要使用只能在 Microsoft Windows 和 Azure 作業系統上使用的執行階段元件。

**注意**

本教學課程會建立 **tasklist** 資料夾的參照。因為路徑語意在不同作業系統之間會有所差異，此資料夾的完整路徑將會被省略。建立此資料夾的理想位置應該在本機檔案系統上您可輕易存取的位置，例如 **\~/node/tasklist** 或 **c:\\node\\tasklist**

**注意**

下述的許多步驟使用命令列。在這些步驟中，請使用適用於您作業系統的命令列，例如 **cmd.exe** (Windows) 或 **Bash** (Unix Shell)。在 OS X 系統上，您可以透過 [終端機] 應用程式來存取命令列。

必要條件
--------

在依照本文中的指示進行之前，您應確定已安裝下列項目：

-   [node](http://nodejs.org) 版本 0.6.14 或更高版本

-   [Git](http://git-scm.com)

-   Microsoft SQL Server Native Client 程式庫 - 作為 [Microsoft SQL Server 2012 功能套件](http://www.microsoft.com/en-us/download/details.aspx?id=29065)的一部分提供

-   文字編輯器

-   網頁瀏覽器

建立網站和資料庫
----------------

請依照下列步驟來建立 Azure 網站和 SQL Database：

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)。
2.  按一下入口網站左下方的 [+ 新增] 圖示****。

    ![Create New Azure Website](./media/sql-database-nodejs-use-web-site/new_website.jpg)

3.  依序按一下 **[網站]** 及 **[自訂建立]**。

    ![Custom Create a new Website](./media/sql-database-nodejs-use-web-site/custom_create.png)

    在 **[URL]** 中輸入值，從 **[資料庫]** 下拉式清單中選取 **[Create a New SQL Database]**，然後在**[區域]** 下拉式清單中為您的網站選取資料中心。按一下對話方塊底部的箭頭。

    ![填入網站詳細資料](./media/sql-database-nodejs-use-web-site/website_details_sqlazure.jpg)

4.  在資料庫的 **[名稱]** 中輸入值，依序選取 **[版本]** [(WEB 或 BUSINESS)](http://msdn.microsoft.com/zh-tw/library/windowsazure/ee621788.aspx)、資料庫的 **[最大大小]**，並選擇 **[定序]**，然後選取 **[NEW SQL Database server]**。按一下對話方塊底部的箭頭。

    ![Fill in SQL Database settings](./media/sql-database-nodejs-use-web-site/database_settings.jpg)

5.  輸入系統管理員名稱和密碼 (及確認密碼)，選擇將要建立新 SQL Database 伺服器的所在區域，然後勾選 **[Allow Azure Services to access the server]** 方塊。

    ![Create new SQL Database server](./media/sql-database-nodejs-use-web-site/create_server.jpg)

    建立網站後，您會看到 [Creation of Web Site '[SITENAME]' completed successfully]**** 的字樣。現在，您可以啟用 Git 發佈。

6.  按一下顯示在網站清單中的網站名稱，以開啟網站的 [快速啟動] 儀表板。

    ![Open website dashboard](./media/sql-database-nodejs-use-web-site/go_to_dashboard.png)

7.  按一下 [快速啟動] 頁面底部的 [Set up Git publishing] ****。

    ![Set up Git publishing](./media/sql-database-nodejs-use-web-site/setup_git_publishing.png)

8.  若要啟用 Git 發佈，您必須提供使用者名稱和密碼。記下您所建立的使用者名稱和密碼。(如果您之前設定過 Git 儲存機制，系統將會略過此步驟。)

    ![Create publishing credentials](./media/sql-database-nodejs-use-web-site/git-deployment-credentials.png)

    設定儲存機制需要幾秒鐘的時間。

9.  當您的儲存機制準備就緒時，您會看到將應用程式檔案發佈至該儲存機制的指示。請記下這些指示，以供稍後使用。

    ![Git instructions](./media/sql-database-nodejs-use-web-site/git-instructions.png)

取得 SQL Database 連接資訊
--------------------------

若要連線到正在 Azure 網站上執行的 SQL Database 執行個體，您將需要連接資訊。若要取得 SQL Database 連接資訊，請依照下列步驟進行：

1.  在 Azure 管理入口網站中，按一下 **[LINKED RESOURCES]**，然後按一下資料庫名稱。

    ![Linked Resources](./media/sql-database-nodejs-use-web-site/linked_resources.jpg)

2.  按一下 **[View connection strings]**。

    ![Connection string](./media/sql-database-nodejs-use-web-site/connection_string.jpg)

3.  從產生的對話方塊的 **[ODBC]** 區段中，記下連接字串以供稍後使用。

設計工作資料表
--------------

若要建立用來儲存 tasklist 應用程式項目的資料庫資料表，請執行下列步驟：

1.  在 Azure 管理入口網站中，選取您的 SQL Database 然後按一下頁面底部的 **[管理]**。如果您收到指出「目前 IP 不是防火牆規則的一部分」的訊息，請選取 **[確定]** 以新增此 IP 位址。

    ![manage button](./media/sql-database-nodejs-use-web-site/sql-manage.png)

2.  使用稍早建立資料庫伺服器時所選取的登入名稱和密碼登入。

    ![database manage login](./media/sql-database-nodejs-use-web-site/sqlazurelogin.png)

3.  選取頁面左下方的 **[設計]**，然後選取 **[新增資料表]**。

    ![New table](./media/sql-database-nodejs-use-web-site/new-table.png)

4.  輸入 'tasks' 作為 **[資料表名稱]**，並勾選 **[識別碼]** 欄中的 **[為識別?]**。

    ![table name set to tasks and is identity checked](./media/sql-database-nodejs-use-web-site/table-name-identity.png)

5.  將 **[資料行1]** 變更為 **[名稱]** 並將 **[資料行2]** 變更為 **[類別]**。透過按一下 **[加入資料行]** 按鈕來新增兩個資料行。第一個新增資料行的名稱應該是 **[建立時間]** 且類型為 **[日期]**。第二個新增資料行的名稱應該是 **[已完成]** 且類型為 **[位元]**。這兩個新增資料行都應標示為 **[必要的?]**。

    ![completed table design](./media/sql-database-nodejs-use-web-site/table-columns.png)

6.  按一下 **[儲存]** 按鈕以儲存資料表的變更。您現在可以關閉 SQL Database 管理頁面。

安裝模組與產生樣板
------------------

在本節中，您將建立新的 Node 應用程式，並使用 npm 來新增模組封裝。若是工作清單應用程式，您將會用到 [express](http://expressjs.com) 和 [node-sqlserver](https://github.com/WindowsAzure/node-sqlserver) 模組。Express 模組可提供節點的模型檢視控制器架構，而 node-sqlserver 模組可提供與 Azure SQL Database 的連線能力。

### 安裝 Express 與產生樣板

1.  從命令列，切換至 **tasklist** 目錄。如果 **tasklist** 目錄不存在，請建立此目錄。

2.  輸入下列命令以安裝 Express。

         npm install express -g

    **注意**

    在部分作業系統上使用 '-g' 參數時，您可能會收到 **[Error:EPERM, chmod '/usr/local/bin/express']** 錯誤訊息，以及嘗試以系統管理員執行帳戶的要求。如果發生這個情形，請使用 **sudo** 命令以較高的權限等級來執行 npm。

    此命令的輸出應顯示如下：

        express@2.5.9 /usr/local/lib/node_modules/express
		├── mime@1.2.4
		├── mkdirp@0.3.0
		├── qs@0.4.2
		└── connect@1.8.7

    **注意**

    安裝 Express 模組時使用的 '-g' 參數會全域安裝 Express 模組。這樣做的原因是方便我們可以存取 **express** 命令以產生網站樣板，而無需輸入額外的路徑資訊。

3.  若要建立將用於此應用程式的樣板，請使用 **express** 命令：

         express

    此命令的輸出應顯示如下：

      	    create : .
            create :./package.json
            create :./app.js
            create :./public
            create :./public/javascripts
            create :./public/images
            create :./public/stylesheets
            create :./public/stylesheets/style.css
            create :./routes
            create :./routes/index.js
            create :./views
            create :./views/layout.jade
            create :./views/index.jade
            
            dont forget to install dependencies:
            $ cd .&& npm install

    完成此命令之後，您的 **tasklist** 目錄中應會有數個新目錄和檔案。

### 安裝其他模組

1.  從命令列，切換至 **tasklist** 資料夾，並輸入下列命令以安裝 **package.json** 檔案中所述的模組：

         npm install

    此命令的輸出應顯示如下：

        express@2.5.8 ./node_modules/express
		├── mime@1.2.4
		├── qs@0.4.2
		├── mkdirp@0.3.0
		└── connect@1.8.7
		jade@0.26.0 ./node_modules/jade
		├── commander@0.5.2
		└── mkdirp@0.3.0

    這會安裝 Express 需要的所有預設模組。

2.  接下來，使用下列命令來新增 nconf 模組。應用程式將使用此模組來讀取組態檔中的資料庫連接字串。

    npm install nconf -save

3.  接下來，從[下載中心](http://www.microsoft.com/en-us/download/details.aspx?id=29995)中下載二進位版本的 Microsoft Driver for Node.JS for SQL Server。

4.  將此封存解壓縮到 **tasklist\\node\_modules** 目錄。

5.  在 **tasklist\\node\_modules** 目錄中執行 **msnodesql-install.cmd** 檔案。這會在 **node\_modules** 下建立 **msnodesql** 子目錄，並將驅動程式檔案移入此新目錄結構。

6.  我們將不會再用到 **msnodesql-install.cmd** 檔案，請將它刪除。

在節點應用程式中使用 SQL Database
---------------------------------

在本節中，您將透過修改現有的 **app.js** 來將 **express** 命令所建立的基本應用程式進行延伸，並建立新的 **index.js** 檔案以便使用稍早建立的資料庫。

### 修改控制器

1.  在 **tasklist/routes** 目錄中，以文字編輯器開啟 **index.js** 檔案。

2.  在 **index.js** 檔案中，以下列程式碼取代現有的程式碼。這會載入 msnodesql 和 nconf 模組，然後使用 nconf 從名為 **SQL\_CONN** 的環境變數或 **config.json** 檔案中的 **SQL\_CONN** 值載入連接字串。

        var sql = require('msnodesql')
		    , nconf = require('nconf');

		nconf.env()
	         .file({ file: 'config.json' });
		var conn = nconf.get("SQL_CONN");

3.  透過新增 **index** 和 **updateItem** 方法來繼續新增 **index.js** 檔案。**index** 方法會傳回資料庫中所有未完成的工作，同時 **updateItem** 會將選取的工作標示為已完成。

		exports.index = function(req, res) {
		    var select = "select * from tasks where completed = 0";
		    sql.query(conn, select, function(err, items) {
		        if(err)
		            throw err;
		        res.render('index', { title: 'My ToDo List ', tasks: items });
		    });
		};

		exports.updateItem = function(req, res) {
		    var item = req.body.item;
		    if(item) {
		        var insert = "insert into tasks (name, category, created, completed) values (?, ?, GETDATE(), 0)";
		        sql.query(conn, insert, [item.name, item.category], function(err) {
		            if(err)
		                throw err;
		            res.redirect('/');
		        });
		    } else {
		        var completed = req.body.completed;
		        if(!completed.forEach)
		            completed = [completed];
		        var update = "update tasks set completed = 1 where id in (" + completed.join(",") + ")";
		        sql.query(conn, update, function(err) {
		            if(err)
		                throw err;
		            res.redirect('/');
		        });
		    }
		}

4.  儲存 **index.js** 檔案。

### 修改 app.js

1.  在 **tasklist** 目錄中，以文字編輯器開啟 **app.js** 檔案。這是稍早執行 **express** 命令所建立的檔案。

2.  在 app.js 檔案中，向下捲動到您可以看到以下程式碼的位置。

         app.configure('development', function(){
	     app.use(express.errorHandler());
         });

3.  現在插入下列程式碼。

         app.get('/', routes.index);
         app.post('/', routes.updateItem);

這會在您先前加入 **index.js** 檔案的 **updateItem** 方法中新增路由。

1.  儲存 **app.js** 檔案。

### 修改索引檢視表

1.  切換至 **views** 目錄，以文字編輯器開啟 **index.jade** 檔案。

2.  以下列程式碼取代 **index.jade** 檔案的內容。這會定義顯示現有工作的檢視表，並定義新增工作與將現有工作標示為已完成的表單。

		h1= title
		br

		form(action="/", method="post")
		  table(class="table table-striped table-bordered")
		    thead
		      tr
		        td Name
		        td Category
		        td Date
		        td Complete
		    tbody
		    each task in tasks
		      tr
		        td #{task.name}
		        td #{task.category}
		        td #{task.created}
		        td
		          input(type="checkbox", name="completed", value="#{task.ID}", checked=task.completed == 1)
		  button(type="submit", class="btn") Update tasks
		hr

		form(action="/", method="post", class="well")
		  label Item Name:
		  input(name="item[name]", type="textbox")
		  label Item Category:
		  input(name="item[category]", type="textbox")
		  br
		  button(type="submit", class="btn") Add Item

3.  儲存並關閉 **index.jade** 檔案。

### 修改全域配置

**views** 目錄中的 **layout.jade** 檔案可作為其他 **.jade** 檔案的全域範本使用。在此步驟中，您將修改此檔案以使用 [Twitter Bootstrap](https://github.com/twbs/bootstrap)，Twitter Bootstrap 是可讓您輕鬆設計美觀網站的工具組。

1.  下載並解壓縮 [Twitter Bootstrap](http://getbootstrap.com/) 的檔案。將 **bootstrap.min.css** 檔案從 **bootstrap\\css** 資料夾複製到您工作清單應用程式的 **public\\stylesheets** 目錄。

2.  在 **views** 資料夾中，以文字編輯器開啟 **layout.jade**，並以下列程式碼取代其內容：

		!!!html
		html
		  head
		    title= title
		    meta(http-equiv='X-UA-Compatible', content='IE=10')
		    link(rel='stylesheet', href='/stylesheets/style.css')
		    link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
		  body(class='app')
		    div(class='navbar navbar-fixed-top')
		      .navbar-inner
		        .container
		          a(class='brand', href='/') My Tasks
		    .container!= body

3.  儲存 **layout.jade** 檔案。

### 建立組態檔

**config.json** 檔案包含用來連接到 SQL Database 的連接字串，並會在執行階段時由 **index.js** 檔案讀取。若要建立此檔案，請執行下列步驟：

1.  在 **tasklist** 目錄中建立名為 **config.json** 的新檔案，並以文字編輯器開啟。

2.  **config.json** 檔案的內容應顯示如下：

         {
        "SQL_CONN" :"connection_string"
         }

    以稍早傳回的 ODBC 連接字串值取代 **connection\_string**。

3.  儲存檔案。

在本機執行您的應用程式
----------------------

若要測試本機機器上的應用程式，請執行下列步驟：

1.  從命令列，切換至 **tasklist** 目錄。

2.  使用下列命令在本機上啟動應用程式：

         node app.js

3.  開啟網頁瀏覽器並瀏覽到 http://127.0.0.1:3000。這應會顯示如下的網頁：

    ![A webpage displaying an empty tasklist](./media/sql-database-nodejs-use-web-site/sql_todo_empty.png)

4.  使用所提供的 **[項目名稱]** 和 **[項目類別]** 欄位來輸入資訊，然後按一下 **[新增項目]**。

5.  系統應更新此頁面以在 [ToDo] 清單中顯示此項目。

    ![An image of the new item in the list of tasks](./media/sql-database-nodejs-use-web-site/sql_todo_list.png)

6.  若要完成工作，您只需勾選 [已完成] 資料行中的核取方塊，然後按一下 **[更新工作]**。

7.  若要停止節點程序，請移至命令列並按 **CTRL** 和 **C** 鍵。

將您的應用程式部署至 Azure
--------------------------

在本節中，您將使用建立網站之後所收到的部署步驟，將您的應用程式發佈到 Azure。

### 發佈應用程式

1.  在命令列中，切換至 **tasklist** 目錄 (如果您尚未在此目錄下)。

2.  使用下列命令來初始化應用程式的本機 git 儲存機制、在此儲存機制中新增應用程式檔案，最後將這些檔案發佈至 Azure

        git init
        git add .
        git commit -m "adding files"
        git remote add azure [URL for remote repository]
        git push azure master

    在部署結束時，您應該會看到如下所示的陳述式：

         To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
	     \* [new branch] 	master -\> master

3.  在發送作業完成之後，請瀏覽到 **http://[site name].azurewebsites.net/** 以檢視您的應用程式。

### 切換至環境變數

稍早我們實作了程式碼，來尋找連接字串的 **SQL\_CONN** 環境變數或從 **config.json** 檔案中載入值。在下列步驟中，您將在網站組態中，建立應用程式透過環境變數真正存取的機碼值組。

1.  在 Azure 管理入口網站中，按一下 **[網站]**，然後選取您的網站。

    ![Open website dashboard](./media/sql-database-nodejs-use-web-site/go_to_dashboard.png)

2.  按一下 **[設定]**，然後尋找頁面中的 **[應用程式設定]** 區段。

    ![configure link](./media/sql-database-nodejs-use-web-site/sql-task-configure.png)

3.  在 **[應用程式設定]** 區段中，在 **[機碼]** 欄位中輸入 **SQL\_CONN**，並在 **[值]** 欄位中輸入 ODBC 連接字串。最後，按一下核取記號。

    ![app settings](./media/sql-database-nodejs-use-web-site/appsettings.png)

4.  最後，按一下頁面底部的 **[儲存]** 圖示，以對執行階段環境認可此變更。

    ![app settings save](./media/sql-database-nodejs-use-web-site/savebutton.png)

5.  從命令列，切換至 **tasklist** 目錄，並輸入下列命令以移除 **config.json** 檔案：

         git rm config.json
        git commit -m "Removing config file"

6.  執行下列命令並將變更部署到 Azure：

        git push azure master

在將變更部署到 Azure 之後，您的 Web 應用程式應會繼續運作，因為它現在會從 **[應用程式設定]** 項目中讀取連接字串。若要驗證這一點，請在 **[應用程式設定]** 中，將 **SQL\_CONN** 項目的值變更成無效值。在儲存此值之後，該網站應該會因無效的連接字串而發生錯誤。

後續步驟
--------

-   [使用 MongoDB 的 Node.js Web 應用程式](../store-mongolab-web-sites-nodejs-store-data-mongodb/)

-   [使用資料表儲存體的 Node.js Web 應用程式](/en-us/develop/nodejs/tutorials/web-site-with-storage/)

其他資源
--------

[適用於 Mac 和 Linux 的 Azure 命令列工具](/en-us/develop/nodejs/how-to-guides/command-line-tools/)

