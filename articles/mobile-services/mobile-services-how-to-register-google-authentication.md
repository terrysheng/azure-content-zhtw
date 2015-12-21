<properties
	pageTitle="註冊 Google 驗證 | Microsoft Azure"
	description="了解如何在 Azure 行動服務中註冊您的應用程式以使用 Google 進行驗證。"
	services="mobile-services"
	documentationCenter="android"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>


<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/30/2015" 
	ms.author="glenga"/>

# 在行動服務中註冊您的應用程式以進行 Google 登入

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

本主題說明如何在 Azure 行動服務中註冊您的應用程式，使其能夠採用 Google 驗證。

>[AZURE.NOTE]本教學課程有關 [Azure 行動服務](http://azure.microsoft.com/services/mobile-services/)，此方案可協助您建置適用於任何平台的可擴充行動應用程式。行動服務讓同步處理資料、驗證使用者及推播通知等作業變得簡單。此頁面是[開始使用驗證](mobile-services-ios-get-started-users.md)教學課程的輔助，說明如何將使用者登入您的應用程式。<br/>如果這是您第一次使用行動服務，請完成[開始使用行動服務](mobile-services-ios-get-started.md)教學課程。

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。

3. 瀏覽至 [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) (英文) 網站，以您的 Google 帳戶認證登入，按一下 [Create Project]，提供「專案名稱」，然後按一下 [Create]。

4. 在 [產品與服務] 下拉式清單中，按一下 [API 管理員]，然後在 [社交平台類 API] 底下，按一下 [Google+ API] > [啟用 API]。

5. 按一下 [認證] > [OAuth 同意畫面]，然後選取您的**電子郵件地址**，輸入**產品名稱**，再按一下 [儲存]。

6. 在 [認證] 索引標籤中，按一下 [新增認證] > [OAuth 2.0 用戶端識別碼]，然後選取 [Web 應用程式]。


7. 在 [授權的 JavaScript 來源] 中輸入您的行動服務 URL、使用下列其中一種 URL 格式取代 [授權重新導向 URI] 中所產生的 URL，然後按一下 [建立]：
 

	+ **.NET 後端**：`https://<mobile_service>.azure-mobile.net/signin-google`
	+ **JavaScript 後端**：`https://<mobile_service>.azure-mobile.net/login/google`

	 >[AZURE.NOTE]請確定針對您的行動服務後端類型使用正確的重新導向 URL 路徑格式。若格式不正確，驗證將不會成功。

8. 在下一個畫面上，記下用戶端識別碼和用戶端密碼的值。

    > [AZURE.IMPORTANT]用戶端密碼是重要的安全性認證。請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。

您現在已經準備好設定您的行動服務來使用 Google 登入，以在您的應用程式中進行驗證。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

<!---HONumber=AcomDC_1210_2015-->