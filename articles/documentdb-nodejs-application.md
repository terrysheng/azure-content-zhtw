<properties title="Build a Node.js web application using DocumentDB" pageTitle="Build a Node.js web application using DocumentDB | Azure" description="Learn how to use Azure DocumentDB to store and access data from a Node.js application hosted on Azure." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="ryancraw" />

# <a name="_Toc395783175">使用 DocumentDB 建置 Node.js Web 應用程式</a>

<a name="_Toc395783175">

本教學課程說明如何使用 Azure DocumentDB 服務，
從託管於 Azure 網站上的 Node.js Express 應用程式儲存和存取資料。

本教學課程假設您先前已有些許使用 Node.js 的經驗。

您將了解：

· 如何使用 Node.js Tools for Visual Studio

· 如何透過 documentdb npm 模組使用 Azure DocumentDB 服務

· 如何將 Web 應用程式部署至 Azure 網站

按照本教學課程進行後，您將建置簡單的網頁型
工作管理應用程式，以便建立、擷取和
完成工作。在 Azure DocumentDB 中，這些工作將會儲存為
JSON 文件。

![替代文字][0]

## <a name="_Toc395783176">必要條件</a>

在依照本文中的指示進行之前，您應該確定
已安裝下列項目：

[Node.js][Node.js] v0.10.29 版或更高版本

[Git][Git]

[Visual Studio 2013][Visual Studio 2013] (含 Update 3)

[Node.js Tools for Visual Studio][Node.js Tools for Visual Studio]

**注意：**在本教學課程中，雖然我們使用 Visual Studio 來建置、偵錯和部署
Node.js 專案，但是您可以按照平時執行 Node.js 專案的方式，使用慣用的編輯器
並直接在選擇的平台上執行 Node.js。
接著，您可以使用 [Azure CLI][Azure CLI] 工具
將應用程式部署至 Azure 網站。

# <a name="_Toc395637761">建立 DocumentDB 資料庫帳戶</a>

若要在 Azure 中佈建 DocumentDB 資料庫帳戶，請開啟 [Azure 管理入口網站][Azure 管理入口網站]
，並按一下首頁上的 [Azure Gallery] 磚，或按一下畫面左下角的 "+"。

![替代文字][1]

這會開啟 [Azure Gallery]，您可以在其中從多個可用 Azure 服務
進行選取。在 Gallery 中，從類別清單中選取 [Data, storage and backup]。

![替代文字][2]

從這裡，選取 Azure DocumentDB 的選項

![替代文字][3]

然後選取畫面底部的 [建立]

![替代文字][4]

這會開啟 [New DocumentDB] 刀鋒，您可以在其中指定
新帳戶的名稱、區域、規模、資源群組
和其他設定。

![替代文字][5]

提供好帳戶值之後，按一下 [建立]，佈建程序
即會開始建立資料庫帳戶。佈建程序完成時，
您會在入口網站的通知區域中看到通知，而且
開始畫面上的磚 (如果您已選擇建立磚) 將會變更成
顯示已完成的動作。

![替代文字][6]

佈建完成之後，按一下開始畫面中的 [DocumentDB] 磚
，這個新建立 DocumentDB 帳戶的
主要刀鋒就會出現。

![替代文字][7]
![替代文字][8]

使用 [金鑰] 按鈕，存取端點 URL 和主要金鑰，
將這些項目複製到剪貼簿並將它們保存在隨手可得的地方，
因為我們將在接下來要建立的 Web 應用程式中使用這些值。

## <a name="_Toc395783178">建立新的 Node.js 應用程式</a>

在 Visual Studio 中，選取 [檔案] – [新增專案]，然後選擇建立 [Basic Microsoft Azure Express Application]。

![替代文字][9]

這會為您建立基本 Express 應用程式。如果系統
提示您 [install dependencies]，請選取 [是]。這樣就會安裝
新的 Express 應用程式所需的所有 npm 封裝。

這項作業完成之後，[方案總管] 應該會與下面
類似；

![替代文字][10]

這代表您已安裝 Express、Jade 和 Stylus。

如果您在 Visual Studio 中按 F5 鍵，則應該會建置專案，並啟動
Node.js，然後顯示內含 Express 版 "Hello
World" 的瀏覽器。

![替代文字][11]

## <a name="_Toc395783179">安裝其他模組</a>

**package.json** 檔案是建立在專案根目錄中的
其中一個檔案。這個檔案包含 Express 應用程式需要的
額外模組清單。之後，當您部署此應用程式至
Azure 網站，此檔案將用於決定 Azure 上需要安裝哪些模組來支援
您的應用程式。

![替代文字][12]

在本教學課程中，我們需要再安裝兩個封裝。

在 [方案總管] 中，於 [npm] 上按一下滑鼠右鍵，然後選取 [Install npm Packages]。

![替代文字][13]

在 [Install New npm Packages] 對話方塊中，輸入 **nconf** 以搜尋該模組。
應用程式將使用此模組來讀取組態檔中的
資料庫組態值。

![替代文字][14]

最後，搜尋 **documentdb**，使用相同方式安裝
Azure DocumentDB 模組。此模組可發揮 DocumentDB 的所有強大功能。

這兩個額外模組以及依存項目在安裝好之後，
應該會出現在 [方案總管] 的 [npm] 模組下方。

![替代文字][15]

快速檢查應用程式的 **package.json** 檔案時，應該會
顯示額外的模組。此檔案之後會指示 Azure
在執行應用程式時，需要下載和安裝哪些封裝。

視需要編輯 package.json 檔案，使其與下面範例類似。

![替代文字][16]

這會讓 Node (之後則是 Azure) 知道您的應用程式需要仰賴這些
額外模組。

# <a name="_Toc395783180">在節點應用程式中使用 DocumentDB 服務</a>

前面的內容在講述所有初始設定和組態，現在讓我們來了解本教學課程的真正目的，也就是使用 Azure DocumentDB 撰寫一些程式碼。 一開始先編輯 **app.js**，其位於剛剛建立之 Express 應用程式的根目錄之中。 在檔案中，找到下列兩行；

    app.get('/', routes.index);
    app.get('/users', user.list);

並取代為下列兩行；

    app.get('/', routes.index);
    app.post('/', routes.createOrUpdateItem);

這兩行會讓應用程式知道如何處理我們接下來要建立之表單上的預設 GET 和 POST 方法。

現在，在 **routes** 資料夾中找到 **index.js** 檔案。在編輯器中開啟此檔案，並刪除此檔案中的所有程式碼。

在檔案開頭處新增下列程式碼；

    // import the modules we will use
    var DocumentDBClient = require('documentdb').DocumentClient;
    var nconf = require('nconf');

    // tell nconf which config file to use
    nconf.env();
    nconf.file({ file: 'config.json' });

此程式碼定義將要利用的模組，這些模組分別是 **documentdb** 和 **nconf**。

**nconf** 這個模組可讓您從外部檔案載入組態值 (例如資料庫連接字串)，而不是將這些值內嵌於程式碼中。nconf 預設會在 **config.json** 中尋找其組態。

接著繼續在專案的根目錄 (與 app.js 的位置相同) 中建立空白文字檔 **config.json**。

開啟這個新的 **config.json** 檔案，並為 DocumentDB 端點適當輸入下列值。請務必要正確設定 HOST 和 MASTER\_KEY 值。

    {
        "HOST"       : "<insert your DocDB endpoint here>",
        "AUTH_KEY"   : "<insert either primary or secondary key here>",
        "DATABASE"   : "ToDoList",
        "COLLECTION" : "Items"
    }

現在回到 **index.js**，並在最後幾行後面新增下列幾行，以實際讀取組態檔，然後將值儲存至頁面層級變數。

    var host = nconf.get("HOST");
    var authKey = nconf.get("AUTH_KEY");
    var databaseId = nconf.get("DATABASE");
    var collectionId = nconf.get("COLLECTION");

現在，編輯作業已完成，請繼續將下列程式碼新增至 **index.js**。

    // create some global variables which we will use later to hold instances of the DocumentDBClient, Database and Collection

    // create an instance of the DocumentDB client
    var client = new DocumentDBClient(host, { masterKey: authKey });

    exports.index = function (req, res) {       
        // before we can query for Items in the document store, we need to ensure we 
        // have a database with a collection then use the collection to read the documents
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                listItems(collection, function (items) {
                    res.render('index', { title: 'My ToDo List', tasks: items });
                });    
            });
        });
    };

    exports.createOrUpdateItem = function (req, res) {
        //first have to set the database & collection context so that we have the self links   
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                        
                //if we find an item on the form, we'll create it in the database
                var item = req.body.item;
                if (item) {
                    createItem(collection, item, function () {
                    res.redirect('/');
                });
                
                //else let's look for items marked as completed, 
                //and update that item in the database
                } else {
                    var completed = req.body.completed;
                    
                    //check if completed is actually an Array, if not make it one. 
                    //this happens when you select only one item            
                    if (!completed.forEach)
                        completed = [completed];
                    
                    //use a recursive function to loop through items in 
                    //array calling updateItem each time through                                    
                    function updater(i) {
                        if (i < completed.length) {
                            updateItem(collection, completed[i], function () {
                                updater(i + 1);
                            });
                        } else {
                            res.redirect('/');
                        }
                    }
                    
                    //kick off the recursion
                    updater(0);
                }
            });
        });
    }

這些是先前定義路由時，在 **app.js** 中告知應用程式使用的兩個函數。GET 叫用索引檢視時，將會執行 **exports.index** 函數，同樣地，索引檢視收到 POST 時，將會執行 **exports.createOfUpdateItem** 函數。

這兩個函數會執行我們所建置之應用程式的所有工作，不過，它們還會呼叫其他函數，但純粹是要讓程式碼更容易閱讀而且容易追蹤。請繼續將下列程式碼新增至 **index.js** 檔案。這個程式碼包含上面兩個函數所使用的所有方法，以及包含所有 DocumentDB 呼叫。稍後我們會更詳細地逐步解說每個函數。

    // update item
    var updateItem = function (collection, itemId, callback) {
        //first fetch the document based on the id
        getItem(collection, itemId, function (doc) {
            //now replace the document with the updated one
            doc.completed = true;
            client.replaceDocument(doc._self, doc, function (err, replacedDoc) {
                if (err) {
                    throw (err);
                }
                
                callback();
            });
        });
    }

    // get item
    var getItem = function (collection, itemId, callback) {      
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.id="' + itemId + '"').toArray(function (err, results) {
            if (err) {
                throw (err);
            }

            callback(results[0]);
        });
    }

    // create new item
    var createItem = function (collection, documentDefinition, callback) {
        documentDefinition.completed = false;
        client.createDocument(collection._self, documentDefinition, function (err, doc) {
            if (err) {
                throw (err);
            }
            
            callback();
        });
    }

    // query the provided collection for all non-complete items
    var listItems = function (collection, callback) {
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.completed=false').toArray(function (err, docs) {
            if (err) {
                throw (err);
            }
            
            callback(docs);
        });
    }

    // if the database does not exist, then create it, else return the database object
    var readOrCreateDatabase = function (callback) {
        client.queryDatabases('SELECT * FROM root r WHERE r.id="' + databaseId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                // indicating no database exists matching the query            
                client.createDatabase({ id: databaseId }, function (err, createdDatabase) {
                    callback(createdDatabase);
                });
            } else {
                // we found a database
                callback(results[0]);
            }
        });
    };

    // if the collection does not exist for the database provided, create it, else return the collection object
    var readOrCreateCollection = function (database, callback) {
        client.queryCollections(database._self, 'SELECT * FROM root r WHERE r.id="' + collectionId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }           
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                //indicating no collection exists in the provided database matching the query
                client.createCollection(database._self, { id: collectionId }, function (err, createdCollection) {
                    callback(createdCollection);
                });
            } else {
                // we found a collection
                callback(results[0]);
            }
        });
    };

**updateItem** – 根據從表單傳入的 [項目 ID]，來更新資料庫中的文件。它使用此 ID 對 DocumentDB 執行 ***readDocument*** 方法，以讀取我們已儲存的特定文件。接著，會將文件的 [已完成] 屬性變更為 true，指出現已完成作業，然後繼續取代資料庫中的文件。

**getItem –** 使用 ***queryDocuments***，利用項目的 id 屬性取得資料庫中的單一項目。

**createItem** – 使用 ***createDocument*** 方法，根據在表單上輸入的 [項目名稱] 和 [項目類別]，於資料庫中建立新的文件。它也會將 [已完成] 旗標設定為 false，指出此項目尚未完成。

**listItems** – 使用 ***queryDocuments*** 尋找集合中所有尚未完成的文件，或 [已完成] = false 的文件。它使用根據 ANSI-SQL 的 DocumentDB 查詢文法，來示範這個常見但功能強大的查詢功能。

**readOrCreateDatabase** – 使用 ***queryDatabases***，確認是否已有此名稱的資料庫存在。如果沒有，則接著使用 ***createDatabase*** 在指定的端點 (來自組態檔) 建立具有所提供識別碼 (也是來自組態檔) 的新資料庫。

**readOrCreateCollection** – 與 readOrCreateDatabase 一樣，此方法會先嘗試尋找具有所提供識別碼的集合，有找到就加以傳回，如果沒有找到，則為您建立一個。

以上就是此範例應用程式與 DocumentDB 互動時所需的所有程式碼。

現在，讓我們將注意力轉到建置使用者介面，以便使用者可以實際與我們的應用程式互動。我們建立的 Express 應用程式使用 **Jade** 做為檢視引擎。如需 Jade 的詳細資訊，請參閱 <http://jade-lang.com/> (英文)。

開啟在 **views** 資料夾中找到的 **layout.jade** 檔案，並將其中的內容取代為下列內容；

    doctype html
    html
      head
        title= title
        meta(http-equiv='X-UA-Compatible', content='IE=10')
        link(rel='stylesheet', href='/stylesheets/style.css')
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
      body.app
        nav.navbar.navbar-fixed-top
          div.navbar-inner
            container
                a.brand(href='/') My Tasks
        block content

這個程式碼實際上會指示 **Jade** 引擎呈現我們應用程式的部分 HTML，並建立稱為 "**content**" 的 "**block**"，我們可以在其中提供內容頁面的配置。

現在，開啟 **index.jade** 檔案 (與 index.js 相關聯的檢視)，並將其中的內容取代為下列內容；

    extends layout

    block content
      h1= title
      br

      form(action="/", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Complete
          each task in tasks
            tr
                td #{task.name}
                td #{task.category}
                td 
                    input(type="checkbox", name="completed", value="#{task.id}", checked=(task.completed == true))
        button.btn(type="submit") Update tasks

      hr

      form.well(action="/", method="post")
        label Item Name:  
        input(name="item[name]", type="textbox")
        label Item Category: 
        input(name="item[category]", type="textbox")
        br
        button.btn(type="submit") Add item

這個程式碼會擴充配置，並為我們在前面的 layout.jade 檔案中看到的 "block content"
預留位置提供內容。

在此配置中，我們建立了兩個 HTML 表單。第一個表單有一張存有我們資料的資料表
以及一個可讓我們更新項目的按鈕。第二個表單有
一些輸入欄位以及一個可讓我們建立新項目的按鈕。

按一下上述任一個按鈕時，
便會呼叫我們在 **index.js**
 檔案中所建立的函數 **createOrUpdateItem** (也就是執行表單 POST)。如果此頁面是直接載入 (即執行 GET)，
則會執行 **index** 函數。

這應該就是要讓應用程式開始運作所需的所有程式碼。

</h1>
<a name="_Toc395783181">在本機執行您的應用程式</a>
========================================================

若要在本機電腦上測試應用程式，請在 Visual Studio 中按 F5 鍵，
這樣應該會建置應用程式、啟動 Node.js，以及啟動所含頁面與下圖類似的
瀏覽器：

![替代文字][17]

1\. 使用所提供的 [項目]、[項目名稱] 和 [類別] 欄位來輸入資訊，
然後按一下 [新增項目]。

2\. 系統應該會更新此頁面以在 ToDo List 中
顯示新建立的項目。

![替代文字][18]

3\. 若要完成工作，您只需勾選 [已完成] 資料行中的核取方塊，
然後按一下 [更新工作]。

</h1>
<a name="_Toc395783182">將應用程式部署至 Azure 網站</a>
=====================================================================

安裝好 Node.js Tools for Visual Studio 之後，只要幾個簡短步驟，
就可以輕鬆地在 Azure 網站完成部署。

在專案上按一下滑鼠右鍵，然後選取 [發佈]。

![替代文字][19]

然後遵循發佈精靈，提供
Azure 網站的必要組態。此精靈可讓您選擇更新現有
網站，或是建立新的網站。

提供必要組態之後，按 [發佈] 即可。

![替代文字][20]

然後遵循發佈精靈，提供
Azure 網站的必要組態。此精靈可讓您選擇更新現有
網站，或是建立新的網站。

提供必要組態之後，按 [發佈] 即可。

Visual Studio 就會連線到 Azure 訂用帳戶，並發佈
此 Node.js 應用程式。

幾秒後，Visual Studio 就會發佈好 Web
應用程式並啟動瀏覽器，您可以在其中看到您方便好用的應用程式
已在 Azure 中執行！

# <a name="_Toc395783183"></a> <a name="_Toc395637775">結論</a>

恭喜！您剛剛已經使用 Azure DocumentDB 建置您的第一個Node.js Express Web
應用程式，並將它發佈至 Azure 網站。

您可以在[這裡][這裡]下載完整參考應用程式的原始程式碼。

</h1>

  [0]: ./media/documentdb-nodejs-application/image1.png
  [Node.js]: http://nodejs.org/
  [Git]: http://git-scm.com/
  [Visual Studio 2013]: http://msdn.microsoft.com/zh-tw/vstudio/cc136611.aspx
  [Node.js Tools for Visual Studio]: https://nodejstools.codeplex.com/
  [Azure CLI]: http://azure.microsoft.com/zh-tw/documentation/articles/xplat-cli/
  [Azure 管理入口網站]: http://portal.azure.com
  [1]: ./media/documentdb-nodejs-application/image2.png
  [2]: ./media/documentdb-nodejs-application/image3.png
  [3]: ./media/documentdb-nodejs-application/image4.png
  [4]: ./media/documentdb-nodejs-application/image5.png
  [5]: ./media/documentdb-nodejs-application/image6.png
  [6]: ./media/documentdb-nodejs-application/image7.png
  [7]: ./media/documentdb-nodejs-application/image8.png
  [8]: ./media/documentdb-nodejs-application/image9.png
  [9]: ./media/documentdb-nodejs-application/image10.png
  [10]: ./media/documentdb-nodejs-application/image11.png
  [11]: ./media/documentdb-nodejs-application/image12.png
  [12]: ./media/documentdb-nodejs-application/image13.png
  [13]: ./media/documentdb-nodejs-application/image14.png
  [14]: ./media/documentdb-nodejs-application/image15.png
  [15]: ./media/documentdb-nodejs-application/image16.png
  [16]: ./media/documentdb-nodejs-application/image17.png
  [17]: ./media/documentdb-nodejs-application/image18.png
  [18]: ./media/documentdb-nodejs-application/image19.png
  [19]: ./media/documentdb-nodejs-application/image20.png
  [20]: ./media/documentdb-nodejs-application/image21.png
  [這裡]: http://go.microsoft.com/fwlink/?LinkID=509839&clcid=0x409
