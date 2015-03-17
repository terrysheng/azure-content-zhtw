<properties 
	pageTitle="如何使用佇列服務 (PHP) | Microsoft Azure" 
	description="了解如何使用 Azure 佇列服務來建立和刪除佇列，以及插入、取得和刪除訊息。範例以 PHP 撰寫。" 
	documentationCenter="php" 
	services="storage" 
	authors="tfitzmac" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="tomfitz"/>

# 如何透過 PHP 使用佇列服務

本指南將示範如何使用 Azure 佇列服務執行一般案例。這些範例均是以 Windows SDK for PHP 中的類別撰寫。所涵蓋的案例包括「插入」、「查看」、「取得」及「刪除」佇列訊息，以及「建立及刪除佇列」。如需佇列的詳細資訊，請參閱[後續步驟](#NextSteps)一節。

##目錄

* [什麼是佇列儲存體](#what-is)
* [概念](#concepts)
* [建立 Azure 儲存體帳戶](#create-account)
* [建立 PHP 應用程式](#create-app)
* [設定您的應用程式以存取佇列服務](#configure-app)
* [設定 Azure 儲存體連線](#connection-string)
* [做法：建立佇列](#create-queue)
* [做法：將訊息新增至佇列](#add-message)
* [做法：查看下一個訊息](#peek-message)
* [做法：將下一個訊息清除佇列](#dequeue-message)
* [做法：變更佇列訊息的內容](#change-message)
* [其他將訊息清除佇列的選項](#additional-options)
* [做法：取得佇列長度](#get-queue-length)
* [做法：刪除佇列](#delete-queue)
* [後續步驟](#next-steps)

[AZURE.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a id="create-account"></a>建立 Azure 儲存體帳戶</h2>

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

<h2><a id="create-app"></a>建立 PHP 應用程式</h2>

若要建立 PHP 應用程式並使其存取 Azure 佇列服務，唯一要求就是在您的程式碼中參考 Azure SDK for PHP 中的類別。您可以使用任何開發工具來建立應用程式 (包括 [記事本])。

在本指南中，您將使用可從 PHP 應用程式內本機呼叫的佇列服務功能，或可在 Azure Web 角色、背景工作角色或網站內執行的程式碼中呼叫的佇列服務功能。

<h2><a id="GetClientLibrary"></a>取得 Azure 用戶端程式庫</h2>

[AZURE.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

<h2><a id="configure-app"></a>設定您的應用程式以存取佇列服務</h2>

若要使用 Azure 佇列服務 API，您必須：

1. 參考使用 [require_once][require_once] 陳述式的自動換片器檔案，以及
2. 參考任何您可能使用的類別。

下列範例顯示如何納入自動換片器檔案及參考 **ServicesBuilder** 類別。

> [AZURE.NOTE]
> 此範例 (和本文中的其他範例) 假設您已透過 Composer 安裝 PHP Client Libraries for Azure。如果您以手動方式或以 PEAR 套件方式安裝程式庫，則必須參考  `WindowsAzure.php` 自動換片器檔案。

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


在下列各範例中，一律會顯示  `require_once` 陳述式，但只會參考要執行之範例所需的類別。

<h2><a id="connection-string"></a>設定 Azure 儲存體連線</h2>

若要具現化 Azure 佇列服務用戶端，您必須先具備有效的連接字串。佇列服務的連接字串格式為：

用於存取即時服務：

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

用於存取模擬器儲存體：

	UseDevelopmentStorage=true


若要建立任何 Azure 服務用戶端，您必須使用 **ServicesBuilder** 類別。您可以：

* 直接將連接字串傳遞給它，或
* 使用 **CloudConfigurationManager (CCM)** 到多種外部來源檢查連線字串：
	* 預設已支援一種外部來源 - 環境變數
	* 您可以擴充 **ConnectionStringSource** 類別以加入新來源

在本文的各範例中，將會直接傳遞連接字串。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);


<h2><a id="create-queue"></a>做法：建立佇列</h2>

**QueueRestProxy** 物件可讓您使用 **createQueue** 方法建立佇列。建立佇列時，您可以在佇列上設定選項，但這並非必要動作。(下列範例示範如何在佇列上設定中繼資料。)

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\CreateQueueOptions;
	
	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// OPTIONAL: Set queue metadata.
	$createQueueOptions = new CreateQueueOptions();
	$createQueueOptions->addMetaData("key1", "value1");
	$createQueueOptions->addMetaData("key2", "value2");
	
	try	{
		// Create queue.
		$queueRestProxy->createQueue("myqueue", $createQueueOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

> [AZURE.NOTE]
> 您不應該倚賴大小寫來區分中繼資料索引鍵。從服務讀取索引鍵時，所有索引鍵都是視為小寫。


<h2><a id="add-message"></a>做法：將訊息新增至佇列</h2>

若要將訊息新增至佇列，請使用 **QueueRestProxy->createMessage**。此方法會接受佇列名稱、訊息文字以及訊息選項 (選用)。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\CreateMessageOptions;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	try	{
		// Create message.
		$builder = new ServicesBuilder();
		$queueRestProxy->createMessage("myqueue", "Hello World!");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="peek-message"></a>做法：查看下一個訊息</h2>

透過呼叫 **QueueRestProxy->peekMessages** 方法，您可以在佇列前面查看訊息，而無需將它從佇列中移除。**peekMessage** 方法預設會傳回單一訊息，但是您可以使用 **PeekMessagesOptions->setNumberOfMessages** 方法變更該值。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\PeekMessagesOptions;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// OPTIONAL: Set peek message options.
	$message_options = new PeekMessagesOptions();
	$message_options->setNumberOfMessages(1); // Default value is 1.
	
	try	{
		$peekMessagesResult = $queueRestProxy->peekMessages("myqueue", $message_options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$messages = $peekMessagesResult->getQueueMessages();

	// View messages.
	$messageCount = count($messages);
	if($messageCount <= 0){
		echo "There are no messages.<br />";
	}
	else{
		foreach($messages as $message)	{
			echo "Peeked message:<br />";
			echo "Message Id: ".$message->getMessageId()."<br />";
			echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
			echo "Message text: ".$message->getMessageText()."<br /><br />";
		}
	}

<h2><a id="dequeue-message"></a>做法：將下一個訊息清除佇列</h2>

您的程式碼可以使用兩個步驟來將訊息從佇列中移除。首先，您需呼叫 **QueueRestProxy->listMessages**，這會讓從佇列讀取資料的任何其他程式碼無法看見此訊息。依預設，此訊息會維持不可見狀態 30 秒 (如果未在此期間內刪除訊息，訊息就會再次於佇列中變成可見。)若要完成從佇列中移除訊息，您必須呼叫 **QueueRestProxy->deleteMessage**。這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。您的程式碼會在處理完訊息之後立即呼叫 **deleteMessage**。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// Get message.
	$listMessagesResult = $queueRestProxy->listMessages("myqueue");
	$messages = $listMessagesResult->getQueueMessages();
	$message = $messages[0];
	
	/* ---------------------
		Process message.
	   --------------------- */
	
	// Get message Id and pop receipt.
	$messageId = $message->getMessageId();
	$popReceipt = $message->getPopReceipt();
	
	try	{
		// Delete message.
		$queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="change-message"></a>做法：變更佇列訊息的內容</h2>

透過呼叫 **QueueRestProxy->updateMessage**，您可以在佇列中就地變更訊息的內容。如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。下列程式碼將使用新的內容更新佇列訊息，並將可見度逾時設定延長 60 秒。這可儲存與訊息相關的工作狀態，並提供用戶端多一分鐘的時間繼續處理訊息。您可以使用此技巧來追蹤佇列訊息上的多步驟工作流程，如果因為硬體或軟體故障而導致某個處理步驟失敗，將無需從頭開始。通常，您也會保留重試計數，如果訊息重試超過 n 次，您會將它刪除。這麼做可防止每次處理時便觸發應用程式錯誤的訊息。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;	

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// Get message.
	$listMessagesResult = $queueRestProxy->listMessages("myqueue");
	$messages = $listMessagesResult->getQueueMessages();
	$message = $messages[0];
	
	// Define new message properties.
	$new_message_text = "New message text.";
	$new_visibility_timeout = 5; // Measured in seconds. 
	
	// Get message Id and pop receipt.
	$messageId = $message->getMessageId();
	$popReceipt = $message->getPopReceipt();
	
	try	{
		// Update message.
		$queueRestProxy->updateMessage("myqueue", 
									$messageId, 
									$popReceipt, 
									$new_message_text, 
									$new_visibility_timeout);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="additional-options"></a>其他將訊息清除佇列的選項</h2>

自訂從佇列中擷取訊息的方法有兩種。首先，您可以取得一批訊息 (最多 32 個)。其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完整處理每個訊息。下列程式碼範例將使用 **getMessages** 方法，在一次呼叫中取得 16 個訊息。接著它會使用 **for** 迴圈處理每個訊息。它也會將可見度逾時設定為每個訊息五分鐘。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\ListMessagesOptions;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// Set list message options. 
	$message_options = new ListMessagesOptions();
	$message_options->setVisibilityTimeoutInSeconds(300); 
	$message_options->setNumberOfMessages(16);
	
	// Get messages.
	try{
		$listMessagesResult = $queueRestProxy->listMessages("myqueue", 
														 $message_options); 
		$messages = $listMessagesResult->getQueueMessages(); 

		foreach($messages as $message){
			
			/* ---------------------
				Process message.
			--------------------- */
		
			// Get message Id and pop receipt.
			$messageId = $message->getMessageId();
			$popReceipt = $message->getPopReceipt();
			
			// Delete message.
			$queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);   
		}
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="get-queue-length"></a>做法：取得佇列長度</h2>

您可以取得佇列中的估計訊息數目。**QueueRestProxy->getQueueMetadata** 方法會要求佇列服務傳回佇列本身的中繼資料。在傳回的物件上呼叫 **getApproximateMessageCount** 方法會提供佇列中的訊息計數。此計數只是一個約略值，因為在佇列服務回應您的要求之後，仍有新增或移除訊息的可能。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	try	{
		// Get queue metadata.
		$queue_metadata = $queueRestProxy->getQueueMetadata("myqueue");
		$approx_msg_count = $queue_metadata->getApproximateMessageCount();
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	echo $approx_msg_count;

<h2><a id="delete-queue"></a>做法：刪除佇列</h2>

若要刪除佇列及其內含的所有訊息，請呼叫 **QueueRestProxy->deleteQueue** 方法。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	try	{
		// Delete queue.
		$queueRestProxy->deleteQueue("myqueue");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


<h2><a id="next-steps"></a>後續步驟</h2>

了解 Azure 佇列服務的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

- 請參閱 MSDN 參考：[在 Azure 中儲存及存取資料] []
- 請造訪 Azure 儲存體團隊部落格： <http://blogs.msdn.com/b/windowsazurestorage/>

[下載]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://www.php.net/manual/en/function.require-once.php
[Azure 管理入口網站]: http://manage.windowsazure.com/
[在 Azure 中儲存及存取資料]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
<!--HONumber=42-->
