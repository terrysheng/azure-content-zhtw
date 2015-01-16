<properties urlDisplayName="Service Bus Queues" pageTitle="如何使用服務匯流排佇列 (Python) - Azure" metaKeywords ="Azure 服務匯流排佇列, Azure 佇列, Azure 訊息, Azure 佇列 Python" description="了解如何在 Azure 使用服務匯流排佇列。程式碼範例以 Python 撰寫。" metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Queues" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />




# 如何使用服務匯流排佇列
本指南將示範如何使用服務匯流排佇列。這些範例
均以 Python 撰寫並使用 Python Azure 模組。涵蓋的案例
包括**建立佇列、傳送並接收訊息**，以及
**刪除佇列**。如需佇列的詳細資訊，請參閱[後續步驟][]一節。

## 目錄

-   [什麼是服務匯流排佇列？][]
-   [建立服務命名空間][]
-   [取得命名空間的預設管理認證][]
-   [作法：建立佇列][]
-   [作法：傳送訊息至佇列][]
-   [作法：從佇列接收訊息][]
-   [作法：處理應用程式當機與無法讀取的訊息][]
-   [後續步驟][]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**注意：** 如果您需要安裝 Python 或用戶端程式庫，請參閱 [Python 安裝指南](../python-how-to-install/)。


## <a name="create-queue"> </a>如何建立佇列

**ServiceBusService** 物件可讓您處理佇列。將下列內容新增至您想要在其中以程式設計方式存取 Azure 服務匯流排之任何 Python 檔案內的頂端附近：

	from azure.servicebus import ServiceBusService, Message, Queue

下列程式碼會建立 **ServiceBusService** 物件。請使用真實的命名空間、共用存取簽章 (SAS) 金鑰名稱和值來取代 'mynamespace'、'sharedaccesskeyname' 和 'sharedaccesskey'。

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

SAS 金鑰名稱和值的值可以在 Azure 入口網站連接資訊中找到，或者在 [伺服器總管] 中選取服務匯流排命名空間時於 [Visual Studio 屬性] 視窗中找到 (如上一節所示)。

	bus_service.create_queue('taskqueue')

**create_queue** 也支援其他選項，這
可讓您覆寫訊息存留時間或佇列大小上限等
預設佇列設定。下列範例說明將佇列大小上限
設為 5GB，將存留時間設為 1 分鐘的設定：

	queue_options = Queue()
	queue_options.max_size_in_megabytes = '5120'
	queue_options.default_message_time_to_live = 'PT1M'

	bus_service.create_queue('taskqueue', queue_options)

## <a name="send-messages"> </a>如何傳送訊息至佇列

若要傳送訊息至服務匯流排佇列，應用程式會呼叫
**send\_queue\_message** 方法 (在 **ServiceBusService** 物件上)。

下列範例說明如何使用
使用 **send\_queue\_message** 將測試訊息傳送至名為 *taskqueue* 的佇列：

	msg = Message(b'Test Message')
	bus_service.send_queue_message('taskqueue', msg)

服務匯流排佇列支援 256 Kb 的訊息大小上限 (包含
標準和自訂應用程式屬性的標頭，可以容納
64 KB 的大小上限)。主題中的訊息數目沒有限制，
但是佇列所保有的訊息總大小有
上限。此佇列大小會在建立時定義，
上限是 5 GB。

## <a name="receive-messages"> </a>如何從佇列接收訊息

您可以使用 **receive\_queue\_message**
方法 (在 **ServiceBusService** 物件上)，以從佇列接收訊息：

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
	print(msg.body)

當您讀取訊息後，訊息便會從佇列中刪除，前提是將
**peek\_lock** 參數設為 **False**。您可以讀取 (查看) 並鎖定
訊息，避免系統從佇列刪除訊息，方法是將
**peek\_lock** 參數設為 **True**。

隨著接收作業讀取及刪除訊息之行為是
最簡單的模型，且最適合可容許在發生失敗時
不處理訊息的應用程式
案例。若要了解這點，請考慮以下案例：
取用者發出接收要求，然而在處理之前
當機。因為服務匯流排會將訊息標示為已取用，
當應用程式重新啟動並開始重新取用訊息時，
它將會遺漏當機前已取用的訊息。


如果 **peek\_lock** 參數設定為 **True**，接收會變成
兩階段作業，因此可以支援無法容許遺漏訊息
的應用程式。當服務匯流排收到
要求時，它會尋找下一個要取用的訊息並加以鎖定，以免
其他取用者接收該訊息，然後將它傳回應用程式。
在應用程式完成處理訊息 (或可靠地儲存
此訊息以供未來處理) 之後，它可呼叫
 **delete** 方法 (在 **Message**
物件上)，以完成接收程序的第二個階段。**delete** 方法會將訊息標示為已取用，
並將它從佇列移除。

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
	print(msg.body)

	msg.delete()

## <a name="handle-crashes"> </a>如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，協助您從應用程式的
錯誤或處理訊息的問題中順利復原。如果
接收者應用程式因故無法處理訊息，
它可以呼叫 **unlock** 方法 (在
**Message** 物件上)。這將導致服務匯流排將佇列中的
訊息解除鎖定，讓此訊息可以被相同取用
應用程式或其他取用應用程式
重新接收。

與佇列內鎖定訊息相關的還有逾時，
如果應用程式無法在鎖定逾時到期之前
處理訊息 (例如，如果應用程式當機)，則服務
匯流排會自動解除鎖定訊息，並讓訊息可以被
重新接收。

如果應用程式在處理訊息之後，
尚未呼叫 **delete** 方法時當機，則會在應用程式
重新啟動時將訊息重新傳遞給該應用程式。這通常稱為
**至少處理一次**，也就是說，每個訊息至少會被
處理一次，但在特定狀況下，可能會重新傳遞相同
訊息。如果案例無法容許重複處理，則
應用程式開發人員應將其他邏輯加入至其應用程式
來處理重複的訊息傳遞。通常您可使用訊息的
**MessageId** 方法來達到此目的，該方法將在各個傳遞嘗試中
保持不變。

## <a name="next-steps"> </a>後續步驟

現在，您已了解服務匯流排佇列的基本概念，請遵循下列
連結以了解更多資訊。

-   請參閱 MSDN 參考：[佇列、主題和訂閱。][]

  [後續步驟]: #next-steps
  [什麼是服務匯流排佇列？]: #what-are-service-bus-queues
  [建立服務命名空間]: #create-a-service-namespace
  [取得命名空間的預設管理認證]: #obtain-default-credentials
  [作法：建立佇列]: #create-queue
  [作法：傳送訊息至佇列]: #send-messages
  [作法：從佇列接收訊息]: #receive-messages
  [作法：處理應用程式當機與無法讀取的訊息]: #handle-crashes
  [佇列概念]: ../../../DevCenter/dotNet/Media/sb-queues-08.png
  [Azure 管理入口網站]: http://manage.windowsazure.com
  
  
  
  
  
  [佇列、主題和訂閱。]: http://msdn.microsoft.com/zh-tw/library/windowsazure/hh367516.aspx
