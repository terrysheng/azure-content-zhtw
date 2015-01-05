<properties urlDisplayName="iOS Media Player Framework" pageTitle="使用 Azure 媒體服務的 iOS 媒體播放器架構" metaKeywords="" description="Learn how to use the Media Services iOS Media Player Framework library to create rich, dynamic apps.," metaCanonical="" services="media-services" documentationCenter="" title="How to use the Azure Media Services iOS Media Player Framework" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="mobile-ios" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />



#如何使用 Azure 媒體服務 iOS 媒體播放器架構

Azure 媒體服務 iOS 媒體播放器架構程式庫可讓 iPod、iPhone 和 iPad 開發人員輕鬆建立可即時建立並混合視訊與音訊資料流的多樣化動態用戶端應用程式。例如，顯示體育內容的應用程式可以輕鬆在其所選擇的任何位置插播廣告，並控制這些廣告的出現頻率 (即使主要內容倒轉回頭來重播也是一樣)。教育應用程式可以使用相同的功能，建立返回主要內容前在主要教學旁邊的內容 (也就是資訊看板) 等等。

一般而言，要建置一個能夠根據應用程式與使用者間的互動來建立內容資料流的應用程式，是件相當複雜的工作：您通常必須事先從頭建立整個資料流並將該資料流儲存在伺服器上。使用 iOS 媒體播放器架構，您可以建置可執行所有這些動作的用戶端應用程式，而不需要控制或修改主要內容資料流。您可以：

- 事先在用戶端裝置上排定內容資料流。
- 排定片頭廣告或插播。
- 排定片尾廣告或插播。
- 排定片中廣告或插播並建立廣告組合。
- 控制是要在每次內容時間軸倒轉回頭來重播時就播放片中廣告或插播，或只播放一次然後自行從時間軸中移除。
- 在發生任何事件 (無論是使用者按下按鈕或應用程式收到服務發出的通知) 時，直接將動態插入內容到時間軸中，例如，新聞內容節目可以傳送重大新聞的通知，而應用程式可以「暫停」主要內容，以動態載入重大新聞資料流。 

這些功能結合 iOS 裝置的媒體播放機能後，即可運用更少的資源在極短時間內達到相當多樣化的媒體效果。

SDK 包含一個 SamplePlayer 應用程式，以展示如何將 iOS 應用程式建置成使用大部分這些功能即時建立內容資料流，並且能夠讓使用者按下按鈕動態觸發插播。本教學課程將說明 SamplePlayer 應用程式的主要元件，並示範如何開始使用該應用程式做為您建立應用程式的起點。

## 開始使用範例播放器應用程式
下列步驟說明如何取得此應用程式，並介紹此應用程式中運用該架構的各區域。 

1. 複製 git 存放庫。 

    `git clone https://github.com/WindowsAzure/azure-media-player-framework`

2. 開啟位於 `azure-media-player-framework/src/iOS/HLSClient/` 的專案：**SamplePlayer.xcodeproj**。

 
3. 範例播放器的結構如下：

![HLS Sample Code structure](http://mingfeiy.com/wp-content/uploads/2013/01/HLS-Structure.png)

4. 在 iPad 資料夾下，有 2 個 .xib 檔：**SeekbarViewController** 及 **SamplePlayerViewController**。這些檔案負責建立 iPad 應用程式 UI 配置。在 iPhone 資料夾下也同樣有 2 個 .xib 檔，分別定義搜尋列和控制器。 

6. 主要應用程式邏輯位於 `Shared` 資料夾下的 **SamplePlayerViewController.m**。下列所述的大部分程式碼片段均位在該檔案中。 

## 了解 UI 配置
有 2 個 .xib 檔負責定義我們的播放器介面。(下列討論以 iPad 配置為例，但是 iPhone 配置相當類似，而且原則也相同。)
### SamplePlayerViewController_iPad.xib
![Sample Player Address Bar](http://mingfeiy.com/wp-content/uploads/2013/01/addressbar.png)

* [**媒體 URL**] 是用來載入媒體資料流的 URL。應用程式已預先填入媒體 URL 的清單，使用 URL 選取按鈕即可擇一使用。或者，您也可以輸入自己的 Http 即時資料流 (HLS) 內容 URL。此媒體內容將做為第一個主要內容。 
**注意：請勿將此 URL 留空。**

* [**URL 選取**] 按鈕可以讓您選取媒體 URL 清單中的替代 URL。

### SeekbarViewController_iPad.xib
![Seek Bar Controller](http://mingfeiy.com/wp-content/uploads/2013/01/controller.png)
* 使用 [**播放**] 按鈕可播放和暫停媒體播放。

* **搜尋列**顯示整個播放時間軸。進行搜尋時，請按住不放搜尋列上的搜尋按鈕，然後拖曳至所需的位置再放開。 

**注意**：檢視者進到廣告中搜尋時，新的搜尋列將出現，其中標示廣告的時間長度。主要搜尋列僅呈現主要內容的時間長度 (也就是說，廣告在主要搜尋列中的時間長度為 0)。

* **播放器時間**控制項將顯示兩個時間 (`Label:playerTime`)，例如 00:23/02:10。這裡 00:23 是目前播放時間，02:10 是媒體的總時間長度。 

* **SkipFroward 和 SkipBackward 按鈕**目前沒有作用，更新版將在近期內推出。

* 在主要內容播放時按 [**立即排定**] 按鈕會插入廣告 (您可以在程式碼後置中定義廣告來源 URL)。注意：在目前版本中，您無法在其他廣告播放時排定廣告。 

### 如何排定主要內容
排定 0 秒至 80 秒的內容剪輯：

    //Schedule the main content
    MediaTime *mediaTime = [[[MediaTime alloc] init] autorelease];
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
        
    int clipId = 0;
    if (![framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

在上一段範例程式碼中：

* **MediaTime** 物件可控制要當成主要內容來排定的視訊短片。在上一個範例中，視訊短片是排定為具有 80 秒的時間長度 (從 0 秒到 80 秒)；
* **clipBeginMediaTime** 代表視訊開始播放的開始時間。例如，如果 **clipBeginMediaTime** 為 5，則此視訊短片將於視訊短片的 5 秒處開始。
* **clipEndMediaTime** 代表視訊播放的結束時間。如果 **clipEndMediaTime** 為 100，視訊播放將於視訊短片的 100 秒處結束。
*我們接著要求架構進行 **appendContentClip** 來排定 **MediaTime**。上一個範例在 `[NSURL URLWithString:url]` 中設定主要內容 URL，並且使用 **withMedia** 設定該媒體的排程：
 `[framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])`。

**注意：**務必先排定主要內容再排定任何廣告 (包括片頭廣告)。 

### 變化：如果有兩個主要內容要播放，也可以使用下列程式碼，排定第二個短片在第一個短片之後播放：

    //Schedule second content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 30;
    mediaTime.clipEndMediaTime = 80;
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

在上一段程式碼之後這麼做將在主要內容時間軸排定兩個內容資料流。第一個內容將按照 `URLWithString:url` 排定，第二個內容將按照 `URLWithString:secondContent` 排定。對於第二個內容，內容將在媒體資料流 30 秒處開始，並在 80 秒處結束。 

## 廣告排定 
目前版本僅支援 **pauseTimeline=false** 廣告，這表示，廣告結束後，播放器將從主要內容中斷處繼續播放。 

以下為一些重點：
<ul><li> 排定廣告時，所有 **LinearTime.duration** 都必須為 0。</li>
<li> **clipEndMediaTime** 比廣告時間長度更長時，廣告將在完成後結束，而且不會擲回任何例外狀況。建議您確認廣告的原本時間長度是否在轉譯時間 (**clipEndMediaTime**) 內，以免錯失廣告機會。</li> 
<li> 支援片頭、片中和片尾廣告。片頭廣告只能排定在所有內容的一開頭。例如，在粗剪編輯 (RCE) 情況下，您無法為第二個內容排定片頭。 </li>
<li> 支援連續廣告和單次播放廣告，這些廣告可搭配片頭、片中或片尾廣告使用。</li>
<li> 廣告格式可以是 .Mp4 或 HLS。</li>
</ul>
### 如何排定片頭、片中、片尾廣告和廣告組合

####排定片頭廣告

    LinearTime *adLinearTime = [[[LinearTime alloc] init] autorelease];
    NSString *adURLString = @"http://smoothstreamingdemo.blob.core.windows.net/videoasset/WA-BumpShort_120530-1.mp4";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.clipBeginMediaTime = 0;
    adInfo.mediaTime.clipEndMediaTime = 5;
    adInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

**AdInfo** 物件代表廣告短片的所有資訊：
* **ClipURL** 是短片來源的 URL。
* **mediaTime** 屬性指出廣告播放的時間長度。(**clipBeginMediaTime** 是廣告的開始時間，**clipEndMediaTime** 是廣告的結束時間。)在上一段範例程式碼中，我們排定 5 秒的廣告，從廣告時間長度的 0 秒處開始播放到 5 秒處。
* 架構目前不使用**原則**物件。
* 如果不是廣告組合，您必須將 **appendTo** 值設定為 -1。 
* **type** 值可以是片頭、片中、片尾或廣告組合。對於片頭或片尾，由於沒有相關聯的時間，因此請指定類型。 

####排定片中廣告

如果您在上一段程式碼範例中新增 `adLinearTime.startTime = 23;`，廣告將在主要內容時間軸的 23 秒處開始播放。

####排定片尾廣告

    //Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    postAdInfo.mediaTime.clipEndMediaTime = 5;
    postAdInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

與排定片頭廣告時的唯一差別是 `postAdInfo.type = AdType_Postroll;`。上一段程式碼排定了 5 秒廣告做為片尾。 

#### 排定廣告組合
廣告組合是連續播放多段廣告的廣告時段。以下是在一個廣告組合中排定兩段廣告的程式碼。 

    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    adpodInfo1.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.mediaTime = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    int32_t adIndex = 0;
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
        
    NSString *adpodSt2=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-532531b8-fca4-4c15-86f6-45f9f45ec980/Windows%208_%20Sign%20in%20with%20a%20Smile.mp4?st=2012-11-28T16%3A35%3A26Z&se=2014-11-28T16%3A35%3A26Z&sr=c&si=c6ede35c-f212-4ccd-84da-805c4ebf64be&sig=zcWsj1JOHJB6TsiQL5ZbRmCSsEIsOJOcPDRvFVI0zwA%3D";
    AdInfo *adpodInfo2 = [[[AdInfo alloc] init] autorelease];
    adpodInfo2.clipURL = [NSURL URLWithString:adpodSt2];
    adpodInfo2.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo2.mediaTime.clipBeginMediaTime = 0;
    adpodInfo2.mediaTime.clipEndMediaTime = 17;
    adpodInfo2.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo2.type = AdType_Pod;
    adpodInfo2.appendTo = adIndex;
    if (![framework scheduleClip:adpodInfo2 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

以下是一些必須注意的重點：
* 對於第一個短片，**appendTo** 是 -1。而且當我們呼叫 `[framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex]`，`adIndex` 將收到唯一的值，表示廣告組合的這個第一個短片結束。然後，對於廣告組合的第二個短片，設定 **appendTo** 做為 `adpodInfo2.appendTo = adIndex;`，以指定第一個短片的結束處成為第二個短片的開始處，使第二個短片的開始與第一個短片的結束相連。 
* 接著，您必須將類型設定為 `AdType_Pod`，表示這是廣告組合。 

### 如何排定單次播放或「連續」廣告
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    oneTimeInfo.deleteAfterPlay = YES;

如上一段程式碼範例所示，如果將 **deleteAfterPlay** 設定為 **YES**，這個廣告將只播放一次。如果將 **deleteAfterPlay** 設定為 **NO**，這個廣告將持續播放，亦即我們所謂的「連續廣告」。
### 請參閱 [Azure 媒體播放器架構 Wiki] (英文)(https://github.com/WindowsAzure/azure-media-player-framework/wiki) 的詳細資訊。

<!--HONumber=35.1-->
