

**注意**

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302)。

1.  導覽至 [Google 雲端主控台](http://cloud.google.com/console) (英文) 網站，並使用 Google 帳戶認證登入，然後按一下 **[建立專案]**。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	<div class="dev-callout"><b>注意</b>
	<p>如果您現已有專案，登入後會將您重新導向至 <strong>[專案[</strong> 頁面。若要從 [Dashboard] 建立新的專案，請展開 <strong>[API Project]</strong>，並按一下 <strong>]Other projects]</strong> 下的 <strong>[Create...]</strong>，然後輸入專案名稱，並按一下 <strong>[Create project]</strong>。</p>
    </div>


2.  輸入專案名稱，接受服務條款，然後按一下 **[建立]**。執行要求的簡訊驗證，然後再按一下 **[建立]**。

3.  記下 **[專案]** 區段中的專案號碼。

    在本教學課程的稍後內容中，您會設定這個值作為用戶端中的 PROJECT\_ID 變數。

4.  在左側欄位中按一下 **[APIs & auth]**，然後向下捲動並按一下切換開關以啟用 **[Google Cloud Messaging for Android]**，並接受服務條款。

    ![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5.  按一下 **[認證]**，然後按一下 **[CREATE NEW KEY]**

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6.  在 **[Create a new key]** 中，按一下 **[伺服器金鑰]**。在下一個視窗中，按一下 **[建立]**。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7.  記下 **[API key]** 值。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 


