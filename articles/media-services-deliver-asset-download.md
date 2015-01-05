<properties urlDisplayName="Delivering Media Assets" pageTitle="如何傳遞媒體資產 - Azure" metaKeywords="" description="Learn about options for delivering media assets that have been uploaded to Media Services in Azure. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver an Asset by Download" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





<h1>作法：透過下載來傳遞資產</h1>
本文是介紹 Azure 媒體服務程式設計的系列文章之一。上一個主題是＜[作法：管理資產](../media-services-manage-assets/)。

此主題將討論已上傳至媒體服務的媒體資產有哪些傳遞選項。您可以透過多種應用程式案例來傳遞媒體服務內容。您可以下載媒體資產，或使用定位器加以存取。您可以將媒體內容傳送至另一個應用程式，或是另一個內容提供者。若要改善效能和延展性，您也可以使用 Azure CDN 之類的內容傳遞網路 (CDN) 來傳遞內容。

此範例將說明如何從媒體服務下載媒體資產。程式碼會以工作 ID 查詢與媒體服務帳戶相關聯的工作，並存取其 **OutputMediaAssets** 集合 (這是執行工作後所產生的一或多個輸出媒體資產)。此範例將說明如何從工作下載輸出媒體資產，但您也可以用相同的方式來下載其他資產。

<pre><code> 
// Download the output asset of the specified job to a local folder.
static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
{
    // This method illustrates how to download a single asset. 
    // However, you can iterate through the OutputAssets
    // collection, and download all assets if there are many. 

    // Get a reference to the job. 
    IJob job = GetJob(jobId);

    // Get a reference to the first output asset. If there were multiple 
    // output media assets you could iterate and handle each one.
    IAsset outputAsset = job.OutputMediaAssets[0];

	// Create a SAS locator to download the asset
    IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
    ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);

    BlobTransferClient blobTransfer = new BlobTransferClient
    {
        NumberOfConcurrentTransfers = 20,
        ParallelTransferThreadCount = 20
    };

    var downloadTasks = new List<Task>();
    foreach (IAssetFile outputFile in outputAsset.AssetFiles)
    {
        // Use the following event handler to check download progress.
        outputFile.DownloadProgressChanged += DownloadProgress;

        string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

        Console.WriteLine("File download path:  " + localDownloadPath);

        downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

        outputFile.DownloadProgressChanged -= DownloadProgress;
    }

    Task.WaitAll(downloadTasks.ToArray());

    return outputAsset;
}

static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
{
    Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
}
</code></pre>

Weitere Informationen zur Auslieferung von Medienobjekten finden Sie unter:
<ul>
<li><a href="http://msdn.microsoft.com/zh-tw/library/jj129575.aspx">使用 Media Services for .NET 傳遞資產</a></li>
<li><a href="http://msdn.microsoft.com/zh-tw/library/jj129578.aspx">使用 Media Services REST API 傳遞資產</a></li>
</ul>

<h2>後續步驟</h2>
此主題說明了從 Azure 儲存體下載資產的方式。如需其他資產傳遞方式的相關資訊，請移至[如何傳遞串流內容](../media-services-deliver-streaming-content/) 主題。

<!--HONumber=35.1-->
