<properties linkid="dev-net-how-to-use-blog-storage-service-java" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Java) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Java" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use Blob Storage from Java" authors="" solutions="" manager="" editor="" />

如何使用 Java 的 Blob 儲存體
============================

本指南將示範如何使用 Azure Blob 儲存服務執行一般案例。相關範例是以 Java 撰寫並使用 [Azure SDK for Java](http://www.windowsazure.com/en-us/develop/java/)。所涵蓋的案例包括「上傳」****、「列出」****、「下載」****及「刪除」****Blob。如需 Blob 的詳細資訊，請參閱[後續步驟](#NextSteps)一節。

目錄
----

-   [什麼是 Blob 儲存體](#what-is)
-   [概念](#Concepts)
-   [建立 Azure 儲存體帳戶](#CreateAccount)
-   [建立 Java 應用程式](#CreateApplication)
-   [設定您的應用程式以存取 Blob 儲存體](#ConfigureStorage)
-   [設定 Azure 儲存體連接字串](#ConnectionString)
-   [作法：建立容器](#CreateContainer)
-   [作法：將 Blob 上傳至容器](#UploadBlob)
-   [作法：列出容器中的 Blob](#ListBlobs)
-   [作法：下載 Blob](#DownloadBlob)
-   [作法：刪除 Blob](#DeleteBlob)
-   [作法：刪除 Blob 容器](#DeleteContainer)
-   [後續步驟](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

建立 Azure 儲存體帳戶
---------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

建立 Java 應用程式
------------------

在本指南中，您將使用的儲存功能可在 Java 應用程式中進行本機呼叫，或在 Azure Web 角色或背景工作角色中執行的程式碼中呼叫。我們假設您已下載並安裝 Java Development Kit (JDK)，且遵循[下載 Azure SDK for Java](http://www.windowsazure.com/en-us/develop/java/) (英文) 中的指示，安裝 Azure Libraries for Java 和 Azure SDK，也在 Azure 訂閱中建立了 Azure 儲存體帳戶。

您可以使用任何開發工具來建立應用程式 (包括 [記事本])。您只需要能夠編譯 Java 專案並參考 Azure Libraries for Java。

設定您的應用程式以存取 Blob 儲存體
----------------------------------

將下列 import 陳述式新增到您要在其中使用 Azure 儲存體 API 存取 blob 的 Java 檔案頂端：

    // Include the following imports to use blob APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;

設定 Azure 儲存體連接字串
-------------------------

Azure 儲存體用戶端使用儲存體連接字串，來存放存取資料管理服務時所需的端點與認證。在用戶端應用程式中執行時，您必須以下列格式提供儲存體連接字串，並針對 *AccountName* 及 *AccountKey* 值，使用管理入口網站所列之儲存體帳戶的儲存體帳戶名稱和主要存取金鑰。本範例示範如何宣告靜態欄位，來存放連接字串：

    // Define the connection-string with your values
    public static final String storageConnectionString = 
    "DefaultEndpointsProtocol=http;" + 
    "AccountName=your_storage_account;" + 
    "AccountKey=your_storage_account_key";

在 Azure 的角色內執行的應用程式中，此字串可以存放在服務組態檔 ServiceConfiguration.cscfg 中，且可以藉由呼叫 **RoleEnvironment.getConfigurationSettings** 方法來存取。以下是從服務組態檔中，名為 *StorageConnectionString* 的 **Setting** 元素取得連接字串的範例：

    // Retrieve storage account from connection-string
    String storageConnectionString = 
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

作法：建立容器
--------------

CloudBlobClient 物件可讓您取得容器和 Blob 的參考物件。下列程式碼將建立 **CloudBlobClient** 物件。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
    CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

所有 Blob 皆位於一個容器中。使用 **CloudBlobClient** 物件來取得想要使用容器的參考。如果容器不存在，可以使用 **createIfNotExist** 方法建立，如果存在，此方法則會傳回現有的容器。根據預設，新容器屬私人性質，您必須指定儲存體存取金鑰 (如上面所做過的) 才能從此容器下載 blob。

    // Get a reference to a container
    // The container name must be lower case
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create the container if it does not exist
    container.createIfNotExist();

如果您想讓檔案成為公用性質，可以設定容器的權限。

    // Create a permissions object
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Include public access in the permissions object
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Set the permissions on the container
    container.uploadPermissions(containerPermissions);

網際網路上的任何人都可以看到公用容器中的 Blob，但公用存取僅限讀取。

作法：將 Blob 上傳至容器
------------------------

若要將檔案上傳至 Blob，請取得容器參考，並使用該參考來取得 Blob 參考。擁有 Blob 參考後，即可在 Blob 參考上呼叫 upload，上傳任何資料流。如果 Blob 不存在，此作業將予以建立，若已存在，則予以覆寫。此程式碼範例顯示此點，並假設已經建立好容器。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
    CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create or overwrite the "myimage.jpg" blob with contents from a local file
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File("c:\myimages\myimage.jpg");
    blob.upload(new FileInputStream(source), source.length());

作法：列出容器中的 Blob
-----------------------

若要列出容器中的 Blob，請先取得容器參考，就像上傳 Blob 那樣。您可以使用容器的 **listBlobs** 方法搭配 for 迴圈。下列程式碼將容器中每個 Blob 的 URI 輸出到主控台。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
    CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop over blobs within the container and output the URI to each of them
    for (ListBlobItem blobItem :container.listBlobs()) {
    System.out.println(blobItem.getUri());
    }

Blob 服務也具備容器中之目錄的概念。正因如此，您能夠以更像資料夾的結構組織 Blob。

例如，您可能有名為 "photos" 的容器，當中您可能上傳名為 "rootphoto1"、"2010/photo1"、"2010/photo2" 和 "2011/photo1" 的 Blob。這會在 "photos" 容器內建立虛擬目錄 "2010" 和 "2011"。當您在 "photos" 容器上呼叫 **ListBlobs** 時，傳回的集合將包含 **CloudBlobDirectory** 和 **CloudBlob** 物件，其分別代表最上層所包含的目錄和 Blob。在此情況下，系統會傳回目錄 "2010" 和 "2011"，以及照片 "rootphoto1"。您可以使用 **instanceof** 運算子來區別這些物件。

您可以選擇性地將參數傳入 **listBlobs** 方法，將 **useFlatBlobListing** 參數設為 true。如此會導致不論目錄為何，都會傳回每個 Blob。如需詳細資訊，請參閱 Javadocs 文件中的 CloudBlobContainer.listBlobs。

作法：下載 Blob
---------------

若要下載 Blob，請遵循與上傳 Blob 相同的步驟，以取得 Blob 參考。在上傳範例中，您對 blob 物件呼叫了 upload。在下列範例中，呼叫 download 將 Blob 內容傳到串流物件，例如 **FileOutputStream**，您可以用串流物件來將 Blob 永久儲存到本機檔案。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
    CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // For each item in the container
    for (ListBlobItem blobItem :container.listBlobs()) {
    // If the item is a blob, not a virtual directory
    if (blobItem instanceof CloudBlob) {
    // Download the item and save it to a file with the same name
    CloudBlob blob = (CloudBlob) blobItem;
    blob.download(new FileOutputStream(blob.getName()));
        }
    }

作法：刪除 Blob
---------------

若要刪除 Blob，請取得 Blob 參考，然後呼叫 **delete**。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
    CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Retrieve reference to a blob named "myimage.jpg"
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Delete the blob
    blob.delete();

作法：刪除 Blob 容器
--------------------

最後，若要刪除 Blob 容器，請取得 Blob 容器參考，然後呼叫 delete。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
    CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Delete the blob container
    container.delete();

後續步驟
--------

了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

-   請參閱 MSDN 參考：[在 Azure 中儲存和存取資料]
-   請造訪 Azure 儲存體團隊部落格：&lt;http://blogs.msdn.com/b/windowsazurestorage/\>

