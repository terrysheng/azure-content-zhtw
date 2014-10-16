<properties pageTitle="Add paging to data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Phone app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

使用分頁縮小行動服務查詢範圍
============================

> [AZURE.SELECTOR-LIST (Platform | Backend )]
> - [(Windows 市集 C\# | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data.md)
> - [(Windows 市集 C\# | JavaScript)](mobile-services-windows-store-dotnet-add-paging-data.md)
> - [(Windows 市集 JavaScript | .NET)](mobile-services-dotnet-backend-windows-store-javascript-add-paging-data.md)
> - [(Windows 市集 JavaScript | JavaScript)](mobile-services-windows-store-javascript-add-paging-data.md)
> - [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-add-paging-data)
> - [(Windows Phone | JavaScript)](mobile-services-windows-phone-add-paging-data)
> - [(iOS | JavaScript)](mobile-services-ios-add-paging-data)
> - [(Android | JavaScript)](mobile-services-android-add-paging-data)
> - [(HTML | .NET)](mobile-services-html-add-paging-data)
> - [(Xamarin iOS | .NET)](partner-xamarin-mobile-services-ios-add-paging-data)
> - [(Xamarin Android | .NET)](partner-xamarin-mobile-services-android-add-paging-data)

此主題將說明如何使用分頁，管理從 Azure 行動服務傳回至 Windows Phone 應用程式的資料量。在此教學課程中，您將在用戶端上使用 **Take** 和 **Skip** 查詢方法要求特定的資料「頁面」。

> [WACOM.NOTE]為防止行動裝置用戶端出現資料溢位的狀況，行動服務會實作自動分頁限制，而將回應中的項目數上限預設為 50 個。您可以指定頁面大小，以明確要求在回應中傳回最多 1,000 個項目。

本教學課程會以上一堂教學課程[開始使用資料](/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-data/)中的步驟和範例應用程式為基礎。開始進行此教學課程之前，您至少必須先完成資料序列中的第一個教學課程[開始使用資料](/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-data/)。

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

後續步驟
--------

有關於在行動服務中使用資料的基本概念教學課程，在此告一段落。考慮更深入了解下列行動服務主題：

-   [開始使用驗證](/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-users/)
    <br/>了解如何使用 Windows 帳戶驗證您的應用程式的使用者。

-   [開始使用推播通知](/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-push/)
    <br/>了解如何將極為基本的推播通知傳送到應用程式。


