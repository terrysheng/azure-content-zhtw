<properties pageTitle="註冊單一登入 - Azure 行動服務" metaKeywords="" description="了解如何在 Azure Mobile Services 應用程式中註冊單一登入驗證。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store apps to use Windows Live Connect single sign-on" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# 註冊 Windows 市集應用程式以使用 Windows Live Connect 單一登入

本主題說明如何向 Windows 市集註冊您的應用程式，使其能夠以 Live Connect 做為 Azure 行動服務的身分識別提供者進行單一登入。若要使用推播通知，也必須執行此步驟。

<div class="dev-callout"><b>注意</b>
<p>您無須對 Windows 市集註冊您的應用程式，即可在發佈應用程式之前使用 Microsoft 帳戶進行驗證。當您的 Windows 市集應用程式不需要單一登入或推播通知時，您可以直接向 Live Connect 註冊應用程式以使用 Microsoft 帳戶登入。如需詳細資訊，請參閱 <a href="/zh-tw/develop/mobile/how-to-guides/register-for-microsoft-authentication">註冊 Windows 市集應用程式來使用 Microsoft 帳戶登入</a>。</p>
</div>

1. 如果您尚未註冊您的應用程式，請瀏覽至 Windows 市集應用程式之開發人員中心的[提交應用程式頁面]，使用您的 Microsoft 帳戶登入，然後按一下 [**應用程式名稱**]。

   	![][0]

2. 在 [**應用程式名稱**] 中為您的應用程式輸入名稱，然後依序按一下 [**保留應用程式名稱**] 和 [**儲存**]。

   	![][1]

   這會為您的應用程式建立新的 Windows 市集註冊。

3. 在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成[開始使用行動服務]教學課程時所建立的專案。

4. 在 [方案總管] 中，以滑鼠右鍵按一下此專案，然後依序按一下 [**市集**] 和 [**將應用程式與市集建立關聯...**]。 

  	![][2]

   	這將會顯示 [**將您的應用程式與 Windows 市集建立關聯**] 精靈。

5. 在此精靈中，按一下 [**登入**]，然後使用您的 Microsoft 帳戶登入。

6. 選取您在步驟 2 中註冊的應用程式，按 [**下一步**]，然後按一下 [**關聯**]。

   	![][3]

   	如此會將必要的 Windows 市集註冊資訊新增至應用程式資訊清單。    

9. 瀏覽到 Live Connect 開發人員中心的 [[我的應用程式]] 頁面，並在 [**我的應用程式**] 清單中按一下您的應用程式。

   	![][6] 

10. 依序按一下 [**編輯設定**]、[**API 設定**]，並記下 [**用戶端密碼**] 的值。 

   	![][7]

    <div class="dev-callout"><b>安全性注意事項</b>
	<p>用戶端密碼是重要的安全性認證。請勿將用戶端密碼與任何人分享，或與您的應用程式一起散發。</p>
    </div>

11. 在 [**重新導向網域**] 中，輸入您行動服務的 URL (取自步驟 8)，然後按一下 [**儲存**]。

現在，您已可使用 Live Connect 將驗證整合至您的應用程式中。行動服務提供了下列兩種透過 Live Connect 來驗證使用者的方法：

   - Windows 市集應用程式的單一登入。使用此方法時，使用者只需使用 Live Connect 在您的應用程式中授權驗證一次，後續認證即會根據使用者的喜好設定受到 Windows 的管理。如需詳細資訊，請參閱[使用 Live Connect 在 Windows 市集應用程式提供單一登入功能]。

   - 基本驗證。此方法支援多種驗證提供者，但需要使用者在每次啟動應用程式時進行登入。如需詳細資訊，請參閱[開始使用驗證] (英文)。

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png


[6]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png



<!-- URLs. -->
[使用 Live Connect 在 Windows 市集應用程式提供單一登入功能]: /zh-tw/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-dotnet
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-dotnet/
[使用指令碼來授權使用者]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript 和 HTML]: /zh-tw/develop/mobile/tutorials/get-started-with-users-js/
[Azure 管理入口網站]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
