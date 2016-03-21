<properties
	pageTitle="Azure App Service 中 API Apps 的使用者驗證 | Microsoft Azure"
	description="了解如何藉由僅允許經過驗證的使用者存取，保護 Azure App Service 中的 API 應用程式。"
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="tdykstra"/>

# Azure App Service 中 API Apps 的使用者驗證

[AZURE.INCLUDE [選取器](../../includes/app-service-api-auth-selector.md)]

## 概觀

這是 App Service API Apps 入門系列中的第四篇文章。在本文中，您將了解：

* 如何保護 App Service API 應用程式，以便只有通過驗證的使用者可以呼叫它。
* 如何使用 Azure Active Directory (Azure AD) 的詳細資料設定驗證提供者。
* 如何使用 [JavaScript 適用的 Active Directory 驗證程式庫 (ADAL)](https://github.com/AzureAD/azure-activedirectory-library-for-js) 取用受保護的 API 應用程式。

本文包含兩個部分：

* [如何在 Azure App Service 中設定使用者驗證](#authconfig)一節大致說明如何為 API 應用程式設定使用者驗證，且一體適用於 App Service 支援的所有架構，包括 .NET、Node.js 和 Java。

* 從[繼續 .NET 使用者入門教學課程](#tutorialstart)一節開始，本文會引導您使用 .NET 後端和 AngularJS 前端設定範例應用程式，使其使用 Azure Active Directory 進行使用者驗證。

## <a id="authconfig"></a>如何在 Azure App Service 中設定使用者驗證

本節提供適用於任何 API 應用程式的一般指示。如需「待辦事項清單」.NET 範例應用程式的專用步驟，請移至[繼續進行 .NET 入門教學課程](#tutorialstart)。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，瀏覽至您想要保護的 API 應用程式的 [設定] 刀鋒視窗，尋找 [功能] 區段，再按一下 [驗證/授權]。

	![Azure 入口網站驗證/授權](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. 在 [驗證/授權] 刀鋒視窗中，按一下 [開啟]。

4. 在 [要求未經驗證時所採取的動作] 下拉式清單中，選取其中一個選項。

	* 如果您只想讓經過驗證的呼叫觸達 API 應用程式，請選擇其中一個 [登入方式] 選項。此選項可讓您保護 API 應用程式，卻不需要撰寫任何在其中執行的程式碼。

	* 如果您想讓所有呼叫觸達 API 應用程式，請選擇 [允許要求 (無動作)]。您可以使用此選項將未經驗證的呼叫端導向您選擇的驗證提供者。若要使用此選項，您必須撰寫程式碼來處理授權。

	如需詳細資訊，請參閱 [Azure App Service 中的 API Apps 驗證與授權](app-service-api-authentication.md#multiple-protection-options)。

5. 選取一或多個 [驗證提供者]。

	下圖顯示要求所有呼叫端要由 Azure AD 進行驗證的選項。
 
	![Azure 入口網站驗證/授權刀鋒視窗](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

	當您選擇驗證提供者後，入口網站會顯示該提供者的設定刀鋒視窗。

	如需說明如何設定驗證提供者設定刀鋒視窗的詳細指示，請參閱[如何設定 App Service 應用程式使用 Azure Active Directory 登入](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md) (此連結會移至有關 Azure AD 的文章，但該文章本身含有連往其他驗證提供者之文章的索引標籤)。

7. [驗證提供者設定] 刀鋒視窗使用完畢時，按一下 [確定]。

7. 在 [驗證/授權] 刀鋒視窗中，按一下 [儲存]。

完成此作業後，App Service 就會先驗證 API 呼叫再讓其觸達 API 應用程式。凡是 App Service 所支援的語言 (包括 .NET、Node.js 和 Java)，驗證服務的運作方式都相同。

為了讓 API 呼叫受到驗證，呼叫端會在 HTTP 要求的 Authorization 標頭中包含驗證提供者的 OAuth 2.0 持有人權杖。若要取得權杖，請使用驗證提供者的 SDK。

## <a id="tutorialstart"></a>繼續進行 .NET 入門教學課程

如果您要遵循適用於 API 應用程式的 Node.js 或 Java 入門系列，請跳至下一節 [API Apps 的服務主體驗證](app-service-api-dotnet-service-principal-auth.md)。

如果您要遵循適用於 API 應用程式的 .NET 入門系列，並已依照[第一個](app-service-api-dotnet-get-started.md)和[第二個](app-service-api-cors-consume-javascript.md)教學課程中的指示部署範例應用程式，請跳至[設定驗證](#azureauth)一節。

如果您未進行第一個和第二個教學課程，且您想要遵循這個教學課程，請檢查[第一個教學課程](app-service-api-dotnet-get-started.md)列出的先決條件，然後使用[待辦事項清單範例儲存機制讀我檔案](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/readme.md)中的 [部署至 Azure] 按鈕，以部署 API 應用程式和 Web 應用程式。

部署完成時，會顯示連接至 Web 應用程式的 HTTP 連結。若要執行應用程式，並確認它可以正常操作，請將該 URL 變更為 HTTPS。

## <a id="azureauth"></a> 在 App Service 和 Azure AD 中設定驗證

您現在已擁有在 Azure App Service 中執行、且不需要使用者接受驗證的應用程式。在本節中，您將執行下列工作來新增驗證機制：

* 設定 App Service 以要求在呼叫中介層 API 應用程式時需要進行 Azure Active Directory (Azure AD) 驗證。
* 建立 Azure AD 應用程式。
* 設定 Azure AD 應用程式，使其在登入後將持有人權杖傳送到 AngularJS 前端。 

如果您遵循教學課程指示進行時遇到問題，請參閱教學課程尾端的[疑難排解](#troubleshooting) 一節。
 
### 為中介層 API 應用程式設定驗證

1. 在 [Azure 入口網站](https://portal.azure.com/)中，瀏覽至您想要為 ToDoListAPI 專案建立的 API 應用程式的 [設定] 刀鋒視窗，尋找 [功能] 區段，再按一下 [驗證/授權]。

	![Azure 入口網站驗證/授權](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. 在 [驗證/授權] 刀鋒視窗中，按一下 [開啟]。

4. 在 [要求未經驗證時所採取的動作] 下拉式清單中，選取 [登入 Azure Active Directory]。

	此選項可確保未經驗證的要求均無法觸達 API 應用程式。

5. 在 [驗證提供者] 底下，按一下 [Azure Active Directory]。

	![Azure 入口網站驗證/授權刀鋒視窗](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. 在 [Azure Active Directory 設定] 刀鋒視窗中，按一下 [快速]

	![Azure 入口網站驗證/授權表達選項](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	在使用 [快速] 選項時，App Service 可以自動在 Azure AD [租用戶](https://msdn.microsoft.com/zh-TW/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)中建立 Azure AD 應用程式。

	您不必建立租用戶，因為每個 Azure 帳戶都會自動擁有一個。

7. 在 [管理模式]下，按一下 [建立新的 AD 應用程式] (如果尚未選取)，並記下 [建立應用程式] 文字方塊中的值；您稍後將在 Azure 傳統入口網站中查閱此 AAD 應用程式。

	![Azure 入口網站 Azure AD 設定](./media/app-service-api-dotnet-user-principal-auth/aadsettings2.png)

	Azure 會自動在 Azure AD 租用戶中建立包含指示值的 Azure AD 應用程式。根據預設，Azure AD 應用程式的名稱會與 API 應用程式相同。如有需要，也可以輸入不同名稱。
 
7. 按一下 [確定]。

7. 在 [驗證/授權] 刀鋒視窗中，按一下 [儲存]。

	![按一下 [Save] (儲存)。](./media/app-service-api-dotnet-user-principal-auth/authsave.png)

現在，只有 Azure AD 租用戶中的使用者可以呼叫 API 應用程式。

### 選擇性：測試 API 應用程式

1. 在瀏覽器中，移至 API 應用程式的 URL：在 Azure 入口網站的 [API 應用程式] 刀鋒視窗中，按一下 [URL] 下方的連結。  

	由於未經驗證的要求不得觸達 API 應用程式，因此系統會將您重新導向至登入畫面。

	如果瀏覽器移至 [已成功建立] 頁面，則表示瀏覽器可能已登入，若是如此，請開啟 InPrivate 或 Incognito 視窗並移至 API 應用程式的 URL。

2. 在 Azure AD 租用戶中以使用者的認證進行登入。

	登入後，[已成功建立] 頁面隨即出現在瀏覽器中。

9. 關閉瀏覽器。

### 設定 Azure AD 應用程式

當您設定了 Azure AD 驗證，App Service 就會為您建立 Azure AD 應用程式。根據預設，新的 Azure AD 應用程式已設定為會將持有人權杖提供給 API 應用程式的 URL。在本節中，您將會設定 Azure AD 應用程式，以將持有人權杖提供給 AngularJS 前端，而非直接提供給中介層 API 應用程式。AngularJS 前端會在呼叫 API 應用程式時，將權杖傳送到 API 應用程式。

>[AZURE.NOTE] 為了盡可能簡化程序，本教學課程為前端和中介層 API 應用程式使用單一 Azure AD 應用程式。另一個選項是使用兩個 Azure AD 應用程式。在此情況下，您必須授與前端的 Azure AD 應用程式權限才能呼叫中介層的 Azure AD 應用程式。此多重應用程式方法可讓您更精細地控制每一個層級的權限。

11. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中，移至 [Azure Active Directory]。

	您必須使用傳統入口網站，因為現行的 Azure 入口網站尚未提供您需要存取的特定 Azure Active Directory 設定。

12. 在 [目錄] 索引標籤中，選取您的 AAD 租用戶。

	![傳統入口網站的 Azure AD](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. 按一下 [應用程式] > [我的公司擁有的應用程式]，然後按一下核取記號。

	您可能還必須重新整理頁面，才能看見新的應用程式。

15. 在應用程式清單中，按一下當您針對 API 應用程式啟用驗證時 Azure 為您建立的應用程式名稱。

	![Azure AD [應用程式] 索引標籤](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. 按一下 [設定]。

	![Azure AD [設定] 索引標籤](./media/app-service-api-dotnet-user-principal-auth/configure.png)

17. 將 [登入 URL] 設定為 AngularJS Web 應用程式的 URL，且結尾不要有斜線。

	例如：https://todolistangular.azurewebsites.net

	![登入 URL](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

17. 將 [回覆 URL] 設定為 Web 應用程式的 URL，且結尾不要有斜線。

	例如：https://todolistsangular.azurewebsites.net

16. 按一下 [儲存]。

	![回覆 URL](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

15. 在頁面底部，按一下 [管理資訊清單] > [下載資訊清單]。

	![下載資訊清單](./media/app-service-api-dotnet-user-principal-auth/downloadmanifest.png)

17. 將檔案下載至可在其中編輯它的位置。

16. 在下載的資訊清單檔案中，搜尋 `oauth2AllowImplicitFlow` 屬性。將這個屬性的值從 `false` 變更為 `true`，然後儲存檔案。

	必須要有這項設定才能從 JavaScript 單一頁面應用程式進行存取。它可讓 Oauth 2.0 持有人權杖傳回 URL 片段中。

16. 按一下 [管理資訊清單] > [上傳資訊清單]，然後上傳您在上述步驟中更新的檔案。

	![上傳資訊清單](./media/app-service-api-dotnet-user-principal-auth/uploadmanifest.png)

17. 複製 [用戶端識別碼] 值，並將其儲存至可在稍後取得此值的位置。

## 設定 ToDoListAngular 專案以使用驗證

在本節中，您將會變更 AngularJS 前端，使其使用 JS 適用的 Active Directory 驗證程式庫 (ADAL)，從 Azure AD 取得登入使用者的持有人權杖。程式碼會將權杖包含在傳送至中介層的 HTTP 要求中，如下圖所示。

![使用者驗證圖表](./media/app-service-api-dotnet-user-principal-auth/appdiagram.png)

對 ToDoListAngular 專案中的檔案進行下列變更。

1. 開啟 index.html 檔案。

2. 將參考 JS 適用的 Active Directory 驗證程式庫 (ADAL) 指令碼的程式行取消註解。

		<script src="app/scripts/adal.js"></script>
		<script src="app/scripts/adal-angular.js"></script>

1. 開啟 app/scripts/app.js 檔案。

2. 將標記為「不需要驗證」的程式碼區塊註解化，並將標記為「需要驗證」的程式碼區塊取消註解。

	這項變更會參考 ADAL JS 驗證提供者，並為其提供設定值。在下列步驟中，您會設定 API 應用程式和 Azure AD 應用程式的設定值。

8. 在設定 `endpoints` 變數的程式碼中，將 API URL 設定為您為 ToDoListAPI 專案建立之 API 應用程式的 URL，並將 Azure AD 應用程式識別碼設定為您從 Azure 傳統入口網站複製而來的用戶端識別碼。

	程式碼現在類似下列範例。

		var endpoints = {
		    "https://todolistapi0121.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. 在 `adalProvider.init` 的呼叫中，將 `tenant` 設定為租用戶名稱，並將 `clientId` 設定為您在上一個步驟中使用的相同值。

	程式碼現在類似下列範例。

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: 'contoso.onmicrosoft.com',
		        clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		    },
		    $httpProvider
		    );

	這些 `app.js` 變更會指定讓呼叫端程式碼和接受呼叫的 API 位於相同的 Azure AD 應用程式。

1. 開啟 app/scripts/homeCtrl.js 檔案。

2. 將標記為「不需要驗證」的程式碼區塊註解化，並將標記為「需要驗證」的程式碼區塊取消註解。

1. 開啟 app/scripts/indexCtrl.js 檔案。

2. 將標記為「不需要驗證」的程式碼區塊註解化，並將標記為「需要驗證」的程式碼區塊取消註解。

### 將 ToDoListAngular 專案部署到 Azure

8. 在 [方案總管] 中，以滑鼠右鍵按一下 ToDoListAngular 專案，然後按一下 [發佈]。

9. 按一下 [發行]。

	Visual Studio 會部署專案並將瀏覽器開啟至 Web 應用程式的基底 URL。這會顯示 403 錯誤頁面，這是嘗試從瀏覽器移至 Web API 基底 URL 時的正常現象。

	您還需要變更中介層 API 應用程式，才能測試應用程式。

10. 關閉瀏覽器。

## 設定 ToDoListAPI 專案以使用驗證

ToDoListAPI 專案目前會將 "*" 做為 `owner` 值傳送到 ToDoListDataAPI。在本節中，您將會變更用來傳送登入使用者識別碼的程式碼。

對 ToDoListAPI 專案進行下列變更。

1. 開啟 Controllers/ToDoListController.cs 檔案，並將每個動作方法中會把 `owner` 設定為 Azure AD `NameIdentifier` 宣告值的程式行取消註解。例如：

		owner = ((ClaimsIdentity)User.Identity).FindFirst(ClaimTypes.NameIdentifier).Value;

	重要︰不要取消註解 `ToDoListDataAPI` 方法中的程式碼；您稍後將在服務主體驗證教學課程中執行它。

### 將 ToDoListAPI 專案部署到 Azure

8. 在 [方案總管] 中，以滑鼠右鍵按一下 ToDoListAPI 專案，然後按一下 [發佈]。

9. 按一下 [發行]。

	Visual Studio 會部署專案並將瀏覽器開啟至 API 應用程式的基底 URL。

10. 關閉瀏覽器。

### 測試應用程式

9. 使用 HTTPS 而非 HTTP 移至 Web 應用程式的 URL。

8. 按一下 [待辦事項清單] 索引標籤。

	系統會提示您登入。

9. 使用 AAD 租用戶中使用者的認證進行登入。

10. [待辦事項清單] 頁面隨即出現。

	![待辦事項清單頁面](./media/app-service-api-dotnet-user-principal-auth/webappindex.png)

	目前並不會顯示任何待辦事項項目，因為這些項目到目前為止全都適用於擁有者 "*"。現在中介層正在要求登入使用者的項目，但目前尚未建立任何項目。

11. 新增待辦事項項目，以確認應用程式可以運作。

12. 在另一個瀏覽器視窗中，移至 ToDoListDataAPI API 應用程式的 Swagger UI URL，並按一下 [ToDoList] > [取得]。輸入星號來代表 `owner` 參數，然後按一下 [立即試用]。

	回應顯示新的待辦事項項目在 Owner 屬性中擁有實際的 Azure AD 使用者識別碼。

	![JSON 回應中的擁有者識別碼](./media/app-service-api-dotnet-user-principal-auth/todolistapiauth.png)


## 從頭建置專案

兩個 Web API 專案是透過使用 Azure API 應用程式專案範本並以 ToDoList 控制器取代預設值控制器所建立。

如需如何使用 Web API 2 後端建立 AngularJS 單一頁面應用程式的相關資訊，請參閱[實習實驗室：使用 ASP.NET Web API 和 Angular.js 建置單一頁面應用程式 (SPA)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs)。如需如何新增 Azure AD 驗證程式碼的相關資訊，請參閱下列資源：

* [使用 Azure AD 保護 AngularJS 單一頁面應用程式](../active-directory/active-directory-devquickstarts-angular.md)。
* [簡介 ADAL JS v1](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)

## 疑難排解

[AZURE.INCLUDE [疑難排解](../../includes/app-service-api-auth-troubleshooting.md)]

* 請確定不要混淆 ToDoListAPI (中介層) 和 ToDoListDataAPI (資料層)。例如，請確認將驗證新增至中介層 API 應用程式而非資料層。 
* 請確定 AngularJS 原始碼其參考中介層 API 應用程式 URL (ToDoListAPI 而非 ToDoListDataAPI) 和正確的 Azure AD 用戶端識別碼。 

## 後續步驟

在本教學課程中，您已了解如何使用 API 應用程式的 App Service 驗證，以及如何利用 ADAL JS 程式庫呼叫 API 應用程式。在下一個教學課程中，您將學習如何[對於服務對服務的案例保護您的 API 應用程式存取](app-service-api-dotnet-service-principal-auth.md)。

<!---HONumber=AcomDC_0309_2016-->