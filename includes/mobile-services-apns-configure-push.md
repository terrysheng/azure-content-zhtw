在向 APNS 註冊應用程式及設定專案後，接下來您必須設定行動服務以整合 APNS。

1. 在 Keychain Access 中，以滑鼠右鍵按一下 [**金鑰**] 或 [**我的憑證**]，按一下 [**匯出**]，將您的檔案命名為 QuickstartPusher，選取 **.p12** 格式，然後按一下 [**儲存**]。

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

  記下匯出憑證的檔案名稱和位置。

>[WACOM.NOTE]> [WACOM.NOTE] 本教學課程建立 QuickstartPusher.p12 檔案。您的檔案名稱和位置可能不同。

2. 登入 [[Azure 管理入口網站]]，按一下 [**行動服務**]，然後按一下您的應用程式。

   	![](./media/mobile-services-apns-configure-push/mobile-services-selection.png)

3. 按一下 [**推播**] 索引標籤，然後按一下 [**上傳**]。

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios.png)

	[上傳憑證對話方塊] 隨即顯示。

4. 按一下 [**檔案**]，選取匯出的憑證 QuickstartPusher.p12 檔案，輸入 [**密碼**]，確定正確的 [**模式**] (Dev/Sandbox 或 Prod/Production)，按一下核取圖示，然後按一下 [**儲存**]。

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios-upload.png)

    > [WACOM.NOTE] 本教學課程使用開發憑證。

您的行動服務現已設定為與 APNS 搭配運作。

<!-- URLs. -->
[Azure 管理入口網站]: https://manage.windowsazure.com/
