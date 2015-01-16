<properties urlDisplayName="Manage Assets in Media Services" pageTitle="如何管理媒體服務中的資產 - Azure" metaKeywords="" description="了解如何管理媒體服務上的資產。您也可以管理工作、存取原則、定位器等項目。程式碼範例以 C# 撰寫，並使用 Media Services SDK for .NET。" metaCanonical="" services="media-services" documentationCenter="" title="How to: Manage Assets in storage" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />




<h1>作法：管理儲存體中的資產</h1>

本文是介紹 Azure 媒體服務程式設計的系列文章之一。上一個主題是[作法：保護資產](../media-services-protect-asset/)。

在建立媒體資產並上傳至媒體服務之後，您就可以在伺服器上存取和管理這些資產。您也可以在伺服器上管理其他屬於媒體服務的物件，包括工作、作業、存取原則、定位器等等。

下列範例說明如何依 assetId 來查詢資產。
<pre><code>
static IAsset GetAsset(string assetId)
{
    // Use a LINQ Select query to get an asset.
    var assetInstance =
        from a in _context.Assets
        where a.Id == assetId
        select a;
    // Reference the asset as an IAsset.
    IAsset asset = assetInstance.FirstOrDefault();

    return asset;
}
</code></pre> 

若要列出伺服器上可用的所有資產，您可以利用下列方法來逐一查看資產集合，並顯示每一個資產的詳細資料。
<pre><code> 
static void ListAssets()
{
    string waitMessage = "Building the list. This may take a few "
        + "seconds to a few minutes depending on how many assets "
        + "you have."
        + Environment.NewLine + Environment.NewLine
        + "Please wait..."
        + Environment.NewLine;
    Console.Write(waitMessage);

    // Create a Stringbuilder to store the list that we build. 
    StringBuilder builder = new StringBuilder();

    foreach (IAsset asset in _context.Assets)
    {
        // Display the collection of assets.
        builder.AppendLine("");
        builder.AppendLine("******ASSET******");
        builder.AppendLine("Asset ID: " + asset.Id);
        builder.AppendLine("Name: " + asset.Name);
        builder.AppendLine("==============");
        builder.AppendLine("******ASSET FILES******");

        // Display the files associated with each asset. 
        foreach (IAssetFile fileItem in asset.AssetFiles)
        {
            builder.AppendLine("Name: " + fileItem.Name);
            builder.AppendLine("Size: " + fileItem.ContentFileSize);
            builder.AppendLine("==============");
        }
    }

    // Display output in console.
    Console.Write(builder.ToString());
}
</code></pre>
下列程式碼片段會從媒體服務帳戶中刪除所有資產。
<pre><code>
foreach (IAsset asset in _context.Assets)
{
    asset.Delete();
}
</code></pre>

如需有關管理資產的詳細資訊，請參閱：
<ul>
<li><a href="http://msdn.microsoft.com/zh-tw/library/jj129589.aspx">使用 Media Services SDK for .NET 管理資產</a></li>
<li><a href="http://msdn.microsoft.com/zh-tw/library/jj129583.aspx">使用 Media Services REST API 管理資產</a></li></ul>


<h2>後續步驟</h2>
現在您知道如何管理資產，請移至[如何透過下載來傳遞資產](../media-services-deliver-asset-download/) 主題。

<!--HONumber=35.1-->
