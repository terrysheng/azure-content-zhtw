<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

開始使用行動服務中的驗證
========================

[Windows 市集 C\#](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Windows 市集 C#")[Windows 市集 JavaScript](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Windows 市集 JavaScript")[Windows Phone](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone")[iOS](/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users "iOS")

[.NET 後端](/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ ".NET 後端") | [JavaScript 後端](/zh-tw/documentation/articles/mobile-services-ios-get-started-users/ "JavaScript 後端")

本主題說明如何從您的應用程式在 Azure 行動服務中驗證使用者。在本教學課程中，您會使用行動服務所支援的身份識別提供者將驗證新增至快速入門專案。使用者 ID 值在成功經過行動服務的驗證和授權後，就會顯示出來。

本教學課程將引導您逐步完成以下在您的應用程式中啟用驗證的基本步驟：

1.  [註冊應用程式進行驗證，並設定行動服務]
2.  [限制只有經驗證的使用者具有資料表的權限]
3.  [新增驗證至應用程式]

本教學課程是以行動服務快速入門為基礎。您也必須先完成 [開始使用行動服務](../zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started/) 教學課程。

註冊應用程式進行驗證，並設定行動服務
------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  在 Visual Studio 中開啟行動服務專案的 Web.config 檔案，然後在 appSettings 區段中設定應用程式識別碼，以及身分識別提供者為您提供的共用密碼值。

    在本機開發期間將會使用這些設定。當您將行動服務專案發佈至 Azure 之後，這些設定將會覆寫為入口網站中的設定值。

限制只有經驗證的使用者具有權限
------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

1.  在 Xcode 中，開啟您完成教學課程[開始使用行動服務](/zh-tw/documentation/articles/mobile-services-ios-get-started)時建立的專案。

2.  按 **[執行]** 按鈕，以在 iPhone 模擬器中建立專案並啟動應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。

    發生這個情況是因為應用程式嘗試以未經驗證的使用者身分存取行動服務，但 *TodoItem* 資料表現在要求要經過驗證。

    接下來，您將更新應用程式，使其先驗證使用者再向行動服務要求資源。 ##新增驗證至應用程式 

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)] 

## 後續步驟 在下一個 

[行動服務使用者的伺服器端授權] [以指令碼授權使用者] 教學課程中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，來篩選行動服務傳回的資料。 

<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]:#register 
[限制只有經驗證的使用者具有資料表的權限]:#permissions 
[新增驗證至應用程式]:#add-authentication 
[後續步驟]:#next-steps 


<!-- URLs. -->
[Get started with data]:/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ 
[Get started with authentication]:/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ 
[Get started with push notifications]:/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ 
[以指令碼授權使用者]:/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts 


[Azure 管理入口網站]:https://manage.windowsazure.com/ 
[Mobile Services .NET How-to Conceptual Reference]:/en-us/develop/mobile/how-to-guides/work-with-net-client-library 
[Register your Windows Store app package for Microsoft authentication]:/zh-tw/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication


