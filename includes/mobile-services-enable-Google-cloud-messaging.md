
1. 瀏覽至 <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a> 網站，使用 Google 帳戶認證登入，然後按一下 [建立專案]****。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	>[AZURE.NOTE]如果您現已有專案，登入後會將您重新導向至 [專案]<strong></strong 頁面。若要從 [儀表板] 建立新的專案，請展開 [API 專案]<strong></strong>，並按一下 [其他專案]<strong></strong 下的 [建立...]<strong></strong，然後輸入專案名稱，並按一下 [建立專案]<strong></strong>。

2. 輸入專案名稱，接受服務條款，然後按一下 [建立]**。如果有要求，請執行簡訊驗證，再重新按一下 [**建立**]。

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

	您將使用此 API 金鑰值，讓行動服務能夠使用 GCM 進行驗證，並代表您的應用程式傳送推播通知。


<!--HONumber=47-->
