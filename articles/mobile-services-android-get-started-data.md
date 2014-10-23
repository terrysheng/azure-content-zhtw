<properties linkid="develop-mobile-tutorials-get-started-with-data-android" urlDisplayName="Get Started with Data - Android" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="Azure android data, Azure mobile services data, windows droid, windows android, microsoft droid, microsoft android" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal,glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/29/2014" ms.author="ricksal,glenga"></tags>

# 開始使用行動服務中的資料

[WACOM.INCLUDE [mobile-services-selector-get-started-data][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>本主題說明如何在 Android 應用程式中使用 Azure 行動服務來處理資料。在本教學課程中，您將下載應用程式，並在記憶體中儲存資料、建立新的行動服務、將行動服務與該應用程式整合，然後登入 Azure 管理入口網站查看執行應用程式時所做的資料變更。</p>

</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a><span class="time">下午 03:32:00</span></div>

</div>

<div class="dev-callout"><b>注意</b>
<p>本教學課程是為了協助您充分了解行動服務如何讓您從 Android 應用程式中利用 Azure 來儲存和擷取資料。因此，本主題將逐步說明已在行動服務快速入門中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮首先完成教學課程<a href="/en-us/develop/mobile/tutorials/get-started-android">開始使用行動服務</a>。</p>
</div>

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 Android 應用程式專案][]
2.  [建立行動服務][]
3.  [新增用於儲存的資料表][]
4.  [更新應用程式以使用行動服務][]
5.  [針對行動服務進行應用程式測試][]

<div class="dev-callout"><strong>注意</strong> <p>若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Azure 免費試用</a>。</p></div>

本教學課程需要 [Mobile Services Android SDK][]、[Android SDK][] (含 Eclipse 整合式開發環境 (IDE) 和 Android Developer Tools (ADT) 外掛程式)，以及 Android 4.2 或更新版本。

<div class="dev-callout"><b>注意</b>
<p>本教學課程提供提示來安裝 Android SDK 和 Mobile Services Android SDK。下載的 GetStartedWithData 專案需要 Android 4.2 或更新版本。不過，Mobile Services SDK 只需要 Android 2.2 或更新版本。</p>
</div>

## <a name="download-app"></a><span class="short-header">下載專案</span>下載 GetStartedWithData 專案

### 取得範例程式碼

[WACOM.INCLUDE [download-android-sample-code][]]

### 驗證 Android SDK 版本

[WACOM.INCLUDE [Verify SDK][]]

### 檢查和執行範例程式碼

[WACOM.INCLUDE [mobile-services-android-run-sample-code][]]

## <a name="create-service"></a><span class="short-header">建立行動服務</span>在管理入口網站中建立新的行動服務

[WACOM.INCLUDE [mobile-services-create-new-service-data][]]

## <a name="add-table"></a><span class="short-header">新增資料表</span>將資料表新增至行動服務

[WACOM.INCLUDE [mobile-services-create-new-service-data-2][]]

## <a name="update-app"></a><span class="short-header">更新應用程式</span>更新應用程式以使用行動服務進行資料存取

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data][]]

## <a name="test-app"></a><span class="short-header">測試應用程式</span>在您的新行動服務中測試應用程式

現在，應用程式已更新為使用行動服務做為後端儲存，您可以使用 Android 模擬器或 Android 手機，在行動服務上測試應用程式。

1.  從 [執行] 功能表，按一下 [執行] 來啟動專案。

    這樣會執行您的應用程式 (以 Android SDK 建立)，該應用程式利用用戶端程式庫傳送查詢，然後從您的行動服務傳回項目。

2.  同樣地，輸入有意義的文字，然後按一下 [加入]。

    這會傳送新項目以插入至行動服務。

3.  在[管理入口網站][]中按一下 [行動服務]，然後按一下您的行動服務。

4.  按一下 [資料] 索引標籤，然後按一下 [瀏覽]。

    ![][]

    請注意，**TodoItem** 資料表現在包含資料和行動服務產生的一些值，且資料行已自動加入至資料表，以符合應用程式中的 TodoItem 類別。

Android 的**開始使用資料**教學課程到此結束。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓 Android 應用程式在行動服務中使用資料的基礎。

接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

-   [使用指令檔驗證與修改資料][]
    深入了解在行動服務中使用伺服器指令檔，來驗證並變更從應用程式傳送出來的資料。

-   [使用分頁縮小查詢範圍][]
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料系列後，可試著閱讀這些另外的 Android 教學課程：

-   [開始使用驗證][]
    了解如何驗證應用程式的使用者。

-   [開始使用推送通知][]
    了解如何使用行動服務將非常基本的推送通知傳送到應用程式。

<!-- Anchors. --> 

<!-- Images. --> 

<!-- URLs. -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [觀看教學課程]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services
  [開始使用行動服務]: /en-us/develop/mobile/tutorials/get-started-android
  [下載 Android 應用程式專案]: #download-app
  [建立行動服務]: #create-service
  [新增用於儲存的資料表]: #add-table
  [更新應用程式以使用行動服務]: #update-app
  [針對行動服務進行應用程式測試]: #test-app
  [Azure 免費試用]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AED8DE357
  [Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  [Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [download-android-sample-code]: ../includes/download-android-sample-code.md
  [Verify SDK]: ../includes/mobile-services-verify-android-sdk-version.md
  [mobile-services-android-run-sample-code]: ../includes/mobile-services-android-run-sample-code.md
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [mobile-services-android-getting-started-with-data]: ../includes/mobile-services-android-getting-started-with-data.md
  [管理入口網站]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
  [使用指令檔驗證與修改資料]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [使用分頁縮小查詢範圍]: /en-us/develop/mobile/tutorials/add-paging-to-data-android
  [開始使用驗證]: /en-us/develop/mobile/tutorials/get-started-with-users-android
  [開始使用推送通知]: /en-us/develop/mobile/tutorials/get-started-with-push-android
