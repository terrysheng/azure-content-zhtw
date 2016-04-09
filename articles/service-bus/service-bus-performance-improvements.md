<properties 
    pageTitle="使用服務匯流排提升效能的最佳做法 |Microsoft Azure"
    description="描述如何使用 Azure 服務匯流排來在交換代理的訊息時將效能最佳化。"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="03/16/2016"
    ms.author="sethm" />

# 使用服務匯流排代理傳訊的效能改進最佳作法

本主題描述如何使用 Azure 服務匯流排來在交換代理的訊息時將效能最佳化。本主題的第一個部分說明提供協助提高效能的不同機制。第二個部分針對如何在特定案例中利用可提供最佳效能的方式來使用服務匯流排提供指引。

在本主題中，「用戶端」一詞是指任何存取服務匯流排的實體。用戶端可以擔任傳送者或接收者的角色。「傳送者」一詞用於傳送訊息至服務匯流排佇列或主題的服務匯流排佇列或主題用戶端。「接收者」一詞指的是從服務匯流排佇列或訂用帳戶接收訊息的服務匯流排佇列或訂用帳戶用戶端。

這些章節介紹幾個服務匯流排用來協助提升效能的概念。

## 通訊協定

服務匯流排可讓用戶端透過兩種通訊協定傳送和接收訊息：服務匯流排用戶端通訊協定和 HTTP (REST)。服務匯流排用戶端通訊協定較有效率，因為只要傳訊處理站存在，它就會維護服務匯流排服務的連接。它也會實作批次處理和預先擷取作業。服務匯流排用戶端通訊協定適用於使用 .NET API 的 .NET 應用程式。

除非明確提到，否則本主題中的所有內容都假設為使用服務匯流排用戶端通訊協定。

## 重複使用處理站和用戶端

服務匯流排用戶端物件，例如 [QueueClient][] 或 [MessageSender][]，會透過也提供內部連接管理的 [MessagingFactory][] 物件來建立。當您傳送一個訊息，並在傳送下一個訊息時重新建立傳訊處理站或佇列、主題及訂用帳戶用戶端之後，您不能將其關閉。關閉傳訊處理站會刪除服務匯流排服務的連接，並在重新建立處理站時建立新的連接。建立連接是成本高昂的作業，您可以藉由重新使用多項作業的相同處理站和用戶端物件來避免此作業。您可以安全地使用 [QueueClient][] 物件，從並行非同步作業和多個執行緒傳送訊息。

## 並行作業

執行作業 (傳送、接收、刪除等等) 需要一點時間。這個時間包括服務匯流排服務處理作業的時間加上要求和回覆的延遲時間。若要增加每次的作業數目，就必須並行執行作業。您可以利用數個不同方式執行這項操作：

-   **非同步作業**：用戶端會透過執行非同步作業來排程作業。下一個要求會在前一個要求完成之前啟動。非同步傳送作業的範例如下：

	```
	BrokeredMessage m1 = new BrokeredMessage(body);
	BrokeredMessage m2 = new BrokeredMessage(body);
	
	Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
	  {
	    Console.WriteLine("Sent message #1");
	  });
	Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
	  {
	    Console.WriteLine("Sent message #2");
	  });
	Task.WaitAll(send1, send2);
	Console.WriteLine("All messages sent");
	```

	非同步接收作業的範例如下：
	
	```
	Task receive1 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
	Task receive2 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
	
	Task.WaitAll(receive1, receive2);
	Console.WriteLine("All messages received");
	
	async void ProcessReceivedMessage(Task<BrokeredMessage> t)
	{
	  BrokeredMessage m = t.Result;
	  Console.WriteLine("{0} received", m.Label);
	  await m.CompleteAsync();
	  Console.WriteLine("{0} complete", m.Label);
	}
	```

-   **多個處理站**：由相同處理站建立的所有用戶端 (除了接收者之外還有傳送者) 共用一個 TCP 連線。最大訊息輸送量受限於可通過此 TCP 連線的作業數目。透過單一處理站取得的輸送量與 TCP 來回時間和訊息大小有顯著的差異。若要取得更高的輸送量速率，您應該使用多個傳訊處理站。

## 接收模式

建立佇列或訂用帳戶用戶端時，您可以指定接收模式：*peek-lock* 或*接收與刪除*。預設接收模式是 [PeekLock][]。以此模式操作時，用戶端會傳送要求，以接收來自服務匯流排的訊息。用戶端收到訊息後，它會傳送要求以完成訊息。

將接收模式設為 [ReceiveAndDelete][] 時，兩個步驟會在單一要求中結合。這可減少整體的作業數目並可改善整體訊息輸送量。此效能改善的風險為遺失訊息。

服務匯流排不支援接收和刪除作業的交易。此外，在用戶端想要延遲或讓訊息寄不出去的任何案例中都需要鎖定語意。

## 用戶端批次處理

用戶端批次處理可讓佇列或主題用戶端將訊息的傳送延遲一段時間。如果用戶端在此期間傳送其他訊息，它將以單一批次傳輸訊息。用戶端批次處理也會導致佇列/訂用帳戶用戶端將多個**完成**要求批次處理為單一要求。批次處理僅適用於非同步**傳送**及**完成**作業。同步作業會立即傳送至服務匯流排服務。查看或接收作業不會進行批次處理，也不會跨用戶端進行。

如果批次超過訊息大小上限，最後一則訊息會從批次移除，而且用戶端會立即傳送批次。最後一則訊息會成為下一個批次的第一則訊息。根據預設，用戶端使用的批次間隔為 20 毫秒。您可以藉由在建立傳訊處理站之前設定 [BatchFlushInterval][] 屬性來變更批次間隔。這個設定會影響此處理站所建立的所有用戶端。

若要停用批次處理，請將 [BatchFlushInterval][] 屬性設為 **TimeSpan.Zero**。例如：

```
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

批次處理並不會影響可計費的傳訊作業數目，而且僅適用於服務匯流排用戶端通訊協定。HTTP 通訊協定不支援批次處理。

## 批次處理存放區存取

為了提高佇列/主題/訂用帳戶的輸送量，服務匯流排會在寫入至其內部存放區時批次處理多個訊息。如果在佇列或主題上啟用，將會批次處理寫入訊息至存放區。如果在佇列或訂用帳戶上啟用，將會批次處理從存放區刪除訊息。如果某個實體啟用批次處理的存放區存取，服務匯流排會延遲與該實體有關的存放區寫入作業，最多 20 毫秒。在此間隔期間進行的其他存放區作業都會加入至批次。批次處理的存放區存取只會影響**傳送**和**完成**作業；接收作業不會受到影響。批次處理的存放區存取是實體上的屬性。批次處理會在啟用批次處理存放區存取的所有實體進行。

建立新佇列、主題或訂用帳戶時，預設會啟用批次處理的存放區存取。若要停用批次處理的存放區存取，請在建立實體之前將 [EnableBatchedOperations][] 屬性設為 **false**。例如：

```
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

批次處理的存放區存取並不會影響可計費的傳續作業數目，並且是佇列、主題或訂用帳戶的屬性。它獨立於接收模式及用於用戶端和服務匯流排服務之間的通訊協定之外。

## 預先擷取

預先擷取可讓佇列或訂用帳戶用戶端在執行接收作業時從服務載入額外的訊息。用戶端會將這些訊息儲存在本機快取中。快取的大小取決於 [QueueClient.PrefetchCount][] 和 [SubscriptionClient.PrefetchCount][] 屬性。啟用預先擷取的每個用戶端會維護自己的快取。快取不會跨用戶端共用。如果用戶端起始接收作業且其快取是空的，則服務會傳輸一批次的訊息。批次的大小等於快取的大小或 256 KB，以較小者為準。如果用戶端起始接收作業且快取包含一則訊息，訊息會從快取中擷取。

預先擷取訊息時，服務會鎖定預先擷取的訊息。藉由這麼做，其他接收者就無法接收預先擷取的訊息。如果接收者無法在鎖定到期之前完成訊息，訊息就會變成可供其他接收者接收。訊息的預先擷取副本會保留在快取中。當取用過其快取複本的接收者嘗試完成該訊息時，他會收到例外狀況。根據預設，訊息鎖定會在 60 秒之後到期。此值可延長為 5 分鐘。若要避免過期訊息遭到取用，快取大小應該一律小於用戶端在鎖定逾時間隔內可取用的訊息數目。

使用 60 秒的預設鎖定到期時間時，[SubscriptionClient.PrefetchCount][] 的良好值為處理站中所有接收者最高處理速率的 20 倍。例如，處理站建立 3 個接收者，而每個接收者每秒可以處理最多 10 則訊息。預先擷取計數不應該超過 20*3*10 = 600。根據預設，[QueueClient.PrefetchCount][] 設為 0，表示沒有其他訊息從服務擷取。

預先擷取訊息會增加佇列或訂用帳戶的整體輸送量，因為此舉可減少訊息作業或來回時間的整體數目。然而，擷取第一個訊息需要較長的時間 (因為訊息大小增加)。接收預先擷取的訊息比較快速，因為用戶端已經下載這些訊息。

伺服器會在它將訊息傳送至用戶端時，檢查訊息的存留時間 (TTL) 屬性。用戶端不會在收到訊息時檢查訊息的 TTL 屬性。相反地，即使訊息由用戶端快取時已超過其 TTL，仍然可以收到訊息。

預先擷取並不會影響可計費的傳訊作業數目，而且僅適用於服務匯流排用戶端通訊協定。HTTP 通訊協定不支援預先擷取。同步和非同步接收作業皆可使用預先擷取。

## 快速佇列和主題

快速實體會啟用高輸送量並減少延遲案例。使用快速實體時，如果訊息傳送至佇列或主題，訊息不會立即儲存在訊息存放區。相反地，它會快取在記憶體中。如果訊息保留在佇列中超過幾秒鐘，它會自動寫入至穩定儲存體，藉此保護它免於因中斷而遺失。將訊息寫入記憶體快取會增加輸送量並減少延遲，因為訊息傳送時沒有存取穩定儲存體。在幾秒鐘內取用的訊息不會寫入至訊息存放區。下列範例會建立快速主題。

```
TopicDescription td = new TopicDescription(TopicName);
td.EnableExpress = true;
namespaceManager.CreateTopic(td);
```

如果將包含不能遺失之重要資訊的訊息傳送至快速實體，傳送者可以強制服務匯流排立即藉由將 [ForcePersistence][] 屬性設為 **true** 來將訊息儲存到穩定儲存體。

## 使用分割的佇列或主題

服務匯流排會在內部使用相同的節點和訊息存放區來處理和儲存訊息實體 (佇列或主題) 的所有訊息。另一方面，分割的佇列或主題會在多個節點和訊息存放區中散佈。分割的佇列和主題不僅會產生比一般佇列和主題更高的輸送量，也會顯示較優異的可用性。若要建立分割的實體，請將 [EnablePartitioning][] 屬性設為 **true**，如下列範例所示。如需分割實體的詳細資訊，請參閱[分割的傳訊實體][]。

```
// Create partitioned queue.
QueueDescription qd = new QueueDescription(QueueName);
qd.EnablePartitioning = true;
namespaceManager.CreateQueue(qd);
```

## 使用多個佇列

如果您不能使用分割的佇列或主題，或無法由單一分割佇列或主題處理預期的負載，您必須使用多個訊息實體。使用多個實體時，請針對每個實體建立專屬用戶端，而不是讓所有實體使用相同的用戶端。

## 案例

下列各節描述典型傳訊的案例，並簡述慣用的服務匯流排設定。輸送量速率會分類為小型 (小於 1 則訊息/秒)、中型 (1 則訊息/秒或更多但小於 100 則訊息/秒) 和高型 (100 則訊息/秒或更多)。用戶端數目可分類為小型 (5 個或更少)、中型 (5 個以上但小於或等於 20 個) 和大型 (超過 20 個)。

### 高輸送量佇列

目標：最大化單一佇列的輸送量。傳送者和接收者的數目很少。

-   使用分割的佇列以改善效能和可用性。

-   若要增加傳送到佇列的整體速率，請使用多個訊息處理站來建立傳送者。對每個傳送者使用非同步作業或多個執行緒。

-   若要增加從佇列接收的整體速率，請使用多個訊息處理站來建立接收者。

-   使用非同步作業來利用用戶端批次處理。

-   將批次處理間隔設為 50 毫秒，以減少服務匯流排用戶端通訊協定傳輸數目。如果使用多個傳送者，批次處理間隔會增加到 100 毫秒。

-   讓批次處理的存放區存取保持啟用。這會增加訊息寫入至佇列的整體速率。

-   將預先擷取計數設為 20 乘以處理站所有接收者的最高處理速率。這會減少服務匯流排用戶端通訊協定傳輸數目。

### 多個高輸送量佇列

目標：最大化多個佇列的整體輸送量。個別佇列的輸送量為中或高。

若要取得跨多個佇列的最大輸送量，請使用簡述的設定以最大化單一佇列的輸送量。此外，使用不同的處理站來建立傳送至或接收自不同佇列的用戶端。

### 低延遲性佇列

目標：最小化佇列或主題的端對端延遲。傳送者和接收者的數目很少。佇列的輸送量為小或中。

-   使用分割的佇列以改善可用性。

-   停用用戶端批次處理。用戶端會立即傳送訊息。

-   停用批次處理的存放區存取。服務會立即將訊息寫入至存放區中。

-   如果使用單一用戶端，請將預先擷取計數設為 20 乘以接收者的處理速率。如果多個訊息同時抵達佇列，服務匯流排用戶端通訊協定會同時將它們全部傳輸。當用戶端收到下一個訊息時，該訊息已經在本機快取中。快取應該很小。

-   如果使用多個用戶端，請將預先擷取計數設為 0。如此一來，第二個用戶端可以在第一個用戶端仍在處理第一個訊息時接收第二個訊息。

### 具有大量傳送者的佇列

目標：最大化具有大量傳送者之佇列或主題的輸送量。每個傳送者都會以中等速率傳送訊息。接收者的數目很少。

服務匯流排可啟用多達 1000 個並行連線至訊息實體 (或使用 AMQP 可達 5000 個)。在命名空間層級強制執行這項限制，且佇列/主題/訂用帳戶的上限為每個命名空間的並行連線限制。對佇列而言，這個數目是在傳送者和接收者之間共用的。如果 1000 個連線全都為傳送者所需，您必須以主題和單一訂用帳戶取代此佇列。主題會接受多達 1000 個來自傳送者的並行連線，而訂用帳戶可接受來自接收者的額外 1000 個並行連線。如果需要超過 1000 個並行傳送者，傳送者必須透過 HTTP 將訊息傳送至服務匯流排通訊協定。

若要最大化輸送量，請執行下列作業：

-   使用分割的佇列以改善效能和可用性。

-   如果每個傳送者都位於不同的處理序中，每個處理序僅使用單一處理站。

-   使用非同步作業來利用用戶端批次處理。

-   使用預設的批次處理間隔 20 毫秒來減少服務匯流排用戶端通訊協定傳輸數目。

-   讓批次處理的存放區存取保持啟用。這會增加訊息寫入至佇列或主題的整體速率。

-   將預先擷取計數設為 20 乘以處理站所有接收者的最高處理速率。這會減少服務匯流排用戶端通訊協定傳輸數目。

### 具有大量接收者的佇列

目標：最大化具有大量接收者之佇列或訂用帳戶的接收速率。每個接收者皆以中等速率接收訊息。傳送者的數目很少。

服務匯流排可啟用多達 1000 個並行連線至實體。如果佇列需要超過 1000 個接收者，您必須以主題和多個訂用帳戶取代此佇列。每個訂用帳戶可支援最多 1000 個並行連線。此外，接收者可以透過 HTTP 通訊協定存取佇列。

若要最大化輸送量，請執行下列作業：

-   使用分割的佇列以改善效能和可用性。

-   如果每個接收者都位於不同的處理序中，每個處理序僅使用單一處理站。

-   接收者可以使用同步或非同步作業。若指定個別接收者的中等接收速率，完全要求的用戶端批次處理不會影響接收者輸送量。

-   讓批次處理的存放區存取保持啟用。這會減少實體的整體負載。它也會減少訊息寫入至佇列或主題的整體速率。

-   將預先擷取計數設為較小的值 (例如，PrefetchCount = 10)。這樣可以避免接收者在其他接收者具有大量快取的訊息時閒置。

### 具有少量訂用帳戶的主題

目標：最大化具有少量訂用帳戶之主題的輸送量。許多訂用帳戶收到一則訊息，表示所有訂用帳戶的合併接收速率高於傳送速率。傳送者的數目很少。每個訂用帳戶的接收者數目很小。

若要最大化輸送量，請執行下列作業：

-   使用分割的主題以改善效能和可用性。

-   若要增加傳送到主題的整體速率，請使用多個訊息處理站來建立傳送者。對每個傳送者使用非同步作業或多個執行緒。

-   若要增加從訂用帳戶接收的整體速率，請使用多個訊息處理站來建立接收者。對每個接收者使用非同步作業或多個執行緒。

-   使用非同步作業來利用用戶端批次處理。

-   使用預設的批次處理間隔 20 毫秒來減少服務匯流排用戶端通訊協定傳輸數目。

-   讓批次處理的存放區存取保持啟用。這會增加訊息寫入至主題的整體速率。

-   將預先擷取計數設為 20 乘以處理站所有接收者的最高處理速率。這會減少服務匯流排用戶端通訊協定傳輸數目。

### 具有大量訂用帳戶的主題

目標：最大化具有大量訂用帳戶之主題的輸送量。許多訂用帳戶收到一則訊息，表示所有訂用帳戶的合併接收速率遠高於傳送速率。傳送者的數目很少。每個訂用帳戶的接收者數目很小。

如果所有訊息都路由傳送至所有訂用帳戶，具有大量訂用帳戶的主題通常會公開較低的整體輸送量。這是因為每個訊息都被接收了許多次，而且包含在主題與其所有訂用帳戶中的所有訊息都儲存在相同的存放區中。它會假設每個訂用帳戶的傳送者數目和接收者數目都很少。服務匯流排支援每個主題最多 2,000 個訂用帳戶。

若要最大化輸送量，請執行下列作業：

-   使用分割的主題以改善效能和可用性。

-   使用非同步作業來利用用戶端批次處理。

-   使用預設的批次處理間隔 20 毫秒來減少服務匯流排用戶端通訊協定傳輸數目。

-   讓批次處理的存放區存取保持啟用。這會增加訊息寫入至主題的整體速率。

-   將預先擷取設為 20 乘以預期的接收速率 (以秒為單位)。這會減少服務匯流排用戶端通訊協定傳輸數目。

## 後續步驟

若要深入了解如何最佳化服務匯流排效能，請參閱[分割的傳訊實體][]。

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [PeekLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [ReceiveAndDelete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [BatchFlushInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.netmessagingtransportsettings.batchflushinterval.aspx
  [EnableBatchedOperations]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations.aspx
  [QueueClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.prefetchcount.aspx
  [SubscriptionClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.prefetchcount.aspx
  [ForcePersistence]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.forcepersistence.aspx
  [EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [分割的傳訊實體]: service-bus-partitioning.md
  

<!---HONumber=AcomDC_0323_2016-->