<properties
	pageTitle="如何為您的應用程式服務應用程式設定 Facebook 驗證"
	description="了解如何為您的應用程式服務應用程式設定 Facebook 驗證。"
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
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

# 如何設定 App Service 應用程式以使用 Facebook 登入

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何設定 Azure App Service，以使用 Facebook 做為驗證提供者。

若要完成本主題的程序，您必須具有已通過電子郵件地址與手機號碼驗證的 Facebook 帳戶。若要建立新的 Facebook 帳戶，請前往 [facebook.com]。

> [AZURE.NOTE]本主題示範 App Service 驗證/授權功能的用法。這會取代大部分應用程式的 App Service 閘道器。整個主題中使用閘道器所產生的差異都列在注意事項中。


## <a name="register"> </a>向 Facebook 註冊您的應用程式

1. 登入 [Azure 入口網站]，然後瀏覽到您的應用程式。複製您的 **URL**。您將使用此 URL 設定您的 Facebook 應用程式。
 
2. 在其他瀏覽器視窗中，瀏覽至 [Facebook 開發人員]網站，並以您的 Facebook 帳戶認證登入。

3. (選用) 按一下 [應用程式] > [以開發人員身分註冊]，接受政策並遵循註冊步驟 (若您尚未註冊)。

4. 按一下 [我的應用程式] > [加入新的應用程式] > [網站]，輸入應用程式的唯一名稱，然後按一下 [建立新的 Facebook 應用程式識別碼]。

6. 從下拉式清單挑選應用程式類別，接著按一下 [建立應用程式識別碼]，然後在下一個頁面按一下 [略過快速啟動]。這會將您帶到應用程式的開發人員儀表板。

8. 在 [**應用程式密鑰**] 欄位中，按一下 [**顯示**]、在系統要求時提供您的密碼，然後記下 [**應用程式識別碼**] 和 [**應用程式密鑰**] 的值。稍後您會設定您的應用程式以使用這些。

	> [AZURE.NOTE]**安全性注意事項** 用戶端密碼是重要的安全性認證。請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。

9. 在左側的導覽列按一下 [設定]，在 [應用程式網域] 中輸入行動應用程式的 [URL]，接著輸入 [連絡人電子郵件]。

    ![][0]

10. 如果您看不到下方的網站區段，請按一下 [新增平台] > [網站]，在 [網站 URL] 欄位中輸入行動應用程式的 [URL]，然後按一下 [儲存變更]。

11. 按一下 [進階] 索引標籤，將應用程式的**重新導向 URI** 加入 [有效的 OAuth 重新導向 URI]，然後按一下 [儲存變更]。您的重新導向 URI 是應用程式 URL 加上路徑 _/.auth/login/facebook/callback_。例如：`https://contoso.azurewebsites.net/.auth/login/facebook/callback`。請確實使用 HTTPS 配置。


> [AZURE.NOTE]如果您使用的是 App Service 閘道器，而不是App Service 驗證/授權功能，重新導向 URL 會改用閘道器 URL 加上 _/signin-facebook_ 路徑。


12. 用來註冊應用程式的 Facebook 帳戶是應用程式的系統管理員。此時，只有系統管理員可以登入此應用程式。若要驗證其他 Facebook 帳戶，請按一下左側導覽列中的 [狀態和檢閱]。然後按一下 [是]，以啟用一般公用存取權。


## <a name="secrets"> </a>將 Facebook 資訊加入應用程式

> [AZURE.NOTE]如果您使用 App Service 閘道器，請忽略此章節，並改為在入口網站中瀏覽至您的閘道器。依序選取 [設定]、[身分識別]、[Facebook]。貼入您之前取得的值，然後按一下 [儲存]。


13. 回到 [Azure 入口網站]，並瀏覽到您的應用程式。按一下 [設定] > [驗證/授權]，並確定 [App Service 驗證] 為 [開啟]。

15. 按一下 [Facebook]，貼上先前取得的應用程式識別碼與應用程式密碼值，選擇性啟用應用程式需要的任何範圍，然後按一下 [確定]。

    ![][1]
	
	App Service 預設會提供驗證，但不會限制對您網站內容和 API 的已授權存取。您必須在應用程式程式碼中授權使用者。

17. (選擇性) 若要限制只有透過 Facebook 授權的使用者可以存取您的網站，請將 [要求未經驗證時所採取的動作] 設為 [Facebook]。這會要求所有的要求都經過驗證，且所有未經驗證的要求會重新導向至 Facebook 以進行驗證。

17. 按一下 [儲存]。

現在，您已可在應用程式中使用 Facebook 進行驗證。

## <a name="related-content"> </a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook 開發人員]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /zh-TW/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure 入口網站]: https://portal.azure.com/

<!----HONumber=AcomDC_1203_2015-->