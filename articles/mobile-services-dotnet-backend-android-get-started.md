<properties pageTitle="Get Started with Azure Mobile Services for Android apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>開始使用行動服務

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

本教學課程顯示如何使用 Azure 行動服務，將雲端型後端服務新增到 Android 應用程式。在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單*待辦事項*應用程式。您所將建立的行動服務，會使用 Visual Studio 與支援的 .NET 語言撰寫伺服器端商務邏輯，並管理行動服務。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [JavaScript 後端版本][JavaScript 後端版本]。

以下是完成應用程式的螢幕擷取畫面：

![][0]

完成本教學課程需要 [Android Developer Tools][Android Developer Tools]，其中包含 Eclipse 整合式開發環境 (IDE)、Android Developer Tools (ADT) 外掛程式，以及最新版 Android 平台。需要 Android 4.2 或以上的版本。

下載的快速入門專案包含 Mobile Services SDK for Android。雖然這個專案需要 Android 4.2 或以上的版本，不過行動服務 SDK 只需要 Android 2.2 或以上的版本。

<div class="dev-callout"><strong>注意</strong> <p>若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得多達 10 個免費的行動服務，即使在試用期結束之後仍可繼續使用這些服務。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 免費試用</a>。</p></div>

## <a name="create-new-service"> </a>建立新的行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 將行動服務下載至您的本機電腦

您已建立行動服務，接著請下載可在您的本機電腦或虛擬機器上執行的個人化行動服務專案。

1.  按一下您剛剛建立的行動服務，然後在 [快速入門] 索引標籤中，按一下 [選擇平台] 下的 [Android]，並展開 [Create a new Android app]。

    ![][1]

2.  下載並安裝 [Visual Studio Professional 2013][Visual Studio Professional 2013] 或更新版本 (若您尚未這麼做)。

3.  在 [Download and publish your service to the cloud] 下，按一下 [下載]。

    這會下載實作行動服務的 Visual Studio 專案。請將壓縮的專案檔案儲存至本機電腦，並記下儲存位置。

4.  此外，請下載您的發佈設定檔、將下載的檔案儲存至本機電腦，並記下儲存位置。

## 測試行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## 發佈行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## 建立新的 Android 應用程式

在本節中，您將建立與行動服務連線的新 Android 應用程式。

1.  在[管理入口網站][管理入口網站]中，按一下 [行動服務]，然後按一下您剛剛建立的行動服務。

2.  在快速入門索引標籤中，按一下 [Choose platform] 下的 [Android]，並展開 [Create a new Android app]。

    ![][2]

3.  如果您尚未這麼做，請在您的本機電腦或虛擬機器下載並安裝 [Android Developer Tools][Android Developer Tools]。

4.  在 [Download and run your app] 下，按 [下載]。

    這將會下載與行動服務連接的範例*待辦事項*應用程式專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

## 執行您的 Android 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

1.  瀏覽至您儲存此壓縮專案檔案的位置，並在您的電腦上展開檔案。

2.  在 Eclipse 中，依序按一下 [檔案]、[匯入]、展開 [Android]，按一下 [匯入現有的 Android 程式碼至工作區]，然後按 [下一步]。

    ![][3]

3.  按一下 [瀏覽] 瀏覽到展開的專案檔案位置，並且按一下 [確定] 確定已勾選 TodoActivity 專案，然後按一下 [完成]。

    ![][4]

    </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">這會將專案檔案匯入目前的工作區中。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

    ![][5]

4.  從 [執行] 功能表中，按一下 [執行] 在 Android 模擬器中啟動專案。

    <div class="dev-callout"><strong>注意</strong> <p>若要能夠在 Android 模擬器中執行此專案，您必須至少定義一個 Android 虛擬裝置 (AVD)。使用 AVD Manager 來建立與管理這些裝置。</p></div>

5.  在應用程式中輸入有意義的文字 (例如 *Complete the tutorial*)，然後按一下 [新增]。

    ![][6]

    如此會傳送 POST 要求到 Azure 中代管的新行動服務。要求中的資料會插入 TodoItem 資料表中。</cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

    <div class="dev-callout"><strong>注意</strong> 
<p>您可以檢閱存取行動服務來查詢和插入資料的程式碼，這可以在 ToDoActivity.java 檔案中找到。</p>
</div>

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](/zh-tw/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).-->

## <a name="next-steps"> </a>後續步驟

請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

-   [開始使用資料][開始使用資料]
    深入了解使用行動服務來儲存與查詢資料。

-   [開始使用驗證][開始使用驗證]
    了解如何向身分識別提供者驗證應用程式的使用者。

-   [開始使用推播通知][開始使用推播通知]
    了解如何將極為基本的推播通知傳送到應用程式。

-   [行動服務 .NET 後端的疑難排解][行動服務 .NET 後端的疑難排解] (英文)
     了解如何診斷及修正使用行動服務 .NET 後端時可能發生的問題。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [JavaScript 後端版本]: /zh-tw/documentation/articles/mobile-services-android-get-started/
  [0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
  [Android Developer Tools]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs.png
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [管理入口網站]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
  [3]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
  [4]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png
  [5]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png
  [6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
  [開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
  [行動服務 .NET 後端的疑難排解]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
