<properties 
	pageTitle="開始為 Android 應用程式使用 Azure 行動服務" 
	description="遵循此教學課程，可開始使用 Azure 行動服務進行 Android 開發。" 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="hero-article" 
	ms.date="02/13/2015" 
	ms.author="ricksal,glenga"/>

# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started-EC.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>本教學課程顯示如何使用 Azure 行動服務，將雲端型後端服務新增到 Android 應用程式。在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單<em>待辦事項</em>應用程式。</p>
<p>以下是完成應用程式的螢幕擷取畫面：</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a><span class="time">上午 07:26</span></div>
</div>

![][0]

完成本教學課程需要 [Android Developer Tools][Android SDK]，其中包含 Eclipse 整合式開發環境 (IDE)、Android Developer Tools (ADT) 外掛程式，以及最新版 Android 平台。需要 Android 4.2 或以上的版本。 

下載的快速入門專案包含 Mobile Services SDK for Android。雖然這個專案需要 Android 4.2 或以上的版本，不過行動服務 SDK 只需要 Android 2.2 或以上的版本。

> [AZURE.IMPORTANT] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank)。

<!-- -->

>[AZURE.NOTE] 如果您想查看已完成之應用程式的原始程式碼，請移至<a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStarted/Android" target="_blank">這裡</a>。

## <a name="create-new-service"> </a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2>建立新的 Android 應用程式</h2>

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。 

在本節中，您將建立與行動服務連線的新 Android 應用程式。

1.  在管理入口網站中，按一下 [行動服務]****，然後按一下您剛剛建立的行動服務。

2. 在 [快速入門] 索引標籤中，按一下 [選擇平台]**** 下的 [Android]****，並展開 [建立新的 Android 應用程式]****。

   	![][6]

   	此處顯示建立與行動服務連線的 Android 應用程式的 3 個簡單步驟。

  	![][7]

3. 如果您尚未這麼做，請在您的本機電腦或虛擬機器下載並安裝 [Android Developer Tools][Android SDK]。

4. 按一下 [**建立 TodoItem 資料表**] 以建立可儲存應用程式資料的資料表。


5. 立即下載您的應用程式：
	- 最新的應用程式版本使用行動服務 Android SDK 2.0。您可以從<a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">這裡</a>下載該版本。按一下 [**下載 Zip**]，將它解壓縮，專案會出現在 Android 資料夾下的 GettingStarted 中。
	 
	- 較早的版本會使用先前版本的 SDK。若要使用它，請在 [**下載及執行您的應用程式**] 下方，按一下 [**下載**]。這將會下載與行動服務連接的範例「_待辦事項清單_」應用程式專案。專案檔案已壓縮，請瀏覽到其位置，並將檔案解壓縮到您的電腦上。


## 執行您的 Android 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

2. 在 Eclipse 中，依序按一下 [**檔案**]、[**匯入**]、展開 [**Android**]，按一下 [**匯入現有的 Android 程式碼至工作區**]，然後按 [**下一步**]。 

 	![][14]

3. 按一下 [瀏覽]**** 瀏覽到展開的專案檔案位置，並且按一下 [確定]**** 確定已勾選 TodoActivity 專案，然後按一下 [完成]****。 

 	![][15]

	這會將專案檔案匯入目前的工作區中。

   	![][8]

4. 如果下載的 SDK 是 2.0 版，則需要以您行動服務的 Url 和索引鍵來更新程式碼：
	- 	在 **TodoActivity.java** 中尋找 **OnCreate** 方法，並找出可將行動服務用戶端具現化的程式碼。在前一個影像中可看到此程式碼。
	- 	以您行動服務的實際 Url 取代 "MobileServiceUrl"。
	- 	以您行動服務的金鑰取代 "AppKey"。
	- 	如需詳細資訊，請參閱教學課程<a href="http://azure.microsoft.com/documentation/articles/mobile-services-android-get-started-data/">將行動服務新增至現有的應用程式</a>。 



4. 從 [**執行**] 功能表中，按一下 [**執行**] 以在 Android 模擬器中啟動專案。

	> [AZURE.IMPORTANT] 若要能夠在 Android 模擬器中執行此專案，您必須至少定義一個 Android 虛擬裝置 (AVD)。使用 AVD Manager 來建立與管理這些裝置。

5. 在應用程式中輸入有意義的文字 (例如「完成教學課程」)，然後按一下 [**新增**]。

   	![][10]

   	如此會傳送 POST 要求到 Azure 中代管的新行動服務。要求中的資料會插入 TodoItem 資料表中。行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。

	> [AZURE.NOTE] 您可以檢閱存取行動服務來查詢和插入資料的程式碼，這可以在 ToDoActivity.java 檔案中找到。

6. 回到管理入口網站，按一下 [**資料**] 索引標籤，然後按一下 [**TodoItems**] 資料表。

   	![][11]

   	如此可讓您瀏覽由應用程式插入資料表中的資料。

   	![][12]

## <a name="next-steps"> </a>後續步驟
請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作： 

* [開始使用資料]
  <br/>深入了解如何使用行動服務儲存和查詢資料。

* [開始使用驗證]
  <br/>了解如何向身分識別提供者驗證應用程式的使用者。

* [開始使用推播通知] 
  <br/>了解如何將極為基本的推播通知傳送到應用程式。

<!-- Anchors. -->
[開始使用行動服務]:#getting-started
[建立新的行動服務]:#create-new-service
[定義行動服務執行個體]:#define-mobile-service-instance
[後續步驟]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png





[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android-EC.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[開始使用資料]: /documentation/articles/mobile-services-android-get-started-data/
[開始使用驗證]: /documentation/articles/mobile-services-android-get-started-users/
[開始使用推播通知]: /documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[行動服務 Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[管理入口網站]: https://manage.windowsazure.com/

<!--HONumber=47-->
