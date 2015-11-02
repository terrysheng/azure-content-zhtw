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
	ms.date="08/22/2015"
	ms.author="glenga"/>

# 將驗證新增至您的 Windows 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

本主題說明如何從用戶端應用程式驗證 App Service 行動應用程式的使用者。在本教學課程中，您將使用 App Service 支援的身分識別提供者，將驗證新增至快速入門專案。由行動應用程式成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程以行動應用程式快速入門為基礎。您必須先完成[開始使用行動應用程式]教學課程。http://acom-sandbox.azurewebsites.net/documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/?rnd=1

##<a name="create-gateway"></a>建立 App Service 閘道

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway-preview](../../includes/app-service-mobile-dotnet-backend-create-gateway-preview.md)]

##<a name="register"></a>註冊應用程式進行驗證，並設定 App Service

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4.在 Visual Studio 中，開啟您用戶端應用程式專案中共用的 App.xaml.cs 專案檔案，並確定 **MobileServiceClient** 執行個體設定為使用行動應用程式後端與閘道兩者的 URL。

&nbsp;&nbsp;5.將其中一個 Windows 應用程式專案設定為啟始專案，按 F5 鍵執行應用程式；應用程式啟動後，確認會引發狀態碼為 401 (未經授權) 的未處理例外狀況。

&nbsp;&nbsp;這是因為應用程式嘗試以未驗證的使用者身分存取您的行動應用程式程式碼，但 *TodoItem* 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求 App Service 的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)]


##<a name="tokens"></a>將驗證權杖儲存在用戶端上

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)]

##後續步驟


<!-- URLs. -->
[開始使用行動應用程式]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md
 

<!---HONumber=Oct15_HO4-->