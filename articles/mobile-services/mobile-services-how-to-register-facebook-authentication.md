<properties
	pageTitle="註冊 Facebook 驗證 | Azure 行動服務"
	description="了解如何在 Azure 行動服務應用程式中使用 Facebook 驗證。"
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/28/2016"
	ms.author="glenga"/>

# 在行動服務中註冊應用程式以採用 Facebook 驗證

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]&nbsp;


[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

本主題說明如何在 Azure 行動服務中註冊您的應用程式，使其能夠採用 Facebook 驗證。此頁面支援[開始使用驗證](mobile-services-ios-get-started-users.md)教學課程，顯示如何將使用者登入您的應用程式。如果這是您第一次使用行動服務，請完成[開始使用行動服務](mobile-services-ios-get-started.md)教學課程。

若要完成本主題的程序，您必須具有已通過電子郵件地址與手機號碼驗證的 Facebook 帳戶。若要建立新的 Facebook 帳戶，請前往 [facebook.com](http://go.microsoft.com/fwlink/p/?LinkId=268285)。

1. 瀏覽至 [Facebook 開發人員](http://go.microsoft.com/fwlink/p/?LinkId=268285)網站，並以您的 Facebook 帳戶認證登入。

2. (選用) 按一下 [我的應用程式]，然後按一下 [註冊為開發人員]，接受政策並遵循註冊步驟 (若您尚未註冊)。

3. 按一下 [我的應用程式] > [加入新的應用程式] > [網站] > [略過並建立應用程式識別碼]。

4. 在 [顯示名稱] 中輸入應用程式的唯一名稱，選擇應用程式的 [類別]，然後按一下 [建立應用程式識別碼] 並完成安全性檢查。這會將您帶到開發人員儀表板來設定新的 Facebook 應用程式。

5. 在 [**應用程式密鑰**] 欄位中，按一下 [**顯示**]、在系統要求時提供您的密碼，然後記下 [**應用程式識別碼**] 和 [**應用程式密鑰**] 的值。稍後您會在 Azure 中使用這些資訊來設定您的應用程式。

	> [AZURE.NOTE] **安全性注意事項** 用戶端密碼是重要的安全性認證。請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。

5. 在左側導覽列中，按一下 [設定]，在 [應用程式網域] 中輸入您行動服務的網域，輸入選用 [連絡人電子郵件]，然後按一下 [新增平台] 並選取 [網站]。

   	![][3]

6. 在 [網站 URL] 中輸入您行動服務的 URL，然後按一下 [儲存變更]。

7. 按一下 [顯示]，提供您的密碼 (如有要求)，然後記下 [應用程式 ID] 和 [應用程式密鑰] 的值。

   	![][5] &nbsp;

    >[AZURE.IMPORTANT] 應用程式密鑰是重要的安全性認證。請勿將此密碼告訴任何人或隨應用程式一起散發。&nbsp;

8. 按一下 [進階] 索引標籤，在 [有效的 OAuth 重新導向 URl] 中輸入下列其中一個 URL，然後按一下 [儲存變更]：

	+ **.NET 後端**：`https://<mobile_service>.azure-mobile.net/signin-facebook`
	+ **JavaScript 後端**：`https://<mobile_service>.azure-mobile.net/login/facebook`

	 >[AZURE.NOTE]針對您的行動服務後端類型，請使用 *HTTPS* 結構描述，確定您使用正確的重新導向 URL 路徑格式。若格式不正確，驗證將不會成功。


12. 用來註冊應用程式的 Facebook 帳戶是應用程式的系統管理員。此時，只有系統管理員可以登入此應用程式。若要驗證其他 Facebook 帳戶，請按一下 [應用程式檢閱] 並啟用 [公開 todolist-complete-nodejs]，以允許使用 Facebook 驗證來公開存取。

現在您已準備好提供應用程式 ID 和應用程式密碼值給行動服務，以在您的應用程式中採用 Facebook 登入驗證。

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Mobile Services]: http://azure.microsoft.com/services/mobile-services/

<!---HONumber=AcomDC_0302_2016-->