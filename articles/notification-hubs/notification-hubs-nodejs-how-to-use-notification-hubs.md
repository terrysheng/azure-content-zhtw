<properties
	pageTitle="使用 Azure 通知中樞和 Node.js 傳送推播通知"
	description="了解如何使用通知中樞，從 Node.js 應用程式傳送推播通知。"
    keywords="推播通知, 推播通知, node.js 推播,ios 推播"
	services="notification-hubs"
	documentationCenter="nodejs"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="03/08/2015"
	ms.author="wesmc"/>

# 使用 Azure 通知中樞和 Node.js 傳送推播通知
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

##概觀

> [AZURE.IMPORTANT] 若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs)。

本指南將會示範如何透過 Azure 通知中樞的協助，直接從 Node.js 應用程式傳送推播通知。

涵蓋的案例包括在下列平台將推播通知傳送至應用程式：

* Android
* iOS
* Windows Phone
* 通用 Windows 平台 

如需通知中心的詳細資訊，請參閱[後續步驟](#next)一節。

##什麼是通知中心？

Azure 通知中樞提供易用、多平台、可調整的基礎結構，用以將推播通知傳送至行動裝置。如需服務基礎結構的詳細資訊，請參閱 [Azure 通知中樞](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx)頁面。

##建立 Node.js 應用程式

本教學課程的第一個步驟是建立新的空白 Node.js 應用程式。如需建立 Node.js 應用程式的相關指示，請參閱[建立 Node.js 應用程式並將其部署到 Azure 網站][nodejswebsite]、[Node.js 雲端服務][Node.js Cloud Service] (使用 Windows PowerShell) 或[使用 WebMatrix 的網站]。

##將應用程式設為使用通知中樞

若要使用 Azur通知中樞，您需要下載並使用 Node.js [azure 封裝](https://www.npmjs.com/package/azure)，這包含一組內建的協助程式庫，能與推播通知 REST 服務通訊。

### 使用 Node Package Manager (NPM) 取得封裝

1.  使用命令列介面，例如 PowerShell (Windows)、終端機 (Mac) 或 Bash (Linux)，瀏覽至建立空白應用程式所在的資料夾。

2.  在命令視窗中輸入 npm install azure，這應該會產生類似下列內容的輸出 (版本號碼可能會不同)：

        azure@0.7.0 node_modules\azure
		|-- dateformat@1.0.2-1.2.3
		|-- xmlbuilder@0.4.2
		|-- node-uuid@1.2.0
		|-- mime@1.2.9
		|-- underscore@1.4.4
		|-- validator@0.4.28
		|-- tunnel@0.0.2
		|-- wns@0.5.3
		|-- xml2js@0.2.6 (sax@0.4.2)
		|-- request@2.16.6 (forever-agent@0.2.0, aws-sign@0.2.0, tunnel-agent@0.2.0, oauth-sign@0.2.0, json-stringify-safe@3.0.0, cookie-jar@0.2.0, node-uuid@1.4.0, qs@0.5.5, hawk@0.10.2, form-data@0.0.7)

3.  您可以手動執行 **ls** 或 **dir** 命令，以確認 **node\_modules** 資料夾是否已建立。在該資料夾內找到 azure 套件，其中包含您存取通知中樞所需的程式庫。

>[AZURE.NOTE] 您可以在官方 [NPM 部落格](http://blog.npmjs.org/post/85484771375/how-to-install-npm)進一步了解安裝 NPM。

### 匯入模組

使用文字編輯器，將以下內容新增至應用程式的 server.js 檔案頂端：

    var azure = require('azure');

### 設定 Azure 通知中樞連線

NotificationHubService 物件可讓您使用通知中心。下列程式碼會為名為 hubname 的通知中心建立 NotificationHubService 物件。請將程式碼新增至 server.js 檔案的頂端附近，放置在匯入 azure 模型的陳述式後方：

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

執行下列步驟，可從 [Azure 入口網站]取得連接 connectionstring 值：

1. 在左導覽窗格中，按一下 [瀏覽]。

2. 選取 [通知中樞]，然後尋找您要用於範例的中樞。如果您需要建立新的通知中樞的說明，您可以參考 [Windows 市集開始使用教學課程](notification-hubs-windows-store-dotnet-get-started.md)。

3. 選取 [Settings] \(設定)。

4. 按一下 [存取原則]。您會看到兩個共用和完整存取連接字串。

![Azure 入口網站 - 通知中樞](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [AZURE.NOTE] 您也可以使用 [Azure PowerShell](../powershell-install-configure.md) 所提供的 Get-AzureSbNamespace Cmdlet，或透過 [Azure 命令列介面 (Azure CLI)](../xplat-cli-install.md) 使用 azure sb namespace show 命令，來擷取連接字串。

##一般架構

NotificationHubService 物件會公開下列可將推播通知傳送至特定裝置和應用程式的物件執行個體：

* Android - 請使用 GcmService 物件，此物件可從 notificationHubService.gcm 取得
* iOS - 請使用 ApnsService 物件，此物件可從 notificationHubService.apns 存取
* Windows Phone - 請使用 MpnsService 物件，此物件可從 notificationHubService.mpns 取得
* 通用 Windows 平台 - 請使用 WnsService 物件，此物件可從 notificationHubService.wns 取得

### 做法：將推播通知傳送至 Android 應用程式

GcmService 物件會提供可用來將推播通知傳送至 Android 應用程式的 send 方法。此 send 方法可接受下列參數：

* Tags - 標籤識別碼。若未提供標籤，通知將會傳送至所有用戶端。
* Payload - 訊息的 JSON 或原始字串裝載。
* Callback - 回呼函數。

如需裝載格式的詳細資訊，請參閱[實作 GCM 伺服器](http://developer.android.com/google/gcm/server.html#payload)文件的「裝載」一節。

下列程式碼會使用 NotificationHubService 所公開的 GcmService 執行個體，將推播通知傳送至所有註冊的用戶端。

	var payload = {
	  data: {
	    msg: 'Hello!'
	  }
	};
	notificationHubService.gcm.send(null, payload, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### 做法：將推播通知傳送至 iOS 應用程式

與上述的 Android 應用程式一樣，ApnsService 物件會提供可用來將推播通知傳送至 iOS 應用程式的 send 方法。此 send 方法可接受下列參數：

* Tags - 標籤識別碼。若未提供標籤，通知將會傳送至所有用戶端。
* Payload - 訊息的 JSON 或字串裝載。
* Callback - 回呼函數。

如需裝載格式的詳細資訊，請參閱[本機與推播通知程式設計指南](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html)文件的「通知裝載」一節。

下列程式碼會使用 NotificationHubService 所公開的 ApnsService 執行個體，將警示訊息傳送至所有用戶端：

	var payload={
	    alert: 'Hello!'
	  };
	notificationHubService.apns.send(null, payload, function(error){
	  if(!error){
 	    // notification sent
      }
	});

### 做法：將推播通知傳送至 Windows Phone 應用程式

MpnsService 物件會提供可用來將推播通知傳送至 Windows Phone 應用程式的 send 方法。此 send 方法可接受下列參數：

* Tags - 標籤識別碼。若未提供標籤，通知將會傳送至所有用戶端。
* Payload - 訊息的 XML 裝載。
* TargetName - `toast` 用於快顯通知。`token` 用於圖格通知。
* NotificationClass - 通知的優先順序。如需有效值，請參閱[從伺服器推播通知](http://msdn.microsoft.com/library/hh221551.aspx)文件的「HTTP 標頭元素」一節。
* Options - 選用的要求標頭。
* Callback - 回呼函數。

如需有效 TargetName、NotificationClass 和標頭選項的清單，請參閱[從伺服器推播通知](http://msdn.microsoft.com/library/hh221551.aspx)頁面。

下列範例程式碼會使用 NotificationHubService 所公開的 MpnsService 執行個體，來傳送快顯推播通知：

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### 做法：將推播通知傳送至通用 Windows 平台 (UWP) 應用程式

WnsService 物件會提供可用來將推播通知傳送至通用 Windows 平台應用程式的 send 方法。此 send 方法可接受下列參數：

* Tags - 標籤識別碼。若未提供標籤，通知將會傳送至所有註冊的用戶端。
* Payload - XML 訊息裝載。
* Type - 通知類型。
* Options - 選用的要求標頭。
* Callback - 回呼函數。

如需有效類型和要求標頭的清單，請參閱[推播通知服務要求和回應標頭](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx)。

下列程式碼會使用 NotificationHubService 所公開的 MpnsService 執行個體，將快顯推播通知傳送至 UWP 應用程式：

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

## 後續步驟

上述的範例程式碼片段可讓您輕鬆地建置服務基礎結構，將推播通知傳遞到各種裝置。了解基本的透過 node.js 的通知中樞使用方式之後，請參考下列連結以了解如何進一步延伸這些功能。

-   請參閱 [Azure 通知中樞](https://msdn.microsoft.com/library/azure/jj927170.aspx)的 MSDN 參考。
-   造訪 GitHub 上的 [Azure SDK for Node] 儲存機制以取得更多範例和實作詳細資料。

  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Classic Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [使用 WebMatrix 的網站]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
  [Azure 入口網站]: https://portal.azure.com

<!---HONumber=AcomDC_0309_2016-->
