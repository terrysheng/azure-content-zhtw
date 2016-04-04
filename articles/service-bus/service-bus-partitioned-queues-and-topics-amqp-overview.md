<properties 
	pageTitle="適用於服務匯流排分割的佇列和主題的 AMQP 1.0 支援 | Microsoft Azure" 
	description="了解如何使用進階訊息佇列通訊協定 (AMQP) 1.0 搭配服務匯流排分割的佇列和主題。" 
	services="service-bus" 
	documentationCenter=".net" 
	authors="hillaryc" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/16/2016" 
	ms.author="hillaryc;sethm"/>

# 適用於服務匯流排分割的佇列和主題的 AMQP 1.0 支援 

Azure 服務匯流排現在針對服務匯流排**分割的佇列和主題**，支援進階訊息佇列通訊協定 (**AMQP**) 1.0。

**AMQP** 是一套開放的標準訊息佇列通訊協定，可讓您使用不同的程式設計語言開發跨平台的應用程式。如需服務匯流排中 AMQP 支援的一般資訊，請參閱[服務匯流排中的 AMQP 1.0 支援](service-bus-amqp-overview.md)。

相較於傳統且非分割的佇列和主題，**分割的佇列和主題** (也稱為*分割的實體*) 可提供更高的可用性、可靠性和輸送量。如需分割實體的詳細資訊，請參閱[分割訊息實體](service-bus-partitioning.md)。

新增 AMQP 1.0 做為與分割的佇列和主題通訊的通訊協定，讓您能夠建置可相互執行的應用程式，以利用服務匯流排分割的實體所提供的更高的可靠性、可用性和輸送量。

## 使用 AMQP 搭配分割的佇列

針對需要暫時分離、負載調節、負載平衡和鬆散結合的案例，佇列是非常實用的。使用佇列時，發行者會將訊息傳送到佇列，而取用者會從佇列接收訊息，一次只能接收一則訊息。一個很好的範例是庫存系統，其中的銷售點終端機會將資料發佈至佇列，而不是直接發佈至庫存管理系統。庫存管理系統之後便能隨時取用資料來管理庫存補充。這會有數個優點，包括庫存管理系統不需要隨時上線。如需服務匯流排佇列的詳細資訊，請參閱[建立使用服務匯流排佇列的應用程式](service-bus-create-queues.md)

分割的佇列會進一步增加應用程式的可用性、可靠性和輸送量，因為這些佇列會在多個訊息代理人和訊息存放區之間進行分割。

### 建立分割的佇列

您可以使用 [Azure 傳統入口網站][]和服務匯流排 SDK 建立分割的佇列。要建立分割的佇列，請在 [QueueDescription ](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx)執行個體中將[ EnablePartitioning ](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx)屬性設為 **true**。下列程式碼示範如何使用服務匯流排 SDK 建立分割的佇列。
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### 使用 AMQP 傳送和接收訊息

您可以使用 AMQP 做為通訊協定，透過將 [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) 屬性設定為 [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx)，傳送及接收來自分割的佇列的訊息，如下列程式碼所示。

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## 使用 AMQP 搭配分割的主題

主題在概念上類似於佇列，不過主題可以將相同訊息的複本路由傳送至多個訂閱者。在主題中，發行者會將訊息傳送到主題，而取用者會從「訂用帳戶」接收訊息。在庫存系統的案例中，銷售點終端機會將資料發佈至主題。然後庫存管理系統會接收來自訂用帳戶的訊息。此外，監視系統可以從不同的訂用帳戶接收相同訊息。如需服務匯流排主題和訂用帳戶的詳細資料，請參閱[建立使用服務匯流排主題和訂用帳戶的應用程式](service-bus-create-topics-subscriptions.md)

分割的主題會進一步增加應用程式的可用性、可靠性和輸送量，因為這些主題及其訂用帳戶會在多個訊息代理人和訊息存放區之間進行分割。

### 建立分割的主題

您可以使用 [Azure 傳統入口網站][]和服務匯流排 SDK 建立分割的主題。若要建立分割的主題，請在 [TopicDescription ](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx)執行個體中將 [EnablePartitioning ](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx)屬性設為 **true**。下列程式碼示範如何使用服務匯流排 SDK 建立分割的主題。
	
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### 使用 AMQP 傳送和接收訊息

您可以使用 AMQP 作為通訊協定，透過將 [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) 屬性設定為 [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx)，傳送及接收來自分割的主題訂用帳戶的訊息，如下列程式碼所示。

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
	
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
	
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## 後續步驟

請參閱下列其他資訊來深入了解分割的傳訊實體。

*    [分割訊息實體](service-bus-partitioning.md)
*    [OASIS 進階訊息佇列通訊協定 (AMQP) 版本 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [服務匯流排中的 AMQP 1.0 支援](service-bus-amqp-overview.md)
*    [如何搭配使用 Java 訊息服務 (JMS) API 與服務匯流排和 AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [如何透過服務匯流排 .NET API 使用 AMQP 1.0](service-bus-dotnet-advanced-message-queuing.md)

[Azure 傳統入口網站]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0323_2016-->