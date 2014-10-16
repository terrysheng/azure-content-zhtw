<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

開始在行動服務中使用推播通知
============================

[Windows 市集 C\#](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows 市集 C#")[Windows 市集 JavaScript](/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows 市集 JavaScript")[Windows Phone](/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone")[iOS](/zh-tw/documentation/articles/mobile-services-ios-get-started-push "iOS")[Android](/zh-tw/documentation/articles/mobile-services-android-get-started-push "Android")[Xamarin.iOS](/zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push "Xamarin.iOS")[Xamarin.Android](/zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started-push "Xamarin.Android")

[.NET 後端](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ ".NET 後端") | [JavaScript 後端](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/ "JavaScript 後端")

本主題說明 Visual Studio 2013 如何讓您使用 Azure 行動服務將推播通知傳送至 Windows 市集應用程式。在本教學課程中，您會使用 Windows 推播通知服務 (WNS)，將推播通知從 Visual Studio 新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。

> [WACOM.NOTE]行動服務現在與 Azure 通知中心整合，以支援其他推播通知功能，例如範本、多個平台和範圍。這項整合後的功能目前只能預覽。如需詳細資訊，請參閱這一版的[開始使用推播通知](/zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/)。

本教學課程會逐步引導您完成啟用推播通知的基本步驟：

1.  [針對推播通知註冊您的應用程式及設定行動服務](#register)
2.  [更新產生的推播通知程式碼](#update-scripts)
3.  [插入資料以接收通知](#test)

本教學課程是以行動服務快速入門為基礎。開始本教學課程之前，您必須先完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started/)或[開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/)，將您的專案連接到行動服務。若未連接行動服務，[新增推播通知] 精靈會為您建立此連線。

註冊您的應用程式在應用程式中新增和設定推播通知
----------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  展開 **[服務]**、**[行動服務]**、您的服務名稱，開啟所產生的程式碼檔，然後檢查 **UploadChannel** 方法，該方法可取得裝置的安裝 ID 和通道並將此資料插入新的通道資料表中。

    精靈也會將對此方法的呼叫新增至 App.xaml.cs 程式碼檔中的 **OnLaunched** 事件處理常式。這可確保在每次啟動應用程式時嘗試註冊裝置。

2.  在 [伺服器總管] 中，展開 **[Azure]**、**[行動服務]**、您的服務名稱及 **[通道]**，然後開啟 insert.js 檔案。

    這個檔案 (存放在您的行動服務中) 包含 JavaScript 程式碼，而該程式碼會在用戶端將資料插入通道資料表中以傳送註冊裝置的要求時執行。

    **注意**

    此檔案的初版包含可檢查裝置之現有註冊的程式碼。此外，還包含可在新的註冊加入至通道資料表時傳送推播通知的程式碼。傳送推播通知的程式碼可以包含在任何已註冊的指令碼檔案中。此指令碼的位置取決於通知的觸發方式。您可以針對資料表的插入、更新、刪除或讀取作業註冊指令碼；作為排程的工作；或作為自訂 API。如需詳細資訊，請參閱[在行動服務中使用伺服器指令碼](http://go.microsoft.com/fwlink/p/?LinkID=287178)。

3.  按 F5 鍵以執行應用程式並確認立即從行動服務接收通知。

    此通知是經由將資料列插入新通道資料表中而產生的，這就是裝置註冊。

雖然產生的程式碼能輕鬆地在應用程式執行時示範通知，但這通常不是有意義的案例。接下來，您會從通道資料表移除通知程式碼，而在 TodoItem 資料表中以某些變更取代。

更新程式碼更新所產生的推播通知程式碼
------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

測試應用程式在您的應用程式中測試推播通知
----------------------------------------

1.  在 Visual Studio 中，按 F5 鍵以執行應用程式。

2.  在應用程式的 **[Insert a TodoItem]** 中輸入文字，然後按一下 **[儲存]**。

	![](./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png)

   請注意，在插入完成後，應用程式會收到來自 WNS 的推播通知。

    ![](./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png)

後續步驟
--------

本教學課程示範讓 Windows 市集應用程式能夠在行動服務中處理資料的基本概念。接著，考慮完成下列其中一個以您在此教學課程中建立的 GetStartedWithData 應用程式為基礎的教學課程。

-   [開始使用通知中心](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    了解如何在 Windows 市集應用程式中利用通知中心。

-   [將通知傳送給訂閱者](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    了解使用者如何註冊及接收其所需類別的推播通知。

-   [將通知傳送給使用者](/en-us/manage/services/notification-hubs/notify-users/)
    了解如何將推播通知從行動服務傳送給任何裝置上的特定使用者。

-   [將交叉平台通知傳送給使用者](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    了解如何使用範本從行動服務傳送推播通知，但不必在您的後端製作平台特定裝載。

考慮更深入了解下列行動服務主題：

-   [開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/)
    深入了解如何使用行動服務儲存和查詢資料。

-   [開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    了解如何使用 Windows 帳戶驗證您的應用程式的使用者。

-   [行動服務伺服器指令碼參考](http://go.microsoft.com/fwlink/?LinkId=262293)
    深入了解如何註冊和使用伺服器指令碼。

-   [行動服務 .NET 作法概念性參考](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/)
    深入了解如何搭配使用行動服務與 .NET。


