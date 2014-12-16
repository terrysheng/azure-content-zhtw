<properties urlDisplayName="Service Bus Topics" pageTitle="如何使用服務匯流排主題 (Python) - Azure" metaKeywords="開始使用 Azure 服務匯流排主題發行訂閱訊息 Python" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Python applications." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Topics/Subscriptions" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />





# 如何使用服務匯流排主題/訂閱
本指南將說明如何從 Python 應用程式使用服務匯流排主題和訂閱。所涵蓋的案例包括**建立主題和訂閱、建立訂閱篩選器、傳送訊息**至主題、**接收訂閱的訊息**，及**刪除主題和訂閱**。如需主題和訂閱的詳細資訊，請參閱[後續步驟](#Next_Steps)一節。

## 目錄

-   [什麼是服務匯流排主題和訂閱？](#what-are-service-bus-topics)
-   [建立服務命名空間](#create-a-service-namespace)
-   [取得命名空間的預設管理認證](#obtain-default-credentials)
-   [作法：建立主題](#How_to_Create_a_Topic)
-   [作法：建立訂閱](#How_to_Create_Subscriptions)
-   [作法：傳送訊息至主題](#How_to_Send_Messages_to_a_Topic)
-   [作法：自訂閱接收訊息](#How_to_Receive_Messages_from_a_Subscription)
-   [作法：處理應用程式當機與無法讀取的訊息](#How_to_Handle_Application_Crashes_and_Unreadable_Messages)
-   [作法：刪除主題和訂閱](#How_to_Delete_Topics_and_Subscriptions)
-   [後續步驟](#Next_Steps)


[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

**注意：** 如果您需要安裝 Python 或用戶端程式庫，請參閱 [Python 安裝指南](../python-how-to-install/)。


##<a name="How_to_Create_a_Topic"></a>如何建立主題

**ServiceBusService** 物件可讓您處理主題。將下列內容新增至您想要在其中以程式設計方式存取 Azure 服務匯流排之任何 Python 檔案內的頂端附近：

	from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME

下列程式碼會建立 **ServiceBusService** 物件。請使用真實的命名空間、共用存取簽章 (SAS) 金鑰名稱和值來取代 'mynamespace'、'sharedaccesskeyname' 和 'sharedaccesskey'。

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

SAS 金鑰名稱和值的值可以在 Azure 入口網站連接資訊中找到，或者在 [伺服器總管] 中選取服務匯流排命名空間時於 [Visual Studio 屬性] 視窗中找到 (如上一節所示)。

	bus_service.create_topic('mytopic')

**create\_topic** 也支援其他選項，這
可讓您覆寫訊息存留時間或主題大小上限等
預設主題設定。下列範例將示範如何
將主題大小上限設為 5GB，將存留時間設為 1 分鐘的設定：

	topic_options = Topic()
	topic_options.max_size_in_megabytes = '5120'
	topic_options.default_message_time_to_live = 'PT1M'

	bus_service.create_topic('mytopic', topic_options)

##<a name="How_to_Create_Subscriptions"></a>如何建立訂閱

**ServiceBusService** 物件也能用來建立主題訂閱。訂閱是具名的，它們能擁有選用的篩選器，以限制傳遞至訂閱之虛擬佇列的訊息集合。

**注意**：訂閱是持續性的，它們會持續存在，直到本身或相關的主題遭到刪除為止。

### 使用預設 (MatchAll) 篩選器建立訂閱

**MatchAll** 篩選器是預設篩選器，如果在建立新的訂閱時沒有指定篩選器，便會使用此篩選器。使用 **MatchAll** 篩選器時，所有發佈至主題的訊息都會被置於訂閱的虛擬佇列中。下列範例將建立名為 'AllMessages' 的訂閱，並使用預設的 **MatchAll** 篩選器。

	bus_service.create_subscription('mytopic', 'AllMessages')

### 使用篩選器建立訂閱

您也可以設定篩選器，讓您界定哪些傳送至主題的訊息
應出現在特定主題訂閱中。

訂閱所支援的最具彈性篩選器類型是實作 SQL92 子集的 **SqlFilter**。SQL 篩選器會對發佈至主題之訊息的屬性運作。如需可與 SQL 篩選器搭配使用的運算式詳細資料，請檢閱 [SqlFilter.SqlExpression][] 語法。

您可以使用 **ServiceBusService** 物件的 **create\_rule** 方法將篩選器新增至訂閱。此方法可讓您將篩選器新增至現有的訂閱中。

**注意**：由於預設篩選器會自動套用至所有新訂閱，因此您必須先移除預設篩選器，否則 **MatchAll** 會覆寫您指定的其他任何篩選器。您可以使用 **ServiceBusService** 物件的 **delete\_rule** 方法移除預設規則。

以下範例將建立名為 'HighMessages' 並帶有只選取自訂 **messagenumber** 屬性大於 3 之 **SqlFilter** 的訂閱：

	bus_service.create_subscription('mytopic', 'HighMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber > 3'

	bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)

同樣地，以下範例將建立名為
'LowMessages' 帶有只選取 **messagenumber** 屬性小於或等於 3 之 **SqlFilter** 的訂閱： 
	bus_service.create_subscription('mytopic', 'LowMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber <= 3'

	bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)

當訊息傳送到 'mytopic' 時，一律會將該訊息傳遞到已訂閱 "AllMessages" 主題訂閱的接收者，並選擇性地將它傳遞到已訂閱 "HighMessages" 和 "LowMessages" 主題訂閱的接收者 (視訊息內容而定)。

##<a name="How_to_Send_Messages_to_a_Topic"></a>如何傳送訊息至主題

若要傳送訊息至服務匯流排主題，應用程式必須使用 **ServiceBusService** 物件的 **send\_topic\_message** 方法。

下列範例說明如何將五個測試訊息傳送至 'MyTopic''。請注意迴圈反覆運算上每個訊息的 **messagenumber** 屬性值的變化 (這可判斷接收訊息的訂閱為何)：

	for i in range(5):
		msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
		bus_service.send_topic_message('mytopic', msg)

服務匯流排主題支援 256 Kb 的訊息大小上限 (包含標準和自訂應用程式屬性的標頭可以容納 64 Kb 的大小上限)。主題中所保存的訊息數目沒有限制，但主題所保存的訊息大小總計會有最高限制。此主題大小會在建立時定義，上限是 5 GB。

##<a name="How_to_Receive_Messages_from_a_Subscription"></a>如何自訂閱接收訊息

您可以使用 **ServiceBusService** 物件的 **receive\_subscription\_message** 方法接收來自訂閱的訊息：

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
	print(msg.body)

將 **peek\_lock** 參數設為 **False** 時，則當您讀取訊息後，訊息便會從訂閱中刪除。您可以將參數 **peek\_lock** 設為 **True**，來讀取 (查看) 並鎖定訊息，避免系統從佇列刪除訊息。

隨著接收作業讀取及刪除訊息之行為是最簡單的模型，且最適合可容許在發生失敗時不處理訊息的應用程式案例。若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。


如果將 **peek\_lock** 參數設為 **True**，接收會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。在應用程式完成訊息處理 (或可靠地儲存此訊息以供未來處理) 之後，它會在 **Message** 物件上呼叫 **delete** 方法，以完成接收程序的第二個階段。**delete** 方法會將訊息標示為已取用，並將其從訂閱中移除。

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
	print(msg.body)

	msg.delete()


  ##<a name="How_to_Handle_Application_Crashes_and_Unreadable_Messages"></a>如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，協助您從應用程式的錯誤或處理訊息的問題中順利復原。如果接收者應用程式因為某些原因無法處理訊息，它可以在 **Message** 物件上呼叫 **unlock** 方法。這將導致服務匯流排將訂閱中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在訂閱內鎖定訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

如果應用程式在處理訊息之後，尚未呼叫 **delete** 方法時當機，則會在應用程式重新啟動時將訊息重新傳遞給該應用程式。這通常稱為**至少處理一次**，也就是說，每個訊息至少會被處理一次，但在特定狀況下，可能會重新傳遞相同訊息。如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。通常您可使用訊息的 **MessageId** 屬性來達到此目的，該屬性將在各個傳遞嘗試中會保持不變。

##<a name="How_to_Delete_Topics_and_Subscriptions"></a>如何刪除主題和訂閱

主題和訂閱是持續性的，您必須透過 Azure 管理入口網站
或程式設計明確地加以刪除。
下列範例說明如何刪除名為 'mytopic' 的主題：

	bus_service.delete_topic('mytopic')

刪除主題也將會刪除對主題註冊的任何訂閱。您也可以個別刪除訂閱。下列程式碼說明如何將名為 'HighMessages' 的訂閱從 'mytopic' 主題中刪除：

	bus_service.delete_subscription('mytopic', 'HighMessages')

##<a name="Next_Steps"></a>後續步驟

現在，您已了解服務匯流排主題的基本概念，請遵循下列
連結以了解更多資訊。

-   請參閱 MSDN 參考：[佇列、主題和訂閱][] (英文)。
-   [SqlFilter][] 的 API 參考資料。

  [後續步驟]: #nextsteps
  [什麼是服務匯流排主題和訂閱？]: #what-are-service-bus-topics
  [建立服務命名空間]: #create-a-service-namespace
  [取得命名空間的預設管理認證]: #obtain-default-credentials
  [作法：建立主題]: #How_to_Create_a_Topic
  [作法：建立訂閱]: #How_to_Create_Subscriptions
  [作法：傳送訊息至主題]: #How_to_Send_Messages_to_a_Topic
  [作法：自訂閱接收訊息]: #How_to_Receive_Messages_from_a_Subscription
  [作法：處理應用程式當機與無法讀取的訊息]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [作法：刪除主題和訂閱]: #How_to_Delete_Topics_and_Subscriptions
  
  [概念主題]: ../../../DevCenter/dotNet/Media/sb-topics-01.png
  [Azure 管理入口網站]: http://manage.windowsazure.com
  
  
  
  
  
  
  [佇列、主題和訂閱]: http://msdn.microsoft.com/zh-tw/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/zh-tw/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
