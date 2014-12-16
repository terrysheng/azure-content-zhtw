<properties urlDisplayName="Service Bus Topics" pageTitle="如何使用服務匯流排主題 (Java) - Azure" metaKeywords="開始使用 Azure 服務匯流排主題, 開始使用服務匯流排主題, Azure 發行訂閱訊息, Azure 訊息主題和訂閱, 服務匯流排主題 Java" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Java applications." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Topics/Subscriptions" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# 如何使用服務匯流排主題/訂閱

本指南將說明如何使用服務匯流排主題和
訂閱。相關範例是以 Java 撰寫並使用 [Azure SDK for Java][]。涵蓋的案例包括**建立主題
和訂閱**、**立訂閱篩選條件**、**將
訊息傳送至主題**、**從訂閱接收訊息**，以及
**刪除主題和訂閱**。

## 目錄

-   [什麼是服務匯流排主題和訂閱？][]
-   [建立服務命名空間][]
-   [取得命名空間的預設管理認證][]
-   [設定應用程式使用服務匯流排][]
-   [作法：建立主題][]
-   [作法：建立訂閱][]
-   [作法：傳送訊息至主題][]
-   [作法：自訂閱接收訊息][]
-   [作法：處理應用程式當機與無法讀取的訊息][]
-   [作法：刪除主題和訂閱][]
-   [後續步驟][]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a name="bkmk_ConfigYourApp"> </a>設定應用程式使用服務匯流排

在 Java 檔案頂端新增下列 import 陳述式：

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

將 Azure Libraries for Java 新增至您的建置路徑，並將其納入專案部署組件中。

## <a name="bkmk_HowToCreateTopic"> </a>如何建立主題

服務匯流排主題的管理作業可透過
**ServiceBusContract** 類別來執行。**ServiceBusContract** 物件是
可使用含有權杖權限加以管理的
適當組態來建構，而對於 Azure，**ServiceBusContract** 類別是
唯一的通訊點。

**ServiceBusService** 類別提供建立、列舉及刪除主題
的方法。下列範例示範如何使用 **ServiceBusService** 物件
建立名稱為 "TestTopic" 的佇列及名稱為 "HowToSample" 的命名空間：

    Configuration config = 
    	ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

	ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
	try  
	{
    	CreateTopicResult result = service.createTopic(topicInfo);
	}
	catch (ServiceException e) {
		System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
		System.exit(-1);
	}

有 **TopicInfo** 的方法可允許調整佇列的
屬性 (例如：針對要在傳送至主題的訊息套用的
[存留時間] 設定預設值)。下列範例將示範如何
使用大小上限為 5 GB 的設定來建立名為 "TestTopic" 的主題：

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

請注意，您可以將 **listTopics** 方法使用於
**ServiceBusContract** 物件，檢查服務命名空間內是否已有指定名稱
的主題存在。

## <a name="bkmk_HowToCreateSubscrip"> </a>如何建立訂閱

**ServiceBusService** 類別也能用來建立主題
訂閱。訂閱是具名的，它們能擁有選用的篩選器，
以用來限制傳遞至訂閱的虛擬佇列的訊息
集合。

### 使用預設 (MatchAll) 篩選器建立訂閱

**MatchAll** 篩選器是預設篩選器，如果在建立新訂閱時
沒有指定篩選器，便會使用此篩選器。使用 **MatchAll**
篩選器時，所有發佈至主題的訊息都會被置於訂閱的
虛擬佇列中。下列範例會建立
名為 "AllMessages" 的訂閱，並使用預設的 **MatchAll**
篩選器。

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### 使用篩選器建立訂閱

您也可以設定篩選器，讓您界定哪些傳送至主題的訊息
應出現在特定主題訂閱中。

訂閱所支援的最具彈性篩選器類型是實作 SQL92 子集的
**SqlFilter**。SQL 篩選器適用於
發佈至主題之訊息的屬性。如需
可與 SQL 篩選器搭配使用的運算式詳細資料，
請檢閱 SqlFilter.SqlExpression 語法。

以下範例將建立名為 "HighMessages" 並帶有
**SqlFilter** 的訂閱，該篩選器只選取自訂
**MessageNumber** 屬性大於 3 的訊息：

    // Create a "HighMessages" filtered subscription  
	SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
    CreateSubscriptionResult result = 
		service.createSubscription("TestTopic", subInfo);
	RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
	CreateRuleResult ruleResult = 
		service.createRule("TestTopic", "HighMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "HighMessages", "$Default");

同樣地，以下範例將建立名為
"LowMessages" 且其   
 SqlFilter 只選取 MessageNumber
屬性小於   
 或等於 3 的訊息：

    // Create a "LowMessages" filtered subscription
	SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
	CreateSubscriptionResult result = 
		service.createSubscription("TestTopic", subInfo);
    RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
	CreateRuleResult ruleResult = 
		service.createRule("TestTopic", "LowMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "LowMessages", "$Default");


現在，當訊息傳送智 "TestTopic" 時，一律會
將該訊息傳遞至已訂閱 "AllMessages" 主題訂閱的
的接收者，並選擇性地傳遞至已訂閱
"HighMessages" 和 "LowMessages" 主題訂閱的接收者 (視
訊息內容而定)。

## <a name="bkmk_HowToSendMsgs"> </a>如何傳送訊息至主題

若要傳送訊息至服務匯流排主題，應用程式會取得
**ServiceBusContract** 物件。下列程式碼示範如何將訊息
傳送至我們先前在 "HowToSample" 服務命名空間內
建立的 "TestTopic" 主題：

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

傳送至服務匯流排主題的訊息是
**BrokeredMessage** 類別的執行個體。**BrokeredMessage** 物件具有一組
標準方法 (例如 **setLabel** 和 **TimeToLive**)、一個用來
保存自訂應用程式特定屬性的字典，以及一些任意的
應用程式資料。應用程式可以設定訊息內文，
方法是將任何可序列化的物件傳遞至
**BrokeredMessage** 的建構函式，而適當的 **DataContractSerializer** 則會
用來序列化物件。也可以提供
**java.io.InputStream**。

下列範例說明如何將五個測試訊息傳送至上述程式碼片段中所取得的
"TestTopic" **MessageSender**：
請注意迴圈反覆運算上每個訊息的 **MessageNumber** 屬性值
的變化 (這可判斷接收訊息的訂閱
為何)：

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

服務匯流排主題支援 256 Kb 的訊息大小上限 (包含
標準和自訂應用程式屬性的標頭可以容納
64 Kb 的大小上限)。主題中的訊息數目沒有限制，
但是主題所保有的訊息總大小有
上限。此主題大小會在建立時定義，
上限是 5 GB。

## <a name="bkmk_HowToReceiveMsgs"> </a>如何自訂閱接收訊息

從訂閱接收訊息的主要方式是使用
**ServiceBusContract** 物件。接收的訊息可在兩種不同的
模式下運作：**ReceiveAndDelete** 和 **PeekLock**。

使用 **ReceiveAndDelete** 模式食，接收是一次性
作業；也就是說，當服務匯流排收到訊息的讀取要求時，
它會將此訊息標示為已使用，並將它傳回
應用程式。**ReceiveAndDelete** 模式是最簡單的模型，
最適合可容許在發生失敗時不處理訊息的
應用程式案例。若要了解這點，請考慮
以下案例：取用者發出接收要求，然而
在處理之前便當機。因為服務匯流排會將
訊息標示為已取用，當應用程式重新啟動並開始
重新取用訊息時，它將會遺漏當機前
已取用的訊息。

在 **PeekLock** 模式中，接收會變成兩階段作業，因此
可以支援無法容許遺漏訊息的應用
程式。當服務匯流排收到要求時，它會尋找下一個要
要取用的訊息並加以鎖定，以免其他取用者接收該訊息，然後
將它傳回應用程式。在應用程式完成
訊息處理 (或可靠地儲存此訊息以供未來處理) 之後，它可在接收的訊息上
呼叫 **Delete**
方法，以完成接收程序的第二個階段。當服務匯流排發現 **Delete** 呼叫時，
它會將訊息標示為已取用，並將它從主題中移除。

下列範例將示範如何
使用 **PeekLock** 模式 (這不是預設模式) 來接收與處理訊息。下列範例
將執行迴圈，並處理 "HighMessages" 訂閱中的訊息，然後在沒有任何訊息時結束 (您也可以將其設為等待新訊息)。

	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);
	
		while(true)  { 
		    ReceiveSubscriptionMessageResult  resultSubMsg = 
		        service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
		    BrokeredMessage message = resultSubMsg.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());    
			    // Display the topic message.
			    System.out.print("From topic: ");
			    byte[] b = new byte[200];
			    String s = null;
			    int numRead = message.getBody().read(b);
			    while (-1 != numRead)
	            {
	                s = new String(b);
	                s = s.trim();
	                System.out.print(s);
	                numRead = message.getBody().read(b);
			    }
	            System.out.println();
			    System.out.println("Custom Property: " + 
			        message.getProperty("MessageNumber"));
			    // Delete message.
			    System.out.println("Deleting this message.");
			    service.deleteMessage(message);
		    }  
		    else  
		    {        
		        System.out.println("Finishing up - no more messages.");        
		        break; 
		        // Added to handle no more messages.
		        // Could instead wait for more messages to be added.
		    }
	    }
	}
	catch (ServiceException e) {
	    System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	}
	catch (Exception e) {
	    System.out.print("Generic exception encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	} 

## <a name="bkmk_HowToHandleAppCrash"> </a>如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，協助您從應用程式的
錯誤或處理訊息的問題中順利復原。如果
接收者應用程式因故無法處理訊息，
它可以在接收的訊息上呼叫 **unlockMessage** 方法
(而非 **deleteMessage** 方法)。這將導致服務匯流排
將主題中的訊息解除鎖定，讓此訊息可以被
相同取用應用程式或其他取用應用程式
重新接收。

與主題內鎖定訊息相關的還有逾時，
如果應用程式無法在鎖定逾時到期之前
處理訊息 (例如，如果應用程式當機)，則服務
匯流排會自動解除鎖定訊息，並讓訊息可以被
重新接收。

如果應用程式在處理訊息之後，
尚未發出 **deleteMessage** 要求時當機，則會在應用程式
重新啟動時將訊息重新傳遞給該應用程式。這通常
稱為**至少處理一次**，也就是說，每個訊息
至少會被處理一次，但在特定狀況下，可能會重新傳遞相同
訊息。如果案例無法容許重複處理，
則應用程式開發人員應將其他邏輯加入至其
應用程式來處理重複的訊息傳遞。通常您可使用
訊息的 **getMessageId** 方法來達到此目的，該方法
將在各個傳遞嘗試中保持不變。

## <a name="bkmk_HowToDeleteTopics"> </a>如何刪除主題和訂閱

刪除主題和訂閱的主要方式是使用
**ServiceBusContract** 物件。刪除主題也將會刪除對主題註冊的
任何訂閱。您也可以個別刪除訂閱。

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

# <a name="bkmk_NextSteps"> </a>後續步驟

現在，您已了解服務匯流排佇列的基本概念，請參閱 MSDN
主題[服務匯流佇列、主題和訂閱][]，以獲得詳細資訊。

  [Azure SDK for Java]: http://www.windowsazure.com/zh-tw/develop/java/
  [什麼是服務匯流排主題和訂閱？]: #what-are-service-bus-topics
  [建立服務命名空間]: #create-a-service-namespace
  [取得命名空間的預設管理認證]: #obtain-default-credentials
  [設定應用程式使用服務匯流排]: #bkmk_ConfigYourApp
  [作法：建立主題]: #bkmk_HowToCreateTopic
  [作法：建立訂閱]: #bkmk_HowToCreateSubscrip
  [作法：傳送訊息至主題]: #bkmk_HowToSendMsgs
  [作法：自訂閱接收訊息]: #bkmk_HowToReceiveMsgs
  [作法：處理應用程式當機與無法讀取的訊息]: #bkmk_HowToHandleAppCrash
  [作法：刪除主題和訂閱]: #bkmk_HowToDeleteTopics
  [後續步驟]: #bkmk_NextSteps
  [服務匯流排主題圖表]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Azure 管理入口網站]: http://manage.windowsazure.com/
  [Service Bus Node screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [建立新命名空間 ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [Namespace List screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [Properties Pane screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [Default Key screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [服務匯流排佇列、主題和訂閱]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
