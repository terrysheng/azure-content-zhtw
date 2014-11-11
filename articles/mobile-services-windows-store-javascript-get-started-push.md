<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (legacy push) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store JavaScript app (legacy push)." metaCanonical="http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 開始在行動服務中使用推播通知 (舊版為推播)

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows 市集 C#">Windows 市集 C#</a>
<a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows 市集 JavaScript" class="current">Windows 市集 JavaScript</a>
<a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
<a href="/zh-tw/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
<a href="/zh-tw/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
<a href="/zh-tw/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title=".NET 後端">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-push/"  title="JavaScript 後端" class="current">JavaScript 後端</a></div>

本主題說明 Visual Studio 2013 如何讓您使用 Azure 行動服務將推播通知傳送至 Windows 市集應用程式。在本教學課程中，您會使用 Windows 推播通知服務 (WNS)，將推播通知從 Visual Studio 新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。

> [WACOM.NOTE]此主題支援「尚未升級」來使用通知中心整合的「現有」行動服務。當您建立「新的」行動服務時，就會自動啟用這項整合功能。如需新的行動服務，請參閱[開始使用推播通知][開始使用推播通知]。
>
> 行動服務已與 Azure 通知中心整合，以支援其他推播通知功能，例如範本、多個平台和提升的範圍。*您應該升級現有的行動服務，以盡可能使用通知中樞*。升級之後，請參閱這一版的[開始使用推播通知][開始使用推播通知]。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1.  [針對推播通知註冊您的應用程式及設定行動服務][針對推播通知註冊您的應用程式及設定行動服務]
2.  [更新產生的推播通知程式碼][更新產生的推播通知程式碼]
3.  [插入資料以接收通知][插入資料以接收通知]

本教學課程會以行動服務快速入門為基礎。開始本教學課程之前，您必須先完成[開始使用行動服務][開始使用行動服務]或[開始使用資料][開始使用資料]，將您的專案連接到行動服務。若未連接行動服務，[新增推播通知] 精靈會為您建立此連線。

## <a name="register"></a><span class="short-header">註冊您的應用程式</span>在應用程式中新增和設定推播通知

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  開啟產生的 push.register.js 程式碼檔，然後檢查程式碼，該程式碼可取得裝置的安裝 ID 和通道，並將這些資料插入新的 **channels** 資料表中。

    此資料表是由 [加入推播通知] 精靈在您的行動服務中建立的。此程式碼可確保在每次啟動應用程式時都會嘗試註冊裝置。

2.  在 [伺服器總管] 中，展開 [Azure]、[行動服務]、您的服務名稱及 [通道]，然後開啟 insert.js 檔案。

    這個檔案 (存放在您的行動服務中) 包含 JavaScript 程式碼，而該程式碼會在用戶端將資料插入通道資料表中以傳送註冊裝置的要求時執行。

    <div class="dev-callout"><b>注意</b>
<p>此檔案的初版包含可檢查裝置之現有註冊的程式碼。此外，還包含可在新的註冊加入通道資料表時傳送推播通知的程式碼。傳送推播通知的程式碼可以包含在任何已註冊的指令碼檔案中。此指令碼的位置取決於通知的觸發方式。您可以針對資料表的插入、更新、刪除或讀取作業註冊指令碼；作為排程的工作；或作為自訂 API。如需詳細資訊，請參閱<a href="http://go.microsoft.com/fwlink/p/?LinkID=287178">在行動服務中使用伺服器指令碼</a>。</p>
</div>

3.  按 F5 鍵以執行應用程式並確認立即從行動服務接收通知。

    此通知是經由將資料列插入新通道資料表中而產生的，這就是裝置註冊。

雖然產生的程式碼能輕鬆地在應用程式執行時示範通知，但這通常不是有意義的案例。接下來，您會從通道資料表移除通知程式碼，而在 TodoItem 資料表中以某些變更取代。

## <a name="update-scripts"></a><span class="short-header">更新程式碼</span>更新所產生的推播通知程式碼

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

## <a name="test"></a><span class="short-header">測試應用程式</span>在您的應用程式中測試推播通知

1.  在 Visual Studio 中，按 F5 鍵以執行此應用程式。

2.  在應用程式的 [Insert a TodoItem] 中輸入文字，然後按一下 [儲存]。

    ![][0]

    注意插入完成之後，應用程式會收到來自 WNS 的推播通知。

    ![][1]

## <a name="next-steps"> </a>後續步驟

本教學課程將示範行動服務所提供的基本推播通知功能。如果您的應用程式需要更進階的功能，例如傳送跨平台通知、訂閱型路由或是極大的磁碟區，請考慮在您的行動服務中使用 Azure 通知中心。如需詳細資訊，請參閱下列通知中心主題：

-   [開始使用通知中心][開始使用通知中心]
    了解如何在 Windows 市集應用程式中利用通知中心。

-   [將通知傳送給訂閱者][將通知傳送給訂閱者]
    了解使用者如何註冊及接收其所需類別的推播通知。

-   [將通知傳送給使用者][將通知傳送給使用者]
    了解如何將推播通知從行動服務傳送給任何裝置上的特定使用者。

-   [將交叉平台通知傳送給使用者][將交叉平台通知傳送給使用者]
    了解如何使用範本從行動服務傳送推播通知，但不必在您的後端製作平台特定裝載。

考慮更深入了解下列行動服務主題：

-   [開始使用資料][開始使用資料]
    深入了解使用行動服務來儲存與查詢資料。

-   [開始使用驗證][開始使用驗證]
    了解如何使用 Windows 帳戶驗證您的應用程式的使用者。

-   [行動服務伺服器指令碼參考][行動服務伺服器指令碼參考]
    深入了解如何註冊和使用伺服器指令碼。

-   [行動服務 HTML/JavaScript 作法概念參考][行動服務 HTML/JavaScript 作法概念參考]
    深入了解如何將行動服務搭配 HTML 及 JavaScript 使用。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/
  [針對推播通知註冊您的應用程式及設定行動服務]: #register
  [更新產生的推播通知程式碼]: #update-scripts
  [插入資料以接收通知]: #test
  [開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started/
  [開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-js/
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [mobile-services-create-new-push-vs2013-2]: ../includes/mobile-services-create-new-push-vs2013-2.md
  [0]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
  [1]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png
  [開始使用通知中心]: /zh-tw/manage/services/notification-hubs/getting-started-windows-dotnet/
  [將通知傳送給訂閱者]: /zh-tw/manage/services/notification-hubs/breaking-news-dotnet/
  [將通知傳送給使用者]: /zh-tw/manage/services/notification-hubs/notify-users/
  [將交叉平台通知傳送給使用者]: /zh-tw/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-js
  [行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
  [行動服務 HTML/JavaScript 作法概念參考]: /zh-tw/develop/mobile/how-to-guides/work-with-html-js-client/
