

>[AZURE.NOTE]若要完成此程序，您必須要有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。


1. 瀏覽至 <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a> 網站，使用 Google 帳戶認證登入，然後按一下 [**建立專案**]。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	>[AZURE.NOTE]當您已經有現有的專案時，您會在登入之後，被導向至 <strong>Projects</strong> 頁面。若要從儀表板建立新的專案，展開 <strong>API Project</strong>, 按一下 <strong>Create...</strong> (在 <strong>Other projects</strong 下方)>，然後輸入專案名稱，再按一下 <strong>Create project</strong>。

2. 輸入專案名稱，接受服務條款，然後按一下 [**建立**]。如果有要求，請執行簡訊驗證，再重新按一下 [**建立**]。

3. 記下 [**專案**] 區段中的專案號碼。 

	在本教學課程的稍後內容中，您會設定這個值作為用戶端中的 PROJECT_ID 變數。

4. 在左欄中，展開 [**API 與驗證**]，按一下 [**API**]，然後向下捲動並按一下切換開關以啟用 [**Google Cloud Messaging for Android**] 並接受服務條款。 

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. 按一下 [**認證**]，然後按一下 [**建立新的金鑰**] 

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. 在 [**建立新的金鑰**] 中，按一下 [**伺服器金鑰**]。在下一個視窗中，按一下 [**建立**]。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. 記下 [**API 金鑰**] 值。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 

	您將使用此 API 金鑰值，讓 Azure 能夠使用 GCM 進行驗證，並代表您的應用程式傳送推播通知。


<!--HONumber=49-->