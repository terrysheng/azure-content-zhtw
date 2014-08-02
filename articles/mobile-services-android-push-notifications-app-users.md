<properties linkid="" urlDisplayName="" pageTitle="Push notifications to users (Android ) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

使用行動服務推播通知給使用者
============================

[Windows Phone](/en-us/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/push-notifications-to-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/push-notifications-to-users-android "Android")

本主題將新增資料表來儲存 Google 雲端通訊 (GCM) 註冊 URI，這將用來傳送推播通知給多位 Android 應用程式使用者，藉以延伸[上一個推播通知教學課程](/en-us/develop/mobile/tutorials/get-started-with-push-android)。在本教學課程中，每當 ToDoList 資料表中完成插入時，就會以單一更新向所有註冊的裝置產生推播通知。在前一個教學課程中，只會向進行插入的裝置傳送通知。

本教學課程將會逐步引導您完成這些步驟，來更新應用程式中的推播通知：

1.  [建立 Registration 資料表](#create-table)
2.  [更新應用程式](#update-app)
3.  [更新伺服器指令碼](#update-scripts)
4.  [驗證推播通知行為](#test-app)

本教學課程將根據行動服務快速入門，並以上一堂教學課程[開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-android)為基礎。在開始本教學課程之前，您必須先完成[開始推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-android)。

建立新資料表
------------

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的應用程式。

   	![](./media/mobile-services-android-push-notifications-app-users/mobile-services-selection.png)

2.  按一下 **[資料]** 索引標籤，然後按一下 **[建立]**。

   	![](./media/mobile-services-android-push-notifications-app-users/mobile-create-table.png)

    This displays the **Create new table** dialog.

3.  保留所有權限的預設 **[具有應用程式金鑰的任何人]** 設定，在 **[資料表名稱]** 中輸入 *Registration*，然後按一下檢查按鈕。

   	![](./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-table.png)

    這樣做會建立 **Registration** 資料表，以儲存用於傳送推播通知 (與項目資料分開傳送) 的註冊 URI。

接下來，您將修改推播通知應用程式，以在此新資料表 (而不是在 **TodoItem** 資料表) 中儲存註冊資料。

更新應用程式
------------

1.  在 Eclipse 的 Package Explorer 中，以滑鼠右鍵按一下封裝 (在 [src]`` 節點下)，按一下 **[New]**，按一下 **[Class]**。

2.  在 **[名稱]** 中，輸入 `Registration`，然後按一下 **[完成]**

    ![](./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-class.png)

    如此即會建立新的 Registration 類別。

3.  開啟 ToDoItem.java，剪下下列程式碼：

        @com.google.gson.annotations.SerializedName("handle")
        private String mHandle;

        public String getHandle() {
            return mHandle;
        }

        public final void setHandle(String handle) {
            mHandle = handle;
        }

4.  在您先前建立的 **Registration** 類別的本文中，貼上上一個步驟剪下的程式碼。

5.  在 **Registration** 類別中加入以下程式碼：

        @com.google.gson.annotations.SerializedName("id")
        private int mId;

        /**
         * Returns the item id
         */
        public int getId() {
            return mId;
        }

        /**
         * Sets the item id
         * 
         * @param id :id to set
         */
        public final void setId(int id) {
            mId = id;
        }

6.  開啟 **ToDoActivity.java** 檔案，並在 `addItem` 方法中刪除下列幾行：

        item.setHandle(MyHandler.getHandle());

7.  找出 `mClient` 屬性並改為下列程式碼：

        /**
        * Mobile Service Client reference
        */
        private static MobileServiceClient mClient;

        /**
         * Returns the client reference
         */
        public static MobileServiceClient getClient() {
            return mClient;
        }

8.  在 **MyHandler** 檔案中新增下列 import 陳述式：

        import android.util.Log;
            
        import com.microsoft.windowsazure.notifications.NotificationsHandler;

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;

9.  在 `onRegistered` 方法的結尾加入以下程式碼：

        MobileServiceClient client = ToDoActivity.getClient();
        MobileServiceTable<Registration> registrations = client.getTable(Registration.class);

        // Create a new Registration
        Registration registration = new Registration();
        registration.setHandle(gcmRegistrationId);

        // Insert the new Registration
        registrations.insert(registration, new TableOperationCallback<Registration>() {

            public void onCompleted(Registration entity, Exception exception, ServiceFilterResponse response) {

                if (exception != null) {
                    Log.e("MyHandler", exception.getMessage());
                } else {
                    Log.e("MyHandler", "Registration OK");
                }
             }
        });

您的應用程式此時將更新，以支援對使用者的推播通知。

更新伺服器指令碼
----------------

1.  在管理入口網站中，按一下 **[資料]** 索引標籤，然後按一下 **Registration** 資料表。

    ![](./media/mobile-services-android-push-notifications-app-users/mobile-portal-data-tables-registration.png)

2.  在 **registration** 中，按一下 **[指令碼]** 索引標籤，然後選取 **[插入]**。

    ![](./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-registration.png)

    This displays the function that is invoked when an insert occurs in the **Registration** table.

3.  使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

        function insert(item, user, request) {
            var registrationTable = tables.getTable('Registration');
            registrationTable
                .where({ handle:item.handle })
                .read({ success:insertRegistrationIfNotFound });
            function insertRegistrationIfNotFound(existingRegistrations) {
                if (existingRegistrations.length > 0) {
                    request.respond(200, existingRegistrations[0]);
                } else {
                    request.execute();
                }
            }
         }

    此指令碼會檢查 **Registration** 資料表，以找出具有相同 URI 的現有註冊。只有在找不到相符的註冊時，才會繼續插入。這可避免重複的註冊記錄。

4.  依序按一下 **[TodoItem]**、**[指令碼]**，然後選取 **[插入]**。

    ![](./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-push2.png)

5.  使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

		function insert(item, user, request) {
		    request.execute({
		        success: function() {
		            // Write to the response and then send the notification in the background
		            request.respond();
		            sendNotifications(item.text);
		
		        }
            });
		
        function sendNotifications(item_text) {
            var registrationTable = tables.getTable('Registration');
            registrationTable.read({
                success: function(registrations) {
                    registrations.forEach(function(registration) {
                        push.gcm.send(registration.handle, item_text, {
                            success: function(response) {
                                console.log('Push notification sent: ', response);
                            }, error: function(error) {
                                console.log('Error sending push notification: ', error);
                            }
                        });
                    });
                 }
            });
          }
        }

    這個 insert 指令碼會傳送推播通知 (含所插入項目的文字) 給 **Registration** 資料表中儲存的所有註冊。

測試應用程式
------------

1.  從 Eclipse 的 **[Run]** 功能表，按一下 **[Run]** 啟動應用程式。

2.  在應用程式中，輸入有意義的文字，如 *A new Mobile Services task*，然後按一下 **[加入]** 按鈕。

3.  您將在螢幕的下半部看到黑色通知方塊短暫出現。

![](./media/mobile-services-android-push-notifications-app-users/mobile-push-icon.png)

您已順利完成本教學課程。

後續步驟
--------

這將結束示範使用推播通知基本概念的教學課程。考慮搜尋有關下列行動服務主題的更多資訊：

-   [開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-android)
    深入了解使用行動服務來排序和查詢資料。

-   [開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-android)
    了解如何驗證具有 Windows 帳戶的應用程式使用者。

-   [行動服務伺服器指令碼參照](http://go.microsoft.com/fwlink/?LinkId=262293)
    深入了解註冊與使用伺服器指令碼。

-   [如何使用適用於行動服務的 Android 用戶端程式庫](/en-us/develop/mobile/how-to-guides/work-with-android-client-library)
    深入了解如何將 .NET 搭配行動服務使用。


