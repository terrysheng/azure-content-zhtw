<properties urlDisplayName="How to manage media content" pageTitle="如何管理媒體內容 - Azure 媒體服務" metaKeywords="" description="了解如何在 Azure 媒體服務中管理您的媒體內容。" metaCanonical="" services="media-services" documentationCenter="" title="How to Manage Content in Media Services" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





# 如何管理媒體服務中的內容 #
Azure 媒體服務內容檢視可讓您管理自己媒體服務帳戶的媒體內容。

您目前可以直接從入口網站執行下列內容作業：

- 檢視內容資訊 (例如前次更新的已發佈狀態、已發佈 URL、大小和日期時間)。
- 上傳新的內容
- 編碼內容
- 播放內容視訊
- 發佈/取消發佈內容
- 刪除內容


## 作法：上傳內容 ##


1. 在[管理入口網站](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)中，按一下 [**媒體服務**]，然後按一下媒體服務帳戶名稱。
2. 選取 [內容] 頁面。 
3. 按一下頁面或入口網站底部的 [**上傳**] 按鈕。 
4. 在 [**上傳內容**] 對話方塊中，瀏覽到想要的資產檔案。按一下檔案，然後按一下 [**開啟**] 或按 **Enter** 鍵。

	![UploadContentDialog][uploadcontent]

5. 在 [上傳內容] 對話方塊中，按一下核取按鈕以接受 [File and Content Name]。
6. 上傳隨即開始，而您可以在入口網站底部追蹤進度。  

	![JobStatus][status]

上傳完成之後，您將會看到新資產列在 [內容] 清單中。依照慣例，名稱的結尾會附加 "**-Source**"，以協助追蹤新的內容做為編碼工作的來源內容。

如果上傳程序停止之後檔案大小值未更新，請按 [**Sync Metadata**] 按鈕。這樣會以儲存體中的實際檔案大小同步處理資產檔案大小，並重新整理 [內容] 頁面上的值。	


## 作法：編碼內容

1. 在[管理入口網站](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)中，按一下 [**媒體服務**]，然後按一下媒體服務帳戶名稱。 
2. 按一下頁面頂端的 [內容] 頁面。
3. 按一下想要用於編碼工作的來源影片，然後按一下頁面底部的 [**編碼**]。
4. 在 Azure Media Encoder 對話方塊中，從其中一個一般或進階編碼預設格式中進行選擇。

	**一般預設格式**

	+ **在 PC/Mac 上播放 (透過 Flash/Silverlight)**。此預設格式會產生具有下列特性的 Smooth Streaming 資產：使用 AAC 並以 96 kbps 編碼的 44.1 kHz 16 位元/樣本立體音訊 CBR，以及使用 H.264 Main Profile 並以範圍從 3400 kbps 到 400 kbps 的 6 位元速率編碼的 720p 視訊 CBR，以及兩個第二個 GOP。
	+ **透過 HTML5 播放 (IE/Chrome/Safari)**。此預設格式會產生具有下列特性的單一 MP4 檔案：使用 AAC 並以 128 kbps 編碼的 44.1 kHz 16 位元/樣本立體音訊 CBR，以及使用 H.264 Main Profile 並以 4500 kbps 編碼的 720p 視訊 CBR。
	+ **在 iOS 裝置和 PC/Mac 上播放**。此預設格式會產生特性與 Smooth Streaming 資產 (如上所述) 相同、但是其格式可以用來將 Apple HLS 資料流傳遞至 iOS 裝置的資產。 
	+ **使用 PlayReady 內容保護進行編碼**。此預設格式會產生以 PlayReady 內容保護編碼的資產。若要讓此預設格式運作，您必須啟用 PlayReady 授權傳遞服務。若要這麼做，請移至 [**內容保護**] 索引標籤，然後在 [Branding Reporting] 資料表中新增一個資料列。媒體服務 PlayReady 授權服務會在您按 [儲存] 後的幾分鐘內啟用。   
	
   依預設會使用媒體服務 PlayReady 授權服務。若要指定其他可讓用戶端從中取得授權以播放 PlayReady 加密內容的服務，請使用 REST 或媒體服務 .NET SDK API。如需詳細資訊，請參閱[使用靜態加密保護您的內容]，並在媒體編碼程式預設中設定 **licenseAcquisitionUrl** 屬性。或者，您可以使用動態加密，並依照**使用 PlayReady 動態加密和授權傳遞服務**來設定 [PlayReadyLicenseAcquisitionUrl] 屬性(http://go.microsoft.com/fwlink/?LinkId=507720 )。 

   請注意，只有在您註冊 PlayReady 內容保護預覽功能後，才會顯示此選項。若要註冊預覽功能，您必須完成下列頁面上說明的程序：[Microsoft Azure 預覽功能](http://azure.microsoft.com/zh-tw/services/preview/)。  
	
   **進階預設格式**
	
	+ [Azure Media Encoder 的工作預設字串](http://go.microsoft.com/fwlink/?LinkId=270865) 主題說明「進階預設格式」清單中每個預設值的意思。 

   ![EncoderDialog][encoder]

   入口網站目前不支援 Media Encoder 所支援的所有編碼格式。也不支援媒體資產加密\解密。您可以透過程式設計方式執行這些工作，如需詳細資訊，請參閱[使用 Media Services SDK for .NET 建立應用程式](http://go.microsoft.com/fwlink/?LinkId=270866) 和 [Azure Media Encoder 的工作預設字串](http://go.microsoft.com/fwlink/?LinkId=270865)。


5. 在 Azure Media Encoder 對話方塊中，輸入想要的好記輸出內容名稱，或是接受預設值。然後，按一下核取按鈕以啟動編碼作業，而您可以在入口網站底部追蹤進度。

   編碼完成之後，您的檢視應該會與下面的螢幕擷取畫面類似。 

   ![PortalViewUploadCompleted][portaloverview]

   如果編碼完成之後檔案大小值未更新，請按 [**Sync Metadata**] 按鈕。這樣會以儲存體中的實際檔案大小同步處理輸出資產檔案大小，並重新整理 [內容] 頁面上的值。	

## 作法：發佈內容

1. 在[管理入口網站](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)中，按一下 [**媒體服務**]，然後按一下媒體服務帳戶名稱。
2. 按一下頁面頂端的 [內容] 頁面。
3. 按一下未發佈的資產。按一下發佈按鈕以發佈至公用 URL。將內容發佈至 URL 之後，該 URL 就可由能夠轉譯所編碼內容的用戶端播放程式開啟。

 ![PublishedContent][publishedcontent]

## 作法：從入口網站播放內容


1. 在[管理入口網站](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)中，按一下 [**媒體服務**]，然後按一下媒體服務帳戶名稱。
2. 按一下頁面頂端的 [內容] 頁面。
3. 按一下想要的視訊內容，然後按一下入口網站底部的 [**播放**] 按鈕。從入口網站只能播放已發佈的內容。而且，您的瀏覽器必須支援編碼。


<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[商標]: ./media/branding-reporting.png

<!--HONumber=35.1-->
