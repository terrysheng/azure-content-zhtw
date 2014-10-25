<properties linkid="develop-node-website-with-mongodb-mac" urlDisplayName="Website with MongoDB" pageTitle="Node.js website with MongoDB on a VM - Azure tutorial" metaKeywords="Azure tutorial MongoDB, MongoDB store data, access data MongoDB Node, Azure Node app" description="A tutorial that teaches you how to use MongoDB to store and access data from a Node application hosted on Azure." metaCanonical="http://www.windowsazure.com/zh-TW/develop/nodejs/tutorials/website-with-mongodb-mongolab/" services="web-sites,virtual-machines" documentationCenter="nodejs" title="Node.js Web Application with Storage on MongoDB (Virtual Machine)" authors="larryfr"  solutions="" writer="" manager="" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# 以虛擬機器中的 MongoDB 在 Azure 上建立 Node.js 應用程式

本教學課程說明如何使用 Azure 虛擬機器上代管的 [MongoDB][] (英文) 存放資料，以及從 Azure 網站代管的 [Node][] (英文) 應用程式存取這些資料。[MongoDB][] (英文) 是受歡迎的高效能開放原始碼 NoSQL 資料庫。

您將了解：

-   如何從 VM Depot 設定執行 Ubuntu 和 MongoDB 的虛擬機器。
-   如何從節點應用程式存取 MongoDB
-   如何使用 Azure 的跨平台工具建立 Azure 網站

依照本教學課程的指示，您將建置一個簡單的網頁型工作管理應用程式，用於建立、擷取、完成工作。工作將存放於 MongoDB 之中。

> [WACOM.NOTE] 本教學課程使用安裝在虛擬機器上的 MongoDB 的執行個體。如果您偏好使用 MongoLabs 提供的託管 MongoDB 執行個體，請參閱[以使用 MongoLab 附加元件的 MongoDB 在 Azure 上建立 Node.js 應用程式][]。

本教學課程的專案檔案將存放於 **tasklist** 目錄中，完成的應用程式應類似這樣：

![A web page displaying an empty tasklist][]

> [WACOM.NOTE] 以下的許多步驟皆提到要使用命令列。針對這些步驟，請使用作業系統的命令列，如 **Windows PowerShell** (Windows) 或 **Bash** (Unix Shell)。在 OS X 系統上，您可以透過 [終端機] 應用程式存取命令列。

## 必要條件

本教學課程步驟使用的 Node.js，在您的開發環境中必須有最新版本的 [Node.js][Node] (英文)。

此外，必須可以從開發環境的命令列使用 [Git][]，因為這會用於將應用程式部署至 Azure 網站。

[WACOM.INCLUDE [create-account-and-websites-note][]]

## 建立虛擬機器

<!--This tutorial assumes you have created a virtual machine in Azure. After creating the virtual machine you need to install MongoDB on the virtual machine:  * To create a Linux virtual machine and install MongoDB, see [Installing MongoDB on a Linux Virtual machine].  After you have created the virtual machine in Azure and installed MongoDB, be sure to remember the DNS name of the virtual machine ("testlinuxvm.cloudapp.net", for example) and the external port for MongoDB that you specified in the endpoint.  You will need this information later in the tutorial.-->

雖然可以建立新的虛擬機器，然後依照 [MongoDB 安裝指南][] (英文) 所述在其中安裝 MongoDB ，但已有社群幫您做好大部分的工作，並放在 VM Depot 中。以下步驟示範如何使用 VM Depot 中已安裝及設定 Mongo DB 的映像。

> [WACOM.NOTE] 本教學課程使用的社群映像會將 MongoDB 資料儲存在作業系統磁碟上。雖然這樣已足以應付教學目的，但是將 MongoDB 資料儲存在資料磁碟上會有更強的效能。如需建立新 VM (包括資料磁碟) 和將 MongoDB 資料儲存在資料磁碟上的步驟，請參閱[將 MongoDB 安裝在 Azure 上的 Linux][]。

1.  登入 [Azure 管理入口網站][]，選取 [虛擬機器]，選取 [映像]，然後選取 [VM Depot]。

    ![選取 VM Depot 的螢幕擷取畫面][]

2.  選取包含 MongoDB 的映像。在這個範例中，我選取 Ubuntu 以將清單縮減至以 Ubuntu Linux 散發套件為基礎的映像。最後，我選取 MongoDB v2.2.3 on Hardened Ubuntu 映像。

    ![選取 mongodb v2.2.3 on hardened ubuntu 映像的螢幕擷取畫面][]

    > [WACOM.NOTE] 請務必選取 [詳細資訊] 查看有關映像的所有資訊。部分映像可能需要您在使用映像建立虛擬機器後進行額外的設定。

    按一下底部的箭頭前往下一個畫面。

3.  選取地區和儲存體帳戶，用於存放此映像的 VHD。按一下打勾記號繼續。

    ![選擇儲存體帳戶的螢幕擷取畫面][]

    > [WACOM.NOTE] 這會啟動複製程序，將 VM Depot 上的映像複製到指定的儲存體帳戶。這需要一些時間，約 15 分鐘或更久。

4.  一旦映像的狀態變更為 [Pending registration]，選取 [註冊]，然後為新映像輸入易記的名稱。按一下打勾記號繼續。

    ![註冊映像的螢幕擷取畫面][]

5.  一旦映像的狀態變更為 [可用]，選取 [+ 新增]、[虛擬機器]、[從組件庫]。在系統要求 [選擇映像] 時，選取 [我的映像]，然後選擇上一個步驟建立的映像。按一下箭頭以繼續。

    ![映像的螢幕擷取畫面][]

6.  提供虛擬機器名稱、大小、使用者名稱。按一下箭頭以繼續。

    ![虛擬機器名稱、使用者名稱等的螢幕擷取畫面][]

    > [WACOM.NOTE] 在本教學課程中，您不需要使用 SSH 遠端連接至虛擬機器。若您不熟悉如何搭配使用憑證與 SSH，請選取 [使用密碼] 並提供密碼。
    >
    > 如需在 Azure 上搭配使用 SSH 與 Linux 虛擬機器的詳細資訊，請參閱[如何在 Azure 上搭配使用 SSH 與 Linux][] (英文)。

7.  選取要使用新的或現有的雲端服務，以及要將虛擬機器建立在哪個區域。按一下箭頭以繼續。

    ![虛擬機器組態的螢幕擷取畫面][]

8.  為虛擬機器設定額外的端點。由於我們將存取此虛擬機器上的 MongoDB，請使用以下資訊新增端點：

    -   名稱 - MongoDB
    -   通訊協定 - TCP
    -   公用連接埠 - 27017
    -   私人連接埠 - 27017

    若要公開 MongoDB Web 入口網站，請使用以下資訊新增另一個端點：

    -   名稱 - MongoDBWeb
    -   通訊協定 - TCP
    -   公用連接埠 - 28017
    -   私人連接埠 - 28017

    最後，選取核取記號以設定虛擬機器。

    ![端點組態的螢幕擷取畫面][]

9.  一旦虛擬機器狀態變更為 [執行中]，您應該就能夠開啟 Web 瀏覽器以連到 **http://&lt;YourVMDNSName&gt;.cloudapp.net:28017/** 確認該 MongoDB 為執行中。頁面底部應該是一份記錄，會顯示服務的相關資訊，類似這樣：

        Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
           18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
           18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
           18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
           18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
           18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
           18:57:16 [initandlisten] recover : no journal files present, no recovery needed
           18:57:17 [initandlisten] waiting for connections on port 27017

    如果記錄顯示錯誤，請查閱 [MongoDB 文件][] (英文) 以了解疑難排解步驟。

## 安裝模組及產生樣板

在本節中，您將在開發環境上建立新的 Node 應用程式，並使用 npm 加入模組套件。針對工作清單應用程式，您將使用 [Express][] (英文) 和 [Mongoose][] (英文) 模組。Express 模組提供節點的模型檢視控制器架構，Mongoose 則是用來與 MongoDB 通訊的驅動程式。

### 安裝 Express 及產生樣板

1.  使用命令列變更目錄至 **tasklist** 目錄。如果 **tasklist** 目錄不存在，請建立。

    > [WACOM.NOTE] 本教學課程會參考 **tasklist** 資料夾。在此忽略此資料夾的完整路徑，是因為不同作業系統有不同的路徑語義。您應該在本機檔案系統中易於存取的位置建立此資料夾，例如 **~/node/tasklist** 或 **c:\\node\\tasklist**

2.  輸入以下命令以安裝 express 命令。

    npm install express-generator -g

    > [WACOM.NOTE] 在某些作業系統上使用 '-g' 參數，可能會收到錯誤訊息 ***Error:EPERM, chmod '/usr/local/bin/express'***，並要求您以管理員執行該帳戶。若發生這個情況，使用 `sudo` 命令以更高的權限層級執行 npm。

    此命令的輸出應類似這樣：

        express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
        ├── mkdirp@0.3.5
        └── commander@1.3.2 (keypress@0.1.0)                                                                         

    > [WACOM.NOTE] 安裝 Express 模組時使用的 '-g' 參數，會全域安裝該模組。這麼做我們就可以存取 ***express*** 命令來產生網站樣板，而不需額外輸入路徑資訊。

3.  若要建立用於此應用程式的樣板，使用 **express** 命令：

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

4.  將 **tasklist/bin/www** 檔案複製到 **tasklist** 資料夾中名為 **server.js** 的檔案。Azure 網站預期 Node.js應用程式的進入點會是 **server.js** 或 **app.js** 兩者之一。雖然 **app.js** 已存在，但它並不是進入點，因此我們必須使用 **server.js**。

5.  修改 **server.js** 檔案，將下列那一行其中一個 '.' 字元移除。

        var app = require('../app');

    經過修改的那一行應顯示如下。

        var app = require('./app');

    這項作業是必須的，因為 **server.js** (先前為 **bin/www**) 現已位於與必要 **app.js** 檔案相同的資料夾中。

### 安裝其他模組

**package.json** 檔案是 **express** 命令建立的檔案之一。這個檔案包含 Express 應用程式的其他必要模組清單。之後，當您部署此應用程式至 Azure 網站，此檔案將用於決定 Azure 上需要安裝哪些模組來支援您的應用程式。

1.  從 **tasklist** 資料夾使用以下命令安裝 **package.json** 檔案中所指出的模組：

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

2.  接下來，輸入以下命令在本機安裝 Mongoose 模組並將其所用項目儲存至 **package.json** 檔案：

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

    > [WACOM.NOTE] 您可以放心忽略任何有關安裝 C++ bson 剖析器的訊息。

## 在節點應用程式中使用 MongoDB

在本節中，您將藉著加入 **task.js** 檔案的方式擴充由 **express** 命令建立的基本應用程式 (task.js 檔案包含您的工作的模型)。您也將修改現有的 **app.js** 及建立新的 **tasklist.js** 控制器檔案，以使用模型。

### 建立模型

1.  在 **tasklist** 目錄中，建立新目錄 **models**。

2.  在 **models** 目錄中，建立新檔案 **task.js**。此檔案將包含您的應用程式建立之工作的模型。

3.  在 **task.js** 檔案的開頭加入以下程式碼以參考所需的程式庫：

        var mongoose = require('mongoose'),
            Schema = mongoose.Schema;

4.  接下來，要加入程式碼以定義和匯出模型。此模型將用於執行與 MongoDB 資料庫的互動操作。

        var TaskSchema = new Schema({
            itemName      : String,
            itemCategory  : String,
            itemCompleted : { type: Boolean, default: false },
            itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5.  儲存並關閉 **task.js** 檔案。

### 建立控制器

1.  在 **tasklist/routes** 目錄中建立新檔案 **tasklist.js**，然後在文字編輯器中開啟檔案。

2.  在 **tasklist.js** 中加入以下程式碼。這會載入 mongoose 模組和在 **task.js** 中定義的工作模型。TaskList 函數會根據 **connection** 值與 MongoDB 伺服器建立連線：

        var mongoose = require('mongoose'),
            task = require('../models/task.js');

        module.exports = TaskList;

        function TaskList(connection) {
          mongoose.connect(connection);
        }

3.  繼續在 **tasklist.js** 檔案中加入 **showTasks** (顯示工作)、**addTask** (新增工作)、**completeTasks** (完成工作) 方法：

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

4.  儲存 **tasklist.js** 檔案。

### 修改 app.js

1.  在 **tasklist** 目錄中，在文字編輯器中開啟 **app.js** 檔案。此檔案是稍早執行 **express** 命令所建立。

2.  在 **app.js** 檔案的開頭加入以下程式碼。這將以 MongoDB 伺服器的連接字串將 **TaskList** 初始化：

        var TaskList = require('./routes/tasklist');
        var taskList = new TaskList(process.env.MONGODB_URI);

    請注意第二行，您會存取稍後將設定的環境變數，此環境變數包含您的 mongo 執行個體的連線資訊。若您有為開發目的而執行的本機 mongo 執行個體，要暫時將此值設為 "localhost" 而非 process.env.MONGODB\_URI。

3.  找出下列幾行：

        app.use('/', routes);
        app.use('/users', users);

    將以上兩行取代為下列幾行：

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

    這會加入 **tasklist.js** 中定義的函數做為路由。

4.  儲存 **app.js** 檔案。

### 修改索引檢視

1.  將目錄變更為 **views** 目錄，在文字編輯器中開啟 **index.jade** 檔案。

2.  以下列程式碼取代 **index.jade** 檔案的內容。這會定義用於顯示現有工作的檢視，以及用於加入新工作及將現有工作標示為完成的表單。

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

3.  儲存並關閉 **index.jade** 檔案。

<!-- ##Run your application locally  To test the application on your local machine, perform the following steps:  1. From the command-line, change directories to the **tasklist** directory.  2. Set the MONGODB_URI environment variable on your development environment to point to the virtual machine hosting MongoDB. In the examples below, replace __mymongodb__ with your virtual machine name.      On a Windows system, use the following to set the environment variable.          set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks      On an OS X or Linux-based system, use the following to set the environment variable.          set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks         export MONGODB_URI      This will instruct the application to connect to MongoDB on the __mymongodb.cloudapp.net__ virtual machine created earlier, and to use a DB named 'tasks'.  2. Use the following command to launch the application locally:          node app.js  3. Open a web browser and navigate to http://localhost:3000. This should display a web page similar to the following:      ![A webpage displaying an empty tasklist][node-mongo-finished]  4. Use the provided fields for **Item Name** and **Item Category** to enter information, and then click **Add item**.      ![An image of the add item field with populated values.][node-mongo-add-item]  5. The page should update to display the item in the ToDo List table.      ![An image of the new item in the list of tasks][node-mongo-list-items]  6. To complete a task, simply check the checkbox in the Complete column, and then click **Update tasks**. While there is no visual change after clicking **Update tasks**, the document entry in MongoDB has now been marked as completed.  7. To stop the node process, go to the command-line and press the **CTRL** and **C** keys. -->

## 將應用程式部署至 Azure

本節的步驟使用 Azure 命令列工具建立新的 Azure 網站，然後使用 Git 部署您的應用程式。若要執行這些步驟，必須有 Azure 訂用帳戶。

> [WACOM.NOTE] 也可以使用 Azure 入口網站執行這些步驟。如需使用 Azure 入口網站部署 Node.js 應用程式的步驟，請參閱[建立並部署 Node.js 應用程式至 Azure 網站][]。

> [WACOM.NOTE] 如果這是您建立的第一個 Azure 網站，您必須使用 Azure 入口網站部署此應用程式。

### 安裝 Azure 跨平台命令列介面

Azure 跨平台命令列介面 (xplat-cli) 可讓您執行 Azure 服務的管理作業。若您的開發環境尚未安裝及設定 xplat-cli，請參閱[安裝及設定 Azure 跨平台命令列介面][]中的說明。

### 建立 Azure 網站

1.  使用命令列變更目錄至 **tasklist** 目錄。

2.  使用以下命令建立新的 Azure 網站。以您網站獨有的名稱取代 'myuniquesitename'。此值將成為所建立網站之 URL 的一部分。

        azure site create myuniquesitename --git

    系統將提示您網站所在的資料中心。選取地理位置與您最近的資料中心。

    `--git` 參數將在 **tasklist** 資料夾中本機建立 Git 儲存機制 (若其中還沒有)。它也會建立名為 'azure' 的 [Git 遠端][]，用於將應用程式發行至 Azure。它將建立 [iisnode.yml][] (英文)，其中包含 Azure 代管節點應用程式所使用的設定。最後，它還會建立 .gitignore 檔案，以排除 node-modules 資料夾而不要將其發佈至 .git。

    > [WACOM.NOTE] 若從已有 Git 儲存機制的目錄執行此命令，則無法將目錄重新初始化。

    > [WACOM.NOTE] 若省略 '--git' 參數，但目錄中有 Git 儲存機制，則仍會建立 'azure' 遠端。

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

    > [WACOM.NOTE] 如果這是您的訂用帳戶的第一個 Azure 網站，系統將指示您使用入口網站來建立網站。如需詳細資訊，請參閱[建立 Node.js 應用程式並部署至 Azure 網站][建立並部署 Node.js 應用程式至 Azure 網站]。

### 設定 MONGODB\_URI 環境變數

應用程式預期會在 MONGODB\_URI 環境變數中找到 MongoDB 執行個體的連接字串。使用以下命令為網站設定此值：

    azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

這將為網站建立新的應用程式設定，這些設定將用來填入網站讀取的 MONGODB\_URI 環境變數。以 MongoDB 安裝所在的虛擬機器名稱取代 'mymongodb.cloudapp.net' 的值。

### 發行應用程式

1.  若您尚未進入 **tasklist** 目錄，使用 [終端機] 視窗變更目錄至此。

2.  使用以下命令加入並認可加入本機 Git 儲存機制的檔案：

        git add .
        git commit -m "adding files"

3.  發行最新 Git 儲存機制變更至 Azure 網站時，必須指定目標分支為 **master**，因為它將用於網站內容。

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

4.  完成推播作業後，請使用 `azure site browse` 命令瀏覽至該網站以檢視您的應用程式。

## 後續步驟

本文的步驟會說明如何使用 MongoDB 存放資訊，不過，您也可以使用 Azure 資料表服務。如需詳細資訊，請參閱 [Node.js Web 應用程式與 Azure 資料表服務][]。

若要了解如何使用 MongoLab 提供的託管 MongoDB 執行個體，請參閱[以使用 MongoLab 附加元件的 MongoDB 在 Azure 上建立 Node.js 應用程式][]。

若要了解如何保護 MongoDB，請參閱 [MongoDB 安全性][] (英文)。

## 其他資源

[適用於 Mac 和 Linux 的 Azure 命令列工具][建立並部署 Node.js 應用程式至 Azure 網站]
[使用 Git 發佈至 Azure 網站][]

  [MongoDB]: http://www.mongodb.org
  [Node]: http://nodejs.org
  [以使用 MongoLab 附加元件的 MongoDB 在 Azure 上建立 Node.js 應用程式]: /zh-TW/develop/nodejs/tutorials/website-with-mongodb-mongolab/
  [A web page displaying an empty tasklist]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_empty.png
  [Git]: http://git-scm.com
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [MongoDB 安裝指南]: http://docs.mongodb.org/manual/installation/
  [將 MongoDB 安裝在 Azure 上的 Linux]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [選取 VM Depot 的螢幕擷取畫面]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
  [選取 mongodb v2.2.3 on hardened ubuntu 映像的螢幕擷取畫面]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
  [選擇儲存體帳戶的螢幕擷取畫面]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
  [註冊映像的螢幕擷取畫面]: ./media/web-sites-nodejs-store-data-mongodb/register.png
  [映像的螢幕擷取畫面]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
  [虛擬機器名稱、使用者名稱等的螢幕擷取畫面]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
  [如何在 Azure 上搭配使用 SSH 與 Linux]: http://www.windowsazure.com/zh-TW/documentation/articles/linux-use-ssh-key/
  [虛擬機器組態的螢幕擷取畫面]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
  [端點組態的螢幕擷取畫面]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
  [http://\<YourVMDNSName\>.cloudapp.net:28017/\_\_]: http://<YourVMDNSName>.cloudapp.net:28017/__
  [MongoDB 文件]: http://docs.mongodb.org/manual/
  [Express]: http://expressjs.com
  [Mongoose]: http://mongoosejs.com
  [建立並部署 Node.js 應用程式至 Azure 網站]: /zh-TW/develop/nodejs/tutorials/create-a-website-(mac)/
  [安裝及設定 Azure 跨平台命令列介面]: /zh-TW/documentation/articles/xplat-cli/
  [Git 遠端]: http://git-scm.com/docs/git-remote
  [iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
  [Node.js Web 應用程式與 Azure 資料表服務]: /zh-TW/develop/nodejs/tutorials/web-site-with-storage/
  [MongoDB 安全性]: http://docs.mongodb.org/manual/security/
  [使用 Git 發佈至 Azure 網站]: /zh-TW/develop/nodejs/common-tasks/publishing-with-git/
