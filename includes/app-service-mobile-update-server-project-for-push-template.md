使用符合您後端專案類型的程序 &mdash; [.NET 後端](#dotnet)或 [Node.js 後端](#nodejs)。

### <a name="dotnet"></a>.NET 後端專案
1. 在 Visual Studio 中，以滑鼠右鍵按一下伺服器專案並按一下 [管理 NuGet 封裝]，搜尋 `Microsoft.Azure.NotificationHubs`，然後按一下 [安裝]。這會安裝通知中樞程式庫，以便從後端傳送通知。

3. 在伺服器專案中，開啟 **Controllers** > **TodoItemController.cs**，並新增下列 using 陳述式：

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. 在 **PostTodoItem** 方法中，於呼叫 **InsertAsync** 之後新增下列程式碼：

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

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    此程式碼會指示通知中樞，將範本通知傳送至所有包含「messageParam」的範本註冊。字串會在每個具有「messageParam」註冊的 PNS 間插入並取代 messageParam。這可讓您將通知傳送給 APNS、GCM、WNS 或任何其他 PNS。

	如需有關通知中樞範本的詳細資訊，請參閱[範本](notification-hubs-templates.md)。

4. 發佈伺服器專案。

### <a name="nodejs"></a>Node.js 後端專案

1. 如果您還沒這麼做，請下載[快速入門專案](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)或使用 [Azure 入口網站中的線上編輯器](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)。

2. 在 todoitem.js 檔案中，以下列程式碼取代現有的程式碼：

		var azureMobileApps = require('azure-mobile-apps'),
	    promises = require('azure-mobile-apps/src/utilities/promises'),
	    logger = require('azure-mobile-apps/src/logger');
	
		var table = azureMobileApps.table();
		
		table.insert(function (context) {
	    // For more information about the Notification Hubs JavaScript SDK, 
	    // see http://aka.ms/nodejshubs
	    logger.info('Running TodoItem.insert');
	    
	    // Define the template payload.
	    var payload = '{"messageParam": context.item.text}'; 
	    
	    // Execute the insert.  The insert returns the results as a Promise,
	    // Do the push as a post-execute action within the promise flow.
	    return context.execute()
	        .then(function (results) {
	            // Only do the push if configured
	            if (context.push) {
					// Send a template notification.
	                context.push.send(null, payload, function (error) {
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

	插入新的 todo 項目時，這會傳送包含 item.text 的範本通知。

2. 當您在本機電腦上編輯檔案時，請重新發佈伺服器專案。

<!---HONumber=AcomDC_1223_2015-->