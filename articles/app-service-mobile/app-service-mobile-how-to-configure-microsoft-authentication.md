<properties
	pageTitle="如何為您的應用程式服務應用程式設定 Microsoft 帳戶驗證"
	description="了解如何為您的應用程式服務應用程式設定 Microsoft 帳戶驗證。"
	authors="mattchenderson"
	services="app-service"
	documentationCenter=""
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/24/2016"
	ms.author="mahender"/>

# 如何設定 App Service 應用程式以使用 Microsoft 帳戶登入

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題說明如何設定 Azure App Service，以使用 Microsoft 帳戶作為驗證提供者。

## <a name="register-windows-dev-center"> </a>在 Windows 開發人員中心註冊 Windows 應用程式

通用 Windows 應用程式和 Windows 市集應用程式必須透過 Windows 開發人員中心註冊。這能讓您在日後可更輕鬆地設定應用程式的推播通知。

>[AZURE.NOTE]略過 Windows Phone 8、Windows Phone 8.1 Silverlight 和非 Windows 的應用程式的章節。如果您已為 Windows 應用程式設定推播通知，也可以略過本節。

1. 登入 [Azure 入口網站]，然後瀏覽到您的應用程式。複製您的 **URL**。您將以此使用 Microsoft 帳戶來設定應用程式。

2. 如果您尚未註冊 App，請瀏覽至 [Windows 開發人員中心](https://dev.windows.com/dashboard/Application/New)，使用 Microsoft 帳戶登入，輸入 App 名稱並檢查是否可用，然後按一下 [保留應用程式名稱]。

3. 在 Visual Studio 開啟您的 Windows App 專案，然後在方案總管中以滑鼠右鍵按一下 Windows 市集 App 專案，按一下 [市集] > [將應用程式與市集建立關聯]。

  	![](./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-windows-store-association.png)

4. 在精靈中，按一下 [登入]，然後以您的 Microsoft 帳戶登入，選取您保留的應用程式名稱，按 [下一步] > [關聯]。若為 Windows 8.1 通用應用程式，請務必針對 Windows Phone 市集專案重複執行步驟 4 與 5。

6. 回到新 App 的 Windows 開發人員中心頁面，按一下 [服務] > [推播通知]。

7. 在 [推播通知] 頁面上，按一下 [Windows 推播通知服務 (WNS) 和 Microsoft Azure 行動服務] 下的 [線上服務網站]。

接著，請設定 Windows 應用程式的 Microsoft 帳戶驗證。


## <a name="register-microsoft-account"> </a>使用 Microsoft 帳戶註冊應用程式

如果已經在上一節註冊 Windows 應用程式，就可以跳到步驟 4。

1. 登入 [Azure 入口網站]，然後瀏覽到您的應用程式。複製您的 **URL**。您將以此使用 Microsoft 帳戶來設定應用程式。

2. 瀏覽到 Microsoft 帳戶開發人員中心的 [我的應用程式]頁面，並視需要使用您的 Microsoft 帳戶登入。

3. 按一下 [建立應用程式]、然後輸入 [應用程式名稱]，並按一下 [I accept]。

4. 按一下 [API 設定]，針對 [行動或桌面用戶端應用程式] 選取 [是]，提供應用程式的 **重新導向 URL**，然後按一下 [儲存]。
 
	![][0]

	>[AZURE.NOTE]您的重新導向 URI 是您的應用程式 URL 加上路徑 _/.auth/login/microsoftaccount/callback_。例如，`https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`。請確實使用 HTTPS 配置。

6. 按一下 [應用程式設定]，記下 [用戶端 ID] 和 [用戶端密碼] 的值。

    > [AZURE.IMPORTANT] 用戶端密碼是重要的安全性認證。請勿與任何人共用此用戶端密碼，或在用戶端應用程式中加以散發。

## <a name="secrets"> </a>將 Microsoft 帳戶資訊新增至 App Service 應用程式

1. 回到 [Azure 入口網站]，瀏覽至您的應用程式，按一下 [設定] > [驗證/授權]。

2. 如果 [驗證/授權] 功能未啟用，請切換至 [開啟]。

3. 按一下 [Microsoft 帳戶]。貼上先前取得的應用程式識別碼與應用程式密碼值，然後選擇性啟用應用程式需要的任何範圍。然後按一下 [確定]。

    ![][1]

	App Service 預設會提供驗證，但不會限制對您網站內容和 API 的已授權存取。您必須在應用程式程式碼中授權使用者。

4. (選擇性) 若要限制只有透過 Microsoft 帳戶授權的使用者可以存取您的網站，請將 [要求未經驗證時所採取的動作] 設為 [Microsoft 帳戶]。這會要求所有的要求都經過驗證，且所有未經驗證的要求會重新導向至 Microsoft 帳戶以進行驗證。

5. 按一下 [儲存]。

現在，您已可在應用程式中使用 Microsoft 帳戶進行驗證。

## <a name="related-content"> </a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure 入口網站]: https://portal.azure.com/

<!---HONumber=AcomDC_0302_2016-->