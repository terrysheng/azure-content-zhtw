<properties linkid="dev-nodejs-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage (Node.js) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Node.js, table storage Node.js" description="Learn how to use the table storage service in Azure. Code samples are written using the Node.js API." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Table Service from Node.js" authors="" solutions="" manager="" editor="" />

如何從 Node.js 使用資料表服務
=============================

本指南將示範如何使用 Azure 資料表服務執行一般案例。這些範例使用 Node.js API 撰寫。所涵蓋的案例包括「建立和刪除資料表」、**在資料表中插入及查詢實體**。如需資料表的詳細資訊，請參閱[後續步驟](#next-steps)一節。

目錄
----

-   [什麼是資料表服務？](#what-is)
-   [概念](#concepts)
-   [建立 Azure 儲存體帳戶](#create-account)
-   [建立 Node.js 應用程式](#create-app)
-   [設定您的應用程式以存取儲存體](#configure-access)
-   [設定 Azure 儲存體連接](#setup-connection-string)
-   [作法：建立資料表](#create-table)
-   [作法：將實體加入至資料表](#add-entity)
-   [作法：更新實體](#update-entity)
-   [作法：使用實體群組](#change-entities)
-   [作法：查詢實體](#query-for-entity)
-   [作法：查詢實體集合](#query-set-entities)
-   [作法：查詢實體屬性的子集](#query-entity-properties)
-   [作法：刪除實體](#delete-entity)
-   [作法：刪除資料表](#delete-table)
-   [後續步驟](#next-steps)

什麼是資料表服務？
------------------

Azure 資料表服務可儲存大量的結構化資料。此服務接受來自 Azure 雲端內外經過驗證的呼叫。Azure 資料表很適合儲存結構化、非關聯式資料。資料表服務的一般用途包括：

-   儲存巨量結構化資料 (許多 TB) 且會自動調整以滿足輸送量需求
-   儲存資料集，不需要複雜的聯結、外部索引鍵或預存程序，還可以反正規化以加速存取
-   使用叢集索引來快速查詢資料，例如使用者設定檔

您可以使用資料表服務來儲存和查詢龐大的結構化、非關聯式資料集，且資料表會在資料量增加時調整。

概念
----

資料表服務包含下列元件：

![表格 1](./media/storage-nodejs-how-to-use-table-storage/table1.png)

-   **URL 格式：**程式碼使用此位址格式來定位帳戶中的資料表：

        http://storageaccount.table.core.windows.net/table  

    您可以使用此位址搭配 OData 通訊協定來直接定址 Azure 資料表。如需詳細資訊，請參閱 [OData.org](http://www.odata.org/) (英文)

-   **儲存體帳戶：**一律透過儲存體帳戶來存取 Azure 儲存體。儲存體帳戶中的 blob、資料表及佇列內容的大小總計不能超過 100TB。

-   **資料表**：資料表是無限制的實體集合。資料表不強制規定實體的結構描述，這表示單一資料表包含的實體可以有幾組不同的屬性。帳戶可以包含許多資料表。

-   **實體**：實體是一組屬性，類似於資料庫的資料列。實體的大小上限為 1MB。

-   **屬性**：屬性是名稱/值組。每個實體最多可包含 252 個屬性來儲存資料。每個實體也有三個系統屬性，可指定資料分割索引鍵、資料列索引鍵和時間戳記。具有相同資料分割索引鍵的實體，查詢起來更快，還能在不可部分完成的作業中插入/更新。實體的資料列索引鍵是資料分割內的唯一識別碼。

建立 Azure 儲存體帳戶
---------------------

若要使用儲存體作業，您需要 Azure 儲存體帳戶。您可以依照下列步驟來建立儲存體帳戶。(您也可以[使用 REST API](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh264518.aspx) 來建立儲存體帳戶。)

1.  登入 [Azure 管理入口網站](http://manage.windowsazure.com)。

2.  在導覽窗格的底端，按一下 **[新增]**。

    ![+新增](./media/storage-nodejs-how-to-use-table-storage/plus-new.png)

3.  按一下 **[儲存體帳戶]**，然後按一下 **[快速建立]**。

    ![快速建立對話方塊](./media/storage-nodejs-how-to-use-table-storage/quick-storage.png)

4.  在 [URL] 中，為儲存體帳戶輸入要在 URI 中使用的子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。此值會成為 URI 內用來將訂閱的 Blob、「佇列」或「表格」資源定址的主機名稱。

5.  選擇要將儲存體放置於的「區域/同質群組」。如果您會從 Azure 應用程式使用儲存體，請選取您會在其中部署應用程式的相同區域。

6.  按一下 **[建立儲存體帳戶]**。

建立 Node.js 應用程式
---------------------

建立空白的 Node.js 應用程式。如需建立 Node.js 應用程式的指示，請參閱[建立並部署 Node.js 應用程式至 Azure 網站](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/)、[Node.js 雲端服務]({localLink:2221} "使用 Express 的 Web 應用程式") (使用 Windows PowerShell) 或[使用 WebMatrix 的網站](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/)。

設定您的應用程式以存取儲存體
----------------------------

若要使用 Azure 儲存體，您需要下載並使用 Node.js azure 封裝，這包含一組便利程式庫，能與儲存體 REST 服務通訊。

### 使用 Node Package Manager (NPM) 取得封裝

1.  使用命令列介面，例如 **PowerShell** (Windows)、**[終端機]** (Mac) 或 **Bash** (Unix)，瀏覽到您建立範例應用程式的資料夾。

2.  在命令視窗中輸入 **npm install azure**，這應該會導致下列輸出：

        azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  您可以手動執行 **ls** 命令，確認已建立 **node\_modules** 資料夾。在該資料夾內，您會找到 **azure** 封裝，其中包含您存取儲存體所需的程式庫。

### 匯入封裝

使用記事本或其他文字編輯器，將以下內容新增至您要使用儲存體之應用程式的 **server.js** 檔案頂端：

    var azure = require('azure');

設定 Azure 儲存體連接
---------------------

azure 模組會讀取環境變數 AZURE\_STORAGE\_ACCOUNT 及 AZURE\_STORAGE\_ACCESS\_KEY，以取得連接 Azure 儲存體帳戶所需的資訊。如果未設定這些環境變數，則呼叫 **TableService** 時必須指定帳戶資訊。

如需在 Azure 雲端服務組態檔中設定環境變數的範例，請參閱[使用儲存體的 Node.js 雲端服務](/en-us/develop/nodejs/tutorials/web-app-with-storage/)。

如需在 Azure 網站管理入口網站中設定環境變數的範例，請參閱[使用儲存體的 Node.js Web 應用程式](/en-us/develop/nodejs/tutorials/web-site-with-storage/)。

如何建立資料表
--------------

下列程式碼會建立 **TableService** 物件，並使用該物件建立新資料表。將下列內容新增至接近 **server.js** 的頂端。

    var tableService = azure.createTableService();

對 **createTableIfNotExists** 的呼叫，如果指定的資料表存在便會傳回它，如果尚不存在，則會以指定的名稱建立新的資料表。如果名為 'mytable' 的資料表尚不存在，下列範例便會建立這個新資料表：

    tableService.createTableIfNotExists('mytable', function(error){
        if (!error) {
            // 資料表存在或已建立
        }
    });

### 篩選器

可以將選用性的篩選操作套用到使用 **TableService** 執行的操作。篩選作業可包括記錄、自動重試等等。篩選器是以簽章實作方法的物件：

     function handle (requestOptions, next)

在對要求選項進行前處理之後，方法需要呼叫 "next" 並傳遞具有下列簽章的回呼：

     function (returnObject, finalCallback, next)

在此回呼中，以及處理 returnObject (來自對伺服器之要求的回應) 之後，回呼需要叫用 next (如果存在) 以繼續處理其他篩選，或是就改為叫用 finalCallback 結束服務叫用。

Azure SDK for Node.js 包含了實作重試邏輯的兩個篩選器：**ExponentialRetryPolicyFilter** 和 **LinearRetryPolicyFilter**。以下會建立使用 **ExponentialRetryPolicyFilter** 的 **TableService** 物件：

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableService = azure.createTableService().withFilter(retryOperations);

如何將實體新增至資料表
----------------------

若要新增實體，請先建立定義實體屬性及其資料型別的物件。請注意，對每個實體都必須指定 **PartitionKey** 及 **RowKey**。這些是實體的唯一識別碼，且其值的查詢速度比其他屬性快上許多。系統使用 **PartitionKey** 自動將資料表的實體分散在許多儲存體節點上。具有相同 **PartitionKey** 的實體會儲存在相同節點上。**RowKey** 是實體在其所屬資料分割內的唯一識別碼。若要將實體新增至資料表，請將實體物件傳給 **insertEntity** 方法。

    var task = {
        PartitionKey :'hometasks'
        , RowKey : '1'
        , Description :'Take out the trash'
        , DueDate:new Date(2012, 6, 20)
    };
    tableService.insertEntity('mytable', task, function(error){
        if (!error) {
            // 實體已插入
        }
    });

如何更新實體
------------

有多種方法可以用來更新現有的實體：

-   **updateEntity** - 藉由取代來更新現有實體。

-   **mergeEntity** - 藉由將新的屬性值合併到現有實體來更新現有實體。

-   **insertOrReplaceEntity** - 藉由取代來更新現有實體。如果實體不存在，將會插入新的實體。

-   **insertOrMergeEntity** - 藉由將新的屬性值合併到現有實體來更新現有實體。如果實體不存在，將會插入新的實體。

下列範例示範使用 **updateEntity** 來更新實體：

    var task = {
        PartitionKey :'hometasks'
        , RowKey : '1'
        , Description :'Wash Dishes'
    }
    tableService.updateEntity('mytable', task, function(error){
        if (!error) {
            // 實體已更新
        }
    });

使用 **updateEntity** 及 **mergeEntity** 時，如果正在更新的實體不存在，則更新操作會失敗。因此，如果您要儲存一個實體，而不管它是否已存在，您應該改用 **insertOrReplaceEntity** 或 **insertOrMergeEntity**。

如何使用實體群組
----------------

有時候批次提交多個操作是有意義的，可以確保伺服器會進行不可部分完成的處理。要達到此點，請在 **TableService** 上使用 **beginBatch** 方法，然後如常呼叫操作系列。差別在於這些運算子的回呼函數將指出操作是批次式，未提交給伺服器。當您真的想提交批次時，請呼叫 **commitBatch**。提供給該方法的回叫會指出整個批次是否已成功提交。下列範例示範在批次中提交兩個實體：

    var tasks=[
        {
            PartitionKey :'hometasks'
            , RowKey : '1'
            , Description :'Take out the trash.'
            , DueDate :new Date(2012, 6, 20)
        }
        , {
            PartitionKey :'hometasks'
            , RowKey : '2'
            , Description :'Wash the dishes.'
            , DueDate :new Date(2012, 6, 20)
        }
    ]
    tableService.beginBatch();
    var async=require('async');

    async.forEach(tasks
        , function taskIterator(task, callback){
            tableService.insertEntity('mytable', task, function(error){
                if (!error) {
                    // 實體已插入
                    callback(null);
                } else {
                    callback(error);
                }
            });
        }
        , function(error){
            if (!error) {
                // 所有插入都已完成
                tableService.commitBatch(function(error){
                    if (!error) {
                        // 批次已成功認可
                    }
                });
            }
        });

**注意**

以上範例使用 'async' 模組來確定實體已成功提交，然後才呼叫 **commitBatch**。

如何查詢實體
------------

若要查詢資料表中的實體，請使用 **queryEntity** 方法，傳遞 **PartitionKey** 和 **RowKey**。

    tableService.queryEntity('mytable'
        , 'hometasks'
        , '1'
        , function(error, entity){
            if (!error) {
                // 實體包含傳回的實體
            }
        });

如何查詢一組實體
----------------

若要查詢資料表，請使用 **TableQuery** 物件建立查詢運算式，使用子句如 **select**、**from**、**where** (包括便利子句，例如 **wherePartitionKey**、**whereRowKey**、**whereNextPartitionKey** 及 **whereNextRowKey**)、**and**、**or** 及 **top**。然後將查詢運算式傳給 **queryEntities** 方法。您可以在回呼內的 **for** 迴圈中使用結果。

此範例會根據 **PartitionKey** 找到在西雅圖的所有工作。

    var query = azure.TableQuery
        .select()
        .from('mytable')
        .where('PartitionKey eq 
        ', 'hometasks');
    tableService.queryEntities(query, function(error, entities){
        if (!error) {
            //實體包含實體的陣列
        }
    });

如何查詢實體屬性的子集
----------------------

一項資料表查詢可以只擷取實體的少數屬性。這項稱為「投射」**的技術可減少頻寬並提高查詢效能 (尤其是對大型實體而言)。使用 **select** 子句並傳遞您要帶到用戶端的屬性名稱。

下列程式碼中的查詢只會傳回資料表中實體的 **Descriptions**，請注意，在程式輸出裡，**DueDate** 將顯示為 **undefined**，因為它不是由伺服器所傳送。

**注意**

請注意，下列片段只適用於雲端儲存體服務，儲存體模擬器不支援 **select** 關鍵字。

    var query = azure.TableQuery
        .select('Description')
        .from('mytable')
        .where('PartitionKey eq 
        ', 'hometasks');
    tableService.queryEntities(query, function(error, entities){
        if (!error) {
            //實體包含實體的陣列
        }
    });

如何刪除實體
------------

您可以使用實體的資料分割和資料列索引鍵來刪除實體。在本例中，**task1** 物件包含待刪除實體的 **RowKey** 及 **PartitionKey** 值。然後物件會傳給 **deleteEntity** 方法。

    tableService.deleteEntity('mytable'
        , {
            PartitionKey :'hometasks'
            , RowKey : '1'
        }
        , function(error){
            if (!error) {
                // 實體已刪除
            }
        });

如何刪除資料表
--------------

下列程式碼會從儲存體帳戶刪除資料表。

    tableService.deleteTable('mytable', function(error){
        if (!error) {
            // 資料表已刪除
        }
    });

後續步驟
--------

了解資料表儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

-   請參閱 MSDN 參考資料：[在 Azure 中儲存及存取資料](http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx)。
-   造訪 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/) (英文)。
-   造訪 GitHub 上的 [Azure SDK for Node](https://github.com/WindowsAzure/azure-sdk-for-node) (英文) 儲存機制。

