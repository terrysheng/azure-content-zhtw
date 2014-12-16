<properties urlDisplayName="Call a custom API from the client" pageTitle="從 HTML 用戶端呼叫自訂 API - 行動服務" metaKeywords="" description="Learn how to define a custom API and then call it from an HTML app that uses Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="jparrel" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# 從 HTML 應用程式呼叫自訂 API

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

本主題說明如何從 HTML 應用程式呼叫自訂 API。自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

本主題中所建立的自訂 API 可提供您傳送單一 POST 要求，並將資料表中所有 todo 項目的已完成旗標設定為 `true`。若沒有此自訂 API，用戶端必須傳送個別要求，以更新資料表中每個 todo 項目的旗標。

將此功能新增至您在完成[開始使用行動服務]或[開始使用資料]教學課程時所建立的應用程式。若要達到此目的，您將需要完成下列步驟：

1. [定義自訂 API]
2. [更新應用程式以呼叫自訂 API]
3. [測試應用程式] 

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務]或[開始使用資料]。

## <a name="define-custom-api"></a>定義自訂 API

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

<h2><a name="update-app"></a>更新應用程式以呼叫自訂 API</h2>

1. 使用您的文字編輯器開啟 index.html 檔案，找出名為 `buttonRefresh` 的 **button** 元素，然後在後面新增下列新元素： 

		<button id="buttonCompleteAll">Complete All</button> 

	這會將新按鈕新增至頁面。 

2. 在 page.js 中的 **refreshTodoItems** 函數後面新增下列程式碼：

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

1. 重新整理您的瀏覽器。

2. 在應用程式的 [**Insert a TodoItem**] 中輸入一些文字，然後按一下 [**儲存**]。

3. 重複前一個步驟，直到將數個 Todo 項目新增至清單為止。

4. 按一下 [**全部完成**] 按鈕。這時會顯示訊息對話方塊，指出標示為已完成的項目數，且會再次篩選查詢來清除清單中的所有項目。

## 後續步驟

您已建立自訂 API，並從您的 HTML 應用程式呼叫此 API，建議您搜尋有關下列行動服務主題的更多資訊：

* [行動服務伺服器指令碼參考]
  <br/>進一步了解如何建立自訂 API。

<!-- Anchors. -->
[定義自訂 API]: #define-custom-api
[更新應用程式以呼叫自訂 API]: #update-app
[測試應用程式]: #test-app
[後續步驟]: #next-steps

<!-- URLs. -->
[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
[我的應用程式儀表板]: http://go.microsoft.com/fwlink/?LinkId=262039
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-html-get-started
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-html-get-started-data
