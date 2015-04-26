<properties 
	pageTitle="在資料中加入分頁 (Windows Phone) | 行動開發人員中心" 
	description="了解如何使用分頁來管理行動服務傳回您 Windows Phone 應用程式的資料量。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="glenga"/>

# 使用分頁縮小行動服務查詢範圍

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

此主題將說明如何使用分頁，管理從 Azure 行動服務傳回至 Windows Phone 應用程式的資料量。在此教學課程中，您將在用戶端上使用 **Take** 和 **Skip** 查詢方法要求特定的資料「頁面」。

>[AZURE.NOTE]為防止行動裝置用戶端出現資料溢位的狀況，行動服務會實作自動分頁限制，而將回應中的項目數上限預設為 50 個。您可以指定頁面大小，以明確要求在回應中傳回最多 1,000 個項目。

本教學課程以先前的教學課程[將行動服務新增至現有的應用程式](/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-data/)中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須先完成此教學課程。  

[AZURE.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

## <a name="next-steps"> </a>後續步驟

有關於在行動服務中使用資料的基本概念教學課程，在此告一段落。考慮更深入了解下列行動服務主題：

* [開始使用驗證]
  <br/>了解如何使用 Windows 帳戶驗證您的應用程式的使用者。

* [開始使用推播通知] 
  <br/>了解如何將非常基本的推播通知傳送至您的應用程式。

<!-- Anchors. -->

[後續步驟]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started/
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-data/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-push/


[管理入口網站]: https://manage.windowsazure.com/



<!--HONumber=42-->
