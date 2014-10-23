<properties linkid="develop-mobile-tutorials-add-paging-to-data-html" urlDisplayName="Add paging to data (HTML5)" pageTitle="Add paging to data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your HTML app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# 使用分頁縮小行動服務查詢範圍

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows 市集 C#">Windows 市集 C#</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-js" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

此主題將說明如何使用分頁，管理從 Azure 行動服務傳回至 HTML 應用程式的資料量。在此教學課程中，您將在用戶端上使用 **Take** 和 **Skip** 查詢方法要求特定的資料「頁面」。

<div class="dev-callout"><b>注意</b>
<p>為防止行動裝置用戶端出現資料溢位的狀況，行動服務會實作自動分頁限制，而將回應中的項目數上限預設為 50 個。您可以指定頁面大小，以明確要求在回應中傳回最多 1,000 個項目。</p>
</div>

本教學課程會以上一堂教學課程[開始使用資料][]中的步驟和範例應用程式為基礎。開始進行此教學課程之前，您至少必須先完成資料序列中的第一個教學課程[開始使用資料][]。

1.  從您完成[開始使用資料][]教學課程時所修改之專案的 **server** 子資料夾，執行下列其中一個命令檔案。

    -   **launch-windows** (Windows 電腦)
    -   **launch-mac.command** (Mac OS X 電腦)
    -   **launch-linux.sh** (Linux 電腦)

    <div class="dev-callout"><b>注意</b>
<p>在 Windows 電腦上，PowerShell 要求您確認想要執行指令碼時，請輸入 'R'。因為指令碼是從網際網路中下載，所以您的網頁瀏覽器可能會警告您不要執行指令碼。發生此情況時，您必須要求瀏覽器繼續載入指令碼。</p>
</div>

    這會在本機電腦上啟動 Web 伺服器，以代管應用程式。

2.  在網頁瀏覽器中導覽至 <http://localhost:8000/>，在 [Add new task] 中輸入文字，然後按一下 [新增]。

3.  重複前述步驟至少三次，以在 TodoItem 資料表中儲存三個以上的項目。

4.  在 app.js 檔案中，將 **refreshTodoItems** 方法中的 `query` 變數定義取代為下列程式碼：

        var query = todoItemTable.where({ complete: false }).take(3);

    This query, when executed, returns the top three items that are not marked as completed.

5.  回到網頁瀏覽器，並重新載入頁面。

    請注意，TodoItem 資料表中只有前三個結果會顯示出來。

6.  (選用) 使用訊息檢查軟體 (例如瀏覽器開發人員工具或 Fiddler) 檢視傳送至行動服務之要求的 URI。

    請注意，**take(3)** 方法在查詢 URI 中會轉譯成查詢選項 **$top=3**。

7.  再次以下列程式碼更新查詢：

        var query = todoItemTable.where({ complete: false }).skip(3).take(3);

8.  回到網頁瀏覽器，並重新載入頁面。

    This query skips the first three results and returns the next three after that.實際上這就是第二「頁」資料，頁面大小為三個項目。

    <div class="dev-callout"><b>注意</b>
<p>此教學課程使用的是將硬式編碼分頁值傳遞至 <strong>Take</strong> 和 <strong>Skip</strong> 方法的簡化案例。在實際的應用程式中，您可以搭配頁面巡覽區控制項或類似的 UI 來使用類似上述的查詢，讓使用者導覽至上一頁和下一頁。您也可以呼叫 <strong>includeTotalCount</strong> 方法，以取得伺服器上可用項目的總計數和分頁資料。</p>
</div>

9.  (選用) 檢視傳送至行動服務之要求的 URI。

    請注意，**skip(3)** 方法在查詢 URI 中會轉譯成查詢選項 **$skip=3**。

## <a name="next-steps"> </a>後續步驟

有關於在行動服務中使用資料的基本概念教學課程，在此告一段落。接著，請在[開始使用驗證][]中了解如何驗證應用程式的使用者。深入了解如何透過 HTML/JavaScript 來使用行動服務，請參閱[行動服務 HTML/JavaScript 作法概念性參考][]

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows 市集 C#]: /en-us/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows 市集 C#"
  [Windows 市集 JavaScript]: /en-us/develop/mobile/tutorials/add-paging-to-data-js "Windows 市集 JavaScript"
  [Windows Phone]: /en-us/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone"
  [iOS]: /en-us/develop/mobile/tutorials/add-paging-to-data-ios "iOS"
  [Android]: /en-us/develop/mobile/tutorials/add-paging-to-data-android "Android"
  [HTML]: /en-us/develop/mobile/tutorials/add-paging-to-data-html "HTML"
  [Xamarin.iOS]: /en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android"
  [開始使用資料]: /en-us/develop/mobile/tutorials/get-started-with-data-html
  [開始使用驗證]: /en-us/develop/mobile/tutorials/get-started-with-users-html
  [行動服務 HTML/JavaScript 作法概念性參考]: /en-us/develop/mobile/how-to-guides/work-with-html-js-client
