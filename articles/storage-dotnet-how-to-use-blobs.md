<properties urlDisplayName="Blob Service" pageTitle="如何使用 .NET 的 Blob 儲存體 | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use Microsoft Azure Blob storage to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# 如何使用 .NET 的 Blob 儲存體

本指南將示範如何使用 
。這些範例均以 C\# 撰寫，
使用 Azure Storage Client Library for .NET。所涵蓋的案例包括
**上傳**、**列出**、**下載**及**刪除** Blob。如需
Blob 的詳細資訊，請參閱[後續步驟][]一節。

> [WACOM.NOTE] 本指南以 Azure .NET Storage Client Library 2.x 和更新版本為對象。建議的版本是儲存體用戶端程式庫 4.x，可透過 [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) 或從 [Azure SDK for .NET](/zh-tw/downloads/) 中取得。請參閱下列[作法：以程式設計方式存取 Blob 儲存體][]，以詳細了解如何取得儲存體用戶端程式庫。

##目錄

-   [什麼是 Blob 儲存體][]
-   [概念][]
-   [建立 Azure 儲存體帳戶][]
-   [設定儲存體連接字串][]
-   [作法：以程式設計方式存取 Blob 儲存體][]
-   [作法：建立容器][]
-   [作法：將 Blob 上傳至容器][]
-   [作法：列出容器中的 Blob][]
-   [作法：下載 Blob][]
-   [作法：刪除 Blob][]
-   [作法：以非同步方式分頁列出 Blob][]
-   [後續步驟][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a><span  class="short-header">建立 Azure 儲存體帳戶</span>
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a><span  class="short-header">設定儲存體連接字串</span>

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span  class="short-header">作法：以程式設計方式存取 Blob 儲存體</span>

###取得組件
建議您使用 NuGet 來取得 `Microsoft.WindowsAzure.Storage.dll` 組件。在 [**方案總管]** 中以滑鼠右鍵按一下專案，再選取 [**管理 NuGet 封裝]**。在線上搜尋 "WindowsAzure.Storage"，再按一下 [**安裝**] 以安裝 Azure 儲存體封裝與相依性。

`Microsoft.WindowsAzure.Storage.dll` 也隨附於 Azure SDK for .NET (可自 <a href="http://www.windowsazure.com/zh-tw/develop/net/#">.NET Developer Center</a> 下載)。此組件會安裝至 `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\` 目錄中。

###命名空間宣告
將下列命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何\# 檔案
內的頂端：

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

確定已參照 `Microsoft.WindowsAzure.Storage.dll` 組件。

###擷取連接字串
您可以使用 **CloudStorageAccount** 類型來代表 
儲存體帳戶資訊。如果您使用 
Azure 專案範本且 (或) 具有 
Microsoft.WindowsAzure.CloudConfigurationManager 參照，可以 
可以使用 **CloudConfigurationManager** 類型，
擷取 Azure 服務設定中的儲存體連接字串與
儲存體帳戶資訊：

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

如果您是建立不含 Microsoft.WindowsAzure.CloudConfigurationManager 參照的應用程式，且連接字串位於 `web.config` 或 `app.config` (如上所示)，則可使用 **ConfigurationManager** 來擷取連接字串。  您需要將 System.Configuration.dll 參照新增至專案，並為其新增其他命名空間宣告：

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

**CloudBlobClient** 類型可讓您擷取物件，這些物件代表
Blob 儲存體服務中儲存的容器和 Blob。下列
下列程式碼會使用儲存體帳戶名稱和帳戶金鑰來建立 **CloudBlobClient** 
物件：

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###ODataLib 相依性
Storage Client Library for .NET 中的 ODataLib 相依性現已透過 ODataLib (5.0.2 版) 封裝解決，該封裝是由 NuGet 而非 WCF Data Services 提供。  您可以直接下載 ODataLib 程式庫，或是由您的程式碼專案透過 NuGet 參照這些程式庫。  這些 ODataLib 封裝具體來說是 [OData]、[Edm] 和 [Spatial]。

## <a name="create-container"> </a><span  class="short-header">作法：建立容器</span>

儲存體 Blob 中的每個 Blob 必須位於一個容器中。此範例說明如何建立尚不存在的容器：

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 建立 Blob 用戶端。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 擷取容器的參照。 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // 於容器不存在時建立容器。
    container.CreateIfNotExists();

根據預設，新容器屬私人性質，您必須指定
儲存體存取金鑰才能從此
容器下載 blob。若要讓所有人都能使用容器中的檔案，
您可以使用下列程式碼將容器設定為公用
容器：

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

網際網路上的任何人都可以看到公用容器中的 Blob，但
要有適當的存取金鑰，才能修改或刪除這些 Blob。

## <a name="upload-blob"> </a><span  class="short-header">作法：將 Blob 上傳至容器</span>

Azure Blob 儲存體支援區塊 Blob 和頁面 Blob。  在大多數情況下，建議使用區塊 Blob 的類型。

若要將檔案上傳至區塊 Blob，請取得容器參照，並使用
該參照來取得區塊 Blob 參照。擁有 Blob 參照後，即可
藉由呼叫 **UploadFromStream** 方法，將任何資料流上傳至其中。此操作會建立 Blob (如果其並不存在) 
或覆寫 Blob (如果其已存在)。下列範例顯示如何將 Blob 上傳到容器，並假設已建立該容器。

    // Retrieve storage account from connection string.
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

##<a name="list-blob"> </a><span  class="short-header">作法：列出容器中的 Blob</span>

若要列出容器中的 Blob，請先取得容器參照。您
您即可使用容器的 **ListBlobs** 方法來擷取 Blob 和 (或) 其中的
目錄。若要針對 
傳回的 **IListBlobItem** 存取一組豐富的屬性與方法，您必須先將它轉換至 **CloudBlockBlob**, 
**CloudPageBlob** 或 **CloudBlobDirectory** 物件。如果不清楚類型，可使用 
類型檢查來決定要將其轉換至何種類型。  下列程式碼 
示範如何擷取和輸出 
 `photos` 容器中每個項目的 URI：

    // Retrieve storage account from connection string.
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

			Console.WriteLine("Block blob of length {0}:{1}", blob.Properties.Length, blob.Uri);
                                        
		}
		else if (item.GetType() == typeof(CloudPageBlob))
		{
			CloudPageBlob pageBlob = (CloudPageBlob)item;

			Console.WriteLine("Page blob of length {0}:{1}", pageBlob.Properties.Length, pageBlob.Uri);

		}
		else if (item.GetType() == typeof(CloudBlobDirectory))
		{
			CloudBlobDirectory directory = (CloudBlobDirectory)item;
			
			Console.WriteLine("Directory:{0}", directory.Uri);
		}
	}

如上所示，Blob 服務也具備容器中之目錄的
概念。正因如此，您能夠以更像資料夾的結構
組織 Blob。例如，假設此容器中有名為 
的容器中有下列一組區塊 Blob：

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

當您在 'photos' 容器上呼叫 **ListBlobs** 時，傳回的集合將
包含 **CloudBlobDirectory** 和 **CloudBlockBlob** 物件，
其分別代表最上層所包含的目錄和 Blob。以下是最終得到的輸出：

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


此外，您可以選擇將 **ListBlobs** 方法的 **UseFlatBlobListing** 參數設定為   
**true**。如此會導致不論目錄為何，都將每個 Blob 各當成一個 **CloudBlockBlob**
來傳回。  以下是 **ListBlobs**的呼叫：

    // 對容器中的項目執行迴圈，並輸出長度和 URI。
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

以下則是結果：

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

如需詳細資訊，請參閱 [CloudBlobContainer.ListBlobs][] (英文)。

## <a name="download-blobs"> </a><span  class="short-header">作法：下載 Blob</span>

若要下載 Blob，請先擷取 Blob 參照，然後呼叫 **DownloadToStream** 方法。下列
範例使用 **DownloadToStream** 方法將 Blob 內容傳送
給資料流物件，您接著可將該物件儲存為本機檔案。

    // Retrieve storage account from connection string.
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

	// Retrieve storage account from connection string.
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

##<a name="delete-blobs"> </a><span  class="short-header">作法：刪除 Blob</span>

若要刪除 Blob，請先取得 Blob 參照，然後在該參照上呼叫
**Delete** 方法。

    // Retrieve storage account from connection string.
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


##<a name="list-blobs-async"> </a><span  class="short-header">作法：以非同步方式分頁列出 Blob</span> 

如果您要列出大量的 Blob，或是要控制在單一列出作業中傳回的結果數，您可以在結果頁面中列出 Blob。此範例說明如何以非同步方式分頁傳回結果，使執行不會因為等待大型結果集傳回而中斷。

此範例說明一般 Blob 列出方式，但您也可以執行階層式列出，方法是將**ListBlobsSegmentedAsync** 方法的 `useFlatBlobListing` 參數設為 `false`。

範例方法會呼叫非同步方法，因此前面必須加上 `async` 關鍵字，且必須傳回 **Task** 物件。為 **ListBlobsSegmentedAsync** 方法指定的 await 關鍵字會擱置範例方法的執行，直到列出工作完成為止。

    async public static Task ListBlobsSegmentedInFlatListing()
    {
        // Retrieve storage account from connection string.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // 建立 Blob 用戶端。
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // 擷取先前建立之容器的參照。
        CloudBlobContainer container = blobClient.GetContainerReference("myblobs");

        //分頁列出 Blob。
        Console.WriteLine("List blobs in pages:");

        //List blobs with a paging size of 10, for the purposes of the example. 
		//The first call does not include the continuation token.
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(
                "", true, BlobListingDetails.All, 10, null, null, null);

        //Enumerate the result segment returned.
        int i = 0;
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token, if there are additional pages of results.
        BlobContinuationToken continuationToken = resultSegment.ContinuationToken;

        //Check whether there are more results and list them in pages of 10 while a continuation token is returned.
        while (continuationToken != null)
        {
            //This overload allows control of the page size. 
			//You can return all remaining results by passing null for the maxResults parameter, 
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync(
					"", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            // 取得下一個接續權杖。
            continuationToken = resultSegment.ContinuationToken;
        }
    }

## <a name="next-steps"></a><span  class="short-header">後續步驟</span>

了解檔案儲存體的基礎概念之後，請參考下列連結
以了解如何執行更複雜的儲存工作。
<ul>
<li>如需可用 API 的完整詳細資訊，請檢視 Blob 服務參考文件：
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Storage Client Library for .NET 參考資料</a>
    </li>
    <li><a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/dd179355">REST API 參考資料</a></li>
  </ul>
</li>
<li>請參閱 <a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx">在 Azure 中儲存和存取資料</a>，深入了解可使用 Azure 儲存體執行的更多進階工作。</li>
<li>若要了解如何在 Azure 網站的後端程序中使用 Azure 儲存體，請參閱<a href="/zh-tw/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">開始使用 Azure WebJobs SDK</a>。</li>
<li>如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。
  <ul>
    <li>使用<a href="/zh-tw/documentation/articles/storage-dotnet-how-to-use-tables/">資料表儲存體</a>儲存結構化資料。</li>
    <li>使用<a href="/zh-tw/documentation/articles/storage-dotnet-how-to-use-queues/">佇列儲存體</a>儲存非結構化資料。</li>
    <li>使用 <a href="/zh-tw/documentation/articles/sql-database-dotnet-how-to-use/">SQL Database</a> 儲存關聯式資料。</li>
  </ul>
</li>
</ul>

  [後續步驟]: #next-steps
  [什麼是 Blob 儲存體]: #what-is
  [概念]: #concepts
  [建立 Azure 儲存體帳戶]: #create-account
  [設定儲存體連接字串]: #setup-connection-string
  [作法：以程式設計方式存取 Blob 儲存體]: #configure-access
  [作法：建立容器]: #create-container
  [作法：將 Blob 上傳至容器]: #upload-blob
  [作法：列出容器中的 Blob]: #list-blob
  [作法：下載 Blob]: #download-blobs
  [作法：刪除 Blob]: #delete-blobs
  [作法：以非同步方式分頁列出 Blob]: #list-blobs-async
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [在 Azure 中儲存及存取資料]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx
  [Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
  [設定連接字串]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee758697.aspx
  [.NET 用戶端程式庫參考]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API 參考資料]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
