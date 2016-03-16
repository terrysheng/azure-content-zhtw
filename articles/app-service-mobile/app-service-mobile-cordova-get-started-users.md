<properties
	pageTitle="使用 Mobile Apps 在 Apache Cordova 上新增驗證 | Azure App Service"
	description="了解如何在 Azure App Service 中使用 Mobile Apps，透過眾多識別提供者驗證 Apache Cordova 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="ggailey777"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="adrianha"/>

# 新增驗證至您的 Apache Cordova 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## 摘要

在本教學課程中，您可以使用支援的身分識別提供者，將驗證加入 Apache Cordova 上的 TodoList 快速入門專案。本教學課程以[開始使用行動應用程式]為基礎，您必須先完成該教學課程。

##<a name="register"></a>註冊應用程式進行驗證，並設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ 當您完成[開始使用行動應用程式]教學課程時，請在 Visual Studio 中開啟您建立的專案，然後在 **Google Android 模擬器**中執行您的應用程式，並驗證「未預期的連線錯誤」會在啟動應用程式之後顯示。

    通常因為應用程式嘗試以未經驗證的使用者身分存取後端，而發生這種情況。後端會將使用者重新導向至使用 OAuth 的驗證頁面。不過，應用程式並不信任 OAuth 端點。

接下來，您要將應用程式更新為在要求行動應用程式後端的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

1. 在 **Visual Studio** 中開啟您的專案，然後開啟 `www/index.html` 檔案進行編輯。

2. 找出 head 區段中的 `Content-Security-Policy` 中繼標籤。您必須將 OAuth 主機新增至允許的來源清單。

    | 提供者 | SDK 提供者名稱 | OAuth 主機 |
    | :--------------------- | :---------------- | :-------------------------- |
    | Azure Active Directory | aad | https://login.windows.net |
    | Facebook | facebook | https://www.facebook.com |
    | Google | google | https://accounts.google.com |
    | Microsoft | microsoftaccount | https://login.live.com |
    | Twitter | twitter | https://api.twitter.com |

    content-security-policy (針對 Azure Active Directory 實作) 的範例如下所示：

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' 
			data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    您應該使用上表中的 OAuth 主機取代 `https://login.windows.net`。如需此中繼標籤的詳細資訊，請參閱 [Content-Security-Policy 文件]。

    請注意，在適當的行動裝置上使用時，某些驗證提供者不需要 Content-Security-Policy 變更。例如，在 Android 裝置上使用 Google 驗證時不需要 Content-Security-Policy 變更。

3. 開啟 `www/js/index.js` 檔案進行編輯，找出 `onDeviceReady()` 方法，並在用戶端建立程式碼底下加入下列內容：

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    請注意，此程式碼會取代現有用於建立資料表參考和重新整理 UI 的程式碼。

    login () 方法會開始向提供者驗證。login() 方法是會傳回 JavaScript Promise 的非同步函式。初始化作業的其餘部分會置於承諾回應中，如此就不會在 login() 方法完成之前執行。

4. 在您剛才加入的程式碼中，使用您的登入提供者名稱取代 `SDK_Provider_Name`。例如，針對 Azure Active Directory，請使用 `client.login('aad')`。

4. 執行專案。當專案完成初始化時，您的應用程式會針對選擇的驗證提供者顯示 OAuth 登入頁面。

##<a name="next-steps"></a>後續步驟

* 深入了解 Azure App Service [驗證相關資訊]。
* 將[推播通知]新增至 Apache Cordova 應用程式，以繼續本教學課程。

<!-- URLs. -->
[開始使用行動應用程式]: app-service-mobile-cordova-get-started.md
[Content-Security-Policy 文件]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[推播通知]: app-service-mobile-cordova-get-started-push.md
[驗證相關資訊]: app-service-mobile-auth.md

<!---HONumber=AcomDC_0302_2016-------->