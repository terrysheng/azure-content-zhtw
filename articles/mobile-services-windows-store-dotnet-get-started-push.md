<properties pageTitle="開始使用推播通知 (Windows 市集) | 行動開發人員中心" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga" />

# 將推播通知新增至行動服務應用程式 (舊版推播)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" class="current">Windows 市集 C#</a>
    <a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows 市集 JavaScript</a>
    <a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/zh-tw/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/zh-tw/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/zh-tw/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title=".NET backend">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/"  title="JavaScript backend" class="current">JavaScript 後端</a></div>	

本主題說明 Visual Studio 2013 如何讓您使用 Azure 行動服務將推播通知傳送至 Windows 市集應用程式。在本教學課程中，您會使用 Windows 推播通知服務 (WNS)，將推播通知從 Visual Studio 新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。

>[WACOM.NOTE]本主題支援<em>尚未升級</em>到使用通知中樞整合的<em>現有</em>行動服務。當您建立「<em>新的</em>」行動服務時，會自動啟用這項整合功能。關於新的行動服務，請參閱[開始使用推播通知](/zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/)。
>
>行動服務與 Azure 通知中樞整合，以支援其他推播通知功能，例如範本、多個平台和改善的規模。<em>您應該升級現有的行動服務，以盡可能使用通知中樞</em>。升級後，請參閱這一版的[開始使用推播通知](/zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/)。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [針對推播通知註冊您的應用程式及設定行動服務]
2. [更新產生的推播通知程式碼]
3. [插入資料以接收通知]

本教學課程會以行動服務快速入門為基礎。開始本教學課程之前，您必須先完成[開始使用行動服務]或[開始使用資料]，將您的專案連接到行動服務。若未連接行動服務，[新增推播通知] 精靈會為您建立此連線。 

<h2><a name="register"></a>在應用程式中新增和設定推播通知</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>展開 [<strong>服務</strong>]、[<strong>行動服務</strong>]、您的服務名稱，開啟所產生的程式碼檔，然後檢查 <strong>UploadChannel</strong> 方法，該方法可取得裝置的安裝 ID 和通道，並將此資料插入新的通道資料表中。</p> 

<p>精靈也會將對此方法的呼叫新增至 App.xaml.cs 程式碼檔中的 <strong>OnLaunched</strong> 事件處理常式。這可確保在每次啟動應用程式時嘗試註冊裝置。</p></li> 
<li><p>在 [伺服器總管] 中，展開 [<strong>Azure</strong>]、[<strong>行動服務</strong>]、您的服務名稱和 [<strong>通道</strong>]，然後開啟 insert.js 檔案。</p> 

<p>這個檔案 (存放在您的行動服務中) 包含 JavaScript 程式碼，而該程式碼會在用戶端將資料插入通道資料表中以傳送註冊裝置的要求時執行。</p> 

<div class="dev-callout"><b>注意</b>
	<p>此檔案的初版包含可檢查裝置之現有註冊的程式碼。此外，還包含可在新的註冊加入通道資料表時傳送推播通知的程式碼。傳送推播通知的程式碼可以包含在任何已註冊的指令碼檔案中。此指令碼的位置取決於通知的觸發方式。您可以針對資料表的插入、更新、刪除或讀取作業註冊指令碼；作為排程的工作；或作為自訂 API。如需詳細資訊，請參閱<a href="http://go.microsoft.com/fwlink/p/?LinkID=287178">在行動服務中使用伺服器指令碼</a>。</p>
</div>
</li> 
<li><p>按 F5 鍵以執行應用程式並確認立即從行動服務接收通知。</p>
<p>此通知是經由將資料列插入新通道資料表中而產生的，這就是裝置註冊。</p>
</li>
</ol>
雖然產生的程式碼能輕鬆地在應用程式執行時示範通知，但這通常不是有意義的案例。接下來，您會從通道資料表移除通知程式碼，而在 TodoItem 資料表中以某些變更取代。 

<h2><a name="update-scripts"></a>更新產生的推播通知程式碼</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

<h2><a name="test"></a>在應用程式中測試推播通知</h2>

1. 在 Visual Studio 中，按 F5 鍵以執行此應用程式。

2. 在應用程式的 [**Insert a TodoItem**] 中輸入文字，然後按一下 [**儲存**]。

   	![][13]

   	Note that after the insert completes, the app receives a push notification from WNS.

   	![][14]

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓 Windows 市集應用程式能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

+ [開始使用通知中心]
  <br/>了解如何在 Windows 市集應用程式中利用通知中樞。

+ [將通知傳送給訂閱者]
	<br/>了解使用者如何註冊及接收他們所需類別的推播通知。

+ [將通知傳送給使用者]
	<br/>了解如何將推播通知從行動服務傳送給任何裝置上的特定使用者。

+ [傳送跨平台通知給使用者]
	<br/>了解如何使用範本從行動服務傳送推播通知，但不必在您的後端製作平台特定裝載。

考慮更深入了解下列行動服務主題：

* [開始使用資料]
  <br/>進一步了解使用行動服務來儲存和查詢資料。

* [開始使用驗證]
  <br/>了解如何使用 Windows 帳戶驗證您的應用程式使用者。

* [行動服務伺服器指令碼參考]
  <br/>進一步了解註冊和使用伺服器指令碼。

* [行動服務 .NET 作法概念性參考]
  <br/>深入了解如何搭配使用行動服務與 .NET。

<!-- Anchors. -->
[針對推播通知註冊您的應用程式及設定行動服務]: #register
[更新產生的推播通知程式碼]: #update-scripts
[插入資料以接收通知]: #test
[後續步驟]:#next-steps

<!-- Images. -->











[13]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png




<!-- URLs. -->
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows (英文)]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started/
[開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-dotnet/
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-dotnet
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-dotnet
[推播通知給應用程式使用者]: /zh-tw/develop/mobile/tutorials/push-notifications-to-users-dotnet
[使用指令碼授權使用者]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript 和 HTML]: /zh-tw/develop/mobile/tutorials/get-started-with-push-js

[Azure 管理入口網站]: https://manage.windowsazure.com/
[wns 物件]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[行動服務 .NET 作法概念性參考]: /zh-tw/develop/mobile/how-to-guides/work-with-net-client-library/
[使用指令碼驗證和修改資料]: /zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-dotnet
[開始使用通知中心]: /zh-tw/manage/services/notification-hubs/getting-started-windows-dotnet/
[什麼是通知中心？]: /zh-tw/develop/net/how-to-guides/service-bus-notification-hubs/
[將通知傳送給訂閱者]: /zh-tw/manage/services/notification-hubs/breaking-news-dotnet/
[將通知傳送給使用者]: /zh-tw/manage/services/notification-hubs/notify-users/
[傳送跨平台通知給使用者]: /zh-tw/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
