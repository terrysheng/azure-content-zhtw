<properties 
	pageTitle="如何管理媒體內容 - Azure 媒體服務" 
	description="了解如何在 Azure 媒體服務中管理您的媒體內容。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>





# 如何管理媒體服務中的內容 #

本主題說明如何使用 Azure 管理入口網站，管理您的媒體服務帳戶中的媒體內容。

您目前可以直接從入口網站執行下列內容作業：

- 檢視內容資訊，例如已發佈的狀態、已發佈的 URL、大小、上次更新的日期時間，以及資產是否加密。
- 上傳新的內容
- 索引內容
- 編碼內容
- 播放內容
- 發佈/取消發佈內容


## 作法：上傳內容 


1. 在[管理入口網站](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)中按一下 [**媒體服務**]，然後按一下媒體服務帳戶名稱。
2. 選取 [內容] 頁面。 
3. 按一下頁面或入口網站底部的 [**上傳**] 按鈕。 
4. 在 [**上傳內容**] 對話方塊中，瀏覽到想要的資產檔案。按一下檔案，然後按一下 [**開啟**] 或按 **Enter** 鍵。

	![UploadContentDialog][uploadcontent]

5. 在 [上傳內容] 對話方塊中，按一下核取按鈕以接受 [File and Content Name]。
6. 上傳隨即開始，而您可以在入口網站底部追蹤進度。  

	![JobStatus][status]

上傳完成之後，您將會看到新資產列在 [內容] 清單中。依照慣例，名稱的結尾會附加 "**-Source**"，以協助追蹤新的內容作為編碼工作的來源內容。

![ContentPage][contentpage]

如果上傳程序停止之後檔案大小值未更新，請按 [**Sync Metadata**] 按鈕。這樣會以儲存體中的實際檔案大小同步處理資產檔案大小，並重新整理 [內容] 頁面上的值。	

## 作法：索引內容

Azure 媒體索引器可讓您使媒體檔案的內容可供搜尋，及產生隱藏式輔助字幕和關鍵字的全文檢索記錄。您可以依照以下說明的步驟，使用管理入口網站編製內容的索引。不過，如果您想要更充分掌控要編製索引的檔案和索引工作的執行方式，您可以使用 Media Services SDK for .NET 或 REST API。如需詳細資訊，請參閱[使用 Azure 媒體索引器編製媒體檔案的索引](https://msdn.microsoft.com/zh-tw/library/azure/dn783455.aspx)。

下列步驟說明如何使用管理入口網站編製內容的索引。

1. 選取要編製索引的檔案。
	如果此檔案類型支援索引，則 [內容] 頁面底部的 [處理] 按鈕將會啟用。
1. 按 [處理] 按鈕。
2. 在 [**處理**] 對話方塊中，選擇 [**Azure 媒體索引器**] 處理器。
3. 然後，在 [處理] 對話方塊中，填寫輸入媒體檔案的詳細**標題**和**描述**資訊。
	
	![Process][process]

## 作法：編碼內容

若要透過網際網路傳遞數位視訊，您必須壓縮媒體。媒體服務提供媒體編碼程式，可讓您指定內容的編碼方式 (例如，要使用的轉碼器、檔案格式、解析度及位元速率。) 

使用 Azure 媒體服務時最常見的案例之一，是將調適性位元速率串流傳遞至您的用戶端。透過調適性位元速率串流，用戶端可以在視訊顯示時，根據目前的網路頻寬、CPU 使用率和其他因素，切換至較高或較低的位元速率串流。媒體服務支援下列調適性位元速率串流技術：HTTP 即時資料流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS (僅適用於 Adobe PrimeTime/Access 授權)。 

媒體服務所提供的動態封裝，可讓您以媒體服務支援的串流格式 (MPEG DASH、HLS、Smooth Streaming、HDS) 傳遞調適性位元速率 MP4 或 Smooth Streaming 編碼內容，而無須重新封裝成這些串流格式。 

若要使用動態封裝，您必須執行下列動作：

- 將您的 mezzanine (來源) 檔案編碼或轉換成一組調適性位元速率 MP4 檔案或調適性位元速率 Smooth Streaming 的檔案 (本教學課程稍後將示範編碼步驟)，  
- 針對您要傳遞內容的串流端點，取得至少一個隨選串流單元。如需詳細資訊，請參閱[如何調整隨選串流保留單元](http://azure.microsoft.com/zh-tw/documentation/articles/media-services-how-to-scale/)。

透過動態封裝，您只需要儲存及支付一種儲存格式的檔案，媒體服務將會根據用戶端的要求建立及提供適當的回應。 

請注意，除了能夠使用動態封裝功能以外，隨選串流保留單元也為您提供可購買的專用流出容量 (以 200 Mbps 為增量來購買)。依預設，隨選串流是以共用執行個體模型來設定，可與其他所有使用者共用伺服器資源 (例如，運算、流出容量等)。若要改進隨選串流輸送量，建議購買隨選串流保留單元。


本節說明您可以採用哪些步驟，使用 Azure 媒體編碼程式和管理入口網站為您的內容編碼。

1.  選取您要編碼的檔案。
	如果這種檔案類型支援編碼，則 [內容] 頁面底部的 [處理] 按鈕將會啟用。
4. 在 [**處理**] 對話方塊中，選取 [**Azure Media Encoder**] 處理器。
5. 選擇其中一個**編碼組態**。

	![Process2][process2]

		
	[Azure Media Encoder 的工作預設字串](https://msdn.microsoft.com/zh-tw/library/azure/dn619392.aspx)主題說明**調適性串流 (動態封裝) 的預設**、**漸進式下載的預設**、**調適性串流的傳統預設**等類別中的各項預設  有何意義。  


	**其他**組態的說明如下：

	+ **使用 PlayReady 內容保護進行編碼**m此預設格式會產生以 PlayReady 內容保護編碼的資產。  
	
	
		依預設會使用媒體服務 PlayReady 授權服務。若要指定其他可讓用戶端從中取得授權以播放 PlayReady 加密內容的服務，請使用 REST 或媒體服務 .NET SDK API。如需詳細資訊，請參閱[使用靜態加密保護您的內容]，並在媒體編碼程式預設中設定 **licenseAcquisitionUrl** 屬性。或者，您可以使用動態加密，並依照[使用 PlayReady 動態加密和授權傳遞服務](http://go.microsoft.com/fwlink/?LinkId=507720 )來設定 **PlayReadyLicenseAcquisitionUrl** 屬性。 
	+ **在 PC/Mac 上播放 (透過 Flash/Silverlight)**。此預設格式會產生具有下列特性的 Smooth Streaming 資產：使用 AAC 並以 96 kbps 編碼的 44.1 kHz 16 位元/樣本立體音訊 CBR，以及使用 H.264 Main Profile 並以範圍從 3400 kbps 到 400 kbps 的 6 位元速率編碼的 720p 視訊 CBR，以及兩個第二個 GOP。
	+ **透過 HTML5 播放 (IE/Chrome/Safari)**。此預設格式會產生具有下列特性的單一 MP4 檔案：使用 AAC 並以 128 kbps 編碼的 44.1 kHz 16 位元/樣本立體音訊 CBR，以及使用 H.264 Main Profile 並以 4500 kbps 編碼的 720p 視訊 CBR。
	+ **在 iOS 裝置和 PC/Mac 上播放**。此預設格式會產生特性與 Smooth Streaming 資產 (如上所述) 相同、但是其格式可以用來將 Apple HLS 資料流傳遞至 iOS 裝置的資產。 

5. 接著，輸入適當的易記輸出內容名稱，或接受預設值。然後，按一下核取按鈕以啟動編碼作業，而您可以在入口網站底部追蹤進度。
6. 按 [確定]。

	編碼完成後，[內容] 頁面會包含已編碼的檔案。 

	若要檢視編碼工作的進度，請切換至 [**工作**] 頁面。  


	如果編碼完成之後檔案大小值未更新，請按 [**Sync Metadata**] 按鈕。這樣會以儲存體中的實際檔案大小同步處理輸出資產檔案大小，並重新整理 [內容] 頁面上的值。	

## 作法：發佈內容

當您發佈內容時，系統會為您提供串流或漸進式下載 URL。您的用戶端將可使用此 URL 播放您的視訊。

1. 按一下您想要發佈的資產。 
2. 然後，按一下 [發佈] 按鈕。 
	
	將內容發佈至 URL 之後，該 URL 就可由能夠轉譯所編碼內容的用戶端播放程式開啟。

 ![PublishedContent][publishedcontent]

## 作法：從入口網站播放內容

管理入口網站會提供可用來測試視訊的媒體服務內容播放程式。

按一下想要的視訊內容，然後按一下入口網站底部的 [**播放**] 按鈕。 
 
從入口網站只能播放已發佈的內容。而且，您的瀏覽器必須支援編碼。


<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-manage-content/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-manage-content/media-services-process-video2.png
<!--HONumber=42-->
