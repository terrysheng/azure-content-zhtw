<properties 
	pageTitle="如何為您的應用程式服務應用程式設定 Azure Active Directory 驗證" 
	description="了解如何為您的應用程式服務應用程式設定 Azure Active Directory 驗證。" 
	authors="mattchenderson" 
	services="app-service\mobile" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="mahender"/>

# 如何設定 App Service 應用程式使用 Azure Active Directory 登入

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何設定 Azure 應用程式服務，以使用 Azure Active Directory 做為驗證提供者。

> [AZURE.NOTE]本主題示範 App Service 驗證/授權功能的用法。這會取代大部分應用程式的 App Service 閘道器。如果使用閘道器，請參閱[另一種方法]。該小節中使用閘道器所產生的差異都列在注意事項中。


## <a name="express"> </a>使用快速設定設定 Azure Active Directory

13. 在 [Azure 管理入口網站]中，瀏覽至您的應用程式。依序按一下 [設定] 及 [驗證/授權]。

14. 如果 [驗證/授權] 功能未啟用，請切換到 [開]。

15. 按一下 [Azure Active Directory]，然後按一下 [管理模式] 下方的 [快速]。

16. 按一下 [確定] 以在 Azure Active Directory 中註冊應用程式。這樣會建立新的註冊。如果您想改為選擇現有的註冊，請按一下 [選取現有的應用程式]，然後在您的租用戶內搜尋先前建立之註冊的名稱。按一下註冊以選取它，然後按一下 [確定]。然後在 Azure Active Directory 設定刀鋒視窗上按一下 [確定]。

    ![][0]
	
	App Service 預設會提供驗證，但不會限制對您網站內容和 API 的已授權存取。您必須在應用程式程式碼中授權使用者。

17. (選擇性) 若要限制只有透過 Azure Active Directory 授權的使用者可以存取您的網站，請將 [要求未經驗證時所採取的動作] 設為 [Azure Active Directory]。這會要求所有要求都需經過驗證，且所有未經驗證的要求都會重新導向至 Azure Active Directory 以進行驗證。

17. 按一下 [儲存]。

現在，您已可在應用程式中使用 Azure Active Directory 進行驗證。

## <a name="advanced"> </a>(替代方法) 使用進階設定手動設定 Azure Active Directory
您也可以選擇手動提供組態設定。如果您想使用的 AAD 租用戶不同於您登入 Azure 所用的租用戶，這會是較佳的解決方案。若要完成組態，您必須先在 Azure Active Directory 中建立註冊，接著必須提供一些註冊詳細資料給 App Service。

### <a name="register"> </a>向 Azure Active Directory 註冊您的應用程式

1. 登入[預覽 Azure 管理入口網站]，並瀏覽至您的應用程式。複製您的 **URL**。您將使用此資訊設定 Azure Active Directory 應用程式。

3. 登入 [Azure 管理入口網站]，然後導覽至 [**Active Directory**]。

    ![][2]

4. 選取您的目錄，然後選取頂端的 [**應用程式**] 索引標籤。按一下底部的 [**新增**]，以建立新的應用程式註冊。

5. 按一下 [Add an application my organization is developing]。

6. 在 [新增應用程式精靈] 中，輸入應用程式的 [名稱]，然後按一下 [Web Application And/Or Web API] 類型。接著，按一下以繼續。

7. 在 [登入 URL] 方塊中，貼上您之前複製的應用程式 URL。在 [應用程式識別碼 URI] 方塊中輸入相同的 URL。接著，按一下以繼續。

8. 新增應用程式之後，按一下 [設定] 索引標籤。將 [單一登入] 下的 [回覆 URL] 編輯成您應用程式的 URL，並加上 _/.auth/login/aad/callback_ 路徑。例如：`https://contoso.azurewebsites.net/.auth/login/aad/callback`。請確實使用 HTTPS 配置。

    ![][3]
	
	
	> [AZURE.NOTE]如果您使用的是 App Service 閘道器，而不是App Service 驗證/授權功能，回覆 URL 會改用閘道器 URL 加上 _/signin-aad_ 路徑。


9. 按一下 [儲存]。然後，複製應用程式的 [**用戶端識別碼**]。稍後您會設定您的應用程式使用此資訊。

10. 在底部命令列中，按一下 [檢視端點]，然後複製 [同盟中繼資料文件] URL 並下載該文件，或在瀏覽器中瀏覽至該文件。

11. 在根 **EntityDescriptor** 項目中，應該會有 `https://sts.windows.net/` 格式的 **entityID** 屬性，後面是您的租用戶專屬的 GUID (稱為「租用戶識別碼」)。複製這個值，此值將做為您的「簽發者 URL」。稍後您會設定您的應用程式使用此資訊。

### <a name="secrets"> </a>將 Azure Active Directory 資訊新增至應用程式

> [AZURE.NOTE]如果您使用 App Service 閘道器，請忽略此章節，並改為在入口網站中瀏覽至您的閘道器。依序選取 [設定]、[身分識別]，[Azure Active Directory]。貼入 ClientID 並將租用戶識別碼新增至 [允許的租用戶] 清單。按一下 [儲存]。


13. 回到[預覽 Azure 管理入口網站]，並瀏覽至應用程式。依序按一下 [設定] 及 [驗證/授權]。

14. 如果 [驗證/授權] 功能未啟用，請切換到 [開]。

15. 按一下 [Azure Active Directory]，然後按一下 [管理模式] 下方的 [進階]。貼入您先前取得的用戶端識別碼和簽發者 URL 值。然後按一下 [確定]。

    ![][1]
	
	App Service 預設會提供驗證，但不會限制對您網站內容和 API 的已授權存取。您必須在應用程式程式碼中授權使用者。

17. (選擇性) 若要限制只有透過 Azure Active Directory 授權的使用者可以存取您的網站，請將 [要求未經驗證時所採取的動作] 設為 [Azure Active Directory]。這會要求所有要求都需經過驗證，且所有未經驗證的要求都會重新導向至 Azure Active Directory 以進行驗證。

17. 按一下 [儲存]。

現在，您已可在應用程式中使用 Azure Active Directory 進行驗證。

## <a name="related-content"> </a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[預覽 Azure 管理入口網站]: https://portal.azure.com/
[Azure 管理入口網站]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-xamarin-ios-aad-sso.md
[另一種方法]: #advanced

<!---HONumber=AcomDC_1125_2015-->