<properties linkid="mobile-services-dotnet-backend-xamarin-ios-get-started" urlDisplayName="Get Started with Mobile Services for Xamarin iOS apps" pageTitle="Get Started with Mobile Services for Xamarin iOS apps - Azure Mobile Services" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin iOS development" metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with Mobile Services for Xamarin iOS apps" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# <a name="getting-started"> </a>開始使用行動服務

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

本教學課程顯示如何使用 Azure 行動服務，將雲端型後端服務新增到 Xamarin iOS 應用程式。在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單*待辦事項*應用程式。您所將建立的行動服務，會使用 Visual Studio 與支援的 .NET 語言撰寫伺服器端商務邏輯，並管理行動服務。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [JavaScript 後端版本][JavaScript 後端版本]。

> [WACOM.NOTE]本主題將說明如何使用 Azure 管理入口網站建立新的行動服務專案。使用 Visual Studio 2013 Update 2，可讓您將新的行動服務專案新增至現有的 Visual Studio 方案。如需詳細資訊，請參閱 Windows 開發人員中心的[快速入門：新增行動服務 (.NET 後端)][快速入門：新增行動服務 (.NET 後端)]

以下是完成應用程式的螢幕擷取畫面：

![][]

完成本教學課程是 Xamarin iOS 應用程式所有其他行動服務教學課程的先決條件。

> [WACOM.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][Azure 免費試用]。
> 本教學課程需要 [Visual Studio Professional 2013][Visual Studio Professional 2013]。您可以使用免費試用版。

## 建立新的行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 建立新的 Xamarin iOS 應用程式

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。

在本節中，您將為行動服務下載新的 Xamarin iOS 應用程式和服務專案。

1.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2.  在快速入門索引標籤中，按一下 [Choose platform] 下的 [Xamarin]，並展開 [Create a new Xamarin app]。

    ![][1]

    這將顯示三個簡單步驟，可用來建立連接到您行動服務的 Xamarin iOS 應用程式。

    ![][2]

3.  在您的本機電腦或虛擬機器下載並安裝 [Visual Studio Professional 2013][Visual Studio Professional 2013] (如果您尚未這麼做)。

4.  下載並安裝 [Xcode][Xcode] v4.4 或更新版本和 [Xamarin Studio][Xamarin Studio]。您也可以使用 Xamarin for Visual Studio。

5.  在 [Download and publish your service to the cloud] 下選取 [iOS]，然後按一下 [下載]。

    這會下載一個方案，其中包含行動服務的專案，以及與行動服務連接的範例*待辦事項*應用程式的專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

6.  下載您的發行設定檔、將下載的檔案儲存至本機電腦，並記下儲存位置。

## 測試行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## 發佈行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## 執行 Xamarin iOS 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

1.  在 Visual Studio 或 Xamarin Studio 中，導覽至行動服務方案內的用戶端專案。

    ![][3]

    ![][4]

2.  按 [執行] 按鈕以建置用戶端專案，並在 iPhone 模擬器中啟動應用程式。

3.  在應用程式中輸入有意義的文字 (例如 *Complete the tutorial*)，然後按一下加號 (**+**) 圖示。

    ![][5]

    如此會傳送 POST 要求到 Azure 中代管的新行動服務。</cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">要求中的資料會插入</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000"> TodoItem </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">資料表中。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000"></cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

> [WACOM.NOTE]您可以在 QSTodoService.cs C# 檔案中檢閱存取行動服務以查詢和插入資料的程式碼。

## 後續步驟

請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

-   [開始使用驗證][開始使用驗證]
    了解如何向身分識別提供者驗證應用程式的使用者。

-   [開始使用推播通知][開始使用推播通知]
    了解如何將極為基本的推播通知傳送到應用程式。

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [JavaScript 後端版本]: /zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-get-started
  [快速入門：新增行動服務 (.NET 後端)]: http://msdn.microsoft.com/zh-tw/library/windows/apps/dn629482.aspx
  []: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
  [Azure 免費試用]: http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
  [2]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
  [Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
  [Xamarin Studio]: http://xamarin.com/download
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [3]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
  [4]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
  [5]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users
  [開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push
