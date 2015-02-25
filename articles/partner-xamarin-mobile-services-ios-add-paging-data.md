<properties 
	pageTitle="在資料中加入分頁 (Xamarin iOS) | 行動開發人員中心" 
	description="了解如何使用分頁來管理行動服務傳回您 Xamarin iOS 應用程式的資料量。" 
	services="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	documentationCenter="xamarin"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="donnam"/>


# 使用分頁縮小行動服務查詢範圍

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

此主題將說明如何使用分頁，管理從 Azure 行動服務傳回至 iOS 應用程式的資料量。在此教學課程中，您將在用戶端上使用 **Skip** 和 **Take** 查詢屬性要求特定的資料「頁面」。

> [AZURE.NOTE] 為防止行動裝置用戶端出現資料溢位的狀況，行動服務會實作自動分頁限制，而將回應中的項目數上限預設為 50 個。您可以指定頁面大小，以明確要求在回應中傳回最多 1,000 個項目。

本教學課程會以上一堂教學課程[開始使用資料]中的步驟和範例應用程式為基礎。開始進行此教學課程之前，您至少必須先完成資料序列中的第一個教學課程[開始使用資料]。 

1. 在 Xamarin Studio 中，開啟您完成[開始使用資料]教學課程時所修改的專案。

2. 按 **[執行]** 按鈕以建置專案並啟動應用程式，在文字方塊中輸入文字，然後按一下加號 (**+**) 圖示。

3. 重複前述步驟至少三次，以在 TodoItem 資料表中儲存三個以上的項目。 

4. 開啟 **TodoService.cs** 檔案，找出 **RefreshDataAsync** 方法，然後以下列查詢取代 <code>todoTable</code> 上的 LINQ 查詢： 

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
        		            .Take(3)
                		    .ToListAsync();

   	此查詢會傳回前三個未標示為已完成的項目。 

5. 重新建置並啟動應用程式。 
   
    請注意，TodoItem 資料表中只有前三個結果會顯示出來。 

7. 再次將 **RefreshDataAsync** 方法中的 LINQ 查詢更新為下列內容：

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
		                    .Skip(3)
		                    .Take(3)
		                    .ToListAsync();

   	這次，將 **Skip** 值設為 3。 

   	此查詢會忽略前三個結果，並傳回接下來的三個結果。實際上這就是第二「頁」資料，頁面大小為三個項目。

    > [AZURE.NOTE] 此教學課程使用的是為 **Skip** 和 **Take** 屬性設定硬式編碼分頁值的簡化案例。在實際的應用程式中，您可以搭配頁面巡覽區控制項或類似的 UI 來使用類似上述的查詢，讓使用者瀏覽至上一頁和下一頁。您也可以呼叫 **IncludeTotalCount** 方法，以取得伺服器上所有可用項目的總計數和分頁資料。

## <a name="next-steps"> </a>後續步驟

有關於在行動服務中使用資料的基本概念教學課程，在此告一段落。建議您深入了解下列行動服務主題：

* [開始使用驗證]
  <br/>了解如何使用 Windows 帳戶驗證您的應用程式的使用者。
 
<!--
* [開始使用推播通知] 
  <br/>了解如何將極為基本的推播通知傳送到應用程式。
-->

<!-- Anchors. -->

[後續步驟]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[開始使用行動服務]: ./zh-tw/develop/mobile/tutorials/get-started-xamarin-ios
[開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[管理入口網站]: https://manage.windowsazure.com/


<!--HONumber=42-->
