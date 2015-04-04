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
	ms.date="02/03/2015" 
	ms.author="ricksal"/>

# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>本教學課程顯示如何使用 Azure 行動服務，將雲端型後端服務新增到 Android 應用程式。在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單 <em>待辦事項應用程式</em> 。</p>
<p>以下是完成應用程式的螢幕擷取畫面：</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a><span class="time">7:26</span></div>
</div>

![][0]

完成本教學課程需要 [Android Developer Tools][Android Studio]，其中包括 Android Studio 整合式開發環境，以及最新的 Android 平台。需要 Android 4.2 或以上的版本。 

下載的快速入門專案包含 Azure Mobile Services SDK for Android。

> [AZURE.IMPORTANT] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank)。

<!-- -->

> [AZURE.NOTE] 若要查看已完成應用程式的原始程式碼，請移至 <a href="https://github.com/RickSaling/mobile-services-samples/tree/androidStudio/GettingStarted/AndroidStudio" target="_blank">這裡</a>。

<!-- -->

> [AZURE.NOTE] 如果您想要查看本教學課程的 Eclipse 版本，請移至：[取得啟動 (Eclipse)]。


## <a name="create-new-service"> </a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2>建立新的 Android 應用程式</h2>

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。 

在本節中，您將建立與行動服務連線的新 Android 應用程式。

1.  在管理入口網站中，按一下 [**行動服務**]，然後按一下您剛剛建立的行動服務。

2. 在 [快速入門] 索引標籤中，按一下 [**選擇平台**] 下的 [**Android**]，並展開 [**建立新的 Android 應用程式**]。

   	![][6]

   	此處顯示建立與行動服務連線的 Android 應用程式的 3 個簡單步驟。

  	![][7]

3. 如果您尚未這麼做，請在您的本機電腦或虛擬機器下載並安裝 [Android Developer Tools][Android SDK]。

4. 按一下 [**建立 TodoItem 資料表**] 以建立可儲存應用程式資料的資料表。


5. 立即下載您的應用程式：
	- 最新的應用程式版本使用行動服務 Android SDK 2.0。您可以從 <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">這裡</a>。按一下 [**下載 Zip**]，將它解壓縮，專案會出現在 Android 資料夾下的 GettingStarted 中。
	 
	- 較早的版本會使用先前版本的 SDK。若要使用它，請在 [**下載及執行您的應用程式**] 下方，按一下 [**下載**]。這將會下載與行動服務連接的範例「_待辦事項清單_」應用程式專案。專案檔案已壓縮，請瀏覽到其位置，並將檔案解壓縮到您的電腦上。


## 執行您的 Android 應用程式

[WACOM.INCLUDE [mobile-services-run-your-app](../includes/mobile-services-android-get-started.md)]


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
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[開始使用 (Eclipse)]: /documentation/articles/mobile-services-android-get-started-EC/
[開始使用資料]: /documentation/articles/mobile-services-android-get-started-data/
[開始使用驗證]: /documentation/articles/mobile-services-android-get-started-users/
[開始使用推播通知]: /documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[行動服務 Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[管理入口網站]: https://manage.windowsazure.com/

<!--HONumber=47-->
