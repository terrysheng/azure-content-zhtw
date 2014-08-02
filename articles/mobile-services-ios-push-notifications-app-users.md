<properties linkid="develop-net-tutorials-push-notifications-to-users-ios" urlDisplayName="Push Notifications to Users (iOS)" pageTitle="Push notifications to users (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="" solutions="" manager="" editor="" />

使用行動服務推播通知給使用者
============================

[Windows Phone](/en-us/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/push-notifications-to-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/push-notifications-to-users-android "Android")

本主題將透過新增可儲存 Apple 推播通知服務 (APNS) 權杖的資料表，來延伸[上一個推播通知教學課程](/en-us/develop/mobile/tutorials/get-started-with-push-ios)。這些權杖接著可用來傳送推播通知給 iPhone 或 iPad 應用程式的使用者。

本教學課程將會逐步引導您完成這些步驟，來更新應用程式中的推播通知：

1.  [建立 Devices 資料表](#create-table)
2.  [更新應用程式](#update-app)
3.  [更新伺服器指令碼](#update-scripts)
4.  [驗證推播通知行為](#test-app)

本教學課程將根據行動服務快速入門，並以上一堂教學課程[開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-ios)為基礎。在開始本教學課程之前，您必須先完成[開始推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-ios)。

## <a name="create-table"></a><h2><span class="short-header">建立資料表建立新的</span>Devices 資料表</h2>


1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的應用程式。

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-services-selection.png)

2.  按一下 **[資料]** 索引標籤，然後按一下 **[建立]**。

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-create-table.png)

     這將顯示 [建立新資料表] 對話方塊。

3.  保留所有權限的預設 **[具有應用程式金鑰的任何人]** 設定，在 **[資料表名稱]** 中輸入 *Devices*，然後按一下檢查按鈕。

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-create-devices-table.png)

   這樣做會建立 **Devices** 資料表，以儲存用於傳送推播通知 (與項目資料分開傳送) 的裝置權杖。

接下來，您將修改推播通知應用程式，以在此新資料表 (而不是在 **TodoItem** 資料表) 中儲存資料。

更新應用程式
------------

1.  在 Xcode 中，開啟 QSTodoService.h 檔案並新增下列方法宣告：

         // Declare method to register device token for other users
        - (void)registerDeviceToken:(NSString *)deviceToken;

    This enables the App Delegate to register a deviceToken with the Mobile Service.

2.  在 QSTodoService.m 中新增下列執行個體方法：

         // Instance method to register deviceToken in Devices table.
        // Called in AppDelegate.m when APNS registration succeeds.
        - (void)registerDeviceToken:(NSString *)deviceToken
         {
        MSTable *devicesTable = [self.client tableWithName:@"Devices"]; 
        NSDictionary *device = @{ @"deviceToken" :deviceToken };

        // Insert the item into the devices table and add to the items array on completion
        [devicesTable insert:device completion:^(NSDictionary *result, NSError *error) {
        if (error) {
        NSLog(@"ERROR %@", error);
                 }
             }];
         }

    This allows other callers to register the device token with Mobile Services.

3.  在 QSAppDelegate.m 檔案中新增下列 import 陳述式：

         #import "QSTodoService.h"

    此程式碼將使 AppDelegate 注意到 TodoService 實作。

4.  在 QSAppDelegate.m 中，使用下列程式碼來取代 **didRegisterForRemoteNotificationsWithDeviceToken** 方法：

         // We have registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

        // Register the APNS deviceToken with the Mobile Service Devices table.
        NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
        NSString *token = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
                
        QSTodoService *instance = [QSTodoService defaultService];
        [instance registerDeviceToken:token];
         }

5.  在 QSTodoListViewController.m 中，尋找 **(IBAction)onAdd** 方法並*移除*下列程式碼：

         // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
        @"text" :itemText.text,
        @"complete" :@(NO),
        // add the device token property to our todo item payload
        @"deviceToken" :delegate.deviceToken
         };
         
     使用下列程式碼來取代此項：

        // We removed the delegate; this application no longer passes the deviceToken here.
        // Remove the device token from the payload
        NSDictionary *item = @{ @"text" :itemText.text, @"complete" :@(NO) };

您的應用程式現已更新為使用新的 Devices 資料表，可儲存用來將推播通知傳回裝置的裝置權杖。

更新伺服器指令碼
----------------

1.  在管理入口網站中，按一下 **[資料]** 索引標籤，然後按一下 **[Devices]** 資料表。

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-portal-data-tables-devices.png)

2.  在 **[裝置]** 中，按一下 **[指令碼]** 索引標籤，然後選取 **[插入]**。

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-devices.png)

    這會顯示 **[Devices]** 資料表中發生插入時所叫用的函數。

3.  使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

         function insert(item, user, request) {
        var devicesTable = tables.getTable('Devices');
        devicesTable.where({
        token:item.token
        }).read({
        success:insertTokenIfNotFound
            });

        function insertTokenIfNotFound(existingTokens) {
        if (existingTokens.length > 0) {
        request.respond(200, existingTokens[0]);
        } else {
        request.execute();
                }
            }
         }

     此指令碼會檢查 [Devices] 資料表，以找出是否有具備相同權杖的現有裝置。只有當找不到相符裝置時才會執行插入作業。這可避免重複的裝置記錄。

4.  依序按一下 **[TodoItem]**、**[指令碼]**，然後選取 **[插入]**。

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-push2.png)

5.  使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

        function insert(item, user, request) {
        request.execute({
        success:function() {
        request.respond();
        sendNotifications();
               }
           });

        function sendNotifications() {
        var devicesTable = tables.getTable('Devices');
        devicesTable.read({
        success:function(devices) {
        // Set timeout to delay the notifications, 
        // to provide time for the app to be closed 
        // on the device to demonstrate toast notifications.
        setTimeout(function() {
        devices.forEach(function(device) {

        push.apns.send(device.deviceToken, {
        alert:"Toast:" + item.text,
        payload: {
        inAppMessage: 
        "Hey, a new item arrived: '" + 
        item.text + "'"
                                   }
                               });
                           });
                       }, 2500);
                   }
               });
           }

         }

    此插入指令碼會傳送推播通知 (包含插入項目的文字) 給所有儲存於 **Devices** 資料表中的裝置。

測試應用程式在應用程式中測試推播通知
------------------------------------

1.  按 **[執行]** 按鈕以建立專案，並在支援 iOS 的裝置中啟動應用程式，然後在應用程式中鍵入有意義的文字 (例如 *A new Mobile Services task*)，然後按一下加號 (**+**) 圖示。

    ![](./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push2-ios.png)

1.  驗證是否收到通知，然後按一下 **[確定]** 以關閉通知。

    ![](./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push3-ios.png)

1.  重複步驟 2 並立即關閉應用程式，然後驗證是否出現下列快顯。

    ![](./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push4-ios.png)

您已順利完成本教學課程。

後續步驟
--------

這將結束示範使用推播通知基本概念的教學課程。考慮搜尋有關下列行動服務主題的更多資訊：

-   [開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-ios)
    
    深入了解使用行動服務來儲存與查詢資料。

-   [開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-ios)
    
    了解如何驗證具有 Windows 帳戶的應用程式使用者。

-   [行動服務伺服器指令碼參照](http://go.microsoft.com/fwlink/?LinkId=262293)
    
    深入了解註冊與使用伺服器指令碼。


