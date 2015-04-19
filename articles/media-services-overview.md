<properties 
	pageTitle="Azure 媒體服務概觀" 
	description="本主題提供 Azure 媒體服務的概觀" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="juliako"/>

#Azure 媒體服務概觀

Microsoft Azure 媒體服務是一個可延伸的雲端型平台，供開發人員建置可擴充的媒體管理和傳遞應用程式。媒體服務是以 REST API 為基礎，可讓您安全地上傳、儲存、編碼和封裝視訊或音訊內容，以用於隨選和即時串流傳遞給各種用戶端 (例如電視、電腦和行動裝置)。

您可以建置完全採用媒體服務的端對端工作流程。您也可以選擇在工作流程的某些部分採用協力廠商元件。例如，使用第三方編碼器來進行編碼；然後使用媒體服務上傳、保護、封裝、傳遞。

若要建置媒體服務解決方案，您可以使用：

- [媒體服務 REST API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- 其中一個可用的用戶端 SDK：[Azure Media Services SDK for .NET](https://github.com/Azure/azure-sdk-for-media-services)、[Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java)、[Azure Media Services for Node.js](https://github.com/fritzy/node-azure-media)、[Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
- 現有的工具：[Azure 管理入口網站](http://manage.windowsazure.com/)或 [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer)。

**服務等級協定 (SLA)**：媒體服務保證媒體服務編碼的 REST API 交易可用性高達 99.9%。至少購買一個串流保留單元時，針對現有的媒體內容，隨選串流可保證以 99.9% 的可用性成功服務要求。可用性是以每月帳單週期來計算。如需詳細資訊，請下載 [SLA 文件](https://www.microsoft.com/download/details.aspx?id=39302)。

下列海報說明從媒體建立到耗用量的 Azure 媒體服務工作流程。您可以從這裡下載海報：[Azure 媒體服務海報](http://www.microsoft.com/download/details.aspx?id=38195)。

![Overview][overview]

##概念

如需詳細資訊，請參閱[概念](media-services-concepts.md)。

##選擇您的媒體經驗

分享視訊內容的第一個步驟是決定您要用戶端擁有何種經驗。您的客戶將如何檢視視訊內容？客戶會連線到網際網路嗎？他們是在電腦還是手持裝置上檢視您的內容？您的客戶期待 HD 視訊嗎？這些問題可以協助您提供客戶最佳的觀看經驗。

###存取視訊
 
客戶有四種存取視訊的基本方式：

- 離線檢視 
- 漸進式下載
- 串流\調適性位元速率串流

####離線檢視

若要離線檢視視訊，客戶必須先將整個視訊下載到他的電腦或裝置上。視訊通常相當大，所以完成下載可能需要花一些時間。離線檢視的好處是，只要將視訊下載到裝置後，您不需要網路連線也能觀看。 

####漸進式下載

漸進式下載需要客戶連線到網際網路，並可在下載完整視訊前就讓客戶開始觀看視訊。離線檢視和漸進式下載方法都需要客戶用來觀看視訊的裝置具有足夠的儲存空間，以便容納整個視訊。

####串流

串流技術也需要網際網路連線，但一次只下載一小片段的視訊，且在該視訊片段顯示之後就將其捨棄。這對檢視裝置上的儲存空間要求非常小。網路連線的輸送量不一，但無論網路頻寬為何，客戶仍期望能夠觀看視訊。調適性位元速率技術可讓視訊播放器應用程式判斷網路狀況，並由數種位元速率中進行選擇。當網路通訊降級時，用戶端會選擇較低的位元速率，讓播放器能夠持續以較低的視訊品質來播放視訊。當網路狀況改善時，用戶端會切換至較高的位元速率，以提高視訊品質。Azure 媒體服務支援下列調適性位元速率技術：HTTP 即時資料流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS。

###使用何種裝置

需要決定的另一個決策是：您的客戶使用何種裝置來檢視您的視訊。媒體服務支援網頁瀏覽器、智慧型手機、平板電腦、XBOX、機上盒和網路連線電視。

####網頁瀏覽器

網頁瀏覽器可以在 Windows 電腦、Macintosh 電腦和智慧型手機上執行。在電腦或 Macintosh 電腦上執行時，您可以運用大螢幕及大型儲存容量的優勢。這可讓您串流較高品質的視訊。Windows 電腦或 Macintosh 電腦可以使用原生應用程式或 HTML 相容網頁瀏覽器來檢視由媒體服務傳遞的視訊。原生應用程式可以支援 Smooth Streaming、Apple HLS、漸進式下載或離線檢視。HTML5 網頁支援漸進式下載。


####智慧型手機

智慧型手機的螢幕較小，儲存容量也較小。串流是這些裝置的最佳選擇。支援 iPhone、Windows Phone 和 Android 手機。iPhone 和 Android 手機支援 Smooth Streaming 和 HLS。Windows Phone 支援 Smooth Streaming。

###平板電腦

平板電腦的螢幕比智慧型手機大，但通常儲存容量比較小。串流是平板電腦的最佳選擇。擁有較大儲存容量的平板電腦也可以運用離線檢視以及漸進式下載的優勢。

####XBox

XBox 主控台擁有較大的螢幕和較大的儲存容量，適合採用離線、漸進式下載和串流。
機上盒和網路連線電視
這些裝置也擁有大螢幕，但儲存容量很少，串流是最好的選擇。

###裝置支援的技術

下表顯示媒體服務支援的每種裝置類型和用戶端技術：
 
<table border="1">
<tr><th>裝置</th><th>技術</th></tr>
<tr><td>iOS</td><td>Smooth Streaming、Apple HLS、漸進式下載</td></tr>
<tr><td>Windows Phone 8</td><td>Smooth Streaming</td></tr>
<tr><td>Windows RT</td><td>Smooth Streaming</td></tr>
<tr><td>Windows</td><td>Smooth Streaming、漸進式下載</td></tr>
<tr><td>Android 手機</td><td>Smooth Streaming 和 Apple HLS</td></tr>
<tr><td>XBox</td><td>Smooth Streaming</td></tr>
<tr><td>Macintosh</td><td>Apple HLS、漸進式下載</td></tr>
<tr><td>機上盒、網路連線電視</td><td>Smooth Streaming、Apple HLS、漸進式下載</td></tr>
</table>


##常見的開發工作流程：隨選視訊和即時資料流

###隨選視訊：傳遞串流內容 

1. 將高品質夾層檔上傳到資產。
1. 編碼為調適性位元速率 MP4 集。
1. 設定資產傳遞原則 (用於動態封裝)。
1. (選擇性) 設定內容保護和金鑰授權原則 (用於動態加密)。
1. 發佈資產。
1. 串流發佈的內容。 

###隨選視訊：編製內容索引

1. 將高品質夾層檔上傳到資產。
1. 編製內容索引。
1. 使用索引內容。

###隨選視訊：提供漸進式下載 

1. 將高品質夾層檔上傳到資產。
1. 編碼為調適性位元速率 MP4 集或單一 MP4。
1. 發佈資產。
1. 漸進式下載內容。

[媒體服務隨選視訊](media-services-video-on-demand-workflow.md) 包含示範如何執行上述工作的主題連結。

###即時資料流

1. 建立並啟動通道。
1. 擷取通道內嵌 URL。
1. 啟動並設定您所選擇的即時轉碼器。
1. 擷取通道的預覽端點，並確認您的通道正確接收即時資料流。
1. 建立程式和資產。 
2. 設定資產的傳遞原則 (用於動態封裝)。
3. (選擇性) 設定內容保護和金鑰授權原則 (用於動態加密)。
1. 若要將資料流提供給使用者，請建立存取與程式相關聯資產所需的串流定位器。
1. 當您準備好開始串流和封存時，啟動程式。
1. 當您想停止串流和封存事件時，停止程式。
1. 刪除程式 (以及選擇性刪除資產)。

[媒體服務即時資料流](media-services-live-streaming-wrokflow.md) 包含示範如何執行上述工作的主題連結。

##用戶端

Azure 媒體服務提供一些工具，供您用來建立適用於大部分平台的豐富、動態用戶端播放器應用程式，此處所述的平台包括：iOS 裝置、Android 裝置、Windows、Windows Phone、Xbox 和機上盒。

- [Smooth Streaming Client SDK](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Microsoft Media Platform：Player Framework](http://playerframework.codeplex.com/) 
- [HTML5 Player Framework 文件](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Microsoft Smooth Streaming Plugin for OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Media Player Framework for iOS](https://github.com/Azure/azure-media-player-framework) 
- [授權 Microsoft(r) Smooth Streaming Client Porting Kit](https://www.microsoft.com/mediaplatform/sspk.aspx) 
- 建置 Windows 8 上的視訊應用程式 
- [XBOX 視訊應用程式開發](http://xbox.create.msdn.com/)

如需詳細資訊，請參閱[開發視訊播放器應用程式](media-services-develop-video-players.md)

##模式與實例指南

[模式與實例指南](https://wamsg.codeplex.com/)
[線上文件](https://msdn.microsoft.com/library/dn735912.aspx)
[可下載的電子書](https://www.microsoft.com/download/details.aspx?id=42629)

##支援

[Azure 支援](http://azure.microsoft.com/support/options/)提供 Azure 的支援選項，包括媒體服務。



<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png

<!--HONumber=47-->
