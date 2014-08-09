<properties linkid="dev-nodejs-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Node.js) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Node.js" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Blob Service from Node.js" authors="" solutions="" manager="" editor="" />

如何從 Node.js 使用 Blob 服務
=============================

本指南將示範如何使用 Azure Blob 服務執行一般案例。這些範例使用 Node.js API 撰寫。所涵蓋的案例包括**上傳**、**列出**、**下載**及**刪除**Blob。如需 Blob 的詳細資訊，請參閱[後續步驟](#next-steps)一節。

目錄
----

-   [什麼是 Blob 服務？](#what-is)
-   [概念](#concepts)
-   [建立 Azure 儲存體帳戶](#create-account)
-   [建立 Node.js 應用程式](#create-app)
-   [設定您的應用程式以存取儲存體](#configure-access)
-   [設定 Azure 儲存體連接字串](#setup-connection-string)
-   [作法：建立容器](#create-container)
-   [作法：將 Blob 上傳至容器](#upload-blob)
-   [作法：列出容器中的 Blob](#list-blob)
-   [作法：下載 Blob](#download-blobs)
-   [作法：刪除 Blob](#delete-blobs)
-   [後續步驟](#next-steps)

什麼是 Blob 服務？
------------------

Azure Blob 服務適用於儲存大量非結構化的資料，全球任何地方都可透過 HTTP 或 HTTPS 來存取這些資料。單一 blob 可能在大小上有數百 GB，而單一儲存體帳戶可以包含高達 100TB 的 blob。Blob 的一般用途包括：

-   直接提供映像或文件給瀏覽器
-   儲存檔案供分散式存取
-   串流傳輸視訊和音訊
-   執行安全備份和災害復原
-   儲存資料供內部部署或 Azure 託管服務進行分析

您可以使用 Blob 向全球公開資料，或不公開而只供內部應用程式儲存。

概念
----

Blob 服務包含下列元件：

![Blob 1](./media/storage-nodejs-how-to-use-blob-storage/blob1.jpg)

-   **URL 格式：**可利用下列 URL 格式來定址 Blob：

        http://storageaccount.blob.core.windows.net/container/blob  

    下列 URL 可定址圖中的其中一個 blob：

        http://sally.blob.core.windows.net/movies/MOV1.AVI

-   **儲存體帳戶：**一律透過儲存體帳戶來存取 Azure 儲存體。這是存取 blob 用的最高等級的命名空間。帳戶可以包含不限數目的容器，只要它們的大小總計低於 100TB 即可。

-   **容器：**容器提供一組 Blob 的群組。所有 Blob 都必須放在容器中。一個帳戶可以包含的容器不限數量。容器可以儲存無限制的 Blob。

-   **Blob：**任何類型和大小的檔案。Blob 有兩種：區塊和分頁。大部分檔案都是區塊 Blob。單一區塊 Blob 的大小上限為 200GB。本教學課程使用區塊 Blob。分頁 Blob (另一種 Blob 類型) 的大小上限為 1TB，當檔案中的位元組範圍經常修改時，分頁 Blob 的效率較高。

建立 Azure 儲存體帳戶
---------------------

若要使用儲存體作業，您需要 Azure 儲存體帳戶。您可以依照下列步驟來建立儲存體帳戶。(您也可以[使用 REST API](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh264518.aspx) 來建立儲存體帳戶。)

1.  登入 [Azure 管理入口網站](http://manage.windowsazure.com)。

2.  在導覽窗格的底端，按一下 **[新增]**。

    ![+新增](./media/storage-nodejs-how-to-use-blob-storage/plus-new.png)

3.  按一下 **[儲存體帳戶]**，然後按一下 **[快速建立]**。

    ![快速建立對話方塊](./media/storage-nodejs-how-to-use-blob-storage/quick-storage.png)

4.  在 [URL] 中，為儲存體帳戶輸入要在 URI 中使用的子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。此值會成為 URI 內用來將訂閱的 Blob、「佇列」或「表格」資源定址的主機名稱。

5.  選擇要將儲存體放置於的「區域/同質群組」。如果您會從 Azure 應用程式使用儲存體，請選取您會在其中部署應用程式的相同區域。

6.  按一下 **[建立儲存體帳戶]**。

建立 Node.js 應用程式
---------------------

建立空白的 Node.js 應用程式。如需建立 Node.js 應用程式的指示，請參閱[建立並部署 Node.js 應用程式至 Azure 網站](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/)、[Node.js 雲端服務]({localLink:2221} "Node.js Web 應用程式") (使用 Windows PowerShell) 或[使用 WebMatrix 的網站](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/)。

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

3.  您可以手動執行 **ls** 命令，確認已建立 **node\_modules** 資料夾。在該資料夾內找到 **azure** 封裝，其中包含您存取儲存體所需的程式庫。

### 匯入封裝

使用記事本或其他文字編輯器，將以下內容新增至您要使用儲存體之應用程式的 **server.js** 檔案頂端：

    var azure = require('azure');

設定 Azure 儲存體連接
---------------------

azure 模組會讀取環境變數 AZURE\_STORAGE\_ACCOUNT 及 AZURE\_STORAGE\_ACCESS\_KEY，以取得連接 Azure 儲存體帳戶所需的資訊。如果未設定這些環境變數，則呼叫 **createBlobService** 時必須指定帳戶資訊。

如需在 Azure 雲端服務組態檔中設定環境變數的範例，請參閱[使用儲存體的 Node.js 雲端服務](/en-us/develop/nodejs/tutorials/web-app-with-storage/)。

如需在 Azure 網站管理入口網站中設定環境變數的範例，請參閱[使用儲存體的 Node.js Web 應用程式](/en-us/develop/nodejs/tutorials/web-site-with-storage/)。

作法：建立容器
--------------

**BlobService** 物件讓您能使用容器及 blob。下列程式碼會建立 **BlobService** 物件。將下列內容新增至接近 **server.js** 的頂端：

    var blobService = azure.createBlobService();

所有 Blob 皆位於一個容器中。對 **BlobService** 物件上的 **createContainerIfNotExists** 的呼叫，如果指定的容器存在便會傳回它，如果尚不存在，則會以指定的名稱建立新的容器。根據預設，新容器屬私人性質，且需要使用存取金鑰才能從此容器下載 blob。

    blobService.createContainerIfNotExists(containerName, function(error){
        if (!error) {
        // Container exists and is private
        }
    });

若要讓容器中的檔案成為公用，以便不需存取金鑰即可存取它們，可以將容器的存取等級設為 **blob** 或 **container**。將存取等級設為 **blob** 可允許對此容器內的 blob 內容和中繼資料的匿名讀取存取，但不包含對容器中繼資料的匿名讀取存取，例如列出容器內的所有 blob。將存取等級設為 **container** 可允許對 blob 內容、中繼資料以及容器中繼資料的匿名讀取存取。下列範例示範將存取等級設為 **blob**：

    blobService.createContainerIfNotExists(containerName
        , {publicAccessLevel :'blob'}
        , function(error){
            if (!error) {
                // Container exists and is public
            }
        });

或者，您可以使用 **setContainerAcl** 指定存取等級，以修改容器的存取等級。下列範例會將存取等級變更為 container：

    blobService.setContainerAcl(containerName
        , 'container'
        , function(error){
            if (!error) {
                // Container access level set to 'container'
            }
        });

### 篩選器

可以將選用的篩選作業套用到使用 **BlobService** 執行的作業。篩選作業可包括記錄、自動重試等等。篩選器是以簽章實作方法的物件：

     function handle (requestOptions, next)

在對要求選項進行前處理之後，方法需要呼叫 "next" 並傳遞具有下列簽章的回呼：

     function (returnObject, finalCallback, next)

在此回呼中，以及處理 returnObject (來自對伺服器之要求的回應) 之後，回呼需要叫用 next (如果存在) 以繼續處理其他篩選，或是就改為叫用 finalCallback 結束服務叫用。

Azure SDK for Node.js 包含了實作重試邏輯的兩個篩選器：**ExponentialRetryPolicyFilter** 和 **LinearRetryPolicyFilter**。以下會建立使用 **ExponentialRetryPolicyFilter** 的 **BlobService** 物件：

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobService = azure.createBlobService().withFilter(retryOperations);

作法：將 Blob 上傳至容器
------------------------

若要上傳資料至 blob，請使用 **createBlockBlobFromFile**、**createBlockBlobFromStream** 或 **createBlockBlobFromText** 方法。**createBlockBlobFromFile** 會上傳檔案的內容，而 **createBlockBlobFromStream** 會上傳串流的內容。**createBlockBlobFromText** 會上傳指定的文字值。

下列範例會將 **test1.txt** 檔的內容上傳至 'test1' blob。

    blobService.createBlockBlobFromFile(containerName
        , 'test1'
        , 'test1.txt'
        , function(error){
            if (!error) {
                // File has been uploaded
            }
        });

作法：列出容器中的 Blob
-----------------------

若要列出容器中的 blob，請使用 **listBlobs** 方法與 **for** 迴圈，顯示容器中每個 blob 的名稱。下列程式碼會將容器中每個 blob 的 **name** 輸出到主控台。

    blobService.listBlobs(containerName, function(error, blobs){
        if (!error) {
            for(var index in blobs){
                console.log(blobs[index].name);
            }
        }
    });

作法：下載 Blob
---------------

若要從 blob 下載資料，請使用 **getBlobToFile**、**getBlobToStream** 或 **getBlobToText**。下列範例示範使用 **getBlobToStream** 來下載 **test1** blob 的內容並使用串流存放到 **output.txt** 檔案：

    var fs=require('fs');
    blobService.getBlobToStream(containerName
        , 'test1'
        , fs.createWriteStream('output.txt')
        , function(error){
            if (!error) {
                // Wrote blob to stream
            }
        });

作法：刪除 Blob
---------------

最後，呼叫 **deleteBlob** 以刪除 blob。下列範例會刪除名為 'blob1' 的 blob。

    blobService.deleteBlob(containerName
        , 'blob1'
        , function(error){
            if (!error) {
                // Blob has been deleted
            }
        });

後續步驟
--------

了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

-   請參閱 MSDN 參考資料：[在 Azure 中儲存及存取資料](http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx)。
-   造訪 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/) (英文)。
-   造訪 GitHub 上的 [Azure SDK for Node](https://github.com/WindowsAzure/azure-sdk-for-node) (英文) 儲存機制。

