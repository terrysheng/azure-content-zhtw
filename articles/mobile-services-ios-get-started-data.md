<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/19/2014" ms.author="glenga"></tags>

# 開始使用行動服務中的資料

[WACOM.INCLUDE [mobile-services-selector-get-started-data][]]

本主題將示範如何使用 Azure 行動服務，進而運用 iOS 應用程式中的資料。在本教學課程中，您將下載應用程式，並在記憶體中儲存資料、建立新的行動服務、將行動服務與該應用程式整合，然後登入 Azure 管理入口網站查看執行應用程式時所做的資料變更。

<div class="dev-callout"><b>注意</b>
<p>本教學課程旨在協助您深入了解如何透過行動服務，來使用 Azure 儲存並擷取 iOS 應用程式中的資料。因此，本主題將逐步說明已在行動服務快速入門中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮首先完成教學課程<a href="/zh-tw/develop/mobile/tutorials/get-started-ios">開始使用行動服務</a>。</p>
</div>

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 iOS 應用程式專案][]
2.  [建立行動服務][]
3.  [新增用於儲存的資料表][]
4.  [更新應用程式以使用行動服務][]
5.  [針對行動服務進行應用程式測試][]

本教學課程需要下列各項：

-   [行動服務 iOS SDK][] 和 [XCode 4.5][] 以及 iOS 5.0 或更新版本。
-   Microsoft Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][]。
    </p>
    </div>

## <a name="download-app"></a>下載 GetStartedWithData 專案

本教學課程以 [GetStartedWithData 應用程式][] (iOS 應用程式) 為基礎而建立。除了會將新增項目儲存在本機記憶體中之外，由此應用程式與行動服務 iOS 快速入門所產生的應用程式兩者的 UI 完全相同。

1.  下載 GetStartedWithData [範例應用程式][GetStartedWithData 應用程式]。

2.  在 Xcode 中，開啟下載的專案，並檢查 TodoService.m 檔案。

    請注意，有 8 個 **// TODO** 註解指定讓此應用程式與行動服務搭配使用的必要步驟。

3.  按 [執行] 按鈕 (或 Command+R 鍵)，以重建專案並啟動應用程式。

4.  在應用程式的文字方塊中鍵入一些文字，然後按一下 **+** 按鈕。

    ![][]

    請注意，儲存的文件會顯示在清單下方。

## <a name="create-service"></a>在管理入口網站中建立新的行動服務

[WACOM.INCLUDE [mobile-services-create-new-service-data][]]

## <a name="add-table"></a>新增資料表至行動服務

[WACOM.INCLUDE [mobile-services-create-new-service-data-2][]]

## <a name="update-app"></a>更新應用程式以使用行動服務進行資料存取

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access][]]

## <a name="test-app"></a>對新的行動服務進行應用程式測試

1.  在 Xcode 中，選取要部署到 iPhone 或 iPad 上的模擬器，按 [執行] 按鈕 (或 Command+R 鍵)，以重建專案並啟動應用程式。

    這會執行您的 Azure 行動服務用戶端，其使用 iOS SDK 建置，可從您的行動服務查詢項目。

2.  和之前一樣，在文字方塊中鍵入文字，然後按一下 **+** 按鈕。

    這會傳送新項目以插入至行動服務。

3.  在[管理入口網站][]中按一下 [行動服務]，然後按一下您的行動服務。

4.  按一下 [資料] 索引標籤，然後按一下 [瀏覽]。

    ![][1]

    請注意，[TodoItem] 資料表現在包含資料和行動服務產生的識別碼值，且資料行已自動新增至資料表，以符合應用程式中的 TodoItem 類別。

這將結束 iOS 的**開始使用資料**教學課程。

## <a name="next-steps"></a>後續步驟

本教學課程將示範啟用 iOS 應用程式來使用行動服務資料的基本概念。

接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

-   [使用指令檔驗證與修改資料][]
    深入了解在行動服務中使用伺服器指令檔，來驗證並變更從應用程式傳送出來的資料。

-   [使用分頁縮小查詢範圍][]
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料數列之後，請嘗試下列其他 iOS 教學課程：

-   [開始使用驗證][]
    了解如何驗證應用程式的使用者。

-   [開始使用推送通知][]
    了解如何使用行動服務將非常基本的推送通知傳送到應用程式。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-ios
  [下載 iOS 應用程式專案]: #download-app
  [建立行動服務]: #create-service
  [新增用於儲存的資料表]: #add-table
  [更新應用程式以使用行動服務]: #update-app
  [針對行動服務進行應用程式測試]: #test-app
  [行動服務 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Azure 免費試用]: http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F
  [GetStartedWithData 應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=268622
  []: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [mobile-services-ios-enable-mobile-service-access]: ../includes/mobile-services-ios-enable-mobile-service-access.md
  [管理入口網站]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png
  [使用指令檔驗證與修改資料]: /zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-ios
  [開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-ios
  [開始使用推送通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-ios
