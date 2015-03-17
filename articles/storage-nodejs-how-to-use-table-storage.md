<properties 
	pageTitle="如何使用資料表儲存體 (Node.js) | Microsoft Azure" 
	description="如何在 Azure 中使用資料表儲存體服務。程式碼範例以 Node.js API 撰寫。" 
	services="storage" 
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

# 如何從 Node.js 使用資料表服務

本指南將示範如何使用 Azure 資料表服務執行一般案例。這些範例使用 Node.js API 撰寫。所涵蓋的案例包括「建立和刪除資料表」、「在資料表中插入及查詢實體」。如需資料表的詳細資訊，請參閱[後續步驟][]一節。

## 目錄

* [什麼是資料表服務？][]   
* [概念][]   
* [建立 Azure 儲存體帳戶](#create-account)
* [建立 Node.js 應用程式](#create-app)
* [設定您的應用程式以存取儲存體](#configure-access)
* [設定 Azure 儲存體連接](#setup-connection-string)  
* [做法：建立資料表](#create-table)
* [做法：將實體新增至資料表](#add-entity)
* [做法：更新實體](#update-entity)
* [做法：使用實體群組](#change-entities)
* [做法：擷取實體](#query-for-entity)
* [做法：查詢實體集合](#query-set-entities)
* [做法：刪除實體](#delete-entity)
* [做法：刪除資料表](#delete-table)   
* [做法：使用共用存取簽章](#sas)
* [後續步驟][]

[AZURE.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a>建立 Azure 儲存體帳戶</h2>

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## <a name="create-app"> </a>建立 Node.js 應用程式 
建立空白的 Node.js 應用程式。如需建立 Node.js 應用程式的相關指示，請參閱[建立 Node.js 應用程式並將其部署到 Azure 網站]、[Node.js 雲端服務][Node.js Cloud Service] (使用 Windows PowerShell) 或[使用 WebMatrix 的網站]()。

## <a name="configure-access"> </a>設定您的應用程式以存取儲存體

若要使用 Azure 儲存體，您需要 Azure Storage SDK for Node.js，這包含一組便利程式庫，能與儲存體 REST 服務通訊。

### 使用 Node Package Manager (NPM) 取得封裝

1.  使用命令列介面，例如 **PowerShell** (Windows)、「終端機」 (Mac) 或 **Bash** (Unix)，瀏覽到您建立範例應用程式的資料夾。

2.  在命令視窗中輸入 **npm install azure-storage**，該命令應可產生以下輸出：

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  您可以手動執行 **ls** 命令，確認已建立 **node\_modules** 資料夾。在該資料夾內，您會找到 **azure-storage** 封裝，其中包含您存取儲存體所需的程式庫。

### 匯入封裝

使用記事本或其他文字編輯器，將以下內容新增至您要使用儲存體之應用程式的 **server.js** 檔案頂端：

    var azure = require('azure-storage');

## <a name="setup-connection-string"> </a>設定 Azure 儲存體連接

Azure 模組會讀取環境變數 AZURE\_STORAGE\_ACCOUNT 和 AZURE\_STORAGE\_ACCESS\_KEY 或 AZURE\_STORAGE\_CONNECTION\_STRING，以取得連接到 Azure 儲存體帳戶所需的資訊。如果未設定這些環境變數，則呼叫 **TableService** 時必須指定帳戶資訊。

如需在 Azure 網站管理入口網站中設定環境變數的範例，請參閱[使用儲存體的 Node.js Web 應用程式]。

## <a name="create-table"> </a>如何建立資料表

下列程式碼會建立 **TableService** 物件，並使用該物件建立新資料表。將下列內容新增至接近 **server.js** 的頂端。

    var tableSvc = azure.createTableService();

呼叫 **createTableIfNotExists** 會以指定的名稱建立新的資料表 (若尚未存在)。如果名為 'mytable' 的資料表尚不存在，下列範例便會建立這個新資料表：

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
		if(!error){
			// Table exists or created
		}
	});

 `result` 為  `true` 表示已建立新的資料表，若為  `false`，則表示資料表已存在。 `response` 將包含要求的相關資訊。

###篩選器

可以將選用性的篩選操作套用到使用 **TableService** 執行的操作。篩選作業可包括記錄、自動重試等等。篩選器是以簽章實作方法的物件：

		function handle (requestOptions, next)

在對要求選項進行前處理之後，方法需要呼叫 "next" 並傳遞具有下列簽章的回呼：

		function (returnObject, finalCallback, next)

在此回呼中，以及處理 returnObject (來自對伺服器之要求的回應) 之後，回呼需要叫用 next (如果存在) 以繼續處理其他篩選，或是就改為叫用 finalCallback 結束服務叫用。

Azure SDK for Node.js 包含了實作重試邏輯的兩個篩選器：**ExponentialRetryPolicyFilter** 和 **LinearRetryPolicyFilter**。以下會建立使用 **ExponentialRetryPolicyFilter** 的 **TableService** 物件：

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-entity"> </a>如何將實體新增至資料表

若要新增實體，請先建立一個定義實體屬性的物件。所有實體都必須包含 **PartitionKey** 和 **RowKey**，這些是實體的唯一識別碼。

* **PartitionKey** - 決定儲存實體的分割區。

* **RowKey** - 可唯一識別分割區內的實體。

**PartitionKey** 和 **RowKey** 都必須是字串值。如需詳細資訊，請參閱[了解表格服務資料模型](http://msdn.microsoft.com/library/azure/dd179338.aspx)。

以下是定義實體的範例。請注意，**dueDate** 定義為 **Edm.DateTime** 型別。可選擇是否指定型別，若未指定，則會推斷型別。

	var task = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
	};

> [AZURE.NOTE] 每一筆記錄也都有 **Timestamp** 欄位，這是在插入或更新實體時由 Azure 設定。

您也可以使用 **entityGenerator** 來建立實體。下列範例使用 **entityGenerator** 建立相同的工作實體。

	var entGen = azure.TableUtilities.entityGenerator;
    var task = {
	  PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

若要將實體新增至資料表，請將實體物件傳給 **insertEntity** 方法。

	tableSvc.insertEntity('mytable',task, function (error, result, response) {
		if(!error){
			// Entity inserted
		}
	});

若操作成功， `result` 會包含插入之記錄的 [ETag](http://en.wikipedia.org/wiki/HTTP_ETag)， `response` 將包含操作的相關資訊。

> [AZURE.NOTE] 依預設，**insertEntity** 不會透過  `response` 資訊傳回已插入的實體。若您打算對此實體執行其他操作，或想要快取資訊，則比較實用的做法是透過此參數傳回以做為部分  `result`。您可以啟用 **echoContent** 來達成目的，如下所示：
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-entity"> </a>如何更新實體

有多種方法可以用來更新現有的實體：

* **updateEntity** - 藉由取代來更新現有實體。

* **mergeEntity** - 藉由將新的屬性值合併到現有實體來更新現有實體。

* **insertOrReplaceEntity** - 藉由取代來更新現有實體。如果實體不存在，將會插入新的實體。

* **insertOrMergeEntity** - 藉由將新的屬性值合併到現有實體來更新現有實體。如果實體不存在，將會插入新的實體。

下列範例示範使用 **updateEntity** 來更新實體：

	tableSvc.updateEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [AZURE.NOTE] 依預設，更新實體並不會檢查正在更新的資料先前是否被另一個程序修改過。若要支援並行更新：
> 
> 1.取得正在更新之物件的 ETag。這會放在任何實體相關操作的  `response` 中傳回，可透過  `response['.metadata'].etag` 擷取。
> 
> 2.對實體執行更新操作時，請將先前擷取的 ETag 資訊新增至新的實體。例如：
> 
>     `entity2['.metadata'].etag = currentEtag;`
>    
> 3.執行更新操作。如果您在擷取 ETag 值時已修改實體，例如您應用程式的其他執行個體，會傳回  `error`，指出不符合要求中指定的更新條件。
    
使用 **updateEntity** 及 **mergeEntity** 時，如果正在更新的實體不存在，則更新操作會失敗。因此，如果您要儲存一個實體，而不管它是否已存在，您應該改用 **insertOrReplaceEntity** 或 **insertOrMergeEntity**。

成功之更新操作的  `result` 將包含已更新之實體的 **Etag**。

## <a name="change-entities"> </a>如何使用實體群組

有時候批次提交多個操作是有意義的，可以確保伺服器會進行不可部分完成的處理。若要達到此目的，請使用 **TableBatch** 類別建立批次，然後使用 **TableService** 的 **executeBatch** 方法執行批次作業。

 下列範例示範在批次中提交兩個實體：

    var task1 = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'Take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};
	var task2 = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '2'},
	  description: {'_':'Wash the dishes'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};

	var batch = new azure.TableBatch();
	
	batch.insertEntity(task1, {echoContent: true});
	batch.insertEntity(task2, {echoContent: true});

	tableSvc.executeBatch('mytable', batch, function (error, result, response) {
	  if(!error) {
	    // Batch completed
	  }
	});

批次操作成功時， `result` 將包含批次中每個操作的相關資訊。

###處理批次操作

若要檢查新增至批次的操作，您可以檢視  `operations` 屬性。您也可以使用下列方法來處理操作。

* **clear** - 清除批次中的所有操作。

* **getOperations** - 從批次取得操作。

* **hasOperations** - 若批次包含操作，則傳回 true。

* **removeOperations** - 移除操作。

* **size** - 傳回批次中的操作數目。

## <a name="query-for-entity"> </a>如何擷取實體

若您要根據 **PartitionKey** 和 **RowKey** 傳回特定的實體，請使用 **retrieveEntity** 方法。

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
	  if(!error){
	    // result contains the entity
	  }
    });

當此操作完成時， `result` 將包含實體。

## <a name="query-set-entities"> </a>如何查詢一組實體

若要查詢資料表，請使用 **TableQuery** 物件並利用下列子句來建置查詢運算式：

* **select** - 要從查詢傳回的欄位。

* **where** - where 子句。

	* **and** -  `and` where 條件。

	* **or** -  `or` where 條件。

* **top** - 要提取的項目數。


下列範例建立的查詢會傳回 PartitionKey 為 'hometasks' 的前 5 個項目。

	var query = new azure.TableQuery()
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

因為沒有使用 **select**，所以會傳回所有欄位。若要對資料表執行查詢，請使用 **queryEntities**。下列範例使用此查詢從 'mytable' 傳回實體。

	tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
	  if(!error) {
	    // query was successful
	  }
	});

若成功， `result.entries` 將包含符合查詢的實體陣列。若查詢無法傳回所有實體， `result.continuationToken` 將為「非 null」，並且可作為 **queryEntities** 的第三個參數來擷取更多結果。在初始查詢中，第三個參數應為  *null*。

###如何查詢實體屬性的子集

一項資料表查詢可以只擷取實體的少數欄位。
這可以減少頻寬並提高查詢效能 (尤其是對大型實體而言)。請使用 **select** 子句並傳遞要傳回的欄位名稱。例如，下列查詢只會傳回 **description** 和 **dueDate** 欄位。

	var query = new azure.TableQuery()
	  .select(['description', 'dueDate'])
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-entity"> </a>如何刪除實體

您可以使用實體的資料分割和資料列索引鍵來刪除實體。在此範例中，**task1** 物件包含要刪除之實體的 **RowKey** 和
**PartitionKey** 值。然後物件會傳給 **deleteEntity** 方法。

	var task = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'}
	};

    tableSvc.deleteEntity('mytable', task, function(error, response){
	  if(!error) {
		// Entity deleted
	  }
	});

> [AZURE.NOTE] 刪除項目時應該考慮使用 ETag，以確保項目未被另一個程序修改過。請參閱[做法：更新實體][]，以取得使用 ETag 的相關資訊。

## <a name="delete-table"> </a>如何刪除資料表

下列程式碼會從儲存體帳戶刪除資料表。

    tableSvc.deleteTable('mytable', function(error, response){
		if(!error){
			// Table deleted
		}
	});

若不確定資料表是否存在，請使用 **deleteTableIfExists**。

## <a name="sas"></a>做法：使用共用存取簽章

共用存取簽章 (SAS) 可安全地提供對資料表的精確存取，而不必提供您的儲存體帳戶名稱或金鑰。SAS 通常用來提供對資料的有限存取，例如允許行動應用程式查詢記錄。

信任的應用程式 (例如雲端型服務) 會使用 **TableService** 的 **generateSharedAccessSignature** 來產生 SAS，並提供它給不信任或不完全信任的應用程式。例如行動應用程式。SAS 是使用原則來產生，該原則描述 SAS 有效期間的開始和結束日期，以及授與 SAS 持有者的存取等級。

下列範例會產生新的共用存取原則，讓 SAS 持有者查詢 ('r') 資料表，並於建立它之後的 100 分鐘過期。

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
		
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};

	var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
	var host = tableSvc.host;

請注意，也必須提供主機資訊，因為 SAS 持有者嘗試存取資料表時需要此資訊。

用戶端應用程式接著以 **TableServiceWithSAS** 來使用 SAS，以對資料表執行操作。下列範例會連線到資料表並執行查詢。

	var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
	var query = azure.TableQuery()
	  .where('PartitionKey eq ?', 'hometasks');
		
	sharedTableService.queryEntities(query, null, function(error, result, response) {
	  if(!error) {
		// result contains the entities
	  }
	});

因為產生的 SAS 只有查詢權限，若嘗試插入、更新或刪除實體，則會傳回錯誤。

###存取控制清單

您也可以使用存取控制清單 (ACL) 來設定 SAS 的存取原則。若您要允許用戶端存取資料表，但對每個用戶端提供不同的存取原則，則這會很有用。

ACL 是使用存取原則陣列來實作，每個原則有相關聯的識別碼。下列範例定義兩個原則，其中一個用於 'user1'，另一個用於 'user2'：

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

下列範例會取得 **hometasks** 資料表的目前 ACL，然後使用 **setTableAcl** 來加入新的原則。此方法允許：

	tableSvc.getTableAcl('hometasks', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		tableSvc.setTableAcl('hometasks', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

設定 ACL 之後，您可以根據原則的識別碼來建立 SAS。下列範例會建立 'user2' 的新 SAS：

	tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"> </a>後續步驟

了解資料表儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

-   請參閱 MSDN 參考：[在 Azure 中儲存和存取資料]()。
-   請瀏覽 [Azure 儲存體團隊部落格]()。
-   請瀏覽 GitHub 上的 [Azure Storage SDK for Node][] 儲存機制。

  [Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node
  [後續步驟]: #next-steps
  [什麼是資料表服務？]: #what-is
  [概念]: #concepts
  [Create an Azure Storage Account]: #create-account
  [Create a Node.js Application]: #create-app
  [Configure your Application to Access Storage]: #configure-access
  [Setup an Azure Storage Connection]: #setup-connection-string
  [How To: Create a Table]: #create-table
  [How To: Add an Entity to a Table]: #add-entity
  [How To: Update an Entity]: #update-entity
  [How to: Work with Groups of Entities]: #change-entities
  [How to: Query for an Entity]: #query-for-entity
  [How to: Query a Set of Entities]: #query-set-entities
  [How To: Query a Subset of Entity Properties]: #query-entity-properties
  [How To: Delete an Entity]: #delete-entity
  [How To: Delete a Table]: #delete-table

  [OData.org]: http://www.odata.org/
  [using the REST API]: http://msdn.microsoft.com/library/windowsazure/hh264518.aspx
  [Azure Management Portal]: http://manage.windowsazure.com

  [Node.js Cloud Service]: /zh-tw/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
  [Visit the Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Web Site with WebMatrix]: /zh-tw/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Node.js Cloud Service with Storage]: /zh-tw/documentation/articles/storage-nodejs-use-table-storage-cloud-service-app/
  [Node.js Web Application with Storage]: /zh-tw/documentation/articles/storage-nodejs-use-table-storage-web-site/
  [Create and deploy a Node.js application to an Azure Web Site]: /zh-tw/documentation/articles/web-sites-nodejs-develop-deploy-mac/
<!--HONumber=42-->
