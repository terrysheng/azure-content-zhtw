<properties pageTitle="Service-side authorization (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# 行動服務使用者的伺服器端授權

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts" title="Windows 市集 C#" class="current">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts" title="Windows Phone" class="current">Windows Phone</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts" title="iOS" class="current">iOS</a></div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/" title=".NET 後端" class="current">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts/"  title="JavaScript 後端">JavaScript 後端</a></div>

本主題說明如何授權經驗證的使用者，從 Windows 市集應用程式存取 Azure 行動服務中的資料。在本教學課程中，您會將程式碼新增至控制器中的資料存取方法，而根據經驗證使用者的 userId 篩選查詢，確保每位使用者只能看到他們自己的資料。

本教學課程會以行動服務快速入門為基礎，並依據上一個[開始使用驗證][]教學課程建立。開始此教學課程之前，您必須先完成[開始使用驗證][]。

## <a name="register-scripts"></a>修改資料存取方法

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend][]]

## 測試應用程式

1.  在 Visual Studio 中，開啟您完成[開始使用驗證][]教學課程時所修改的專案。

2.  按 F5 鍵執行應用程式，然後以您選擇的識別提供者登入。

    </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">請注意，雖然已有先前教學課程中在</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000"> TodoItem </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">資料表中輸入的項目，依然不會傳回任何項目。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000"></cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">因為之前的項目插入時沒有</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000"> userID </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">資料欄，而現在有</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000"> Null </cf><cf font="Microsoft JhengHei" complexscriptsfont="Microsoft JhengHei" asiantextfont="Microsoft JhengHei" fontcolor="000000">值。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

3.  在應用程式的 [Insert a TodoItem] 中輸入文字，然後按一下 [儲存]。

    ![][]

    </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">如此會在行動服務中的</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000"> TodoItem </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">資料表中插入文字及</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000"> userId</cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">Because the new item has the correct userId value, it is returned by the mobile service and displayed in the second column.

4.  (選用) 如果您有其他登入帳戶，您可以驗證使用者是否只能看到自己的資料，方法是關閉此應用程式 (Alt+F4) 然後重新執行。顯示登入認證對話方塊時，請輸入不同的登入，然後驗證是否不會顯示在上一個帳戶下所輸入的項目。

## 後續步驟

這將結束示範使用驗證基本概念的教學課程。考慮更深入了解下列行動服務主題：

-   [開始使用資料][]
    深入了解使用行動服務來儲存與查詢資料。

-   [開始使用推播通知][]
    了解如何將極為基本的推播通知傳送到應用程式。

-   [行動服務 .NET 作法概念參考資料][]
    深入了解如何使用搭配 .NET 的行動服務。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows 市集 C#]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts "Windows 市集 C#"
  [Windows 市集 JavaScript]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts "Windows 市集 JavaScript"
  [Windows Phone]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts "Windows Phone"
  [iOS]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts "iOS"
  [.NET 後端]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/ ".NET 後端"
  [JavaScript 後端]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts/ "JavaScript 後端"
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
  [mobile-services-filter-user-results-dotnet-backend]: ../includes/mobile-services-filter-user-results-dotnet-backend.md
  []: ./media/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/mobile-quickstart-startup.png
  [開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
  [開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [行動服務 .NET 作法概念參考資料]: /zh-tw/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
