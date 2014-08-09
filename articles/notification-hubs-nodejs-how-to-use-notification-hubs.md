<properties linkid="develop-nodejs-how-to-guides-service-bus-notification-hubs" urlDisplayName="Notification Hubs" pageTitle="Service Bus Notification Hubs - Node.js Dev Center" metaKeywords="" description="Learn how to use Service Bus Notification Hubs to send push notifications. Code samples are written for Node.js applications." metaCanonical="" services="service-bus" documentationCenter="Node.js" title="How to Use Service Bus Notification Hubs" authors="larryfr" solutions="" manager="" editor="" />

如何使用服務匯流排通知中心
==========================

本指南將說明如何從 Node.js 應用程式使用服務匯流排通知中心。本文說明的案例包括**傳送通知到 Android、iOS、Windows Phone 和 Windows 市集應用程式**。如需通知中心的詳細資訊，請參閱[後續步驟](#next)一節。

目錄
----

-   [什麼是服務匯流排通知中心？](#hub)
-   [建立 Node.js 應用程式](#create)
-   [設定應用程式以使用服務匯流排](#config)
-   [作法：傳送通知](#send)
-   [後續步驟](#next)

什麼是服務匯流排通知中心？
--------------------------

Azure 服務匯流排通知中心提供易用、多平台、可擴充的基礎結構，用以將推播通知傳送至行動裝置。如需詳細資訊，請參閱 [Azure 服務匯流排通知中心](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj927170.aspx)。

建立 Node.js 應用程式
---------------------

建立空白的 Node.js 應用程式。如需建立 Node.js 應用程式的相關指示，請參閱[建立 Node.js 應用程式並將其部署到 Azure 網站](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/)、[Node.js 雲端服務](/en-us/documentation/articles/cloud-services-nodejs-develop-deploy-app/) (使用 Windows PowerShell) 或[使用 WebMatrix 的網站](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/)。

設定應用程式使用服務匯流排
--------------------------

若要使用 Azure 服務匯流排，您需要下載及使用 Node.js azure 套件。套件含有一組能與服務匯流排 REST 服務通訊的便利程式庫。

### 使用 Node Package Manager (NPM) 來取得套件

1.  使用 **PowerShell** (Windows)、**終端機** (Mac) 或 **Bash** (Unix) 等命令列介面，瀏覽至建立範例應用程式的資料夾。

2.  在命令視窗中輸入 **npm install azure**，該命令應能產生以下輸出：

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

3.  您可以手動執行 **ls** 或 **dir** 命令，以確認 **node\_modules** 資料夾是否已建立。在該資料夾內找出 **azure** 封裝，其中含有存取服務匯流排通知中心所需的程式庫。

### 匯入模組

使用文字編輯器，將以下內容新增至應用程式的 **server.js** 檔案頂端：

    var azure = require('azure');

### 設定 Azure 服務匯流排連線

**NotificationHubService** 物件可讓您使用通知中心。下列程式碼會為名為 **hubname** 的通知中心建立 **NotificationHubService** 物件。請將程式碼新增至 **server.js** 檔案的頂端附近，放置在匯入 azure 模型的陳述式後方：

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

執行下列步驟，可從 Azure 管理入口網站取得連線 **connectionstring** 值：

1.  從 Azure 管理入口網站中選取 **[服務匯流排]**，然後選取包含通知中心的命名空間。

2.  選取 **[通知中心]**，然後選取您要使用的中心。

3.  從 **[quick glance]** 區段中選取 **[檢視連接字串]**，然後複製連接字串值。

**注意**

您也可以使用 Azure PowerShell 所提供的 **Get-AzureSbNamespace** Cmdlet 來擷取連接字串，或透過 Azure 命令列工具使用 **azure sb namespace show** 命令來擷取。

如何傳送通知
------------

**NotificationHubService** 物件會公開下列可將通知傳送至特定裝置和應用程式的物件執行個體：

-   **Android** - 請使用 **GcmService** 物件，此物件可從 **notificationHubService.gcm** 取得
-   **iOS** - 請使用 **ApnsService** 物件，此物件可從 **notificationHubService.apns** 存取
-   **Windows Phone** - 請使用 **MpnsService** 物件，此物件可從 **notificationHubService.mpns** 取得
-   **Windows 市集應用程式** - 請使用 **WnsService** 物件，此物件可從 **notificationHubService.wns** 取得

### 如何傳送 Android 應用程式通知

**GcmService** 物件會提供可用來將通知傳送至 Android 應用程式的 **send** 方法。此 **send** 方法可接受下列參數：

-   Tags - 標籤識別碼。若未提供標籤，通知將會傳送至所有用戶端
-   Payload - 訊息的 JSON 或字串裝載
-   Callback - 回呼函數

如需裝載格式的詳細資訊，請參閱[實作 GCM 伺服器](http://developer.android.com/google/gcm/server.html#payload)的「裝載」一節。

下列程式碼會使用 **NotificationHubService** 所公開的 **GcmService** 執行個體，將訊息傳送至所有用戶端。

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

### 如何傳送 iOS 應用程式通知

**ApnsService** 物件會提供可用來將通知傳送至 iOS 應用程式的 **send** 方法。此 **send** 方法可接受下列參數：

-   Tags - 標籤識別碼。若未提供標籤，通知將會傳送至所有用戶端
-   Payload - 訊息的 JSON 或字串裝載
-   Callback - 回呼函數

如需裝載格式的詳細資訊，請參閱[本機與推播通知程式設計指南](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html)的「通知裝載」一節。

下列程式碼會使用 **NotificationHubService** 所公開的 **ApnsService** 執行個體，將警示訊息傳送至所有用戶端：

	var payload={ 
	    alert: 'Hello!'
	  };
	notificationHubService.apns.send(null, payload, function(error){
	  if(!error){
 	    // notification sent
      }
	});

### 如何傳送 Windows Phone 通知

**MpnsService** 物件會提供可用來將通知傳送至 Windows Phone 應用程式的 **send** 方法。此 **send** 方法可接受下列參數：

-   Tags - 標籤識別碼。若未提供標籤，通知將會傳送至所有用戶端
-   Payload - 訊息的 XML 裝載
-   TargetName - 快顯通知的「快顯」。磚通知的「權杖」。
-   NotificationClass - 通知的優先順序。如需有效值，請參閱[從伺服器推播通知](http://msdn.microsoft.com/zh-tw/library/hh221551.aspx)的「HTTP 標頭元素」一節。
-   Options - 選用的要求標頭
-   Callback - 回呼函數

如需有效 TargetName、NotificationClass 和標頭選項的清單，請參閱[從伺服器推播通知](http://msdn.microsoft.com/zh-tw/library/hh221551.aspx)。

下列程式碼會使用 **NotificationHubService** 所公開的 **MpnsService** 執行個體，來傳送快顯通知警示：

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### 如何傳送 Windows 市集應用程式通知

**WnsService** 物件會提供可用來將通知傳送至 Windows 市集應用程式的 **send** 方法。此 **send** 方法可接受下列參數：

-   Tags - 標籤識別碼。若未提供標籤，通知將會傳送至所有用戶端
-   Payload - XML 訊息裝載
-   Type - 通知類型
-   Options - 選用的要求標頭
-   Callback - 回呼函數

如需有效類型和要求標頭的清單，請參閱[推播通知服務要求和回應標頭](http://msdn.microsoft.com/zh-tw/library/windows/apps/hh465435.aspx)。

下列程式碼會使用 **NotificationHubService** 所公開的 **WnsService** 執行個體，來傳送快顯通知警示：

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

後續步驟
--------

了解基本的服務匯流排主題之後，請參考下列連結以取得更多資訊。

-   請參閱 MSDN 參考：[Azure 服務匯流排通知中心](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj927170.aspx)。
-   請造訪 GitHub 上的 [Azure SDK for Node](https://github.com/WindowsAzure/azure-sdk-for-node) 儲存機制 (英文)。

