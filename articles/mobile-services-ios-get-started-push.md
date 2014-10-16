<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="" solutions="" manager="" editor="" />

開始在行動服務中使用推播通知
============================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js "Windows 市集 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

本主題說明如何使用 Azure 行動服務將推播通知傳送至 iOS 應用程式。在本教學課程中，您會使用 Apple 推播通知服務 (APNS) 將推播通知新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。

您可以按一下右邊的影片，觀看本教學指南的視訊版。

[觀看教學課程](http://channel9.msdn.com/posts/iOS-Support-in-Windows-Azure-Mobile-Services) [播放視訊](http://channel9.msdn.com/posts/iOS-Support-in-Windows-Azure-Mobile-Services) 10:37

> [WACOM.NOTE] 本主題說明如何使用行動服務提供的舊有支援來啟用推播通知。Azure 通知中心與行動服務整合，能夠讓您將範本型和跨平台推播通知傳送至數百萬個裝置。使用通知中心的推播通知預設為未啟用，目前行動服務程式庫沒有 iOS 的通知中心支援。不過，您可以使用通知中心程式庫，從您的行動服務傳送推播通知。如需詳細資訊，請參閱[開始使用通知中心](/zh-tw/documentation/articles/notification-hubs-ios-get-started/)。

本教學課程會逐步引導您完成啟用推播通知的基本步驟：

1.  [產生憑證簽署要求](#certificates)
2.  [註冊您的應用程式並啟用推播通知](#register)
3.  [建立應用程式的佈建設定檔](#profile)
4.  [設定行動服務](#configure)
5.  [將推播通知新增至應用程式](#add-push)
6.  [更新指令碼傳送推播通知](#update-scripts)
7.  [插入資料以接收通知](#test)

本教學課程需要下列各項：

-   [行動服務 iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533)
-   [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   iOS 5.0 (或以上版本) 功能裝置
-   iOS Developer Program 成員資格

    > [WACOM.NOTE] 基於推播通知組態需求，您必須在 iOS 功能裝置 (iPhone 或 iPad) 而非在模擬器上部署和測試推播通知。

本教學課程是以行動服務快速入門為基礎。開始本教學課程之前，您必須先完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started-ios)。

Apple 推播通知服務 (APNS) 使用憑證來驗證您的行動服務。遵循這些指示建立必要的憑證，並將憑證上傳至您的行動服務。如需正式的 APNS 功能文件，請參閱 [Apple 推播通知服務](http://go.microsoft.com/fwlink/p/?LinkId=272584) (英文)。

產生憑證簽署要求檔案
--------------------

首先，您必須產生憑證簽署要求 (CSR) 檔案，這將由 Apple 用來產生簽署的憑證。

1.  從 Utilities 資料夾中，執行 Keychain Access 工具。

2.  按一下 **[Keychain Access]**，並展開 **[Certificate Assistant]**，然後按一下 **[Request a Certificate from a Certificate Authority...]**。

 ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png)

1.  選取您的**使用者電子郵件**和**一般名稱**，並確定已選取 **[Saved to disk]**，然後按一下 **[繼續]**。將 **[CA Email Address]** 欄位保留空白，因為它不是必填欄位。

 ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png)

1.  在 **[另存新檔]** 中輸入憑證簽署要求 (CSR) 檔案的名稱，並且在 **[位置]** 中選取位置，然後按一下 **[儲存]**。

 ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png)

   這會在選取的位置儲存 CSR 檔；預設位置為桌面。請記住對於此檔案選擇的位置。

接著，您將向 Apple 註冊您的應用程式，並啟用推播通知，然後上傳這個匯出的 CSR 建立推播憑證。

註冊進行推播通知的應用程式
--------------------------

若要從行動服務將推播通知傳送至 iOS 應用程式，您必須向 Apple 註冊您的應用程式，並註冊進行推播通知。

1.  如果您尚未註冊應用程式，請瀏覽至 Apple 開發人員中心的 [iOS 佈建入口網站](http://go.microsoft.com/fwlink/p/?LinkId=272456)，然後使用您的 Apple ID 登入，並按一下 **[識別碼]**，接著按一下 **[App IDs]**，最後按一下 **+** 號註冊新的應用程式。

       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png)

2.  在 **[說明]** 中您輸入應用程式的名稱，並且在 **[Bundle Identifier]** 中輸入 *MobileServices.Quickstart* 值，接著勾選 [應用程式服務] 區段中的 [推播通知]，然後按一下 **[繼續]**。此範例使用識別碼 **MobileServices.Quickstart**，但是您可能無法重複使用同一個識別碼，因為所有使用者的應用程式識別碼不得重複。因此，建議在應用程式名稱後加上您的全名或縮寫。

       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png)
           
       這將產生您的應用程式識別碼，並要求您 **提交** 這個資訊。按一下 **提交**
           
       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png)
           
       按一下 **提交** 之後，您將看見 **註冊完成** 畫面，如下所示。按一下 **完成**。
           
       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png)

    > [WACOM.NOTE] 如果您選擇提供 **Bundle Identifier** 值而非 *MobileServices.Quickstart*，則您必須也更新 Xcode 專案中的搭配識別碼。

3.  找出剛才建立的應用程式識別碼，並且按一下該資料列。

       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png)
           
       按一下應用程式識別碼將顯示應用程式和應用程式識別碼的詳細資料。按一下 **設定** 按鈕。
           
       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png)

4.  捲動至畫面底端，並按一下 **[Development Push SSL Certificate]** 區段下的 **[建立憑證...]** 按鈕。

       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png)

       這將顯示 [Add iOS Certificate] 助理。
           
       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png)

    > [WACOM.NOTE] 本教學課程使用開發憑證。註冊生產憑證時，將使用同一個程序。您將憑證上傳至行動服務時，請確定設定相同的憑證類型。

5.  按一下 **[選擇檔案]**，瀏覽至您儲存第一個工作中建立的 CSR 檔所在的位置，然後按一下 **[產生]**。

 ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png)

6.  入口網站建立憑證後，按一下 **[下載]** 按鈕，並按一下 **[完成]**。

 ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png)

 這將下載簽署憑證並儲存至您電腦中的 Downloads 資料夾。

 ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png)

    > [WACOM.NOTE] By default, the downloaded file a development certificate is named **aps_development.cer**.

7.  按兩下下載的推播憑證 **aps\_development.cer**。

    This installs the new certificate in the Keychain, as shown below:

       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png)

    > [WACOM.NOTE] 您的憑證中出現的名稱可能不同，但是前面都會加上 **Apple Development iOS Push Notification Services:**。

然後，您將使用此憑證產生 .p12 檔，並將該檔案上傳至行動服務，向 APNS 啟用驗證。

建立應用程式的佈建設定檔
------------------------

1.  返回 [iOS 佈建入口網站](http://go.microsoft.com/fwlink/p/?LinkId=272456)，選取 **[Provisioning Profiles]**，並選取 **[全部]**，然後按一下 **+** 按鈕建立新的設定檔。這將啟動 **[Add iOS Provisiong Profile]** 精靈

       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png)

2.  選取 **[開發]** 下的 **[iOS App Development]** 作為佈建設定檔類型，並按一下 **[繼續]**

       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png)

3.  接著，從 **[應用程式識別碼]** 下拉式清單中選取行動服務快速入門的應用程式識別碼，並按一下 **[繼續]**

       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png)

4.  在 **[選取憑證]** 畫面中，選取稍早建立的憑證，並按一下 **[繼續]**

       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png)

5.  然後，選取要用來測試的 **[裝置]**，然後按一下 **[繼續]**

       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png)

6.  最後，在 **[設定檔名稱]** 中選擇設定檔的名稱，並按一下 **[產生]**，然後按一下 **[完成]**

      ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png)

 這將建立新的佈建設定檔。

1.  在 Xcode 中，開啟 [組合管理] 選取 [Devices view]，並選取左窗格的 **[程式庫]** 區段中顯示的 **[Provisioning Profiles]**，然後按一下中間窗格底端的 **[重新整理]**。

       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png)

2.  在 **[目標]** 下，按一下 **[Quickstart]**，並展開 **[Code Signing Identity]**，然後在 **[偵錯]** 下選取新的設定檔。

       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png)

這將確保 Xcode 專案使用新的設定檔進行程式碼簽署。接著，您必須將憑證上傳至行動服務。

設定行動服務傳送推播要求
------------------------

向 APNS 註冊您的應用程式並設定您的專案之後，您必須接著設定您的行動服務與 APNS 整合。

1.  在 Keychain Access 中，以滑鼠右鍵按一下新憑證，按一下 **[匯出]**，並且將 QuickstartPusher 檔案命名，接著選取 **.p12** 格式，然後按一下 **[儲存]**。

       ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png)

 記下匯出憑證的檔案名稱和位置。

    > [WACOM.NOTE] This tutorial creates a QuickstartPusher.p12 file.Your file name and location might be different.

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的應用程式。

       ![](./media/mobile-services-ios-get-started-push/mobile-services-selection.png)

2.  按一下 **[推播]** 索引標籤，並按一下 **[上傳]**。

       ![](./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png)

    這將顯示 [上傳憑證] 對話方塊。

3.  按一下 **[檔案]**，選取匯出的憑證 QuickstartPusher.p12 檔，然後輸入 **[密碼]**，確定選取正確的**[模式]**，按一下核取圖示，然後按一下 **[儲存]**。

       ![](./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png)

    > [WACOM.NOTE] 本教學課程使用開發憑證。

您的行動服務現在是設定為使用 APNS。

將推播通知新增至應用程式
------------------------

1.  在 Xcode 中，開啟 QSAppDelegate.h 檔，並且在 **[\*window]** 屬性下新增下列屬性：

        @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] 對於您的行動服務啟用動態結構描述時，只要插入包含此屬性的新項目，就會自動將新的「deviceToken」欄新增至 **[TodoItem]** 資料表。

2.  在 QSAppDelegate.m 中，取代實作內的下列處理常式方法：

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3.  在 QSAppDelegate.m 中，新增實作內的下列處理常式方法：

        // We are registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4.  在 QSAppDelegate.m 中，新增實作內的下列處理常式方法：

        // Handle any failure to register.In this case we set the deviceToken to an empty
        // string to prevent the insert from failing.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications:%@", error);
            self.deviceToken = @"";
        }

5.  在 QSAppDelegate.m 中，新增實作內的下列處理常式方法：

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

6.  在 QSTodoListViewController.m 中，匯入 QSAppDelegate.h 檔，以便使用委派來取得裝置權杖：

        #import "QSAppDelegate.h"

7.  在 QSTodoListViewController.m 中，找出下列一行，修改 **(IBAction)onAdd** 動作：

        NSDictionary *item = @{ @"text" :itemText.text, @"complete" :@(NO) }; 

    以下列程式碼取代這一項：

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" :itemText.text,
            @"complete" :@(NO),
            // add the device token property to our todo item payload
            @"deviceToken" :delegate.deviceToken
        };

       這將新增 **QSAppDelegate** 的參考來取得裝置權杖，然後修改要求裝載來包含該裝置權杖。

    > [WACOM.NOTE] 您必須先新增這段程式碼，再呼叫 <strong>addItem</strong> 方法。

您的應用程式此時將更新，以支援推播通知。

在管理入口網站中更新已註冊的插入指令碼
--------------------------------------

1.  在管理入口網站中，按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

       ![](./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png)

2.  在 **[todoitem]** 中，按一下 **[指令碼]** 索引標籤，然後選取 **[插入]**。

	   ![](./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png)

 這會顯示 **[TodoItem]** 資料表中發生插入時所叫用的函數。

1.  使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send(item.deviceToken, {
                    警示："Toast:" + item.text,
                    payload: {
                        inAppMessage:"Hey, a new item arrived:'" + item.text + "'"
                    }
                });
            }, 2500);
        }

    這將註冊新的插入指令碼，使用 [apns object] 將推播通知 (插入的文字) 傳送至插入要求中提供的裝置。 


      > [WACOM.NOTE] 此指令碼將延遲傳送通知，讓您有時間關閉應用程式來接收快顯通知。

在應用程式中測試推播通知
------------------------

1.  按 **[執行]** 按鈕建立專案，並且在 iOS 功能裝置中啟動應用程式，然後按一下 **[確定]** 接受推播通知

 ![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png)

   > [WACOM.NOTE] You must explicitly accept push notifications from your app.只有在應用程式第一次執行時，才會發生此要求。

1.  在應用程式中輸入有意義的文字，例如*新的行動服務工作*，然後按一下加號 (**+**) 圖示。

 ![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png)

1.  確認收到通知，然後按一下 **[確定]** 關閉通知。

 ![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png)

1.  重複步驟 2 並立即關閉應用程式，然後確認下列快顯通知是否顯示。

 ![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png)

您已經成功完成本教學課程。

後續步驟
--------

在這個簡單的範例中，使用者收到剛才插入的資料包含在其中的推播通知。要求中的用戶端將 APNS 使用的裝置權杖提供給行動服務。在下一個教學課程[應用程式使用者的推播通知](/en-us/develop/mobile/tutorials/push-notifications-to-users-ios)中，您將建立個別的裝置資料表儲存裝置權杖，並且在插入時將推播通知傳送至所有儲存的頻道。

