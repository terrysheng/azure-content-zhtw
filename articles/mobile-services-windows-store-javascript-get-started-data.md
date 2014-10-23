<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/19/2014" ms.author="glenga"></tags>

# 開始使用行動服務中的資料

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy][]]

<div class="dev-center-tutorial-subselector">
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title=".NET 後端">.NET 後端</a> | 
<a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="JavaScript 後端" class="current">JavaScript 後端</a>
</div>

本主題將示範如何使用 Azure 行動服務，以在 Windows 市集應用程式中運用資料。在本教學課程中，您將下載 Visual Studio 2013 專案，以取得在記憶體中儲存資料的應用程式、建立新的行動服務、整合行動服務與該應用程式，然後登入 Azure 管理入口網站來檢視執行應用程式時所做的資料變更。

> [WACOM.NOTE]本主題說明如何使用 Visual Studio 2013 將 Azure 行動服務新增至 Windows 市集專案。您可將相同的 JavaScript 後端行動服務新增至通用 Windows 應用程式專案中。如需詳細資訊，請參閱本教學課程的[通用 Windows 應用程式版本][]。

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 Windows 市集應用程式專案][]
2.  [建立行動服務][]
3.  [新增用於儲存的資料表][]
4.  [更新應用程式以使用行動服務][]
5.  [針對行動服務進行應用程式測試][]

若要完成此教學課程，您需要下列項目：

-   使用中的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][]。
-   Visual Studio 2013 可讓您更輕鬆地連接 Windows 市集應用程式與行動服務。若要使用 Visual Studio 2012 完成相同的基本程序，請依照主題[使用 Visual Studio 2012 開始使用行動服務中的資料][]中的步驟進行。

## <a name="download-app"></a><span class="short-header">下載專案</span>下載 GetStartedWithData 專案

本教學課程是採用 [GetStartedWithMobileServices 應用程式][]所建立，此應用程式是 Visual Studio 2013 中的 Windows 市集應用程式專案。除了會將新增項目儲存在本機記憶體中之外，此應用程式的 UI 與行動服務快速入門所產生的應用程式完全相同。

1.  從[開發人員程式碼範例網站][GetStartedWithMobileServices 應用程式] (英文) 下載 JavaScript 版本的 GetStartedWithData 範例應用程式。

2.  在 Visual Studio 2012 Express for Windows 8 中，開啟下載專案，並展開 **js** 資料夾檢查 default.js 檔案。

    請注意，新增的 **TodoItem** 物件儲存在記憶體內部的 **List** 物件中。

3.  按 **F5** 鍵，以重建專案並啟動應用程式。

4.  在應用程式的 [Insert a TodoItem] 中鍵入一些文字，然後按一下 [儲存]。

    ![][]

    請注意，儲存的文字會顯示在 [查詢和更新資料] 下方的第二個資料欄中。

## <a name="create-service"></a>從 Visual Studio 建立新的行動服務

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013][]]

1.  在 [方案總管] 中展開 \*\*services\*\*、\*\*mobile services\*\*、\*\*\<your\_service\>\*\* 資料夾，然後開啟 service.js 指令碼檔案，並注意新的全域變數看起來會如下列範例所示：

        var todolistClient = new WindowsAzure.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

    本程式碼透過使用全域變數，提供對應用程式中新行動服務的存取權。用戶端是利用提供新行動服務的 URI 和應用程式金鑰來建立。由於此指令碼的參考已新增到 default.html 檔案，因此此變數可供所有也從此頁面受參考的指令碼檔案使用。

## <a name="add-table"></a>新增用於儲存資料的資料表

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013][]]

> [WACOM.NOTE]新的資料表是使用 Id、\_\_createdAt、\_\_updatedAt 和 \_\_version 等資料行建立而成。啟用動態結構描述時，行動服務會根據插入或更新要求中的 JSON 物件自動產生新資料行。如需詳細資訊，請參閱[動態結構描述][]。

# <a name="update-app"></a>更新應用程式以使用行動服務

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app][]]

## <a name="test-app"></a>對新的行動服務進行應用程式測試

1.  在 Visual Studio 中，按 F5 鍵以執行此應用程式。

2.  和之前一樣，在 [Insert a TodoItem] 中鍵入文字，然後按一下 [儲存]。

    這會傳送新項目以插入至行動服務。

3.  在[管理入口網站][]中按一下 [行動服務]，然後按一下您的行動服務。

4.  按一下 [資料] 索引標籤，然後按一下 [瀏覽]。

    ![][1]

    請注意，[TodoItem] 資料表現在包含資料和行動服務產生的識別碼值，且資料行已自動新增至資料表，以符合應用程式中的 TodoItem 類別。

5.  在應用程式中檢查清單中的其中一個項目，然後返回入口網站中的 [瀏覽] 索引標籤，並按一下 [重新整理]。

    請注意，完成值已從 [false] 變更為 [true]。

6.  在 default.js 專案檔案中，使用下列可篩選出已完成項目的程式碼，來取代現有的 **RefreshTodoItems** 函數：

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7.  在應用程式中檢查清單中的其中一個項目，然後按一下 [重新整理] 按鈕。

    請注意，所有已核取的項目都會從清單中消失。Each refresh results in a round-trip to the mobile service, which now returns filtered data.

這將結束**開始使用資料**教學課程。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓 Windows 市集應用程式能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

-   [使用指令檔驗證與修改資料][]
    深入了解在行動服務中使用伺服器指令檔，來驗證並變更從應用程式傳送出來的資料。

-   [使用分頁縮小查詢範圍][]
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料序列之後，請嘗試下列其中一個其他教學課程：

-   [開始使用驗證][]
    了解如何驗證應用程式的使用者。

-   [開始使用推播通知][]
    了解如何將極為基本的推播通知傳送到應用程式。

-   [行動服務 HTML/JavaScript 作法概念參考][]
    深入了解如何將行動服務搭配 HTML 及 JavaScript 使用。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-data-legacy]: ../includes/mobile-services-selector-get-started-data-legacy.md
  [.NET 後端]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ ".NET 後端"
  [JavaScript 後端]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-data/ "JavaScript 後端"
  [通用 Windows 應用程式版本]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
  [下載 Windows 市集應用程式專案]: #download-app
  [建立行動服務]: #create-service
  [新增用於儲存的資料表]: #add-table
  [更新應用程式以使用行動服務]: #update-app
  [針對行動服務進行應用程式測試]: #test-app
  [Azure 免費試用]: http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F
  [使用 Visual Studio 2012 開始使用行動服務中的資料]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-data-vs2012
  [GetStartedWithMobileServices 應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  []: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [動態結構描述]: http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx
  [mobile-services-windows-javascript-update-data-app]: ../includes/mobile-services-windows-javascript-update-data-app.md
  [管理入口網站]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
  [使用指令檔驗證與修改資料]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
  [使用分頁縮小查詢範圍]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/
  [行動服務 HTML/JavaScript 作法概念參考]: /zh-tw/documentation/articles/mobile-services-html-how-to-use-client-library/
