<properties linkid="develop-media-services-how-to-guides-create-media-processor" urlDisplayName="Create a Media Processor" pageTitle="How to Create a Media Processor - Azure" metaKeywords="" description="Learn how to create a media processor component to encode, convert format, encrypt, or decrypt media content for Azure Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="migree" solutions="" manager="" editor="" />

作法：取得媒體處理器執行個體
============================

本文是介紹 Azure 媒體服務程式設計的系列文章之一。上一個主題是[作法：建立加密資產並上傳至儲存體](http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409)。

在媒體服務中，媒體處理器是可處理特定處理工作的元件，例如編碼、格式轉換、加密或解密媒體內容。您通常會在建立媒體內容的編碼、加密或格式轉換工作時建立媒體處理器。

下表提供每個可用媒體處理器的名稱和說明。

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>媒體處理器名稱</th>
       <th>說明</th>
	<th>相關資訊</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Azure Media Encoder</td>
       <td>可讓您使用 Media Encoder 執行編碼工作。</td>
       <td><a href="http://msdn.microsoft.com/zh-tw/library/jj129582.aspx">Azure Media Encoder 的工作預設字串</a></td>
    </tr>
    <tr>
        <td>Azure Media Packager</td>
        <td>可讓您將媒體資產從 .mp4 轉換為 Smooth Streaming 格式。此外，也可讓您將媒體資產從 Smooth Streaming 轉換為 Apple HTTP Live Streaming (HLS) 格式。</td>
		<td><a href="http://msdn.microsoft.com/zh-tw/library/hh973635.aspx">Azure Media Packager 的工作預設字串</a></td>
    </tr>
    <tr>
        <td>Azure Media Encryptor</td>
        <td>可讓您使用 PlayReady Protection 為媒體資產加密。</td>
        <td><a href="http://msdn.microsoft.com/zh-tw/library/hh973610.aspx">Azure Media Packager 的工作預設字串</a></td>
    </tr>
    <tr>
        <td>Storage Decryption</td>
        <td>可讓您對使用儲存體加密功能加密的媒體資產進行解密。</td>
		<td>N/A</td>
    </tr>
  </tbody>
</table>

<br />

下列方法將說明如何取得媒體處理器執行個體。此程式碼範例假設會使用名為 **\_context** 的模組層級變數來參考伺服器內容，如[作法：以程式設計方式連接到媒體服務](http://www.windowsazure.com/en-us/develop/media-services/how-to-guides/set-up-computer-for-media-services)一節所說明。

``` {}
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

     if (processor == null)
         throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

     return processor;
}
```

後續步驟
--------

現在您已了解如何取得媒體處理器執行個體，接著請移至[如何為資產編碼](http://go.microsoft.com/fwlink/?LinkId=301753)主題，以了解如何使用 Azure Media Encoder 為資產編碼。

