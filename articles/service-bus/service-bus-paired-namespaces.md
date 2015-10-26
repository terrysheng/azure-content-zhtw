<properties 
   pageTitle="服務匯流排配對的命名空間 |Microsoft Azure"
   description="配對的命名空間實作詳細資料和成本"
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

# 配對命名空間實作詳細資料和成本影響

使用 [SendAvailabilityPairedNamespaceOptions][] 執行個體的 [PairNamespaceAsync][] 方法會代表您執行可見的工作。因為使用此功能時有一些成本考量，了解這些工作會有所幫助，以便預期發生時的行為。API 會代表您下列從事下列自動行為：

-   建立待處理項目佇列。
-   建立可與佇列或主題交談的 [MessageSender][] 物件。
-   當傳訊實體變成無法使用時，在嘗試偵測該實體何時可再度使用時傳送 ping 訊息到實體。
-   選擇性建立一組可將訊息從待處理項目佇列移到主要佇列的「訊息幫浦」。
-   協調主要和次要 [MessagingFactory][] 執行個體的關閉/失敗。

概括而言，此功能的運作方式如下：當主要實體的狀況良好時，不會發生任何行為變更。當 [FailoverInterval][] 期間過後，而且主要實體未看到非暫時性 [MessagingException][] 或 [TimeoutException][] 之後有任何成功傳送，則會發生下列行為：

1.  傳送至主要實體的作業會停用，而系統會 ping 主要實體，直到可以成功傳送 ping 為止。

2.  已選取隨機待處理項目佇列。

3.  [BrokeredMessage][] 物件會路由傳送至所選的待處理項目佇列。

1.  如果傳送至所選待處理項目佇列的作業失敗，則會從輪替提取該佇列並選取新的佇列。[MessagingFactory][] 執行個體上的所有傳送端都會得知失敗。

下圖描繪順序。首先，傳送端會傳送訊息。

![配對的命名空間][0]

無法傳送到主要佇列時，傳送端會開始傳送訊息到隨機選擇的待處理項目佇列。同時，它會開始進行 ping 工作。

![配對的命名空間][1]

此時訊息仍在次要佇列中，而尚未傳遞到主要佇列。一旦主要佇列再度恢復良好狀況，至少有一個程序應該執行 Syphon。Syphon 會將各種待處理項目佇列中的訊息傳遞至適當的目的地實體 (佇列和主題)。

![配對的命名空間][2]

本主題的其餘部分將討論這些片段運作方式的特定詳細資料。

## 建立待處理項目佇列

傳遞至 [PairNamespaceAsync][] 方法的 [SendAvailabilityPairedNamespaceOptions][] 物件會指出您要使用的待處理項目佇列的數目。然後會建立已明確設定下列屬性的每個待處理項目佇列 (所有其他值都會設為 [QueueDescription][] 預設值)︰

| Path | [primary namespace]/x-servicebus-transfer/[index]，其中 [index] 是 [0, BacklogQueueCount) 中的值 |
|----------------------------------------|------------------------------------------------------------------------------------------------------|
| MaxSizeInMegabytes | 5120 |
| MaxDeliveryCount | int.MaxValue |
| DefaultMessageTimeToLive | TimeSpan.MaxValue |
| AutoDeleteOnIdle | TimeSpan.MaxValue |
| LockDuration | 1 分鐘 |
| EnableDeadLetteringOnMessageExpiration | true |
| EnableBatchedOperations | true |

例如，針對命名空間 **contoso** 建立的第一個待處理項目佇列名為 `contoso/x-servicebus-transfer/0`。

建立佇列時，程式碼會先查看是否有此佇列存在。如果此佇列不存在，則會建立佇列。程式碼不會清除「額外的」待處理項目佇列。具體而言，如果具有主要命名空間 **contoso** 的應用程式要求 5 個待處理項目佇列，但有一個具有路徑 `contoso/x-servicebus-transfer/7` 的待處理項目佇列存在，則該額外的待處理項目佇列仍然存在，但不會使用。系統明確允許額外的待處理項目佇列存在但不會使用。身為命名空間擁有者，您必須負責清除任何未使用/不需要的待處理項目佇列。此決策的原因是服務匯流排無法得知您的命名空間中所有佇列的目的為何。此外，如果佇列具有指定的名稱但不符合所假設的 [QueueDescription][]，則您的理由是您自己要變更預設行為。不保證您的程式碼會修改待處理項目佇列。請務必徹底測試您的變更。

## 自訂 MessageSender

傳送時，所有訊息都會經過內部 [MessageSender][] 物件，該物件在一切可行時會正常表現，而在出現問題會重新導向至待處理項目佇列。 收到非暫時性失敗時，會啟動計時器。在由 [FailoverInterval][] 屬性值組成的 [TimeSpan][] 期間 (其間並未傳送任何成功的訊息) 之後，會進行容錯移轉。此時，每個實體會發生下列情況︰

- 每隔 [PingPrimaryInterval][] 執行一次 Ping 工作來檢查實體是否可用。此工作一旦成功，使用該實體的所有用戶端程式碼會立即開始將新訊息傳送到主要命名空間。

- 未來要從任何其他傳送端傳送到該相同實體的要求，將會導致所傳送的 [BrokeredMessage][] 遭到修改以放入待處理項目佇列中。修改作業會從 [BrokeredMessage][] 物件移除某些屬性並將其儲存在其他地方。下列屬性已被清除並新增於新別名之下，可讓服務匯流排和 SDK 以一致的方式處理訊息︰

| 舊屬性名稱 | 新屬性名稱 |
|-------------------------|-------------------|
| SessionId | x-ms-sessionid |
| TimeToLive | x-ms-timetolive |
| ScheduledEnqueueTimeUtc | x-ms-path |

原始目的地路徑也會以名為 x-ms-path 的屬性形式儲存在訊息內。這種設計可讓多個實體的訊息共存於單一待處理項目佇列中。這些屬性會由 Syphon 轉譯回來。

自訂 [MessageSender][] 物件可能會在訊息接近 256 KB 限制並進行容錯移轉時遇到問題。自訂 [MessageSender][] 物件會將所有佇列和主題的訊息一起存放在待處理項目佇列中。此物件會將待處理項目佇列中許多主要項目的訊息混合在一起。為了處理許多不知道彼此存在的用戶端之間的負載平衡，SDK 會為您在程式碼中建立的每個 [QueueClient][] 或 [TopicClient][] 隨機挑選一個待處理項目佇列。

## Ping

Ping 訊息是空的 [BrokeredMessage][]，其 [ContentType][] 屬性設定為 application/vnd.ms-servicebus-ping 且 [TimeToLive][] 值為 1 秒。此 ping 在服務匯流排中有一個特性︰當任何呼叫端要求 [BrokeredMessage][] 時，伺服器決不會傳遞 ping。因此，您永遠都不必了解如何接收並忽略這些訊息。將每個用戶端的每個 [MessagingFactory][] 執行個體的每個實體 (唯一佇列或主題) 視為無法使用時，將會進行 ping。根據預設，這會每分鐘發生一次。Ping 訊息會被視為一般的服務匯流排訊息，可能會導致頻寬和訊息的費用。只要用戶端偵測到系統可以使用，訊息就會停止。

## Syphon

應用程式中至少有一個可執行程式應該主動執行 Syphon。Syphon 會執行持續 15 分鐘的長時間輪詢接收。當所有實體都可使用且有 10 個待處理項目佇列時，裝載 Syphon 的應用程式會呼叫接收作業︰每小時 40 次、每天 960 次和 30 天內 28800 次。當 Syphon 主動將訊息從待處理項目移到主要佇列時，每則訊息會發生下列收費 (訊息大小和頻寬的標準收費適用於所有階段)︰

1.  傳送至待處理項目佇列。

2.  從待處理項目佇列接收。

3.  傳送至主要佇列。

4.  從主要佇列接收。

## 關閉/錯誤行為

在裝載 Syphon 的應用程式內，一旦主要或次要 [MessagingFactory][] 發生錯誤或已關閉，但其夥伴並未同時發生錯誤/關閉且 Syphon 偵測到此狀態，Syphon 就會採取行動。如果其他 [MessagingFactory][] 未在 5 秒內關閉，則虹吸會發生錯誤但仍開啟 [MessagingFactory][]。

## 後續步驟

如需服務匯流排非同步傳訊的詳細討論，請參閱[非同步傳訊模式和高可用性]。

  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [FailoverInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.failoverinterval.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [0]: ./media/service-bus-paired-namespaces/IC673405.png
  [1]: ./media/service-bus-paired-namespaces/IC673406.png
  [2]: ./media/service-bus-paired-namespaces/IC673407.png
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
  [PingPrimaryInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.pingprimaryinterval.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [ContentType]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx
  [TimeToLive]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
  [非同步傳訊模式和高可用性]: service-bus-async-messaging.md

<!---HONumber=Oct15_HO3-->