<properties pageTitle="在 iOS 應用程式中開始使用 Azure 行動服務" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="遵循此教學課程，可開始使用 Azure 行動服務進行 iOS 開發。 " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="hero-article" ms.date="10/10/2014" ms.author="krisragh" />

# <a name="getting-started"> </a>開始使用行動服務

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>本教學課程說明如何使用 Azure 行動服務在 iOS 應用程式中新增雲端型後端服務。</p>
<p>如果想要看影片，右邊片段播放的步驟與本教學課程相同。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-ios-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a> <span class="time">09:38:00</span></div>
</div>

在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單「_待辦事項清單_」應用程式。您將建立的行動服務會使用 JavaScript 建立伺服器端商務邏輯。若要建立可讓您在 Visual Studio 以支援的 .NET 語言撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [.NET 後端版本]。

以下是完成應用程式的螢幕擷取畫面：

![][0]

完成本教學課程需使用 XCode 4.5 及 iOS 5.0 或更新版本。

<div class="dev-callout"><strong>注意</strong> <p>若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得多達 10 個免費的行動服務，即使在試用期結束之後仍可繼續使用這些服務。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Azure 免費試用</a>。</p></div>

## <a name="create-new-service"> </a>建立新的行動服務

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>建立新的 iOS 應用程式</h2>

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。

在本節中，您將建立連線至您行動服務的新 iOS 應用程式。

1.  在管理入口網站中，按一下 [**行動服務**]，然後按一下您剛剛建立的行動服務。

2. 在 [快速入門] 索引標籤中，按一下 [**選擇平台**] 下的 [**iOS**]，然後展開 [**Create a new iOS app**]。

   	![][6]

   	這會顯示三個簡單的步驟來建立與您行動服務連線的 iOS 應用程式。

  	![][7]

3. 下載並安裝 [Xcode] v4.4 或更新版本 (若您尚未這麼做)。

4. 按一下 [**建立 TodoItem 資料表**]，以建立儲存應用程式資料的資料表。

5. 在 [**下載及執行您的應用程式**] 下，按一下 [**下載**]。

  	如此會下載與您行動服務連線之範例應用程式「_待辦事項清單_」的專案，以及行動服務 iOS SDK。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

## 執行新的 iOS 應用程式

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

<ol start="4">
<li><p>回到管理入口網站，按一下 [<strong>資料</strong>] 索引標籤，然後按一下 [<strong>TodoItems</strong>] 資料表。<p></li></ol>

![](./media/mobile-services-ios-get-started/mobile-data-tab.png)

如此可讓您瀏覽由應用程式插入資料表中的資料。</p>

![](./media/mobile-services-ios-get-started/mobile-data-browse.png)


## <a name="next-steps"> </a>後續步驟
請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

* [開始使用資料]
  <br/>進一步了解使用行動服務來儲存和查詢資料。

* [開始使用離線資料同步]
  <br/>了解如何使用離線資料同步處理，建立可快速回應和功能強大的應用程式。

* [開始使用驗證]
  <br/>了解如何透過身分識別提供者來驗證您的應用程式使用者。

* [開始使用推播通知]
  <br/>了解如何將非常基本的推播通知傳送至您的應用程式。


<!-- Anchors. -->
[開始使用行動服務]:#getting-started
[建立新的行動服務]:#create-new-service
[定義行動服務執行個體]:#define-mobile-service-instance
[後續步驟]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png

[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-ios-get-started-data/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-ios-get-started-users/
[開始使用離線資料同步]: /zh-tw/documentation/articles/mobile-services-ios-get-started-offline-data
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/

[行動服務 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[管理入口網站]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET 後端版本]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started
