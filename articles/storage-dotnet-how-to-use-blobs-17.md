<properties  linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Blob Service (2.0)" pageTitle="How to use blob storage in .NET | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob .NET, Azure blob C#, Azure blob C#" description="Learn how to use the Azure blob service to upload,,download, list, and delete blob content. Samples are written in C#." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Azure Blob Storage Service in .NET" authors="" solutions="" manager="paulettm" editor="cgronlun" />

# 如何在 .NET 中使用 Azure Blob 儲存體服務

<div  class="dev-center-tutorial-selector">
[1.7 版](/en-us/develop/net/how-to-guides/blob-storage-v17/ "1.7 版"){:
 .current} [2.0
版](/en-us/develop/net/how-to-guides/blob-storage/ "2.0 版")
</div>

本指南將示範如何使用 Azure Blob 儲存體服務執行一般案例。這些範例均以 C# 撰寫並使用 .NET API。所涵蓋的案例包括**上傳」**、**列出**、**下載**及**刪除**Blob。如需 Blob 的詳細資訊，請參閱[後續步驟](#next-steps)一節。

<h2>目錄</h2>


* [什麼是 Blob 儲存體](#what-is)
* [概念](#concepts)
* [建立 Azure 儲存體帳戶](#create-account)
* [設定儲存體連接字串](#setup-connection-string)
* [作法：以程式設計方式存取 Blob 儲存體](#configure-access)
* [作法：建立容器](#create-container)
* [作法：將 Blob 上傳至容器](#upload-blob)
* [作法：列出容器中的 Blob](#list-blob)
* [作法：下載 Blob](#download-blobs)
* [作法：刪除 Blob](#delete-blobs)
* [後續步驟](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">建立帳戶</span>建立 Azure 儲存體帳戶</h2>


[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">設定連接字串</span>設定儲存體連接字串</h2>


Azure .NET 儲存體 API 可支援使用儲存體連接字串，來設定存取儲存服務時所需的端點與認證。您可以將儲存體連接字串置於設定檔中，而非硬式編碼至程式碼中：

* 使用 Azure 雲端服務時，建議您使用 Azure 服務設定系統 (`*.csdef` 和 `*.cscfg` 檔案) 來儲存連接字串。
* 使用 Azure 網站或 Azure 虛擬機器時，建議您使用 .NET 設定系統 (例如 `web.config` 檔案) 來儲存連接字串。

在這兩種情況下，您都可以使用 `CloudConfigurationManager.GetSetting` 方法擷取連接字串，如本指南稍後所示範。

### 在使用雲端服務時設定連接字串

服務設定機制為 Azure 雲端服務專案所獨有，可讓您從 Azure 管理入口網站動態變更組態設定，而無需重新部署應用程式。

若要在 Azure 服務設定中設定連接字串：

1.  在 Visual Studio 的 **方案總管]，於 Azure 部署專案之 [角色** 資料夾中的 Web 角色或背景工作角色上按一下滑鼠右鍵，再按一下 **屬性**。  
    ![在 Visual Studio
    中，於雲端服務角色上選取屬性](./media/storage-dotnet-how-to-use-blobs-17/blob5.png)

2.  按一下 **設定** 索引標籤，再按 **加入設定** 按鈕。  
    ![在 Visual Studio
    中新增雲端服務設定](./media/storage-dotnet-how-to-use-blobs-17/blob6.png)
    
    設定清單中隨即顯示新的 **Setting1** 項目。

3.  在新 **Setting1** 項目的 **類型** 下拉式清單中，選擇 **連接字串**。  
    ![Blob7](./media/storage-dotnet-how-to-use-blobs-20/blob7.png)

4.  按一下 **Setting1** 項目右側的 **...** 按鈕。**儲存體帳戶連接字串** 對話方塊隨即開啟。

5.  選擇要以儲存體模擬器 (本機電腦上模擬的 Azure 儲存體)，還是要以雲端中的實際儲存體帳戶為目標。不論您選擇哪一種，本指南的程式碼都適用。 如您想要在稍早於 Azure 上建立的儲存體帳戶中儲存 Blob 資料，請輸入在本教學課程的稍早步驟複製的 **主要存取金鑰** 值。  
     ![Blob8](./media/storage-dotnet-how-to-use-blobs-20/blob8.png)

6.  將 **名稱** 項目從 **Setting1** 變為更易記的名稱，如
    **StorageConnectionString**。您稍後將在本指南的程式碼中參照此連接字串。  
    ![Blob9](./media/storage-dotnet-how-to-use-blobs-20/blob9.png)

### 使用網站或虛擬機器時設定連接字串

使用網站或虛擬機器時，建議您使用 .NET 設定系統 (例如 `web.config`)。您可以使用 `<a ppSettings>` 元素來儲存連接字串：

    <configuration>
        <appSettings>
    	     <add key="StorageConnectionString"
    		      value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>	
    </configuration>

如需儲存體連接字串的詳細資訊，請閱讀[設定連接字串][1]。

現在您已準備就緒，可以開始執行本指南的「作法」工作。

<h2> <a name="configure-access"> </a><span  class="short-header">以程式設計方式存取</span>作法：以程式設計方式存取 Blob 儲存體</h2>


將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端：

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

您可以使用 **CloudStorageAccount** 類型及 **CloudConfigurationManager** 類型，擷取 Azure 服務設定中的儲存體連接字串與儲存體帳戶資訊：

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

**CloudBlobClient** 類型可讓您擷取物件，這些物件代表 Blob 儲存體服務中儲存的容器和 Blob。下列程式碼會使用我們在前面擷取的儲存體帳戶物件來建立 **CloudBlobClient** 物件：

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

<h2> <a name="create-container"> </a><span  class="short-header">建立容器</span>作法：建立容器</h2>


所有儲存體 Blob 皆位於一個容器中。您可以使用 **CloudBlobClient** 物件來取得欲使用之容器的參照。如果容器不存在，您可以建立容器：

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve a reference to a container 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Create the container if it doesn't already exist
    container.CreateIfNotExist();

根據預設，新容器屬私人性質，因此您必須指定儲存體存取金鑰 (和之前所做的一樣) 才能從此容器下載 Blob。若要讓所有人都能使用容器中的檔案，您可以使用下列程式碼將容器設定為公用容器：

    container.SetPermissions(
    new BlobContainerPermissions { PublicAccess = 

 BlobContainerPublicAccessType.Blob }); 網際網路上的任何人都可以看到公用容器中的 Blob，但要有適當的存取金鑰，才能修改或刪除這些 Blob。

<h2> <a name="upload-blob"> </a><span  class="short-header">上傳至容器</span>作法：將 Blob 上傳至容器</h2>


若要將檔案上傳至 Blob，請取得容器參照，並使用該參照來取得 Blob 參照。擁有 Blob 參照後，即可藉由在 Blob 參照上呼叫 **UploadFromStream** 方法，將任何資料流上傳至其中。此操作會建立 Blob (如果其並不存在) 或覆寫 Blob (如果其已存在)。以下程式碼範例示範此操作，並假設已建立該容器。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Retrieve reference to a blob named "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");
    
    // Create or overwrite the "myblob" blob with contents from a local file
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
    blob.UploadFromStream(fileStream);
    } 

<h2> <a name="list-blob"> </a><span  class="short-header">列出容器中的 Blob</span>作法：列出容器中的 Blob</h2>


若要列出容器中的 Blob，請先取得容器參照。然後您即可使用容器的 **ListBlobs** 方法來擷取容器中的 Blob。下列程式碼示範如何擷取和輸出容器中每個 Blob 的 **Uri**：

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Loop over blobs within the container and output the URI to each of them
    foreach (var blobItem in container.ListBlobs())
    {
    Console.WriteLine(blobItem.Uri);
    } 

Blob 服務也具備容器中之目錄的概念。正因如此，您能夠以更像資料夾的結構組織 Blob。例如，您擁有名為 'photos' 的容器，您可在此容器中上傳名為 'rootphoto1'、'2010/photo1'、'2010/photo2' 和 '2011/photo1' 的 Blob。這會以虛擬方式在 'photos' 容器內建立目錄 '2010' 和 '2011'。當您在 'photos' 容器上呼叫 **ListBlobs** 時，傳回的集合將包含 **CloudBlobDirectory** 和 **CloudBlob** 物件，其分別代表最上層所包含的目錄和 Blob。在此案例中，將會傳回目錄 '2010' 和 '2011' 以及照片 'rootphoto1'。您可以選擇性地傳入將 **UseFlatBlobListing** 設定為 **true** 的新 **BlobRequestOptions** 類別。這會導致不論目錄為何，將傳回所有 Blob。如需詳細資訊，請參閱
[CloudBlobContainer.ListBlobs][]。

<h2> <a name="download-blobs"> </a><span  class="short-header">下載 Blob</span>作法：下載 Blob</h2>


若要下載 Blob，請先擷取 Blob 參照。下列範例使用 **DownloadToStream** 方法將 Blob 內容傳送給資料流物件，您接著可將該物件永久儲存成本機檔案。您也可以呼叫 Blob 的 **DownloadToFile**、**DownloadByteArray** 或 **DownloadText** 方法。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Retrieve reference to a blob named "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");
    
    // Save blob contents to disk
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
    blob.DownloadToStream(fileStream);
    } 

<h2> <a name="delete-blobs"> </a><span  class="short-header">刪除 Blob</span>作法：刪除 Blob</h2>


最後，若要刪除 Blob，請先取得 Blob 參照，然後在該參照上呼叫 **Delete** 方法。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Retrieve reference to a blob named "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");
    
    // Delete the blob
    blob.Delete(); 

<h2> <a name="next-steps"></a><span  class="short-header">後續步驟</span>後續步驟</h2>


了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

*  如需可用 API 的完整詳細資訊，請檢視 Blob 服務參考文件：
  *  [.NET 用戶端程式庫參考][2]
  *  [REST API 參考][3]

*  請參閱[在 Azure 中儲存和存取資料][4]，深入了解可使用 Azure 儲存體執行的更多進階工作。
*  如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。
  *  使用[資料表儲存體](/en-us/develop/net/how-to-guides/table-services/)儲存結構化資料。
  *  使用 [SQL Database](/en-us/develop/net/how-to-guides/sql-database/) 儲存關聯式資料。
  




[1]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee758697.aspx
[2]: http://msdn.microsoft.com/zh-tw/library/windowsazure/wl_svchosting_mref_reference_home
[3]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dd179355
[4]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx