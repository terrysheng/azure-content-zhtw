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
	ms.date="02/28/2016"
	ms.author="mahender"/>

# 如何設定 App Service 應用程式以使用 Facebook 登入

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題說明如何設定 Azure App Service，以使用 Facebook 做為驗證提供者。

若要完成本主題的程序，您必須具有已通過電子郵件地址與手機號碼驗證的 Facebook 帳戶。若要建立新的 Facebook 帳戶，請前往 [facebook.com]。

## <a name="register"> </a>向 Facebook 註冊您的應用程式

1. 登入 [Azure 入口網站]，然後瀏覽到您的應用程式。複製您的 **URL**。您將使用此 URL 設定您的 Facebook 應用程式。

2. 在其他瀏覽器視窗中，瀏覽至 [Facebook 開發人員]網站，並以您的 Facebook 帳戶認證登入。

3. (選用) 按一下 [應用程式] > [以開發人員身分註冊]，接受政策並遵循註冊步驟 (若您尚未註冊)。

4. 按一下 [我的應用程式] > [加入新的應用程式] > [網站] > [略過並建立應用程式識別碼]。

5. 在 [顯示名稱] 中輸入應用程式的唯一名稱，選擇應用程式的 [類別]，然後按一下 [建立應用程式識別碼] 並完成安全性檢查。這會將您帶到開發人員儀表板來設定新的 Facebook 應用程式。

6. 在 [**應用程式密鑰**] 欄位中，按一下 [**顯示**]、在系統要求時提供您的密碼，然後記下 [**應用程式識別碼**] 和 [**應用程式密鑰**] 的值。稍後您會在 Azure 中使用這些資訊來設定您的應用程式。

	> [AZURE.IMPORTANT] 應用程式密鑰是重要的安全性認證。請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。

7. 在左側的導覽列按一下 [設定]，在 [應用程式網域] 中輸入行動應用程式的 [URL]，接著輸入 [連絡人電子郵件]。

    ![][0]

8. 如果您看不到下方的網站區段，請按一下 [新增平台] > [網站]，在 [網站 URL] 欄位中輸入行動應用程式的 [URL]，然後按一下 [儲存變更]。

9. 按一下 [進階] 索引標籤，將應用程式的**重新導向 URI** 加入 [有效的 OAuth 重新導向 URI]，然後按一下 [儲存變更]。

	> [AZURE.NOTE] 您的重新導向 URI 是應用程式 URL 加上路徑 _/.auth/login/facebook/callback_。例如，`https://contoso.azurewebsites.net/.auth/login/facebook/callback`。請確實使用 HTTPS 配置。

10. 用來註冊應用程式的 Facebook 帳戶是應用程式的系統管理員。此時，只有系統管理員可以登入此應用程式。若要驗證其他 Facebook 帳戶，請按一下 [應用程式檢閱] 並啟用 [公開 todolist-complete-nodejs]，以允許使用 Facebook 驗證來公開存取。


## <a name="secrets"> </a>將 Facebook 資訊加入應用程式

1. 回到 [Azure 入口網站]，並瀏覽到您的應用程式。按一下 [設定] > [驗證/授權]，並確定 [App Service 驗證] 為 [開啟]。

2. 按一下 [Facebook]，貼上先前取得的應用程式識別碼與應用程式密碼值，選擇性啟用應用程式需要的任何範圍，然後按一下 [確定]。

    ![][1]

	App Service 預設會提供驗證，但不會限制對您網站內容和 API 的已授權存取。您必須在應用程式程式碼中授權使用者。

3. (選擇性) 若要限制只有透過 Facebook 授權的使用者可以存取您的網站，請將 [要求未經驗證時所採取的動作] 設為 [Facebook]。這會要求所有的要求都經過驗證，且所有未經驗證的要求會重新導向至 Facebook 以進行驗證。

4. 設定驗證完成時，按一下 [儲存]。

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

<!---HONumber=AcomDC_0302_2016-->