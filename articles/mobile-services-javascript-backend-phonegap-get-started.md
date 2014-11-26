<properties pageTitle="Get started with Mobile Services with PhoneGap | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for PhoneGap development for iOS, Android, and Windows Phone." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-phonegap" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

# <a name="getting-started"> </a>開始使用行動服務

本教學課程說明如何使用 Azure 行動服務，將雲端型後端服務新增至應用程式。在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單*待辦事項*應用程式。

以下是完成應用程式的螢幕擷取畫面：

![][0]

### <a name="additional-requirements"></a>其他需求

要完成本教學課程，必須使用 PhoneGap 工具 (Windows Phone 8 專案需要 3.2 以上的版本)。

PhoneGap 支援多個平台的開發。除了 PhoneGap 工具本身以外，您還必須為所要使用的各個平台安裝下列工具：

-   Windows Phone：安裝 [Visual Studio 2012 Express for Windows Phone][Visual Studio 2012 Express for Windows Phone]
-   iOS：安裝 [Xcode][Xcode] (必須是 4.4 以上的版本)
-   Android：安裝 [Android Developer Tools][Android Developer Tools]
    <br/>(Mobile Services SDK for Android 支援適用於 Android 2.2 或更新版本的應用程式。若要執行快速入門應用程式，則必須使用 Android 4.2 或更新版本。)

## <a name="create-new-service"> </a>建立新的行動服務

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <span class="short-header">建立新的應用程式</span>建立新的 PhoneGap 應用程式

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。

在本節中，您將建立連接到行動服務的新 PhoneGap 應用程式。

1.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2.  在 [快速入門] 索引標籤中，按一下 [選擇平台] 下的 [PhoneGap]，然後展開 [Create a new PhoneGap app]。

    ![][1]

    </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">這將顯示三個簡單步驟，可用來建立連接到您行動服務的</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000"> PhoneGap </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">應用程式。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

    ![][2]

3.  如果您尚未下載並安裝 PhoneGap 和至少其中一項平台開發工具 (Windows Phone、iOS 或 Android)，請於此時執行。

4.  按一下 [Create TodoItems table] 以建立儲存應用程式資料的資料表。

5.  在 [Download and run your app] 下，按 [下載]。

    這會下載與您的行動服務連接的範例應用程式*待辦事項* 的專案，以及行動服務 JavaScript SDK。請將壓縮的專案檔案儲存至本機電腦，並記下儲存位置。

## 執行新的 PhoneGap 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

1.  瀏覽至您儲存此壓縮專案檔案的位置，並在您的電腦上展開檔案。

2.  根據以下各個平台的指示，開啟並執行對應的專案。

    -   **Windows Phone 8**

    1.  Windows Phone 8：在 Visual Studio 2012 Express for Windows Phone 中，開啟 **platforms\\wp8** 資料夾中的 .sln 檔案。

    2.  按 **F5** 鍵，以重建專案並啟動應用程式。

        ![][3]

    -   **iOS**

    1.  在 Xcode 中，開啟 **platforms/ios** 資料夾中的專案。

    2.  按 [執行] 按鈕以建立專案，並在 iPhone 模擬器中啟動應用程式 (此專案的預設選項)。

        ![][0]

    -   **Android**

        1.  在 Eclipse 中，依序按一下 [檔案]、[匯入]、展開 [Android]，按一下 [匯入現有的 Android 程式碼至工作區]，然後按 [下一步]。

        2.  按一下 [瀏覽] 瀏覽到展開的專案檔案位置，並且按一下 [確定] 確定已勾選 TodoActivity 專案，然後按一下 [完成]。

            </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">這會將專案檔案匯入目前的工作區中。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

            </p>
        3.  從 [執行] 功能表中，按一下 [執行] 在 Android 模擬器中啟動專案。

            ![][4]

        > [WACOM.NOTE]若要能夠在 Android 模擬器中執行此專案，您至少必須定義一個 Android 虛擬裝置 (AVD)。使用 AVD Manager 來建立與管理這些裝置。

3.  在前述其中一個行動模擬器中啟動應用程式後，在文字方塊中輸入文字，然後按一下 [新增]。

    如此會傳送 POST 要求到 Azure 中代管的新行動服務。要求中的資料會插入 **TodoItem** 資料表中。</cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

    <div class="dev-callout"><strong>重要事項</strong> <p>如果使用 PhoneGap 工具重新建置主要專案，將會覆寫此平台專案的變更。因此，請依照下一節中的說明，在專案的根 www 目錄中進行變更。</p></div>

4.  回到管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItems] 資料表。

    ![][5]

    如此可讓您瀏覽由應用程式插入資料表中的資料。

    ![][6]

## 為各個平台進行應用程式更新及重新建置專案

1.  變更 ´www´ 目錄中的程式碼檔案，在此案例中為 ´todolist/www´。

2.  驗證所有的目標平台工具皆可從系統路徑存取。

3.  在根專案目錄中開啟命令提示字元，然後執行下列其中一個平台特定命令：

    -   **Windows Phone**

        從 Visual Studio 開發人員命令提示字元執行下列命令：

            phonegap local build wp8

    -   **iOS**

        開啟終端機，然後執行下列命令：

            phonegap local build ios

    -   **Android**

        開啟命令提示字元或終端機視窗，然後執行下列命令：

            phonegap local build android

4.  根據下一節中的說明，在適當的開發環境中開啟每個專案。

> [WACOM.NOTE]您可以檢閱存取行動服務來查詢和插入資料的程式碼，這可以在 js/index.js 檔案中找到。

## <a name="next-steps"> </a>後續步驟

請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

-   [開始使用資料][開始使用資料]
    深入了解使用行動服務來儲存與查詢資料。

-   [開始使用驗證][開始使用驗證]
    了解如何向身分識別提供者驗證應用程式的使用者。

 


  [0]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
  [Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Android Developer Tools]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
  [2]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
  [3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
  [4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png
  [5]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png
  [6]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png
  [開始使用資料]: /zh-tw/documentation/articles/mobile-services-html-get-started-data
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-html-get-started-users
