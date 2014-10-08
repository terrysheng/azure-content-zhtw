在向 APNS 註冊應用程式及設定專案後，接下來您必須設定行動服務以整合 APNS。

1.  在 [Keychain Access] 中，於新憑證上按一下滑鼠右鍵、按一下 [匯出]、為 QuickstartPusher 檔案命名、選取 [.p12] 格式，然後按一下 [儲存]。

    ![][]

	記下匯出憑證的檔案名稱和位置。

> [WACOM.NOTE] 本教學課程將建立 QuickstartPusher.p12 檔案。Your file name and location might be different.

1.  登入 [Azure 管理入口網站][]，按一下 [行動服務]，然後按一下您的應用程式。

    ![][1]

2.  依序按一下 [推播] 索引標籤和 [上傳]。

    ![][2]

    [上傳憑證對話方塊] 隨即顯示。

3.  按一下 [檔案]、選取匯出的憑證 QuickstartPusher.p12 檔案、輸入 [密碼]、確認選取正確的 [模式]、按一下打勾圖示，然後按一下 [儲存]。

    ![][3]

    > [WACOM.NOTE] 本教學課程使用開發憑證。

您的行動服務現已設定為與 APNS 搭配運作。

<!-- URLs. -->

  []: ./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-apns-configure-push/mobile-services-selection.png
  [2]: ./media/mobile-services-apns-configure-push/mobile-push-tab-ios.png
  [3]: ./media/mobile-services-apns-configure-push/mobile-push-tab-ios-upload.png
