<properties linkid="develop-mobile-how-to-guides-register-for-single-sign-on" urlDisplayName="Register for single sign on" pageTitle="Register for single sign-on - Azure Mobile Services" metaKeywords="" description="Learn how to register for single sign-on authentication in your Azure Mobile Services application." metaCanonical="" services="" documentationCenter="" title="Register your Windows Store apps to use Windows Live Connect single sign-on" authors="" solutions="" manager="" editor="" />

註冊 Windows 市集應用程式以使用 Windows Live Connect 單一登入
=============================================================

本主題說明如何向 Windows 市集註冊您的應用程式，使其能夠以 Live Connect 作為 Azure 行動服務的身分識別提供者進行單一登入。若要使用推播通知，也必須執行此步驟。

**注意**

您無須對 Windows 市集註冊您的應用程式，即可在發佈應用程式之前使用 Microsoft 帳戶進行驗證。當您的 Windows 市集應用程式不需要單一登入或推播通知時，您可以直接向 Live Connect 註冊應用程式以使用 Microsoft 帳戶登入。如需詳細資訊，請參閱[註冊 Windows 市集應用程式以使用 Microsoft 帳戶登入](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication)。

1.  如果您尚未註冊您的應用程式，請瀏覽至 Windows 市集應用程式之開發人員中心的[提交應用程式頁面](http://go.microsoft.com/fwlink/p/?LinkID=266582)，使用您的 Microsoft 帳戶登入，然後按一下 **[應用程式名稱]**。

	![](./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png)

2.  在 **[應用程式名稱]** 中為您的應用程式輸入名稱，然後依序按一下 **[保留應用程式名稱]** 和 **[儲存]**。

	![](./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png)

	如此會為您的應用程式建立新的 Windows 市集註冊。

3.  在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started)教學課程時所建立的專案。

4.  在 [方案總管] 中，以滑鼠右鍵按一下此專案，然後依序按一下 **[市集]** 和 **[將應用程式與市集建立關聯...]**。

	![](./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png)

	這將會顯示 **[將您的應用程式與 Windows 市集建立關聯]** 精靈。

5.  在此精靈中，按一下 **[登入]**，然後使用您的 Microsoft 帳戶登入。

6.  選取您在步驟 2 中註冊的應用程式，按 **[下一步]**，然後按一下 **[關聯]**。

	![](./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png)

	如此會將必要的 Windows 市集註冊資訊新增至應用程式資訊清單。    

7.  瀏覽到 Live Connect 開發人員中心的[我的應用程式](http://go.microsoft.com/fwlink/p/?LinkId=262039)頁面，並在 **[我的應用程式]** 清單中按一下您的應用程式。

	![](./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png)

8.  依序按一下 **[編輯設定]**、**[API 設定]**，並記下 **[用戶端密碼]** 的值。

	![](./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png)

    <div class="dev-callout"><b>	安全性注意事項</b>
    <p>	用戶端密碼是重要的安全性認證。 請勿將用戶端密碼與任何人分享，或與您的應用程式一起散發。</p>
    </div>

9.  在 **[重新導向網域]** 中，輸入您行動服務的 URL (取自步驟 8)，然後按一下 **[儲存]**。

現在，您已可使用 Live Connect 將驗證整合至您的應用程式中。行動服務提供了下列兩種透過 Live Connect 來驗證使用者的方法：

-   Windows 市集應用程式的單一登入。使用此方法時，使用者只需使用 Live Connect 在您的應用程式中授權驗證一次，後續認證即會根據使用者的喜好設定受到 Windows 的管理。如需詳細資訊，請參閱[使用 Live Connect 在 Windows 市集應用程式提供單一登入功能](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet)。

-   基本驗證。此方法支援多種驗證提供者，但需要使用者在每次啟動應用程式時進行登入。如需詳細資訊，請參閱[開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet) (英文)。


