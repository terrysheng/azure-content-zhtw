Apple 推播通知服務 (APNS) 使用憑證來驗證您的行動服務。遵循這些指示建立必要的憑證，並將憑證上傳至您的行動服務。如需正式的 APNS 功能文件，請參閱 [Apple 推播通知服務][Apple 推播通知服務] (英文)。

## 產生憑證簽署要求檔案

首先，您必須產生憑證簽署要求 (CSR) 檔案，這將由 Apple 用來產生簽署的憑證。

1.  從 Utilities 資料夾中，執行 Keychain Access 工具。

2.  按一下 [Keychain Access]，並展開 [Certificate Assistant]，然後按一下 [Request a Certificate from a Certificate Authority...]。

    ![][0]

3.  選取您的 [使用者電子郵件地址] 和 [一般名稱]，確定已勾選 [Saved to disk]，然後按一下 [繼續]。請將 [CA Email Address] 欄位留空，因為它不是必要資訊。

    ![][1]

4.  在 [另存新檔] 中輸入憑證簽署要求 (CSR) 檔案的名稱，並且在 [位置] 中選取位置，然後按一下 [儲存]。

    ![][2]

    這會在選取的位置中儲存 CSR 檔案，預設的位置是在桌面上。請記住對於此檔案選擇的位置。

接下來，您要向 Apple 註冊應用程式、啟用推播通知，以及上傳這個匯出的 CSR 以建立推播憑證。

## 針對推播通知註冊應用程式

若要從行動服務將推播通知傳送至 iOS 應用程式，您必須向 Apple 註冊您的應用程式，並註冊進行推播通知。

1.  如果您尚未註冊應用程式，請瀏覽至 Apple 開發人員中心的 [iOS 佈建入口網站][iOS 佈建入口網站]，然後使用您的 Apple ID 登入，並按一下 [識別碼]，接著按一下 [App IDs]，最後按一下 **+** 號註冊新的應用程式。

    ![][3]

2.  在 [描述] 中輸入您應用程式的名稱，在 [Bundle Identifier] 中輸入 *MobileServices.Quickstart* 值，勾選 [應用程式服務] 區段中的 [推播通知] 選項，然後按一下 [繼續]。此範例會使用識別碼 **MobileServices.Quickstart**，但您可能不會重複使用此相同識別碼，因為應用程式識別碼在所有使用者中必須是唯一的。因此，建議您在應用程式名稱之後附加您的全名或縮寫。

    ![][4]

    這將產生您的應用程式識別碼，並要求您 [提交] 這個資訊。按一下 [提交]

    ![][5]

    按一下 [提交] 之後，您將看見 [註冊完成] 畫面，如下所示。按一下 [完成]。

    ![][6]

    > [WACOM.NOTE] 如果您選擇提供 *MobileServices.Quickstart* 以外的 [Bundle Identifier] 值，您也必須在 Xcode 專案中更新套件組合識別碼值。

3.  找出剛才建立的應用程式識別碼，並且按一下該資料列。

    ![][7]

    按一下應用程式 ID 將顯示應用程式和應用程式 ID 上的詳細資料。按一下 [設定] 按鈕。

    ![][8]

4.  捲動到畫面底部，然後按一下 [Development Push SSL Certificate] 區段下方的 [Create Certificate...] 按鈕。

    ![][9]

    這將顯示 [Add iOS Certificate] 助理。

    > [WACOM.NOTE] 本教學課程使用開發憑證。註冊生產憑證時，將使用同一個程序。您將憑證上傳至行動服務時，請確定設定相同的憑證類型。

5.  按一下 [選擇檔案]，瀏覽到您在第一個工作中所建立之 CSR 檔案的儲存位置，然後按一下 [產生]。

    ![][10]

6.  在入口網站建立憑證之後，依序按一下 [下載] 按鈕和 [完成]。

    ![][11]

    這會下載簽署憑證，並將它儲存到您電腦中的 [下載] 資料夾。

    ![][12]

    > [WACOM.NOTE] 依預設，下載的檔案 (開發憑證) 的名稱會是 **aps\_development.cer**。

7.  按兩下下載的推播憑證 **aps\_development.cer**。

    這樣會將新的憑證安裝在金鑰鏈中，如下所示：

    ![][13]

    > [WACOM.NOTE] 您的憑證中出現的名稱可能不同，但是前面都會加上 **Apple Development iOS Push Notification Services:**。

之後，您會使用該憑證來產生 .p12 檔案，並將該檔案上傳到行動服務以啟用 APNS 驗證。

## 建立應用程式的佈建設定檔

1.  返回 [iOS 佈建入口網站][iOS 佈建入口網站]，選取 [Provisioning Profiles]，並選取 [全部]，然後按一下 **+** 按鈕建立新的設定檔。如此會啟動 **Add iOS Provisiong Profile** 精靈

    ![][14]

2.  將 [Development] 下方的 [iOS App Development] 選為佈建設定檔類型，然後按一下 [繼續]

    ![][15]

3.  接著，從 [App ID] 下拉式清單選取行動服務快速入門應用程式的應用程式 ID，然後按一下 [繼續]

    ![][16]

4.  在 [Select certificates] 畫面中選取先前建立的憑證，然後按一下 [繼續]

    ![][17]

5.  接著，選取要用來測試的 [裝置]，然後按一下 [繼續]

    ![][18]

6.  最後，在 [Profile Name] 中為設定檔挑選名稱，然後依序按一下 [產生] 和 [Done]

    ![][19]

    這將建立新的佈建設定檔。

7.  在 Xcode 中開啟 Organizer 並選取 Devices 檢視，在左窗格的 [程式庫] 區段中選取 [Provisioning Profiles]，然後按一下中間窗格底部的 [重新整理] 按鈕。

    ![][20]

8.  在 [Targets] 下方按一下 [Quickstart]、展開 [Code Signing Identity]，然後在 [偵錯] 下方選取新的設定檔。

    ![][21]

這將確保 Xcode 專案使用新的設定檔進行程式碼簽署。接著，您必須將憑證上傳到 Azure。

  [Apple 推播通知服務]: http://go.microsoft.com/fwlink/p/?LinkId=272584
  [0]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step5.png
  [1]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step6.png
  [2]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step7.png
  [iOS 佈建入口網站]: http://go.microsoft.com/fwlink/p/?LinkId=272456
  [3]: ./media/enable-apple-push-notifications/mobile-services-ios-push-02.png
  [4]: ./media/enable-apple-push-notifications/mobile-services-ios-push-03.png
  [5]: ./media/enable-apple-push-notifications/mobile-services-ios-push-04.png
  [6]: ./media/enable-apple-push-notifications/mobile-services-ios-push-05.png
  [7]: ./media/enable-apple-push-notifications/mobile-services-ios-push-06.png
  [8]: ./media/enable-apple-push-notifications/mobile-services-ios-push-07.png
  [9]: ./media/enable-apple-push-notifications/mobile-services-ios-push-08.png
  [10]: ./media/enable-apple-push-notifications/mobile-services-ios-push-10.png
  [11]: ./media/enable-apple-push-notifications/mobile-services-ios-push-11.png
  [12]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step9.png
  [13]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step10.png
  [14]: ./media/enable-apple-push-notifications/mobile-services-ios-push-12.png
  [15]: ./media/enable-apple-push-notifications/mobile-services-ios-push-13.png
  [16]: ./media/enable-apple-push-notifications/mobile-services-ios-push-14.png
  [17]: ./media/enable-apple-push-notifications/mobile-services-ios-push-15.png
  [18]: ./media/enable-apple-push-notifications/mobile-services-ios-push-16.png
  [19]: ./media/enable-apple-push-notifications/mobile-services-ios-push-17.png
  [20]: ./media/enable-apple-push-notifications/mobile-services-ios-push-01.png
  [21]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step17.png
