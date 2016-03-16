<properties 
   pageTitle="使用服務匯流排的共用存取簽章驗證 | Microsoft Azure"
   description="使用服務匯流排的 SAS 驗證詳細資訊"
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
   ms.date="12/09/2015"
   ms.author="sethm" />

# 使用服務匯流排的共用存取簽章驗證

[共用存取簽章 (SAS)](service-bus-sas-overview.md) 驗證可讓應用程式使用在命名空間或在與特定權限相關聯的訊息實體 (佇列或主題) 上設定的存取金鑰，向服務匯流排進行驗證。您可以接著使用此金鑰來產生 SAS 權杖，以便用戶端用來向服務匯流排進行驗證。

SAS 驗證支援包含在 Azure SDK 2.0 版或更新版本中。如需有關服務匯流排驗證的詳細資訊，請參閱[服務匯流排驗證和授權](service-bus-authentication-and-authorization.md)。

## 概念

服務匯流排中的 SAS 驗證牽涉到在服務匯流排資源上設定具有相關權限的密碼編譯金鑰。用戶端會藉由出示 SAS 權杖來宣告服務匯流排資源的存取權。此權杖包含所存取的資源 URI 以及使用設定的金鑰所簽署的有效期限。

您可以在服務匯流排[轉送](service-bus-fundamentals-hybrid-solutions.md#relays)、[佇列](service-bus-fundamentals-hybrid-solutions.md#queues)、[主題](service-bus-fundamentals-hybrid-solutions.md#topics)和[事件中樞](https://azure.microsoft.com/documentation/services/event-hubs/)上設定共用存取簽章授權規則。

SAS 驗證會使用下列元素︰

- [共用存取授權規則](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)︰Base64 表示法中的 256 位元主要密碼編譯金鑰、選用的次要金鑰以及金鑰名稱和相關權限 (接聽、傳送或管理權限的集合)。

- [共用存取簽章](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx)權杖︰使用資源字串的 HMAC-SHA256 所產生，包含所存取資源的 URI 和有效期限以及密碼編譯金鑰。下列各節所述的簽章和其他元素都會格式化為字串，以構成 [SharedAccessSignature](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx) 權杖。

## 共用存取簽章驗證的設定

您可以在服務匯流排命名空間、佇列或主題上設定 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 規則。目前不支援在服務匯流排訂用帳戶上設定 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)，但是您可以使用在命名空間或主題上設定的規則來保護訂用帳戶的存取安全。如需可說明此程序的實用範例，請參閱[搭配使用共用存取簽章 (SAS) 驗證與服務匯流排訂用帳戶](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)範例。

在服務匯流排命名空間、佇列或主題上最多可以設定 12 條這類規則。在服務匯流排命名空間上設定的規則可套用到該命名空間中的所有實體。

![SAS](./media/service-bus-shared-access-signature-authentication/IC676272.gif)

在此圖中，manageRuleNS、sendRuleNS 和 listenRuleNS 授權規則會套用到佇列 Q1 和主題 T1，而 listenRuleQ 和 sendRuleQ 只會套用到佇列 Q1，*sendRuleT* 只會套用到主題 T1。

[SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 的主要參數如下所示︰

|參數|說明|
|---|---|
|*KeyName*|用以描述授權規則的字串。|
|*PrimaryKey*|用來簽署和驗證 SAS 權杖的 Base64 編碼 256 位元主要金鑰。|
|*SecondaryKey*|用來簽署和驗證 SAS 權杖的 Base64 編碼 256 位元次要金鑰。|
|*AccessRights*|授權規則所授與的存取權限清單。這些權限可以是任何接聽、傳送和管理權限的集合。|

佈建完服務匯流排命名空間後，預設會建立一個 [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) 設為 **RootManageSharedAccessKey** 的 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)。此外，也會針對通知中樞設定兩個預設 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 物件：一個具有接聽、傳送和管理權限，另一個只有接聽權限。

## 重新產生並撤銷共用存取授權規則的金鑰

建議您定期重新產生用於 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 物件的金鑰。應用程式通常應該使用 [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) 來產生 SAS 權杖。重新產生金鑰，您應該以舊的主要金鑰取代 [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx)，並產生新金鑰做為新的主要金鑰。如此一來，您即可繼續使用以舊的主要金鑰簽發但尚未到期的權杖進行授權。

如果金鑰受到危害而必須撤銷金鑰，您可以重新產生 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 的 [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) 和 [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx)，並取代成新的金鑰。此程序會讓所有以舊金鑰簽章的權杖失效。

## 產生共用存取簽章權杖

任何有權存取在共用存取授權規則中指定的簽署金鑰的用戶端都可以產生 SAS 權杖。格式如下：

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

SAS 權杖的 **signature** 會使用 string-to-sign 的 HMAC-SHA256 雜湊 (包含授權規則的 [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) 屬性) 進行運算。string-to-sign 是由資源 URI 和有效期限所組成，其格式如下：

```
StringToSign = <resourceURI> + "\n" + expiry;
```

請注意，您應該使用編碼的資源 URI 進行此操作。資源 URI 是宣告其存取權之服務匯流排資源的完整 URI。例如，`http://<namespace>.servicebus.windows.net/<entityPath>` 或 `sb://<namespace>.servicebus.windows.net/<entityPath>`；也就是 `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`。

有效期限會以從新紀元時間 (Epoch) 1970 年 1 月 1日 00:00:00 UTC 時間至今的秒數表示。

用於簽署的共用存取授權規則必須設定於此 URI 或其中一個階層式上層所指定的實體。例如，先前範例中的 `http://contoso.servicebus.windows.net/contosoTopics/T1` 或 `http://contoso.servicebus.windows.net`。

SAS 權杖適用於 string-to-sign 中所用 `<resourceURI>` 底下的所有資源。

SAS 權杖中的 [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) 是指用來產生權杖之共用存取授權規則的 **keyName**。

*URL-encoded-resourceURI* 必須與簽章預算期間 string-to-sign 中所用的 URI 相同。它應該是[百分比編碼](https://msdn.microsoft.com/library/4fkewx0t.aspx)。

## 如何搭配使用共用存取簽章驗證與服務匯流排

下列案例包括授權規則的設定、SAS 權杖的產生以及用戶端授權。

如需服務匯流排應用程式的完整工作範例，以便了解相關設定及使用 SAS 授權，請參閱[共用存取簽章驗證與服務匯流排](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8)。以下提供相關的範例，說明如何使用在命名空間或主題上設定的 SAS 授權規則來保護服務匯流排訂用帳戶︰[使用共用存取簽章 (SAS) 驗證與服務匯流排訂用帳](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)。

## 存取命名空間上的共用存取授權規則

服務匯流排命名空間根目錄上的作業需要憑證驗證。您必須針對您的 Azure 訂用帳戶上傳管理憑證。若要上傳管理憑證，請按一下 [Azure 傳統入口網站][]的左窗格中的 [設定]。如需 Azure 管理憑證的詳細資訊，請參閱[建立 Azure 的管理憑證](https://msdn.microsoft.com/library/azure/gg551722.aspx)。

可供存取服務匯流排命名空間上共用存取授權規則的端點如下所示：

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

若要在服務匯流排命名空間上建立 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 物件，請以序列化為 JSON 或 XML 的規則資訊在此端點上執行 POST 作業。例如：

```
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

同樣地，在端點上使用 GET 作業以讀取在命名空間上設定的授權規則。

若要更新或刪除特定授權規則，請使用以下端點：

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## 存取實體上的共用存取授權規則

您可以透過對應 [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx)、[TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) 或 [NotificationHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.notifications.notificationhubdescription.aspx) 物件中的 [AuthorizationRules](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.authorizationrules.aspx) 集合，存取在服務匯流排佇列或主題上設定的 [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 物件。

下列程式碼示範如何新增佇列的授權規則。

```
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString( 
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey", 
    SharedAccessAuthorizationRule.GenerateRandomKey(), 
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## 使用共用存取簽章授權

搭配使用 Azure .NET SDK 與服務匯流排 .NET 程式庫的應用程式可以透過 [SharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.aspx) 類別來使用 SAS 授權。下列程式碼說明如何使用權杖提供者將訊息傳送至服務匯流排佇列。

```
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb", 
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri, 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

應用程式在接受連接字串的方法中使用 SAS 連接字串，也可以使用 SAS 進行驗證。

請注意，若要搭配使用 SAS 授權與服務匯流排轉送，您可以使用在服務匯流排命名空間上設定的 SAS 金鑰。如果您在命名空間 (具有 [RelayDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.relaydescription.aspx) 的 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)) 物件上明確建立轉送，您可以設定該轉送的 SAS 規則。若要搭配使用 SAS 授權與服務匯流排訂用帳戶，您可以使用在服務匯流排命名空間或主題上設定的 SAS 金鑰。

## 服務匯流排作業所需的權限

下表顯示服務匯流排資源上各種作業所需的存取權限。

|作業|所需的宣告|宣告範圍|
|---|---|---|
|**命名空間**|||
|在命名空間上設定授權規則|管理|任何命名空間位址|
|**服務登錄**|||
|列舉私人原則|管理|任何命名空間位址|
|轉送|||
|開始在命名空間上接聽|接聽|任何命名空間位址|
|將訊息傳送至命名空間上的接聽程式|傳送|任何命名空間位址|
|**佇列**|||
|建立佇列|管理|任何命名空間位址|
|刪除佇列|管理|任何有效的佇列位址|
|列舉佇列|管理|/$Resources/Queues|
|取得佇列描述|管理或傳送|任何有效的佇列位址|
|設定佇列的授權規則|管理|任何有效的佇列位址|
|傳送到佇列中|傳送|任何有效的佇列位址|
|從佇列接收訊息|接聽|任何有效的佇列位址|
|在 peek-lock 模式中接收訊息後放棄或完成訊息|接聽|任何有效的佇列位址|
|延遲訊息以便稍後擷取|接聽|任何有效的佇列位址|
|讓訊息寄不出去|接聽|任何有效的佇列位址|
|取得與訊息佇列工作階段相關聯的狀態|接聽|任何有效的佇列位址|
|設定與訊息佇列工作階段相關聯的狀態|接聽|任何有效的佇列位址|
|**主題**|||
|建立主題|管理|任何命名空間位址|
|刪除主題|管理|任何有效的主題位址|
|列舉主題|管理|/$Resources/Topics|
|取得主題描述|管理或傳送|任何有效的主題位址|
|設定主題的授權規則|管理|任何有效的主題位址|
|傳送至主題|傳送|任何有效的主題位址|
|**訂用帳戶**|||
|建立訂閱|管理|任何命名空間位址|
|刪除訂用帳戶|管理|../myTopic/Subscriptions/mySubscription|
|列舉訂用帳戶|管理|../myTopic/Subscriptions|
|取得訂用帳戶描述|管理或接聽|../myTopic/Subscriptions/mySubscription|
|在 peek-lock 模式中接收訊息後放棄或完成訊息|接聽|../myTopic/Subscriptions/mySubscription|
|延遲訊息以便稍後擷取|接聽|../myTopic/Subscriptions/mySubscription|
|讓訊息寄不出去|接聽|../myTopic/Subscriptions/mySubscription|
|取得與主題工作階段相關聯的狀態|接聽|../myTopic/Subscriptions/mySubscription|
|設定與主題工作階段相關聯的狀態|接聽|../myTopic/Subscriptions/mySubscription|
|**規則**|||
|建立規則|管理|../myTopic/Subscriptions/mySubscription|
|刪除規則|管理|../myTopic/Subscriptions/mySubscription|
|列舉規則|管理或接聽|../myTopic/Subscriptions/mySubscription/Rules|
|**通知中樞**|||
|建立通知中樞|管理|任何命名空間位址|
|建立或更新使用中裝置的註冊|接聽或管理|../notificationHub/tags/{tag}/registrations|
|更新 PNS 資訊|接聽或管理|../notificationHub/tags/{tag}/registrations/updatepnshandle|
|傳送至通知中樞|傳送|../notificationHub/messages|

## 後續步驟

如需服務匯流排中的 SAS 整體概觀，請參閱[共用存取簽章](service-bus-sas-overview.md)。

如需有關服務匯流排驗證的詳細背景資訊，請參閱[服務匯流排驗證和授權](service-bus-authentication-and-authorization.md)。

[Azure 傳統入口網站]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0107_2016-->