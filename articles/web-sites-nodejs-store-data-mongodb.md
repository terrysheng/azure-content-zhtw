<properties 
	pageTitle="VM 上使用 MongoDB 的 Node.js 網站 - Azure 教學課程" 
	description="指導如何使用 MongoDB 從 Azure 主控的節點應用程式儲存和存取資料。" 
	services="web-sites, virtual-machines" 
	documentationCenter="nodejs" 
	authors="blackmist" 
	writer="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>


# 以虛擬機器中的 MongoDB 在 Azure 上建立 Node.js 應用程式

本教學課程說明如何使用 Azure 虛擬機器上裝載的 [MongoDB] 來存放資料，以及從 Azure 網站中裝載的 [Node] 應用程式存取資料。[MongoDB] 是受歡迎的高效能開放原始碼 NoSQL 資料庫。

您將學到：

* 如何從 VM Depot 設定執行 Ubuntu 和 MongoDB 的虛擬機器。
* 如何從節點應用程式存取 MongoDB
* 如何使用 Azure 的跨平台工具建立 Azure 網站

依照本教學課程的指示，您將建置一個簡單的網頁型工作管理應用程式，用於建立、擷取、完成工作。工作將存放於 MongoDB 之中。

> [AZURE.NOTE] 本教學課程使用安裝在虛擬機器上的 MongoDB 的執行個體。如果您偏好使用 MongoLabs 提供的託管 MongoDB 執行個體，請參閱<a href="/zh-tw/develop/nodejs/tutorials/website-with-mongodb-mongolab/">以使用 MongoLab 附加元件的 MongoDB 在 Azure 上建立 Node.js 應用程式</a>。
 
本教學課程的專案檔案將存放在名為 **tasklist** 的目錄中，完成的應用程式看起來將會像下面這樣：

![A web page displaying an empty tasklist][node-mongo-finished]

> [AZURE.NOTE] 以下的許多步驟皆提到要使用命令列。在這些步驟中，請使用適用於您作業系統的命令列，例如 __Windows PowerShell__(Windows) 或 __Bash__(Unix Shell)。在 OS X 系統上，您可以透過 [終端機] 應用程式存取命令列。

##先決條件

本教學課程中的步驟是使用 Node.js，因此您的開發環境中必須有最新版本的 [Node.js][node]。

此外，必須可以從開發環境的命令列使用 [Git]，因為這會用來將應用程式部署至 Azure 網站。

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##建立虛擬機器

<!--本教學課程假設您已在 Azure 中建立虛擬機器。建立虛擬機器之後，您必須在這部虛擬機器上安裝 MongoDB：

* 若要建立 Linux 虛擬機器和安裝 MongoDB，請參閱[在 Linux 虛擬機器上安裝 MongoDB]。

在 Azure 中建立虛擬機器並安裝 MongoDB 之後，請務必記住虛擬機器的 DNS 名稱 (例如，"testlinuxvm.cloudapp.net") 以及您在端點中指定之 MongoDB 的外部連接埠。稍後在教學課程中需要此資訊。.-->

雖然可以建立新的 VM，然後依照 [MongoDB 安裝指南][installguides]所述在其中安裝 MongoDB，但已有社群幫您執行其中大部分的工作，並在 VM Depot 中提供。以下步驟示範如何使用 VM Depot 中已安裝及設定 Mongo DB 的映像。 

> [AZURE.NOTE] 本教學課程使用的社群映像會將 MongoDB 資料儲存在作業系統磁碟上。雖然這樣已足以應付教學目的，但是將 MongoDB 資料儲存在資料磁碟上會有更強的效能。如需建立新 VM (包括資料磁碟) 和將 MongoDB 資料儲存在資料磁碟上的步驟，請參閱[將 MongoDB 安裝在 Azure 上的 Linux][mongodbonazure]。

1. 登入[Azure 管理入口網站][azureportal]，選取 [__Virtual Machines__]，選取 [__Images__]，然後選取 [__VM Depot__]。

	![screenshot of selecting VM Depot][selectdepo]

2. 選取包含 MongoDB 的映像。在這個範例中，我選取 Ubuntu 以將清單縮減至以 Ubuntu Linux 散發套件為基礎的映像。最後，我選取 MongoDB v2.2.3 on Hardened Ubuntu 映像。

	![screenshot of selected mongodb v2.2.3 on hardened ubuntu image][selectedimage]

	> [AZURE.NOTE] 請務必選取 [__More__] 以看有關映像的所有資訊。部分映像可能需要您在使用映像建立虛擬機器後進行額外的設定。

	按一下底部的箭頭前往下一個畫面。

3. 選取地區和儲存體帳戶，用於存放此映像的 VHD。按一下核取記號繼續。
	
	![screenshot of choose a storage account][selectstorage]

	> [AZURE.NOTE] 這會啟動複製程序，將 VM Depot 上的映像複製到指定的儲存體帳戶。這需要一些時間，約 15 分鐘或更久。

4. 一旦映像的狀態變更為 [__Pending registration__]，請選取 [__Register__]，然後為新映像輸入易記名稱。按一下核取記號繼續。

	![screenshot of registering an image][register]

5. 一旦映像的狀態變更為 [__Available__]，請選取 [__+ New__]、[__Virtual Machine__]、[__From Gallery__]。當系統要求您 [__Choose an Image__]，請選取 [__My Images__]，然後選取先前步驟中建立的映像。按一下箭頭以繼續。

	![screenshot of the image][myimage]

6. 提供虛擬機器名稱、大小、使用者名稱。按一下箭頭以繼續。

	![screenshot of the vm name, user name, etc.][vmname]

	>[AZURE.NOTE] 在本教學課程中，您不需要使用 SSH 從遠端連線至虛擬機器。若您不熟悉如何使用憑證來搭配 SSH，請選取 [使用密碼]**** 並提供密碼。
	>
	> 如需使用 SSH 來搭配 Azure 上之 Linux VM 的詳細資訊，請參閱[如何使用 SSH 來搭配 Azure 上的 Linux][sshazure]。

7. 選取要使用新的或現有的雲端服務，以及要將虛擬機器建立在哪個區域。按一下箭頭以繼續。

	![screenshot of the vm configuration][vmconfig]

8. 為虛擬機器設定額外的端點。由於我們將存取此虛擬機器上的 MongoDB，請使用下列資訊新增端點：

	* Name - MongoDB
	* Protocol - TCP
	* Public port - 27017
	* private port - 27017

	若要公開 MongoDB Web 入口網站，請使用以下資訊新增另一個端點：

	* Name - MongoDBWeb
	* Protocol - TCP
	* Public port - 28017
	* Private port - 28017
	
	最後，選取核取記號以設定虛擬機器。

	![screenshot of the endpoint configuration][vmendpoint]

9. 一旦虛擬機器狀態變更為 [__Running__]，您應該就能夠開啟 Web 瀏覽器以連到 __http://&lt;YourVMDNSName&gt;.cloudapp.net:28017/__ 確認 MongoDB 為執行中。頁面底部應該是一份記錄，會顯示服務的相關資訊，類似這樣：

		Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
           18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
           18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
           18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
           18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
           18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
           18:57:16 [initandlisten] recover : no journal files present, no recovery needed
           18:57:17 [initandlisten] waiting for connections on port 27017

	如果記錄顯示錯誤，請查閱 [MongoDB 文件][mongodocs]以了解疑難排解步驟。


##安裝模組及產生樣板

在本節中，您將在開發環境上建立新的 Node 應用程式，並使用 npm 加入模組套件。針對工作清單應用程式，您將使用 [Express] 和 [Mongoose] 模組。Express 模組提供節點的模型檢視控制器架構，Mongoose 則是用來與 MongoDB 通訊的驅動程式。

###安裝 Express 及產生樣板

1. 從命令列將目錄變更到 [tasklist]**** 目錄。如果 [tasklist]**** 目錄不存在，請建立該目錄。

	> [AZURE.NOTE] 本教學課程參考 __tasklist__ 資料夾。在此忽略此資料夾的完整路徑，是因為不同作業系統有不同的路徑語義。您應該在本機檔案系統中易於存取的位置建立此資料夾，例如 __~/node/tasklist__ 或 __c:\node\tasklist__

2. 輸入以下命令以安裝 express 命令。

	npm install express-generator -g
 
	> [AZURE.NOTE] 在某些作業系統上使用 '-g' 參數時，您可能會收到 ___Error: EPERM, chmod '/usr/local/bin/express'___ 的錯誤，要求您以系統管理員身分執行該帳戶。若發生此情形，請使用 `sudo` 命令以更高的權限層級執行 npm。

    此命令的輸出應類似這樣：

		express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)                                                                         
 
	> [AZURE.NOTE] 安裝 Express 模組時若使用 '-g' 參數，會全域安裝模組。這麼做我們就可以存取 ___express___ 命令來產生網站樣板，而不需額外輸入路徑資訊。

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

	此命令完成後，在 [tasklist]**** 目錄中應有數個新的目錄和檔案。

3. 將 **tasklist/bin/www** 檔案複製到 [tasklist]**** 資料夾中名為 **server.js** 的檔案。Azure 網站預期 Node.js 應用程式的進入點會是 **server.js** 或 **app.js**。雖然 **app.js** 已存在，但它並不是進入點，因此我們必須使用 **server.js**。

4. 修改 **server.js** 檔案以將以下這一行的其中一個 '.' 字元移除。

		var app = require('../app');

	經過修改的那一行應顯示如下。

		var app = require('./app');

	這是必要的，因為 **server.js** (先前為 **bin/www**) 現已位於和必要的 **app.js** 檔案相同的資料夾中。

###安裝其他模組

**package.json** 檔案是 **express** 命令建立的檔案之一。這個檔案包含 Express 應用程式的其他必要模組清單。之後，當您部署此應用程式至 Azure 網站，此檔案將用於決定 Azure 上需要安裝哪些模組來支援您的應用程式。
	
1. 從 [tasklist]**** 資料夾使用以下命令來安裝 **package.json** 檔案中描述的模組：

        npm install

    此命令的輸出應類似這樣：

		debug@0.7.4 node_modules\debug
		
		cookie-parser@1.0.1 node_modules\cookie-parser
		├── cookie-signature@1.0.3
		└── cookie@0.1.0
		
		morgan@1.0.0 node_modules\morgan
		└── bytes@0.2.1
		
		body-parser@1.0.2 node_modules\body-parser
		├── qs@0.6.6
		├── raw-body@1.1.4 (bytes@0.3.0)
		└── type-is@1.1.0 (mime@1.2.11)
		
		express@4.0.0 node_modules\express
		├── methods@0.1.0
		├── parseurl@1.0.1
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── escape-html@1.0.1
		├── cookie-signature@1.0.3
		├── fresh@0.2.2
		├── range-parser@1.0.0
		├── buffer-crc32@0.2.1
		├── qs@0.6.6
		├── cookie@0.1.0
		├── path-to-regexp@0.1.2
		├── send@0.2.0 (mime@1.2.11)
		├── type-is@1.0.0 (mime@1.2.11)
		├── accepts@1.0.0 (negotiator@0.3.0, mime@1.2.11)
		└── serve-static@1.0.1 (send@0.1.4)
		
		jade@1.3.1 node_modules\jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── mkdirp@0.3.5
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── constantinople@2.0.0 (uglify-js@2.4.13)
		├── with@3.0.0 (uglify-js@2.4.13)
		└── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)                                                                

	這會安裝 Express 應用程式使用的所有預設模組。

2. 接下來輸入下列命令，在本機安裝 Mongoose 模組並將其項目儲存至 **package.json** 檔案：

		npm install mongoose --save

	此命令的輸出應類似這樣：

		mongoose@3.8.8 node_modules\mongoose
		├── regexp-clone@0.0.1
		├── sliced@0.0.5
		├── muri@0.3.1
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.5.3
		└── mongodb@1.3.23 (kerberos@0.0.3, bson@0.2.5)         

    > [AZURE.NOTE] 您可以放心忽略任何有關安裝 C++ bson 剖析器的訊息。

##在節點應用程式中使用 MongoDB

在本節中，您將透過加入包含您工作的模型的 **task.js** 檔案，來擴充 **express** 命令所建立的基本應用程式。您也將修改現有的 **app.js** 並建立新的 **tasklist.js** 控制器檔案，以使用模型。

### 建立模型

1. 在 **tasklist** 目錄中，建立名稱為 **models** 的新目錄。

2. 在 [models]**** 目錄中，建立名稱為 **task.js** 的新檔案。此檔案將包含您的應用程式建立之工作的模型。

3. 在 **task.js** 檔案的開頭加入以下程式碼以參照所需的程式庫：

        var mongoose = require('mongoose'),
	        Schema = mongoose.Schema;

4. 接下來，要加入程式碼以定義和匯出模型。此模型將用於執行與 MongoDB 資料庫的互動操作。

        var TaskSchema = new Schema({
	        itemName      : String,
	        itemCategory  : String,
	        itemCompleted : { type: Boolean, default: false },
	        itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. 儲存並關閉 **task.js** 檔案。

###建立控制器

1. 在 **tasklist/routes** 目錄中建立名為 **tasklist.js** 的新檔案，並以文字編輯器開啟。

2. 將下列程式碼新增至 **tasklist.js**。這會載入 mongoose 模組和 **task.js** 中定義的工作模型。TaskList 函數是用來根據 **connection** 值建立與 MongoDB 伺服器的連線：

		var mongoose = require('mongoose'),
	        task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
  		  mongoose.connect(connection);
		}

2. 繼續在 **tasklist.js** 檔案中加入 **showTasks**、**addTask** 和 **completeTasks** 方法：

		TaskList.prototype = {
  		  showTasks: function(req, res) {
      	    task.find({itemCompleted: false}, function foundTasks(err, items) {
      		  res.render('index',{title: 'My ToDo List ', tasks: items})
    		});
  		  },

  		  addTask: function(req,res) {
    		var item = req.body.item;
    		newTask = new task();
    		newTask.itemName = item.name;
    		newTask.itemCategory = item.category;
    		newTask.save(function savedTask(err){
      		  if(err) {
      		    throw err;
      		  }
    	    });
    	  	res.redirect('/');
  		  },
  

  		  completeTask: function(req,res) {
    		var completedTasks = req.body;
    		for(taskId in completedTasks) {
      		  if(completedTasks[taskId]=='true') {
        		var conditions = { _id: taskId };
        		var updates = { itemCompleted: completedTasks[taskId] };
        		task.update(conditions, updates, function updatedTask(err) {
          		  if(err) {
          		    throw err;
          		  }
        		});
      		  }
    		}
    		res.redirect('/');
  		  }
		}

3. 儲存 **tasklist.js** 檔案。

### 修改 app.js

1. 在 [tasklist]**** 目錄中，以文字編輯器開啟 **app.js** 檔案。此檔案是稍早執行 **express** 命令所建立的。

2. 在 **app.js** 檔案的開頭新增下列程式碼。這將會以 MongoDB 伺服器的連接字串將 **TaskList** 初始化：

        var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.MONGODB_URI);

	請注意第二行，您會存取稍後將設定的環境變數，此環境變數包含您的 mongo 執行個體的連線資訊。若您有為開發目的而執行的本機 mongo 執行個體，請暫時將此值設為 "localhost" 而非 process.env.MONGODB_URI。

3. 找出下列幾行：

		app.use('/', routes);
		app.use('/users', users);

	將以上兩行取代為下列幾行：

    	app.get('/', taskList.showTasks.bind(taskList));
    	app.post('/addtask', taskList.addTask.bind(taskList));
    	app.post('/completetask', taskList.completeTask.bind(taskList));

	這會加入 **tasklist.js** 中定義的函式做為路由。

4. 儲存 **app.js** 檔案。

###修改索引檢視

1. 將目錄變更為 **views** 目錄，並在文字編輯器中開啟 **index.jade** 檔案。

2. 以下列程式碼取代 **index.jade** 檔案的內容。這會定義用於顯示現有工作的檢視，以及用於加入新工作及將現有工作標示為完成的表單。

		h1= title
		form(action="/completetask", method="post")
		  table(border="1")
		    tr
		      td Name
		      td Category
		      td Date
		      td Complete
		    each task in tasks
		      tr
		        td #{task.itemName}
		        td #{task.itemCategory}
		        - var day   = task.itemDate.getDate();
		        - var month = task.itemDate.getMonth() + 1;
		        - var year  = task.itemDate.getFullYear();
		        td #{month + "/" + day + "/" + year}
		        td
		          input(type="checkbox", name="#{task._id}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
		  input(type="submit", value="Update tasks")
		hr
		form(action="/addtask", method="post")
		  table(border="1") 
		    tr
		      td Item Name: 
		      td 
		        input(name="item[name]", type="textbox")
		    tr
		      td Item Category: 
		      td 
		        input(name="item[category]", type="textbox")
		  input(type="submit", value="Add item")

3. 儲存並關閉 **index.jade** 檔案。

<!-- ##在本機執行您的應用程式

若要在本機電腦測試應用程式，請執行下列步驟：

1. 從命令列將目錄變更到 [tasklist]**** 目錄。

2. 將您開發環境中的 MONGODB_URI 環境變數，設為指向虛擬機器代管的 MongoDB。在下面的範例中，用您虛擬機器的名稱取代 __mymongodb__。

	在 Windows 系統上，請使用下列項目設定環境變數。

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

	在 OS X 或 Linux 架構的系統上，請使用下列項目設定環境變數。

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks
		export MONGODB_URI

	這會指示應用程式連線至之前在 __mymongodb.cloudapp.net__ 虛擬機器上建立的 MongoDB，並使用名為 'tasks' 的 DB。

2. 使用下列命令在本機啟動應用程式：

        node app.js

3. 開啟 Web 瀏覽器並瀏覽至 http://localhost:3000。這應該會顯示如下所示的網頁：

    ![A webpage displaying an empty tasklist][node-mongo-finished]

4. 使用所提供的 [Item Name]**** (項目名稱) 和 [Item Category]**** (項目類別) 欄位來輸入資訊，然後按一下 [Add item]**** (新增項目)。

    ![An image of the add item field with populated values.][node-mongo-add-item]

5. 頁面應該會更新，將項目顯示在待辦事項資料表中。

    ![An image of the new item in the list of tasks][node-mongo-list-items]

6. 若要完成工作，您只需選取 [Complete] (已完成) 欄位中的核取方塊，然後按一下 [Update tasks]**** (更新工作)。雖然按一下 [Update tasks]**** (更新工作) 後不會看到任何變更，但 MongoDB 中的文件項目現在已經標示為已完成。

7. 若要停止 node 處理程序，請移至命令列並按下 **CTRL** 和 **C** 鍵。-->

##將應用程式部署至 Azure

本節的步驟使用 Azure 命令列工具建立新的 Azure 網站，然後使用 Git 部署您的應用程式。若要執行這些步驟，必須有 Azure 訂用帳戶。

> [AZURE.NOTE] 也可以使用 Azure 入口網站執行這些步驟。如需使用 Azure 入口網站部署 Node.js 應用程式的步驟，請參閱<a href="/zh-tw/develop/nodejs/tutorials/create-a-website-(mac)/">建立並部署 Node.js 應用程式至 Azure 網站</a>。

> [AZURE.NOTE] 如果這是您建立的第一個 Azure 網站，您必須使用 Azure 入口網站部署此應用程式。

###安裝 Azure 跨平台命令列介面

Azure 跨平台命令列介面 (xplat-cli) 可讓您執行 Azure 服務的管理作業。若您的開發環境尚未安裝及設定 xplat-cli，請參閱[安裝及設定 Azure 跨平台命令列介面][xplatcli]中的說明。

###建立 Azure 網站

1. 從命令列將目錄變更到 [tasklist]**** 目錄。

2. 使用以下命令建立新的 Azure 網站。將  'myuniquesitename' 取代成您網站的唯一網站名稱。此值將成為所建立網站之 URL 的一部分。

		azure site create myuniquesitename --git
		
	系統將提示您網站所在的資料中心。選取地理位置與您最近的資料中心。
	
	`--git` 參數將在 [tasklist]**** 資料夾中本機建立 Git 儲存機制 (若其中還沒有)。它也會建立名為  'azure' 的 [Git 遠端]，以用來將應用程式發行至 Azure。它將建立 [iisnode.yml]，其中包含 Azure 代管 node 應用程式時所使用的設定。最後，它還會建立 .gitignore 檔案，以排除 node-modules 資料夾而不要將其發佈至 .git。
	
	> [AZURE.NOTE] 若從已有 Git 儲存機制的目錄執行此命令，則無法將目錄重新初始化。
	
	> [AZURE.NOTE] 若省略 '--git' 參數，但目錄中有 Git 儲存機制，則仍會建立 'azure' 遠端。
	
	一旦此命令完成，您將會看到類似以下的輸出。請注意，**Created website at** 開頭的這一行包含網站的 URL。

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://username@mongodbtasklist.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

	> [AZURE.NOTE]> 如果這是您的訂用帳戶的第一個 Azure 網站，系統將指示您使用入口網站來建立網站。如需詳細資訊，請參閱<a href="/zh-tw/develop/nodejs/tutorials/create-a-website-(mac)/">建立 Node.js 應用程式並部署至 Azure 網站</a>。

###設定 MONGODB_URI 環境變數

應用程式預期會在 MONGODB_URI 環境變數中找到 MongoDB 執行個體的連接字串。使用以下命令為網站設定此值：

	azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

這將為網站建立新的應用程式設定，這些設定將用來填入網站讀取的 MONGODB_URI 環境變數。以 MongoDB 安裝所在的虛擬機器名稱取代 'mymongodb.cloudapp.net' 的值。

###發行應用程式

1. 若您尚未進入 [tasklist]**** 目錄，請在 [終端機] 視窗中將目錄變更到此目錄。

2. 使用以下命令加入並認可加入本機 Git 儲存機制的檔案：

		git add .
		git commit -m "adding files"

3. 發行最新 Git 儲存機制變更至 Azure 網站時，必須指定目標分支為 **master**，因為它將用於網站內容。

		git push azure master
	
	您將看到類似以下的輸出：部署開始時，Azure 會下載所有 npm 模組。 

		Counting objects: 17, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (13/13), done.
		Writing objects: 100% (17/17), 3.21 KiB, done.
		Total 17 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id 'ef276f3042'.
		remote: Preparing files for deployment.
		remote: Running NPM.
		...
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://username@mongodbtasklist.azurewebsites.net/MongoDBTasklist.git
 		 * [new branch]      master -> master
 
4. 完成推播作業後，請使用 `azure site browse` 命令瀏覽至該網站以檢視您的應用程式。

##後續步驟

本文的步驟會說明如何使用 MongoDB 存放資訊，不過，您也可以使用 Azure 資料表服務。如需詳細資訊，請參閱 [使用 Azure 表格服務的 Node.js Web 應用程式]。

若要了解如何使用 MongoLab 提供的已代管 MongoDB 執行個體，請參閱[以使用 MongoLab 附加元件的 MongoDB 在 Azure 上建立 Node.js 應用程式](/zh-tw/develop/nodejs/tutorials/website-with-mongodb-mongolab/).

若要了解如何保護 MongoDB，請參閱 [MongoDB 安全性][mongosecurity]。

##其他資源

[適用於 Mac 與 Linux 的 Azure 命令列工具]    
[建立 Node.js 應用程並部署至 Azure 網站]
[使用 Git 發佈至 Azure 網站]

[mongosecurity]: http://docs.mongodb.org/manual/security/
[node]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /zh-tw/pricing/free-trial
[Git remote]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[適用於 Mac 與 Linux 的 Azure 命令列工具]: /zh-tw/develop/nodejs/how-to-guides/command-line-tools/
[Azure 開發人員中心]: /zh-tw/develop/nodejs/
[建立 Node.js 應用程式並部署至 Azure 網站]: /zh-tw/develop/nodejs/tutorials/create-a-website-(mac)/
[使用 Git 發佈至 Azure 網站]: /zh-tw/develop/nodejs/common-tasks/publishing-with-git/
[在 Linux 虛擬機器上安裝 MongoDB]: /zh-tw/manage/linux/common-tasks/mongodb-on-a-linux-vm/
[使用 Azure 表格服務的 Node.js Web 應用程式]: /zh-tw/develop/nodejs/tutorials/web-site-with-storage/
[node-mongo-finished]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_empty.png
[node-mongo-express-results]: ./media/store-mongodb-web-sites-nodejs-use-mac/express_output.png
[node-mongo-add-item]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_add_item.png
[node-mongo-list-items]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_items.png
[download-publishing-settings]: ./media/store-mongodb-web-sites-nodejs-use-mac/azure-account-download-cli.png
[installguides]: http://docs.mongodb.org/manual/installation/
[azureportal]: https://manage.windowsazure.com/
[mongodocs]: http://docs.mongodb.org/manual/
[xplatcli]: /zh-tw/documentation/articles/xplat-cli/

[selectdepo]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
[selectedimage]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
[selectstorage]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
[register]: ./media/web-sites-nodejs-store-data-mongodb/register.png
[myimage]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
[vmname]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
[vmconfig]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
[vmendpoint]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
[sshazure]: http://www.windowsazure.com/zh-tw/documentation/articles/linux-use-ssh-key/
[mongodbonazure]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/ 

<!--HONumber=42-->
