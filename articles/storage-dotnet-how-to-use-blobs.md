<properties urlDisplayName="Blob Service" pageTitle="如何使用 .NET 的 Blob 儲存體 | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use Microsoft Azure Blob storage to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="tamram" />

# 如何使用 .NET 的 Blob 儲存體

本指南將示範如何使用
Azure Blob 儲存體服務執行一般案例。這些範例均以 C# 撰寫，並
使用 Azure Storage Client Library for .NET。涵蓋的案例包括：
**上傳**、**列出**、**下載**和**刪除** Blob。如需
Blob 的詳細資訊，請參閱[後續步驟][]一節。

> [WACOM.NOTE] 本指南以 Azure .NET Storage Client Library 2.x 和更新版本為對象。建議的版本是透過 [NuGet] 提供(https://www.nuget.org/packages/WindowsAzure.Storage/) 或隨 [Azure SDK for .NET] 一起安裝的 Storage Client Library 4.x(/zh-tw/downloads/)。請參閱[作法：以程式設計方式存取 Blob 儲存體][]，以詳細了解如何取得儲存體用戶端程式庫。

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

##<a name="create-account"></a>建立 Azure 儲存體帳戶
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a>設定儲存體連接字串

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>作法：以程式設計方式存取 Blob 儲存體

###取得組件
建議您使用 NuGet 來取得 `Microsoft.WindowsAzure.Storage.dll` 組件。在 [**方案總管**] 中以滑鼠右鍵按一下專案，然後選擇 [**管理 NuGet 封裝**]。在線上搜尋 "WindowsAzure.Storage"，再按一下 [**安裝**] 以安裝 Azure 儲存體封裝與相依性。

`Microsoft.WindowsAzure.Storage.dll` 也隨附於 Azure SDK for .NET (可自 <a href="http://www.windowsazure.com/zh-tw/develop/net/#">.NET Developer Center 下載)</a>。此組件會安裝至 `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\` 目錄中。

###命名空間宣告
將下列命名空間宣告加入至任何 C\# 檔案的頂端
(您想要在該檔案中以程式設計方式存取 Azure 儲存體)：

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

確定已參照 `Microsoft.WindowsAzure.Storage.dll` 組件。

###擷取連接字串
您可以使用 **CloudStorageAccount** 類型來代表 
儲存體帳戶資訊。如果使用 
Azure 專案範本和 (或) 具有 
Microsoft.WindowsAzure.CloudConfigurationManager 參照， 
可以使用 **CloudConfigurationManager** 類型，
擷取 Azure 服務設定中的儲存體連接字串與儲存體帳戶
資訊：

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

如果您是建立不含 Microsoft.WindowsAzure.CloudConfigurationManager 參照的應用程式，且連接字串位於 `web.config` 或 `app.config` (如上所示)，則可使用 **ConfigurationManager** 來擷取連接字串。您需要將 System.Configuration.dll 參照新增至專案，並為其新增其他命名空間宣告：

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

**CloudBlobClient** 類型可讓您擷取代表
Blob 儲存體服務內所儲存之容器和 Blob 的物件。Auch die Eigenschaften
下列程式碼將建立 **CloudBlobClient** 物件，方法是使用
以上擷取的儲存體帳戶物件：

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###ODataLib 相依性
Storage Client Library for .NET 中的 ODataLib 相依性現已透過 ODataLib (5.0.2 版) 封裝解決，該封裝是由 NuGet 而非 WCF Data Services 提供。您可以直接下載 ODataLib 程式庫，或是由您的程式碼專案透過 NuGet 參照這些程式庫。特定 ODataLib 封裝是 [OData]、[Edm] 和 [Spatial]。

## <a name="create-container"> </a>作法：建立容器

儲存體 Blob 中的每個 Blob 必須位於一個容器中。此範例說明如何建立尚不存在的容器：

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

根據預設，新容器屬私人性質，您必須指定儲存體存取金鑰才能從此容器下載 Blob。若要讓所有人都能使用容器中的檔案，您可以使用下列程式碼將容器設定為公用容器：

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

網際網路上的任何人都可以看到公用容器中的 Blob，但要有適當的存取金鑰，才能修改或刪除這些 Blob。

## <a name="upload-blob"> </a>作法：將 Blob 上傳至容器

Azure Blob 儲存體支援區塊 Blob 和頁面 Blob。在大多數情況下，建議使用區塊 Blob 的類型。

若要將檔案上傳至區塊 Blob，請取得容器參照，並使用該參照來取得區塊 Blob 參考。擁有 Blob 參考後，即可藉由呼叫 **UploadFromStream()** 方法，將任何資料流上傳至其中。此操作會建立 Blob (如果其並不存在) 或覆寫 Blob (如果其已存在)。下列範例顯示如何將 Blob 上傳到容器，並假設已建立該容器。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

##<a name="list-blob"> </a>作法：列出容器中的 Blob

若要列出容器中的 Blob，請先取得容器參照。然後您即可使用容器的 **ListBlobs** 方法來擷取 Blob 和 (或) 其中的目錄。若要針對傳回的 **IListBlobItem** 存取一組豐富的屬性與方法，您必須先將它轉換成 **CloudBlockBlob**、**CloudPageBlob** 或 **CloudBlobDirectory** 物件。如果不清楚類型，可使用類型檢查來決定要將其轉換至何種類型。下列程式碼示範如何擷取和輸出 
`photos` 容器中每個項目的 URI：

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client. 
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve reference to a previously created container.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Loop over items within the container and output the length and URI.
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

當您在 'photos' 容器上呼叫 **ListBlobs** 時，傳回的集合將包含 **CloudBlobDirectory** 和 **CloudBlockBlob** 物件，
其分別代表最上層所包含的目錄和 Blob。以下是最終得到的輸出：

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


此外，您可以選擇將 **

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

and here would be the results:

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

For more information, see [CloudBlobContainer.ListBlobs][].

## <a name="download-blobs"> </a>How to: Download blobs

To download blobs, first retrieve a blob reference and then call the **DownloadToStream** method. The following
example uses the **DownloadToStream** method to transfer the blob
contents to a stream object that you can then persist to a local file.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

You can also use the **DownloadToStream** method to download the contents of a blob as a text string.

	// Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Retrieve reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

##<a name="delete-blobs"> </a>How to: Delete blobs

To delete a blob, first get a blob reference and then call the
**Delete** method on it.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete(); 


##<a name="list-blobs-async"> </a>How to: List blobs in pages asynchronously

If you are listing a large number of blobs, or you want to control the number of results you return in one listing operation, you can list blobs in pages of results. This example shows how to return results in pages asynchronously, so that execution is not blocked while waiting to return a large set of results.

This example shows a flat blob listing, but you can also perform a hierarchical listing, by setting the `useFlatBlobListing` parameter of the **ListBlobsSegmentedAsync** method to `false`.

Because the sample method calls an asynchronous method, it must be prefaced with the `async` keyword, and it must return a **Task** object. The await keyword specified for the **ListBlobsSegmentedAsync** method suspends execution of the sample method until the listing task completes.

    async public static Task ListBlobsSegmentedInFlatListing()
    {
        // Retrieve storage account from connection string.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.GetContainerReference("myblobs");

        //List blobs in pages.
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

            //Get the next continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
    }

## <a name="next-steps"></a>Next steps

Now that you've learned the basics of blob storage, follow these links to learn how to do more complex storage tasks.
<ul>
<li>View the Blob service reference documentation for complete details about available APIs:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Storage Client Library for .NET reference</a>
    </li>
    <li><a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/dd179355">REST API reference</a></li>
  </ul>
</li>
<li>Learn about more advanced tasks you can perform with Azure Storage at <a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx">Storing and Accessing Data in Azure</a>.</li>
<li>Learn how to work with Azure Storage in backend processes for Azure Websites at <a href="/zh-tw/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Get Started with the Azure WebJobs SDK</a>.</li>
<li>View more feature guides to learn about additional options for storing data in Azure.
  <ul>
    <li>Use <a href="/zh-tw/documentation/articles/storage-dotnet-how-to-use-tables/">Table Storage</a> to store structured data.</li>
    <li>Use <a href="/zh-tw/documentation/articles/storage-dotnet-how-to-use-queues/">Queue Storage</a> to store unstructured data.</li>
    <li>Use <a href="/zh-tw/documentation/articles/sql-database-dotnet-how-to-use/">SQL Database</a> to store relational data.</li>
  </ul>
</li>
</ul>

  [Next Steps]: #next-steps
  [What is Blob Storage]: #what-is
  [Concepts]: #concepts
  [Create an Azure Storage Account]: #create-account
  [Setup a storage Connection String]: #setup-connection-string
  [How To: Programmatically access Blob Storage]: #configure-access
  [How To: Create a Container]: #create-container
  [How To: Upload a Blob into a Container]: #upload-blob
  [How To: List the Blobs in a Container]: #list-blob
  [How To: Download Blobs]: #download-blobs
  [How To: Delete blobs]: #delete-blobs
  [How to: List blobs in pages asynchronously]: #list-blobs-async
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=35_1-->
