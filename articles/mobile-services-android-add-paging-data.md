<properties linkid="develop-mobile-tutorials-add-paging-to-data-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Android app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# 使用分頁縮小行動服務查詢範圍

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows 市集 C#">Windows 市集 C#</a><a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-js" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-android" title="Android" class="current">Android</a><a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-tw/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

此主題將說明如何使用分頁，管理從 Azure 行動服務傳回至 Android 應用程式的資料量。在此教學課程中，您將在用戶端上使用 **top** 和 **skip** 查詢方法要求特定的資料「頁面」。

<div class="dev-callout"><b>注意</b>
<p>為防止行動裝置用戶端出現資料溢位的狀況，行動服務會實作自動分頁限制，而將回應中的項目數上限預設為 50 個。您可以指定頁面大小，以明確要求在回應中傳回最多 1,000 個項目。</p>
</div>

本教學課程會以上一堂教學課程[開始使用資料][開始使用資料]中的步驟和範例應用程式為基礎。開始進行此教學課程之前，您至少必須先完成資料序列中的第一個教學課程[開始使用資料][開始使用資料]。

1.  在 Eclipse 中，開啟您完成教學課程[開始使用資料][開始使用資料]時所建立的專案。

2.  從 [執行] 功能表按一下 [執行] 以啟動應用程式，然後在文字方塊中輸入文字，再按一下 [新增] 按鈕。

3.  重複前述步驟至少三次，以在 TodoItem 資料表中儲存三個以上的項目。

4.  在 ToDoActivity.java 檔案中，使用下列程式碼來取代 **RefreshTodoItems** 方法：

        private void refreshItemsFromTable() {
            // Define a filtered query that returns the top 3 items.
            mToDoTable.where().field("complete").eq(false).top(3)
                    .execute(new TableQueryCallback<ToDoItem>() {

                        public void onCompleted(List<ToDoItem> result, int count,
                                Exception exception, ServiceFilterResponse response) {
                            if (exception == null) {
                                mAdapter.clear();

                                for (ToDoItem item : result) {
                                    mAdapter.add(item);
                                }

                            } else {
                                createAndShowDialog(exception, "Error");
                            }
                        }
                    });
        }

    查詢會傳回並未標記為已完成的前三個項目。

5.  重新建置並啟動應用程式。

    請注意，TodoItem 資料表中只有前三個結果會顯示出來。

6.  (選用) 使用訊息檢查軟體 (例如瀏覽器開發人員工具或 Fiddler) 檢視傳送至行動服務之要求的 URI。

    請注意，`top(3)` 方法在查詢 URI 中會轉譯成查詢選項 `$top=3`。

7.  再次以下列程式碼更新 **RefreshTodoItems** 方法：

        private void refreshItemsFromTable() {
            // Define a filtered query that returns the top 3 items.
            mToDoTable.where().field("complete").eq(false).skip(3).top(3)
                    .execute(new TableQueryCallback<ToDoItem>() {

                        public void onCompleted(List<ToDoItem> result, int count,
                                Exception exception, ServiceFilterResponse response) {
                            if (exception == null) {
                                mAdapter.clear();

                                for (ToDoItem item : result) {
                                    mAdapter.add(item);
                                }

                            } else {
                                createAndShowDialog(exception, "Error");
                            }
                        }
                    });
        }

    查詢會略過前三個結果，並傳回接下來的三個結果。實際上這就是第二「頁」資料，頁面大小為三個項目。

    <div class="dev-callout"><b>注意</b>
<p>此教學課程使用的是將硬式編碼分頁值傳遞至 <strong>top</strong> 和 <strong>skip</strong> 方法的簡化案例。在實際的應用程式中，您可以搭配頁面巡覽區控制項或類似的 UI 來使用類似上述的查詢，讓使用者導覽至上一頁和下一頁。您也可以呼叫 <strong>includeInlineCount</strong> 方法，以取得伺服器上可用項目的總計數和分頁資料。</p>
</div>

8.  (選用) 檢視傳送至行動服務之要求的 URI。

    請注意，`skip(3)` 方法在查詢 URI 中會轉譯成查詢選項 `$skip=3`。

## <a name="next-steps"> </a>後續步驟

有關於在行動服務中使用資料的基本概念教學課程，在此告一段落。考慮更深入了解下列行動服務主題：

-   [開始使用驗證][開始使用驗證]
    了解如何使用 Windows 帳戶驗證您的應用程式的使用者。

-   [開始使用推播通知][開始使用推播通知]
    了解如何將極為基本的推播通知傳送到應用程式。

 

 



  [開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-android
  [開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-android
  [開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-android
