<properties pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>開始使用行動服務

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>本教學課程說明如何使用 Azure 行動服務在 Windows Phone 8 應用程式中新增雲端型後端服務。在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單<em>待辦事項</em>應用程式。</p>
<p>如果想要看影片，右邊片段播放的步驟與本教學課程相同。在影片中，Nick Harris 將介紹行動服務，並引導您建立自己的第一個行動服務，並從 Windows 市集應用程式與之連線。</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a> <span class="time">下午 01:18:00</span></div>

</div>

在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單*待辦事項*應用程式。您將建立的行動服務會使用 JavaScript 建立伺服器端商務邏輯。若要建立可讓您在 Visual Studio 以支援的 .NET 語言撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [.NET 後端版本][]。

以下是完成應用程式的螢幕擷取畫面：

![][]

> [WACOM.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，您可以註冊 Azure 試用版並取得高達 10 項的免費行動服務。此外，在試用期間結束後您仍可繼續使用這些服務。如需詳細資訊，請參閱 [Azure 免費試用][]。若要建立新的 Windows Phone 8.1 應用程式，您必須具備 Visual Studio 2013 Update 2 或更新版本。

## <a name="create-new-service"> </a>建立新的行動服務

[WACOM.INCLUDE [mobile-services-create-new-service][]]

## 

## <span class="short-header">建立新的應用程式</span>建立新的 Windows Phone 應用程式

</h2>
在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。

在本節中，您將建立連線至您行動服務的新 Windows Phone 8 應用程式。

1.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2.  在 [快速入門] 索引標籤中，按一下 [選擇平台] 底下的 [Windows Phone 8]，然後展開 [Create a new Windows Phone 8 app]。

    ![][1]

    </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">這將顯示三個簡單步驟，可用來建立連接到您行動服務的</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000"> Windows Phone </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">應用程式。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

    ![][2]

3.  在本機電腦下載並安裝 [Visual Studio 2012 Express for Windows Phone][] (若您尚未這麼做)。

    > [WACOM.NOTE]若要建立新的 Windows Phone 8.1 應用程式，您必須具備 Visual Studio 2013 Update 2 或更新版本。

4.  按一下 [Create TodoItem table] 以建立儲存應用程式資料的資料表。

5.  按一下 [Download and run app] 下的 [下載]。

    這將會下載與行動服務連接的範例*待辦事項*應用程式專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

## 執行 Windows Phone 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

1.  瀏覽至儲存壓縮專案檔案的位置，在電腦上將檔案解壓縮，然後在 Visual Studio 中開啟方案檔。

2.  (選用) 如果您要建立 Windows Phone 8.1 應用程式，請在 [方案總管] 中，以滑鼠右鍵按一下專案，然後按一下 [屬性]，將 [目標 Windows Phone OS 版本] 設為 [Windows Phone 8.1]，然後按一下 [是] 以升級專案。

3.  按 **F5** 鍵，以重建專案並啟動應用程式。

4.  在應用程式中，輸入有意義的文字，如 *Complete the tutorial*，然後按一下 [儲存]。

    ![][3]

    如此會傳送 POST 要求到 Azure 中代管的新行動服務。要求中的資料會插入 TodoItem 資料表中。</cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

    > [WACOM.NOTE]您可以檢閱造成存取您行動服務來進行查詢和插入資料的程式碼，該程式碼位於 MainPage.xaml.cs 檔案中。

5.  回到管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItems] 資料表。

    ![][4]

    如此可讓您瀏覽由應用程式插入資料表中的資料。

    ![][5]

## <a name="next-steps"> </a>後續步驟

請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

-   [開始使用資料][]
    深入了解使用行動服務來儲存與查詢資料。

-   [開始使用離線資料同步][]
    了解如何使用離線資料同步，讓您的應用程式更穩健及具備回應力。

-   [開始使用驗證][]
    了解如何向身分識別提供者驗證應用程式的使用者。

-   [開始使用推播通知][]
    了解如何將極為基本的推播通知傳送到應用程式。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [觀看教學課程]: http://go.microsoft.com/fwlink/?LinkId=290816
  [.NET 後端版本]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
  []: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [Azure 免費試用]: http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2FFzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-windows-phone-get-started%2F
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [3]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [4]: ./media/mobile-services-windows-phone-get-started/mobile-data-tab.png
  [5]: ./media/mobile-services-windows-phone-get-started/mobile-data-browse.png
  [開始使用資料]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-data/
  [開始使用離線資料同步]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-users/
  [開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
