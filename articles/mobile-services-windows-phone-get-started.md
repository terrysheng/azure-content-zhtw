<properties  pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

# <a name="getting-started"> </a>開始使用行動服務

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/mobile-services-windows-store-get-started" title="Windows 市集">Windows 市集</a><a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone" class="current">Windows Phone</a><a href="/zh-tw/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a><a href="/zh-tw/documentation/articles/mobile-services-android-get-started" title="Android">Android</a><a href="/zh-tw/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a><a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a><a href="/zh-tw/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a><a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a></div>

 
<div  class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/" title=".NET backend">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started/" title="JavaScript backend" class="current">JavaScript 後端</a></div>

 
<div  class="dev-onpage-video-clear clearfix">
<div  class="dev-onpage-left-content">
<p>本教學課程說明如何使用 Azure 行動服務在 Windows Phone 8 應用程式中新增雲端型後端服務。在本教學課程中，您將建立新的行動服務和簡單的<em>待辦事項清單</em>應用程式，後者會在前者儲存應用程式資料。</p>
<p>若您偏好觀看影片，右方的短片會示範與此教學課程相同的步驟。在影片中，Nick Harris 將介紹行動服務，並引導您建立自己的第一個行動服務，並從 Windows 市集應用程式與之連線。</p>
</div>

 
<div  class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="dev-onpage-video"><span  class="icon">播放影片</span></a> <span  class="time">13:18</span></div>

 在本教學課程中，您將建立新的行動服務和簡單的*待辦事項清單*應用程式，後者會在前者儲存應用程式資料。您將建立的行動服務會使用
JavaScript 建立伺服器端商務邏輯。若要建立可讓您在 Visual Studio 以支援的 .NET 語言撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [.NET 後端版本](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started)。

以下是完成之應用程式的螢幕擷取畫面：

![](./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png)
<div class="dev-callout"><strong>注意</strong> <p>若要完成此教學課程，您需要已啟用 Azure 行動服務功能的 Azure 帳戶。</p> <ul> <li>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2FFen-us%2Fdocumentation%2Farticles%2Fmobile-services-windows-phone-get-started%2F" target="_blank">Azure 免費試用</a>。</li></ul> </div>

## <a name="create-new-service"> </a>建立新的行動服務

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2><span  class="short-header">建立新的應用程式</span>建立新的 Windows Phone 應用程式</h2>

建立行動服務後，您可以遵循管理入口網站中的簡易快速入門來建立新的應用程式，或修改現有的應用程式以連線到行動服務。

在本節中，您將建立連線至您行動服務的新 Windows Phone 8 應用程式。

1.  在管理入口網站中，按一下 **行動服務**，然後按一下您剛剛建立的行動服務。

2.  在 **快速入門] 索引標籤中，按一下 [選擇平台** 底下的 **Windows Phone 8**，然後展開 **Create a new Windows Phone 8 app**。
    
	![][6]
        
	這會顯示三個簡單的步驟來建立與您行動服務連線的 Windows Phone 應用程式。

	![](./media/mobile-services-windows-phone-get-started/mobile-quickstart-steps-wp8.png) 

1.  在本機電腦下載並安裝 [Visual Studio 2012 Express for Windows Phone][1] (若您尚未這麼做)。

2.  按一下 **Create TodoItem table** 來建立儲存應用程式資料的資料表。

3.  在 **Download and run app** 底下，按 **下載**。

 這將下載與行動服務連線的範例*待辦事項清單*應用程式專案。將壓縮的專案檔案儲存到本機電腦，並記下您儲存的位置。 

## 執行 Windows Phone 應用程式

本教學課程的最後步驟，是建置並執行您的新應用程式。

1.  瀏覽至儲存壓縮專案檔案的位置，在電腦上將檔案解壓縮，然後在 Visual Studio 中開啟方案檔。

2.  按 **F5** 鍵重新建置專案並啟動應用程式。

3.  在應用程式中，輸入有意義的文字，如 *Complete the tutorial*，然後按一下 **儲存**。
    
	![][10]
        
	這會將 POST 要求傳送到 Azure 中代管的新行動服務。來自要求的資料將會插入到TodoItem 資料表中。行動服務會傳回資料表中儲存的項目，並且在清單中顯示資料。


   
    > [WACOM.NOTE]您可以檢閱造成存取您行動服務來進行查詢和插入資料的程式碼，該程式碼位於 MainPage.xaml.cs
    > 檔案中。

4.  回到管理入口網站，按一下 **資料** 索引標籤，然後按一下 **TodoItems** 資料表。
    
	![][11]
        
	這可以讓您瀏覽應用程式插入資料表中的資料。
        
	![][12]

## <a name="next-steps"> </a>後續步驟

完成快速入門後，請了解如何在行動服務中執行其他重要工作：

* [開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-wp8)   
	深入了解使用行動服務來儲存及查詢資料。

* [開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-wp8)  
  
	了解如何以身分識別提供者驗證您的應用程式使用者。

* [開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-wp8) 
  
	了解如何傳送最基本的推播通知到您的應用程式。

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png





[6]: ./media/mobile-services-windows-phone-get-started/mobile-portal-quickstart-wp8.png
[7]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-steps-wp8.png
[8]: ./media/mobile-services-windows-phone-get-started/mobile-vs-project-wp8.png

[10]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-startup-wp8.png
[11]: ./media/mobile-services-windows-phone-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-windows-phone-get-started/mobile-data-browse.png

<!-- URLs. -->



[1]: https://go.microsoft.com/fwLink/p/?LinkID=268374