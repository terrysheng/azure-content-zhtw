



傳送範本通知時，您只需提供一組屬性，在我們的案例中，我們將傳送一組包含已當地語系化版本的目前新聞屬性，例如：

	{
		"News_English": "World News in English!",
    	"News_French": "World News in French!",
    	"News_Mandarin": "World News in Mandarin!"
	}


本節將使用主控台應用程式示範傳送通知的方式

後端可廣播至任何支援的裝置，因此隨附的程式碼會廣播至 Windows 市集和 iOS 裝置。


### 使用 C# 主控台應用程式傳送通知 

在先前建立的主控台應用程式中以下列程式碼修改 `SendTemplateNotificationAsync` 方法。請注意，此案例不需要針對不同地區設定和平台傳送多次通知。

        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
				NotificationHubClient.CreateClientFromConnectionString(
					"<connection string with full access>", "<hub name>");

            // Sending the notification as a template notification. All template registrations that contain 
			// "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
			// This includes APNS, GCM, WNS, and MPNS template registrations.
            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
            var locales = new string[] { "English", "French", "Mandarin" };

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";

                // Sending localized News for each tag too...
                foreach( var locale in locales)
                {
                    string key = "News_" + locale;

					// Your real localized news content would go here.
                    templateParams[key] = "Breaking " + category + " News in " + locale + "!";
                }

                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
        }


請注意，此簡單呼叫會將已當地語系化的新聞片段傳送到您的**所有**裝置 (不論平台為何)，因為您的通知中樞會建立並傳遞正確的原生裝載給訂用特定標籤的所有裝置。

### 使用行動服務傳送通知

在您的行動服務排程器中，您可以使用下列指令碼：

	var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
			"News_English": "World News in English!",
			"News_French": "World News in French!",
			"News_Mandarin", "World News in Mandarin!"
	}
	notificationHubService.send('World', notification, function(error) {
		if (!error) {
			console.warn("Notification successful");
		}
	});
	

<!---HONumber=AcomDC_1217_2015-->