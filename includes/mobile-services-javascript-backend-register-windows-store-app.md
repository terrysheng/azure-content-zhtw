

1.  如果您尚未註冊您的應用程式，請瀏覽至 Windows 市集應用程式之開發人員中心的[提交應用程式頁面](http://go.microsoft.com/fwlink/p/?LinkID=266582)，使用您的 Microsoft 帳戶登入，然後按一下 **[應用程式名稱]**。

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-submit-win8-app.png)

2.  在 **[應用程式名稱]** 中為您的應用程式輸入名稱，然後依序按一下 **[保留應用程式名稱]** 和 **[儲存]**。

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-name.png)

   	"如此會為您的應用程式建立新的 Windows 市集註冊。"

3.  在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成[開始使用行動服務](/en-us/documentation/articles/mobile-services-windows-store-get-started/)教學課程時所建立的專案。

4.  在 [方案總管] 中，以滑鼠右鍵按一下此專案，然後依序按一下 **[市集]** 和 **[將應用程式與市集建立關聯...]**。

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-store-association.png)

   	 這將會顯示 **[將您的應用程式與 Windows 市集建立關聯]** 精靈。

1.  在此精靈中，按一下 **[登入]**，然後使用您的 Microsoft 帳戶登入。

2.  選取您在步驟 2 中註冊的應用程式，按 **[下一步]**，然後按一下 **[關聯]**。

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-select-app-name.png)

   	“如此會將必要的 Windows 市集註冊資訊新增至應用程式資訊清單。”    

3.  回到新應用程式的 Windows 開發人員中心頁面，按一下 **[服務]**。

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit-app.png) 

4.  在 [服務] 頁面中，按一下 **[Azure 行動服務]** 下的 **[Live Services site]**。

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

5.  按一下 **[驗證您的服務]**，並記下 **[用戶端密碼]** 和 **[封裝安全性識別碼 (SID)]** 的值。

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    **安全性注意事項**

    用戶端密碼和封裝 SID 是重要的安全性認證。請勿與任何人共用這些密碼，或與您的應用程式一起散發密碼。

6.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的應用程式。

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-selection.png)

1.  依序按一下 **[推播]** 索引標籤、**[Enable enhanced push]** 和 **[是]**，以接受組態變更。

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-enable-enhanced-push.png)

    這會更新行動服務的組態，以使用通知中心所提供的增強式推播通知功能。您可在付費的行動服務中免費使用部分通知中心功能。如需詳細資訊，請參閱[行動服務定價詳細資料](http://go.microsoft.com/fwlink/p/?LinkID=311786)。

    **重要事項**

    此作業會重設您的推播認證，並在您的指令碼中變更推播方法的行為。這些變更無法回復。請不要使用此方法將通知中心新增至生產行動服務。如需如何在生產行動服務中啟用增強式推播通知的指引，請參閱[此指引](http://go.microsoft.com/fwlink/p/?LinkId=391951)。

2.  輸入您在步驟 4 中從 WNS 取得的 **[用戶端密碼]** 和 **[封裝 SID]** 值，然後按一下 **[儲存]**。

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-push-tab.png)

	>[WACOM.NOTE]When you set your WNS credentials for enhanced push notifications in the **Push** tab in the portal, they are shared with Notification Hubs to configure the notification hub with your app.
