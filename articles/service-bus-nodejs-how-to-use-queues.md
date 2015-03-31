<properties 
	pageTitle="如何使用服務匯流排佇列 (Node.js) - Azure" 
	description="了解如何在 Azure 使用服務匯流排佇列。程式碼範例以 Node.js 撰寫。" 
	services="service-bus" 
	documentationCenter="nodejs" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>






# 如何使用服務匯流排佇列

本指南將說明如何使用服務匯流排佇列。這些範例均以 JavaScript 撰寫並使用 Node.js Azure 模組。本文說明的案例包括**建立佇列、傳送並接收訊息**和
**刪除佇列**。如需佇列的詳細資訊，請參閱[後續
步驟]一節。

[AZURE.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## 建立 Node.js 應用程式

建立空白的 Node.js 應用程式。如需建立 Node.js 應用程式的相關指示，請參閱[建立 Node.js 應用程式並將其部署到 Azure 網站]、[Node.js 雲端服務][Node.js Cloud Service] (使用 Windows PowerShell) 或[使用 WebMatrix 的網站]。

## 設定應用程式使用服務匯流排

若要使用 Azure 服務匯流排，您需要下載及使用 Node.js azure 套件。套件含有一組能與服務匯流排 REST 服務通訊的便利程式庫。

### 使用 Node Package Manager (NPM) 取得封裝

1.  使用 **Windows PowerShell for Node.js** 命令視窗瀏覽至您建立範例應用程式的 **c:\\node\\sbqueues\\WebRole1** 資料夾。

2.  在命令視窗中輸入 **npm install azure**，這應該會產生類似下列內容的輸出：

        azure@0.7.5 node_modules\azure
		├── dateformat@1.0.2-1.2.3
		├── xmlbuilder@0.4.2
		├── node-uuid@1.2.0
		├── mime@1.2.9
		├── underscore@1.4.4
		├── validator@1.1.1
		├── tunnel@0.0.2
		├── wns@0.5.3
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  您可以手動執行 **ls** 命令，確認已建立
    已建立 **node\_modules** 資料夾。在該資料夾內找出
    **azure** 封裝，其中含有存取服務匯流排佇列所需的
    程式庫。

### 匯入模組

使用 [記事本] 或其他文字編輯器，將以下內容新增至應用程式的 **server.js** 檔案頂端：

    var azure = require('azure');

### 設定 Azure 服務匯流排連接

Azure 模組將讀取環境變數 AZURE\_SERVICEBUS\_NAMESPACE 和 AZURE\_SERVICEBUS\_ACCESS\_KEY，以取得連接至 Azure 服務匯流排所需的資訊。如果未設定這些環境變數，則在呼叫 **createServiceBusService** 時必須指定帳戶資訊。

如需在 Azure 雲端服務組態檔中設定環境變數的範例，請參閱[使用儲存體的 Node.js 雲端服務]。

如需在 Azure 網站管理入口網站中設定環境變數的範例，請參閱[使用儲存體的 Node.js Web 應用程式]。

## 如何建立佇列

**ServiceBusService** 物件可讓您處理佇列。下列程式碼將建立 **ServiceBusService** 物件。請將程式碼新增至 **server.js** 檔案的頂端附近，放置在匯入 azure 模型的陳述式後方：

    var serviceBusService = azure.createServiceBusService();

呼叫 **ServiceBusService** 物件上的 **createQueueIfNotExists**，將傳回指定的佇列 (如果存在)，或是利用指定的名稱建立新佇列。下列程式碼使用
**createQueueIfNotExists** 來建立或連接名為 'myqueue' 的
佇列：

    serviceBusService.createQueueIfNotExists('myqueue', function(error){
        if(!error){
            // Queue exists
        }
    });

**createServiceBusService** 也支援其他選項，讓您能夠覆寫預設佇列設定，例如，訊息存留時間或佇列大小上限。下列範例說明將佇列大小上限設為 5GB，將存留時間設為 1 分鐘的設定：

    var queueOptions = {
          MaxSizeInMegabytes: '5120',
          DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
        if(!error){
            // Queue exists
        }
    });

### 篩選器

您可以將選用的篩選作業套用至使用 **ServiceBusService** 執行的作業。篩選作業可包括記錄、自動重試等等。篩選器是以簽章實作方法的物件：

		function handle (requestOptions, next)

在對要求選項進行前處理之後，方法需要呼叫 "next" 並傳遞具有下列簽章的回呼：

		function (returnObject, finalCallback, next)

在此回呼中，以及處理 returnObject (來自對伺服器之要求的回應) 之後，回呼需要叫用 next (如果存在) 以繼續處理其他篩選，或是就改為叫用 finalCallback 結束服務叫用。

Azure SDK for Node.js 包含了實作重試邏輯的兩個篩選器：**ExponentialRetryPolicyFilter** 和 **LinearRetryPolicyFilter**。下列程式碼將建立使用 **ExponentialRetryPolicyFilter** 的 **ServiceBusService** 物件：

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## 如何傳送訊息至佇列

若要傳送訊息至服務匯流排佇列，應用程式將對於 **ServiceBusService** 物件呼叫 **sendQueueMessage** 方法。傳送至服務匯排流 (以及服務匯流排接收) 的佇列是 **BrokeredMessage** 物件，而且具有一組標準屬性 (例如 **Label** 和 **TimeToLive**)、一個用來保存自訂應用程式特定屬性的目錄，以及一堆任意的應用程式資料。應用程式可傳遞字串值做為訊息來設定訊息內文，而預設值將填入任何所需的標準內容。

下列範例示範如何使用 **sendQueueMessage** 將測試訊息傳送至名為  'myqueue' 的佇列：

    var message = {
        body: 'Test message',
        customProperties: {
            testproperty: 'TestValue'
        }};
    serviceBusService.sendQueueMessage('myqueue', message, function(error){
        if(!error){
            // message sent
        }
    });

服務匯流排佇列最多可支援 256 KB 的訊息大小 (包含標準和自訂應用程式屬性的標頭可以容納 64 KB 的大小上限)。佇列中所保存的訊息數目沒有限制，但佇列所保存的訊息大小總計會有最高限制。此佇列大小會在建立時定義，上限是 5 GB。

## 如何從佇列接收訊息

對於 **ServiceBusService** 物件使用 **receiveQueueMessage** 方法，可從佇列接收訊息。根據預設，當您讀取訊息後，訊息便會從佇列中刪除，不過，您可以將選用參數 **isPeekLock** 設為 **true**，來讀取 (查看) 並鎖定訊息，避免系統從佇列刪除訊息。

隨著接收作業讀取及刪除訊息之預設行為是最簡單的模型，且最適合可容許在發生失敗時不處理訊息的應用程式案例。若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

如果您將 **isPeekLock** 參數設定為 **true**，接收會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。
在應用程式完成處理訊息 (或可靠地儲存此訊息以供未來處理) 之後，它可透過呼叫 **deleteMessage** 方法和以參數形式提供要刪除的訊息，完成接收程序的第二個階段。**deleteMessage** 方法會將訊息標示為已取用，並將它從佇列中移除。

以下範例將示範如何使用 **receiveQueueMessage** 來接收與處理訊息。範例首先會接收並刪除訊息，然後使用設定為 true 的 **isPeekLock** 接收訊息，接著使用 **deleteMessage** 刪除訊息：

    serviceBusService.receiveQueueMessage('taskqueue', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
        }
    });
    serviceBusService.receiveQueueMessage(queueName, { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                }
            }
        }
    });

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。如果接收者應用程式因為某些原因無法處理訊息，則它可以呼叫 **ServiceBusService** 物件上的 **unlockMessage** 方法。這將導致服務匯流排將佇列中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在佇列內鎖定之訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

如果應用程式在處理訊息之後，但在尚未呼叫 **deleteMessage** 方法之前當機，則會在應用程式重新啟動時，將訊息重新傳遞給該應用程式。這通常稱為「至少處理一次」****，也就是說，每個訊息至少會處理一次，但在特定狀況下，可能會重新傳遞相同訊息。如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。通常您可使用訊息的
**MessageId** 屬性來達到此目的，該屬性在每次嘗試傳遞時都將保持為常數。

## 後續步驟

了解基本的服務匯流排佇列之後，請參考下列連結以取得更多資訊。

-   請參閱 MSDN 參考：[佇列、主題和訂閱。][]
-   造訪 GitHub 上的 [Azure SDK for Node] 儲存機制。

  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [後續步驟]: #next-steps
  [什麼是服務匯流排佇列？]: #what-are-service-bus-queues
  [建立服務命名空間]: #create-a-service-namespace
  [取得命名空間的預設管理認證]: #obtain-default-credentials
  [建立 Node.js 應用程式]: #create-app
  [設定應用程式使用服務匯流排]: #configure-app
  [作法：建立佇列]: #create-queue
  [作法：傳送訊息至佇列]: #send-messages
  [作法：從佇列接收訊息]: #receive-messages
  [作法：處理應用程式當機與無法讀取的訊息]: #handle-crashes
  [佇列概念]: ../../dotNet/Media/sb-queues-08.png
  [Azure 管理入口網站]: http://manage.windowsazure.com
  
  
  
  
  
  [Node.js 雲端服務]: /documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [佇列、主題和訂閱。]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
  [使用 WebMatrix 的網站]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[先前的管理入口網站]: ../../Shared/Media/previous-portal.png
  [建立 Node.js 應用程式並部署到 Azure 網站]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [使用儲存體的 Node.js 雲端服務]: /develop/nodejs/tutorials/web-app-with-storage/
  [使用儲存體的 Node.js Web 應用程式]: /develop/nodejs/tutorials/web-site-with-storage/

<!--HONumber=47-->
