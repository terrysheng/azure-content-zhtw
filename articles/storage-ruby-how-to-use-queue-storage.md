<properties 
	pageTitle="如何使用佇列服務 (Ruby) | Microsoft Azure" 
	description="了解如何使用 Azure 佇列服務來建立和刪除佇列，以及插入、取得和刪除訊息。範例以 Ruby 撰寫。" 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="tomfitz"/>





# 如何從 Ruby 使用佇列儲存體服務

本指南說明如何使用 Windows Azure 佇列儲存體服務執行一般案例。這些範例是以 Ruby Azure API 撰寫的。
所涵蓋的案例包括「插入」****、「查看」********「取得」****及「刪除」****、佇列訊息，以及「建立及刪除佇列」****。如需佇列的詳細資訊，請參閱[後續步驟](#next-steps) 一節。

## 目錄

* [什麼是佇列儲存體？](#what-is)
* [概念](#concepts)
* [建立 Azure 儲存體帳戶](#CreateAccount)
* [建立 Ruby 應用程式](#create-a-ruby-application)
* [設定您的應用程式以存取儲存體](#configure-your-application-to-access-storage)
* [設定 Azure 儲存體連線](#setup-a-windows-azure-storage-connection)
* [做法：建立佇列](#how-to-create-a-queue)
* [做法：將訊息插入佇列](#how-to-insert-a-message-into-a-queue)
* [做法：查看下一個訊息](#how-to-peek-at-the-next-message)
* [做法：清除下一個佇列訊息](#how-to-dequeue-the-next-message)
* [做法：變更佇列訊息的內容](#how-to-change-the-contents-of-a-queued-message)
* [做法：清除佇列訊息的其他選項](#how-to-additional-options-for-dequeuing-messages)
* [做法：取得佇列長度](#how-to-get-the-queue-length)
* [做法：刪除佇列](#how-to-delete-a-queue)
* [後續步驟](#next-steps)

[AZURE.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a id="CreateAccount"></a>建立 Azure 儲存體帳戶

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## <a id="create-a-ruby-application"></a>建立 Ruby 應用程式

建立 Ruby 應用程式。如需指示， 
請參閱[在 Azure 上建立 Ruby 應用程式](/zh-tw/develop/ruby/tutorials/web-app-with-linux-vm/)。

## <a id="configure-your-application-to-access-storage"></a>設定您的應用程式以存取儲存體

若要使用 Azure 儲存體，您需要下載並使用 Ruby azure 套件，這包含一組便利程式庫，能與儲存體 REST 服務通訊。

### 使用 RubyGems 來取得套件

1. 使用命令列介面，例如 **PowerShell** (Windows)、**Terminal** (Mac) 或 **Bash** (Unix)。

2. 在命令視窗中鍵入 "gem install azure" 以安裝 Gem 和相依性。

### 匯入封裝

使用您偏好的文字編輯器，將以下內容新增至您打算使用儲存體的 Ruby 檔案頂端：

	require "azure"

## <a id="setup-a-windows-azure-storage-connection"></a>設定 Azure 儲存體連線

Azure 模組將會讀取環境變數 **AZURE\_STORAGE\_ACCOUNT** 和 **AZURE\_STORAGE\_ACCESS_KEY** 
以取得連線至您的 Azure 儲存體帳戶時所需的資訊。如果尚未設定這些環境變數，您必須使用下列程式碼，在使用 **Azure::QueueService** 之前指定帳戶資訊：

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your Azure storage access key>"

若要取得這些值，請執行下列動作：

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com/)。
2. 瀏覽到您要使用的儲存體帳戶
3. 按一下導覽窗格底部的 **[管理金鑰]**。
4. 在快顯對話方塊中，您將會看到儲存體帳戶名稱、主要存取金鑰和次要存取金鑰。如需存取金鑰，您可以選取主要存取金鑰或次要存取金鑰。

## <a id="how-to-create-a-queue"></a>做法：建立佇列

下列程式碼會建立一個 **Azure::QueueService** 物件，其讓您能夠使用佇列。

	azure_queue_service = Azure::QueueService.new

使用 **create_queue()** 方法，建立具有指定名稱的佇列。

	begin
	  azure_queue_service.create_queue("test-queue")
	rescue
	  puts $!
	end

## <a id="how-to-insert-a-message-into-a-queue"></a>做法：將訊息插入佇列

若要將訊息插入佇列，請使用 **create_message()** 方法建立新訊息，然後將該訊息加到佇列中。

	azure_queue_service.create_message("test-queue", "test message")

## <a id="how-to-peek-at-the-next-message"></a>做法：查看下一個訊息

透過呼叫 **peek\_messages()** 方法，您可以在佇列前面查看訊息，而無需將它從佇列中移除。依照預設，**peek\_messages()** 會查看單一訊息。您也可以指定所要查看的訊息數。

	result = azure_queue_service.peek_messages("test-queue",
	  {:number_of_messages => 10})

## <a id="how-to-dequeue-the-next-message"></a>做法：清除下一個佇列訊息

您可以使用兩個步驟將訊息從佇列中移除。

1. 呼叫 **list\_messages()** 時，依照預設，您會取得佇列中的下一個訊息。您也可以指定您要取得的訊息數。對於從此佇列讀取訊息的任何其他程式碼而言，將無法看到從 **list\_messages()** 傳回的訊息。您可以傳入以秒為單位的可見性逾時，作為參數。

2. 若要完成從佇列中移除訊息，您還必須呼叫 **delete_message()**。

這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。您的程式碼會在處理完訊息之後立即呼叫 **delete\_message()**。

	messages = azure_queue_service.list_messages("test-queue", 30)
	azure_queue_service.delete_message("test-queue", 
	  messages[0].id, messages[0].pop_receipt)

## <a id="how-to-change-the-contents-of-a-queued-message"></a>做法：變更佇列訊息的內容

您可以在佇列中就地變更訊息內容。以下程式碼使用 **update_message()** 方法來更新訊息。此方法會傳回一個 Tuple (其中包含佇列訊息的 pop receipt) 和一個 UTC 日期時間值 (代表訊息將會顯示在佇列上的時間)。

	message = azure_queue_service.list_messages("test-queue", 30)
	pop_receipt, time_next_visible = azure_queue_service.update_message(
	  "test-queue", message.id, message.pop_receipt, "updated test message", 
	  30)

## <a id="how-to-additional-options-for-dequeuing-messages"></a>做法：清除佇列訊息的其他選項

自訂從佇列中擷取訊息的方法有兩種。

1. 您可以取得一批訊息。

2. 您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。

下列程式碼範例將使用 **list\_messages()** 方法，在一次呼叫中取得 15 個訊息。接著，它會列出每個訊息，並加以刪除。它也會將可見度逾時設定為每個訊息五分鐘。

	azure_queue_service.list_messages("test-queue", 300
	  {:number_of_messages => 15}).each do |m|
	  puts m.message_text
	  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
	end

## <a id="how-to-get-the-queue-length"></a>做法：取得佇列長度

您可以取得佇列中的估計訊息數目。**get\_queue\_metadata()** 方法會要求佇列服務傳回約略的訊息計數和佇列的相關中繼資料。

	message_count, metadata = azure_queue_service.get_queue_metadata(
	  "test-queue")

## <a id="how-to-delete-a-queue"></a>做法：刪除佇列

若要刪除佇列及其內含的所有訊息，請在佇列物件上呼叫 **delete\_queue()** 方法。

	azure_queue_service.delete_queue("test-queue")

## <a id="next-steps"></a>後續步驟

了解佇列儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

- 請參閱 MSDN 參考：[在 Azure 中儲存及存取資料](http://msdn.microsoft.com/library/windowsazure/gg433040.aspx)
- 請造訪 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/)
- 請造訪 GitHub 上的 [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) 儲存機制 (英文)。

關於本文中討論之 Azure 佇列服務和[如何使用服務匯流排佇列](/zh-tw/develop/ruby/how-to-guides/service-bus-queues/)文中討論之 Azure 服務匯流排佇列之間的比較，請參閱 [Azure 佇列與 Azure 服務匯流排 - 比較和對比](http://msdn.microsoft.com/library/windowsazure/hh767287.aspx)
<!--HONumber=42-->
