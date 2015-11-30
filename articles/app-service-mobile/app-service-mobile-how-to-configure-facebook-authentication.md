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
	ms.date="10/29/2015"
	ms.author="mahender"/>

# 如何設定 App Service 應用程式以使用 Facebook 登入

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何設定 Azure App Service，以使用 Facebook 做為驗證提供者。

若要完成本主題的程序，您必須具有已通過電子郵件地址與手機號碼驗證的 Facebook 帳戶。若要建立新的 Facebook 帳戶，請前往 [facebook.com]。


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>向 Facebook 註冊您的應用程式

1. 登入 [Azure 管理入口網站]，並瀏覽至應用程式。複製您的 **URL**。您將使用此 URL 設定您的 Facebook 應用程式。
 
2. 在其他瀏覽器視窗中，瀏覽至 [Facebook 開發人員]網站，並以您的 Facebook 帳戶認證登入。

3. (選用) 按一下 [應用程式]，然後按一下 [Register as a Developer]，接受政策並遵循註冊步驟 (若您尚未註冊)。

4. 按一下 [我的應用程式]，然後按一下 [加入新的應用程式]。

5. 選取 [**網站**] 做為您的平台。選擇應用程式的唯一名稱，然後按一下 [**建立新的 Facebook 應用程式識別碼**]。

6. 從下拉式清單中選取應用程式的類別。然後按一下 [**建立應用程式識別碼**]。

7. 在下一個頁面上，選取右上角的 [**略過快速入門**]。這會將您帶到應用程式的開發人員儀表板。

8. 在 [**應用程式密鑰**] 欄位中，按一下 [**顯示**]、在系統要求時提供您的密碼，然後記下 [**應用程式識別碼**] 和 [**應用程式密鑰**] 的值。稍後您會設定您的應用程式以使用這些。

	> [AZURE.NOTE]**安全性注意事項** 用戶端密碼是重要的安全性認證。請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。

9. 在左側的導覽列上，按一下 [**設定**]。在 [應用程式網域] 中輸入您行動應用程式的 [URL]，並輸入 [連絡人電子郵件]。

    ![][0]

10. 如果您未看見下方的網站區段，請按一下 [**新增平台**]，然後選取 [**網站**]。在 [網站 URL] 欄位中輸入您行動應用程式的 [URL]，然後按一下 [儲存變更]。

11. 按一下 [進階] 索引標籤，然後將應用程式的**重新導向 URI** 加入 [有效的 OAuth 重新導向 URI]。然後按一下 [**儲存變更**]。您的重新導向 URI 是應用程式加上路徑 _/.auth/login/facebook/callback_ 的 URL。例如：`https://contoso.azurewebsites.net/.auth/login/facebook/callback`。請確實使用 HTTPS 配置。


	> [AZURE.NOTE]如果您使用的是 App Service 閘道器，而不是App Service 驗證 / 授權功能，重新導向 URL 會改用閘道器 URL 加上 _/signin-facebook_ 路徑。


12. 用來註冊應用程式的 Facebook 帳戶是應用程式的系統管理員。此時，只有系統管理員可以登入此應用程式。若要驗證其他 Facebook 帳戶，請按一下左側導覽列中的 [狀態和檢閱]。然後按一下 [是]，以啟用一般公用存取權。


## <a name="secrets"> </a>將 Facebook 資訊加入應用程式


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Facebook**. Paste in the values you obtained earlier and click **Save**.


13. 回到 [Azure 管理入口網站]，並瀏覽至應用程式。依序按一下 [設定] 及 [驗證 / 授權]。

14. 如果 [驗證 / 授權] 功能未啟用，請切換到 [開]。

15. 按一下 [Facebook]。貼上先前取得的應用程式識別碼與應用程式密碼值，然後選擇性啟用應用程式需要的任何範圍。然後按一下 [確定]。

    ![][1]
	
16. 根據預設，App Service 提供登入但不限制存取網站內容和應用程式開發介面，這是應用程式程式碼的責任。如果您想要站台完全受到 Facebook 登入的保護，請將 [要求未經驗證時所採取的動作] 下拉式清單變更成使用 [Facebook] 選項。這會要求所有要求都經過驗證，未經驗證的要求會重新導向至使用 Facebook 登入。

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
[Azure 管理入口網站]: https://portal.azure.com/

<!---HONumber=Nov15_HO4-->