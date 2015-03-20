<properties 
	pageTitle="使用 DocumentDB 建置 Node.js Web 應用程式 |  Azure" 
	description="了解如何使用 Microsoft Azure DocumentDB 來儲存和存取託管於 Azure 網站的 Node.js Express Web 應用程式資料。" 
	services="documentdb" 
	documentationCenter="" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article" 
	ms.date="03/03/2015" 
	ms.author="ryancraw"/>

# <a name="_Toc395783175"></a>使用 DocumentDB 建置 Node.js Web 應用程式

本教學課程示範如何使用 Azure DocumentDB 服務，從託管於 Azure 網站上的 Node.js Express 應用程式儲存和存取資料。

完成本教學課程之後，您將能夠回答下列問題：

- 如何使用 documentdb npm 模組與 DocumentDB 搭配使用？
- 如何將 Web 應用程式部署至 Azure 網站？

按照本教學課程進行後，您將建置簡單的網頁型工作管理應用程式，以便建立、擷取和完成工作。這些工作將會以 JSON 文件形式儲存在 Azure
DocumentDB。

![Screen shot of the My Todo List application created in this tutorial](./media/documentdb-nodejs-application/image1.png)

沒有時間完成本教學課程，而且只想要從 Github 取得完整的方案？沒問題，請從[這裡][]取得。

## <a name="_Toc395783176"></a>必要條件

> [AZURE.TIP] 本教學課程假設您有使用 Node.js 和 Azure 網站的經驗。

在依照本文中的指示進行之前，您應先確定備妥下列項目：

- 使用中的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](../../pricing/free-trial/)。
- [Node.js][] v0.10.29 版或更高版本。
- [Express 產生器](http://www.expressjs.com/starter/generator.html) (您可以透過 `npm install express-generator -g`安裝)
- [Git][]。

## <a name="_Toc395637761"></a>步驟 1：建立 DocumentDB 資料庫帳戶

讓我們從建立 DocumentDB 帳戶開始。如果您已經有帳戶，您可以跳到[步驟 2：建立新的 Node.js 應用程式](#_Toc395783178)。

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../includes/documentdb-keys.md)]

## <a name="_Toc395783178"></a>步驟 2：建立新的 Node.js 應用程式

現在讓我們建立使用 [Express](http://expressjs.com/) 架構的基本 Hello World Node.js 專案。

1. 開啟您喜好的終端機。

2. 使用 express 產生器來產生名為 **todo** 的新應用程式。

		express todo

3. 開啟您的新 **todo** 目錄並安裝相依性。

		cd todo
		npm install

4. 執行新應用程式。

		npm start

5. 您可以檢視新的應用程式，方法是導覽您的瀏覽器至 [http://localhost:3000](http://localhost:3000)。

	![Screenshot of the Hello World application in a browser window](./media/documentdb-nodejs-application/image12.png)

## <a name="_Toc395783179"></a>步驟 3：安裝其他模組

**package.json** 檔案是建立在專案根目錄中的其中一個檔案。這個檔案包含 Node.js 應用程式的其他必要模組清單。之後，當您部署此應用程式至 Azure 網站，此檔案用於決定 Azure 上需要安裝哪些模組來支援您的應用程式。在本教學課程中，我們仍需要安裝兩個封裝。

1. 回到終端機，透過 npm 安裝 **async** 模組。

		npm install async --save

1. 透過 npm 安裝 **documentdb** 模組。此模組可發揮 DocumentDB 的所有強大功能。

		npm install documentdb --save

3. 快速檢查應用程式的 **package.json** 檔案，應該會顯示其他模組。這個檔案會在執行您的應用程式時告訴 Azure 要下載及安裝的封裝。它應該類似下面的範例。

	![Screenshot of the package.json tab](./media/documentdb-nodejs-application/image17.png)

       這會讓 Node (之後則是 Azure) 知道您的應用程式需要仰賴這些額外模組。

## <a name="_Toc395783180"></a>步驟 4：在節點應用程式中使用 DocumentDB 服務

前面的內容在講述所有初始設定和組態，現在讓我們來了解本教學課程的真正目的，也就是使用 Azure DocumentDB 撰寫一些程式碼。

### 建立模型

1. 在 project 目錄中，建立名稱為 **models** 的新目錄。
2. 在 **models** 目錄中，建立名稱為 **taskDao.js** 的新檔案。此檔案將包含您的應用程式建立之工作的模型。
3. 在同一個 **models** 目錄中，建立名為 **docdbUtils.js** 的另一個新檔案。這個檔案會包含一些實用、可重複使用的程式碼，我們會在應用程式中使用。 
4. 複製下列程式碼到 **docdbUtils.js**

		var DocumentDBClient = require('documentdb').DocumentClient;
		
		var DocDBUtils = {
		  getOrCreateDatabase: function(client, databaseId, callback) {
		    var querySpec = {
		      query: 'SELECT * FROM root r WHERE r.id=@id',
		      parameters: [{
		        name: '@id',
		        value: databaseId
		      }]
		    };
		
		    client.queryDatabases(querySpec).toArray(function(err, results) {
		      if (err) {
		        callback(err);
		      }
		
		      if (!err && results.length === 0) {
		        client.createDatabase({
		          id: databaseId
		        }, function(err, created) {
		          callback(null, created);
		        });
		      } else {
		        callback(null, results[0]);
		      }
		    });
		  },
		
		  getOrCreateCollection: function(client, databaseLink, collectionId, callback) {
		    var querySpec = {
		      query: 'SELECT * FROM root r WHERE r.id=@id',
		      parameters: [{
		        name: '@id',
		        value: collectionId
		      }]
		    };
		
		    client.queryCollections(databaseLink, querySpec).toArray(function(err, results) {
		      if (err) {
		        callback(err);
		      }
		
		      if (!err && results.length === 0) {
		        client.createCollection(databaseLink, {
		          id: collectionId
		        }, function(err, created) {
		          callback(null, created);
		        });
		      } else {
		        callback(null, results[0]);
		      }
		    });
		  }
		};
		
		module.exports = DocDBUtils;
		
3. 儲存並關閉 **docdbUtils.js** 檔案。

4. 在 **taskDao.js** 檔案的開頭，新增下列程式碼以參考在以上所建立的 **DocumentDBClient** 和 **docdbUtils.js**：

        var DocumentDBClient = require('documentdb').DocumentClient;
		var docdbUtils = require('./docdbUtils');

4. 接下來，要加入程式碼以定義和匯出 Task 物件。這是負責初始化我們的 Task 物件並設定我們將使用的資料庫和文件集合。

		function TaskDao(documentDBClient, databaseId, collectionId) {
		  this.client = documentDBClient;
		  this.databaseId = databaseId;
		  this.collectionId = collectionId;
		
		  this.database = null;
		  this.collection = null;
		}
		
		module.exports = TaskDao;

5. Next, add the following code to define additional methods on the Task object, which allow interactions with data stored in DocumentDB.

		TaskDao.prototype = {
		  init: function(callback) {
		    var self = this;
		
		    docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function(err, db) {
		      if (err) {
		        callback(err);
		      }
		
		      self.database = db;
		      docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function(err, coll) {
		        if (err) {
		          callback(err);
		        }
		
		        self.collection = coll;
		      });
		    });
		  },
		
		  find: function(querySpec, callback) {
		    var self = this;
		
		    self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
		      if (err) {
		        callback(err);
		      } else {
		        callback(null, results);
		      }
		    });
		  },
		
		  addItem: function(item, callback) {
		    var self = this;
		    item.date = Date.now();
		    item.completed = false;
		    self.client.createDocument(self.collection._self, item, function(err, doc) {
		      if (err) {
		        callback(err);
		      } else {
		        callback(null);
		      }
		    });
		  },
		
		  updateItem: function(itemId, callback) {
		    var self = this;
		
		    self.getItem(itemId, function(err, doc) {
		      if (err) {
		        callback(err);
		      } else {
		        doc.completed = true;
		        self.client.replaceDocument(doc._self, doc, function(err, replaced) {
		          if (err) {
		            callback(err);
		          } else {
		            callback(null);
		          }
		        });
		      }
		    });
		  },
		
		  getItem: function(itemId, callback) {
		    var self = this;
		
		    var querySpec = {
		      query: 'SELECT * FROM root r WHERE r.id=@id',
		      parameters: [{
		        name: '@id',
		        value: itemId
		      }]
		    };
		
		    self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
		      if (err) {
		        callback(err);
		      } else {
		        callback(null, results[0]);
		      }
		    });
		  }
		};

6. 儲存並關閉 **taskDao.js** 檔案。 

### 建立控制器

1. 在您專案的 **routes** 目錄建立名為 **tasklist.js** 的新檔案。 
2. 在 **tasklist.js** 中加入以下程式碼。這會載入 DocumentDBClient 和 async 模組，它們是由 **tasklist.js** 所使用。這也會定義 **TaskList** 函數，系統會傳遞我們稍早定義的 **Task** 物件執行個體給它：

		var DocumentDBClient = require('documentdb').DocumentClient;
		var async = require('async');
		
		function TaskList(taskDao) {
		  this.taskDao = taskDao;
		}
		
		module.exports = TaskList;

3. 繼續在 **tasklist.js** 檔案中加入 **showTasks、addTask** 和 **completeTasks** 方法：

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    var self = this;
		
		    var querySpec = {
		      query: 'SELECT * FROM root r WHERE r.completed=@completed',
		      parameters: [{
		        name: '@completed',
		        value: false
		      }]
		    };
		
		    self.taskDao.find(querySpec, function(err, items) {
		      if (err) {
		        throw (err);
		      }
		
		      res.render('index', {
		        title: 'My ToDo List ',
		        tasks: items
		      });
		    });
		  },
		
		  addTask: function(req, res) {
		    var self = this;
		    var item = req.body;
		
		    self.taskDao.addItem(item, function(err) {
		      if (err) {
		        throw (err);
		      }
		
		      res.redirect('/');
		    });
		  },
		
		  completeTask: function(req, res) {
		    var self = this;
		    var completedTasks = Object.keys(req.body);
		
		    async.forEach(completedTasks, function taskIterator(completedTask, callback) {
		      self.taskDao.updateItem(completedTask, function(err) {
		        if (err) {
		          callback(err);
		        } else {
		          callback(null);
		        }
		      });
		    }, function goHome(err) {
		      if (err) {
		        throw err;
		      } else {
		        res.redirect('/');
		      }
		    });
		  }
		};

4. 儲存並關閉 **tasklist.js** 檔案。
 
### 新增 config.json

1. 在 project 目錄中，建立名稱為 **config.js** 的新檔案。
2. 將下列內容新增至 **config.json**。這會定義組態設定和我們的應用程式所需的值。

		var config = {}
		
		config.host = process.env.HOST || "[the URI value from the DocumentDB Keys blade on http://portal.azure.com]";
		config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the DocumentDB Keys blade on http://portal.azure.com]";
		config.databaseId = "ToDoList";
		config.collectionId = "Items";
		
		module.exports = config;

3. 在 **config.js** 檔案中，使用可在[Azure 預覽入口網站](http://portal.azure.com) DocumentDB 帳戶的 [金鑰] 分頁上找到的值來更新 HOST 和 AUTH_KEY 值：

4. 儲存並關閉 **config.js** 檔案。
 
### 修改 app.js

1. 在專案目錄中，開啟 **app.js** 檔案。此檔案是稍早建立 Express Web 應用程式時所建立。
2. 將下列程式碼新增至 **app.js** 的最上方
	
		var DocumentDBClient = require('documentdb').DocumentClient;
		var config = require('./config');
		var TaskList = require('./routes/tasklist');
		var TaskDao = require('./models/taskDao');

3. 此程式碼會定義要使用的組態檔，並繼續從此檔案中讀取值，成為我們將使用的一些變數。
4. 取代 **app.js** 檔案中的下列兩行：

		app.use('/', routes);
		app.use('/users', users); 

      為下列程式碼片段：

		var docDbClient = new DocumentDBClient(config.host, {
		    masterKey: config.authKey
		});
		var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
		var taskList = new TaskList(taskDao);
		taskDao.init();
		
		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));


6. 這些程式行會定義我們的 **TaskDao** 物件的新執行個體，並具有 DocumentDB 的新連線 (使用從 **config.js** 讀取的值)、初始化工作物件，然後將表單動作與我們的 **TaskList** 控制器上的方法繫結。 

7. 最後，儲存並關閉 **app.js** 檔案，這樣就差不多完成了。
 
## <a name="_Toc395783181"></a>步驟 5：建置使用者介面

現在，讓我們將注意力轉到建置使用者介面，以便使用者可以實際與我們的應用程式互動。我們建立的 Express 應用程式使用 **Jade** 做為檢視引擎。如需 Jade 的詳細資訊，請參閱 [http://jade-lang.com/](http://jade-lang.com/)。

1. **views** 目錄中的 **layout.jade** 檔是用來作為其他 **.jade** 檔案的全域範本。在此步驟中，您將修改它以使用 [Twitter Bootstrap](https://github.com/twbs/bootstrap)，這個工具組能夠方便設計美觀的網站。 
2. 開啟在 **views** 資料夾中找到的 **layout.jade** 檔案，並將其中的內容取代為下列內容；
	
		doctype html
		html
		  head
		    title= title
		    link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
		    link(rel='stylesheet', href='/stylesheets/style.css')
		  body
		    nav.navbar.navbar-inverse.navbar-fixed-top
		      div.navbar-header
		        a.navbar-brand(href='#') My Tasks
		    block content
		    script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
		    script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')



	這個程式碼實際上會指示 **Jade** 引擎呈現我們應用程式的部分 HTML，並建立稱為 **content** 的 **block**，我們可以在其中提供內容頁面的配置。
	儲存並關閉此 **layout.jade** 檔案。

4. 現在開啟我們的應用程式將使用的 **index.jade** 檔案，並將檔案的內容取代為下列內容：

		extends layout
		
		block content
		  h1 #{title}
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
		            td #{task.name}
		            td #{task.category}
		            - var date  = new Date(task.date);
		            - var day   = date.getDate();
		            - var month = date.getMonth() + 1;
		            - var year  = date.getFullYear();
		            td #{month + "/" + day + "/" + year}
		            td
		              input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
		    button.btn(type="submit") Update tasks
		  hr
		  form.well(action="/addtask", method="post")
		    label Item Name:
		    input(name="name", type="textbox")
		    label Item Category:
		    input(name="category", type="textbox")
		    br
		    button.btn(type="submit") Add item

	這個程式碼會擴充配置，並為我們在前面的 **layout.jade** 檔案中看到的 **content** 預留位置提供內容。
	
	在此配置中，我們建立了兩個 HTML 表單。 
	第一個表單包含我們的資料和一個按鈕，可讓我們更新項目，方法是張貼至控制器的 **/completetask** 方法。
	第二個表單包含兩個輸入欄位和一個按鈕，可讓我們建立新項目，方法是張貼至控制器的 **/addtask** 方法。
	
	這應該就是要讓應用程式開始運作所需的所有程式碼。

5. 開啟 **public\stylesheets** 目錄中的 **style.css** 檔案，並並將其中的程式碼取代為下列內容：

		body {
		  padding: 50px;
		  font: 14px "Lucida Grande", Helvetica, Arial, sans-serif;
		}
		a {
		  color: #00B7FF;
		}
		.well label {
		  display: block;
		}
		.well input {
		  margin-bottom: 5px;
		}
		.btn {
		  margin-top: 5px;
		  border: outset 1px #C8C8C8;
		}

	儲存並關閉此 **style.css** 檔案。

## <a name="_Toc395783181"></a>步驟 6：在本機執行您的應用程式

1. 若要在本機電腦上測試應用程式，請在終端機中執行 `npm start` 以啟動您的應用程式，以及啟動看起來類似下圖的瀏覽器頁面：

	![Screenshot of the MyTodo List application in a browser window](./media/documentdb-nodejs-application/image18.png)


2. 使用所提供的 [項目]、[項目名稱] 和 [類別] 欄位來輸入資訊，然後按一下 [**新增項目**]。

3. 系統應該會更新此頁面以在 ToDo 清單中顯示新建立的項目。

	![Screenshot of the application with a new item in the ToDo list](./media/documentdb-nodejs-application/image19.png)

4. 若要完成工作，您只需勾選 [已完成] 資料行中的核取方塊，然後按一下 [更新工作]****。

## <a name="_Toc395783182"></a>步驟 7：將應用程式部署至 Azure 網站

1. 如果您還沒有這麼做，可為您的 Azure 網站啟用 git 儲存機制。您可以在[這裡]找到如何執行這項操作的指示(/documentation/articles/web-sites-publish-source-control/#Step4)。

2. 將您的 Azure 網站新增為 git 遠端。

		git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git

3. 推送到遠端來部署。

		git push azure master

4. 幾秒後，git 便會發佈 Web 應用程式並啟動瀏覽器，您可以在瀏覽器中看到您方便好用的應用程式已在 Azure 中執行！

## <a name="_Toc395637775"></a>後續步驟

恭喜！您剛剛已使用 Azure DocumentDB 建立您的第一個 Express Web 
應用程式並發行至 Azure 網站。

您可以在[這裡][]下載完整參考應用程式的原始程式碼。

  [Node.js]: http://nodejs.org/
  [Git]: http://git-scm.com/
  [這裡]: https://github.com/Azure/azure-documentdb-node/tree/master/core_sdk/tutorial/todo
  [Azure CLI]: http://azure.microsoft.com/documentation/articles/xplat-cli/
  [Azure 管理入口網站]: http://portal.azure.com

<!--HONumber=47-->
