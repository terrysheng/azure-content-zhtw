<properties pageTitle="Get Started with Mobile Services for Xamarin iOS apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin iOS development." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with Mobile Services" authors="craigd" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="craigd" />

# <a name="getting-started"> </a>開始使用行動服務

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

本教學課程將示範如何使用 Azure 行動服務，以將雲端後端服務新增至 Xamarin.iOS 應用程式。在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單*待辦事項*應用程式。

如果您想要看影片，下方片段播放的步驟與本教學課程相同。

影片：「開始使用 Xamarin 和 Azure 行動服務」，示範者為 Xamarin 推廣開發人員 Craig Dunn (片長：10 分 5 秒)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

以下是完成應用程式的螢幕擷取畫面：

![][0]

完成本教學課程需要 XCode 4.5 和 iOS 5.0 或更新版本，以及適用於 OS X 的 [Xamarin Studio][Xamarin Studio] 或適用於 Visual Studio 的 Xamarin Visual Studio 外掛程式 (在 Windows 上)。

<div class="dev-callout"><strong>注意</strong> <p>若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，您可以註冊 Azure 試用版並取得高達 10 項的免費行動服務。此外，在試用期間結束後您仍可繼續使用這些服務。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F" target="_blank">Azure 免費試用</a>。</p></div>

## <a name="create-new-service"> </a>建立新的行動服務

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <span class="short-header">建立新的應用程式</span>建立新的 Xamarin.iOS 應用程式

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。

在本節中，您將建立可連接到您行動服務的新 Xamarin.iOS 應用程式。

1.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2.  在快速入門索引標籤中，按一下 [Choose platform] 下的 [Xamarin.iOS]，並展開 [Create a new Xamarin.iOS app]。

    ![][1]

    這將顯示三個簡單步驟，可用來建立連接到您行動服務的 Xamarin.iOS 應用程式。

    ![][2]

3.  如果您尚未這樣做，請下載並安裝 [Xcode] v4.4 或更新版本及 [Xamarin Studio][Xamarin Studio]。

4.  按一下 [Create TodoItems table] 以建立儲存應用程式資料的資料表。

5.  按一下 [Download and run app] 下的 [下載]。

    這會下載範例*待辦事項*應用程式的專案，以連接至您的行動服務並參照適用於 Xamarin.iOS 的 Azure 行動服務元件。將壓縮的專案檔案儲存到本機電腦並記下儲存位置。

## <span class="short-header">執行應用程式</span>執行新的 Xamarin.iOS 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

1.  瀏覽至您儲存此壓縮專案檔案的位置、在您的電腦上展開檔案，並使用 Xamarin Studio 或 Visual Studio 來開啟 **XamarinTodoQuickStart.iOS.sln** 解決方案檔案。

    ![][3]

    ![][4]

2.  按 [執行] 按鈕以建立專案，並在 iPhone 模擬器中啟動應用程式 (此專案的預設選項)。

3.  在應用程式中輸入有意義的文字 (例如 *Complete the tutorial*)，然後按一下加號 (**+**) 圖示。

    ![][5]

    如此會傳送 POST 要求到 Azure 中代管的新行動服務。要求中的資料會插入 TodoItem 資料表中。</cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

    <div class="dev-callout"> 
<b>注意</b> 
<p>您可以檢閱存取行動服務以查詢與插入資料的程式碼，您可在 TodoService.cs C# 檔案中找到此程式碼。</p> 
</div>

4.  回到管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItems] 資料表。

    ![][6]

    如此可讓您瀏覽由應用程式插入資料表中的資料。

    ![][7]

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

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
  [Xamarin Studio]: http://xamarin.com/download
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
  [2]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
  [3]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
  [4]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
  [5]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
  [6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
  [7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png
  [開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [開始使用離線資料同步]: /zh-tw/develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
  [開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-xamarin-ios
  [開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-xamarin-ios
