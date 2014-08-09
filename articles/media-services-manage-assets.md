<properties linkid="develop-media-services-how-to-guides-manage-assets" urlDisplayName="Manage Assets in Media Services" pageTitle="How to Manage Assets in Media Services - Azure" metaKeywords="" description="Learn how to manage assets on Media Services. You can also manage jobs, tasks, access policies, locators, and more. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Manage Assets in storage" authors="migree" solutions="" manager="" editor="" />

作法：管理儲存體中的資產
========================

本文是介紹 Azure 媒體服務程式設計的系列文章之一。上一個主題是[作法：保護資產](http://go.microsoft.com/fwlink/?LinkID=301813&clcid=0x409)。

在建立媒體資產並上傳至媒體服務之後，您就可以在伺服器上存取和管理這些資產。您也可以在伺服器上管理其他屬於媒體服務的物件，包括工作、作業、存取原則、定位器等等。

下列範例顯示如何依 assetId 來查詢資產。

``` {}
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
```

若要列出伺服器上可用的所有資產，您可以利用下列方法來逐一查看資產集合，並顯示每一個資產的詳細資料。

``` {}
 
static void ListAssets()
{
string waitMessage = "Building the list.This may take a few "
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
builder.AppendLine("Asset ID:" + asset.Id);
builder.AppendLine("Name:" + asset.Name);
builder.AppendLine("==============");
builder.AppendLine("******ASSET FILES******");

// Display the files associated with each asset. 
foreach (IAssetFile fileItem in asset.AssetFiles)
        {
builder.AppendLine("Name:" + fileItem.Name);
builder.AppendLine("Size:" + fileItem.ContentFileSize);
builder.AppendLine("==============");
        }
    }

// Display output in console.
Console.Write(builder.ToString());
}
```

下列程式碼片段會從媒體服務帳戶中刪除所有資產。

``` {}
foreach (IAsset asset in _context.Assets)
{
asset.Delete();
}
```

如需有關管理資產的詳細資訊，請參閱：

-   [使用 Media Services SDK for .NET 管理資產](http://msdn.microsoft.com/zh-tw/library/jj129589.aspx)
-   [使用 Media Services REST API 管理資產](http://msdn.microsoft.com/zh-tw/library/jj129583.aspx)

後續步驟
--------

現在您知道如何管理資產，請移至＜[如何透過下載來傳遞資產](http://go.microsoft.com/fwlink/?LinkID=301734&clcid=0x409)＞主題。

