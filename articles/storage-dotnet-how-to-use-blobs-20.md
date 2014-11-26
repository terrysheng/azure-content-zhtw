<properties  linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage from .NET | Microsoft Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use the Windows Azure blob service to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use the Windows Azure Blob Storage Service in .NET" authors="tamram" />

# 如何使用 .NET 的 Blob 儲存體

本指南將示範如何使用 Azure Blob 儲存體服務執行一般案例。這些範例均以 C# 撰寫，並使用 Azure Storage Client Library for .NET。所涵蓋的案例包括**上傳**、**列出**、**下載**及**刪除**Blob。如需 Blob 的詳細資訊，請參閱[後續步驟](#next-steps)一節。

## 目錄

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

## <a name="create-account"></a><span  class="short-header">建立帳戶</span>建立 Azure 儲存體帳戶

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="setup-connection-string"></a><span  class="short-header">設定連接字串</span>設定儲存體連接字串

Azure Storage Client Library for .NET 可支援使用儲存體連接字串，來設定存取儲存服務時所需的端點與認證。您可以將儲存體連接字串置於設定檔中，而非硬式編碼至程式碼中：

* 使用 Azure 雲端服務時，建議您使用 Azure 服務設定系統 (`*.csdef` 和 `*.cscfg` 檔案) 來儲存連接字串。
* 使用 Azure 網站、Azure 虛擬機器，或建置要在 Azure 外執行的 .NET 應用程式時，建議您使用 .NET 設定系統 (例如
  `web.config` 或 `app.config` 檔案) 來儲存連接字串。

本指南稍後會示範如何擷取連接字串。

### 在使用雲端服務時設定連接字串

服務設定機制為 Azure 雲端服務專案所獨有，可讓您從 Azure 管理入口網站動態變更組態設定，而無需重新部署應用程式。

若要在 Azure 服務設定中設定連接字串：

1.  在 Visual Studio 的 **方案總管]，於 Azure 部署專案之 [角色** 資料夾中的 Web 角色或背景工作角色上按一下滑鼠右鍵，再按一下 **屬性**。  
    ![在 Visual Studio
    中，於雲端服務角色上選取屬性](./media/storage-dotnet-how-to-use-blobs-20/blob5.png)

2.  按一下 **設定** 索引標籤，再按 **加入設定** 按鈕。  
    ![在 Visual Studio
    中新增雲端服務設定](./media/storage-dotnet-how-to-use-blobs-20/blob6.png)
    
    設定清單中隨即顯示新的 **Setting1** 項目。

3.  在新 **Setting1** 項目的 **類型** 下拉式清單中，選擇 **連接字串**。  
    ![Blob7](./media/storage-dotnet-how-to-use-blobs-20/blob7.png)

4.  按一下 **Setting1** 項目右側的 **...** 按鈕。**儲存體帳戶連接字串** 對話方塊隨即開啟。

5.  選擇要以儲存體模擬器 (本機電腦上模擬的 Azure 儲存體)，還是雲端中的儲存體帳戶為目標。不論您選擇哪一種，本指南的程式碼都適用。如您想要在稍早於 Azure 上建立的儲存體帳戶中儲存 Blob 資料，請輸入在本教學課程的稍早步驟複製的 **主要存取金鑰** 值。  
     ![Blob8](./media/storage-dotnet-how-to-use-blobs-20/blob8.png)

6.  將 **名稱** 項目從 **Setting1** 變為更易記的名稱，如 **StorageConnectionString**。您稍後將在本指南的程式碼中參照此連接字串。  
    ![Blob9](./media/storage-dotnet-how-to-use-blobs-20/blob9.png)

### 使用 .NET 設定來設定連接字串

如果您要撰寫的應用程式並非 Azure 雲端服務 (請參閱上節)，建議您使用 .NET 設定系統 (例如 `web.config` 或 `app.config`)。這類應用程式包括 Azure 網站或 Azure 虛擬機器，以及設計成在 Azure 外執行的應用程式。您可以使用如下所示的 `<a ppSettings>` 元素儲存連接字串：

	<configuration>
  		<a ppSettings>
    		<a dd key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

 如需儲存體連接字串的詳細資訊，請閱讀[設定連接字串][1]。

 現在您已準備就緒，可以開始執行本指南的「作法」工作。

## <a name="configure-access"> </a><span  class="short-header">以程式設計方式存取</span>作法：以程式設計方式存取 Blob 儲存體

### 取得組件

 您可以使用 NuGet 來取得 `Microsoft.WindowsAzure.Storage.dll` 組件。在 **方案總管** 中以滑鼠右鍵按一下專案，然後選擇 **管理 NuGet 封裝**。在線上搜尋 "WindowsAzure.Storage"，再按一下 **安裝** 以安裝 Azure 儲存體封裝與相依性。

 `Microsoft.WindowsAzure.Storage.dll` 也隨附於 Azure SDK for .NET (可自 [.NET Developer Center][2] 下載)。此組件會安裝至 `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk>\ref\` 目錄中。

### 命名空間宣告

 將下列命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端：

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

 確定已參照 `Microsoft.WindowsAzure.Storage.dll` 組件。

### 擷取連接字串

 您可以使用 **CloudStorageAccount** 類型來代表儲存體帳戶資訊。如果您使用 Azure 專案範本且 (或) 具有 Microsoft.WindowsAzure.CloudConfigurationManager 參照，可以使用 **CloudConfigurationManager** 類型，擷取 Azure 服務設定中的儲存體連接字串與儲存體帳戶資訊：

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

 如果您是建立不含 Microsoft.WindowsAzure.CloudConfigurationManager 參照的應用程式，且連接字串位於 `web.config` 或 `app.config` (如上所示)，則可使用 **ConfigurationManager** 來擷取連接字串。您需要將 System.Configuration.dll 參照新增至專案，並為其新增其他命名空間宣告：

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

 **CloudBlobClient** 類型可讓您擷取物件，這些物件代表 Blob 儲存體服務中儲存的容器和 Blob。下列程式碼會使用我們在前面擷取的儲存體帳戶物件來建立 **CloudBlobClient** 物件：

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

### ODataLib 相依性

 Storage Client Library for .NET 中的 ODataLib 相依性現已透過 ODataLib (5.0.2 版) 封裝解決，該封裝是由 NuGet 而非 WCF Data Services 提供。您可以直接下載 ODataLib 程式庫，或是由您的程式碼專案透過 NuGet 參照這些程式庫。這些 ODataLib 封裝具體來說是 [OData][3]、[Edm][4] 和
[Spatial][5]。

## <a name="create-container"> </a><span  class="short-header">建立容器</span>作法：建立容器

 所有儲存體 Blob 皆位於一個容器中。您可以使用 **CloudBlobClient** 物件來取得欲使用之容器的參照。如果容器不存在，您可以建立容器：

    // 從連接字串擷取儲存體帳戶。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 建立 Blob 用戶端。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 擷取容器的參照。 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // 於容器不存在時建立容器。
    container.CreateIfNotExists();

 根據預設，新容器屬私人性質，您必須指定儲存體存取金鑰才能從此容器下載 Blob。若要讓所有人都能使用容器中的檔案，您可以使用下列程式碼將容器設定為公用容器：

    container.SetPermissions(
    new BlobContainerPermissions { PublicAccess = 
 	BlobContainerPublicAccessType.Blob });

 網際網路上的任何人都可以看到公用容器中的 Blob，但要有適當的存取金鑰，才能修改或刪除這些 Blob。

 <h2> <a name="upload-blob"> </a><span  class="short-header">上傳至容器</span>作法：將 Blob 上傳至容器</h2>


 Azure Blob 儲存體支援區塊 Blob 和頁面 Blob。在大多數情況下，建議使用區塊 Blob 的類型。

 若要將檔案上傳至區塊 Blob，請取得容器參照，並使用該參照來取得區塊 Blob 參照。擁有 Blob 參照後，即可藉由呼叫 **UploadFromStream** 方法，將任何資料流上傳至其中。此操作會建立 Blob (如果其並不存在) 或覆寫 Blob (如果其已存在)。下列範例顯示如何將 Blob 上傳到容器，並假設已建立該容器。

    // 從連接字串擷取儲存體帳戶。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 建立 Blob 用戶端。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 擷取先前建立之容器的參照。
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // 擷取名為 "myblob" 之 Blob 的參照。
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // 以本機檔案的內容建立或覆寫 "myblob" Blob。
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
    blockBlob.UploadFromStream(fileStream);
    } 

## <a name="list-blob"> </a><span  class="short-header">列出容器中的 Blob</span>作法：列出容器中的 Blob

 若要列出容器中的 Blob，請先取得容器參照。然後您即可使用容器的 **ListBlobs** 方法來擷取 Blob 和 (或) 其中的目錄。若要針對傳回的 **IListBlobItem** 存取一組豐富的屬性與方法，您必須先將它轉換至 **CloudBlockBlob**、**CloudPageBlob** 或 **CloudBlobDirectory** 物件。如果不清楚類型，可使用類型檢查來決定要將其轉換至何種類型。下列程式碼示範如何擷取和輸出 `photos` 容器中每個項目的 URI：

    // 從連接字串擷取儲存體帳戶。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 建立 Blob 用戶端。 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 擷取先前建立之容器的參照。
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // 對容器中的項目執行迴圈，並輸出長度和 URI。
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
    	if (item.GetType() == typeof(CloudBlockBlob))
    	{
    		CloudBlockBlob blob = (CloudBlockBlob)item;
    		Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                                        
    	}
    	else if (item.GetType() == typeof(CloudPageBlob))
    	{
    		CloudPageBlob pageBlob = (CloudPageBlob)item;
    		Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
    	}
    	else if (item.GetType() == typeof(CloudBlobDirectory))
    	{
    		CloudBlobDirectory directory = (CloudBlobDirectory)item;
    		
    		Console.WriteLine("Directory: {0}", directory.Uri);
    	}
    }

 如上所示，Blob 服務也具備容器中之目錄的概念。正因如此，您能夠以更像資料夾的結構組織 Blob。例如，假設名為 `photos` 的容器中有下列一組區塊 Blob：

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

 當您在 'photos' 容器上呼叫 **ListBlobs** (如以上範例所示)，傳回的集合將包含 **CloudBlobDirectory** 和 **CloudBlockBlob** 物件，其分別代表最上層所包含的目錄和 Blob。以下是最終得到的輸出：

    Directory: https://<a ccountname>.blob.core.windows.net/photos/2010/
    Directory: https://<a ccountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<a ccountname>.blob.core.windows.net/photos/photo1.jpg

 此外，您可以選擇將 **ListBlobs** 方法的 **UseFlatBlobListing** 參數設定為 **true**。如此會導致不論目錄為何，都將每個 Blob 各當成一個 **CloudBlockBlob** 來傳回。以下是 **ListBlobs** 的呼叫：

    // 對容器中的項目執行迴圈，並輸出長度和 URI。
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

 以下則是結果：

    Block blob of length 4: https://<a ccountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<a ccountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<a ccountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<a ccountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<a ccountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<a ccountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<a ccountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<a ccountname>.blob.core.windows.net/photos/photo1.jpg

 如需詳細資訊，請參閱 [CloudBlobContainer.ListBlobs][CloudBlobContainer.ListBlobs]。

## <a name="download-blobs"> </a><span  class="short-header">下載 Blob</span>作法：下載 Blob

 若要下載 Blob，請先擷取 Blob 參照，然後呼叫 **DownloadToStream** 方法。下列範例使用 **DownloadToStream** 方法將 Blob 內容傳送給資料流物件，您接著可將該物件永久儲存成本機檔案。

    // 從連接字串擷取儲存體帳戶。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 建立 Blob 用戶端。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 擷取先前建立之容器的參照。
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // 擷取名為 "photo1.jpg" 之 Blob 的參照。
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // 將 Blob 內容儲存至檔案。
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
    blockBlob.DownloadToStream(fileStream);
    } 

 您也可以使用 **DownloadToStream** 方法，將 Blob 的內容當成文字字串下載。

    // 從連接字串擷取儲存體帳戶。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 建立 Blob 用戶端。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 擷取先前建立之容器的參照。
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // 擷取名為 "myblob.txt" 之 Blob 的參照
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");
    string text;
    using (var memoryStream = new MemoryStream())
    {
    	blockBlob2.DownloadToStream(memoryStream);
    	text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"> </a><span  class="short-header">刪除 Blob</span>作法：刪除 Blob

 若要刪除 Blob，請先取得 Blob 參照，然後在該參照上呼叫 **Delete** 方法。

    // 從連接字串擷取儲存體帳戶。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 建立 Blob 用戶端。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 擷取先前建立之容器的參照。
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // 擷取名為 "myblob.txt" 之 Blob 的參照。
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // 刪除 Blob。
    blockBlob.Delete(); 

## <a name="next-steps"></a><span  class="short-header">後續步驟</span>後續步驟

 了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

 *  如需可用 API 的完整詳細資訊，請檢視 Blob 服務參考文件：
  *  [Storage Client Library for .NET 參考資料][6]
  *  [REST API 參考資料][7]
  

*  請參閱[在 Azure 中儲存和存取資料][8]，深入了解可使用 Azure 儲存體執行的更多進階工作。
*  如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。
  *  使用[資料表儲存體](/zh-tw/develop/net/how-to-guides/table-services/)儲存結構化資料。
  *  使用 [SQL Database](/zh-tw/develop/net/how-to-guides/sql-database/) 儲存關聯式資料。
  


 </appsettings>



[1]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee758697.aspx
[2]: http://www.windowsazure.com/zh-tw/develop/net/#
[3]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[4]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[5]: http://nuget.org/packages/System.Spatial/5.0.2
[6]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn495001(v=azure.10).aspx
[7]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dd179355
[8]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx