<properties linkid="dev-net-how-to-use-queue-storage-service-java" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Java) | Microsoft Azure" metaKeywords="Azure Queue Service, Azure Queue storage service, queues peeking, queues insert messages, queues get messages, queues delete messages, create queues, delete queues, Queue service Java" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use the Queue storage service from Java" authors="" solutions="" manager="" editor="" />

如何使用 Java 的佇列儲存體服務
==============================

本指南將示範如何使用 Azure 佇列儲存服務執行一般案例。相關範例是以 Java 撰寫並使用 [Azure SDK for Java](http://www.windowsazure.com/en-us/develop/java/)。所涵蓋的案例包括插入、查看、取得和刪除佇列訊息，以及建立和刪除佇列。如需佇列的詳細資訊，請參閱[後續步驟](#NextSteps)一節。

目錄
----

-   [什麼是佇列儲存體](#what-is)
-   [概念](#Concepts)
-   [建立 Azure 儲存體帳戶](#CreateAccount)
-   [建立 Java 應用程式](#CreateApplication)
-   [設定您的應用程式以存取佇列儲存體](#ConfigureStorage)
-   [設定 Azure 儲存體連接字串](#ConnectionString)
-   [作法：建立佇列](#create-queue)
-   [作法：將訊息新增至佇列](#add-message)
-   [作法：查看下一個訊息](#peek-message)
-   [作法：將下一個訊息清除佇列](#dequeue-message)
-   [作法：變更佇列訊息的內容](#change-message)
-   [清除佇列訊息的其他選項](#additional-options)
-   [作法：取得佇列長度](#get-queue-length)
-   [作法：刪除佇列](#delete-queue)
-   [後續步驟](#NextSteps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

建立 Azure 儲存體帳戶
---------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

建立 Java 應用程式
------------------

在本指南中，您將使用的儲存功能可在 Java 應用程式中進行本機呼叫，或在 Azure Web 角色或背景工作角色中執行的程式碼中呼叫。我們假設您已下載並安裝 Java Development Kit (JDK)，且遵循[下載 Azure SDK for Java](http://www.windowsazure.com/en-us/develop/java/) (英文) 中的指示，安裝 Azure Libraries for Java 和 Azure SDK，也在 Azure 訂閱中建立了 Azure 儲存體帳戶。您可以使用任何開發工具來建立應用程式 (包括 [記事本])。您只需要能夠編譯 Java 專案並參照 Azure Libraries for Java。

設定您的應用程式以存取佇列儲存體
--------------------------------

將下列 import 陳述式新增到您要在其中使用 Azure 儲存體 API 存取佇列的 Java 檔案頂端：

    // Include the following imports to use queue APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.queue.client.*;

設定 Azure 儲存體連接字串
-------------------------

Azure 儲存體用戶端使用儲存體連接字串，來存放存取資料管理服務時所需的端點與認證。在用戶端應用程式中執行時，您必須以下列格式提供儲存體連接字串，並針對 *AccountName* 及 *AccountKey* 值，使用管理入口網站所列之儲存體帳戶的儲存體帳戶名稱和主要存取金鑰。本範例示範如何宣告靜態欄位，來存放連接字串：

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

在 Azure 的角色內執行的應用程式中，此字串可以存放在服務組態檔 ServiceConfiguration.cscfg 中，且可以藉由呼叫 RoleEnvironment.getConfigurationSettings 方法來存取。以下是從服務組態檔中，名為 *StorageConnectionString* 的 **Setting** 元素取得連接字串的範例：

    // Retrieve storage account from connection-string
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

作法：建立佇列
--------------

CloudQueueClient 物件可讓您取得佇列的參照物件。下列程式碼將建立 CloudQueueClient 物件。

本指南的所有程式碼都使用上述兩個方法之一所宣告的儲存體連接字串。也有其他方式可建立 CloudStorageAccount 物件。請參閱 CloudStorageAccount 的 Javadocs 文件，以取得詳細資訊。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

使用 CloudQueueClient 物件來取得想要使用佇列的參照。如果佇列不存在，您可以建立佇列。

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.createIfNotExist();

作法：將訊息新增至佇列
----------------------

若要將訊息插入現有佇列，請先建立新的 CloudQueueMessage。接著，呼叫 addMessage 方法。您可以從字串 (採用 UTF-8 格式) 或位元組陣列建立 CloudQueueMessage。以下是建立佇列 (如果佇列不存在) 並插入訊息 "Hello, World" 的程式碼。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.createIfNotExist();

    // Create a message and add it to the queue
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);

作法：查看下一個訊息
--------------------

透過呼叫 peekMessage，您可以在佇列前面查看訊息，而無需將它從佇列中移除。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.peekMessage();

作法：清除下一個佇列訊息
------------------------

您的程式碼可以使用兩個步驟來清除佇列訊息。呼叫 retrieveMessage 時，您會取得佇列中的下一個訊息。對於從此佇列讀取訊息的任何其他程式碼而言，將無法看到從 retrieveMessage 傳回的訊息。依預設，此訊息會維持 30 秒的不可見狀態。若要完成從佇列中移除訊息，您還必須呼叫 deleteMessage。這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。您的程式碼會在處理完訊息之後立即呼叫 deleteMessage。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    // Process the message in less than 30 seconds, and then delete the message.
    queue.deleteMessage(retrievedMessage);

作法：變更佇列訊息的內容
------------------------

您可以在佇列中就地變更訊息內容。如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。下列程式碼將使用新的內容更新佇列訊息，並將可見度逾時設定延長 60 秒。這可儲存與訊息相關的工作狀態，並提供用戶端多一分鐘的時間繼續處理訊息。您可以使用此技巧來追蹤佇列訊息上的多步驟工作流程，如果因為硬體或軟體故障而導致某個處理步驟失敗，將無需從頭開始。通常，您也會保留重試計數，如果訊息重試超過 n 次，您會將它刪除。這麼做可防止每次處理時便觸發應用程式錯誤的訊息。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue
    CloudQueueMessage message = queue.retrieveMessage();

    // Modify the message content and set it to be visible in 60 seconds
    message.setMessageContent("Updated contents.");
    EnumSet<MessageUpdateFields> updateFields = 
    EnumSet.of(MessageUpdateFields.CONTENT, MessageUpdateFields.VISIBILITY);
    queue.updateMessage(message, 60, updateFields, null, null);

清除佇列訊息的其他選項
----------------------

自訂從佇列中擷取訊息的方法有兩種。首先，您可以取得一批訊息 (最多 32 個)。其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。

下列程式碼範例將使用 retrieveMessages 方法，在一次呼叫中取得 20 個訊息。接著它會使用 **for** 迴圈處理每個訊息。它也會將可見度逾時設定為每個訊息五分鐘 (300 秒)。請注意，系統會針對所有訊息同時開始計時五分鐘，所以從呼叫 retrieveMessages 開始的五分鐘後，任何尚未刪除的訊息都會重新出現。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds
    for (CloudQueueMessage message :queue.retrieveMessages(20, 300, null, null)) {
    // Do processing for all messages in less than 5 minutes, 
    // deleting each message after processing.
    queue.deleteMessage(message);
    }

作法：取得佇列長度
------------------

您可以取得佇列中的估計訊息數目。downloadAttributes 方法會向佇列服務要求數個目前值，包括計算佇列中的訊息數。由於佇列服務在回應您的要求之後可以新增或移除訊息，此計數僅為近似值。getApproximateMethodCount 方法會傳回呼叫 downloadAttributes 所擷取的最後一個值，而無需呼叫佇列服務。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Download the approximate message count from the server
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count
    long cachedMessageCount = queue.getApproximateMessageCount();

作法：刪除佇列
--------------

若要刪除佇列及其內含的所有訊息，請在佇列物件上呼叫 delete 方法。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue
    queue.delete();

後續步驟
--------

了解佇列儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

-   請參閱 MSDN 參考：[在 Azure 中儲存和存取資料](http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx)
-   請造訪 Azure 儲存體團隊部落格：<http://blogs.msdn.com/b/windowsazurestorage/>

