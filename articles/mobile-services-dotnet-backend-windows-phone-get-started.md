<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Get Started (WP8)" pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>開始使用行動服務

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

本教學課程說明如何使用 Azure 行動服務在 Windows Phone 8 應用程式中新增雲端型後端服務。在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單*待辦事項*應用程式。您所將建立的行動服務，會使用 Visual Studio 與支援的 .NET 語言撰寫伺服器端商務邏輯，並管理行動服務。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [JavaScript 後端版本][JavaScript 後端版本]。

以下是完成應用程式的螢幕擷取畫面：

![][0]

> [WACOM.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，您可以註冊 Azure 試用版並取得高達 10 項的免費行動服務。此外，在試用期間結束後您仍可繼續使用這些服務。如需詳細資訊，請參閱 [Azure 免費試用][Azure 免費試用]。
> 本教學課程需要 [Visual Studio Professional 2013][Visual Studio Professional 2013]。您可以使用免費試用版。若要建立新的 Windows Phone 8.1 應用程式，您必須具備 Visual Studio 2013 Update 2 或更新的版本。

## <a name="create-new-service"> </a>建立新的行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 建立新的 Windows Phone 應用程式

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。

在本節中，您將建立連線至您行動服務的新 Windows Phone 8 應用程式。

1.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2.  在 [快速入門] 索引標籤中，按一下 [選擇平台] 底下的 [Windows Phone 8]，然後展開 [Create a new Windows Phone 8 app]。

    ![][1]

    </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">這將顯示三個簡單步驟，可用來建立連接到您行動服務的</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000"> Windows Phone </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">應用程式。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

    ![][2]

3.  在您的本機電腦或虛擬機器下載並安裝 [Visual Studio Professional 2013][Visual Studio Professional 2013] (如果您尚未這麼做)。

4.  在 [Download and publish you service to the cloud] 下，按一下 [下載]。

    這會下載一個方案，其中包含行動服務的專案，以及與行動服務連接的範例*待辦事項*應用程式的專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

5.  在 [Publish your service to the cloud] 下，下載您的發佈設定檔、將下載的檔案儲存至本機電腦，並記下儲存位置。

## 在本機電腦上測試行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

> [WACOM.NOTE]要執行連接本機服務的 Windows Phone 應用程式，必須額外執行幾個設定步驟。本主題將不說明其執行方式，但您可以在[如何從 Windows Phone 8 模擬器連接到本機 Web 服務][如何從 Windows Phone 8 模擬器連接到本機 Web 服務]中了解相關資訊。

## 發佈行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

1.  在 Windows Phone 應用程式專案中開啟 App.xaml.cs 檔案、找出建立 [MobileServiceClient][MobileServiceClient] 執行個體的程式碼、使用 *localhost* 將建立此用戶端的程式碼註解化，然後使用遠端行動服務 URL 將建立此用戶端程式碼取消註解，如下所示：

        public static MobileServiceClient MobileService = new MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXX-APPLICATION-KEY-XXXXXXXX");

    此時，用戶端會存取發佈至 Azure 的行動服務。

2.  (選用) 如果您要建立 Windows Phone 8.1 應用程式，請在 [方案總管] 中，以滑鼠右鍵按一下專案，然後按一下 [屬性]，將 [目標 Windows Phone OS 版本] 設為 [Windows Phone 8.1]，然後按一下 [是]以升級專案。

3.  按 **F5** 鍵，以重建專案並啟動應用程式。

4.  在應用程式中輸入有意義的文字 (如 *Complete the tutorial*)，然後按一下 [儲存]。

    如此會傳送 POST 要求到 Azure 中代管的新行動服務。要求中的資料會插入 TodoItem 資料表中。</cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

    <div class="dev-callout"> 
<b>注意</b> 
<p>您可以檢閱存取行動服務來查詢和插入資料的程式碼，這可以在 MainPage.xaml.cs 檔案中找到。</p> 
    </div>

![][3]

這會說明如何對執行於 Azure 中的行動服務執行新的用戶端應用程式。您必須設定 Web 伺服器和防火牆以允許來自您的 Windows Phone 裝置或模擬器的存取，才能對執行於本機電腦上的行動服務測試 Windows Phone 應用程式。如需詳細資訊，請參閱[設定本機 Web 伺服器以允許連接到本機行動服務][設定本機 Web 伺服器以允許連接到本機行動服務]。

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

-   [行動服務 .NET 後端疑難排解][行動服務 .NET 後端疑難排解]
     了解如何診斷及修復行動服務 .NET 後端可能發生的問題。

 
 


  [JavaScript 後端版本]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started
  [0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [Azure 免費試用]: http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [如何從 Windows Phone 8 模擬器連接到本機 Web 服務]: http://go.microsoft.com/fwlink/p/?LinkId=391930
  [MobileServiceClient]: http://msdn.microsoft.com/zh-tw/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [設定本機 Web 伺服器以允許連接到本機行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express
  [開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [開始使用離線資料同步]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
  [開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [行動服務 .NET 後端疑難排解]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
