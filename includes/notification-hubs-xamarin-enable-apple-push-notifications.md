

若要透過 Apple Push Notification Service (APNS) 註冊要進行推播通知的應用程式，您必須在 Apple 開發人員入口網站上為專案建立新的推播憑證、應用程式識別碼和佈建設定檔。應用程式識別碼會包含可讓您的應用程式傳送及接收推播通知的組態設定。這些設定將會包含傳送及接收推播通知時，向 Apple Push Notification Service (APNS) 進行驗證所需的推播通知憑證。如需這些概念的詳細資訊，請參閱 [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584) 官方文件。


####產生推播憑證的憑證簽署要求檔案

這些步驟會引導您建立憑證簽署要求。這將會用來產生用於 APNS 的推播憑證。

1. 在您的 Mac 上，執行「鑰匙圈存取」工具。它可從 Launch Pad 上的 [公用程式] 資料夾或 [其他] 資料夾開啟。

2. 按一下 [Keychain Access]，並展開 [Certificate Assistant]，然後按一下 [Request a Certificate from a Certificate Authority...]。

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. 選取您的 [使用者電子郵件地址] 和 [一般名稱]，確定已勾選 [Saved to disk]，然後按一下 [繼續]。請將 [CA Email Address] 欄位留空，因為它不是必要資訊。

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. 在 [另存新檔] 中輸入憑證簽署要求 (CSR) 檔案的名稱，並且在 [位置] 中選取位置，然後按一下 [儲存]。

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

  	這會在選取的位置中儲存 CSR 檔案，預設的位置是在桌面上。請記住為檔案選擇的位置。


####針對推播通知註冊應用程式

為您的 Apple 應用程式建立新的明確應用程式識別碼，並同時為它設定推播通知。

1. 瀏覽至 Apple 開發人員中心的 [iOS 佈建入口網站](http://go.microsoft.com/fwlink/p/?LinkId=272456)，然後使用您的 Apple ID 登入，並按一下 [識別碼]，接著按一下 [應用程式識別碼]，最後按一下 [+] 符號註冊新的應用程式。

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. 將新應用程式的下列三個欄位予以更新，然後按一下 [繼續]：

	* **名稱**：在 [應用程式識別碼描述] 區段的 [名稱] 欄位中輸入應用程式的描述名稱。
	
	* **套件組合識別碼**：在 [明確的應用程式識別碼] 區段之下，以《[應用程式分發指南](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8)》中所提的 `<Organization Identifier>.<Product Name>` 形式輸入 [套件組合識別碼]。這必須符合也用於應用程式的 XCode 或 Xamarin 專案的識別碼。
	 
	* **推播通知**：勾選 [應用程式服務] 區段中的 [推播通知] 選項。

	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

3.	在 [確認應用程式識別碼] 畫面中檢閱設定，並於確認無誤後按一下 [提交]。

4. 	提交新的應用程式識別碼之後，您將看見 [註冊完成] 畫面。按一下 [完成]。

5. 在開發人員中心中，於 [App IDs] 下找出您剛才建立的 App ID，並在該列上按一下。按一下應用程式識別碼列，將顯示應用程式詳細資料。按一下底部的 [編輯] 按鈕。

6. 捲動到畫面底部，然後按一下 [Development Push SSL Certificate] 區段下方的 [Create Certificate...] 按鈕。

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

   	這將顯示 [新增 iOS 憑證] 助理。

    > [AZURE.NOTE]本教學課程使用開發憑證。註冊生產憑證時，將使用同一個程序。只要確定在傳送通知時使用相同的憑證類型即可。

7. 按一下 [選擇檔案]，瀏覽至您為推播憑證儲存 CSR 的位置。然後按一下 [產生]。

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

8. 在入口網站建立憑證之後，按一下 [下載] 按鈕。

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

   	這會下載簽署憑證，並將它儲存到您電腦中的 [下載] 資料夾。

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE]依預設，下載的檔案 (開發憑證) 的名稱會是 **aps\_development.cer**。

9. 按兩下下載的推播憑證 **aps\_development.cer**。這樣會將新的憑證安裝在金鑰鏈中，如下所示：

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE]憑證中的名稱可能會不同，不過字首會加上 **Apple Development iOS Push Services:** 前置詞。

10. 在 Keychain Access 中，以滑鼠右鍵按一下您剛剛在 [憑證] 類別中建立的新推播憑證。按一下 [匯出]、為檔案命名、選取 [.p12] 格式，然後按一下 [儲存]。

	請記住匯出的 .p12 推播憑證的檔案名稱和位置。透過在 Azure 傳統入口網站將它上傳，即可將它用來向 APNS 進行驗證。



####建立應用程式的佈建設定檔

1. 返回 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 佈建入口網站</a>，並依序選取 [**佈建設定檔**] 和 [**全部**]，然後按一下 [**+**] 按鈕建立新的設定檔。如此會啟動 **Add iOS Provisiong Profile** 精靈

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. 將 [Development] 下方的 [iOS App Development] 選為佈建設定檔類型，然後按一下 [Continue]。


3. 接著，從 [**應用程式識別碼**] 下拉式清單選取您剛建立的應用程式識別碼，然後按一下 [**繼續**]。

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. 在 [選取憑證] 畫面中，選取用於程式碼簽署的開發憑證，然後按一下 [繼續]。這是簽署憑證，不是您剛才建立的推播憑證。

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. 接著，選取要用來測試的 [裝置]，然後按一下 [繼續]

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. 最後，在 [設定檔名稱] 中為設定檔挑選名稱，按一下 [產生]。

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

<!-------HONumber=AcomDC_1210_2015--->