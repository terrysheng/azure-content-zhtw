<properties linkid="develop-media-services-how-to-guides-deliver-apple-live-streaming" urlDisplayName="Deliver Apple HTTP Live Streaming (HLS)" pageTitle="How to Deliver Apple HTTP Live Streaming (HLS) - Azure" metaKeywords="" description="Learn how to create a locator to Apple HTTP Live Stream (HLS) content on Media Services origin server. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver Apple HLS streaming content" authors="migree" solutions="" manager="" editor="" />

作法：傳遞 Apple HLS 串流內容
=============================

本文是介紹 Azure 媒體服務程式設計的系列文章之一。上一個主題是[作法：傳遞串流內容](http://go.microsoft.com/fwlink/?LinkID=301942&clcid=0x409)。

本主題說明如何為媒體服務原始伺服器上的 Apple HTTP 即時資料流 (HLS) 內容建立定位器。透過此方式，您可以建置 Apple HLS 內容的 URL，並將它提供給 Apple iOS 裝置進行播放。建置定位器 URL 的基本方式相同。為原始伺服器上的 Apple HLS 串流資產路徑建置定位器，然後建置可連結至串流內容之資訊清單的完整 URL。

下列程式碼範例假設您已取得 HLS 串流資產的參考，且程式碼中參考了名為 **assetToStream** 的變數。在您執行此程式碼對資產產生原始定位器之後，您可以在 iOS 裝置 (例如 iPad 或 iPhone) 中使用產生的 URL 來播放串流內容。

建置 Apple HLS 串流內容的定位器：

1.  取得資產中的資訊清單檔的參考
2.  定義存取原則
3.  呼叫 CreateLocator 來建立原始定位器
4.  建立資訊清單檔的 URL

下列程式碼顯示如何實作這些步驟：

``` {}
static ILocator GetStreamingHLSOriginLocator( string targetAssetID)
{
    // Get a reference to the asset you want to stream.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Get a reference to the HLS streaming manifest file from the  
    // collection of files in the asset. 
    var theManifest =
                        from f in assetToStream.AssetFiles
                        where f.Name.EndsWith(".ism")
                        select f;

    // Cast the reference to a true IAssetFile type. 
    IAssetFile manifestFile = theManifest.First();
    IAccessPolicy policy = null;
    ILocator originLocator = null;

    // Create a 30-day readonly access policy. 
    policy = _context.AccessPolicies.Create("Streaming HLS Policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
                policy,
                DateTime.UtcNow.AddMinutes(-5));

    // Create a URL to the HLS streaming manifest file.Use this for playback
    // in Apple iOS streaming clients.
    string urlForClientStreaming = originLocator.Path
        + manifestFile.Name + "/manifest(format=m3u8-aapl)";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();

    // Return the locator. 
    return originLocator;
}
```

如需有關傳遞資產的詳細資訊，請參閱：

-   [使用 Media Services for .NET 傳遞資產](http://msdn.microsoft.com/zh-tw/library/jj129575.aspx)
-   [使用 Media Services REST API 傳遞資產](http://msdn.microsoft.com/zh-tw/library/jj129578.aspx)

後續步驟
--------

本主題為最後一個「使用 Azure 媒體服務」主題。我們已涵蓋設定電腦來進行媒體服務開發，以及執行一般程式設計工作。如需關於對媒體服務進行程式設計的詳細資訊，請參閱下列資源：

-   [Azure 媒體服務文件](http://go.microsoft.com/fwlink/?linkid=245437)
-   [開始使用 Media Services SDK for .NET](http://go.microsoft.com/fwlink/?linkid=252966)
-   [使用 Media Services SDK for .NET 建立應用程式](http://go.microsoft.com/fwlink/?linkid=247821)
-   [使用 Azure Media Services REST API 建立應用程式](http://go.microsoft.com/fwlink/?linkid=252967)
-   [媒體服務論壇](http://social.msdn.microsoft.com/Forums/en-US/MediaServices/threads)
-   [如何監視媒體服務帳戶](http://www.windowsazure.com/zh-tw/manage/services/media-services/how-to-monitor-a-media-services-account/)
-   [如何管理媒體服務中的內容](http://www.windowsazure.com/zh-tw/manage/services/media-services/how-to-manage-content-in-media-services/)

