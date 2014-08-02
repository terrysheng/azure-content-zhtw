<properties linkid="develop-python-blob-service" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="How to use the Blob service from Python" authors="" videoId="" scriptId="" />

如何從 Python 使用 Blob 儲存體服務
==================================

本指南將示範如何使用 Azure Blob 儲存服務執行一般案例。相關範例是以 Python API 撰寫。所涵蓋的案例包括「上傳」****、「列出」****、「下載」****及「刪除」****Blob。如需 Blob 的詳細資訊，請參閱[後續步驟](#next-steps)一節。

目錄
----

[什麼是 Blob 儲存體？](#what-is)
 [概念](#concepts)
 [建立 Azure 儲存體帳戶](#create-account)
 [作法：建立容器](#create-container)
 [作法：將 Blob 上傳至容器](#upload-blob)
 [作法：列出容器中的 Blob](#list-blob)
 [作法：下載 Blob](#download-blobs)
 [作法：刪除 Blob](#delete-blobs)
 [作法：上傳及下載大型 Blob](#large-blobs)
 [後續步驟](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

建立 Azure 儲存體帳戶
---------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

作法：建立容器
--------------

**注意：** 如果您需要安裝 Python 或用戶端程式庫，請參閱 [Python 安裝指南](../python-how-to-install/)。

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

作法：將 Blob 上傳至容器
------------------------

若要將檔案上傳至 Blob，請使用 **put\_blob** 方法建立 Blob，使用檔案串流作為 Blob 的內容。首先，建立名稱為 **task1.txt** 的檔案 (內容不拘) 並將它存放在與 Python 檔相同的目錄中。

    myblob = open(r'task1.txt', 'r').read()
    blob_service.put_blob('mycontainer', 'myblob', myblob, x_ms_blob_type='BlockBlob')

作法：列出容器中的 Blob
-----------------------

若要列出容器中的 Blob，請搭配使用 **list\_blobs** 方法與 **for** 迴圈，顯示容器中每個 Blob 的名稱。下列程式碼將容器中每個 Blob 的 **name** 和 **url** 輸出到主控台。

    blobs = blob_service.list_blobs('mycontainer')
    for blob in blobs:
        print(blob.name)
        print(blob.url)

作法：下載 Blob
---------------

若要下載 Blob，請使用 **get\_blob** 方法將 Blob 內容傳送給串流物件，您接著可將該物件永久儲存到本機檔案。

    blob = blob_service.get_blob('mycontainer', 'myblob')
    with open(r'out-task1.txt', 'w') as f:
        f.write(blob)

作法：刪除 Blob
---------------

最後，若要刪除 Blob，請呼叫 **delete\_blob**。

    blob_service.delete_blob('mycontainer', 'myblob') 

作法：上傳及下載大型 Blob
-------------------------

區塊 Blob 的大小上限是 200 GB。若為小於 64 MB 的 Blob，Blob 可以使用對 **put\_blob** 或 **get\_blob** 的單一呼叫上傳或下載，如前面所顯示。若為大於 64 MB 的 Blob，則需要以 4 MB 或更小的區塊來上傳或下載 Blob。

下列程式碼顯示上傳或下載任何大小之區塊 Blob 的函數範例。

    import base64

    chunk_size = 4 * 1024 * 1024

    def upload(blob_service, container_name, blob_name, file_path):
    blob_service.create_container(container_name, None, None, False)
    blob_service.put_blob(container_name, blob_name, '', 'BlockBlob')

    block_ids = []
    index = 0
    with open(file_path, 'rb') as f:
    while True:
    data = f.read(chunk_size)
    if data:
    length = len(data)
    block_id = base64.b64encode(str(index))
    blob_service.put_block(container_name, blob_name, data, block_id)
    block_ids.append(block_id)
    index += 1
    else:
    break

    blob_service.put_block_list(container_name, blob_name, block_ids)

    def download(blob_service, container_name, blob_name, file_path):
    props = blob_service.get_blob_properties(container_name, blob_name)
    blob_size = int(props['content-length'])

    index = 0
    with open(file_path, 'wb') as f:
    while index < blob_size:
    chunk_range = 'bytes={}-{}'.format(index, index + chunk_size - 1)
    data = blob_service.get_blob(container_name, blob_name, x_ms_range=chunk_range)
    length = len(data)
    index += length
    if length > 0:
    f.write(data)
    if length < chunk_size:
    break
    else:
    break

如果您需要大於 200 GB 的 Blob，可以使用分頁 Blob，而不要用區塊 Blob。分頁 Blob 的大小上限是 1 TB，且分頁會對準 512 位元組的分頁界線。請使用 **put\_blob** 建立分頁 Blob、用 **put\_page** 寫入它，以及用 **get\_blob** 從其中讀取。

後續步驟
--------

了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

-   請參閱 MSDN 參考：[在 Azure 中儲存和存取資料](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)
-   造訪 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/) (英文)。

