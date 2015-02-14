


1. 導覽至 <a href="http://cloud.google.com/console" target="_blank">Google 雲端主控台</a> (英文) 網站，並使用 Google 帳戶認證登入，然後按一下 **[建立專案]**。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	>[AZURE.NOTE]如果您已經有現有專案，登入後會將您重新導向至 [專案]<strong></strong> 頁面。若要從 [儀表板] 建立新的專案，請展開 [API 專案]<strong></strong>，按一下 [其他專案]<strong></strong> 下的 [建立...]<strong></strong>，然後輸入專案名稱，再按一下 [建立專案]<strong></strong>。

2. 輸入專案名稱，接受服務條款，然後按一下 **[建立]**。如果您要求，請執行簡訊驗證，然後再按一次 **[建立]**。

3. 記下 **[專案]** 區段中的專案號碼。 

	在本教學課程的稍後內容中，您會設定這個值作為用戶端中的 PROJECT_ID 變數。

4. 在左側欄位中按一下 **[APIs & auth]**，然後向下捲動並按一下切換開關以啟用 **[Google Cloud Messaging for Android]**，並接受服務條款。 

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. 按一下 **[認證]**，然後按一下 **[建立新的金鑰]** 

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. 在 **[建立新的金鑰]]** 中，按一下 **[伺服器金鑰]**。在下一個視窗中，按一下 **[建立]**。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. 記下 **[API KEY]** 值。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 

	您將使用此 API 金鑰值，讓行動服務能夠使用 GCM 進行驗證，並代表您的應用程式傳送推播通知。

<!--HONumber=42-->
