<properties urlDisplayName="Blob Service" pageTitle="如何使用 Blob 儲存體 (Node.js) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Node.js" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="nodejs" title="How to Use the Blob Service from Node.js" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="mwasson" />





# 如何從 Node.js 使用 Blob 服務

本指南將示範如何使用 Azure Blob 服務執行一般案例。這些範例使用 Node.js API 撰寫。涵蓋的案例包括**上傳**、**列出**、**下載**及**刪除** Blob。如需 Blob 的詳細資訊，
請參閱[後續步驟][]一節。

## 目錄

* [什麼是 Blob 服務？][]    
* [概念][]    
* [建立 Azure 儲存體帳戶][]    
* [建立 Node.js 應用程式][]  
* [設定您的應用程式以存取儲存體][]     
* [設定 Azure 儲存體連接字串][]  
* [作法：建立容器][]  
* [作法：將 Blob 上傳至容器][]  
* [作法：列出容器中的 Blob][]  
* [作法：下載 Blob][]  
* [作法：刪除 Blob][]  
* [作法：並行存取][]     
* [作法：使用共用存取簽章][]     
* [後續步驟][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a>建立 Azure 儲存體帳戶
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-app"> </a>建立 Node.js 應用程式

建立空白的 Node.js 應用程式。如需建立 Node.js 應用程式的相關指示，請參閱[建立 Node.js 應用程式並將其部署到 Azure 網站]、[Node.js 雲端服務][Node.js Cloud Service] (使用 Windows PowerShell) 或 [使用 WebMatrix 的網站]。

## <a name="configure-access"> </a>設定您的應用程式以存取儲存體

若要使用 Azure 儲存體，您需要 Azure Storage SDK for Node.js，這包含一組便利程式庫，能與儲存體 REST 服務通訊。

### 使用 Node Package Manager (NPM) 取得封裝

1.  使用命令列介面，例如 **PowerShell** (Windows)、**Terminal** (Mac) 或 **Bash** (Unix)，瀏覽到您建立範例應用程式的資料夾。

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

3.  您可以手動執行 **ls** 命令，確認已建立 **node\_modules** 資料夾。在該資料夾內找出 **azure-storage** 封裝，其中包含您存取儲存體所需的程式庫。

### 匯入封裝

使用記事本或其他文字編輯器，將以下內容新增至您要使用儲存體之應用程式的 **server.js** 檔案頂端：

    var azure = require('azure-storage');

## <a name="setup-connection-string"> </a>設定 Azure 儲存體連接

Azure 模組會讀取環境變數 AZURE\_STORAGE\_ACCOUNT 和 AZURE\_STORAGE\_ACCESS\_KEY 或 AZURE\_STORAGE\_CONNECTION\_STRING，以取得連接到 Azure 儲存體帳戶所需的資訊。如果未設定這些環境變數，則呼叫 **createBlobService** 時必須指定帳戶資訊。

如需在 Azure 網站管理入口網站中設定環境變數的範例，請參閱[使用儲存體的 Node.js Web 應用程式]

## <a name="create-container"> </a>作法：建立容器

**BlobService** 物件可讓您使用容器及 Blob。Auch die Eigenschaften
下列程式碼會建立 **BlobService** 物件。將下列內容新增至
**server.js** 頂端附近：

    var blobSvc = azure.createBlobService();

> [WACOM.NOTE] 您可以使用 **createBlobServiceAnonymous** 並提供主機位址，以匿名存取 Blob。例如 `var blobSvc = azure.createBlobService('https://myblob.blob.core.windows.net/');`。

所有 Blob 皆位於一個容器中。若要建立新的容器，請使用 **createContainerIfNotExists**。以下會建立一個名為 'mycontainer' 的新容器

	blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows 
        // anonymous read access to blob 
        // content and metadata within this container
      }
	});

如果已建立容器，則 `result` 將是 true。如果容器已存在，則 `result` 將是 false。`response` 將包含作業的相關資訊，包括容器的 [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) 資訊。

###容器安全性

依預設，新的容器屬私人性質，無法以匿名方式存取。若要將容器變成公開，以允許匿名存取，您可以將容器的存取等級設為 **blob** 或 **container**。

* **blob** - 允許對此容器內的 Blob 內容和中繼資料的匿名讀取存取，但不包含對容器中繼資料的匿名讀取存取，例如列出容器內的所有 Blob。 

* **container** - 允許對 Blob 內容和中繼資料及容器中繼資料的匿名讀取存取。 

下列範例示範如何將存取等級設為 **blob**： 

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
      if(!error){
        // Container exists and is private
      }
	});

或者，您可以使用 **setContainerAcl** 指定存取等級，以修改容器的存取等級。下列範例會將存取等級變更為 container：

    blobSvc.setContainerAcl('mycontainer', null, 'container', function(error, result, response){
	  if(!error){
		// Container access level set to 'container'
	  }
	});

result 將包含操作的相關資訊，包括容器的目前 **ETag**。

###篩選器

可以將選用的篩選作業套用到使用 **BlobService** 執行的作業。篩選作業可包括記錄、自動重試等等。篩選器是以簽章實作方法的物件：

		function handle (requestOptions, next)

在對要求選項進行前處理之後，方法需要呼叫 "next" 並傳遞具有下列簽章的回呼：

		function (returnObject, finalCallback, next)

在此回呼中，以及處理 returnObject (來自對伺服器之要求的回應) 之後，回呼需要叫用 next (如果存在) 以繼續處理其他篩選，或是直接叫用 finalCallback 結束服務叫用。

Azure SDK for Node.js 包含了實作重試邏輯的兩個篩選器：**ExponentialRetryPolicyFilter** 和 **LinearRetryPolicyFilter**。以下會建立使用 **ExponentialRetryPolicyFilter** 的 

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobSvc = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-blob"> </a>How to: Upload a Blob into a Container

A blob can be either block, or page based. Block blobs allow you to more efficiently upload large data, while page blobs are optimized for read/write operations. For more information, see [Understanding block blobs and page blobs](http://msdn.microsoft.com/zh-tw/library/azure/ee691964.aspx).

###Block blobs

To upload data to a block blob, use the following:

* **createBlockBlobFromLocalFile** - creates a new block blob and uploads the contents of a file.

* **createBlockBlobFromStream** - creates a new block blob and uploads the contents of a stream.

* **createBlockBlobFromText** - creates a new block blob and uploads the contents of a string.

* **createWriteStreamToBlockBlob** - provides a write stream to a block blob.

The following example uploads the contents of the **test.txt** file into **myblob**.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

The `result` returned by these methods will contain information on the operation, such as the **ETag** of the blob.

###Page blobs

To upload data to a page blob, use the following:

* **createPageBlob** - creates a new page blob of a specific length.

* **createPageBlobFromLocalFile** - creates a new page blob and uploads the contents of a file.

* **createPageBlobFromStream** - creates a new page blob and uploads the contents of a stream.

* **createWriteStreamToExistingPageBlob** - provides a write stream to an existing page blob.

* **createWriteStreamToNewPageBlob** - creates a new blob and then provides a stream to write to it.

The following example uploads the contents of the **test.txt** file into **mypageblob**.

	blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

> [WACOM.NOTE] Page blobs consist of 512-byte 'pages'. You may receive an error when uploading data with a size that is not a multiple of 512.

## <a name="list-blob"> </a>How to: List the Blobs in a Container

To list the blobs in a container, use the **listBlobsSegmented** method. If you would like to return blobs with a specific prefix, use **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result contains the entries
	  }
	});

The `result` will contain an `entries` collection, which is an array of objects describing each blob. If all blobs cannot be returned, the `result` will also provide a `continuationToken`, which may be used as the second parameter to retrieve additional entries.

## <a name="download-blob"> </a>How to: Download Blobs

To download data from a blob, use the following:

* **getBlobToFile** - writes the blob contents to file

* **getBlobToStream** - writes the blob contents to a stream.

* **getBlobToText** - writes the blob contents to a string. 

* **createReadStream** - provides a stream to read from the blob

The following example demonstrates using **getBlobToStream** to download the contents of the **myblob** blob and store it to the **output.txt** file using a stream:

    var fs=require('fs');
	blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
	  if(!error){
	    // blob retrieved
	  }
	});

The `result` will contain information about the blob, including **ETag** information.

## <a name="delete-blob"> </a>How to: Delete a Blob

Finally, to delete a blob, call **deleteBlob**. The following example deletes the blob named **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
	  if(!error){
		// Blob has been deleted
	  }
	});

##<a name="concurrent-access"></a>How to: Concurrent access

To support concurrent access to a blob from multiple clients or multiple process instances, you can use **ETags** or **leases**.

* **Etag** - provides a way to detect that the blob or container has been modified by another process.

* **Lease** - provides a way to obtain exclusive, renewable, write or delete access to a blob for a period of time.

###ETag

ETags should be used if you need to allow multiple clients or instances to write to the blob simultaneously. The ETag allows you to determine if the container or blob has been modified since you initially read or created it, which allows you to avoid overwriting changes committed by another client or process.

ETag conditions can be set using the optional `options.accessConditions` parameter. The following example will only upload the **test.txt** file if the blob already exists and has the ETag value contained by `etagToMatch`.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { 'if-match': etagToMatch} }, function(error, result, response){
      if(!error){
	    // file uploaded
	  }
	});

The general pattern when using ETags is:

1. Obtain the ETag as the result of a create, list, or get operation.

2. Perform an action, checking that the ETag value has not been modified.

If the value has been modified, this indicates that another client or instance has modified the blob or container since you obtained the ETag value.

###Lease

A new lease can be acquired using the **acquireLease** method, specifying the blob or container that you wish to obtain a lease on. For example, the following acquires a lease on **myblob**.

	blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
	  if(!error) {
	    console.log(result);
	  }
	});

Subsequent operations on **myblob** must provide `options.leaseId` parameter. The lease ID is returned as `result.id` from **acquireLease**.

> [WACOM.NOTE] By default, the lease duration is infinite. You can specify a non-infinite duration (between 15 and 60 seconds,) by providing the `options.leaseDuration` parameter.

To remove a lease, use **releaseLease**. To break a lease, but prevent others from obtaining a new lease until the original duration has expired, use **breakLease**.

## <a name="sas"></a>How to: Work with Shared Access Signatures

Shared Access Signatures (SAS) are a secure way to provide granular access to blobs and containers without providing your storage account name or keys. SAS are often used to provide limited access to your data, such as allowing a mobile app to access blobs.

> [WACOM.NOTE] While you can also allow anonymous access to blobs, SAS allows you to provide more controlled access, as you must generate the SAS.

A trusted application such as a cloud-based service generates a SAS using the **generateSharedAccessSignature** of the **BlobService**, and provides it to an untrusted or semi-trusted application. For example, a mobile app. The SAS is generated using a policy, which describes the start and end dates during which the SAS is valid, as well as the access level granted to the SAS holder.

The following example generates a new shared access policy that will allow the SAS holder to perform read operations on the **myblob** blob, and expires 100 minutes after the time it is created.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
	    
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};
	
	var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
	var host = blobSvc.host;

Note that the host information must be provided also, as it is required when the SAS holder attempts to access the container.

The client application then uses the SAS with **BlobServiceWithSAS** to perform operations against the blob. The following gets information about **myblob**.

	var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
	sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
	  if(!error) {
	    // retrieved info
	  }
	});

Since the SAS was generated with only read access, if an attempt were made to modify the blob, an error would be returned.

###Access control lists

You can also use an Access Control List (ACL) to set the access policy for a SAS. This is useful if you wish to allow multiple clients to access a container, but provide different access policies for each client.

An ACL is implemented using an array of access policies, with an ID associated with each policy. The  following example defines two policies; one for 'user1' and one for 'user2':

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

The following example gets the current ACL for **mycontainer**, then adds the new policies using **setBlobAcl**. This approach allows:

	blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		blobSvc.setBlobAcl('mycontainer', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

Once the ACL has been set, you can then create a SAS based on the ID for a policy. The following example creates a new SAS for 'user2':

	blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## <a name="next-steps"> </a>Next Steps

Now that you've learned the basics of blob storage, follow these links
to learn how to do more complex storage tasks.

-   See the MSDN Reference: [Storing and Accessing Data in Azure][].
-   Visit the [Azure Storage Team Blog][].
-   Visit the [Azure Storage SDK for Node][] repository on GitHub.

  [Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node
  [Next Steps]: #next-steps
  [What is the Blob Service?]: #what-is
  [Concepts]: #concepts
  [Create an Azure Storage Account]: #create-account
  [Create a Node.js Application]: #create-app
  [Configure your Application to Access Storage]: #configure-access
  [Setup an Azure Storage Connection String]: #setup-connection-string
  [How To: Create a Container]: #create-container
  [How To: Upload a Blob into a Container]: #upload-blob
  [How To: List the Blobs in a Container]: #list-blob
  [How To: Download Blobs]: #download-blobs
  [How To: Delete a Blob]: #delete-blobs
  [How To: Concurrent access]: #concurrent-access
  [How To: Work with Shared Access Signatures]: #sas
[Create and deploy a Node.js application to an Azure Web Site]: /zh-tw/develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /zh-tw/documentation/articles/storage-nodejs-use-table-storage-cloud-service-app/
  [Node.js Web Application with Storage]: /zh-tw/documentation/articles/storage-nodejs-use-table-storage-web-site/
 [Web Site with WebMatrix]: /zh-tw/documentation/articles/web-sites-nodejs-use-webmatrix/
  [using the REST API]: http://msdn.microsoft.com/zh-tw/library/windowsazure/hh264518.aspx
  [Azure Management Portal]: http://manage.windowsazure.com
  [Node.js Cloud Service]: /zh-tw/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/

<!--HONumber=35_1-->
