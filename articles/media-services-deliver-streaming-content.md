<properties urlDisplayName="Deliver Streaming Content from Media Services" pageTitle="如何從媒體服務傳遞串流內容 â€" Azure" metaKeywords="" description="Learn how to deliver streaming content from Media Services using a direct URL. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="How to: Deliver streaming content" authors="juliako" manager="dwrede" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />


#作法：傳遞串流內容

本文是介紹 Azure 媒體服務程式設計的系列文章之一。上一個主題是[作法：透過下載來傳遞資產] (英文)(../media-services-deliver-asset-download/)。

除了從媒體服務下載媒體內容，您還可以使用調適性位元速率串流來傳遞內容。例如，您可以對媒體服務原始伺服器上的串流內容建立導向 URL，稱為定位器。用戶端應用程式 (例如 Microsoft Silverlight) 可以直接從定位器播放串流內容。

下列範例顯示如何為工作所產生的輸出資產建立原始定位器。範例假設您對含有順暢串流檔案的資產已取得參考，且程式碼中參考名為 **assetToStream** 的變數。 

建立串流內容的原始定位器：

   1.取得資產中的串流資訊清單檔 (.ism) 的參考 
   2.定義存取原則
   3.呼叫 CreateLocator 方法來建立原始定位器 
   4.建立資訊清單檔的 URL 

下列程式碼說明如何實作這些步驟：
<pre><code>
private static ILocator GetStreamingOriginLocator( string targetAssetID)
{
    // Get a reference to the asset you want to stream.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Get a reference to the streaming manifest file from the  
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
    policy = _context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    // Create an OnDemandOrigin locator to the asset. 
    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
        policy,
        DateTime.UtcNow.AddMinutes(-5));
            
    // Display some useful values based on the locator.
    Console.WriteLine("Streaming asset base path on origin: ");
    Console.WriteLine(originLocator.Path);
    Console.WriteLine();
    
    // Create a full URL to the manifest file. Use this for playback
    // in streaming media clients. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();
    
    // Display the ID of the origin locator, the access policy, and the asset.
    Console.WriteLine("Origin locator Id: " + originLocator.Id);
    Console.WriteLine("Access policy Id: " + policy.Id);
    Console.WriteLine("Streaming asset Id: " + assetToStream.Id);

    // Return the locator. 
    return originLocator;
}
</code></pre>

Weitere Informationen zur Auslieferung von Medienobjekten finden Sie unter:
<ul>
<li><a href="http://msdn.microsoft.com/zh-tw/library/jj129575.aspx">使用 Media Services for .NET 傳遞資產</a></li>
<li><a href="http://msdn.microsoft.com/zh-tw/library/jj129578.aspx">使用 Media Services REST API 傳遞資產</a></li>
</ul>

<h2>後續步驟</h2>
目前為止，我們已說明從 Azure 儲存體和使用 Smooth Streaming 來傳遞資產。下一個主題[如何傳遞 HLS 內容](../media-services-deliver-http-live-streaming-content/) 討論如何使用 Apple HTTP 即時資料流 (HLS) 傳遞串流內容。

<!--HONumber=35.1-->
