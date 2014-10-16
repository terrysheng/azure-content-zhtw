<properties  pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

# <a name="getting-started"> </a>開始使用行動服務

 
<div  class="dev-center-tutorial-selector sublanding">
	<a href="/zh-tw/documentation/articles/mobile-services-windows-store-get-started" title="Windows 市集" class="current">Windows 市集</a> <a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a> <a href="/zh-tw/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a> <a href="/zh-tw/documentation/articles/mobile-services-android-get-started" title="Android">Android</a> <a href="/zh-tw/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a> <a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a> <a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a> <a href="/zh-tw/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a> <a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

 
<div  class="dev-center-tutorial-subselector">
	<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/" title=".NET 後端">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-windows-store-get-started/" title="JavaScript 後端" class="current">JavaScript 後端</a>
</div>

 
<div  class="dev-onpage-video-clear clearfix">
<div  class="dev-onpage-left-content">
<p>本教學課程顯示如何使用 Azure 行動服務，將雲端型後端服務新增到 Windows 應用程式。</p>
<p>若您偏好觀看影片，右方的短片會示範與此教學課程相同的步驟。在此影片中，Scott Guthrie 會提供行動服務的簡介，逐步引導您建立第一個行動服務，並透過 Windows 市集應用程式與該服務連接。</p>
</div>

 
<div  class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span  class="icon">播放影片</span></a> <span  class="time">10:08</span></div>

 在本教學課程中，您將建立新的行動服務和簡單的*待辦事項清單*應用程式，後者會在前者儲存應用程式資料。您將建立的行動服務會使用
JavaScript 建立伺服器端商務邏輯。若要建立可讓您使用 Visual Studio 並以支援的 .NET
語言來撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 .NET 後端版本。

以下是完成之應用程式的螢幕擷取畫面：

![](./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png)

完成本教學課程是 Windows 市集應用程式所有其他行動服務教學課程的先決條件。

> [WACOM.NOTE] 若要完成此教學課程，您需要 Azure
> 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][1]。

> 本教學課程需要 Visual Studio 2013。若要使用 Visual Studio 2012 連接 Windows
> 市集應用程式，請依照主題[使用 Visual Studio 2012
> 開始使用行動服務中的資料](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/)中的步驟進行。

## 建立新的行動服務

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 建立新的 Windows 市集應用程式

建立行動服務後，您可以遵循管理入口網站中的簡易快速入門來建立新的應用程式，或修改現有的應用程式以連線到行動服務。

在本節中，您將建立與行動服務連線的新 Windows 市集應用程式。

1.  在管理入口網站中，按一下 **行動服務**，然後按一下您剛剛建立的行動服務。

2.  在快速入門索引標籤中，按一下 **Choose platform** 下的 **Windows**，並展開 **Create a new Windows Store app**。
    
	![][6]
        
	此處顯示建立與行動服務連線的 Windows 市集應用程式的 3 個簡單步驟。

	![](./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png) 

1.  如果您尚未這麼做，請在您的本機電腦或虛擬機器下載並安裝 [Visual Studio 2013 Express for Windows][2]。

2.  按一下 **Create TodoItem table** 來建立儲存應用程式資料的資料表。

3.  在 **Download and run your app** 中，選取您的應用程式語言，然後按一下 **下載**。

 這將下載與行動服務連線的範例*待辦事項清單*應用程式專案。將壓縮的專案檔案儲存到本機電腦，並記下您儲存的位置。 

## 執行您的 Windows 應用程式

本教學課程的最後步驟，是建置並執行您的新應用程式。

1.  瀏覽到您儲存壓縮專案檔案的位置，在您的電腦上將檔案展開，並在 Visual Studio 2013 Express for Windows 中開啟解決方案檔案。

2.  按 **F5** 鍵重新建置專案並啟動應用程式。

3.  在應用程式的 **Insert a TodoItem** 中輸入有意義的文字，例如*完成教學課程*，然後按一下 **儲存**。
    
	![][10]
        
	這會將 POST 要求傳送到 Azure 中代管的新行動服務。來自要求的資料將會插入到TodoItem 資料表中。行動服務會傳回資料表中儲存的項目，並且在應用程式的第二個資料欄中顯示資料。


   
    > [WACOM.NOTE]您可以檢閱存取行動服務來查詢和插入資料的程式碼，這可以在 MainPage.xaml.cs 檔案
    > (C#/XAML 專案) 或 default.js (JavaScript/HTML 專案) 檔案中找到。

4.  回到管理入口網站，按一下 **資料** 索引標籤，然後按一下 **TodoItems** 資料表。
    
	![][11]
        
	這可以讓您瀏覽應用程式插入資料表中的資料。
        
	![][12]

## 後續步驟

完成快速入門後，請了解如何在行動服務中執行其他重要工作：

* **開始使用資料**
  ([C#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet)/[JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js))
  
  	深入了解使用行動服務儲存和查詢資料。

* **開始使用驗證**
  ([C#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)/[JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js))
  
  	了解如何向身分識別提供者驗證應用程式的使用者。

* **開始使用推播通知**
  ([C#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet)/[JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js))
  
  	了解如何將極為基本的推播通知傳送到應用程式。

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-windows-store-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-windows-store-get-started/mobile-vs2013-project.png

[10]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-windows-store-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-windows-store-get-started/mobile-data-browse.png

<!-- URLs. -->



[1]: http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
[2]: http://go.microsoft.com/fwlink/?LinkId=257546