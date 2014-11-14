<properties urlDisplayName="Create a Media Processor" pageTitle="如何建立媒體處理器 - Azure" metaKeywords="" description="了解如何建立媒體處理器元件，為 Azure 媒體服務的媒體內容進行編碼、格式轉換、加密或解密。程式碼範例以 C# 撰寫，並使用 Media Services SDK for .NET。" metaCanonical="" services="media-services" documentationCenter="" title="作法：取得媒體處理器執行個體" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/26/2014" ms.author="juliako" />

# 作法：取得媒體處理器執行個體

本文是介紹 Azure 媒體服務程式設計的系列文章之一。上一個主題是＜[作法：建立加密資產並上傳至儲存體][作法：建立加密資產並上傳至儲存體]。

在媒體服務中，媒體處理器是可處理特定處理工作的元件，例如編碼、格式轉換、加密或解密媒體內容。您通常會在建立媒體內容的編碼、加密或格式轉換工作時建立媒體處理器。

下表提供每個可用媒體處理器的名稱和說明。

| 媒體處理器名稱                | 說明                                                                                                                                            | 相關資訊                                 |
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------|
| Azure Media Encoder           | 可讓您使用 Media Encoder 執行編碼工作。                                                                                                         | [Azure Media Encoder 的工作預設字串][Azure Media Encoder 的工作預設字串]   |
| Windows Azure Media Packager  | 可讓您將媒體資產從 .mp4 轉換為 Smooth Streaming 格式。此外，也可讓您將媒體資產從 Smooth Streaming 轉換為 Apple HTTP Live Streaming (HLS) 格式。 | [Azure Media Packager 的工作預設字串][Azure Media Packager 的工作預設字串]  |
| Windows Azure Media Encryptor | 可讓您使用 PlayReady Protection 為媒體資產加密。                                                                                                | [Azure Media Packager 的工作預設字串][1] |
| Azure Media Indexer           | 可讓您的媒體檔案和內容可供搜尋，以及產生隱藏式輔助字幕和關鍵字。                                                                                | N/A                                      |
| Storage Decryption            | 可讓您對使用儲存體加密功能加密的媒體資產進行解密。                                                                                              | N/A                                      |

下列方法將說明如何取得媒體處理器執行個體。此程式碼範例假設會使用名為 \*\*\_context\*\* 的模組層級變數來參考伺服器內容，如[作法：以程式設計方式連接到媒體服務][作法：以程式設計方式連接到媒體服務]一節所說明。

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
         var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

## 後續步驟

現在您已了解如何取得媒體處理器執行個體，接著請移至[如何為資產編碼][如何為資產編碼]主題，以了解如何使用 Azure Media Encoder 為資產編碼。

  [作法：建立加密資產並上傳至儲存體]: ../media-services-create-encrypted-asset-upload-storage/
  [Azure Media Encoder 的工作預設字串]: http://msdn.microsoft.com/zh-tw/library/jj129582.aspx
  [Azure Media Packager 的工作預設字串]: http://msdn.microsoft.com/zh-tw/library/hh973635.aspx
  [1]: http://msdn.microsoft.com/zh-tw/library/hh973610.aspx
  [作法：以程式設計方式連接到媒體服務]: ../media-services-set-up-computer/
  [如何為資產編碼]: ../media-services-encode-asset/
