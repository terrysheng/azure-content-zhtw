<properties pageTitle="在資料中加入分頁 (JavaScript) - Azure 行動服務 " metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Store JavaScript app from Mobile Services." metaCanonical="http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/add-paging-to-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />


# 使用分頁縮小行動服務查詢範圍

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]


本主題說明如何使用分頁來管理 Azure 行動服務傳回您 Windows 市集應用程式的資料量。在此教學課程中，您將在用戶端上使用 **Take** 和 **Skip** 查詢方法要求特定的資料「頁面」。

>[WACOM.NOTE]為防止行動裝置用戶端出現資料溢位的狀況，行動服務會實作自動分頁限制，而將回應中的項目數上限預設為 50 個。您可以指定頁面大小，以明確要求在回應中傳回最多 1,000 個項目。

本教學課程會以先前的教學課程[開始使用資料]中的步驟和範例應用程式為基礎。開始進行此教學課程之前，您至少必須先完成資料序列中的第一個教學課程[開始使用資料]。 

[WACOM.INCLUDE [mobile-services-javascript-paging](../includes/mobile-services-javascript-paging.md)]

## <a name="next-steps"> </a>後續步驟

有關於在行動服務中使用資料的基本概念教學課程，在此告一段落。請考慮更深入了解下列行動服務主題：

* [開始使用驗證]
  <br/>了解如何使用 Windows 帳戶驗證您的應用程式使用者。

* [開始使用推播通知] 
  <br/>了解如何將非常基本的推播通知傳送至您的應用程式。

<!-- Anchors. -->

[後續步驟]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-windows-store-get-started/
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[管理入口網站]: https://manage.windowsazure.com/
