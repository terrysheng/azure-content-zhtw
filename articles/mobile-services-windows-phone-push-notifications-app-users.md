<properties linkid="develop-net-tutorials-push-notifications-to-users-wp8" urlDisplayName="Push Notifications to Users (WP8)" pageTitle="Push notifications to users (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Windows Phone app." metaCanonical="" services="" documentationCenter="" title="Push notifications to users by using Mobile Services" authors="glenga" solutions="" manager="" editor="" />

使用行動服務推播通知給使用者
============================

[Windows Phone](/en-us/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/push-notifications-to-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/push-notifications-to-users-android "Android")

本主題將延伸[上一個推播通知教學課程](/en-us/develop/mobile/tutorials/get-started-with-push-wp8)，方法是新增資料表以儲存 Microsoft 推播通知服務 (MPNS) 通道 URI。您可以接著使用這些通道來傳送推播通知給 Windows Phone 8 應用程式的使用者。

本教學課程將引導您逐步完成以下在您的應用程式中更新推播通知的步驟：

1.  [建立 Channel 資料表](#create-table)
2.  [更新應用程式](#update-app)
3.  [更新伺服器指令碼](#update-scripts)
4.  [驗證推播通知行為](#test-app)

本教學課程會以行動服務快速入門為基礎，並依據上一個[開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-wp8)教學課程建立。開始此教學課程之前，您必須先完成[開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-wp8)。

建立新資料表
------------

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的應用程式。

	![][0]

2.  按一下 **[資料]** 索引標籤，然後按一下 **[建立]**。

	![][1]

	This displays the **Create new table** dialog.

3.  保留所有權限的預設 **[Anybody with the application key]** 設定，在 **[資料表名稱]** 中輸入 *Channel*，然後按一下核取按鈕。

	![][2]

這樣做會建立 **Channel** 資料表，以儲存用於傳送推播通知 (與項目資料分開) 的通道 URI。

接下來，您將修改推播通知應用程式，以便在此新資料表中 (而非在 **TodoItem** 資料表中) 儲存資料。

更新應用程式
------------

1.  在 Visual Studio 2012 Express for Windows Phone 中，開啟[開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-wp8)教學課程中的專案，開啟 MainPage.xaml.cs 檔案，並將 **Channel** 屬性從 **TodoItem** 類別中移除。它看起來應該如下所示：

         public class TodoItem
         {
            public int Id { get; set; }

            [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
         }

2.  以此方法的原始版本取代 **ButtonSave\_Click** 事件處理常式方法，如下所示：

         private void ButtonSave_Click(object sender, RoutedEventArgs e)
         {
        var todoItem = new TodoItem { Text = TextInput.Text };
        InsertTodoItem(todoItem);
         }

3.  新增下列程式碼，以建立新的 **Channel** 類別：

         public class Channel
         {
        public int Id { get; set; }

        [JsonProperty(PropertyName = "uri")]
        public string Uri { get; set; }
         }

4.  開啟 App.xaml.cs 檔案，並以下列程式碼取代 **AcquirePushChannel** 方法：

         private void AcquirePushChannel()
         {
        CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

        if (CurrentChannel == null)
             {
        CurrentChannel = new HttpNotificationChannel("MyPushChannel");
        CurrentChannel.Open();
        CurrentChannel.BindToShellTile();
             }
                      
        IMobileServiceTable<Channel> channelTable = App.MobileService.GetTable<Channel>();
        var channel = new Channel { Uri = CurrentChannel.ChannelUri.ToString() };
        channelTable.InsertAsync(channel);
         }

    此程式碼會將通道插入 Channel 資料表中。

更新伺服器指令碼
----------------

1.  在管理入口網站中，按一下 **[資料]** 索引標籤，然後按一下 **[通道]** 資料表。

	![][3]

2.  在 **[通道]** 中，按一下 **[指令碼]** 索引標籤，然後選取 **[插入]**。

	![][4]

	This displays the function that is invoked when an insert occurs in the **Channel** table.

3.  使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

         function insert(item, user, request) {
        var channelTable = tables.getTable('Channel');
        channelTable
        .where({ uri:item.uri })
        .read({ success:insertChannelIfNotFound });
        function insertChannelIfNotFound(existingChannels) {
                if (existingChannels.length > 0) {
                request.respond(200, existingChannels[0]);
                } else {
                request.execute();
                    }
                }
             }

        This script checks the **Channel** table for an existing channel with the same URI.The insert only proceeds if no matching channel was found.This prevents duplicate channel records.

4.  按一下 **[TodoItem]**，按一下 **[指令碼]**，然後選取 **[插入]**。

	![][5]

5.  使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

         function insert(item, user, request) {

			request.execute({
                 success:function() {
                request.respond();
                sendNotifications();
                }

			 });

             function sendNotifications() {
                var channelTable = tables.getTable('Channel');
                channelTable.read({
                success:function(channels) {
                channels.forEach(function(channel) {
                push.mpns.sendFlipTile(channel.uri, {
                title:item.text
                            }, {
                success:function(pushResponse) {
                console.log("Sent push:", pushResponse);
                                }
                            });
                        });
                    }
                });

			}

         }

    這個 insert 指令碼會傳送推播通知 (含所插入項目的文字) 給 **Channel** 資料表中儲存的所有通道。

測試應用程式
------------

1.  在 Visual Studio 中，選取 **[建置]** 功能表上的 **[部署方案]**。

2.  點選名為 **TodoList** 或 **hello push** 的磚來啟動應用程式。

	![](./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push4-wp8.png)

1.  在應用程式中，於文字方塊輸入文字 "hello push again"，然後按一下 **[儲存]**。

	![][24]

	如此會傳送插入要求給行動服務來儲存新增的項目。

1.  按 **[開始]** 按鈕回到開始功能表。

	![](./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push6-wp8.png)

	請注意，應用程式收到推播通知，磚的標題現在是 **hello push**。

1.  (選用) 同時在兩個裝置上執行應用程式，並重複前一個步驟。

    所有執行中的應用程式執行個體都會收到通知。

後續步驟
--------

這將結束示範使用推播通知基本概念的教學課程。考慮更深入了解下列行動服務主題：

-   [開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-wp8)
    深入了解如何使用行動服務儲存和查詢資料。

-   [開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-wp8)
    了解如何使用 Windows 帳戶驗證您的應用程式的使用者。

-   [行動服務伺服器指令碼參考](http://go.microsoft.com/fwlink/p/?LinkId=262293)
    深入了解如何註冊和使用伺服器指令碼。


<!-- anchors -->
[Create the Channel table]: #create-table
[Update the app]: #update-app
[Update server scripts]: #update-scripts
[Verify the push notification behavior]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-table.png
[2]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-channel-table.png
[3]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-portal-data-tables-channel.png
[4]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-channel.png
[5]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-push2.png


[23]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push4-wp8.png
[24]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push5-wp8.png
[25]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push6-wp8.png

<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-wp8
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-wp8
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8

[Azure Management Portal]: https://manage.windowsazure.com/