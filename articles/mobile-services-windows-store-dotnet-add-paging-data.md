<properties pageTitle="Refine Mobile Services queries with paging (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Store app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

使用分頁縮小行動服務查詢範圍
============================

> [AZURE.SELECTOR-LIST (Platform | Backend )]
>  - [(Windows 市集 C\# | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data.md)
>  - [(Windows 市集 C\# | JavaScript)](mobile-services-windows-store-dotnet-add-paging-data.md)
>  - [(Windows 市集 JavaScript | .NET)](mobile-services-dotnet-backend-windows-store-javascript-add-paging-data.md)
>  - [(Windows 市集 JavaScript | JavaScript)](mobile-services-windows-store-javascript-add-paging-data.md)
>  - [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-add-paging-data)
>  - [(Windows Phone | JavaScript)](mobile-services-windows-phone-add-paging-data)
>  - [(iOS | JavaScript)](mobile-services-ios-add-paging-data)
>  - [(Android | JavaScript)](mobile-services-android-add-paging-data)
>  - [(HTML | .NET)](mobile-services-html-add-paging-data)
>  - [(Xamarin iOS | .NET)](partner-xamarin-mobile-services-ios-add-paging-data)
>  - [(Xamarin Android | .NET)](partner-xamarin-mobile-services-android-add-paging-data)

本主題說明如何使用分頁來管理 Azure 行動服務傳回您 Windows 市集應用程式的資料量。在本教學課程中，您將在用戶上使用 **Take** 和 **Skip** 查詢，要求特定「頁面」的資料。

> [WACOM.NOTE]為了避免行動裝置用戶端發生資料溢位，行動服務實施了自動頁面限制，預設為每次傳回最多 50 個項目作為回應。您可以指定頁面大小，明確要求每次傳送最多 1,000 個項目作為回應。

本教學課程會以上一堂教學課程[開始使用資料](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/)中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須至少完成關於使用資料系列的第一個教學課程[開始使用資料](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/)。

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

後續步驟
--------

這將結束一整套示範在行動服務中使用資料基本概念的教學課程。考慮搜尋有關下列行動服務主題的更多資訊：

-   [開始使用驗證](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/)
    了解如何驗證具有 Windows 帳戶的應用程式使用者。

-   [開始使用推播通知](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/)
    了解如何將非常基本的推播通知傳送到應用程式。

-   [行動服務 .NET 作法概念參考資料](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
    深入了解如何使用搭配 .NET 的行動服務。


