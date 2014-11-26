1.  導覽至 [Google 雲端主控台][Google 雲端主控台] (英文) 網站，並使用 Google 帳戶認證登入，然後按一下 [建立專案]。

    ![][0]

    > [WACOM.NOTE]如果擁有現有的專案，系統會在您登入後將您引導至 [專案] 頁面。若要從 [儀表板] 建立新的專案，請展開 [API Project]，並按一下 [其他專案] 下的 [建立]，然後輸入專案名稱，並按一下 [建立專案]。

2.  輸入專案名稱，接受服務條款，然後按一下 [建立]。執行要求的簡訊驗證，然後再按一下 [建立]。

3.  記下 [專案] 區段中的專案號碼。

    在本教學課程的稍後內容中，您會設定這個值作為用戶端中的 PROJECT\_ID 變數。

4.  在左側欄位中按一下 [APIs & auth]，然後向下捲動並按一下切換開關以啟用 [Google Cloud Messaging for Android]，並接受服務條款。

    ![][1]

5.  按一下 [認證]，然後按一下 [CREATE NEW KEY]

    ![][2]

6.  在 [Create a new key] 中，按一下 [伺服器金鑰]。在下一個視窗中，按一下 [建立]。

    ![][3]

7.  記下 [API key] 值。

    ![][4]

    您將使用此 API 金鑰值，讓行動服務能夠使用 GCM 進行驗證，並代表您的應用程式傳送推播通知。

  [Google 雲端主控台]: http://cloud.google.com/console
  [0]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
  [1]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
  [2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
  [3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
  [4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
