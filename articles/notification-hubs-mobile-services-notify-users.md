<properties linkid="notification-hubs-how-to-guides-howto-notify-users-mobileservices" urlDisplayName="Notify Users" pageTitle="Notify Users of your mobile service with Notification Hubs" metaKeywords="" description="Follow this tutorial to register to receive notifications from your mobile service by using Notification Hubs" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Notify users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

使用通知中心來通知使用者
========================

[行動服務](/en-us/manage/services/notification-hubs/notify-users "行動服務") [ASP.NET](/en-us/manage/services/notification-hubs/notify-users-aspnet "ASP.NET")

本教學課程將示範如何使用 Azure 通知中心，來將推播通知傳送到特定裝置上的特定應用程式使用者。Azure 行動服務後端可用來驗證用戶端並產生通知。本教學課程會以您在上一個**開始使用通知中心**教學課程中所建立的通知中心為基礎。將通知註冊程式碼從用戶端移至後端服務。這可確保只有在服務已明確地驗證用戶端之後才會完成註冊。這也代表通知中心認證不會隨著用戶端應用程式一起散佈。服務也可控制註冊期間所要求的標籤。

本教學課程將逐步引導您完成下列基本步驟：

-   [更新您的行動服務以註冊通知](#register-notification)
-   [更新應用程式以登入並要求註冊](#update-app)
-   [更新您的行動服務以傳送通知](#send-notifications)

必要條件
--------

在開始本教學課程之前，您必須先完成下列教學課程：

-   **開始使用通知中心** ([Windows 市集 C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet)/[iOS](/en-us/manage/services/notification-hubs/get-started-notification-hubs-ios)/[Android](/en-us/manage/services/notification-hubs/get-started-notification-hubs-android))。

-   **在行動服務中開始使用驗證** ([Windows 市集 C\#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/)/[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios/)/[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android/))

本教學課程會以您在**開始使用通知中心**中所建立的應用程式和通知中心為基礎。它也會充分利用您在**在行動服務中開始使用驗證**中所設定的通過驗證行動服務。

**注意**

依預設，**在行動服務中開始使用驗證**教學課程會使用 Facebook 驗證。您無法在本教學課程中使用 Microsoft 帳戶驗證，因為兩個 Windows 市集應用程式無法共用單一 Live Connect 註冊。若要使用 Microsoft 帳戶驗證，您必須在 Live Connect 中將行動服務和通知中心註冊到相同應用程式。

註冊通知更新行動服務以註冊通知
------------------------------

因為只有在服務已明確地驗證用戶端之後才會完成通知註冊，註冊會由行動服務中所定義的自訂 API 執行。通過驗證用戶端會呼叫此自訂 API，以要求通知註冊。在本節中，您將更新您在完成**在行動服務中開始使用驗證**教學課程時所定義的通過驗證行動服務。

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[服務匯流排]**、您的命名空間、**[通知中心]**，然後選擇您的通知中心並按一下 **[連接資訊]**。

    ![](./media/notification-hubs-mobile-services-notify-users/notification-hub-select-hub-connection.png)

2.  記下您的通知中心名稱，並複製 **DefaultFullSharedAccessSignature** 的連接字串。

    ![](./media/notification-hubs-mobile-services-notify-users/notification-hub-connection-strings.png)

    您將使用此連接字串以及通知中心名稱來註冊與傳送通知。

3.  依舊在管理入口網站中按一下 **[行動服務]**，然後按一下您的應用程式。

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-services-selection.png)

4.  按一下 **[API]** 索引標籤，然後按一下 **[Create a custom API]**。

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create.png)

    這樣做會顯示 [Create a new custom API] 對話方塊。

5.  在 **[API name]** 中輸入 *register\_notifications*，並在 **[POST Permissions]** 中選取 **[Only Authenticated Users]**，然後按一下核取按鈕。

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create2.png)

	這會建立需要使用者在使用 HTTP POST 方法進行呼叫之前先通過驗證的 API。因為我們不會實作其他 HTTP 方法，所以無需設定他們。

1.  按一下 API 資料表中的新 **register\_notifications** 項目。

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-select.png)

2.  按一下 **[指令碼]** 索引標籤，並使用下列程式碼來取代現有程式碼：

		exports.post = function(request, response) {

			// Create a notification hub instance.
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
				'<FULL_SAS_CONNECTION_STRING>');
		
		    // Get the registration info that we need from the request. 
		    var platform = request.body.platform;
		    var userId = request.user.userId;
		    var installationId = request.header('X-ZUMO-INSTALLATION-ID');
		    
		    // Function called when registration is completed.
		    var registrationComplete = function(error, registration) {
		        if (!error) {
		            // Return the registration.
		            response.send(200, registration);
		        } else {
		            response.send(500, 'Registration failed!');
		        }
		    }
		    // Function called to log errors.
		    var logErrors = function(error) {
		        if (error) {
		            console.error(error)
		        }
		    }
		    // Get existing registrations.
		    hub.listRegistrationsByTag(installationId, function(error, existingRegs) {
		        var firstRegistration = true;
		        if (existingRegs.length > 0) {
		             for (var i = 0; i < existingRegs.length; i++) {
		                if (firstRegistration) {
		                    // Update an existing registration.
		                    if (platform === 'win8') {
		                        existingRegs[i].ChannelUri = request.body.channelUri;                        
		                        hub.updateRegistration(existingRegs[i], registrationComplete);                        
		                    } else if (platform === 'ios') {
		                        existingRegs[i].DeviceToken = request.body.deviceToken;
		                        hub.updateRegistration(existingRegs[i], registrationComplete);
		                    } else {
		                        response.send(500, 'Unknown client.');
		                    }
		                    firstRegistration = false;
		                } else {
		                    // We shouldn't have any extra registrations; delete if we do.
		                    hub.deleteRegistration(existingRegs[i].RegistrationId, logErrors);
		                }
		            }
                } else {
                    // Create a new registration.
                    if (platform === 'win8') {                
                        hub.wns.createNativeRegistration(request.body.channelUri, 
                        [userId, installationId], registrationComplete);
                    } else if (platform === 'ios') {
                        hub.apns.createNativeRegistration(request.body.deviceToken, 
                        [userId, installationId], registrationComplete);
                    } else {
                        response.send(500, 'Unknown client.');
                    }
                }
            });
        }

    此程式碼會從訊息主體中取得平台和 deviceID 資訊。此資料 (以及要求標頭的安裝識別碼和已登入使用者的使用者識別碼) 可用來更新註冊 (若存在的話)，或建立新註冊。此註冊上會附有使用者識別碼和安裝識別碼。

3.  更新程式碼，並使用通知中心的值來取代 *`<NOTIFICATION_HUB_NAME>`* 和 *`<FULL_SAS_CONNECTION_STRING>`*，然後按一下 **[儲存]**。

    **注意**

    務必在 *`<FULL_SAS_CONNECTION_STRING>`* 中使用 **DefaultFullSharedAccessSignature**。此宣告可讓自訂 API 方法建立並更新註冊。

更新應用程式更新應用程式以登入及要求註冊
----------------------------------------

接著，您必須呼叫新的自訂 API，以更新 TodoList 應用程式並要求註冊通知。

1.  視您的用戶端平台而定，請依照下列其中一個**使用行動服務來註冊目前使用者以取得推播通知**版本中的步驟進行：

    -   [Windows 市集 C\# 版本](/en-us/manage/services/notification-hubs/register-users-mobile-services-dotnet)
    -   [iOS 版本](/en-us/manage/services/notification-hubs/register-users-ios)

2.  執行更新的應用程式、使用 Facebook 登入，然後驗證是否顯示指派給通知的註冊識別碼。

傳送通知更新您的行動服務以傳送通知
----------------------------------

最後步驟是新增可在行動服務中傳送通知的程式碼。此通知程式碼會被加入註冊到 **TodoItem** 資料表插入處理常式中的伺服器指令碼。

1.  回到管理入口網站中，按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-portal-data-tables.png)

2.  在 **[todoitem]** 中，按一下 **[指令碼]** 索引標籤，然後選取 **[插入]**。

	![](./media/notification-hubs-mobile-services-notify-users/mobile-insert-script-push2.png) 

	這會顯示 **[TodoItem]** 資料表中發生插入時所叫用的函數。

1.  使用下列程式碼來取代插入函數：

		function insert(item, user, request) {
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
		    '<FULL_SAS_CONNECTION_STRING>');
		
 		   // Create the payload for a Windows Store app.
		    var wnsPayload = '<toast><visual><binding template="ToastText02"><text id="1">New item added:</text><text id="2">' + item.text + '</text></binding></visual></toast>';
		
		    // Execute the request and send notifications.
		    request.execute({
		        success: function() {
		            // Write the default response and send a notification 
		            // to the user on all devices by using the userId tag.
		            request.respond();
		            // Send to Windows Store apps.
		            hub.wns.send(user.userId, wnsPayload, 'wns/toast', function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		            // Send to iOS apps.
		            hub.apns.send(user.userId, {
		                alert: item.text
		            }, function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		        }
		    });
		}

    這會嘗試將通知傳送給目前已登入 Windows 市集和 iOS 應用程式之使用者的標籤。

1.  更新程式碼，並使用通知中心的值來取代 *`<NOTIFICATION_HUB_NAME>`* 和 *`<FULL_SAS_CONNECTION_STRING>`*，然後按一下 **[儲存]**。

    這會註冊新的插入指令碼，並使用通知中心來傳送推播通知 (插入的文字) 給插入要求中所提供的通道。

    **注意**

    務必在 *`<FULL_SAS_CONNECTION_STRING>`* 中使用 **DefaultFullSharedAccessSignature**。此宣告會提供插入指令碼完整存取權，包括傳送通知給註冊用戶端的能力。

測試應用程式在應用程式中測試推播通知
------------------------------------

現在已設定好通知，我們可以開始透過插入資料並產生通知來測試應用程式。

1.  執行應用程式。

    啟動時會再次顯示註冊通知。

2.  像之前一樣輸入文字並新增項目。

    請注意，插入完成後，應用程式便會收到通知中心的推播通知。

    **注意**

    當要求傳送通知的目標平台沒有註冊時，後端上便會發生錯誤。在此情況下，可以忽略此錯誤。若要查看如何使用範本避免此狀況，請參閱[使用通知中心傳送跨平台通知給使用者](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)。

3.  (選擇性) 將此用戶端應用程式部署到第二台裝置，然後執行此應用程式並插入文字。

    每台裝置上都會顯示通知。

後續步驟
--------

現在您已完成本教學課程，請考慮完成下列教學課程：

-   **使用通知中心傳送重大消息 ([Windows 市集 C\#](/en-us/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/en-us/manage/services/notification-hubs/breaking-news-ios))**
    此平台特定教學課程將示範如何使用標籤，來讓使用者訂閱他們感興趣的通知類型。

-   **[使用通知中心傳送跨平台通知給使用者](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services)**
    此教學課程會延伸目前的**使用通知中心通知使用者**教學課程，來使用平台特定範本註冊通知。這可讓您在伺服器端程式碼中，使用單一方法傳送通知。

如需有關通知中心的詳細資訊，請參閱 [Azure 通知中心](http://msdn.microsoft.com/en-us/library/windowsazure/jj927170.aspx)。

