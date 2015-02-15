<properties 
	pageTitle="如何使用 .NET 的 Blob 儲存體 | Azure" 
	description="了解如何使用 Microsoft Azure Blob 儲存體來上傳、下載、列出及刪除 Blob 內容。範例是以 C# 撰寫的。" 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="tamram"/>

# 如何使用 .NET 的 Blob 儲存體

本指南將示範如何使用 Azure Blob 儲存體服務執行一般案例。這些範例是以 C\# 撰寫，並使用「適用於 .NET 的 Azure 儲存體用戶端程式庫」。所涵蓋的案例包括「上傳」****、「列出」****、「下載」****及「刪除」****Blob。如需有關 Blob 的詳細資訊，請參閱[後續步驟][]一節。

> [AZURE.NOTE] 本指南以 Azure .NET Storage Client Library 2.x 和更新版本為對象。建議的版本是「儲存體用戶端程式庫 4.x」，您可以透過 [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) 或從 [Azure SDK for .NET](/zh-tw/downloads/) 中取得。請參閱下面的[做法：以程式設計方式存取 Blob 儲存體][]，以詳細了解如何取得「儲存體用戶端程式庫」。

##目錄

-   [什麼是 Blob 儲存體][]
-   [概念][]
-   [建立 Azure 儲存體帳戶][]
-   [設定儲存體連接字串][]
-   [做法：以程式設計方式存取 Blob 儲存體][]
-   [做法：建立容器][]
-   [做法：將 Blob 上傳至容器][]
-   [做法：列出容器中的 Blob][]
-   [做法：下載 Blob][]
-   [做法：刪除 Blob][]
-   [做法：以非同步方式分頁列出 Blob][]
-   [後續步驟][]

[AZURE.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

[AZURE.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>做法：以程式設計方式存取 Blob 儲存體

###取得組件 建議您使用 NuGet 來取得 `Microsoft.WindowsAzure.Storage.dll` 組件。請在 [方案總管]**** 中您的專案上按一下滑鼠右鍵，然後選擇 [管理 NuGet 封裝]****。在線上搜尋 "WindowsAzure.Storage"，然後按一下 [安裝]**** 以安裝「Azure 儲存體」封裝與相依性。

`Microsoft.WindowsAzure.Storage.dll` 也隨附於 Azure SDK for .NET (可自 <a href="http://www.windowsazure.com/zh-tw/develop/net/#">.NET 開發人員中心</a>下載)。此組件會安裝在 `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\` 目錄。

###命名空間宣告 請將下列命名空間宣告，新增至您想要在其中以程式設計方式存取「Azure 儲存體」的所有 C\# 檔案內的頂端：

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

確定已參照  `Microsoft.WindowsAzure.Storage.dll` 組件。

###擷取連接字串 您可以使用 **CloudStorageAccount** 類型來代表「儲存體帳戶」資訊。如果您使用 Azure 專案範本且 (或) 具有 Microsoft.WindowsAzure.CloudConfigurationManager 參照，便可使用 **CloudConfigurationManager** 類型，從 Azure 服務組態擷取儲存體連接字串和儲存體帳戶資訊：

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

如果您建立的是不含 Microsoft.WindowsAzure.CloudConfigurationManager 參照的應用程式，且連接字串位於 `web.config` 或 `app.config`(如上所示)，則可使用 **ConfigurationManager** 來擷取連接字串。您需要將 System.Configuration.dll 參照新增至專案，並為其新增其他命名空間宣告：

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

**CloudBlobClient** 類型可讓您擷取物件，這些物件代表儲存在「Blob 儲存體服務」中的容器和 Blob。下列程式碼會使用我們在前面擷取的儲存體帳戶物件來建立 **CloudBlobClient** 物件：

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###「適用於 .NET 的儲存體用戶端程式庫」中的 ODataLib 相依性已透過 ODataLib (5.0.2 版) 封裝解決，該封裝是由 NuGet 而非 WCF Data Services 提供。您可以直接下載 ODataLib 程式庫，或是由您的程式碼專案透過 NuGet 參照這些程式庫。這些特定的 ODataLib 封裝是 [OData]、[Edm] 及 [Spatial]。

## <a name="create-container"> </a>做法：建立容器

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

## <a name="upload-blob"> </a>做法：將 Blob 上傳至容器

Azure Blob 儲存體支援區塊 Blob 和頁面 Blob。在大多數情況下，建議使用區塊 Blob 的類型。

若要將檔案上傳至區塊 Blob，請取得容器參照，並使用該參照來取得區塊 Blob 參照。取得 Blob 參照後，即可藉由呼叫 **UploadFromStream** 方法，將任何資料流上傳至 Blob。此操作會建立 Blob (如果其並不存在) 或覆寫 Blob (如果其已存在)。下列範例顯示如何將 Blob 上傳到容器，並假設已建立該容器。

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

##<a name="list-blob"> </a>做法：列出容器中的 Blob

若要列出容器中的 Blob，請先取得容器參照。然後您便可以使用容器的 **ListBlobs** 方法來擷取 Blob 和 (或) 其中的目錄。若要針對傳回的 **IListBlobItem** 存取一組豐富的屬性與方法，您必須先將它轉換成 **CloudBlockBlob**、**CloudPageBlob** 或 **CloudBlobDirectory** 物件。如果不清楚類型，可使用類型檢查來決定要將其轉換至何種類型。下列程式碼示範如何擷取和輸出 `photos` 容器中每個項目的 URI：

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

當您在 'photos' 容器上呼叫 **ListBlobs** (如以上範例所示)，傳回的集合將包含 **CloudBlobDirectory** 和 **CloudBlockBlob** 物件，分別代表最上層所包含的目錄和 Blob。以下是最終得到的輸出：

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


您也可以依選擇將 **ListBlobs** 方法的 **UseFlatBlobListing** 參數設定為 
**true**。這樣會導致不論目錄為何，都將每個 Blob 各當成一個 **CloudBlockBlob** 來傳回。以下是對 **ListBlobs** 的呼叫：

    // Loop over items within the container and output the length and URI.
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

如需詳細資訊，請參閱 [CloudBlobContainer.ListBlobs][]。

## <a name="download-blobs"> </a>做法：下載 Blob

若要下載 Blob，請先擷取 Blob 參照，然後呼叫 **DownloadToStream** 方法。下列範例使用 **DownloadToStream** 方法將 Blob 內容傳輸給資料流物件，您可以接著將該物件保存至本機檔案。

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

您也可以使用 **DownloadToStream** 方法將 Blob 的內容以文字字串方式下載。

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

##<a name="delete-blobs"> </a>做法：刪除 Blob

若要刪除 Blob，請先取得 Blob 參照，然後在該參照上呼叫 **Delete** 方法。

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


##<a name="list-blobs-async"> </a>做法：以非同步方式分頁列出 Blob

如果您要列出大量的 Blob，或是要控制在單一列出作業中傳回的結果數，您可以在結果頁面中列出 Blob。此範例說明如何以非同步方式分頁傳回結果，使執行不會因為等待大型結果集傳回而中斷。

此範例示範一般 Blob 列出方式，但您也可以執行階層式列出，方法是將 **ListBlobsSegmentedAsync** 方法的 `useFlatBlobListing` 參數設定為 `false`。

由於範例方法會呼叫非同步方法，因此其前面必須加上 `async` 關鍵字，並且必須傳回 **Task** 物件。為 **ListBlobsSegmentedAsync** 方法指定的 await 關鍵字會讓範例方法暫停執行，直到列出工作完成為止。

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

## <a name="next-steps"></a>後續步驟

了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。
<ul>
<li>如需可用 API 的完整詳細資訊，請檢視 Blob 服務參考文件：
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">適用於 .NET 的儲存體用戶端程式庫參考</a>
    </li>
    <li><a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/dd179355">REST API 參考</a></li>
  </ul>
</li>
<li>參閱<a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx">在 Azure 中儲存和存取資料，以深入了解可使用「Azure 儲存體」來執行的更多進階工作</a>。</li>
<li>了解如何使用下列工具來簡化您所撰寫要與「Azure 儲存體」搭配使用的程式碼：<a href="../websites-dotnet-webjobs-sdk/">Azure WebJobs SDK。</li>
<li>如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。
  <ul>
    <li>使用<a href="/zh-tw/documentation/articles/storage-dotnet-how-to-use-tables/">資料表儲存體</a>來儲存結構化資料。</li>
    <li>使用<a href="/zh-tw/documentation/articles/storage-dotnet-how-to-use-queues/">佇列儲存體</a>來儲存非結構化資料。</li>
    <li>使用 <a href="/zh-tw/documentation/articles/sql-database-dotnet-how-to-use/">SQL Database</a> 來儲存關聯式資料。</li>
  </ul>
</li>
</ul>

  [後續步驟]: #next-steps
  [什麼是 Blob 儲存體]: #what-is
  [概念]: #concepts
  [建立 Azure 儲存體帳戶]: #create-account
  [設定儲存體連接字串]: #setup-connection-string
  [做法：以程式設計方式存取 Blob 儲存體]: #configure-access
  [做法：建立容器]: #create-container
  [做法：將 Blob 上傳至容器]: #upload-blob
  [做法：列出容器中的 Blob]: #list-blob
  [做法：下載 Blob]: #download-blobs
  [做法：刪除 Blob]: #delete-blobs
  [做法：以非同步方式分頁列出 Blob]: #list-blobs-async
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [在 Azure 中儲存和存取資料]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx
  [Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
  [設定連接字串]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee758697.aspx
  [.NET 用戶端程式庫參考]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API 參考]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=42-->
