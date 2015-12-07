<properties
	pageTitle="將驗證加入至 Windows 執行階段 8.1 通用 app | Azure Mobile Apps"
	description="了解如何使用 Azure App Service Mobile Apps，透過眾多識別提供者驗證 Windows 應用程式使用者，包括 AAD、Google、Facebook、Twitter 和 Microsoft。"
	services="app-service\mobile"
	documentationCenter="windows"
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/23/2015"
	ms.author="glenga"/>

# 將驗證新增至您的 Windows 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何將雲端式驗證加入到您的行動應用程式。在本教學課程中，您會使用 Azure App Service 所支援的身分識別提供者，將驗證新增至行動應用程式快速入門專案。由行動應用程式後端成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程以 Azure Mobile Apps 快速入門為基礎。您必須先完成[開始使用 Mobile Apps ](app-service-mobile-windows-store-dotnet-get-started.md)教學課程。

##<a name="register"></a>註冊應用程式進行驗證，並設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

將其中一個 Windows 應用程式專案設定為啟始專案，按 F5 鍵執行應用程式；應用程式啟動後，確認會引發狀態碼為 401 (未經授權) 的未處理例外狀況。這是因為應用程式嘗試以未驗證的使用者身分存取您的行動應用程式程式碼，但 *TodoItem* 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求 App Service 的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]


##<a name="tokens"></a>將驗證權杖儲存在用戶端上

先前範例所示範的標準登入，在每次應用程式啟動時，皆需要用戶端連絡身分識別提供者和 App Service。這個方法不只效率不彰，而且如果同時有許多用戶試圖啟用您的應用程式時，還可能遇到使用量相關的問題。更好的方法就是快取 App Service 傳回的授權權杖，然後嘗試在使用提供者形式登入前先使用此方法。

>[AZURE.NOTE]無論您使用用戶端管理型或服務管理型驗證，皆可以快取 App Services 發行的權杖。本教學課程使用服務管理驗證。

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##後續步驟

現在您已經完成了這個基本驗證的教學課程，可以考慮繼續進行下列其中一個教學課程：

+ [將推播通知加入至 Windows 應用程式](app-service-mobile-windows-store-dotnet-get-started-push.md)：了解如何將推播通知支援加入至應用程式，並設定行動應用程式後端以使用 Azure 通知中樞傳送推播通知。

+ [啟用 Windows 應用程式的離線同步處理](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)：了解如何使用行動應用程式後端，將離線支援加入至應用程式。離線同步處理可讓使用者與行動應用程式進行互動 - 檢視、新增或修改資料 - 即使沒有網路連線也可行。


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
 

<!---HONumber=AcomDC_1125_2015--->