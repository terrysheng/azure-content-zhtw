<properties  pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

# <a name="getting-started"> </a>開始使用行動服務

 
<div  class="dev-center-tutorial-selector sublanding">
	<a href="/zh-tw/documentation/articles/mobile-services-windows-store-get-started" title="Windows 市集">Windows 市集</a> <a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a> <a href="/zh-tw/documentation/articles/mobile-services-ios-get-started" title="iOS" class="current">iOS</a> <a href="/zh-tw/documentation/articles/mobile-services-android-get-started" title="Android">Android</a> <a href="/zh-tw/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a> <a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a> <a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a> <a href="/zh-tw/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a> <a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

 
<div  class="dev-center-tutorial-subselector">
	<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started/" title=".NET 後端">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-ios-get-started/" title="JavaScript 後端" class="current">JavaScript 後端</a>
</div>

 
<div  class="dev-onpage-video-clear clearfix">
<div  class="dev-onpage-left-content">
<p>本教學課程說明如何使用 Azure 行動服務在 iOS 應用程式中新增雲端型後端服務。</p>
<p>若您偏好觀看影片，右方的短片會示範與此教學課程相同的步驟。</p>
</div>

 
<div  class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-ios-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="dev-onpage-video"><span  class="icon">播放影片</span></a> <span  class="time">9:38</span></div>

 在本教學課程中，您將建立新的行動服務和簡單的*待辦事項清單*應用程式，後者會在前者儲存應用程式資料。您將建立的行動服務會使用
JavaScript 建立伺服器端商務邏輯。若要建立可讓您在 Visual Studio 以支援的 .NET
語言撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [.NET 後端版本]。

以下是完成之應用程式的螢幕擷取畫面：

![][0]

完成本教學課程需使用 XCode 4.5 及 iOS 5.0 或更新版本。
<div class="dev-callout"><strong>注意</strong> <p>若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Azure 免費試用</a>。</p></div>

## <a name="create-new-service"> </a>建立新的行動服務

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2><span  class="short-header">建立新的應用程式</span>建立新的 iOS 應用程式</h2>


建立行動服務後，您可以遵循管理入口網站中的簡易快速入門來建立新的應用程式，或修改現有的應用程式以連線到行動服務。

在本節中，您將建立連線至您行動服務的新 iOS 應用程式。

1.  在管理入口網站中，按一下 **行動服務**，然後按一下您剛剛建立的行動服務。

2.  在 **快速入門] 索引標籤中，按一下 [選擇平台** 下的 **iOS**，然後展開 **Create a new iOS app**。
    
	![][6]
        
	這會顯示三個簡單的步驟來建立與您行動服務連線的 iOS 應用程式。

	![][7] 

1.  下載並安裝 [Xcode] v4.4 或更新版本 (若您尚未這麼做)。

2.  按一下 **Create TodoItems table** 建立資料表來儲存應用程式資料。

3.  在 **Download and run your app** 下，按一下 **下載**。

 如此會下載與您行動服務連線之範例應用程式 *To do list* 的專案，以及行動服務 iOS SDK。請將壓縮的專案檔案儲存至本機電腦，並記下儲存位置。 

## 執行新的 iOS 應用程式

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

1.	回到管理入口網站，按一下 **資料** 索引標籤，然後按一下 **TodoItems** 資料表。

	![](./media/mobile-services-ios-get-started/mobile-data-tab.png)
    
    
     這可以讓您瀏覽應用程式插入資料表中的資料。
    
    
     ![](./media/mobile-services-ios-get-started/mobile-data-browse.png)

## <a name="next-steps"> </a>後續步驟

您已完成快速入門，現在可以了解如何在行動服務中執行其他重要工作：

* [開始使用資料]   
深入了解如何使用行動服務來儲存及查詢資料。 

* [開始使用驗證]   
了解如何以身分識別提供者驗證您的應用程式使用者。 

* [開始使用推播通知]   
了解如何傳送最基本的推播通知到您的應用程式。 


<!-- URLs. -->
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-ios

[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET backend version]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started

<!-- Images. -->
[0]:./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]:
./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]:./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]:./media/mobile-services-ios-get-started/mobile-xcode-project.png
[10]:
./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]:./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]:./media/mobile-services-ios-get-started/mobile-data-browse.png

<!-- URLs. -->

[開始使用資料]:/en-us/develop/mobile/tutorials/get-started-with-data-ios
[開始使用驗證]:/en-us/develop/mobile/tutorials/get-started-with-users-ios
[開始使用推播通知]:/en-us/develop/mobile/tutorials/get-started-with-push-ios
[行動服務 iOS SDK]:https://go.microsoft.com/fwLink/p/?LinkID=266533
[管理入口網站]:https://manage.windowsazure.com/
[XCode]:https://go.microsoft.com/fwLink/p/?LinkID=266532 
[.NET後端版本]:/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started

<!-- URLs. -->
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-ios

[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET backend version]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started

