<properties linkid="" urlDisplayName="" pageTitle="Add paging to data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin iOS app from Mobile Services." metaCanonical="" services="" authors="" solutions="" manager="" editor="" title="Refine Mobile Services queries with paging" documentationCenter="Mobile" />

使用分頁縮小行動服務查詢範圍
============================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/add-paging-to-data-js "Windows 市集 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/add-paging-to-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/add-paging-to-data-html "HTML")[iOS C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS")[Android C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android")

此主題將說明如何使用分頁，管理從 Azure 行動服務傳回至 iOS 應用程式的資料量。在此教學課程中，您將在用戶端上使用 **Skip** 和 **Take** 查詢屬性要求特定的資料「頁面」。

**注意**

為防止行動裝置用戶端出現資料溢位的狀況，行動服務會實作自動分頁限制，而將回應中的項目數上限預設為 50 個。您可以指定頁面大小，以明確要求在回應中傳回最多 1,000 個項目。

本教學課程會以上一堂教學課程[開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios)中的步驟和範例應用程式為基礎。開始進行此教學課程之前，您至少必須先完成資料序列中的第一個教學課程[開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios)。

1.  在 Xamarin Studio 中，開啟您完成[開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios)教學課程時所修改的專案。

2.  按 **[執行]** 按鈕以建置專案並啟動應用程式，在文字方塊中輸入文字，然後按一下加號 (**+**) 圖示。

3.  重複前述步驟至少三次，以在 TodoItem 資料表中儲存三個以上的項目。

4.  開啟 **TodoService.cs** 檔案，找出 **RefreshDataAsync** 方法，然後以下列查詢取代 `todoTable` 上的 LINQ 查詢：

             Items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
                            .Take(3)
                    .ToListAsync();

        This query returns the top three items that are not marked as completed. 

5.  重新建置並啟動應用程式。

    請注意，TodoItem 資料表中只有前三個結果會顯示出來。

6.  再次將 **RefreshDataAsync** 方法中的 LINQ 查詢更新為下列內容：

             Items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .Skip(3)
        .Take(3)
        .ToListAsync();

        This time, set the **Skip** value to 3. 

        This query skips the first three results and returns the next three after that.實際上這就是第二「頁」資料，頁面大小為三個項目。

    **注意**

    此教學課程使用的是為 **Skip** 和 **Take** 屬性設定硬式編碼分頁值的簡化案例。在實際的應用程式中，您可以搭配頁面巡覽區控制項或類似的 UI 來使用類似上述的查詢，讓使用者導覽至上一頁和下一頁。您也可以呼叫 **IncludeTotalCount** 方法，以取得伺服器上所有可用項目的總計數和分頁資料。

後續步驟
--------

有關於在行動服務中使用資料的基本概念教學課程，在此告一段落。建議您深入了解下列行動服務主題：

-   [開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios)
    了解如何使用 Windows 帳戶驗證您的應用程式的使用者。

