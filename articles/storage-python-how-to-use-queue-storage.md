<properties 
	pageTitle="如何使用佇列服務 (Python) | Microsoft Azure" 
	description="了解如何使用 Azure 佇列服務來建立和刪除佇列，以及插入、取得和刪除訊息。範例以 Python 撰寫。" 
	services="storage" 
	documentationCenter="python" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/19/2014" 
	ms.author="robmcm"/>



# 如何從 Python 使用佇列儲存體服務
本指南說明如何使用 Windows Azure 佇列儲存體服務執行一般案例。相關範例是以 Python API 撰寫。所涵蓋的案例包括「插入」、「查看」、「取得」及「刪除」佇列訊息，以及「建立及刪除佇列」。如需佇列的詳細資訊，請參閱[後續步驟][]一節。

## 目錄

[什麼是佇列儲存體][]   
 [概念][]   
 [建立 Azure 儲存體帳戶][]   
 [做法：建立佇列][]   
 [做法：將訊息插入佇列][]   
 [做法：查看下一個訊息][]   
 [做法：清除下一個佇列訊息][]   
 [做法：變更佇列訊息的內容][]   
 [做法：清除佇列訊息的其他選項][]   
 [做法：取得佇列長度][]   
 [做法：刪除佇列][]   
 [後續步驟][]

[AZURE.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a name="create-account"> </a>建立 Azure 儲存體帳戶
[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]


**注意：** 如果您需要安裝 Python 或用戶端程式庫，請參閱 [Python 安裝指南](../python-how-to-install/)。

## <a name="create-queue"> </a>做法：建立佇列

**QueueService** 物件可讓您操作佇列。下列程式碼將建立 **QueueService** 物件。將下列內容新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 Python 檔案內的頂端附近：

	from azure.storage import QueueService

下列程式碼會使用儲存體帳戶名稱和帳戶金鑰來建立 **QueueService** 物件：將  'myaccount' 和  'mykey' 取代為真實的帳戶和金鑰。

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## <a name="insert-message"> </a>做法：將訊息插入佇列

若要將訊息插入佇列，請使用 **put\_message** 方法建立新訊息，然後將該訊息加到佇列中。

	queue_service.put_message('taskqueue', 'Hello World')


## <a name="peek-message"> </a>做法：查看下一個訊息

透過呼叫 **peek\_messages** 方法，您可以在佇列前面查看訊息，而無需將它從佇列中移除。依照預設，
**peek\_messages** 可查看單一訊息。

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.message_text)


## <a name="get-message"> </a>做法：清除下一個佇列訊息

您的程式碼可以使用兩個步驟來將訊息從佇列中移除。呼叫 **get\_messages** 時，依照預設，您會取得佇列中的下一個訊息。對於從此佇列讀取訊息的任何其他程式碼而言，將無法看到從 **get\_messages** 傳回的訊息。依預設，此訊息會維持 30 秒的不可見狀態。若要完成從佇列中移除訊息，您還必須呼叫 **delete\_message**。這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。您的程式碼會在處理完訊息之後立即呼叫 **delete\_message**。

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)


## <a name="change-contents"> </a>做法：變更佇列訊息的內容

您可以在佇列中就地變更訊息內容。如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。下面的程式碼會使用 **update\_message**
方法來更新訊息。

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

## <a name="advanced-get"> </a>做法：清除佇列訊息的其他選項

自訂從佇列中擷取訊息的方法有兩種。首先，您可以取得一批訊息 (最多 32 個)。其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。下列程式碼範例將使用 **get\_messages** 方法，在一次呼叫中取得 16 個訊息。接著，它會
使用 for 迴圈處理每個訊息。它也會將可見度逾時設定為每個訊息五分鐘。

	messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

## <a name="get-queue-length"> </a>做法：取得佇列長度

您可以取得佇列中的估計訊息數目。**get\_queue\_metadata** 方法會要求佇列服務傳回佇列的相關中繼資料，且應使用 **x-ms-approximate-messages-count** 作為傳回字典的索引以找出計數。
由於佇列服務在回應您的要求之後可以新增或移除訊息，
此結果僅為近似值。

	queue_metadata = queue_service.get_queue_metadata('taskqueue')
	count = queue_metadata['x-ms-approximate-messages-count']

## <a name="delete-queue"> </a>做法：刪除佇列

若要刪除佇列及其內含的所有訊息，請呼叫 **delete\_queue** 方法。

	queue_service.delete_queue('taskqueue')

## <a name="next-steps"> </a>後續步驟

了解佇列儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

-   請參閱 MSDN 參考：[在 Azure 中儲存及存取資料][]
-   請造訪 [Azure 儲存體團隊部落格][]

  [後續步驟]: #next-steps
  [什麼是佇列儲存體？]: #what-is
  [概念]: #concepts
  [建立 Azure 儲存體帳戶]: #create-account
  [做法：建立佇列]: #create-queue
  [做法：將訊息插入佇列]: #insert-message
  [做法：查看下一個訊息]: #peek-message
  [做法：清除下一個佇列訊息]: #get-message
  [做法：變更佇列訊息的內容]: #change-contents
  [做法：清除佇列訊息的其他選項]: #advanced-get
  [做法：取得佇列長度]: #get-queue-length
  [做法：刪除佇列]: #delete-queue
  [在 Azure 中儲存及存取資料]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
  [Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
<!--HONumber=42-->
