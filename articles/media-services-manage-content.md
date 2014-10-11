<properties linkid="manage-services-mediaservices-manage-media-services-content" urlDisplayName="How to manage media content" pageTitle="How to manage media content - Azure Media Services" metaKeywords="" description="Learn how to manage your media content in Azure Media Services." metaCanonical="" services="media-services" documentationCenter="" title="How to Manage Content in Media Services" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# 如何管理媒體服務中的內容

Azure 媒體服務內容檢視可讓您管理自己媒體服務帳戶的媒體內容。

您目前可以直接從入口網站執行下列內容作業：

-   檢視內容資訊 (例如前次更新的已發佈狀態、已發佈 URL、大小和日期時間)。
-   上傳新的內容
-   編碼內容
-   播放內容視訊
-   發佈/取消發佈內容
-   刪除內容

## 作法：上傳內容

1.  在[管理入口網站][]中按一下 [媒體服務]，然後按一下媒體服務帳戶名稱。
2.  選取 [內容] 頁面。
3.  按一下頁面或入口網站底部的 [上傳] 按鈕。
4.  在 [Upload content] 對話方塊中，瀏覽到想要的資產檔案。按一下檔案，然後按一下 [開啟] 或按 **Enter** 鍵。

    ![UploadContentDialog][]

5.  在 [Upload Content] 對話方塊中，按一下核取按鈕以接受 [File and Content Name]。
6.  上傳隨即開始，而您可以在入口網站底部追蹤進度。

    ![JobStatus][]

上傳完成之後，您將會看到新資產列在 [內容] 清單中。依照慣例，名稱的結尾會附加 "**-Source**"，以協助追蹤新的內容作為編碼工作的來源內容。

如果上傳程序停止之後檔案大小值未更新，請按 **Sync Metadata** 按鈕。這樣會以儲存體中的實際檔案大小同步處理資產檔案大小，並重新整理 [內容] 頁面上的值。

## 作法：編碼內容

1.  在[管理入口網站][]中按一下 [媒體服務]，然後按一下媒體服務帳戶名稱。
2.  按一下頁面頂端的 [內容] 頁面。
3.  按一下想要用於編碼工作的來源視訊，然後按一下頁面底部的 [編碼]。
4.  在 Azure Media Encoder 對話方塊中，從其中一個一般或進階編碼預設格式中進行選擇。

    **一般預設格式**

    -   在 PC/Mac 上播放 (透過 Flash/Silverlight)。此預設格式會產生具有下列特性的 Smooth Streaming 資產：使用 AAC 並以 96 kbps 編碼的 44.1 kHz 16 位元/樣本立體音訊 CBR，以及使用 H.264 Main Profile 並以範圍從 3400 kbps 到 400 kbps 的 6 位元速率編碼的 720p 視訊 CBR，以及兩個第二個 GOP。
    -   透過 HTML5 播放 (IE/Chrome/Safari)。此預設格式會產生具有下列特性的單一 MP4 檔案：使用 AAC 並以 128 kbps 編碼的 44.1 kHz 16 位元/樣本立體音訊 CBR，以及使用 H.264 Main Profile 並以 4500 kbps 編碼的 720p 視訊 CBR。
    -   在 iOS 裝置和 PC/Mac 上播放。此預設格式會產生特性與 Smooth Streaming 資產 (如上所述) 相同、但是其格式可以用來將 Apple HLS 資料流傳遞至 iOS 裝置的資產。

    **進階預設格式**

    -   [Azure Media Encoder 的工作預設字串][]主題說明 [進階預設格式] 清單中每個預設格式的意義。

    ![EncoderDialog][]

    入口網站目前不支援 Media Encoder 所支援的所有編碼格式。也不支援媒體資產加密\\解密。您可以透過程式設計方式執行這些工作，如需詳細資訊，請參閱[使用 Media Services SDK for .NET 建立應用程式][]和 [Azure Media Encoder 的工作預設字串][]。

5.  在 Azure Media Encoder 對話方塊中，輸入想要的好記輸出內容名稱，或是接受預設值。然後，按一下核取按鈕以啟動編碼作業，而您可以在入口網站底部追蹤進度。

編碼完成之後，您的檢視應該會與下面的螢幕擷取畫面類似。

![PortalViewUploadCompleted][portaloverview]

如果編碼完成之後檔案大小值未更新，請按 [Sync Metadata] 按鈕。這樣會以儲存體中的實際檔案大小同步處理輸出資產檔案大小，並重新整理 [內容] 頁面上的值。

## 作法：發佈內容

1.  在[管理入口網站][]中按一下 [媒體服務]，然後按一下媒體服務帳戶名稱。
2.  按一下頁面頂端的 [內容] 頁面。
3.  按一下未發佈的資產。按一下發佈按鈕以發佈至公用 URL。將內容發佈至 URL 之後，該 URL 就可由能夠轉譯所編碼內容的用戶端播放程式開啟。

   ![PublishedContent][]

## 作法：從入口網站播放內容

1.  在[管理入口網站][]中按一下 [媒體服務]，然後按一下媒體服務帳戶名稱。
2.  按一下頁面頂端的 [內容] 頁面。
3.  按一下想要的視訊內容，然後按一下入口網站底部的 [播放] 按鈕。從入口網站只能播放已發佈的內容。而且，您的瀏覽器必須支援編碼。

<!-- Images -->

  [管理入口網站]: http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409
  [portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
  [UploadContentDialog]: ./media/media-services-manage-content/UploadContent.png
  [JobStatus]: ./media/media-services-manage-content/Status.png
  [Azure Media Encoder 的工作預設字串]: http://go.microsoft.com/fwlink/?LinkId=270865
  [EncoderDialog]: ./media/media-services-manage-content/EncoderDialog2.png
  [使用 Media Services SDK for .NET 建立應用程式]: http://go.microsoft.com/fwlink/?LinkId=270866
  [PublishedContent]: ./media/media-services-manage-content/media-services-upload-content-published.png
