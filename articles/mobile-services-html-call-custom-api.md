<properties linkid="mobile-services-html-call-custom-api" urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from an HTML client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from an HTML app that uses Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="jparrel" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 從 HTML 應用程式呼叫自訂 API

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Windows 市集 C#">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/zh-tw/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/zh-tw/documentation/articles/mobile-services-ios-call-custom-api" title="iOS" >iOS</a><a href="/zh-tw/documentation/articles/mobile-services-android-call-custom-api" title="Android">Android</a><a href="/zh-tw/documentation/articles/mobile-services-html-call-custom-api" title="HTML" class="current">HTML</a></div>

本主題說明如何從 HTML 應用程式呼叫自訂 API。自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

本主題中所建立的自訂 API 可提供您傳送單一 POST 要求，並將資料表中所有 todo 項目的已完成旗標設定為`true` 。若沒有此自訂 API，用戶端必須傳送個別要求，來為資料表中的每個 todo 項目更新旗標。

將此功能新增至您在完成[開始使用行動服務][開始使用行動服務]或[開始使用資料][開始使用資料]教學課程時所建立的應用程式。若要達到此目的，您將需要完成下列步驟：

1.  [定義自訂 API][定義自訂 API]
2.  [更新應用程式以呼叫自訂 API][更新應用程式以呼叫自訂 API]
3.  [測試應用程式][測試應用程式]

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務][開始使用行動服務]或[開始使用資料][開始使用資料]。

## <a name="define-custom-api"></a>定義自訂 API

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

## <a name="update-app"></a><span class="short-header">更新應用程式 </span>更新應用程式以呼叫自訂 API

1.  使用您的文字編輯器開啟 index.html 檔案、找出 [按鈕] 元素`buttonRefresh`，然後在其後面新增下列新元素：

        <button id="buttonCompleteAll">Complete All</button> 

    這會將新按鈕新增至頁面。

2.  在 page.js 中的 **refreshTodoItems** 函數後面新增下列程式碼：

        var completeAllTodoItems = function () {
            // Asynchronously call the custom API using the POST method.
            client.invokeApi("completeall", {
                body: null,
                method: "post"
            }).done(function (results) {
                var message = results.result.count + " item(s) marked as complete.";
                alert(message);
                refreshTodoItems();
            }, function(error) {
                alert(error.message);
            });
        };

        $('#buttonCompleteAll').click(function () {
            completeAllTodoItems();
        });

    此方法會處理新按鈕的 **Click** 事件。**invokeApi** 方法是在用戶端上呼叫，可將 POST 要求傳送給新的自訂 API。如有任何錯誤，自訂 API 傳回的結果會顯示在訊息對話方塊中。

## <a name="test-app"></a>測試應用程式

1.  重新整理您的瀏覽器。

2.  在應用程式的 [Insert a TodoItem] 中鍵入一些文字，然後按一下 [儲存]。

3.  重複前一個步驟，直到將數個 Todo 項目新增至清單為止。

4.  按一下 [Complete All] 按鈕。</cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">出現訊息對話方塊，指出標示為完成的項目數，並重新執行篩選</cf><cf font="Microsoft JhengHei" complexscriptsfont="Microsoft JhengHei" asiantextfont="Microsoft JhengHei" fontcolor="000000">查詢，以便清除清單的所有項目。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

## 後續步驟

您已建立自訂 API，並從您的 HTML 應用程式呼叫此 API，建議您搜尋有關下列行動服務主題的更多資訊：

-   [行動服務伺服器指令碼參照][行動服務伺服器指令碼參照]  
     (英文) 深入了解建立自訂 API。



  [開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-html-get-started
  [開始使用資料]: /zh-tw/documentation/articles/mobile-services-html-get-started-data
  [定義自訂 API]: #define-custom-api
  [更新應用程式以呼叫自訂 API]: #update-app
  [測試應用程式]: #test-app
  [行動服務伺服器指令碼參照]: http://go.microsoft.com/fwlink/?LinkId=262293
