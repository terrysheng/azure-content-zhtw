<properties pageTitle="伺服器端授權 (Windows 市集) | 行動開發人員中心" metaKeywords="" description="了解如何在 Azure 行動服務的 .NET 後端為使用者授權。" metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="glenga" />

# 行動服務使用者的伺服器端授權

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]


本主題說明如何授權經驗證的使用者，從 Windows 市集應用程式存取 Azure 行動服務中的資料。在本教學課程中，您會將程式碼新增至控制器中的資料存取方法，而根據經驗證使用者的 userId 篩選查詢，確保每位使用者只能看到他們自己的資料。

本教學課程以行動服務快速入門為基礎，並依據上一個[開始使用驗證]教學課程建立。開始此教學課程之前，您必須先完成[開始使用驗證]。  

## <a name="register-scripts"></a>修改資料存取方法

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)] 


## 測試應用程式

1. 在 Visual Studio 中，開啟您完成教學課程[開始使用驗證]時所修改的專案。

2. 按 F5 鍵執行應用程式，然後以您選擇的識別提供者登入。 

   	Notice that this time, although there are items already in the TodoItem table from previous tutorials, no items are returned.發生的原因是因為插入之前的項目時並沒有 userId 資料行，但現在這些項目有 Null 值。

3. 在應用程式的 [**Insert a TodoItem**] 中輸入文字，然後按一下 [**儲存**]。

   	![][3]

   	This inserts both the text and the userId in the TodoItem table in the mobile service.因為新項目有正確的 userId 值，它會由行動服務傳回並顯示在第二個資料行中。

6. (選用) 如果您有其他登入帳戶，您可以驗證使用者是否只能看到自己的資料，方法是關閉此應用程式 (Alt+F4) 然後重新執行。顯示登入認證對話方塊時，請輸入不同的登入，然後驗證是否不會顯示在上一個帳戶下所輸入的項目。 

## 後續步驟

示範使用驗證基本概念的教學課程到此結束。請考慮更深入了解下列行動服務主題：

* [開始使用資料]
  <br/>進一步了解使用行動服務來儲存和查詢資料。

* [開始使用推播通知] 
  <br/>了解如何將非常基本的推播通知傳送至您的應用程式。
  
* [行動服務 .NET 作法概念性參考]
  <br/>深入了解如何搭配使用行動服務與 .NET。

<!-- Anchors. -->
[註冊伺服器指令碼]: #register-scripts
[後續步驟]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push

[行動服務 .NET 作法概念性參考]: /zh-tw/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
