<!---Use the procedure that matches your backend project type&mdash;either [.NET backend](#dotnet) or [Node.js backend](#nodejs).

### <a name="dotnet"></a>.NET backend project -->
1. 在 Visual Studio 中，使用下列程式碼更新 `PostTodoItem` 方法定義：  

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
	
	        // Create the notification hub client.
	        NotificationHubClient hub = NotificationHubClient
	            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);
	
	        // Get the current user SID and create a tag for the current user.
	        var claimsPrincipal = this.User as ClaimsPrincipal;
	        string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
	        string userTag = "_UserId:" + sid;
	
	        // Define push message text.
	        var messageText = string.Format("New item added: {0}", item.Text);
	
	        // Build a dictionary for the template.
	        var notification = new Dictionary<string, string> { { "message", messageText } };
	
	        try
	        {
	            // Send a template notification to the user ID.
	            await hub.SendTemplateNotificationAsync(notification, userTag);
	        }
	        catch (System.Exception)
	        {
	            throw new HttpResponseException(System.Net.HttpStatusCode.InternalServerError);
	        }
	        return CreatedAtRoute("Tables", new { id = current.Id }, current);
	    }

	此程式碼只會將範本通知傳送給以目前使用者 SID 標記的註冊。請注意 SID 值可以變更，因為它衍生自驗證提供者的權杖。
 
2. 發佈伺服器專案。

<!---### <a name="nodejs"></a>Node.js backend project

1. Replace the existing code in the todoitem.js file with the following:

		var azureMobileApps = require('azure-mobile-apps'),
	    promises = require('azure-mobile-apps/src/utilities/promises'),
	    logger = require('azure-mobile-apps/src/logger');
	
		var table = azureMobileApps.table();
		
		table.insert(function (context) {
	    // For more information about the Notification Hubs JavaScript SDK, 
	    // see http://aka.ms/nodejshubs
	    logger.info('Running TodoItem.insert');

		// Get the current user SID and create a tag for the current user.
        string userTag = context.user.Id;
	    
	    // Define the template payload.
	    var payload = '{"message": context.item.text}'; 
	    
	    // Execute the insert.  The insert returns the results as a Promise,
	    // Do the push as a post-execute action within the promise flow.
	    return context.execute()
	        .then(function (results) {
	            // Only do the push if configured
	            if (context.push) {
					// Send a template notification.
	                context.push.send(userTag, payload, function (error) {
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

	This sends a template notification only to registrations tagged with the current user's SID. Note that the SID value can change since it derived from the authentication provider's token. 

2. When editing the file in your local computer, republish the server project.-->

<!---HONumber=AcomDC_1210_2015-->