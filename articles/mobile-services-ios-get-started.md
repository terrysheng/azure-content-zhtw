<properties pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="hero-article" ms.date="01/01/1900" ms.author="krisragh" />

# <a name="getting-started"> </a>開始使用行動服務

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>本教學課程說明如何使用 Azure 行動服務在 iOS 應用程式中新增雲端型後端服務。</p>
<p>如果想要看影片，右邊片段播放的步驟與本教學課程相同。</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-ios-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a> <span class="time">上午 09:38:00</span></div>

</div>

在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單*待辦事項*應用程式。您將建立的行動服務會使用 JavaScript 建立伺服器端商務邏輯。若要建立可讓您在 Visual Studio 以支援的 .NET 語言撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [.NET 後端版本][.NET 後端版本]。

以下是完成應用程式的螢幕擷取畫面：

![][0]

完成本教學課程需使用 XCode 4.5 及 iOS 5.0 或更新版本。

<div class="dev-callout"><strong>注意</strong> <p>若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，您可以註冊 Azure 試用版並取得高達 10 項的免費行動服務。此外，在試用期間結束後您仍可繼續使用這些服務。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Azure 免費試用</a>。</p></div>

## <a name="create-new-service"> </a>建立新的行動服務

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <span class="short-header">建立新的應用程式</span>建立新的 iOS 應用程式

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。

在本節中，您將建立連線至您行動服務的新 iOS 應用程式。

1.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2.  在 [快速入門] 索引標籤中，按一下 [選擇平台] 下的 [iOS]，然後展開 [Create a new iOS app]。

    ![][1]

    這會顯示三個簡單的步驟來建立與您行動服務連線的 iOS 應用程式。

    ![][2]

3.  下載並安裝 [Xcode][Xcode] v4.4 或更新版本 (若您尚未這麼做)。

4.  按一下 [Create TodoItems table] 以建立儲存應用程式資料的資料表。

5.  在 [Download and run your app] 下，按 [下載]。

    如此會下載與您行動服務連線之範例應用程式 *To do list* 的專案，以及行動服務 iOS SDK。請將壓縮的專案檔案儲存至本機電腦，並記下儲存位置。

## 執行新的 iOS 應用程式

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

1.  

    回到管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItems] 資料表。

    <p>

</p>
![][3]

如此可讓您瀏覽由應用程式插入資料表中的資料。

</p>
![][4]

## <a name="next-steps"> </a>後續步驟

請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

-   [開始使用資料][開始使用資料]
    深入了解使用行動服務來儲存與查詢資料。

-   [開始使用離線資料同步][開始使用離線資料同步]
    了解如何使用離線資料同步，讓您的應用程式更穩健及具備回應力。

-   [開始使用驗證][開始使用驗證]
    了解如何向身分識別提供者驗證應用程式的使用者。

-   [開始使用推播通知][開始使用推播通知]
    了解如何將極為基本的推播通知傳送到應用程式。

 
 


  [.NET 後端版本]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started
  [0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
  [1]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
  [2]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [3]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
  [4]: ./media/mobile-services-ios-get-started/mobile-data-browse.png
  [開始使用資料]: /zh-tw/documentation/articles/mobile-services-ios-get-started-data/
  [開始使用離線資料同步]: /zh-tw/documentation/articles/mobile-services-ios-get-started-offline-data
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-ios-get-started-users/
  [開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
