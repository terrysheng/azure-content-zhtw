<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 開始使用行動服務中的資料

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

本主題將示範如何使用 Azure 行動服務，充分運用通用 Windows 應用程式裡的資料。通用 Windows 應用程式解決方案包括 Windows 市集 8.1 和 Windows Phone 市集 8.1 應用程式的專案，以及一般共用專案。如需詳細資訊，請參閱[建置目標為 Windows 和 Windows Phone 的通用 Windows 應用程式][建置目標為 Windows 和 Windows Phone 的通用 Windows 應用程式]。

在本教學課程中，您將下載 Visual Studio 2013 專案，以取得在記憶體中儲存資料的通用 Windows 應用程式、建立新的行動服務、整合行動服務與該應用程式，然後登入 Azure 管理入口網站來檢視執行應用程式時所做的資料變更。

> [WACOM.NOTE]本主題說明如何使用 Visual Studio Express 2013 for Windows with Update 3 的工具，將新的行動服務連接到通用 Windows 應用程式。要將行動服務連接到 Windows 市集或 Windows Phone 市集 8.1 應用程式時，也可使用相同的步驟進行。若要將行動服務連接至 Windows Phone 8.0 或 Windows Phone Silverlight 8.1 應用程式，請參閱[開始使用 Windows Phone 的資料][開始使用 Windows Phone 的資料]。

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 Windows 市集應用程式專案][下載 Windows 市集應用程式專案]
2.  [建立行動服務][建立行動服務]
3.  [新增用於儲存的資料表][新增用於儲存的資料表]
4.  [更新應用程式以使用行動服務][更新應用程式以使用行動服務]
5.  [針對行動服務進行應用程式測試][針對行動服務進行應用程式測試]

若要完成此教學課程，您需要下列項目：

-   使用中的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][Azure 免費試用]。
-   [Visual Studio Express 2013 for Windows][Visual Studio Express 2013 for Windows] (Update 2 或更新版本)。

## <a name="download-app"></a>下載 GetStartedWithData 專案

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

## <a name="create-service"></a>從 Visual Studio 建立新的行動服務

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

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

## <a name="add-table"></a>將新資料表新增至行動服務

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

> [WACOM.NOTE]新的資料表是使用 Id、\_\_createdAt、\_\_updatedAt 和 \_\_version 等資料欄建立而成。啟用動態結構描述時，行動服務會根據插入或更新要求中的 JSON 物件自動產生新資料行。如需詳細資訊，請參閱[動態結構描述][動態結構描述]。

# <a name="update-app"></a>更新應用程式以使用行動服務

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

## <a name="test-azure-hosted"></a>測試 Azure 代管的行動服務

現在我們可以將兩個通用的 Windows 應用程式同時與 Azure 代管的行動服務一起測試。

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

1.  在[管理入口網站][管理入口網站]中按一下 [行動服務]，然後按一下您的行動服務。

    <p>
2.  按一下 [資料] 索引標籤，然後按一下 [瀏覽]。

    請注意，[TodoItem] 資料表現在包含資料和行動服務產生的識別碼值，且資料行已自動新增至資料表，以符合應用程式中的 TodoItem 類別。

![][0]

這將結束**開始使用資料**教學課程。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓通用 Windows 應用程式能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

-   [使用指令檔驗證與修改資料][使用指令檔驗證與修改資料]
    深入了解在行動服務中使用伺服器指令檔，來驗證並變更從應用程式傳送出來的資料。

-   [使用分頁縮小查詢範圍][使用分頁縮小查詢範圍]
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料序列之後，請嘗試下列其中一個其他教學課程：

-   [開始使用驗證][開始使用驗證]
    了解如何驗證應用程式的使用者。

-   [開始使用推播通知][開始使用推播通知]
    了解如何將極為基本的推播通知傳送到應用程式。

-   [行動服務 HTML/JavaScript 作法概念參考][行動服務 HTML/JavaScript 作法概念參考]
    深入了解如何將行動服務搭配 HTML 及 JavaScript 使用。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [建置目標為 Windows 和 Windows Phone 的通用 Windows 應用程式]: http://msdn.microsoft.com/zh-tw/library/windows/apps/xaml/dn609832.aspx
  [開始使用 Windows Phone 的資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [下載 Windows 市集應用程式專案]: #download-app
  [建立行動服務]: #create-service
  [新增用於儲存的資料表]: #add-table
  [更新應用程式以使用行動服務]: #update-app
  [針對行動服務進行應用程式測試]: #test-app
  [Azure 免費試用]: http://azure.microsoft.com/zh-tw/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-javascript-get-started-data%2F
  [Visual Studio Express 2013 for Windows]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-windows-universal-javascript-download-project]: ../includes/mobile-services-windows-universal-dotnet-download-project.md
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [動態結構描述]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj193175.aspx
  [mobile-services-windows-javascript-update-data-app]: ../includes/mobile-services-windows-javascript-update-data-app.md
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [管理入口網站]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png
  [使用指令檔驗證與修改資料]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
  [使用分頁縮小查詢範圍]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/
  [行動服務 HTML/JavaScript 作法概念參考]: /zh-tw/documentation/articles/mobile-services-html-how-to-use-client-library/
