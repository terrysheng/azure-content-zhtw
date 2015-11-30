<properties
	pageTitle="如何為您的應用程式服務應用程式設定 Twitter 驗證"
	description="了解如何為您的應用程式服務應用程式設定 Twitter 驗證。"
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

# 如何設定 App Service 應用程式以使用 Twitter 登入

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何設定 Azure App Service，以使用 Twitter 作為驗證提供者。

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Twitter 帳戶。若要建立新的 Twitter 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>。


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>向 Twitter 註冊您的應用程式


1. 登入 [Azure 管理入口網站]，並瀏覽至您的應用程式。複製您的 **URL**。您將使用此 URL 設定您的 Twitter 應用程式。

2. 瀏覽至 [Twitter Developers] 網站，使用您的 Twitter 帳戶認證登入，然後按一下 [**建立新的應用程式**]。

3. 針對您新的應用程式輸入**名稱**和**說明**。貼上您應用程式的 **URL** 作為**網站**值。然後，針對**回呼 URL**，貼上您之前複製的**回呼 URL**。這是您已附加路徑 _/.auth/login/twitter/callback_ 的行動應用程式閘道器。例如，`https://contoso.azurewebsites.net/.auth/login/twitter/callback`。請確實使用 HTTPS 配置。

    ![][0]
	

	> [AZURE.NOTE]如果您使用的是 App Service 閘道器，而非 App Service 驗證/授權功能，重新導向 URL 會改用閘道器 URL 加上 _/signin-twitter_ 路徑。


3.  在頁面底部，閱讀並接受條款。然後按一下 [**建立 Twitter 應用程式**]。這會註冊應用程式並顯示應用程式詳細資料。

4. 按一下 [ **設定**] 索引標籤，勾選 [**允許此應用程式用來以 Twitter 登入**]，然後按一下 [**更新設定**]。

5. 選取 [**金鑰和存取權杖**] 索引標籤。記下 [**消費者金鑰 (API 金鑰)**] 和 [**消費者密鑰 (API 密鑰)**] 的值。

    > [AZURE.NOTE]消費者密碼是重要的安全性認證。請勿將此密碼告訴任何人或隨應用程式一起散發。


## <a name="secrets"> </a>將 Twitter 資訊新增至應用程式


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Twitter**. Paste in the values you obtained earlier and click **Save**.


13. 回到 [Azure 管理入口網站]，並瀏覽至應用程式。依序按一下 [**設定**] 及 [**驗證/授權**]。

14. 如果 [驗證/授權] 功能未啟用，請切換到 [**開**]。

15. 按一下 [**Twitter**]。貼入您先前取得的應用程式識別碼和應用程式密鑰值。然後按一下 [確定]。

    ![][1]
	
16. 根據預設，App Service 提供登入但不限制存取您的網站內容和 API，這是應用程式程式碼的責任。如果您想要網站完全受到 Twitter 登入的保護，請將 [**要求未經驗證時所採取的動作**] 下拉式清單變更成使用 [**Twitter**] 選項。這會要求對所有要求進行驗證，系統會將未經驗證的要求重新導向至使用 Twitter 帳戶登入。

17. 按一下 [儲存]。

現在，您已可在應用程式中使用 Twitter 進行驗證。

## <a name="related-content"> </a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure 管理入口網站]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md

<!---HONumber=Nov15_HO4-->