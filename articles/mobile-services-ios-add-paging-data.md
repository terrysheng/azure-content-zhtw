<properties linkid="develop-mobile-tutorials-add-paging-to-data-ios" urlDisplayName="Add paging to data" pageTitle="Add paging to data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your iOS app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# 使用分頁縮小行動服務查詢範圍

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows 市集 C#">Windows 市集 C#</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-js" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS" class="current">iOS</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-android" title="Android" class="current">Android</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

此主題將說明如何使用分頁，管理從 Azure 行動服務傳回至 iOS 應用程式的資料量。在此教學課程中，您將在用戶端上使用 **fetchLimit** 和 **fetchOffset** 查詢屬性要求特定的資料「頁面」。

<div class="dev-callout"><b>注意</b>
<p>為防止行動裝置用戶端出現資料溢位的狀況，行動服務會實作自動分頁限制，而將回應中的項目數上限預設為 50 個。您可以指定頁面大小，以明確要求在回應中傳回最多 1,000 個項目。</p>
</div>

本教學課程會以上一堂教學課程[開始使用資料][]中的步驟和範例應用程式為基礎。開始進行此教學課程之前，您至少必須先完成資料序列中的第一個教學課程[開始使用資料][]。

1.  在 Xcode 中，開啟您完成教學課程[開始使用資料][]時所修改的專案。

2.  按 [執行] 按鈕 (Command + R) 以建置專案並啟動應用程式，在文字方塊中輸入文字，然後按一下加號 (**+**) 圖示。

3.  重複前述步驟至少三次，以在 TodoItem 資料表中儲存三個以上的項目。

4.  開啟 QSTodoService.m 檔案，並找出下列方法：

        - (void)refreshDataOnSuccess:(QSCompletionBlock)completion

    Replace the body of the entire method with the following code.

        // Create a predicate that finds active items in which complete is false
        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

        // Retrieve the MSTable's MSQuery instance with the predicate you just created.
        MSQuery * query = [self.table queryWithPredicate:predicate];

        query.includeTotalCount = YES; // Request the total item count

        // Start with the first item, and retrieve only three items
        query.fetchOffset = 0;
        query.fetchLimit = 3;

        // Invoke the MSQuery instance directly, rather than using the MSTable helper methods.
        [query readWithCompletion:^(NSArray *results, NSInteger totalCount, NSError *error) {

            [self logErrorIfNotNil:error];
            if (!error)
            {
                // Log total count.
                NSLog(@"Total item count: %@",[NSString stringWithFormat:@"%zd", (ssize_t) totalCount]);            
            }

            items = [results mutableCopy];

            // Let the caller know that we finished
            completion();
        }];

    此查詢會傳回並未標記為已完成的前三個項目。

5.  重新建置並啟動應用程式。

    請注意，TodoItem 資料表中只有前三個結果會顯示出來。

6.  找出下列程式碼行，再次以下列程式碼更新 **refreshDataOnSuccess** 方法：

        query.fetchOffset = 0;

    這次請將 **query.fetchOffset** 值設為 3。

    此查詢會略過前三個結果，並傳回接下來的三個結果。實際上這就是第二「頁」資料，頁面大小為三個項目。

    <div class="dev-callout"><b>注意</b>
<p>此教學課程使用的是為 <strong>fetchOffset</strong> 和 <strong>fetchLimit</strong> 屬性設定硬式編碼分頁值的簡化案例。在實際的應用程式中，您可以搭配頁面巡覽區控制項或類似的 UI 來使用類似上述的查詢，讓使用者導覽至上一頁和下一頁。您也可以設定 **query.includeTotalCount = YES**，以取得伺服器上所有可用項目的總計數和分頁資料。</p>
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

  [Windows 市集 C#]: /en-us/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows 市集 C#"
  [Windows 市集 JavaScript]: /en-us/develop/mobile/tutorials/add-paging-to-data-js "Windows 市集 JavaScript"
  [Windows Phone]: /en-us/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone"
  [iOS]: /en-us/develop/mobile/tutorials/add-paging-to-data-ios "iOS"
  [Android]: /en-us/develop/mobile/tutorials/add-paging-to-data-android "Android"
  [HTML]: /en-us/develop/mobile/tutorials/add-paging-to-data-html "HTML"
  [Xamarin.iOS]: /en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android"
  [開始使用資料]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
  [開始使用驗證]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
