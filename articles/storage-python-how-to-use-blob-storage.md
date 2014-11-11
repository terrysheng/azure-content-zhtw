<properties linkid="develop-python-blob-service" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="How to use the Blob service from Python" authors="huvalo" videoId="" scriptId="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# 如何從 Python 使用 Blob 儲存體服務

本指南將示範如何使用 Azure Blob 儲存體服務執行一般案例。這些範例使用 Python API 撰寫。所涵蓋的案例包括「上傳」、「列出」、「下載」及「刪除」Blob。如需 Blob 的詳細資訊，請參閱[後續步驟][後續步驟]一節。

## 目錄

[什麼是 Blob 儲存體？][什麼是 Blob 儲存體？]
 [概念][概念]
 [建立 Azure 儲存體帳戶][建立 Azure 儲存體帳戶]
 [作法：建立容器][作法：建立容器]
 [作法：將 Blob 上傳至容器][作法：將 Blob 上傳至容器]
 [作法：列出容器中的 Blob][作法：列出容器中的 Blob]
 [作法：下載 Blob][作法：下載 Blob]
 [作法：刪除 Blob][作法：刪除 Blob]
 [作法：上傳及下載大型 Blob][作法：上傳及下載大型 Blob]
 [後續步驟][後續步驟]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"> </a>建立 Azure 儲存體帳戶

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-container"> </a>作法：建立容器

**注意：** 如果您需要安裝 Python 或用戶端程式庫，請參閱 [Python 安裝指南][Python 安裝指南]。

**BlobService** 物件讓您能使用容器及 blob。下列程式碼會建立 **BlobService** 物件。將下列內容新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 Python 檔案內的頂端附近：

    from azure.storage import *

下列程式碼會使用儲存體帳戶名稱和帳戶金鑰來建立 **BlobService** 物件：將 'myaccount' 和 'mykey' 取代為真實的帳戶和金鑰。

    blob_service = BlobService(account_name='myaccount', account_key='mykey')

所有儲存體 Blob 皆位於一個容器中。如果容器不存在，您可以使用 **BlobService** 物件建立容器：

    blob_service.create_container('mycontainer')

根據預設，新容器屬私人性質，您必須指定儲存體存取金鑰 (如上面所做過的) 才能從此容器下載 blob。若要讓所有人都能使用容器中的檔案，您可以使用下列程式碼建立容器，並傳遞公用存取等級：

    blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

或者，您也可以在建立容器之後使用下列程式碼修改它：

    blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

做此變更之後，網際網路上的任何人都可以看到公用容器中的 Blob，但只有您能修改或刪除它們。

## <a name="upload-blob"> </a>作法：將 Blob 上傳至容器

若要將資料上傳至 Blob，請使用 **put\_block\_blob\_from\_path**、**put\_block\_blob\_from\_file**、**put\_block\_blob\_from\_bytes** 或 **put\_block\_blob\_from\_text** 方法。這些是高階方法，可在資料大小超過 64 MB 時執行必要的區塊化動作。

**put\_block\_blob\_from\_path** 會從指定的路徑上傳檔案的內容，**put\_block\_blob\_from\_file** 會從已開啟的檔案/串流上傳內容，**put\_block\_blob\_from\_bytes** 會上傳位元組陣列，**put\_block\_blob\_from\_text** 會使用指定的編碼 (預設為 UTF-8) 上傳指定的文字值。

下列範例會將 **task1.txt** 檔的內容上傳至 **myblob** Blob 中。

    blob_service.put_block_blob_from_path('mycontainer', 'myblob', 'task1.txt')

## <a name="list-blob"> </a>作法：列出容器中的 Blob

若要列出容器中的 Blob，請搭配使用 **list\_blobs** 方法與 **for** 迴圈，顯示容器中每個 Blob 的名稱。下列程式碼將容器中每個 Blob 的 **name** 和 **url** 輸出到主控台。

    blobs = blob_service.list_blobs('mycontainer')
    for blob in blobs:
        print(blob.name)
        print(blob.url)

## <a name="download-blobs"> </a>作法：下載 Blob

若要從 Blob 下載資料，請使用 **get\_blob\_to\_path**、**get\_blob\_to\_file**、**get\_blob\_to\_bytes** 或 **get\_blob\_to\_text**。這些是高階方法，可在資料大小超過 64 MB 時執行必要的區塊化動作。

下列範例示範使用 **get\_blob\_to\_path** 來下載 **myblob** Blob 的內容，並儲存到 **out-task1.txt** 檔案：

    blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-task1.txt')

## <a name="delete-blobs"> </a>作法：刪除 Blob

最後，若要刪除 Blob，請呼叫 **delete\_blob**。

    blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="next-steps"> </a>後續步驟

了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

-   請參閱 MSDN 參考：[在 Azure 中儲存和存取資料][在 Azure 中儲存和存取資料]
-   造訪 [Azure 儲存體團隊部落格][Azure 儲存體團隊部落格] (英文)。

  [後續步驟]: #next-steps
  [什麼是 Blob 儲存體？]: #what-is
  [概念]: #concepts
  [建立 Azure 儲存體帳戶]: #create-account
  [作法：建立容器]: #create-container
  [作法：將 Blob 上傳至容器]: #upload-blob
  [作法：列出容器中的 Blob]: #list-blob
  [作法：下載 Blob]: #download-blobs
  [作法：刪除 Blob]: #delete-blobs
  [作法：上傳及下載大型 Blob]: #large-blobs
  [howto-blob-storage]: ../includes/howto-blob-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [Python 安裝指南]: ../python-how-to-install/
  [在 Azure 中儲存和存取資料]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx
  [Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
