<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use Media Services" authors="" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# 如何使用媒體服務

本指南說明如何使用 Azure 媒體服務來開始程式設計。本指南包含一組文章，並且包含媒體服務的技術概觀、針對媒體服務來設定 Azure 帳戶的步驟、開發用設定指南，以及一些說明如何完成一般程式設計工作的主題。示範的案例包括：上傳資產、加密資產、編碼資產，以及傳遞資產。這些範例均以 C# 撰寫，並使用 Media Services SDK for .NET。如需 Azure 媒體服務的詳細資訊，請參閱[後續步驟][]主題。

您也可以使用 OData 型 REST API 對媒體服務進行程式設計。您可以使用 .NET 語言或其他程式設計語言，建置對媒體服務進行 REST API 呼叫的應用程式。如需關於使用 Media Services REST API 進行程式設計的完整文件系列，請參閱[使用 Azure Media Services REST API 建立應用程式][]。

若要開始使用 Media Services REST API 或 Media Services SDK 進行程式設計，請先對您的 Azure 帳戶啟用媒體服務 (如[設定媒體服務的 Azure 帳戶][]一節所述)。

最新 Media Services SDK 文件位於[這裡][]。

## <a name="what-are"></a><span class="short header">什麼是媒體服務？</span>

Azure 媒體服務形成一個可延伸媒體平台，可整合 Microsoft Media Platform 精華與 Azure 中的協力廠商媒體元件。媒體服務提供雲端中的一個媒體管道，讓業界合作夥伴能夠擴充或取代元件技術。ISV 和媒體提供者可以使用媒體服務來建置端對端媒體解決方案。此概觀說明媒體服務的一般架構和常見開發案例。

下圖說明基本的媒體服務架構。

![Media Services Architecture][]

### 媒體服務功能支援

最新版本的媒體服務提供下列一組功能，以開發雲端中的媒體應用程式。

-   **Ingest**。Ingest 作業會將資產帶入系統中，例如，在將資產放入 Azure 儲存體之前將資產上傳和加密。媒體服務能夠與合作夥伴元件整合，以提供快速 UDP (使用者資料包通訊協定) 上傳解決方案。
-   **Encode**。Encode 作業包括編碼、變形和轉換媒體資產。您可以使用 Azure Media Encoder 在雲端中執行編碼工作。編碼選項如下：
  -   使用 Azure Media Encoder 並採用一系列的標準轉碼器和格式 (包括領先業界的 IIS Smooth Streaming、MP4 以及轉換為 Apple HTTP 即時資料流)。
  -   在對輸入和輸出有完整控制的情況下，轉換整個程式庫或個別檔案。
  -   支援眾多檔案類型、格式和轉碼器 (請參閱 [Azure Media Encoder 支援的轉碼器與檔案類型][])。
  -   支援的格式轉換。媒體服務可讓您將 ISO MP4 (.mp4) 轉換為 Smooth Streaming 檔案格式 (PIFF 1.3) (.ismv；.isma)。您也可以將 Smooth Streaming 檔案格式 (PIFF) 轉換為 Apple HTTP 即時資料流 (.msu8、.ts)。
-   **Protect**。保護內容，表示將即時串流或隨選內容加密，以進行安全的傳輸、儲存和傳遞。媒體服務提供跨 DRM 技術的解決方案來保護內容。目前支援的 DRM 技術有 Microsoft PlayReady 和 MPEG Common Encryption。未來將再支援其他 DRM 技術。
-   **Stream**。串流內容，表示將內容立即或隨選傳送至用戶端，或者您也可以從雲端下載特定媒體檔案。媒體服務提供跨格式的解決方案來串流內容。媒體服務提供 Smooth Streaming、Apple HTTP 即時資料流和 MP4 格式的串流出處支援。未來將再支援其他格式。您也可以使用協力廠商 CDN，實現調整為數百萬位使用者的選項，以順暢地傳遞串流內容。

### 媒體服務開發案例

媒體服務支援數個常見媒體開發案例，如下表所述。

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<thead>

<tr>

<th>
案例

</th>

<th>
說明

</th>

</tr>

</thead>

<tbody>

<tr>

<td>
建置端對端工作流程

</td>

<td>
完全在雲端中建置完整媒體工作流程。從上傳媒體到配送內容，媒體服務都提供一系列可合併使用的元件，以處理特定應用程式工作流程。目前功能包括上傳、儲存、編碼、格式轉換、內容保護和隨選串流傳遞。

</td>

</tr>

<tr>

<td>
建置混合式工作流程

</td>

<td>
您可以將媒體服務整合現有工具和程序。例如，編碼站台上的內容，然後將其上傳至媒體服務以轉碼為多種格式，並透過 Azure CDN 或協力廠商 CDN 進行傳遞。您可以透過標準 REST API 來個別呼叫媒體服務，以與外部應用程式和服務整合。

</td>

</tr>

<tr>

<td>
提供媒體播放程式的雲端支援

</td>

<td>
您可以跨多個裝置 (包括 iOS、Android 和 Windows 裝置) 和平台來建立、管理和傳遞媒體。

</td>

</tr>

</tbody>

</table>
</p>
### <a name="media-client"></a>媒體服務用戶端開發

使用 SDK 和播放程式架構延伸媒體服務解決方案的範圍，以建置媒體用戶端應用程式。如果開發人員想要讓建置的媒體服務應用程式能夠跨一系列的裝置和平台提供吸引人的使用者體驗，就適合使用這些用戶端。視目標裝置而定，有 Microsoft 和協力廠商合作夥伴提供的 SDK 與播放程式架構可選。

下列提供可用之用戶端 SDK 和播放程式架構的清單。如需這些和其他計劃性 SDK 與播放程式架構以及其能支援之功能的詳細資訊，請參閱媒體服務論壇中的[媒體服務用戶端開發][] (英文)。

#### Mac 和 PC 用戶端支援

針對 PC 和 Mac，您可以將目標設為使用 Microsoft Silverlight 或 Adobe Open Source Media Framework 的串流體驗。

-   [Smooth Streaming Client for Silverlight][] (英文)
-   [Microsoft Media Platform：Player Framework (Silverlight 版)][] (英文)
-   [Smooth Streaming Plugin for OSMF 2.0][] (英文)。如需關於如何使用此外掛程式的詳細資訊，請參閱[如何使用 Smooth Streaming Plugin for Adobe Open Source Media Framework][]。

#### Windows 8 應用程式

針對 Windows 8，您可以使用任何支援的開發語言和建構 (例如 HTML、Javascript、XAML、C# 和 C+) 來建置 Windows 市集應用程式。

-   [Smooth Streaming Client SDK for Windows 8][] (英文)。如需關於如何使用此 SDK 建立 Windows 市集應用程式的詳細資訊，請參閱[如何建置 Smooth Streaming Windows 市集應用程式][]。如需關於如何使用 HTML5 建立 Smooth Streaming 播放程式的詳細資訊，請參閱[逐步介紹：建置您的第一個 HTML5 Smooth Streaming 播放程式][] (英文)。

-   [Microsoft Media Platform：Player Framework (Windows 8 Windows 市集應用程式版)][] (英文)

#### Xbox

Xbox 支援可取用 Smooth Streaming 內容的 Xbox LIVE 應用程式。Xbox LIVE Application Development Kit (ADK) 包含：

-   Xbox LIVE ADK 的 Smooth Streaming 用戶端
-   Microsoft Media Platform：Player Framework (Xbox LIVE ADK 版)

#### 內嵌或專用裝置

如連接的電視、機上盒、藍光播放機、OTT 電視盒以及具有自訂應用程式開發架構和自訂媒體管道的行動裝置等裝置。Microsoft 提供下列可授權、並讓合作夥伴能夠針對此平台來移植 Smooth Streaming 播放的移植套件。

-   [Smooth Streaming Client Porting Kit][] (英文)
-   [Microsoft PlayReady Device Porting Kit][] (英文)

#### Windows Phone

Microsoft 提供可用來建置 Windows Phone 版優質視訊應用程式的 SDK。

-   [Smooth Streaming Client for Silverlight][] (英文)
-   [Microsoft Media Platform：Player Framework (Silverlight 版)][] (英文)

#### iOS 裝置

針對 iOS 裝置 (包括 iPhone、iPod 和 iPad)，Microsoft 隨附 SDK，供您用來建置這些平台的應用程式，以提供優質視訊內容，這個 SDK 就是：Smooth Streaming SDK for iOS Devices with PlayReady。只有被授權人才能取得 SDK，因此，如需詳細資訊，請[傳送電子郵件給 Microsoft][]。如需 iOS 開發的詳細資訊，請參閱 [iOS 開發人員中心][] (英文)。

#### Android 裝置

有幾家 Microsoft 合作夥伴都提供 Android 平台的 SDK，可新增在 Android 裝置上播放 Smooth Streaming 的功能。如需這些合作夥伴的詳細資料，請參閱[傳送電子郵件給 Microsoft][1]。

## 後續步驟

您已概略了解媒體服務，現在請移至[設定媒體服務的電腦][]主題。

  [後續步驟]: #next-steps
  [使用 Azure Media Services REST API 建立應用程式]: http://go.microsoft.com/fwlink/?linkid=252967
  [設定媒體服務的 Azure 帳戶]: #setup-account
  [這裡]: http://msdn.microsoft.com/en-us/library/hh973613.aspx
  [Media Services Architecture]: ./media/media-services-dotnet-how-to-use/wams-01.png
  [Azure Media Encoder 支援的轉碼器與檔案類型]: http://msdn.microsoft.com/en-us/library/hh973634
  [媒體服務用戶端開發]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/thread/e9092ec6-2dfc-44cb-adce-1dc935309d2a
  [Smooth Streaming Client for Silverlight]: http://www.microsoft.com/en-us/download/details.aspx?id=29940
  [Microsoft Media Platform：Player Framework (Silverlight 版)]: http://smf.codeplex.com/documentation
  [Smooth Streaming Plugin for OSMF 2.0]: http://go.microsoft.com/fwlink/?LinkId=275022
  [如何使用 Smooth Streaming Plugin for Adobe Open Source Media Framework]: http://go.microsoft.com/fwlink/?LinkId=275034
  [Smooth Streaming Client SDK for Windows 8]: http://go.microsoft.com/fwlink/?LinkID=246146
  [如何建置 Smooth Streaming Windows 市集應用程式]: http://go.microsoft.com/fwlink/?LinkId=271647
  [逐步介紹：建置您的第一個 HTML5 Smooth Streaming 播放程式]: http://msdn.microsoft.com/en-us/library/jj573656(v=vs.90).aspx
  [Microsoft Media Platform：Player Framework (Windows 8 Windows 市集應用程式版)]: http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home
  [Smooth Streaming Client Porting Kit]: http://www.microsoft.com/en-us/mediaplatform/sspk.aspx
  [Microsoft PlayReady Device Porting Kit]: http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx
  [傳送電子郵件給 Microsoft]: mailto:askdrm@microsoft.com
  [iOS 開發人員中心]: https://developer.apple.com/devcenter/ios/index.action
  [1]: mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices
  [設定媒體服務的電腦]: http://go.microsoft.com/fwlink/?LinkId=301751
