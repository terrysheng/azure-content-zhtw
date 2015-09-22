<properties
	pageTitle="開始使用適用於 Chrome 應用程式的 Azure 通知中樞 | Microsoft Azure"
	description="在本教學課程中，您將了解如何使用 Azure 通知中樞，將通知推播到 Chrome 應用程式。"
	services="notification-hubs"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-chrome"
	ms.devlang="JavaScript"
	ms.topic="hero-article"
	ms.date="09/03/2015"
	ms.author="wesmc"/>

# 開始使用適用於 Chrome 應用程式的通知中樞

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

本主題將說明如何使用 Azure 通知中樞將推播通知傳送至 Chrome 應用程式。

使用 Chrome 應用程式通知的主要優點之一，是通知會顯示在 Google Chrome 瀏覽器的內容中。您不需要執行 Chrome 應用程式，也不需要在瀏覽器中開啟 (但 Chrome 瀏覽器本身必須為執行狀態)。您也可以在 [Chrome 通知] 視窗中整合檢視您所有的通知。

>[AZURE.NOTE]這不是一般瀏覽器中的推播通知，而是 Chrome 應用程式專用的通知；如需詳細資料，請參閱 [Chrome 應用程式概觀] (英文)。Chrome 應用程式以前稱為「封裝應用程式」，不同於較簡單的「代管應用程式」。若想了解相關差異，請參閱[可安裝的 Web Apps]。Chrome 應用程式也可以透過 Apache Cordova 在行動裝置 (Android 和 iOS) 上執行。若要深入了解，請參閱[行動裝置上的 Chrome 應用程式]。

在本教學課程中，我們將建立可使用 Google Cloud Messaging (GCM) 接收推播通知的 Chrome 應用程式。完成本教學課程後，您便能夠將推播通知廣播給已安裝此 Chrome 應用程式的所有 Chrome 使用者。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

* [啟用 Google Cloud Messaging](#register)
* [設定您的通知中樞](#configure-hub)
* [將您的 Chrome 應用程式連接到通知中樞](#connect-app)
* [傳送通知給您的 Chrome 應用程式](#send)
* [後續步驟](#next-steps)

本教學課程示範使用通知中樞的簡單廣播案例。設定 GCM 和 Azure 通知中樞的程序與 Android 的設定程序相同，因為 [Google Cloud Messaging for Chrome] 已停用，而相同的 GCM 現在可同時支援 Android 裝置和 Chrome 執行個體。

請確實依照教學課程的「後續步驟」一節操作，以了解如何使用通知中樞來處理特定使用者和裝置群組。

>[AZURE.NOTE]若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F)。

##<a id="register"></a>啟用 Google Cloud Messaging

1. 瀏覽至 [Google 雲端主控台]網站，並使用 Google 帳戶認證登入，然後按一下 [建立專案] 按鈕。提供適當的 [專案名稱]，然後按一下 [建立] 按鈕。

   	![][1]

2. 在 [專案] 頁面上，記下您剛才建立之專案的 [專案編號]。您將以此編號做為 Chrome 應用程式中的 [GCM 寄件者識別碼]，向 GCM 註冊。

   	![][2]

3. 在左窗格中按一下 [API 與驗證]，然後向下捲動並按一下切換開關，以啟用 [Google Cloud Messaging for Android]。您不需要啟用 **Google Cloud Messaging for Chrome**。

   	![][3]

4. 在左窗格中，按一下 [認證] > [建立新的金鑰] > [伺服器金鑰] > [建立]。

   	![][4]

5. 記下伺服器的 [API 金鑰]。您後續將會在通知中樞裡設定此金鑰，讓它能夠將推播通知傳送至 GCM。

   	![][5]

##<a id="configure-hub"></a>設定通知中樞

1. 登入 [Azure 入口網站]，並按一下畫面左下方的 [+新增]。

2. 按一下 [應用程式服務] > [服務匯流排] > [通知中樞] > [快速建立]。為您的通知中心輸入名稱、選取所需的區域，然後按一下 [Create a new Notification Hub]。

   	![][6]

4. 移至您剛剛建立的通知中樞。按一下通知中樞所屬的命名空間 (通常是 ***notification hub name*-ns**)。

   	![][7]

5. 按一下頂端的 [通知中樞] 索引標籤。

   	![][8]

6. 按一下頂端的 [設定] 索引標籤。

   	![][9]

7. 在 [設定] 索引標籤上，向下捲動至 [Google Cloud Messaging 設定]，輸入您在上一節中取得的 [API 金鑰] 值，然後按一下 [儲存]。

   	![][10]

8. 選取頂端的 [儀表板] 索引標籤，然後按一下底部的 [連接資訊]。

   	![][11]

9. 記下 **DefaultListenSharedAccessSignature** (您在 Chrome 應用程式上向通知中樞註冊時需要使用) 和 **DefaultFullSharedAccessSignature** (您在傳送通知時需要使用)。

   	![][12]

現在通知中樞已設定成使用 GCM，而且您已具備必要的連接字串可做進一步設定。

##<a id="connect-app"></a>將您的 Chrome 應用程式連接到通知中樞

###建立新的 Chrome 應用程式

下列範例以 [Chrome 應用程式 GCM 範例]為基礎，並使用建議的方式建立 Chrome 應用程式。在下列各節中，我們將加強說明 Azure 通知中樞的相關步驟。我們建議您從 [Chrome 應用程式通知中樞範例]下載此 Chrome 應用程式的原始碼。

Chrome 應用程式是透過 JavaScript 建立的，您可以使用任何慣用的文字編輯器加以建立。此 Chrome 應用程式如下所示。

   	![][15]

2. 建立資料夾，並將其命名為 **ChromePushApp** 或任何您想要的名稱。

3. 在此資料夾中，下載 [crypto-js 程式庫]中的 **cryto-js library**。此程式庫資料夾將包含兩個子資料夾：**components** 和 **rollups**。

4. 建立 **manifest.json** 檔案。所有的 Chrome 應用程式都會由資訊清單檔案備份，此檔案會說明應用程式中繼資料，特別是應用程式的可用權限。

		{
		  "name": "NH-GCM Notifications",
		  "description": "Chrome platform app.",
		  "manifest_version": 2,
		  "version": "0.1",
		  "app": {
		    "background": {
		      "scripts": ["background.js"]
		    }
		  },
		  "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
		  "icons": { "128": "gcm_128.png" }
		}

	請留意 **permissions** 元素，它會指定此 Chrome 應用程式能夠接收來自 GCM 的推播通知。它也必須指定 Azure 通知中樞 URI，其中 Chrome 應用程式將呼叫 REST 以進行註冊。此程序會使用圖示檔案 gcm\_128.png，您會在原始碼中發現此檔案重複使用於原始的 GCM 範例。您可以使用任何想要的影像。

5. 使用下列程式碼，建立名為 **background.js** 的檔案：

		// Returns a new notification ID used in the notification.
		function getNotificationId() {
		  var id = Math.floor(Math.random() * 9007199254740992) + 1;
		  return id.toString();
		}

		function messageReceived(message) {
		  // A message is an object with a data property that
		  // consists of key-value pairs.

		  // Concatenate all key-value pairs to form a display string.
		  var messageString = "";
		  for (var key in message.data) {
		    if (messageString != "")
		      messageString += ", "
		    messageString += key + ":" + message.data[key];
		  }
		  console.log("Message received: " + messageString);

		  // Pop up a notification to show the GCM message.
		  chrome.notifications.create(getNotificationId(), {
		    title: 'GCM Message',
		    iconUrl: 'gcm_128.png',
		    type: 'basic',
		    message: messageString
		  }, function() {});
		}

		var registerWindowCreated = false;

		function firstTimeRegistration() {
		  chrome.storage.local.get("registered", function(result) {

		    registerWindowCreated = true;
		    chrome.app.window.create(
		      "register.html",
		      {  width: 520,
		         height: 500,
		         frame: 'chrome'
		      },
		      function(appWin) {}
		    );
		  });
		}

		// Set up a listener for GCM message event.
		chrome.gcm.onMessage.addListener(messageReceived);

		// Set up listeners to trigger the first-time registration.
		chrome.runtime.onInstalled.addListener(firstTimeRegistration);
		chrome.runtime.onStartup.addListener(firstTimeRegistration);

	這是快顯 Chrome 應用程式視窗 HTML (**register.html**) 的檔案，且該檔案也會定義處理常式 **messageReceived** 來處理內送的推播通知。

6. 建立名為 **register.html** 的檔案以定義 Chrome 應用程式的 UI。請注意，此範例會使用 *CryptoJS v3.1.2*。如果您下載任何其他版本，請修正指令碼 src 路徑。

		<html>

		<head>
		<title>GCM Registration</title>
		<script src="register.js"></script>
		<script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
		<script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
		</head>

		<body>

		Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
		<button id="registerWithGCM">Register with GCM</button>
		<br/>
		<br/>
		<br/>

		Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
		Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

		<br/>

		<button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

		<br/>
		<br/>

		<textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

		</body>

		</html>

7. 使用下列程式碼，建立名為 **register.js** 的檔案。此檔案會指定 **register.html** 後面的指令碼。Chrome 應用程式並不允許內嵌執行，因此您必須為 UI 建立個別的備份指令碼。

		var registrationId = "";
		var hubName        = "", connectionString = "";
		var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

		window.onload = function() {
		   document.getElementById("registerWithGCM").onclick = registerWithGCM;  
		   document.getElementById("registerWithNH").onclick = registerWithNH;
		   updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
		}

		function updateLog(status) {
		  currentStatus = document.getElementById("console").innerHTML;
		  if (currentStatus != "") {
		    currentStatus = currentStatus + "\n\n";
		  }

		  document.getElementById("console").innerHTML = currentStatus  + status;
		}

		function registerWithGCM() {
		  var senderId = document.getElementById("senderId").value.trim();
		  chrome.gcm.register([senderId], registerCallback);

		  // Prevent register button from being clicked again before the registration finishes.
		  document.getElementById("registerWithGCM").disabled = true;
		}

		function registerCallback(regId) {
		  registrationId = regId;
		  document.getElementById("registerWithGCM").disabled = false;

		  if (chrome.runtime.lastError) {
		    // When the registration fails, handle the error and retry the
		    // registration later.
		    updateLog("Registration failed: " + chrome.runtime.lastError.message);
		    return;
		  }

		  updateLog("Registration with GCM succeeded.");
		  document.getElementById("registerWithNH").disabled = false;

		  // Mark that the first-time registration is done.
		  chrome.storage.local.set({registered: true});
		}

		function registerWithNH() {
		  hubName = document.getElementById("hubName").value.trim();
		  connectionString = document.getElementById("connectionString").value.trim();
		  splitConnectionString();
		  generateSaSToken();
		  sendNHRegistrationRequest();
		}

		// From http://msdn.microsoft.com/library/dn495627.aspx
		function splitConnectionString()
		{
		  var parts = connectionString.split(';');
		  if (parts.length != 3)
		  throw "Error parsing connection string";

		  parts.forEach(function(part) {
		    if (part.indexOf('Endpoint') == 0) {
		    endpoint = 'https' + part.substring(11);
		    } else if (part.indexOf('SharedAccessKeyName') == 0) {
		    sasKeyName = part.substring(20);
		    } else if (part.indexOf('SharedAccessKey') == 0) {
		    sasKeyValue = part.substring(16);
		    }
		  });

		  originalUri = endpoint + hubName;
		}

		function generateSaSToken()
		{
		  targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
		  var expiresInMins = 10; // 10 minute expiration

		  // Set expiration in seconds.
		  var expireOnDate = new Date();
		  expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
		  var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
		    .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
		    .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
		    .getUTCSeconds()) / 1000;
		  var tosign = targetUri + '\n' + expires;

		  // Using CryptoJS.
		  var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
		  var base64signature = signature.toString(CryptoJS.enc.Base64);
		  var base64UriEncoded = encodeURIComponent(base64signature);

		  // Construct authorization string.
		  sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
		                  + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
		}

		function sendNHRegistrationRequest()
		{
		  var registrationPayload =
		  "<?xml version="1.0" encoding="utf-8"?>" +
		  "<entry xmlns="http://www.w3.org/2005/Atom">" +
		      "<content type="application/xml">" +
		          "<GcmRegistrationDescription xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect">" +
		              "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
		          "</GcmRegistrationDescription>" +
		      "</content>" +
		  "</entry>";

		  // Update the payload with the registration ID obtained earlier.
		  registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

		  var url = originalUri + "/registrations/?api-version=2014-09";
		  var client = new XMLHttpRequest();

		  client.onload = function () {
		    if (client.readyState == 4) {
		      if (client.status == 200) {
		        updateLog("Notification Hub Registration succesful!");
		        updateLog(client.responseText);
		      } else {
		        updateLog("Notification Hub Registration did not succeed!");
		        updateLog("HTTP Status: " + client.status + " : " + client.statusText);
		        updateLog("HTTP Response: " + "\n" + client.responseText);
		      }
		    }
		  };

		  client.onerror = function () {
		        updateLog("ERROR - Notification Hub Registration did not succeed!");
		  }

		  client.open("POST", url, true);
		  client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
		  client.setRequestHeader("Authorization", sasToken);
		  client.setRequestHeader("x-ms-version", "2014-09");

		  try {
		      client.send(registrationPayload);
		  }
		  catch(err) {
		      updateLog(err.message);
		  }
		}

	上述指令碼有下列的輸出：
	- *window.onload* 定義 UI 上兩個按鈕的按鈕點擊事件 - 一個按鈕向 GCM 註冊，另一個在向 GCM 註冊後使用傳回的註冊識別碼向 Azure 通知中樞註冊。
	- *updateLog* 函數會定義簡單的記錄函數。
	- *registerWithGCM* 是第一個按鈕點擊處理常式，可向 GCM 進行 *chrome.gcm.register* 呼叫，以註冊此 Chrome 應用程式執行個體。
	- *registerCallback* 是回呼函數，會在上述 GCM 註冊呼叫傳回時受到呼叫。
	- *registerWithNH* 是第二個按鈕點擊處理常式，會向通知中樞進行註冊。它會取得使用者已指定的 *hubName* 和 *connectionString* 並製作通知中樞註冊 REST API 呼叫。
	- *splitConnectionString* 和 *generateSaSToken* 都是建立 SaS 權杖的 Javascript 實作，必須在所有的 REST API 呼叫中傳送。相關資訊請見 - 如需詳細資訊，請參閱 [般概念](http://msdn.microsoft.com/library/dn495627.aspx) 
	- *sendNHRegistrationRequest* 是發出 HTTP REST 呼叫的函數。
	- *registrationPayload* 會定義註冊 xml 裝載。相關資訊請見 - [建立註冊 NH REST API]。我們會以接收自 GCM 的項目來更新其中的註冊識別碼。
	- *client* 是我們用來發出 HTTP POST 要求的 *XMLHttpRequest* 執行個體。請注意，我們會使用 sasToken 更新 *Authorization* 標頭。成功完成此呼叫後，即會向 Azure 通知中樞註冊此 Chrome 應用程式執行個體。


您應該會在結尾處看見下列資料夾檢視：
   	![][21]

###設定和測試 Chrome 應用程式

1. 開啟 Chrome 瀏覽器。開啟 [Chrome 擴充功能]，並啟用 [開發人員模式]。

   	![][16]

2. 按一下 [載入未封裝的擴充功能]，並瀏覽至您在其中建立檔案的資料夾。您也可以選擇性地使用 **Chrome Apps & Extensions Developer Tool**。此工具本身為 Chrome 應用程式 (從 Chrome 線上應用程式商店進行安裝)，且提供 Chrome 應用程式開發進階偵錯功能。

   	![][17]

3. 如果該 Chrome 應用程式在建立時未發生任何錯誤，則您將會看見該應用程式顯示。

   	![][18]

4. 輸入您先前從 [Google 雲端主控台] 取得的 [專案編號]，做為寄件者識別碼，然後按一下 [向 GCM 註冊]。您必須看見 **Registration with GCM succeeded.** 訊息。

   	![][19]

5. 輸入您先前從 Azure 入口網站取得的 [通知中樞名稱] 和 [DefaultListenSharedAccessSignature]，然後按一下 [向 Azure 通知中樞註冊]。您必須看見 **Notification Hub Registration succesful!** 訊息和註冊回應的詳細資料，其中包含 Azure 通知中樞註冊識別碼。

   	![][20]

##<a name="send"></a>傳送通知給您的 Chrome 應用程式

在本教學課程中，您將使用 .NET 主控台應用程式來傳送通知。但您可以透過 <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST 介面</a>，使用通知中樞從任何後端傳送通知。

如需從整合通知中樞之 Azure 行動服務後端傳送通知的範例，請參閱「開始在行動服務中使用推播通知」([.NET 後端](../mobile-services-javascript-backend-android-get-started-push.md) | [JavaScript 後端](../mobile-services-javascript-backend-android-get-started-push.md))。如需如何使用 REST API 傳送通知的範例，請參閱「如何從 Java/PHP/Python 使用通知中樞」([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md) | [Python](notification-hubs-python-backend-how-to.md))。

1. 在 Visual Studio 的 [檔案] 功能表中，選取 [新增]，然後選取 [專案]。在 [Visual C#] 下方，按一下 [Windows] 和 [主控台應用程式]，再按一下 [確定]。這會建立新的主控台應用程式專案。

2. 在 [工具] 功能表中，依序按一下 [Library Package Manager] 及 [Package Manager Console]。這會顯示 [Package Manager Console]。

3. 在主控台視窗中，執行下列命令：

        Install-Package WindowsAzure.ServiceBus

   	這會使用 <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 封裝</a>新增 Azure 服務匯流排 SDK 的參考。

4. 開啟 **Program.cs** 檔案，並新增下列 `using` 陳述式：

        using Microsoft.ServiceBus.Notifications;

5. 在 **Program** 類別中，新增下列方法：

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{"data":{"message":"Hello Chrome from Azure Notification Hubs"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

   	請務必使用出現在入口網站 [通知中樞] 索引標籤上的通知中樞名稱，來取代 *hub name* 預留位置。此外，請將連接字串預留位置替換為您在「設定通知中樞」一節中取得的 **DefaultFullSharedAccessSignature** 連接字串。

	>[AZURE.NOTE]請確定您使用的連接字串具有 [**完整**] 存取權，而非 [**接聽**] 存取權。**接聽**存取權的字串沒有傳送通知的權限。

5. 在 **[主要]** 方法中新增下列命令列。

         SendNotificationAsync();
		 Console.ReadLine();

6. 請確定您已開啟 Chrome 瀏覽器。為此並不需要開啟 Chrome 應用程式。您應該會在桌面上看見下列通知快顯視窗。

   	![][13]

7. 當 Chrome 執行時，您也可以使用工作列 (在 Windows 上) 上的 [Chrome 通知] 視窗來查看所有的通知。

   	![][14]

## <a name="next-steps"> </a>後續步驟

在此簡單範例中，您已將通知廣播到 Chrome 應用程式。請在[通知中樞概觀]中進一步了解通知中樞。若要針對特定使用者，請參閱本教學課程 [Azure 通知中樞通知使用者]。如果您想要按興趣群組分隔使用者，可參閱 [Azure 通知中樞即時新聞]。

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Chrome 應用程式通知中樞範例]: http://google.com
[Google 雲端主控台]: http://cloud.google.com/console
[Azure 入口網站]: https://manage.windowsazure.com/
[通知中樞概觀]: http://msdn.microsoft.com/library/jj927170.aspx
[Chrome 應用程式概觀]: https://developer.chrome.com/apps/about_apps
[Chrome 應用程式 GCM 範例]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[可安裝的 Web Apps]: https://developers.google.com/chrome/apps/docs/
[行動裝置上的 Chrome 應用程式]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[建立註冊 NH REST API]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[crypto-js 程式庫]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure 通知中樞通知使用者]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Azure 通知中樞即時新聞]: notification-hubs-windows-store-dotnet-send-breaking-news.md

<!----HONumber=Sept15_HO2-->