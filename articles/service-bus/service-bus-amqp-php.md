<properties 
   pageTitle="服務匯流排和 PHP 與 AMQP 1.0 |Microsoft Azure"
   description="搭配使用 PHP 的服務匯流排與 AMQP"
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
   ms.date="10/07/2015"
   ms.author="sethm" />

# 搭配使用 PHP 的服務匯流排與 AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-PHP 是繫結至 Proton-C 的 PHP 語言；也就是說，Proton-PHP 會當作以 C 實作之引擎的包裝函式實作。

## 下載 Proton 用戶端程式庫

您可以從 [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html) 下載 Proton-C 及其相關聯的繫結 (包括 PHP)。下載採用原始程式碼形式。若要建置程式碼，請遵循下載的套件中所包含的指示。

> [AZURE.IMPORTANT]在撰寫本文時，Proton-C 中的 SSL 支援只適用於 Linux 作業系統。因為 Azure 服務匯流排需要使用 SSL，所以 Proton-C (和語言繫結) 此時僅可用來從 Linux 存取服務匯流排。在 Windows 上啟用 Proton-C 與 SSL 正在進行中，請經常檢查更新。

## 從 PHP 使用服務匯流排佇列、主題和訂用帳戶

下列程式碼示範如何從服務匯流排傳訊實體傳送和接收訊息。

### 使用 Proton-PHP 傳送訊息

下列程式碼示範如何將訊息傳送至服務匯流排傳訊實體。

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### 使用 Proton-PHP 接收訊息

下列程式碼示範如何從服務匯流排傳訊實體接收訊息。

```
$messenger = new Messenger();
$address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## 在 .NET 與 Proton-PHP 之間傳訊

### 應用程式屬性

#### ProtonPHP 至服務匯流排 .NET API

Proton-PHP 訊息支援下列類型的應用程式屬性︰**integer**、**double**、**Boolean**、**string** 和 **object**。下列 PHP 程式碼示範如何使用每一個屬性類型來設定訊息的屬性。

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

在服務匯流排 .NET API 中，訊息應用程式屬性位於 [BrokeredMessage][] 的 [屬性] 集合中。下列程式碼示範如何讀取從 PHP 用戶端接收之訊息的應用程式屬性。

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

下表將 PHP 屬性類型對應至 .NET 屬性類型。

| PHP 屬性類型 | .NET 屬性類型 |
|-------------------|--------------------|
| integer | int |
| double | double |
| 布林值 | 布林 |
| 字串 | 字串 |
| 物件 | Object |

#### 服務匯流排 .NET API 至 PHP

[BrokeredMessage][] 類型支援下列類型的應用程式屬性︰**byte**、**sbyte**、**char**、**short**、**ushort**、**int**、**uint**、**long**、**ulong**、**float**、**double**、**decimal**、**bool**、**Guid**、**string**、**Uri**、**DateTime**、**DateTimeOffset** 和 **TimeSpan**。下列 .NET 程式碼示範如何使用每一個屬性類型來設定 [BrokeredMessage][] 物件的屬性。

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

下列 PHP 程式碼示範如何讀取從服務匯流排 .NET 用戶端接收之訊息的應用程式屬性。

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

下表將 .NET 屬性類型對應至 PHP 屬性類型。

| .NET 屬性類型 | PHP 屬性類型 | 注意事項 |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte | integer | - | | sbyte | integer | - | | char | Char | Proton-PHP 類別 | | short | integer | - | | ushort | integer | - | | int | integer | - | | uint | Integer | - | | long | integer | - | | ulong | integer | - | | float | double | - | | double | double | - | | decimal | string | Proton 目前不支援 decimal。 | | bool | boolean | - | | Guid | UUID | Proton-PHP class | | string | string | - | | DateTime | integer | - | | DateTimeOffset | DescribedType | DateTimeOffset.UtcTicks 會對應至 AMQP 類型︰<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> | | TimeSpan | DescribedType | Timespan.Ticks 會對應至 AMQP 類型︰<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> | | Uri | DescribedType | Uri.AbsoluteUri 會對應至 AMQP 類型︰<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type> |

### 標準屬性

下表顯示 Proton-PHP 標準訊息屬性與 [BrokeredMessage][] 標準訊息屬性之間的對應。

| Proton-PHP | 服務匯流排 .NET | 注意事項 |
|----------------------|--------------------------|----------------------------------------------------------|
| 持久 | n/a | 服務匯流排僅支援持久的訊息。 |
| 優先順序 | n/a | 服務匯流排僅支援單一訊息優先順序。 |
| Ttl | Message.TimeToLive | 轉換，Proton-PHP TTL 是以毫秒定義。 |
| first\_acquirer | - | - | | delivery\_count | - | - | | Id | Message.Id | - | | user\_id | - | - | | Address | Message.To | - | | Subject | Message.Label | - | | reply\_to | Message.ReplyTo | - | | correlation\_id | Message.CorrelationId | - | | content\_type | Message.ContentType | - | | content\_encoding | n/a | - | | expiry\_time | Message.ExpiresAtUTC | - | | creation\_time | n/a | - | | group\_id | Message.SessionId | - | | group\_sequence | - | - | | reply\_to\_group\_id | Message.ReplyToSessionId | - | | Format | n/a | -

#### 服務匯流排 .NET API 至 PROTON-PHP

| 服務匯流排 .NET | Proton-PHP | 注意事項 |
|-------------------------|--------------------------------------------------------|--------------------------------------------------------|
| ContentType | Message->content\_type | - | | CorrelationId | Message->correlation\_id | - | | EnqueuedTimeUtc | Message->annotations[x-opt-enqueued-time] | - | | Label | Message->subject | - | | MessageId | Message->id | - | | ReplyTo | Message->reply\_to | - | | ReplyToSessionId | Message->reply\_to\_group\_id | - | | ScheduledEnqueueTimeUtc | Message->annotations [“x-opt-scheduled-enqueue-time”] | - | | SessionId | Message->group\_id | - | | TimeToLive | Message->ttl | 轉換，Proton-PHP TTL 是以毫秒定義。 | | To | Message->address | - |

## 後續步驟

準備好進行深入了解嗎？ 請造訪下列連結：

- [服務匯流排 AMQP 概觀]
- [搭配使用 .NET 的服務匯流排與 AMQP]
- [搭配使用 Java 的服務匯流排與 AMQP]
- [搭配使用 Python 的服務匯流排與 AMQP]
- [在 Azure Linux VM 上安裝 Apache Qpid Proton-C]
- [Windows Server 服務匯流排中的 AMQP]


[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

[Windows Server 服務匯流排中的 AMQP]: https://msdn.microsoft.com/library/dn574799.aspx

[服務匯流排 AMQP 概觀]: service-bus-amqp-overview.md
[搭配使用 .NET 的服務匯流排與 AMQP]: service-bus-amqp-dotnet.md
[搭配使用 Java 的服務匯流排與 AMQP]: service-bus-amqp-java.md
[搭配使用 Python 的服務匯流排與 AMQP]: service-bus-amqp-python.md
[在 Azure Linux VM 上安裝 Apache Qpid Proton-C]: service-bus-amqp-apache.md

<!---HONumber=Oct15_HO2-->