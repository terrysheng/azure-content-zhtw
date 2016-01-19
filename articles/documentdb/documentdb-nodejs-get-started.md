<properties
	pageTitle="DocumentDB 的 NoSQL Node.js 教學課程 | Microsoft Azure"
	description="NoSQL Node.js 教學課程，將使用 DocumentDB Node.js SDK 來建立節點資料庫以及主控台應用程式。DocumentDB 是 JSON 的 NoSQL 資料庫。"
    keywords="node.js 教學課程，節點資料庫"
	services="documentdb"
	documentationCenter="node.js"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="hero-article" 
	ms.date="11/18/2015"
	ms.author="anhoh"/>

# NoSQL Node.js 教學課程：DocumentDB Node.js 主控台應用程式  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

歡迎使用 DocumentDB Node.js SDK 的 Node.js 教學課程！ 完成本教學課程之後，您將會有一個主控台應用程式，可用來建立和查詢 DocumentDB 資源，包括 Node 資料庫。

本文將討論：

- 建立和連接到 DocumentDB 帳戶
- 設定您的應用程式
- 建立節點資料庫
- 建立集合
- 建立 JSON 文件
- 查詢集合
- 刪除節點資料庫

沒有時間嗎？ 別擔心！ 您可以在 [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started) 上找到完整的方案。請參閱[取得完整的解決方案](#GetSolution)，以取得簡要指示。

完成 Node.js 教學課程之後，請使用此頁面頂端和底部的投票按鈕來提供意見。如果想要我們直接與您連絡，歡迎在留言中留下電子郵件地址。

讓我們開始吧！

## 必要條件

請確定您具有下列項目：

- 使用中的 Azure 帳戶。如果您沒有帳戶，您可以註冊[免費 Azure 試用](http://azure.microsoft.com/pricing/free-trial/)。
- [Node.js](https://nodejs.org/) v0.10.29 版或更高版本。

## 步驟 1：建立 DocumentDB 帳戶

讓我們建立 DocumentDB 帳戶。如果您已經擁有想要使用的帳戶，就可以跳到[設定您的 Node.js 應用程式](#SetupNode)。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupNode"></a> 步驟 2：設定您的 Node.js 應用程式

1. 開啟您偏好的終端機。
2. 找出您想儲存 Node.js 應用程式的資料夾或目錄位置。
3. 使用下列命令，建立兩個空白的 JavaScript 檔案：
	- Windows:    
	    * ```fsutil file createnew app.js 0```
        * ```fsutil file createnew config.js 0```
	- Linux/OS X： 
	    * ```touch app.js```
        * ```touch config.js```
4. 透過 npm 安裝 documentdb 模組。使用下列命令：
    * ```npm install documentdb --save```

太棒了！ 現在已完成安裝程式，讓我們開始撰寫一些程式碼。

##<a id="Config"></a> 步驟 3：設定您的應用程式組態

在您慣用的文字編輯器中開啟 ```config.js```。

接著，建立一個名為 ```config``` 的空白物件，將屬性 ```config.endpoint``` 和 ```config.authKey``` 設定為您的 DocumentDB 端點和授權金鑰。您可以在 [Azure 入口網站](https://portal.azure.com)找到這兩個設定。

![顯示 DocumentDB 帳戶的 Azure 入口網站螢幕擷取畫面，內含反白顯示的 [主動式] 集線器、[DocumentDB 帳戶] 刀鋒視窗上反白顯示的 [金鑰] 按鈕、[金鑰] 刀鋒視窗上反白顯示的 [URI]、[主要金鑰] 和 [次要金鑰] 值][keys]

    var config = {}

    config.endpoint = "https://YOUR_ENDPOINT_URI.documents.azure.com:443/";
    config.authKey = "oqTveZeWlbtnJQ2yMj23HOAViIr0ya****YOUR_AUTHORIZATION_KEY****ysadfbUV+wUdxwDAZlCfcVzWp0PQg==";

現在讓我們將 ```database id```、```collection id``` 和 ```JSON documents``` 新增到 ```config``` 物件。在您設定 ```config.endpoint``` 和 ```config.authKey``` 屬性的位置下面，新增下列程式碼。如果您已經有想要儲存於資料庫中的資料，就可以使用 DocumentDB 的[資料移轉工具](documentdb-import-data.md)，而不用新增文件定義。

    config.dbDefinition = {"id": "FamilyRegistry"};
    config.collDefinition = {"id": "FamilyCollection"};

    var documents = {
      "Andersen": {
        "id": "AndersenFamily",
        "LastName": "Andersen",
        "Parents": [
          {
            "FirstName": "Thomas"
          },
          {
            "FirstName": "Mary Kay"
          }
        ],
        "Children": [
          {
            "FirstName": "Henriette Thaulow",
            "Gender": "female",
            "Grade": 5,
            "Pets": [
              {
                "GivenName": "Fluffy"
              }
            ]
          }
        ],
        "Address": {
          "State": "WA",
          "County": "King",
          "City": "Seattle"
        }
      },
      "Wakefield":   {
          "id": "WakefieldFamily",
          "Parents": [
            {
              "FamilyName": "Wakefield",
              "FirstName": "Robin"
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Ben"
            }
          ],
          "Children": [
            {
              "FamilyName": "Merriam",
              "FirstName": "Jesse",
              "Gender": "female",
              "Grade": 8,
              "Pets": [
                {
                  "GivenName": "Goofy"
                },
                {
                  "GivenName": "Shadow"
                }
              ]
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Lisa",
              "Gender": "female",
              "Grade": 1
            }
          ],
          "Address": {
            "State": "NY",
            "County": "Manhattan",
            "City": "NY"
          },
          "IsRegistered": false
        }
    };

    config.docsDefinitions = documents;

資料庫、集合和文件定義會作為您 DocumentDB 的```database id```、```collection id```和文件資料。

最後，匯出您的 ```config``` 物件，如此就可以在 ```app.js``` 檔案中參考它。

    module.exports = config;

##<a id="Connect"></a> 步驟 4：連接到 DocumentDB 帳戶

在文字編輯器中開啟您的空白 ```app.js``` 檔案。匯入 ```documentdb``` 模組和新建的 ```config``` 模組。

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");

接下來，我們將使用先前儲存的 ```config.endpoint``` 和 ```config.authKey``` 來建立新的 DocumentClient。

    var client = new documentClient(config.endpoint, {"masterKey": config.authKey});

現在既然您已連接到 DocumentDB 帳戶，讓我們看看如何使用 DocumentDB 資源。

## 步驟 5：建立節點資料庫
可以使用 **DocumentClient** 類別的 [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 函式來建立[資料庫](documentdb-resources.md#databases)。資料庫是分割給多個集合之文件儲存體的邏輯容器。以 ```config``` 物件中指定的 ```id```，在 app.js 檔案中加入建立新資料庫的函式。我們會先檢查確定具有相同 ```FamilyRegistry``` 識別碼的資料庫不存在。如果存在，我們會傳回該資料庫，而不會建立新資料庫。

    var getOrCreateDatabase = function(callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.dbDefinition.id
            }]
        };

        client.queryDatabases(querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createDatabase(config.dbDefinition, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateColl"></a>步驟 6：建立集合  

> [AZURE.WARNING]**CreateDocumentCollectionAsync** 會建立具有定價含意的新 S1 集合。如需詳細資訊，請造訪[定價頁面](https://azure.microsoft.com/pricing/details/documentdb/)。

可以使用 **DocumentClient** 類別的 [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 函式建立[集合](documentdb-resources.md#collections)。集合是 JSON 文件和相關聯 JavaScript 應用程式邏輯的容器。新建立的集合將會對應至 [S1 效能層級](documentdb-performance-levels.md)。以 ```config``` 物件中指定的 ```id```，在 app.js 檔案中加入建立新集合的函式。同樣地，我們會檢查確定具有相同 ```FamilyCollection``` 識別碼的集合不存在。如果存在，我們會傳回該集合，而不會建立新集合。

    var getOrCreateCollection = function(callback) {

        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.collDefinition.id
            }]
        };

        var dbUri = "dbs/" + config.dbDefinition.id;

        client.queryCollections(dbUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createCollection(dbUri, config.collDefinition, function(err, created) {
                    if(err) return callback(err);
                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateDoc"></a>步驟 7：建立文件
可以使用 **DocumentClient** 類別的 [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 函式來建立[文件](documentdb-resources.md#documents)。文件會是使用者定義的 (任意) JSON 內容。您現在可以將文件插入至 DocumentDB。

接下來，將建立文件 (包含儲存在 ```config``` 物件中的 JSON 資料) 的函式加入至 app.js。同樣地，我們會檢查以確定具有相同識別碼的文件不存在。

    var getOrCreateDocument = function(document, callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: document.id
            }]
        };

        var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

        client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if(results.length === 0) {
                client.createDocument(collectionUri, document, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            } else {
                callback(null, results[0]);
            }
        });
    };

恭喜！ 您現在有可以在 DocumentDB 中建立資料庫、集合和文件的函式了！

![說明帳戶、資料庫、集合和文件之間階層式關聯性的圖表](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>步驟 8：查詢 DocumentDB 資源

DocumentDB 支援對儲存於每個集合的 JSON 文件進行[豐富查詢](documentdb-sql-query.md)。下列範例程式碼示範您可以針對集合中之文件執行的查詢。在 ```app.js``` 檔案中新增下列函數。DocumentDB 支援類 SQL 查詢，如下所示。如需建立複雜查詢的詳細資訊，請參閱[查詢遊樂場](https://www.documentdb.com/sql/demo)和[查詢文件](documentdb-sql-query.md)。

    var queryCollection = function(documentId, callback) {
      var querySpec = {
          query: 'SELECT * FROM root r WHERE r.id=@id',
          parameters: [{
              name: '@id',
              value: documentId
          }]
      };

      var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

      client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
          if(err) return callback(err);

          callback(null, results);
      });
    };

下圖說明如何針對您所建立的集合呼叫 DocumentDB SQL 查詢語法。

![說明查詢範圍和意義的圖表](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

因為 DocumentDB 查詢已侷限於單一集合，所以查詢中的 [FROM](documentdb-sql-query.md/#from-clause) 關鍵字是選擇性的。因此，"FROM Families f" 可以換成 "FROM root r"，或您選擇的任何其他變數名稱。依預設，DocumentDB 會推斷該系列、根或您選擇的變數名稱來參考目前的集合。

##<a id="DeleteDatabase"></a>步驟 9：刪除節點資料庫

刪除已建立的資料庫將會移除資料庫和所有子系資源 (集合、文件等)。您可以加入下列程式碼片段來刪除資料庫。

    // WARNING: this function will delete your database and all its children resources: collections and documents
    function cleanup(callback) {
        client.deleteDatabase("dbs/" + config.dbDefinition.id, function(err) {
            if(err) return callback(err);

            callback(null);
        });
    }

##<a id="Build"></a>步驟 10：組合在一起

既然您已經將應用程式所有必要的功能設定好，來呼叫它們吧！

函式呼叫的順序會是 * *getOrCreateDatabase* * *getOrCreateCollection* * *getOrCreateDocument* * *getOrCreateDocument* * *queryCollection* * *cleanup*

將下列程式碼片段加入至您在 ```app.js``` 中的程式碼底部。

    getOrCreateDatabase(function(err, db) {
        if(err) return console.log(err);
        console.log('Read or created db:\n' + db.id + '\n');

        getOrCreateCollection(function(err, coll) {
            if(err) return console.log(err);
            console.log('Read or created collection:\n' + coll.id + '\n');

            getOrCreateDocument(config.docsDefinitions.Andersen, function(err, doc) {
                if(err) return console.log(err);
                console.log('Read or created document:\n' + doc.id + '\n');

                getOrCreateDocument(config.docsDefinitions.Wakefield, function(err, doc) {
                    if(err) return console.log(err);
                    console.log('Read or created document:\n' + doc.id + '\n');

                    queryCollection("AndersenFamily", function(err, results) {
                        if(err) return console.log(err);
                        console.log('Query results:\n' + JSON.stringify(results, null, '\t') + '\n');

                        cleanup(function(err) {
                            if(err) return console.log(err);
                            console.log('Done.');
                        });
                    });
                });
            });
        });
    });

##<a id="Run"></a>步驟 11：執行您的 Node.js 應用程式！

您現在可以開始執行您的 Node.js 應用程式！

在終端機中，找到您的 ```app.js``` 檔案並執行命令：```node app.js```

您應該可以看到入門應用程式的輸出。輸出應該會與下列範例文字相符。

    Read or created db:
    FamilyRegistry

    Read or created collection:
    FamilyCollection

    Read or created document:
    AndersenFamily

    Read or created document:
    WakefieldFamily

    Query results:
    [
            {
                    "id": "AndersenFamily",
                    "LastName": "Andersen",
                    "Parents": [
                            {
                                    "FirstName": "Thomas"
                            },
                            {
                                    "FirstName": "Mary Kay"
                            }
                    ],
                    "Children": [
                            {
                                    "FirstName": "Henriette Thaulow",
                                    "Gender": "female",
                                    "Grade": 5,
                                    "Pets": [
                                            {
                                                    "GivenName": "Fluffy"
                                            }
                                    ]
                            }
                    ],
                    "Address": {
                            "State": "WA",
                            "County": "King",
                            "City": "Seattle"
                    },
                    "_rid": "tOErANjwoQcBAAAAAAAAAA==",
                    "_ts": 1444327141,
                    "_self": "dbs/tOErAA==/colls/tOErANjwoQc=/docs/tOErANjwoQcBAAAAAAAAAA==/",
                    "_etag": ""00001200-0000-0000-0000-5616aee50000"",
                    "_attachments": "attachments/"
            }
    ]

    Done.

恭喜！ 您已完成 Node.js 教學課程，和擁有您的第一個 DocumentDB 主控台應用程式！

##<a id="GetSolution"></a>取得完整的方案
若要建置包含本文中所有範例的 GetStarted 方案，您將需要下列項目：

-   [DocumentDB 帳戶][documentdb-create-account]。
-   您可以在 GitHub 上找到 [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) 方案。

透過 npm 安裝 **documentdb** 模組。使用下列命令：* ```npm install documentdb --save```

接下來，將 ```config.js``` 檔案中的 config.endpoint 和 config.authKey 的值更新為如[步驟 3：設定您的應用程式組態](#Config)中所述。

## 後續步驟

-   需要更複雜的 Node.js 範例嗎？ 請參閱[使用 DocumentDB 建置 Node.js Web 應用程式](documentdb-nodejs-application.md)。
-	了解如何[監視 DocumentDB 帳戶](documentdb-monitor-accounts.md) (英文)。
-	在 [Query Playground](https://www.documentdb.com/sql/demo) 中，針對範例資料集執行查詢。
-	如需深入了解程式設計模型，請參閱 [DocumentDB 文件頁面](../../services/documentdb/)中的＜開發＞一節。

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

<!---HONumber=AcomDC_0114_2016-->