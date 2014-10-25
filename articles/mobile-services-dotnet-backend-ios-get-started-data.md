<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# 開始使用行動服務中的資料

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Windows 市集 C#">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS" class="current">iOS</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android" class="current">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title=".NET 後端" class="current">.NET 後端</a> | <a href="/en-us/develop/mobile/tutorials/get-started-with-data-ios/"  title="JavaScript 後端">JavaScript 後端</a></div>

本主題將說明如何使用 Azure 行動服務，進而運用 iOS 應用程式中的資料。在本教學課程中，您將下載在記憶體中儲存資料的應用程式、建立新的行動服務、整合行動服務與該應用程式，然後檢視執行應用程式時所做的資料變更。

您將在本教學課程中建立的行動服務，將可在行動服務中支援 .NET 執行階段。如此，您即可在行動服務中使用 .NET 語言和 Visual Studio 來撰寫伺服器端商務邏輯。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [JavaScript 後端版本][]。

<div class="dev-callout"><b>注意</b>
<p>本教學課程需要 Visual Studio 2013。</p>
</div>

<div class="dev-callout"><b>注意</b>
<p>本教學課程旨在協助您深入了解如何透過行動服務，來使用 Azure 儲存並擷取 iOS 應用程式中的資料。因此，本主題將逐步說明已在行動服務快速入門中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮首先完成教學課程<a href="/en-us/develop/mobile/tutorials/get-started-ios">開始使用行動服務</a>。</p>
</div>

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 iOS 應用程式專案][]
2.  [建立行動服務][]
3.  [在本機下載服務][]
4.  [測試行動服務][]
5.  [將行動服務發佈至 Azure][]
6.  [更新應用程式以使用行動服務][]
7.  [針對行動服務進行應用程式測試][]

本教學課程需要下列各項：

-   [行動服務 iOS SDK][] 和 [XCode 4.5][] 及 iOS 5.0 或更新版本。
-   Visual Studio 2013 (您可以免費取得 [Visual Studio Express for Web][])。
-   Microsoft Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][]。

## <a name="download-app"></a>下載 GetStartedWithData 專案

本教學課程以 [GetStartedWithData 應用程式][] (iOS 應用程式) 為基礎而建立。除了會將新增項目儲存在本機記憶體中之外，由此應用程式與行動服務 iOS 快速入門所產生的應用程式兩者的 UI 完全相同。

1.  下載 GetStartedWithData [範例應用程式][GetStartedWithData 應用程式]。

2.  在 Xcode 中，開啟已下載的專案，並檢查 TodoService.m 檔案。

    請注意，有八個 **// TODO** 註解會指定讓此應用程式可與您的行動服務搭配使用的必要步驟。

3.  按 [執行] 按鈕 (或 Command+R 鍵)，以重建專案並啟動應用程式。

4.  在應用程式的文字方塊中鍵入一些文字，然後按一下 **+** 按鈕。

    ![][]

    Notice that the saved text is displayed in the list below.

## <a name="create-service"></a>在管理入口網站中建立新的行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][]]

## <a name="download-the-service-locally"></a>將服務下載至您的本機電腦

[WACOM.INCLUDE [mobile-services-ios-download-service-locally][]]

## <a name="test-the-service"></a>測試行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][]]

## <a name="publish-mobile-service"></a>將行動服務發行至 Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][]]

## <a name="update-app"></a>更新應用程式以使用行動服務進行資料存取

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access][]]

## <a name="test-app"></a>對新的行動服務進行應用程式測試

1.  在 Xcode 中，選取要部署到 iPhone 或 iPad 上的模擬器，按 [執行] 按鈕 (或 Command+R 鍵)，以重建專案並啟動應用程式。

    這將執行您的 Azure 行動服務用戶端；它使用 iOS SDK 建置，可從您的行動服務查詢項目。

2.  和之前一樣，在文字方塊中鍵入文字，然後按一下 **+** 按鈕。

    這會傳送新項目以插入至行動服務。每個新的 todoItem，都會儲存在您先前在 Azure 管理入口網站中為行動服務設定的 SQL Database 中，並進行更新。

3.  停止並重新啟動應用程式，以確認變更持續存留至 Azure 中的資料庫。

    您也可以使用 Azure 管理入口網站或 Visual Studio 的 SQL Server 物件總管來檢查資料庫。後續的兩個步驟將使用 [Azure 管理入口網站][]檢視您資料庫中的變更。

4.  在 Azure 管理入口網站中，對您行動服務的相關資料庫按一下 [管理]。

    ![][1]

5.  在管理入口網站中執行查詢，以檢視應用程式所做的變更。您的查詢會與下列查詢相類似，但將使用您的資料庫名稱，而非 `todolist`。

        SELECT * FROM [todolist].[todoitems]

    ![][2]

這將結束**開始使用資料**教學課程。

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

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows 市集 C\#]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ "Windows 市集 C#"
  [Windows 市集 JavaScript]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ "Windows 市集 JavaScript"
  [Windows Phone]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/ "Windows Phone"
  [iOS]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ "iOS"
  [Android]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/ "Android"
  [.NET 後端]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ ".NET 後端"
  [JavaScript 後端]: /en-us/develop/mobile/tutorials/get-started-with-data-ios/ "JavaScript 後端"
  [JavaScript 後端版本]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
  [開始使用行動服務]: /en-us/develop/mobile/tutorials/get-started-ios
  [下載 iOS 應用程式專案]: #download-app
  [建立行動服務]: #create-service
  [在本機下載服務]: #download-the-service-locally
  [測試行動服務]: #test-the-service
  [將行動服務發佈至 Azure]: #publish-mobile-service
  [更新應用程式以使用行動服務]: #update-app
  [針對行動服務進行應用程式測試]: #test-app
  [行動服務 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Visual Studio Express for Web]: http://go.microsoft.com/p/?linkid=9832232
  [Azure 免費試用]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F
  [GetStartedWithData 應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=268622
  []: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [mobile-services-ios-download-service-locally]: ../includes/mobile-services-ios-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-ios-enable-mobile-service-access]: ../includes/mobile-services-ios-enable-mobile-service-access.md
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
  [2]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png
  [使用指令檔驗證與修改資料]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [使用分頁縮小查詢範圍]: /en-us/develop/mobile/tutorials/add-paging-to-data-ios
  [開始使用驗證]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
  [開始使用推送通知]: /en-us/develop/mobile/tutorials/get-started-with-push-ios
