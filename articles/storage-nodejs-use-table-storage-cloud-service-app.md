<properties linkid="dev-nodejs-basic-web-app-with-storage" urlDisplayName="Web App with Storage" pageTitle="Web app with table storage (Node.js) | Microsoft Azure" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="A tutorial that builds on the Web App with Express tutorial by adding Azure Storage services and the Azure module." metaCanonical="" services="cloud-services,storage" documentationCenter="Node.js" title="Node.js Web Application using Storage" authors="" solutions="" manager="" editor="" />

使用儲存體的 Node.js Web 應用程式
=================================

在本教學課程中，您將擴充在[使用 Express 的 Node.js Web 應用程式](http://www.windowsazure.com/zh-tw/develop/nodejs/tutorials/web-app-with-express/)教學課程中所建立的應用程式，方法是使用 Windows Azure Client Libraries for Node.js 來搭配資料管理服務使用。您將擴充您的應用程式，以建立一個可部署到 Azure 的 Web 架構工作清單應用程式。使用者可透過工作清單來擷取工作、新增工作及將工作標示為已完成。

工作項目會儲存於 Azure 儲存體中。Azure 儲存體提供可容錯且高度可用的非結構化資料儲存體。Azure 儲存體包括數種可儲存和存取資料的資料結構，並且您可以從 Azure SDK for Node.js 中所隨附的 API 或透過 REST API 來運用儲存體服務。如需詳細資訊，請參閱[在 Azure 中儲存和存取資料](http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx) (英文)。

本教學課程假設您已完成 [Node.js Web 應用程式](http://www.windowsazure.com/zh-tw/develop/nodejs/tutorials/getting-started/)和[使用 Express 的 Node.js](http://www.windowsazure.com/zh-tw/develop/nodejs/tutorials/web-app-with-express/) 教學課程。

您將了解：

-   如何使用 Jade 範本引擎
-   如何使用 Azure 資料管理服務

以下螢幕擷取畫面顯示完成的應用程式：

![The completed web page in internet explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

在 Web.Config 中設定儲存體認證
------------------------------

若要存取 Azure 儲存體，您必須傳入儲存體認證。若要這樣做，您必須使用 web.config 應用程式設定。這些設定會以環境變數方式傳遞至 Node，接著再由 Azure SDK 進行讀取。

**注意**

只有當將應用程式部署到 Azure 時，才會用到儲存體認證。在模擬器中執行時，應用程式將使用儲存體模擬器。

執行下列步驟以擷取儲存體帳戶認證，並將他們新增至 web.config 設定：

1.  從 **[開始]** 功能表中啟動 Azure PowerShell (如果尚未開啟的話)，方法是依序展開 [所有程式]、**[Azure]**，以滑鼠右鍵按一下 **[Azure PowerShell]**，然後選取 **[以系統管理員身份執行]**。

2.  將目錄變更到包含應用程式的資料夾。例如，C:\\node\\tasklist\\WebRole1。

3.  在 [Azure Powershell] 視窗中，輸入下列 Cmdlet 以擷取儲存體帳戶資訊：

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    這會擷取與託管服務相關的儲存體帳戶和帳戶金鑰清單。

    **注意**

    由於 Azure SDK 會在您部署服務時建立儲存體帳戶，因此，從上一個說明的部署應用程式中應已有儲存體帳戶存在。

4.  開啟包含環境設定 (將應用程式部署到 Azure 時會用到) 的 web.cloud.config 檔案：

        PS C:\node\tasklist\WebRole1> notepad web.cloud.config

5.  將下列區塊插入 **configuration** 元素下方，以您要用於部署之儲存體帳戶的帳戶名稱和主要金鑰取代 {STORAGE ACCOUNT} 和 {STORAGE ACCESS KEY}：

        <appSettings>
        <add key="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}"/>
        <add key="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}"/>
        </appSettings>

    ![web.cloud.config 檔案內容](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  儲存檔案並關閉記事本。

安裝模組
--------

為了要使用 Azure 資料管理服務，您必須安裝節點的 Azure 模組。您還必須安裝 node-uuid 模組，因為它會被用來產生全域唯一識別碼 (UUID)。若要安裝這些模組，請輸入以下命令：

    PS C:\node\tasklist\WebRole1> npm install node-uuid azure

在命令完成之後，模組便會被新增至 **node\_modules** 資料夾中。執行下列步驟，以在應用程式中使用這些模組：

1.  開啟 server.js 檔案：

        PS C:\node\tasklist\WebRole1> notepad server.js

2.  在以 express.createServer() 結尾的行後面加上以下程式碼，以包括 node-uuid、首頁和 azure 模組。首頁模組尚未存在，但您即將建立此模組。

    ![The server.js code with line app = modules.exports line highlighted](./media/storage-nodejs-use-table-storage-cloud-service-app/node38.png)

        var uuid = require('node-uuid');
        var Home = require('./home');
        var azure = require('azure');

3.  新增程式碼，以建立可傳入儲存體帳戶和存取金鑰資訊的儲存體資料表用戶端。

    **注意**

    When running in the emulator, the SDK will automatically use the emulator even though storage account information has been provided via web.config.

        var client = azure.createTableService();

4.  接著，在 Azure 儲存體中建立名為 tasks 的資料表。以下邏輯會建立新的資料表 (如果資料表不存在)，並使用部分預設資料填入資料表。

        //table creation
        client.createTableIfNotExists('tasks', function(error){
        if (error) {
        throw error;
            }

        var item = {
        name :'Add readonly task list',
        category:'Site work',
        date: '12/01/2011',
        RowKey:uuid(),
        PartitionKey :'partition1',
        completed:false
            };

        client.insertEntity('tasks', item, function(){});

        });

5.  以下面的程式碼取代路由區段中的現有程式碼，這將會建立首頁控制器執行個體，並將所有要求路由至 **/** 或它的 **/home**。

    ![The server.js file with the routes section highlighted.](./media/storage-nodejs-use-table-storage-cloud-service-app/node39.png)

        var home = new Home(client);
        app.get('/', home.showItems.bind(home));
        app.get('/home', home.showItems.bind(home));

    請注意，與其內嵌處理要求，您現在可以將命令委派給首頁物件。若要確保在本機首頁控制器內正確解析這些參考，**bind** 為必要命令。

建立首頁控制器
--------------

您現在必須建立首頁控制器，來處理工作清單網站的所有要求。執行下列步驟來建立控制器：

1.  在 [記事本] 中建立新的 home.js 檔案。此檔案將包含可傳遞工作清單邏輯的控制器程式碼。

        PS C:\node\tasklist\WebRole1> notepad home.js

2.  以下列程式碼取代內容，並儲存檔案。以下程式碼使用 javascript 模組模式。它會匯出 Home 函數。Home 原型包含處理實際要求的函數。

        var azure=require('azure');
        module.exports = Home;

        function Home (client) {
        this.client = client;
        };

        Home.prototype = {
        showItems:function(req, res) {
        var self = this;
        this.getItems(false, function (resp, tasklist) {
        if (!tasklist) {
        tasklist = [];
                    }           
        self.showResults(res, tasklist);
                });
            },

        getItems:function (allItems, callback) {
        var query = azure.TableQuery
        .select()
        .from('tasks');
                
        if (!allItems) {
        query = query.where('completed eq 
                    ', 'false');
                }
        this.client.queryEntities(query, callback);
             },

        showResults:function (res, tasklist) {
        res.render('home', { 
        title:'Todo list', 
        layout :false, 
        tasklist:tasklist });
             },
        };

    您的首頁控制器現在包括三個函數：

    -   *showItems* 會處理要求。
    -   *getItems* 會使用資料表用戶端，來從您的工作資料表中擷取公開的工作項目。請注意，您可以在查詢上套用其他篩選條件；例如，上述查詢篩選條件僅會顯示已完成等於 false 的工作。
    -   *showResults* 會呼叫 Express render 函數，使用您將在下一節中建立的首頁檢視來轉譯頁面。

### 修改首頁檢視

Jade 範本引擎會使用比 HTML 簡要的標記語法，而且它是使用 Express 的預設引擎。執行下列步驟，以建立支援顯示工作清單項目的檢視：

1.  在 Windows PowerShell 命令視窗中，使用下列命令來編輯 home.jade 檔案：

        PS C:\node\tasklist\WebRole1\views> notepad home.jade

2.  以下列程式碼取代 home.jade 檔案的內容，並儲存檔案。以下表單包含了讀取與更新工作項目的功能。(請注意，目前首頁控制器僅支援讀取，稍後您將變更此選項。)此表單會包含工作清單中每一項目的詳細資料。

        html
        head
        title Index
        body
        h1 My ToDo List

        form
        table(border="1")
        tr
        td Name
        td Category
        td Date
        td Complete

        each item in tasklist
        tr
        td #{item.name}
        td #{item.category} 
        td #{item.date} 
        td 
        input(type="checkbox", name="completed", value="#{item.RowKey}") 

在計算模擬器中執行應用程式
--------------------------

1.  在 Windows PowerShell 視窗中，請輸入下列 Cmdlet 以便在計算模擬器中啟動服務，並顯示可呼叫此服務的網頁。

        PS C:\node\tasklist\WebRole1> Start-AzureEmulator -launch

    您的瀏覽器會顯示下列網頁，顯示從 Azure 儲存體中擷取的工作項目：

    ![Internet explorer displaying a My Tasklist page with one item in a table.](./media/storage-nodejs-use-table-storage-cloud-service-app/node40.png)

新增工作功能
------------

在本節中，您將更新應用程式以支援新增工作項目。

### 將新路由新增至 Server.js

在 server.js 檔案中，於 **/home** 的最後一個路由項目之後加上下列程式碼行，然後儲存檔案。

![The server.js file with the line containing the route for home highlighted.](./media/storage-nodejs-use-table-storage-cloud-service-app/node41.png)

        app.post('/home/newitem', home.newItem.bind(home));

    The routes section should now look as follows:

    // Routes

    var home = new Home(client);
    app.get('/', home.showItems.bind(home));
    app.get('/home', home.showItems.bind(home));
    app.post('/home/newitem', home.newItem.bind(home));

### 新增 Node-UUID 模組

若要使用 node-uuid 模組建立唯一識別碼，在 home.js 檔案頂端匯出模組的第一行之後新增下列程式碼行。

![The home.js file with the line module.exports = Home highlighted.](./media/storage-nodejs-use-table-storage-cloud-service-app/node42.png)

       var uuid = require('node-uuid');

### 將新項目函數新增至首頁控制器

若要實作新增項目功能，請建立 **newItem** 函數。在您的 home.js 檔案中，在最後一個函數之後貼上下列程式碼，然後儲存檔案。

![The showresults function is highlighted](./media/storage-nodejs-use-table-storage-cloud-service-app/node43.png)

       newItem:function(req, res) {
    var self = this;
    var createItem = function (resp, tasklist) {
    if (!tasklist) {
    tasklist = [];
               }

    var count = tasklist.length;

    var item = req.body.item;
    item.RowKey = uuid();
    item.PartitionKey = 'partition1';
    item.completed = false;

    self.client.insertEntity('tasks', item, function (error) {
    if (error) {  
    throw error;
                   }
    self.showItems(req, res);
               });
           };

    this.getItems(true, createItem);
       },

**newItem** 函數可執行下列工作：

-   從主體中擷取已發佈項目。
-   設定新項目的 **RowKey** 和 **PartitionKey** 值。若要將項目插入 Azure 資料表，這些是必要值。產生 **RowKey** 值的 UUID。
-   透過呼叫 **insertEntity** 函數，將項目插入工作資料表。
-   透過呼叫 **getItems** 函數來轉譯頁面。

### 將新項目表單新增至首頁檢視

現在，您可以透過新增表單來更新檢視，以允許使用者新增項目。在 home.jade 檔案中，在檔案結束位置貼上下列程式碼並儲存。

**注意**

在 Jade 中，空白字元具有特殊意義，因此請勿移除以下任何空格。

        hr
    form(action="/home/newitem", method="post")
    table(border="1")    
    tr
    td Item Name: 
    td 
    input(name="item[name]", type="textbox")
    tr
    td Item Category: 
    td 
    input(name="item[category]", type="textbox")
    tr
    td Item Date: 
    td 
    input(name="item[date]", type="textbox")
    input(type="submit", value="Add item")

### 在模擬器中執行應用程式

1.  由於 Azure 模擬器已在執行中，您可以瀏覽更新的應用程式：

        PS C:\node\tasklist\WebRole1> start http://localhost:81/home

    瀏覽器會開啟並顯示下列頁面：

    ![A web paged titled My Task List with a table containing tasks and fields to add a new task.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

2.  在 **[項目名稱：]** 中輸入"New task functionality"、在 **[項目類別：]** 中輸入"Site work"? 及在 **[項目日期：]** 中輸入"12/02/2011". 然後按一下 **[新增項目]**。

    此項目便會新增至 Azure 儲存體的工作資料表中，並如下面螢幕擷取畫面所示般顯示。

    ![A web page titled My Task List with a table containing tasks, after you have added a task to the list.](./media/storage-nodejs-use-table-storage-cloud-service-app/node45.png)

將應用程式重新發行至 Azure
--------------------------

現在，應用程式已完成，您可以將它發行至 Azure，方法是將部署更新成現有的託管服務。

1.  在 Windows PowerShell 視窗中，呼叫下列 Cmdlet 以將您的託管服務重新部署至 Azure。先前已儲存您的儲存體設定和位置，您無需重新輸入。

        PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

    部署完成之後，您應該會看到類似這樣的回應：

    ![the status messages displayed during deployment.](./media/storage-nodejs-use-table-storage-cloud-service-app/node35.png)

    和之前一樣，因為您指定 **-launch** 選項，所以當發行完成時，瀏覽器便會開啟並顯示在 Azure 中執行的應用程式。

    ![A browser window displaying the My Task List page.The URL indicates the page is now being hosted on Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/node47.png)

停止並刪除您的應用程式
----------------------

在部署應用程式之後，您可能會想要將它停用以避免成本，或在免費試用時間間隔內建置並部署其他應用程式。

Azure 會就每小時伺服器時間所使用的 Web 角色執行個體數進行收費。一旦部署應用程式，即會耗用伺服器時間，即使執行個體不在執行中，並處於停止狀態也一樣。

下列步驟示範如何停止並刪除應用程式。

1.  在 Windows PowerShell 視窗中，使用下列 Cmdlet 停止上一個小節中建立的服務部署：

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    停止服務可能需要幾分鐘的時間。服務停止時，您將收到表示停止的訊息。

    ![Status messages indicating the service has stopped.](./media/storage-nodejs-use-table-storage-cloud-service-app/node48.png)

2.  若要刪除服務，請呼叫下列 Cmdlet：

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    出現提示時，輸入 **Y** 以刪除服務。

    刪除服務可能需要幾分鐘的時間。刪除服務後，您將收到表示已刪除服務的訊息。

    ![Status messages indicating the service has been deleted.](./media/storage-nodejs-use-table-storage-cloud-service-app/node49.png)


