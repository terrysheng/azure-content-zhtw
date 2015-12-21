<properties 
	pageTitle="如何將事件記錄到 Azure API 管理中的 Azure 事件中樞" 
	description="了解如何將事件記錄到 Azure API 管理中的 Azure 事件中樞。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="sdanie"/>

# 如何將事件記錄到 Azure API 管理中的 Azure 事件中樞

事件中樞是可高度調整的資料輸入服務，每秒可擷取數百萬個事件，可讓您處理和分析連接的裝置和應用程式所產生的大量資料。事件中樞能做為事件管線的「大門」，一旦收集的資料進入事件中樞，它可以使用任何即時分析提供者或批次/儲存配接器轉換及儲存資料。事件中樞能分隔事件串流的生產與這些事件的使用，讓事件消費者依照自己的排程存取事件。

這篇文章附隨於[整合 Azure API 管理與事件中樞](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/)視訊並說明如何使用 Azure 事件中樞記錄 API 管理事件。

## 建立 Azure 事件中樞

若要建立新的事件中樞，請登入 [Azure 傳統入口網站](https://manage.windowsazure.com)，按一下 [新增] -> [應用程式服務] -> [服務匯流排] -> [事件中樞] -> [快速建立]。輸入事件中樞名稱、區域，選取訂用帳戶，然後選取命名空間。如果您先前尚未建立命名空間，可以在 [命名空間] 文字方塊中輸入名稱加以建立。設定好所有屬性後，按一下 [建立新的事件中樞] 建立事件中樞。

![建立事件中樞][create-event-hub]

接下來，瀏覽至新事件中樞的 [設定] 索引標籤，建立兩個 [共用存取原則]。將第一個原則命名為 **Sending**，並授與 [傳送] 權限。

![Sending 原則][sending-policy]

將第二個原則命名為 **Receiving**，並授與 [接聽] 權限，然後按一下 [儲存]。

![Receiving 原則][receiving-policy]

每個共用存取原則都可讓應用程式傳送事件至事件中樞和接收來自事件中樞的事件。若要存取這些原則的連接字串，請瀏覽至事件中樞的 [儀表板] 索引標籤，然後按一下 [連接資訊]。

![Connection string][event-hub-dashboard]

**Sending** 連接字串用於記錄事件，**Receiving** 連接字串則用於從事件中樞下載事件時。

![Connection string][event-hub-connection-string]

## 建立 API 管理記錄器

現在您已經有事件中樞，下一步是在 API 管理服務中設定[記錄器](https://msdn.microsoft.com/library/azure/mt592020.aspx)，以將事件記錄至事件中樞。

可使用 [API 管理 REST API](http://aka.ms/smapi) 來設定 API 管理記錄器。在第一次使用 REST API 之前，請先檢閱[必要條件](https://msdn.microsoft.com/library/azure/dn776326.aspx#Prerequisites)，確定您已[啟用 REST API 的存取](https://msdn.microsoft.com/library/azure/dn776326.aspx#EnableRESTAPI)。

若要建立記錄器，請使用下列 URL 範本提出 HTTP PUT 要求。

    https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview

-	以 API 管理服務執行個體的名稱取代 `{your service}`。
-	以您想要的新記錄器名稱取代 `{new logger name}`。當您設定 [log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) 原則時，將會參考此名稱。

將下列標頭加到要求中。

-	Content-Type : application/json
-	Authorization : SharedAccessSignature uid=...
	-	如需產生 `SharedAccessSignature` 的相關指示，請參閱 [Azure API 管理 REST API 驗證](https://msdn.microsoft.com/library/azure/dn798668.aspx)。

使用下列範本指定要求本文。

    {
      "type" : "AzureEventHub",
      "description" : "Sample logger description",
      "credentials" : {
        "name" : "Name of the Event Hub from the Azure Classic Portal",
        "connectionString" : "Endpoint=Event Hub Sender connection string"
        }
    }

-	`type` 必須設為 `AzureEventHub`。
-	`description` 提供記錄器的選擇性描述，如有需要，可以是零長度字串。
-	`credentials` 包含 Azure 事件中樞的 `name` 和 `connectionString`。

當您提出要求時，如果記錄器已建立，會傳回狀態碼 `201 Created`。

>[AZURE.NOTE]如需其他可能的傳回碼和其原因，請參閱[建立記錄器](https://msdn.microsoft.com/library/azure/mt592020.aspx#PUT)。若要查看如何執行其他作業，例如列出、更新和刪除，請參閱[記錄器](https://msdn.microsoft.com/library/azure/mt592020.aspx)實體文件。

## 設定 log-to-eventhubs 原則

您在 API 管理中設定好記錄器後，便可設定 log-to-eventhubs 原則來記錄所需的事件。log-to-eventhubs 原則可用於輸入原則區段或輸出原則區段。

若要設定原則，請登入 [Azure 傳統入口網站](https://manage.windowsazure.com)，瀏覽您的 API 管理服務，然後按一下 [發佈者入口網站] 或 [管理]，以存取發佈者入口網站。

![發行者入口網站][publisher-portal]

按一下左側 [API 管理] 功能表中的 [原則]，選取所需產品和 API，然後按一下 [新增原則]。在此範例中，我們將原則加入 **Unlimited** 產品中的 **Echo API**。

![Add policy][add-policy]

請將游標放置在 `inbound` 原則區段中，按一下 [Log to EventHub] 原則，插入 `log-to-eventhub` 原則陳述式範本。

![Policy editor][event-hub-policy]

    <log-to-eventhub logger-id ='logger-id'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
    </log-to-eventhub>

以您在上一個步驟中所設定的 API 管理記錄器名稱取代 `logger-id`。

您可以使用任何能傳回字串做為 `log-to-eventhub` 項目之值的運算式。在此範例中，將記錄包含日期和時間、服務名稱、要求 ID、 要求的 IP 位址和作業名稱的字串。

按一下 [儲存]，儲存更新的原則組態。儲存完成後，原則便會啟用，事件會記錄至指定的事件中樞。

## 後續步驟

-	深入了解 Azure 事件中樞
	-	[開始使用 Azure 事件中樞](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
	-	[使用 EventProcessorHost 接收訊息](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
	-	[事件中樞程式設計指南](../event-hubs/event-hubs-programming-guide.md)
-	深入了解 API 管理和事件中樞的整合
	-	[記錄器實體參考](https://msdn.microsoft.com/library/azure/mt592020.aspx)
	-	[log-to-eventhub 原則參考](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
	-	[利用 Azure API 管理、事件中樞及 Runscope 監視您的 API](api-management-log-to-eventhub-sample.md)	

## 觀看影片逐步解說

> [AZURE.VIDEO integrate-azure-api-management-with-event-hubs]


[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png

<!---HONumber=AcomDC_1210_2015-->