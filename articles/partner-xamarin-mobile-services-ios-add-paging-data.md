<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-ios" urlDisplayName="" pageTitle="Add paging to data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin iOS app from Mobile Services." metaCanonical="" services="" authors="donnam" solutions="" manager="dwrede" editor="" title="Refine Mobile Services queries with paging" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# 使用分頁縮小行動服務查詢範圍

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows 市集 C#">Windows 市集 C#</a><a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-js" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">iOS C#</a><a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">Android C#</a>
</div>

此主題將說明如何使用分頁，管理從 Azure 行動服務傳回至 iOS 應用程式的資料量。在此教學課程中，您將在用戶端上使用 **Skip** 和 **Take** 查詢屬性要求特定的資料「頁面」。

<div class="dev-callout"><b>注意</b>
<p>為防止行動裝置用戶端出現資料溢位的狀況，行動服務會實作自動分頁限制，而將回應中的項目數上限預設為 50 個。您可以指定頁面大小，以明確要求在回應中傳回最多 1,000 個項目。</p>
</div>

本教學課程會以上一堂教學課程[開始使用資料][]中的步驟和範例應用程式為基礎。開始進行此教學課程之前，您至少必須先完成資料序列中的第一個教學課程[開始使用資料][]。

1.  在 Xamarin Studio 中，開啟您完成[開始使用資料][]教學課程時所修改的專案。

2.  按 [執行] 按鈕以建置專案並啟動應用程式，在文字方塊中輸入文字，然後按一下加號 (**+**) 圖示。

3.  重複前述步驟至少三次，以在 TodoItem 資料表中儲存三個以上的項目。

4.  開啟 **TodoService.cs** 檔案，找出 **RefreshDataAsync** 方法，然後以下列查詢取代 `todoTable` 上的 LINQ 查詢：

            Items = await todoTable
                            .Where (todoItem => todoItem.Complete == false)
                            .Take(3)
                            .ToListAsync();

    此查詢會傳回並未標記為已完成的前三個項目。

5.  重新建置並啟動應用程式。

    請注意，TodoItem 資料表中只有前三個結果會顯示出來。

6.  再次將 **RefreshDataAsync** 方法中的 LINQ 查詢更新為下列內容：

            Items = await todoTable
                            .Where (todoItem => todoItem.Complete == false)
                            .Skip(3)
                            .Take(3)
                            .ToListAsync();

    這次，將 **Skip** 值設為 3。

    此查詢會略過前三個結果，並傳回接下來的三個結果。實際上這就是第二「頁」資料，頁面大小為三個項目。

    <div class="dev-callout"><b>注意</b>
<p>此教學課程使用的是為 <strong>Skip</strong> 和 <strong>Take</strong> 屬性設定硬式編碼分頁值的簡化案例。在實際的應用程式中，您可以搭配頁面巡覽區控制項或類似的 UI 來使用類似上述的查詢，讓使用者導覽至上一頁和下一頁。您也可以呼叫 <strong>IncludeTotalCount</strong> 方法，以取得伺服器上所有可用項目的總計數和分頁資料。</p>
</div>

## <a name="next-steps"> </a>後續步驟

有關於在行動服務中使用資料的基本概念教學課程，在此告一段落。建議您深入了解下列行動服務主題：

-   [開始使用驗證][]
    了解如何使用 Windows 帳戶驗證您的應用程式的使用者。

<!-- 
* [Get started with push notifications]  
  <br/>Learn how to send a very basic push notification to your app. 
--> 
<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows 市集 C#]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows 市集 C#"
  [Windows 市集 JavaScript]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-js "Windows 市集 JavaScript"
  [Windows Phone]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone"
  [iOS]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-ios "iOS"
  [Android]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-android "Android"
  [HTML]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-html "HTML"
  [iOS C#]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS"
  [Android C#]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android"
  [開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-xamarin-ios
