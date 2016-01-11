
+ **.NET 後端 (C#)**：  	
	1. 在 Visual Studio 中，以滑鼠右鍵按一下伺服器專案並按一下 [管理 NuGet 封裝]，搜尋 `Microsoft.Azure.NotificationHubs`，然後按一下 [安裝]。這會安裝通知中樞程式庫，以便從後端傳送通知。

	2. 在後端的 Visual Studio 專案中，開啟 **Controllers** > **TodoItemController.cs**。在檔案頂端新增下列 `using` 陳述式：

	        using Microsoft.Azure.Mobile.Server.Config;
	        using Microsoft.Azure.NotificationHubs;


	3. 以下列程式碼取代 `PostTodoItem` 方法：
      
	        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
	        {
	            TodoItem current = await InsertAsync(item);
	            // Get the settings for the server project.
	            HttpConfiguration config = this.Configuration;
	
	            MobileAppSettingsDictionary settings = 
	                this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
	
	            // Get the Notification Hubs credentials for the Mobile App.
	            string notificationHubName = settings.NotificationHubName;
	            string notificationHubConnection = settings
	                .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;
	
	            // Create a new Notification Hub client.
	            NotificationHubClient hub = NotificationHubClient
	            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);
	
	            // iOS payload
	            var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";
	
	            try
	            {
	                // Send the push notification and log the results.
	                var result = await hub.SendAppleNativeNotificationAsync(appleNotificationPayload);
	
	                // Write the success result to the logs.
	                config.Services.GetTraceWriter().Info(result.State.ToString());
	            }
	            catch (System.Exception ex)
	            {
	                // Write the failure result to the logs.
	                config.Services.GetTraceWriter()
	                    .Error(ex.Message, null, "Push.SendAsync Error");
	            }
	            return CreatedAtRoute("Tables", new { id = current.Id }, current);
	        }

	4. 發佈伺服器專案。

+ **Node.js 後端**：
   
	1. 如果您還沒這麼做，請下載[快速入門專案](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)或使用 [Azure 入口網站中的線上編輯器](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)。	
	
	2. 使用下列程式碼取代 todoitem.js 資料表指令碼：


	        var azureMobileApps = require('azure-mobile-apps'),
	            promises = require('azure-mobile-apps/src/utilities/promises'),
	            logger = require('azure-mobile-apps/src/logger');
	        
	        var table = azureMobileApps.table();
	        
	        // When adding record, send a push notification via APNS
	        // For this to work, you must have an APNS Hub already configured
	        table.insert(function (context) {
	            // For details of the Notification Hubs JavaScript SDK, 
	            // see http://aka.ms/nodejshubs
	            logger.info('Running TodoItem.insert');
	            
				// Create a payload that contains the new item Text.
	            var payload = "{"aps":{"alert":"" + context.item.text + ""}}";
	            
	            // Execute the insert.  The insert returns the results as a Promise,
	            // Do the push as a post-execute action within the promise flow.
	            return context.execute()
	                .then(function (results) {
	                    // Only do the push if configured
	                    if (context.push) {
	                        context.push.apns.send(null, payload, function (error) {
	                            if (error) {
	                                logger.error('Error while sending push notification: ', error);
	                            } else {
	                                logger.info('Push notification sent successfully!');
	                            }
	                        });
	                    }
	                    // Don't forget to return the results from the context.execute()
	                    return results;
	                })
	                .catch(function (error) {
	                    logger.error('Error while running context.execute: ', error);
	                });
	        });
	        
	        module.exports = table;

	2. 當您在本機電腦上編輯檔案時，請重新發佈伺服器專案。

<!---HONumber=AcomDC_1223_2015-->