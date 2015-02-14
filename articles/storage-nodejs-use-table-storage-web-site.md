<properties 
	pageTitle="使用資料表儲存體的 Node.js 網站 | Microsoft Azure" 
	description="本教學課程說明如何使用 Azure 資料表服務，從託管於 Azure 網站上的節點應用程式儲存資料。" 
	services="web-sites, storage" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="mwasson"/>





# 使用 Azure 資料表服務的 Node.js Web 應用程式

本教學課程說明如何使用 Azure 資料管理所提供的資料表服務，從 Azure 上代管的 [node] 應用程式儲存及存取資料。本教學課程假設您先前有過一些使用節點及 [Git] 的經驗。

您將了解：

* 如何使用 npm (節點封裝管理員) 來安裝節點模組

* 如何使用 Azure 資料表服務

* 如何使用適用於 Mac 和 Linux 的 Azure 命令列工具建立 Azure 網站

依照本教學課程的指示，您將建置一個簡單的網頁型工作管理應用程式，用於建立、擷取、完成工作。工作存放於資料表服務中。

本教學課程的專案檔案將存放在名為 **tasklist** 的目錄中，完成的應用程式看起來將會像下面這樣：

![A web page displaying an empty tasklist][node-table-finished]

> [AZURE.NOTE] 本教學課程參考 **tasklist** 資料夾。在此忽略此資料夾的完整路徑，是因為不同作業系統有不同的路徑語義。您應該在本機檔案系統中易於存取的位置建立此資料夾，例如 **~/node/tasklist** 或 **c:\node\tasklist**。

> [AZURE.NOTE] 以下的許多步驟皆提到要使用命令列。在這些步驟中，請使用適用於您作業系統的命令列，例如 **cmd.exe** (Windows) 或 **Bash** (Unix Shell)。在 OS X 系統上，您可以透過 [終端機] 應用程式存取命令列。

##必要條件

在依照本文中的指示進行之前，您應確定已安裝下列項目：

* [node] 版本 0.10.24 或更高版本

* [Git]

* 文字編輯器

* 網頁瀏覽器

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##建立儲存體帳戶

執行下列步驟來建立儲存體帳戶。此帳戶將用於本教學課程的後續指示。

1. 開啟網頁瀏覽器並移至 [Azure 入口網站]。若出現提示，請用您的 Azure 訂用帳戶資訊登入。

2. 在入口網站底部，按一下 [+ NEW] 然後選取 [儲存體帳戶]。

	![+new][portal-new]

	![storage account][portal-storage-account]

3. 選取 [快速建立]，然後輸入此儲存體帳戶的 URL 和區域/同質群組。由於這是教學課程，且不需要複寫到全域，請取消核取 [啟用異地複寫 ]。最後，按一下 [建立儲存體帳戶]。

	![quick create][portal-quick-create-storage]

	記下您輸入的 URL，因為這將在後面的步驟中參考為帳戶名稱。

4. 建立儲存體帳戶之後，按一下頁面底部的 [管理金鑰]。如此會顯示此儲存體帳戶的主要和次要存取金鑰。複製並儲存主要存取金鑰，然後按一下核取記號。

	![access keys][portal-storage-access-keys]

##安裝模組及產生樣板

在本節中，您將建立新的 Node 應用程式，並使用 npm 來新增模組封裝。針對工作清單應用程式，您將使用 [Express] 和 [Azure] 模組。Express 模組提供節點的模型檢視控制器架構，Azure 模組則提供資料表服務的連線。

###安裝 Express 及產生樣板

1. 從命令列將目錄變更到 **tasklist** 目錄。如果 **tasklist** 目錄不存在，請建立該目錄。

2. 輸入以下命令以安裝 express 命令。

		npm install express-generator@4.2.0 -g

    > [AZURE.NOTE] 在某些作業系統上使用 '-g' 參數，您可能會收到錯誤 **Error:EPERM, chmod '/usr/local/bin/express'**，並要求您以管理員執行該帳戶。若發生這個情況，使用 **sudo** 命令以更高的權限層級執行 npm。

    此命令的輸出應類似這樣：

		express-generator@4.2.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)

	> [AZURE.NOTE] 安裝 Express 模組時若使用 '-g' 參數，會全域安裝模組。這麼做我們就可以存取 **express** 命令來產生網站樣板，而不需額外輸入路徑資訊。**

4. 若要建立用於此應用程式的 Scaffolding，請使用 **express** 命令：

        express

	此命令的輸出應類似這樣：

		   create : .
		   create : ./package.json
		   create : ./app.js
		   create : ./public
		   create : ./public/images
		   create : ./routes
		   create : ./routes/index.js
		   create : ./routes/users.js
		   create : ./public/stylesheets
		   create : ./public/stylesheets/style.css
		   create : ./views
		   create : ./views/index.jade
		   create : ./views/layout.jade
		   create : ./views/error.jade
		   create : ./public/javascripts
		   create : ./bin
		   create : ./bin/www
		
		   install dependencies:
		     $ cd . && npm install
		
		   run the app:
		     $ DEBUG=my-application ./bin/www

	此命令完成後，在 **tasklist** 目錄中應有數個新的目錄和檔案。

###安裝其他模組

**package.json** 檔案是 **express** 命令建立的檔案之一。這個檔案包含 Express 應用程式的其他必要模組清單。之後，當您部署此應用程式至 Azure 網站，此檔案將用於決定 Azure 上需要安裝哪些模組來支援您的應用程式。

1. 從命令列切換目錄至 **tasklist** 資料夾，輸入下列命令以安裝 **package.json** 檔案中所述的模組：

        npm install

    此命令的輸出應類似這樣：

		debug@0.7.4 node_modules\debug
		
		static-favicon@1.0.2 node_modules\static-favicon
		
		morgan@1.0.1 node_modules\morgan
		└── bytes@0.3.0
		
		cookie-parser@1.0.1 node_modules\cookie-parser
		├── cookie-signature@1.0.3
		└── cookie@0.1.0
		
		body-parser@1.0.2 node_modules\body-parser
		├── qs@0.6.6
		├── raw-body@1.1.7 (string_decoder@0.10.25-1, bytes@1.0.0)
		└── type-is@1.1.0 (mime@1.2.11)
		
		express@4.2.0 node_modules\express
		├── parseurl@1.0.1
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── cookie@0.1.2
		├── escape-html@1.0.1
		├── cookie-signature@1.0.3
		├── debug@0.8.1
		├── fresh@0.2.2
		├── qs@0.6.6
		├── range-parser@1.0.0
		├── methods@1.0.0
		├── buffer-crc32@0.2.1
		├── serve-static@1.1.0
		├── path-to-regexp@0.1.2
		├── send@0.3.0 (debug@0.8.0, mime@1.2.11)
		├── type-is@1.1.0 (mime@1.2.11)
		└── accepts@1.0.1 (negotiator@0.4.7, mime@1.2.11)
		
		jade@1.3.1 node_modules\jade
		├── commander@2.1.0
		├── character-parser@1.2.0
		├── mkdirp@0.3.5
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── constantinople@2.0.1 (uglify-js@2.4.15)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		└── with@3.0.0 (uglify-js@2.4.15)


	這會安裝 Express 所需的所有預設模組。

2. 接著，輸入下列命令以在本機上安裝 [azure]、[node-uuid]、[nconf] 和 [async] 模組，並將其項目儲存至 **package.json** 檔案：

		npm install azure-storage node-uuid async nconf --save

	此命令的輸出應類似這樣：

		async@0.9.0 node_modules\async

		node-uuid@1.4.1 node_modules\node-uuid
		
		nconf@0.6.9 node_modules\nconf
		├── ini@1.2.1
		├── async@0.2.9
		└── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)
		
		azure-storage@0.3.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── validator@3.1.0
		├── underscore@1.4.4
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (forever-agent@0.5.2, aws-sign@0.3.0, json-stringify-safe@5.0.0, tunnel-agent@0.3.0, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, form-data@0.1.4, hawk@1.0.0, http-signature@0.10.0)

##在節點應用程式中使用資料表服務

在本節中，您將透過加入包含您工作的模型的 **task.js** 檔案，來擴充 **express** 命令所建立的基本應用程式。您也將修改現有的 **app.js** 及建立使用模型的新 **tasklist.js** 檔案。

### 建立模型

1. 在 **tasklist** 目錄中，建立名稱為 **models** 的新目錄。

2. 在 **models** 目錄中，建立名稱為 **task.js** 的新檔案。此檔案將包含您的應用程式建立之工作的模型。

3. 在 **task.js** 檔案的開頭加入以下程式碼以參照所需的程式庫：

        var azure = require('azure-storage');
  		var uuid = require('node-uuid');
		var entityGen = azure.TableUtilities.entityGenerator;

4. 接下來，要加入程式碼以定義和匯出 Task 物件。此物件負責連接至資料表。

  		module.exports = Task;

		function Task(storageClient, tableName, partitionKey) {
		  this.storageClient = storageClient;
		  this.tableName = tableName;
		  this.partitionKey = partitionKey;
		  this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
		    if(error) {
		      throw error;
		    }
		  });
		};

5. 接下來，新增下列程式碼以定義 Task 物件上的其他方法，允許與資料表中存放的資料互動：

		Task.prototype = {
		  find: function(query, callback) {
		    self = this;
		    self.storageClient.queryEntities(this.tableName, query, null, function entitiesQueried(error, result) {
		      if(error) {
		        callback(error);
		      } else {
		        callback(null, result.entries);
		      }
		    });
		  },

		  addItem: function(item, callback) {
		    self = this;
		    // use entityGenerator to set types
			// NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
		    var itemDescriptor = {
		      PartitionKey: entityGen.String(self.partitionKey),
		      RowKey: entityGen.String(uuid()),
		      name: entityGen.String(item.name),
		      category: entityGen.String(item.category),
		      completed: entityGen.Boolean(false)
		    };
		    self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
		      if(error){  
		        callback(error);
		      }
		      callback(null);
		    });
		  },

		  updateItem: function(rKey, callback) {
		    self = this;
		    self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
		      if(error) {
		        callback(error);
		      }
		      entity.completed._ = true;
		      self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
		        if(error) {
		          callback(error);
		        }
		        callback(null);
		      });
		    });
		  }
		}

6. 儲存並關閉 **task.js** 檔案。

###建立控制器

1. 在 **tasklist/routes** 目錄中，建立名稱為 **tasklist.js** 的新檔案，並在文字編輯器中開啟。

2. 在 **tasklist.js** 中加入以下程式碼。這會載入 azure 和 async 模組，它們是由 **tasklist.js** 所使用。這也會定義 **TaskList** 函數，系統會傳遞我們稍早定義的 **Task** 物件執行個體給它：

		var azure = require('azure-storage');
		var async = require('async');

		module.exports = TaskList;

		function TaskList(task) {
		  this.task = task;
		}

2. 繼續在 **tasklist.js** 檔案中加入 **showTasks** (顯示工作)、**addTask** (新增工作)、**completeTasks** (完成工作) 方法：

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    self = this;
		    var query = new azure.TableQuery()
		      .where('completed eq ?', false);
		    self.task.find(query, function itemsFound(error, items) {
		      res.render('index',{title: 'My ToDo List ', tasks: items});
		    });
		  },

		  addTask: function(req,res) {
		    var self = this      
		    var item = req.body.item;
		    self.task.addItem(item, function itemAdded(error) {
		      if(error) {
		        throw error;
		      }
		      res.redirect('/');
		    });
		  },

		  completeTask: function(req,res) {
		    var self = this;
		    var completedTasks = Object.keys(req.body);
		    async.forEach(completedTasks, function taskIterator(completedTask, callback) {
		      self.task.updateItem(completedTask, function itemsUpdated(error) {
		        if(error){
		          callback(error);
		        } else {
		          callback(null);
		        }
		      });
		    }, function goHome(error){
		      if(error) {
		        throw error;
		      } else {
		       res.redirect('/');
		      }
		    });
		  }
		}

3. 儲存 **tasklist.js** 檔案。

### 修改 app.js

1. 在 **tasklist** 目錄中，在文字編輯器中開啟 **app.js** 檔案。此檔案是稍早執行 **express** 命令所建立的。

2. 在檔案開頭，加入下列內容以載入 azure 模組、設定資料表名稱、partitionKey，以及設定此範例所使用的儲存體認證：

		var azure = require('azure-storage');
		var nconf = require('nconf');
		nconf.env()
		     .file({ file: 'config.json'});
		var tableName = nconf.get("TABLE_NAME");
		var partitionKey = nconf.get("PARTITION_KEY");
		var accountName = nconf.get("STORAGE_NAME");
		var accountKey = nconf.get("STORAGE_KEY");

	> [AZURE.NOTE] nconf 會從環境變數或 **config.json** 檔 (我們稍後會建立) 載入組態值。

3. 在 app.js 檔中，向下捲動到看見此行處：

		app.use('/', routes);
		app.use('/users', users);

	將以上幾行取代為以下顯示的程式碼。這會初始化 <strong>Task</strong> 的執行個體，並連接到您的儲存體帳戶。這會傳遞給 <strong>TaskList</strong>，它將用來與表格服務通訊：

		var TaskList = require('./routes/tasklist');
		var Task = require('./models/task');
		var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
		var taskList = new TaskList(task);

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));
	
4. 儲存 **app.js** 檔案。

###修改索引檢視

1. 將目錄變更為 **views** 目錄，並在文字編輯器中開啟 **index.jade** 檔案。

2. 以下列程式碼取代 **index.jade** 檔案的內容。這會定義用於顯示現有工作的檢視，以及用於加入新工作及將現有工作標示為完成的表單。

		extends layout

		block content
		  h1= title
		  br
		
		  form(action="/completetask", method="post")
		    table.table.table-striped.table-bordered
		      tr
		        td Name
		        td Category
		        td Date
		        td Complete
		      if (typeof tasks === "undefined")
		        tr
		          td 
		      else
		        each task in tasks
		          tr
		            td #{task.name._}
		            td #{task.category._}
		            - var day   = task.Timestamp._.getDate();
		            - var month = task.Timestamp._.getMonth() + 1;
		            - var year  = task.Timestamp._.getFullYear();
		            td #{month + "/" + day + "/" + year}
		            td
		              input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
		    button.btn(type="submit") Update tasks
		  hr
		  form.well(action="/addtask", method="post")
		    label Item Name: 
		    input(name="item[name]", type="textbox")
		    label Item Category: 
		    input(name="item[category]", type="textbox")
		    br
		    button.btn(type="submit") Add item

3. 儲存並關閉 **index.jade** 檔案。

###修改全域版面配置

**views** 目錄中的 **layout.jade** 檔是用來作為其他 **.jade** 檔案的全域範本。在此步驟中，您將修改它以使用 [Twitter Bootstrap](https://github.com/twbs/bootstrap)，這個工具組能夠方便設計美觀的網站。

1. 下載並解壓縮 [Twitter Bootstrap](http://getbootstrap.com/) 的檔案。將 **bootstrap.min.css** 檔案從 **bootstrap\\dist\\css** 資料夾複製到您 tasklist 應用程式的 **public\\stylesheets** 目錄。

2. 從 **views** 資料夾，以文字編輯器開啟 **layout.jade**，並將內容取代為：

		doctype html
		html
		  head
		    title= title
		    link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
		    link(rel='stylesheet', href='/stylesheets/style.css')
		  body.app
		    nav.navbar.navbar-default
		      div.navbar-header
		        a.navbar-brand(href='/') My Tasks
		    block content

3. 儲存 **layout.jade** 檔案。

###建立組態檔

**config.json** 檔包含用來連接至 SQL Database 的連接字串，且由應用程式在執行階段讀取。若要建立此檔案，請執行下列步驟：

1. 在 **tasklist** 目錄中建立名為 **config.json** 的新檔案，並以文字編輯器開啟。

2. **config.json** 檔案的內容應該類似以下內容：

		{
			"STORAGE_NAME": "storage account name",
			"STORAGE_KEY": "storage access key",
			"PARTITION_KEY": "mytasks",
			"TABLE_NAME": "tasks"
		}

	將 **storage account name** 取代為您先前建立的儲存體帳戶名稱。將 **storage access key** 取代為儲存體帳戶的主要存取金鑰。

3. 儲存檔案。

##在本機執行您的應用程式

若要在本機電腦測試應用程式，請執行下列步驟：

1. 從命令列將目錄變更到 **tasklist** 目錄。

2. 使用下列命令在本機啟動應用程式：

        npm start

3. 開啟 Web 瀏覽器並瀏覽至 http://127.0.0.1:3000。 這應該會顯示如下所示的網頁：

    ![A webpage displaying an empty tasklist][node-table-finished]

4. 使用所提供的 [項目名稱] 和 [項目類別] 欄位來輸入資訊，然後按一下 [新增項目]。

5. 頁面應該會更新，將項目顯示在待辦事項資料表中。

    ![An image of the new item in the list of tasks][node-table-list-items]

6. 若要完成工作，您只需勾選 [已完成] 資料行中的核取方塊，然後按一下 [更新工作]。

7. 若要停止 node 處理程序，請移至命令列並按下 **CTRL** 和 **C** 鍵。

##將應用程式部署至 Azure

本節的步驟使用 Azure 命令列工具建立新的 Azure 網站，然後使用 Git 部署您的應用程式。若要執行這些步驟，必須有 Azure 訂用帳戶。

> [AZURE.NOTE] 也可以使用 Azure 入口網站執行這些步驟。如需使用 Azure 入口網站部署 Node.js 應用程式的步驟，請參閱[建立並部署 Node.js 應用程式至 Azure 網站]。

> [AZURE.NOTE] 如果這是您建立的第一個 Azure 網站，您必須使用 Azure 入口網站部署此應用程式。

###建立 Azure 訂用帳戶

如果您還沒有 Azure 訂用帳戶，可以[免費]註冊。登入之後，依照這些步驟繼續執行此教學課程。

[AZURE.INCLUDE [antares-iaas-signup](../includes/antares-iaas-signup.md)]

###安裝適用於 Mac 和 Linux 的 Azure 命令列工具

使用以下命令來安裝命令列工具：
	
	npm install azure-cli -g

> [AZURE.NOTE] 如需詳細資訊，請參閱[安裝及設定 Azure 跨平台命令列介面](/zh-tw/documentation/articles/xplat-cli/);

> [AZURE.NOTE] 雖然命令列工具主要是為了 Mac 和 Linux 使用者而建立，不過它們是以 Node.js 為基礎，且應該可在任何能執行 Node 的系統上使用。

###匯入發佈設定

在 Azure 使用命令列工具前，必須先下載包含訂用帳戶資訊的檔案。請執行以下步驟來下載及匯入此檔案。

1. 從命令列將目錄變更到 **tasklist** 目錄。

2. 輸入下列命令以啟動瀏覽器，並瀏覽至下載頁面。若出現提示，請用與訂用帳戶相關聯的帳戶登入。

		azure account download
	
	![The download page][download-publishing-settings]
	
	檔案應該會自動開始下載，如果沒有，您可以按一下頁面開頭的連結手動下載檔案。

3. 檔案下載完畢後，使用以下命令匯入設定：

		azure account import <path-to-file>
		
	指定前一個步驟下載之發佈設定檔案的路徑和檔案名稱。一旦命令完成，您應該會看到類似這樣的輸出：
	
		info:   Executing command account import
		info:   Setting service endpoint to: management.core.windows.net
		info:   Setting service port to: 443
		info:   Found subscription: YourSubscription
		info:   Setting default subscription to: YourSubscription
		warn:   The 'C:\users\username\downloads\YourSubscription-6-7-2012-credentials.publishsettings' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK

4. 匯入完成後，您應該刪除發佈設定檔案，因為您不再需要它，而它卻含有關於 Azure 訂用帳戶的敏感資訊。

###建立 Azure 網站

1. 從命令列將目錄變更到 **tasklist** 目錄。

2. 使用以下命令建立新的 Azure 網站

		azure site create --git
		
	系統將提示您輸入網站名稱，及它將位於哪個資料中心。請提供唯一的名稱，並選取地理位置與您接近的資料中心。
	
	`--git` 參數將在 Azure 上建立此網站的 Git 儲存機制。它也會在目前目錄中初始化 Git 儲存機制 (如果還沒有儲存機制的話)。它也會建立名為  'azure' 的 [Git 遠端]，以用來將應用程式發行至 Azure。最後，它將建立 **web.config** 檔，其中包含 Azure 代管節點應用程式所使用的設定。
	
	> [AZURE.NOTE] 若從已有 Git 儲存機制的目錄執行此命令，則無法將目錄重新初始化。
	
	> [AZURE.NOTE] 若省略 `--git` 參數，但目錄中有 Git 儲存機制，則仍會建立 'azure' 遠端。
	
	一旦此命令完成，您將會看到類似以下的輸出。請注意，**Website created at** 開頭的這一行包含網站的 URL。
	
		info:   Executing command site create
		help:   Need a site name
		Name:   TableTasklist
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default .gitignore file
		info:   Creating a new web site
		info:   Created web site at  tabletasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
		info:   site create command OK

	> [AZURE.NOTE] 如果這是您的訂用帳戶的第一個 Azure 網站，系統將指示您使用入口網站來建立網站。如需詳細資訊，請參閱[建立並部署 Node.js 應用程式至 Azure 網站]。

###發行應用程式

1. 若您尚未進入 **tasklist** 目錄，請在 [終端機] 視窗中將目錄變更到此目錄。

2. 使用以下命令加入並認可加入本機 Git 儲存機制的檔案：

		git add .
		git commit -m "adding files"

3. 發行最新 Git 儲存機制變更至 Azure 網站時，必須指定目標分支為 **master**，因為它將用於網站內容。

		git push azure master
	
	在部署結束時，您應該會看到如下所示的陳述式：
	
		To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
 		 * [new branch]      master -> master

4. 完成推播作業後，瀏覽至先前由  `azure create site` 命令傳回的網站 URL 以檢視您的應用程式。

###切換至環境變數

我們稍早實作程式碼來尋找環境變數或從 **config.json** 檔案中載入值。在下列步驟中，您將在網站組態中，建立應用程式透過環境變數真正存取的機碼值組。

1. 從管理入口網站，按一下 [網站]，然後選取您的網站。

	![Open website dashboard][go-to-dashboard]

2. 按一下 [設定]，然後尋找頁面的 [應用程式設定] 區段。 

	![configure link][web-configure]

3. 在 [應用程式設定] 區段的 [機碼] 欄位中輸入 **STORAGE_NAME**，然後在 [值] 欄位中輸入儲存體帳戶的名稱。按一下核取記號移到下個欄位。針對下列機碼和值重複此程序：

	* **STORAGE_KEY** - 儲存體帳戶的存取金鑰
	
	* **PARTITION_KEY** - 'mytasks'

	* **TABLE_NAME** - 'tasks'

	![app settings][app-settings]

4. 最後，按一下頁面底部的 [儲存] 圖示，對執行階段環境認可此變更。

	![app settings save][app-settings-save]

5. 從命令列切換目錄至 **tasklist** 目錄，輸入下列命令以移除 **config.json** 檔案：

		git rm config.json
		git commit -m "Removing config file"

6. 執行下列命令將變更部署到 Azure：

		git push azure master

在將變更部署到 Azure 之後，您的 Web 應用程式應會繼續運作，因為它現在會從 [應用程式設定] 項目中讀取連接字串。若要驗證這一點，請將 [應用程式設定] 中的 **STORAGE_KEY** 項目值變更為無效的值。儲存此值之後，網站應該會失敗，因為儲存體存取金鑰設定無效。

##後續步驟

雖然本文的步驟說明如何使用資料表服務來存放資訊，您也可以使用 MongoDB。如需詳細資訊，請參閱使用 [MongoDB 的 Node.js Web 應用程式]。

##其他資源

[適用於 Mac 和 Linux 的 Azure 命令列工具]
[Create and deploy a Node.js application to Azure Web Sites]: /zh-tw/documentation/articles/web-sites-nodejs-develop-deploy-mac/
[Publishing to Azure Web Sites with Git]: /zh-tw/documentation/articles/web-sites-publish-source-control/
[Azure Developer Center]: /zh-tw/develop/nodejs/


[node]: http://nodejs.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[for free]: http://windowsazure.com
[Git remote]: http://git-scm.com/docs/git-remote

[MongoDB 的 Node.js Web 應用程式]: /zh-tw/documentation/articles/web-sites-nodejs-store-data-mongodb/
[適用於 Mac 和 Linux 的 Azure 命令列工具]: /zh-tw/documentation/articles/xplat-cli/

[Publishing to Azure Web Sites with Git]: /zh-tw/documentation/articles/web-sites-publish-source-control/
[azure]: https://github.com/Azure/azure-sdk-for-node


[Azure Portal]: http://windowsazure.com


[node-table-finished]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
[node-table-list-items]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
[download-publishing-settings]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
[portal-new]: ./media/storage-nodejs-use-table-storage-web-site/plus-new.png
[portal-storage-account]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
[portal-quick-create-storage]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
[portal-storage-access-keys]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png

[go-to-dashboard]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
[web-configure]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
[app-settings-save]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
[app-settings]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png

[Create and deploy a Node.js application to an Azure Web Site]: /zh-tw/documentation/articles/web-sites-nodejs-develop-deploy-mac/
<!--HONumber=42-->
