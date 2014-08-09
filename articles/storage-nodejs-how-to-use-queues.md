<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Node.js) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Node.js" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Queue Service from Node.js" authors="" solutions="" manager="" editor="" />

如何從 Node.js 使用佇列服務
===========================

本指南將示範如何使用 Windows Azure 佇列服務執行一般案例。這些範例使用 Node.js API 撰寫。所涵蓋的案例包括**「插入」**、**「查看」**、**「取得」**和**「刪除」**佇列訊息，以及**「建立和刪除佇列」**。如需佇列的詳細資訊，請參閱[後續步驟](#next-steps)一節。

目錄
----

-   [什麼是佇列服務？](#what-is)
-   [概念](#concepts)
-   [建立 Azure 儲存體帳戶](#create-account)
-   [建立 Node.js 應用程式](#create-app)
-   [設定您的應用程式以存取儲存體](#configure-access)
-   [設定 Azure 儲存體連接字串](#setup-connection-string)
-   [作法：建立佇列](#create-queue)
-   [作法：將訊息插入佇列](#insert-message)
-   [作法：查看下一個訊息](#peek-message)
-   [作法：將下一個訊息清除佇列](#get-message)
-   [作法：變更佇列訊息的內容](#change-contents)
-   [作法：其他將訊息移出佇列的選項](#advanced-get)
-   [作法：取得佇列長度](#get-queue-length)
-   [作法：刪除佇列](#delete-queue)
-   [後續步驟](#next-steps)

什麼是佇列服務？
----------------

Azure 佇列服務是一項儲存大量訊息的服務，全球任何地方都可利用 HTTP 或 HTTPS 並透過驗證的呼叫來存取這些訊息。單一佇列訊息的大小上限為 64KB，一個佇列可以包含數百萬個訊息，一個儲存體帳戶的總容量上限為 100TB。佇列服務的一般用途包括：

-   建立積存的工作供非同步處理
-   將訊息從 Azure Web 角色傳遞給背景工作角色

概念
----

佇列服務包含下列元件：

![Queue1](./media/storage-nodejs-how-to-use-queues/queue1.png)

-   **URL 格式：**可利用下列 URL 格式來定址佇列：

        http://storageaccount.queue.core.windows.net/queue  

    下列 URL 可定址圖中的其中一個佇列：

        http://myaccount.queue.core.windows.net/imagesToDownload

-   **儲存體帳戶：**一律透過儲存體帳戶來存取 Azure 儲存體。儲存體帳戶是存取佇列用的最高等級的命名空間。儲存體帳戶中的 blob、資料表及佇列內容的大小總計不能超過 100TB。

-   **佇列：**佇列包含一組訊息。所有訊息都必須放在佇列中。

-   **訊息：**訊息 (任何格式) 的大小上限為 64KB。

建立 Azure 儲存體帳戶
---------------------

若要使用儲存體作業，您需要 Azure 儲存體帳戶。您可以依照下列步驟來建立儲存體帳戶。(您也可以[使用 REST API](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh264518.aspx) 來建立儲存體帳戶。)

1.  登入 [Azure 管理入口網站](http://manage.windowsazure.com)。

2.  在導覽窗格的底端，按一下 **[新增]**。

    ![+新增](./media/storage-nodejs-how-to-use-queues/plus-new.png)

3.  按一下 **[儲存體帳戶]**，再按 **[快速建立]**。

    ![快速建立對話方塊](./media/storage-nodejs-how-to-use-queues/quick-storage.png)

4.  在 [URL] 中，為儲存體帳戶輸入要在 URI 中使用的子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。此值會成為 URI 內用來將訂閱的 Blob、「佇列」或「表格」資源定址的主機名稱。

5.  選擇要將儲存體放置於的「區域/同質群組」。如果您會從 Azure 應用程式使用儲存體，請選取您會在其中部署應用程式的相同區域。

6.  按一下 **[建立儲存體帳戶]**。

建立 Node.js 應用程式
---------------------

建立空白的 Node.js 應用程式。如需建立 Node.js 應用程式的指示，請參閱[建立並部署 Node.js 應用程式至 Azure 網站](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/)、[Node.js 雲端服務]({localLink:2221} "使用 Express 的 Web 應用程式") (使用 Windows PowerShell) 或[使用 WebMatrix 的網站](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/)。

設定您的應用程式以存取儲存體
----------------------------

若要使用 Azure 儲存體，您需要下載並使用 Node.js azure 封裝，這包含一組便利程式庫，能與儲存體 REST 服務通訊。

### 使用 Node Package Manager (NPM) 取得封裝

1.  使用命令列介面，例如 **PowerShell** (Windows)、**[終端機]** (Mac) 或 **Bash** (Unix)，瀏覽到您建立範例應用程式的資料夾。

2.  在命令視窗中輸入 **npm install azure**，這應該會導致下列輸出：

        azure@0.7.5 node_modules\azure
        |-- dateformat@1.0.2-1.2.3
        |-- xmlbuilder@0.4.2
        |-- node-uuid@1.2.0
        |-- mime@1.2.9
        |-- underscore@1.4.4
        |-- validator@1.1.1
        |-- tunnel@0.0.2
        |-- wns@0.5.3
        |-- xml2js@0.2.7 (sax@0.5.2)
        |-- request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  您可以手動執行 **ls** 命令，確認已建立 **node\_modules** 資料夾。在該資料夾內，您會找到 **azure** 封裝，其中包含您存取儲存體所需的程式庫。

### 匯入封裝

使用記事本或其他文字編輯器，將以下內容新增至您要使用儲存體之應用程式的 **server.js** 檔案頂端：

    var azure = require('azure');

設定 Azure 儲存體連接
---------------------

azure 模組會讀取環境變數 AZURE\_STORAGE\_ACCOUNT 及 AZURE\_STORAGE\_ACCESS\_KEY，以取得連接 Azure 儲存體帳戶所需的資訊。如果未設定這些環境變數，則在呼叫 **createQueueService** 時必須指定帳戶資訊。

如需在 Azure 雲端服務組態檔中設定環境變數的範例，請參閱[使用儲存體的 Node.js 雲端服務](/en-us/develop/nodejs/tutorials/web-app-with-storage/)。

如需在 Azure 網站管理入口網站中設定環境變數的範例，請參閱[使用儲存體的 Node.js Web 應用程式](/en-us/develop/nodejs/tutorials/web-site-with-storage/)。

作法：建立佇列
--------------

下列程式碼會建立一個 **QueueService** 物件，其讓您能夠使用佇列。

    var queueService = azure.createQueueService();

請使用 **createQueueIfNotExists** 方法，此方法會傳回指定的佇列 (如果佇列已經存在)，或以指定的名稱建立新佇列 (如果佇列不存在)。

    queueService.createQueueIfNotExists(queueName, function(error){
        if(!error){
            // Queue exists
        }
    });

### 篩選器

可以將選用性的篩選操作套用到使用 **QueueService** 執行的操作。篩選操作可包括記錄、自動重試等等。篩選器是以簽章實作方法的物件：

     function handle (requestOptions, next)

在對要求選項進行前處理之後，方法需要呼叫 "next" 並傳遞具有下列簽章的回呼：

     function (returnObject, finalCallback, next)

在此回呼中，以及處理 returnObject (來自對伺服器之要求的回應) 之後，回呼需要叫用 next (如果存在) 以繼續處理其他篩選，或是就改為叫用 finalCallback 結束服務叫用。

Azure SDK for Node.js 包含了實作重試邏輯的兩個篩選器：**ExponentialRetryPolicyFilter** 和 **LinearRetryPolicyFilter**。以下會建立使用 **ExponentialRetryPolicyFilter** 的 **QueueService** 物件：

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueService = azure.createQueueService().withFilter(retryOperations);

作法：將訊息插入佇列
--------------------

若要將訊息插入佇列，請使用 **createMessage** 方法建立新訊息，然後將該訊息加到佇列中。

    queueService.createMessage(queueName, "Hello world!", function(error){
        if(!error){
            // Message inserted
        }
    });

作法：查看下一個訊息
--------------------

透過呼叫 **peekMessages** 方法，您可以在佇列前面查看訊息，而無需將它從佇列中移除。**peekMessages** 預設會查看單一訊息。

    queueService.peekMessages(queueName, function(error, messages){
        if(!error){
            // Messages peeked
            // Text is available in messages[0].messagetext
        }
    });

> [WACOM.NOTE]
> 當佇列中沒有任何訊息時，使用 **peekMessage** 並不會傳回錯誤，不過，也不會傳回任何訊息。

作法：將下一個訊息清除佇列
--------------------------

您的程式碼會以兩個步驟將訊息從佇列中移除。呼叫 **getMessages** 時，您預設會取得佇列中的下一個訊息。從 **getMessage** 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言將會是不可見的。依預設，此訊息會維持不可見狀態 30 秒。若要完成將訊息從佇列中移除，您還必須呼叫 **deleteMessage**。這個移除訊息的兩步驟程序可確保當您的程式碼因為硬體或軟體故障而無法處理訊息時，程式碼的另一個執行個體可以取得相同訊息並再試一次。您的程式碼會在處理完訊息之後立即呼叫 **deleteMessage**。

	queueService.getMessages(queueName, function(error, messages){
	    if(!error){
	        // Process the message in less than 30 seconds, the message
	        // text is available in messages[0].messagetext 
			var message = messages[0]
	        queueService.deleteMessage(queueName
				, message.messageid
				, message.popreceipt
				, function(error){
	            	if(!error){
	                	// Message deleted
	            	}
	        	});
	    }
	});

> [WACOM.NOTE]
> 當佇列中沒有任何訊息時，使用 **getMessages** 並不會傳回錯誤，不過，也不會傳回任何訊息。

作法：變更佇列訊息的內容
------------------------

您可以在佇列中就地變更訊息內容。如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。下方的程式碼使用 **updateMessage** 方法來更新訊息。

    queueService.getMessages(queueName, function(error, messages){
		if(!error){
			// Got the message
			var message = messages[0];
			queueService.updateMessage(queueName
				, message.messageid
				, message.popreceipt
				, 10
				, { messagetext: 'in your message, doing stuff.' }
				, function(error){
					if(!error){
						// Message updated successfully
					}
				});
		}
	});

作法：其他將訊息移出佇列的選項
------------------------------

自訂從佇列中擷取訊息的方法有兩種。首先，您可以取得一批訊息 (最多 32 個)。其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。下列程式碼範例將使用 **getMessages** 方法，在一次呼叫中取得 15 個訊息。接著它會使用 for 迴圈處理每個訊息。它也會將可見度逾時設定為每個訊息五分鐘。

    queueService.getMessages(queueName
		, {numofmessages: 15, visibilitytimeout: 5 * 60}
		, function(error, messages){
		if(!error){
			// Messages retreived
			for(var index in messages){
				// text is available in messages[index].messagetext
				var message = messages[index];
				queueService.deleteMessage(queueName
					, message.messageid
					, message.popreceipt
					, function(error){
						if(!error){
							// Message deleted
						}
					});
			}
		}
	});

作法：取得佇列長度
------------------

您可以取得佇列中的估計訊息數目。**getQueueMetadata** 方法會要求佇列服務傳回佇列的相關中繼資料，而回應的 **approximatemessagecount** 屬性則會包含佇列中的訊息計數。此計數只是一個約略值，因為在佇列服務回應您的要求之後，仍有新增或移除訊息的可能。

    queueService.getQueueMetadata(queueName, function(error, queueInfo){
        if(!error){
            // Queue length is available in queueInfo.approximatemessagecount
        }
    });

作法：刪除佇列
--------------

若要刪除佇列及其內含的所有訊息，請在佇列物件上呼叫 **deleteQueue** 方法。

    queueService.deleteQueue(queueName, function(error){
        if(!error){
            // Queue has been deleted
        }
    });

後續步驟
--------

了解佇列儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

-   請參閱 MSDN 參考資料：[儲存體](http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx)。
-   造訪 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/) (英文)。
-   造訪 GitHub 上的 [Azure SDK for Node](https://github.com/WindowsAzure/azure-sdk-for-node) (英文) 儲存機制。

