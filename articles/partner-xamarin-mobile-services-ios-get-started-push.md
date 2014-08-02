<properties linkid="develop-mobile-tutorials-get-started-with-push-xamarin-ios" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications (Xamarin.iOS) - Mobile Services" metaKeywords="" description="Learn how to use push notifications in Xamarin.iOS apps with Azure Mobile Services." metaCanonical="" disqusComments="0" umbracoNaviHide="1" editor="mollybos" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="" />

開始在行動服務中使用推播通知
============================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js "Windows 市集 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

本主題說明如何使用 Azure 行動服務傳送推播通知至 Xamarin.iOS 應用程式。在本教學課程中，您會使用 Apple 推播通知服務 (APNS) 將推播通知新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。

**注意**

本教學課程示範將推播通知裝置權杖附加在已插入的記錄中，藉此傳送推播通知的簡易方法。請務必遵循接下來的教學課程，以深入瞭解如何將推播通知納入真實世界的應用程式中。

本教學課程會逐步引導您完成啟用推播通知的基本步驟：

1.  [產生憑證簽署要求](#certificates)
2.  [註冊應用程式並啟用推播通知](#register)
3.  [建立應用程式的佈建設定檔](#profile)
4.  [設定行動服務](#configure)
5.  [新增推播通知至應用程式](#add-push)
6.  [更新指令碼來傳送推播通知](#update-scripts)
7.  [插入資料以接收通知](#test)

本教學課程需要下列各項：

-   [XCode 5.0](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   支援 iOS 5.0 (或更新版本) 的裝置
-   iOS 開發人員計劃成員資格
-   [Xamarin.iOS]
-   [Azure 行動服務元件](http://components.xamarin.com/view/azure-mobile-services/)

    **注意**

    因為推播通知組態需求，您必須在支援 iOS 的裝置 (iPhone 或 iPad) 上 (而非在模擬器中) 部署與測試推播通知。

本教學課程是以行動服務快速入門為基礎。開始此教學課程之前，您必須先完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started-xamarin-ios)。

Apple 推播通知服務 (APNS) 會使用憑證來驗證行動服務。依照這些指示進行，以建立必要的憑證並上傳至行動服務。如需官方的 APNS 功能文件，請參閱 [Apple 推播通知服務](http://go.microsoft.com/fwlink/p/?LinkId=272584) (英文)。

產生 CSR 檔案產生憑證簽署要求檔案
---------------------------------

首先，您必須產生 Apple 用來產生簽署憑證的憑證簽署要求 (CSR) 檔案。

1.  從 [公用程式] 資料夾中執行 [Keychain Access] 工具。

2.  按一下 **[Keychain Access]**、展開 **[Certificate Assistant]**，然後按一下 **[Request a Certificate from a Certificate Authority...]**。

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png)

1.  選取 **[User Email Address]**、輸入 **[一般名稱]** 和 **[CA Email Address]** 值，確認 **[Saved to disk]** 已選取，然後按一下 **[繼續]**。

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png)

1.  在 **[另存新檔]** 中輸入憑證簽署要求 (CSR) 檔案的名稱，在 **[Where]** 中選取位置，然後按一下 **[儲存]**。

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png)

	這會在選取的位置中儲存 CSR 檔案，預設的位置是在桌面上。請記住為檔案選擇的位置。

接下來，您要向 Apple 註冊應用程式、啟用推播通知，以及上傳這個匯出的 CSR 以建立推播憑證。

註冊應用程式註冊應用程式以取得推播通知
--------------------------------------

為了要從行動服務將推播通知傳送給 iOS 應用程式，您必須向 Apple 註冊應用程式，以及針對推播通知註冊應用程式。

1.  如果您尚未註冊應用程式，請瀏覽至 Apple Developer Center 的 [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456)、登入 Apple ID、按一下 **[Identifiers]**、按一下 **[App IDs]**，最後再按一下 **[+]** 以登入。

	![][102] 

2.  在 **[描述]** 中輸入您應用程式的名稱，在 **[Bundle Identifier]** 中輸入 *MobileServices.Quickstart* 值，勾選 [應用程式服務] 區段中的 [推播通知] 選項，然後按一下 **[繼續]**。此範例會使用識別碼 **MobileServices.Quickstart**，但您可能不會重複使用此相同識別碼，因為應用程式識別碼在所有使用者中必須是唯一的。因此，建議您在應用程式名稱之後附加您的全名或縮寫。

	![][103]
           
	This generates your app ID and requests you to **Submit** the information.Click **Submit**
           
	![][104] 
           
	Once you click **Submit**, you will see the **Registration complete** screen, as shown below.Click **Done**.
           
	![][105]

    注意：如果您選擇提供 **[Bundle Identifier]** 值 (而非 *MobileServices.Quickstart*)，還必須更新 Xcode 專案中的套件組合識別碼值。

3.  尋找您剛才所建立的應用程式識別碼，並按一下該列。

	![][106]
           
	Clicking on the app ID will display details on the app and app ID. Click the **Settings** button.
           
	![][107] 

4.  捲動到畫面底部，然後按一下 **[Development Push SSL Certificate]** 區段下方的 **[Create Certificate...]** 按鈕。

	![][108] 

	This displays the "Add iOS Certificate" assistant.
           
	![][108] 

    注意：本教學課程使用開發憑證。註冊生產憑證時會使用相同程序。將憑證上傳至行動服務時，只要確保您設定相同的憑證類型即可。

5.  按一下 **[選擇檔案]**，瀏覽到您在第一個工作中所建立之 CSR 檔案的儲存位置，然後按一下 **[產生]**。

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png)

1.  在入口網站建立憑證之後，依序按一下 **[下載]** 按鈕和 **[完成]**。

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png)
 
	這會下載簽署憑證，並將它儲存到您電腦中的 [下載] 資料夾。

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png)

    Note:By default, the downloaded file a development certificate is named <strong>aps_development.cer</strong>.

1.  在下載的推播通知 **aps\_development.cer** 上按兩下。

	This installs the new certificate in the Keychain, as shown below:

	![][10]

    注意：憑證中的名稱可能會不同，不過字首會加上 **Apple Development iOS Push Notification Services:** 前置詞。

之後，您會使用該憑證來產生 .p12 檔案，並將該檔案上傳到行動服務以啟用 APNS 驗證。

佈建應用程式為應用程式建立佈建設定檔
------------------------------------

1.  返回 [iOS 佈建入口網站](http://go.microsoft.com/fwlink/p/?LinkId=272456)中，依序選取 **[佈建設定檔]**、**[全部]**，然後按一下 **[+]** 按鈕以建立新設定檔。如此會啟動 **Add iOS Provisiong Profile** 精靈

	![][112]

2.  將 **[Development]** 下方的 **[iOS App Development]** 選為佈建設定檔類型，然後按一下 **[繼續]**

	![][113]

3.  接著，從 **[App ID]** 下拉式清單選取行動服務快速入門應用程式的應用程式 ID，然後按一下 **[繼續]**

	![][114]

4.  在 **[Select certificates]** 畫面中選取先前建立的憑證，然後按一下 **[繼續]**

	![][115]

5.  接著，選取要用來測試的 **[裝置]**，然後按一下 **[繼續]**

	![][116]

6.  最後，在 **[Profile Name]** 中為設定檔挑選名稱，然後依序按一下 **[產生]** 和 **[Done]**

	![][117]

	這會建立新的佈建設定檔。

1.  在 Xcode 中開啟 Organizer 並選取 Devices 檢視，在左窗格的 **[程式庫]** 區段中選取 **[Provisioning Profiles]**，然後按一下中間窗格底部的 **[重新整理]** 按鈕。

	![][101]

2.  在 **[Targets]** 下方按一下 **[Quickstart]**、展開 **[Code Signing Identity]**，然後在 **[偵錯]** 下方選取新的設定檔。

	![][17]

如此可確保 Xcode 專案使用新的設定檔來簽署程式碼。接著，您必須將憑證上傳到行動服務。

## 設定服務設定行動服務來傳送推播要求

在向 APNS 註冊應用程式及設定專案後，接下來您必須設定行動服務以整合 APNS。

1.  在 [Keychain Access] 中，於新憑證上按一下滑鼠右鍵、按一下 **[匯出]**、為 QuickstartPusher 檔案命名、選取 **[.p12]** 格式，然後按一下 **[儲存]**。

	![][28]

	記下檔案名稱和匯出憑證的位置。

    <div class="dev-callout"><b>Note</b>
    <p>This tutorial creates a QuickstartPusher.p12 file.Your file name and location might be different.</p>
    </div>

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的應用程式。

	![][18]

2.  依序按一下 **[推播]** 索引標籤和 **[上傳]**。

	![][19]

	This displays the Upload Certificate dialog.

3.  按一下 **[檔案]**、選取匯出的憑證 QuickstartPusher.p12 檔案、輸入 **[密碼]**、確認選取正確的 **[模式]**、按一下打勾圖示，然後按一下 **[儲存]**。

	![][20] 

    **注意**

    本教學課程使用開發憑證。

您的行動服務現已設定為與 APNS 搭配運作。

## 新增推播通知新增推播通知至應用程式

1.  在 Xamarin.Studio 中，開啟 AppDelegate.cs 檔案並新增以下屬性：

         public string DeviceToken { get; set; }

2.  開啟 **[TodoItem]** 類別並新增以下屬性：

         [DataMember(Name = "deviceToken")]
        public string DeviceToken { get; set; }

    **注意**

    當行動服務上已啟用動態結構描述，且插入的新項目含有此屬性時，**TodoItem** 資料表中就會自動新增 'deviceToken' 欄。

3.  在 **AppDelegate** 中，覆寫 **FinishedLaunching** 事件：

         public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
         {
        UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
        UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
        UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

        return true;
         }

4.  在 **AppDelegate** 中，覆寫 **RegisteredForRemoteNotifications** 事件：

         public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
         {
        string trimmedDeviceToken = deviceToken.Description;
        if (!string.IsNullOrWhiteSpace(trimmedDeviceToken))
             {
        trimmedDeviceToken = trimmedDeviceToken.Trim('<');
        trimmedDeviceToken = trimmedDeviceToken.Trim('>');
             }
        DeviceToken = trimmedDeviceToken;
         }

5.  在 **AppDelegate** 中，覆寫 **ReceivedRemoteNotification** 事件：

         public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
         {
        Debug.WriteLine(userInfo.ToString());
        NSObject inAppMessage;

        bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

        if (success)
             {
        var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
        alert.Show();
             }
         }

6.  在 **TodoListViewController** 中，修改 **OnAdd** 動作以取得儲存於 **AppDelegeate** 中的裝置權杖，然後將權杖儲存在即將新增的 **TodoItem** 中。

    string deviceToken = ((AppDelegate)UIApplication.SharedApplication.Delegate).DeviceToken;

             var newItem = new TodoItem() 
             {
        Text = itemText.Text, 
        Complete = false,
        DeviceToken = deviceToken
             };

您的應用程式現在已更新為支援推播通知。

更新 insert 指令碼在管理入口網站中更新已註冊的 insert 指令碼
------------------------------------------------------------

1.  在管理入口網站中，按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

	![][21]

2.  在 **[todoitem]** 中，按一下 **[指令碼]** 索引標籤，然後選取 **[插入]**。

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png)

	如此即會顯示 **[TodoItem]** 資料表中發生插入時所叫用的函數。

1.  使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

         function insert(item, user, request) {
        request.execute();
        // Set timeout to delay the notification, to provide time for the 
        // app to be closed on the device to demonstrate toast notifications
        setTimeout(function() {
        push.apns.send(item.deviceToken, {
        alert:"Toast:" + item.text,
        payload: {
        inAppMessage:"Hey, a new item arrived:'" + item.text + "'"
                     }
                 });
             }, 2500);
         }

	This registers a new insert script, which uses the [apns object] to send a push notification (the inserted text) to the device provided in the insert request. 


	<div class="dev-callout"><b>Note</b>

	此指令碼會延遲通知的傳送，讓您有時間關閉應用程式來接收快顯通知。



測試應用程式在您的應用程式中測試推播通知
----------------------------------------

1.  按 **[執行]** 按鈕以組建專案並在可執行 iOS 的裝置上啟動應用程式，然後按一下 **[確定]** 以接受推播通知。

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png)

    <div class="dev-callout"><b>Note</b>
    <p>You must explicitly accept push notifications from your app.This request only occurs the first time that the app runs.</p>
    </div>

1.  在應用程式中，輸入有意義的文字，如 *A new Mobile Services task*，然後按一下加號 (**[+]**) 圖示。

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png)

1.  確認您已接收到通知，然後按一下 **[確定]** 以關閉通知。

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png)

1.  重複步驟 2 並立即關閉應用程式，接著確認以下快顯通知可顯示。

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png)

您已成功完成此教學課程。

取得完成的範例
--------------

下載[完成的範例專案](http://go.microsoft.com/fwlink/p/?LinkId=331303)。請務必將 **applicationURL** 和 **applicationKey** 等變數更新為您自己的 Azure 設定。

後續步驟
--------

在這個簡單範例中，使用者收到推播通知，其中含有剛插入的資料。用戶端會在要求中提供 APNS 所使用的裝置權杖給行動服務。在下一個教學課程[推播通知至應用程式使用者](/en-us/develop/mobile/tutorials/push-notifications-to-users-ios)中，您將另行建立 Devices 資料表來儲存裝置權杖，並在發生插入動作時傳送推播通知給已儲存的所有通道。

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure Mobile Services]: #configure
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->




[5]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png






[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios


[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303