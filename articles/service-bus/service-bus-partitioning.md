<properties 
   pageTitle="分割訊息實體 | Microsoft Azure"
   description="說明如何使用多個訊息代理人分割訊息實體。"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/18/2015"
   ms.author="sethm" />

# 分割訊息實體

Azure 服務匯流排會採用多個訊息代理人來處理訊息，並採用多個訊息存放區來儲存訊息。傳統的佇列或主題由單一訊息代理人處理並儲存在一個訊息存放區中。服務匯流排也可讓佇列或主題分割到多個訊息代理人以及訊息存放區。這表示分割佇列或主題的整體輸送量不會再受到單一訊息代理人或訊息存放區的效能所限制。此外，即使訊息存放區暫時中斷也不會讓分割的佇列或主題無法使用。分割的佇列和主題可以包含所有進階的服務匯流排功能，例如支援交易和工作階段。

如需有關服務匯流排內部的詳細資料，請參閱[服務匯流排架構][]主題。

## 分割的佇列和主題

每個分割的佇列或主題都包含多個片段。每個片段儲存在不同的訊息存放區中，並由不同的訊息代理人處理。當訊息傳送至分割的佇列或主題時，服務匯流排會指派訊息到其中一個片段。選取作業由服務匯流排或傳送者可以指定的分割索引鍵隨機進行。

當用戶端想要從分割的佇列或從分割主題的訂用帳戶接收訊息時，服務匯流排會查詢所有片段的訊息，然後傳回由任何訊息存放區傳回給接收者的第一個訊息。服務匯流排會快取其他訊息，然後在它收到其他接收要求時將其傳回。接收的用戶端並不知道分割。分割佇列或主題的用戶端對向行為 (例如讀取、完成、延遲、無效化、預先擷取) 和一般實體的行為相同。

傳送訊息給分割的佇列或主題，或從該處接收訊息時，不需要額外成本。

## 啟用分割

若要搭配 Microsoft Azure 服務匯流排使用分割的佇列和主題，請使用 Azure SDK 2.2 版或更新版本，或在您的 HTTP 要求中指定 `api-version=2013-10`。

您可以建立 1、2、3、4 或 5 GB 大小的服務匯流排佇列及主題 (預設值為 1 GB)。啟用分割時，服務匯流排會為您指定的每 GB 建立 16 個資料分割。因此，如果您建立 5 GB 大小的佇列，每 GB 有 16 個資料分割，則佇列大小上限會變成 (5 * 16) = 80 GB。您可以在 [Azure 入口網站][]上檢視分割的佇列或主題項目，藉此查看其大小上限。

有多種方式可以建立分割的佇列或主題。當您從應用程式建立佇列或主題時，您可以分別將 [QueueDescription.EnablePartitioning][] 或 [TopicDescription.EnablePartitioning][] 屬性設為 **true** 來啟用佇列或主題的分割。這些屬性必須在建立佇列或主題時設定。您不可以在現有的佇列或主題上變更這些屬性。例如：

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

或者，您可以在 Visual Studio 或 [Azure 入口網站][]中建立分割的佇列或主題。當您在入口網站建立新的佇列或主題時，請在佇列或主題是窗的 [設定] 索引標籤中檢查 [啟用分割] 選項。在 Visual Studio 中，按一下 [新增佇列] 或 [新增主題] 對話方塊中的 [啟用分割] 核取方塊。

## 分割索引鍵的用途

當訊息加入佇列至分割的佇列或主題時，服務匯流排會檢查分割索引鍵是否存在。如果找到，它會根據該索引鍵選取片段。如果找不到分割索引鍵，它會根據內部演算法選取片段。

### 使用分割索引鍵

某些案例，例如工作階段或交易，需要儲存在特定片段的訊息。這些案例都需要使用分割索引鍵。使用相同分割索引鍵的所有訊息都會指派給相同的片段。若片段暫時無法使用，服務匯流排會傳回錯誤。

根據這個案例，會使用不同的訊息屬性做為分割索引鍵：

**SessionId**：若訊息已設定 [BrokeredMessage.SessionId][] 屬性，則服務匯流排會使用這個屬性做為分割索引鍵。如此一來，所有屬於相同工作階段的訊息都會由相同的訊息代理人處理。這樣可讓服務匯流排保證訊息的排序以及工作階段狀態的一致性。

**PartitionKey**：若訊息已設定 [BrokeredMessage.PartitionKey][] 屬性而非 [BrokeredMessage.SessionId][] 屬性，則服務匯流排會使用 [PartitionKey][] 屬性做為分割索引鍵。如果訊息已設定 [SessionId][] 和 [PartitionKey][] 屬性，這兩個屬性必須相同。如果 [PartitionKey][] 屬性設為和 [SessionId][] 屬性不同的值，服務匯流排會傳回 **InvalidOperationException** 例外狀況。如果傳送者傳送非工作階段感知的交易訊息，應使用 [PartitionKey][] 屬性。分割索引鍵可確保在交易內傳送的所有訊息都由相同的訊息代理人處理。

**MessageId**：如果佇列或主題已將 [QueueDescription.RequiresDuplicateDetection][] 屬性設為 **true**，且未設定 [BrokeredMessage.SessionId][] 或 [BrokeredMessage.PartitionKey][] 屬性，則 [BrokeredMessage.MessageId][] 屬性可做為分割索引鍵。(請注意，如果傳送應用程式沒有指派訊息識別碼，Microsoft .NET 和 AMQP 程式庫會自動指派)。 在此情況下，相同訊息的所有複本會由相同的訊息代理人處理。這樣可讓服務匯流排偵測並排除重複的訊息。如果 [QueueDescription.RequiresDuplicateDetection][] 屬性未設為 **true**，服務匯流排不會將 [MessageId][] 屬性視為分割索引鍵。

### 不使用分割索引鍵

沒有分割索引鍵時，服務匯流排會以循環配置的方式將訊息分配到分割佇列或主題的所有片段。如果找不到所選的片段，服務匯流排會將訊息指派至不同的片段。如此一來，儘管訊息存放區暫時無法使用，傳送作業仍會成功。

若要提供服務匯流排足夠的時間以將訊息加入佇列到不同的片段，由傳送訊息之用戶端所指定的 [MessagingFactorySettings.OperationTimeout][] 值必須大於 15 秒。建議將 [OperationTimeout][] 屬性設為預設值 60 秒。

請注意，分割索引鍵會將訊息「釘選」到指定的片段。如果保留此片段的訊息存放區無法使用，服務匯流排會傳回錯誤。沒有分割索引鍵時，服務匯流排可以選擇不同的片段，而作業就會成功。因此，建議您若非必要請勿提供分割索引鍵。

## 進階主題：搭配交易使用分割的實體

傳送做為交易一部分的訊息必須指定資料分割索引鍵。這可以是下列屬性之一：[BrokeredMessage.SessionId][]、[BrokeredMessage.PartitionKey][] 或 [BrokeredMessage.MessageId][]。傳送做為相同交易一部分的所有訊息必須指定相同的分割索引鍵。如果您嘗試在沒有分割索引鍵的交易內傳送一則訊息，服務匯流排會傳回 **InvalidOperationException** 例外狀況。如果您嘗試在具有不同分割索引鍵的相同交易內傳送多則訊息，服務匯流排會傳回 **InvalidOperationException** 例外狀況。例如：

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

如果設定任何做為分割索引鍵的屬性，服務匯流排會將訊息釘選到特定片段。無論是否使用交易，都會發生這個行為。建議您若非必要請勿指定分割索引鍵。

## 搭配工作階段使用分割的實體

若要傳送交易訊息到工作階段感知的主題或佇列，該訊息必須已設定 [BrokeredMessage.SessionId][] 屬性。如果也已指定 [BrokeredMessage.PartitionKey][] 屬性，它必須與 [SessionId][] 屬性相同。如果兩者不同，服務匯流排會傳回 **InvalidOperationException** 例外狀況。

不同於一般 (非分割) 的佇列或主題，無法使用單一交易將多則訊息傳送到不同的工作階段。如果如此嘗試，服務匯流排會傳回 **InvalidOperationException** 例外狀況。例如：

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## 使用分割實體的自動訊息轉送

Azure 服務匯流排支援從分割實體自動轉送訊息、自動轉送訊息至分割實體，或在分割實體之間自動轉送訊息。若要啟用自動訊息轉送，請在來源佇列或訂用帳戶上設定 [QueueDescription.ForwardTo][] 屬性。如果該訊息指定分割索引鍵 ([SessionId][]、[PartitionKey][] 或 [MessageId][])，該分割索引鍵會用於目的地實體。

## 分割實體限制

在目前的實作中，服務匯流排會為分割的佇列和主題設定下列限制：

-   分割的佇列及主題可透過 SBMP 或 HTTP/HTTPS 使用，也可與 AMQP 搭配使用。

-   分割的佇列及主題不支援在單一交易中傳送屬於不同工作階段的訊息。

-   服務匯流排目前每個命名空間允許最多 100 個分割佇列或主題。每個分割佇列或主題的配額計數為每個命名空間 10,000 個實體。

-   Windows Server 1.0 和 1.1 版的服務匯流排不支援分割的佇列和主題。

## 後續步驟

請參閱 AMQP 1.0 對服務匯流排分割佇列和主題的支援的討論 (即將推出！) 以深入了解分割的訊息實體。

  [服務匯流排架構]: service-bus-architecture.md
  [Azure 入口網站]: http://manage.windowsazure.com
  [QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
  [BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-entities-amqp-overview.md

<!---HONumber=Oct15_HO3-->