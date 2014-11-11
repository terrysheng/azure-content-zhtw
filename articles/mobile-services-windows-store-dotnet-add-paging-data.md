<properties pageTitle="Refine Mobile Services queries with paging (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Store app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 使用分頁縮小行動服務查詢範圍

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data" title="Windows 市集 C#" class="current">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-add-paging-data" title="Windows 市集 JavaScript" class="current">Windows 市集 JavaScript</a><a href="/zh-tw/documentation/articles/mobile-services-windows-phone-add-paging-data" title="Windows Phone" class="current">Windows Phone</a><a href="/zh-tw/documentation/articles/mobile-services-ios-add-paging-data" title="iOS" class="current">iOS</a><a href="/zh-tw/documentation/articles/mobile-services-android-add-paging-data" title="Android" class="current">Android</a><a href="/zh-tw/documentation/articles/mobile-services-html-add-paging-data" title="HTML" class="current">HTML</a><a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-add-paging-data" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-android-add-paging-data" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data" title=".NET 後端">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data"  title="JavaScript 後端" class="current">JavaScript 後端</a></div>

本主題說明如何使用分頁來管理 Azure 行動服務傳回您 Windows 市集應用程式的資料量。在此教學課程中，您將在用戶端上使用 **Take** 和 **Skip** 查詢方法要求特定的資料「頁面」。

> [WACOM.NOTE]為防止行動裝置用戶端出現資料溢位的狀況，行動服務會實作自動分頁限制，而將回應中的項目數上限預設為 50 個。您可以指定頁面大小，以明確要求在回應中傳回最多 1,000 個項目。

本教學課程會以上一堂教學課程[開始使用資料][開始使用資料]中的步驟和範例應用程式為基礎。開始進行此教學課程之前，您至少必須先完成資料序列中的第一個教學課程[開始使用資料][開始使用資料]。

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

## <a name="next-steps"> </a>後續步驟

有關於在行動服務中使用資料的基本概念教學課程，在此告一段落。考慮更深入了解下列行動服務主題：

-   [開始使用驗證][開始使用驗證]
    了解如何使用 Windows 帳戶驗證您的應用程式的使用者。

-   [開始使用推播通知][開始使用推播通知]
    了解如何將極為基本的推播通知傳送到應用程式。

-   [行動服務 .NET 作法概念參考資料][行動服務 .NET 作法概念參考資料]
    深入了解如何使用搭配 .NET 的行動服務。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [開始使用資料]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
  [mobile-services-windows-dotnet-paging]: ../includes/mobile-services-windows-dotnet-paging.md
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/
  [行動服務 .NET 作法概念參考資料]: /zh-tw/develop/mobile/how-to-guides/work-with-net-client-library
