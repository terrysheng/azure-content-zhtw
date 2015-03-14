<properties 
	pageTitle="開始使用資料 (iOS) | 行動開發人員中心" 
	description="了解如何開始使用行動服務，在您的 iOS 應用程式中使用資料。" 
	services="" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# 將行動服務新增至現有的應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

本主題將示範如何使用 Azure 行動服務，進而運用 iOS 應用程式中的資料。在本教學課程中，您將下載應用程式，並在記憶體中儲存資料、建立新的行動服務、將行動服務與該應用程式整合，然後登入 Azure 管理入口網站查看執行應用程式時所做的資料變更。

> [AZURE.NOTE] 本教學課程旨在協助您深入了解如何透過行動服務，來使用 Azure 儲存並擷取 iOS 應用程式中的資料。因此，本主題將逐步說明已在行動服務快速入門中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮先完成[開始使用行動服務](/zh-tw/develop/mobile/tutorials/get-started-ios)教學課程。

本教學課程將逐步引導您完成下列基本步驟：

1. [下載 iOS 應用程式專案]
2. [建立行動服務]
3. [新增用於儲存的資料表]
4. [更新應用程式以使用行動服務]
5. [針對行動服務進行應用程式測試]

本教學課程需要下列項目：

+ [行動服務 iOS SDK] 和 [XCode 4.5][安裝 Xcode] 和 iOS 5.0 或更新版本。
+ Microsoft Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">Azure 免費試用</a>。</p></div>

##<a name="download-app"></a>下載 GetStartedWithData 專案

本教學課程以 [GetStartedWithData 應用程式][GitHub] (iOS 應用程式) 為基礎。除了會將新增項目儲存在本機記憶體中之外，由此應用程式與行動服務 iOS 快速入門所產生的應用程式兩者的 UI 完全相同。

1. 下載 GetStartedWithData [範例應用程式][GitHub]。

2. 在 Xcode 中，開啟下載的專案，並檢查 TodoService.m 檔案。

   	請注意，有 8 個 **// TODO** 註解指定讓此應用程式與行動服務搭配使用的必要步驟。

3. 按 [執行] ****按鈕 (或 Command+R 鍵)，以重建專案並啟動應用程式。

4. 在應用程式的文字方塊中鍵入一些文字，然後按一下 **+** 按鈕。

   	![][0]  

   	請注意，儲存的文件會顯示在清單下方。

##<a name="create-service"></a>在管理入口網站中建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>將新資料表新增至行動服務

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>更新應用程式以使用行動服務進行資料存取

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>對新的行動服務進行應用程式測試

1. 在 Xcode 中，選取要部署到 iPhone 或 iPad 上的模擬器，按 [執行]**** 按鈕 (或 Command+R 鍵)，以重建專案並啟動應用程式。

   	這會執行您的 Azure 行動服務用戶端，其使用 iOS SDK 建置，可從您的行動服務查詢項目。

2. 和之前一樣，在文字方塊中鍵入文字，然後按一下 **+** 按鈕。

   	這會傳送新項目以插入至行動服務。

3. 在[管理入口網站]中，按一下 [行動服務]****，然後按一下您的行動服務。

4. 按一下 [資料]**** 索引標籤，然後按一下 [瀏覽]****。

   	![][9]

   	請注意，**TodoItem** 表格現在包含資料，其中識別碼值由行動服務產生，且資料欄已自動新增到表格以符合應用程式中的 TodoItem 類別。

這將結束 iOS 的**開始使用資料**教學課程。

##<a name="next-steps"></a>後續步驟

本教學課程將示範啟用 iOS 應用程式來使用行動服務資料的基本概念。

接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

* [使用指令碼驗證及修改資料]
  <br/>深入了解在行動服務中使用伺服器指令碼，來驗證及變更從應用程式傳送出來的資料。

* [使用分頁縮小查詢範圍]
  <br/>了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料數列之後，請嘗試下列其他 iOS 教學課程：

* [開始使用驗證]
	<br/>了解如何驗證應用程式的使用者。

* [開始使用推播通知]
  <br/>了解如何使用行動服務，將非常基本的推播通知傳送至您的應用程式。

<!-- Anchors. -->
[下載 iOS 應用程式專案]: #download-app
[建立行動服務]: #create-service
[新增用於儲存的資料表]: #add-table
[更新應用程式以使用行動服務]: #update-app
[針對行動服務進行應用程式測試]: #test-app
[後續步驟]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png







[8]: ./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[使用指令碼驗證及修改資料]: /zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-ios
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-ios
[開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-ios
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-ios
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-ios

[Azure 管理入口網站]: https://manage.windowsazure.com/
[管理入口網站]: https://manage.windowsazure.com/
[安裝 Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[行動服務 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub 儲存機制]: http://go.microsoft.com/fwlink/p/?LinkId=268784


<!--HONumber=42-->
