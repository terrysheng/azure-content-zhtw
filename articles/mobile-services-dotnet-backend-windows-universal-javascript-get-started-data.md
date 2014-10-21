<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-javascript-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# 開始使用行動服務中的資料

[WACOM.INCLUDE [mobile-services-selector-get-started-data][mobile-services-selector-get-started-data]]

本主題將說明如何以 Azure 行動服務作為 Windows 市集應用程式的後端資料來源。在本教學課程中，您將下載 Visual Studio 2013 專案，以取得在記憶體中儲存資料的應用程式、建立新的行動服務、整合行動服務與該應用程式，然後檢視執行應用程式時所做的資料變更。

您在此教學課程中建立的行動服務，是一項 .NET 後端行動服務。.NET 後端服務讓您得以在行動服務中運用 .NET 語言與 Visual Studio 建立伺服器端商業邏輯，並在本機電腦上執行與偵錯行動服務。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 JavaScript 後端版本。

> [WACOM.NOTE]本主題說明如何使用 Visual Studio Professional 2013 Update 3 的工具，將新的行動服務連接到通用 Windows 應用程式。如果您無法升級至 Visual Studio Professional 2013 Update 3 或是您偏好將行動服務專案手動新增到 Windows 市集應用程式解決方案，請參閱[此版本][此版本]的主題。

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 Windows 市集應用程式專案][下載 Windows 市集應用程式專案]
2.  [從 Visual Studio 建立新的行動服務][從 Visual Studio 建立新的行動服務]
3.  [在本機測試行動服務專案][在本機測試行動服務專案]
4.  [更新應用程式以使用行動服務][更新應用程式以使用行動服務]
5.  [將行動服務發佈至 Azure][將行動服務發佈至 Azure]
6.  [對 Azure 代管的服務測試應用程式][對 Azure 代管的服務測試應用程式]
7.  [檢視儲存在 SQL Database 裡的資料][檢視儲存在 SQL Database 裡的資料]

若要完成此教學課程，您需要下列項目：

-   使用中的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][Azure 免費試用]。
-   [Visual Studio Professional 2013][Visual Studio Professional 2013]。您可以使用免費試用版。

## <a name="download-app"></a>下載 GetStartedWithData 專案

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project][mobile-services-windows-universal-javascript-download-project]]

## <a name="create-service"></a>從 Visual Studio 建立新的行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013][mobile-services-dotnet-backend-create-new-service-vs2013]]

1.  在 [方案總管] 中瀏覽至 [**services\\mobileService\\scripts**] 子資料夾，然後開啟 service.js 指令碼檔案，並注意新的全域變數看起來會如下列範例所示：

        var todolistClient = new WindowsAzure.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

    本程式碼透過使用全域變數，提供對應用程式中新行動服務的存取權。用戶端是利用提供新行動服務的 URI 和應用程式金鑰來建立。由於此指令碼的參考已新增到 default.html 檔案，因此此變數可供所有也從此頁面受參考的指令碼檔案使用。

2.  開啟 default.html 專案檔案，並找到新的 service.js 指令碼檔案參考，然後確定參考路徑如下列所示：

        <script src="/services/mobileServices/scripts/todolist.js">

    Visual Studio 目前存在一個會在路徑中產生錯誤資料夾名稱的錯誤。

3.  以滑鼠右鍵按一下 [Windows Phone] 應用程式專案，然後依據按一下 [新增]、[已連接服務]，並選取您剛建立的行動服務，然後按一下 [確定]。

    相同的新程式碼檔案會新增至 Windows Phone 市集應用程式專案。務必一併修正新增至 default.html 檔案的參考路徑。

這時，Windows 市集與 Windows Phone 市集應用程式都會同時連接到新的行動服務。下個步驟是測試新的行動服務專案。

## <a name="test-the-service-locally"></a>在本機測試行動服務專案

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation][mobile-services-dotnet-backend-test-local-service-api-documentation]]

## <a name="update-app"></a>更新應用程式以使用行動服務

在本節中，您將會更新通用 Windows 應用程式，以使用行動服務作為應用程式的後端服務。您只需要變更 [GetStartedWithData.Shared] 專案資料夾的 default.js 專案檔案。

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app][mobile-services-windows-javascript-update-data-app]]

## <a name="publish-mobile-service"></a>將行動服務發佈至 Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## <a name="test-azure-hosted"></a>測試 Azure 代管的行動服務

現在我們可以將兩個通用的 Windows 應用程式同時與 Azure 代管的行動服務一起測試。

[WACOM.INCLUDE [mobile-services-windows-universal-test-app][mobile-services-windows-universal-test-app]]

## <a name="view-stored-data"></a>檢視儲存在 SQL Database 裡的資料

[WACOM.INCLUDE [mobile-services-dotnet-backend-view-sql-data][mobile-services-dotnet-backend-view-sql-data]]

這將結束**開始使用資料**教學課程。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓通用 Windows 應用程式專案能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

-   [使用指令檔驗證與修改資料][使用指令檔驗證與修改資料]
    深入了解在行動服務中使用伺服器指令檔，來驗證並變更從應用程式傳送出來的資料。

-   [使用分頁縮小查詢範圍][使用分頁縮小查詢範圍]
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料序列之後，請嘗試下列其中一個其他教學課程：

-   [開始使用驗證][開始使用驗證]
    了解如何驗證應用程式的使用者。

-   [開始使用推播通知][開始使用推播通知]
    了解如何將極為基本的推播通知傳送到應用程式。

-   [行動服務 .NET 作法概念性參考][行動服務 .NET 作法概念性參考]
    深入了解如何搭配使用行動服務與 HTML 和 JavaScript。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [此版本]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
  [下載 Windows 市集應用程式專案]: #download-app
  [從 Visual Studio 建立新的行動服務]: #create-service
  [在本機測試行動服務專案]: #test-the-service-locally
  [更新應用程式以使用行動服務]: #update-app
  [將行動服務發佈至 Azure]: #publish-mobile-service
  [對 Azure 代管的服務測試應用程式]: #test-azure-hosted
  [檢視儲存在 SQL Database 裡的資料]: #view-stored-data
  [Azure 免費試用]: http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-javascript-get-started-data%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-windows-universal-javascript-download-project]: ../includes/mobile-services-windows-universal-javascript-download-project.md
  [mobile-services-dotnet-backend-create-new-service-vs2013]: ../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md
  [mobile-services-dotnet-backend-test-local-service-api-documentation]: ../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md
  [mobile-services-windows-javascript-update-data-app]: ../includes/mobile-services-windows-javascript-update-data-app.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [mobile-services-dotnet-backend-view-sql-data]: ../includes/mobile-services-dotnet-backend-view-sql-data.md
  [使用指令檔驗證與修改資料]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js
  [使用分頁縮小查詢範圍]: /en-us/develop/mobile/tutorials/add-paging-to-data-js
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
  [開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
  [行動服務 .NET 作法概念性參考]: /zh-tw/documentation/articles/mobile-services-html-how-to-use-client-library/
