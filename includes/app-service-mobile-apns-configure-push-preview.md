向 APNS 註冊應用程式及設定專案後，接下來您必須設定行動應用程式以整合 APNS。

1. 在 [鑰匙圈存取] 中，以滑鼠右鍵按一下 [金鑰] 或 [我的憑證] 中快速啟動應用程式的新憑證，按一下 [匯出]，將檔案命名為 QuickstartPusher，選取 [.p12] 格式，然後按一下 [儲存]。

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

    記下匯出憑證的檔案名稱和位置。

>[AZURE.NOTE] 本教學課程會建立一個 QuickstartPusher.p12 檔案。您的檔案名稱和位置可能會有所不同。

2. 登入 [Azure 預覽入口網站][]，選取 [瀏覽]、[行動應用程式]，然後按一下您的應用程式。按一下 [推播通知] 服務。

3. 在 [Apple 推播通知服務] 中，上傳包含 **.p12** 檔案的憑證，您與其建立關聯的密碼，然後選取所需的模式。

您的 App Service 行動應用程式現已設定為搭配 APNS 使用。

<!-- URLs. -->
[Azure 預覽入口網站]: https://portal.azure.com/

<!--HONumber=49-->