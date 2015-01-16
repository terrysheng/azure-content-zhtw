<properties linkid="manage-services-Stream-Analytics-get-started" urlDisplayName="Get Started" pageTitle="開始使用 Azure 資料流分析 | Azure" metaKeywords="" description="開始使用 Azure Stream Analytics 處理和轉換 Azure Service Bus Event Hub 中的事件，並且將結果儲存至 Azure SQL Database。" metaCanonical="" services="stream analytics" documentationCenter="" title="Get started with Azure Stream Analytics" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="stream analytics" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/28/2014" ms.author="jgao" />


# 開始使用 Azure 資料流分析

Azure 資料流分析是完全受管理的服務，可用來對雲端中的串流資料進行低延遲、高可用性、可延展的複雜事件處理。如需詳細資訊，請參閱 [Azure 資料流分析簡介][stream.analytics.introduction]和 [Azure 資料流分析文件][stream.analytics.documentation]。

為了讓您快速開始使用資料流分析，本教學課程將說明如何從 [Azure 服務匯流排事件中心][azure.event.hubs.documentation]使用裝置溫度讀數資料並處理資料，接著將結果輸出至 [Azure SQL Database][azure.sql.database.documentation]。下圖顯示從輸入處理到輸出的事件流程：
  
![Azure Stream Analytics get started flow][img.get.started.flowchart]

##產生事件中心範例資料
本教學課程將會利用服務匯流排事件中心快速入門應用程式 (MSDN CodeGallery 中的程式碼範例) 建立新的事件中心、產生範例裝置溫度讀數，並將裝置讀數資料傳送至事件中心。

###建立服務匯流排命名空間
範例應用程式會以預先存在的服務匯流排命名空間建立事件中心。您可以使用已佈建的服務匯流排命名空間，或依照下列步驟來建立新的命名空間：

1.	登入 [Azure 管理入口網站][azure.management.portal]。
2.	在 [服務匯流排] 頁面底部按一下 [**建立**]，然後依照指示建立命名空間。以 [**傳訊**] 做為類型。
3.	按一下新建立的命名空間，然後在頁面底部按一下 [**連線資訊**]。
4.	複製連線字串。稍後在教學課程中將會用到這些資訊。

###建立 Azure 儲存體帳戶

使用此範例應用程式時，必須要有 Azure 儲存體帳戶或儲存體模擬器，以維護應用程式狀態。您可以使用現有的儲存體帳戶，或依照下列步驟建立新帳戶： 

1.	在入口網站中依序按一下 [**新增**]、[**資料服務**]、[**儲存體**]、[**快速建立**]，然後依照指示操作。
2.	選取新建立的儲存體帳戶，然後按一下頁面底部的 [**管理存取金鑰**]。
3.	複製儲存體帳戶名稱和其中一個存取金鑰。

###產生事件中心範例資料

1.	將 [Service Bus Event Hubs Getting Started.zip](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Event-Hub-286fd097) 下載並解壓縮至您的工作站。
2.	在 Visual Studio 中開啟 **EventHubSample.sln** 方案檔案。
3.	開啟 **app.config**。
4.	指定服務匯流排和儲存體帳戶連線字串。金鑰名稱為 **Microsoft.ServiceBus.ConnectionString** 和 **AzureStorageConnectionString**。儲存體帳戶連線字串會採用下列格式： 	

		DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<yourAccountKey>;
5.	建置方案。
6.	從 bin 資料夾中執行應用程式。使用方式如下： 

		BasicEventHubSample <eventhubname> <NumberOfMessagesToSend> <NumberOfPartitions> 

	下列範例會建立具有 **16** 個資料分割的新事件中心 **devicereadings**，然後將 **200** 個事件傳送至事件中心： 

		BasicEventHubSample devicereadings 200 16

 	![insert image here][img.stream.analytics.event.hub.client.output] 
 	

###建立事件中心共用存取原則
雖然服務匯流排命名空間上已有共用存取原則可用來連接到命名空間內的所有項目，但根據最佳安全性作法，我們只會為事件中心建立個別原則。

1.	從入口網站上的 [服務匯流排] 工作區，按一下 [服務匯流排] 命名空間名稱。
2.	按一下頁面頂端的 [**事件中心**]。
3.	按一下 **devicereadings**，這是本教學課程的事件中心。
4.	按一下頁面頂端的 [**設定**]。
5.	在 [共用存取原則] 下，使用**管理**權限建立新原則。

	![][img.stream.analytics.event.hub.shared.access.policy.config]
6.	按一下頁面底部的 [**儲存**]。
7.	如果事件中心位於與資料流分析工作不同的訂閱中，您必須複製並儲存連線資訊，以供後續使用。若要這樣做，請按一下 [**儀表板**]，然後按一下頁面底部的 [**連線資訊**]，並儲存連線字串。


##準備 Azure SQL Database 以儲存輸出資料
Azure 資料流分析可以將資料輸出至 Azure SQL Database、Azure Blob 儲存體和 Azure 事件中心。在本教學課程中，您將定義輸出至 Azure SQL Database 的工作。如需詳細資訊，請參閱「開始使用 Microsoft Azure SQL Database」。

###建立 Azure SQL Database
如果您已有 Azure SQL Database 可用於本教學課程，請略過本節。

1.	在管理入口網站中，按一下 [**新增**]、[**資料服務**]、[**SQL 資料庫**]、[**快速建立**]。指定現有或新的 SQL Database 伺服器上的資料庫名稱。
2.	選取新建的資料庫
3.	按一下 [**儀表板**]、按一下位於頁面右窗格上的 [**顯示連線字串**]，然後複製 **ADO.NET** 連線字串。稍後在教學課程中將會用到這些資訊。  
4.	確定伺服器層級防火牆設定可讓您連接到資料庫。您可以在伺服器的 [設定] 索引標籤下新增新的 IP 規則，以執行此動作。如需詳細資訊 (包括如何處理動態 IP)，請參閱 [http://msdn.microsoft.com/zh-tw/library/azure/ee621782.aspx](http://msdn.microsoft.com/zh-tw/library/azure/ee621782.aspx)。

###建立輸出資料表
1.	開啟 Visual Studio 或 SQL Server Management Studio。
2.	連接到 Azure SQL Database。
3.	使用下列 T-SQL 陳述式，建立資料庫的兩個資料表：

		CREATE TABLE [dbo].[PassthroughReadings] (
		    [DeviceId]      BIGINT NULL,
			[Temperature] BIGINT    NULL
		);

		GO
		CREATE CLUSTERED INDEX [PassthroughReadings]
		    ON [dbo].[PassthroughReadings]([DeviceId] ASC);
		GO

		CREATE TABLE [dbo].[AvgReadings] (
		    [WinStartTime]   DATETIME2 (6) NULL,
		    [WinEndTime]     DATETIME2 (6) NULL,
		    [DeviceId]      BIGINT NULL,
			[AvgTemperature] FLOAT (53)    NULL,
			[EventCount] BIGINT null
		);
		
		GO
		CREATE CLUSTERED INDEX [AvgReadings]
		    ON [dbo].[AvgReadings]([DeviceId] ASC);

	>[WACOM.NOTE] 所有 SQL 資料庫資料表上都必須有叢集索引，才能插入資料。
	   
##建立資料流分析工作

在您建立 Azure 服務匯流排事件中心、Azure SQL 資料庫和輸出資料表之後，您就可以開始建立資料流分析工作。

###佈建資料流分析工作
1.	在管理入口網站中，按一下 [**新增**]、[**資料服務**]、[**資料流分析**]、[**快速建立**]。 
2.	指定下列值，然後按一下 [**建立資料流分析工作**]：

	- **工作名稱**：輸入工作名稱。例如 **DeviceTemperatures**。
	- **區域**：選取要執行此工作的區域。Azure 資料流分析目前在預覽期間僅適用於 2 個區域。如需詳細資訊，請參閱 [Azure 資料流分析限制和已知問題][stream.analytics.limitations]。請考慮將工作和事件中心放在相同的區域以確保更好的效能，以及在區域之間傳輸資料時無須付費。
	- **儲存體帳戶**：選擇您為在此區域內執行的所有資料流分析工作儲存監視資料時所要使用的儲存體帳戶。您可以選擇現有的儲存體帳戶，或建立新帳戶。
	
3.	按一下左窗格中的 [**資料流分析**]，以列出資料流分析工作。

	![][img.stream.analytics.portal.button]
 
	新工作會以 [**未啟動**] 的狀態列出。請注意，頁面底部的 [**啟動**] 按鈕會停用。您必須先設定工作輸入、輸出、查詢等項目，才能啟動工作。 

###指定工作輸入

1.	按一下工作名稱。
2.	按一下頁面頂端的 [**輸入**]，然後按一下 [**新增輸入**]。開啟的對話方塊會逐步引導您完成設定輸入的若干步驟。
3.	選取 [**資料流**]，然後按一下向右按鈕。
4.	選取 [**事件中心**]，然後按一下右鍵。
5.	在第三頁上輸入或選取下列值： 

	- **輸入別名**：輸入此工作輸入的易記名稱。請注意，此名稱將用於後續的查詢。
	- **事件中心**：如果您建立的事件中心與資料流分析工作位於相同的訂閱中，請選取事件中心所在的命名空間。

		如果事件中心位於不同的訂閱中，請選取 [**使用其他訂閱中的事件中心**]，並手動輸入 [**服務匯流排命名空間**]、[**事件中心名稱**]、[**事件中心原則名稱**]、[**事件中心原則金鑰**] 和 [**事件中心資料分割計數**]。  

		>[WACOM.NOTE] 此範例會使用預設的資料分割數目，即 16 個。
		
	- **事件中心名稱**：選取您建立之 Azure 事件中心的名稱。本教學課程使用 **devicereadings**。
	- **事件中心原則名稱**：選取先前在本教學課程中建立的事件中心原則。
 
	![][img.stream.analytics.config.input]

6.	按一下向右按鈕。
7.	指定下列值：

	- **事件序列化程式格式**：JSON
	- **編碼**：UTF8

8.	按一下核取按鈕以新增此來源，並確認資料流分析可成功連接到事件中心。

###指定工作輸出
1.	按一下頁面頂端的 [**輸出**]，然後按一下 [**新增輸出**]。
2.	選取 [**SQL 資料庫**]，然後按一下向右按鈕。
3.	輸入或選取下列值。從您的資料庫使用 ADO.NET 連線字串，以填入下列欄位：

	- **SQL 資料庫**：選擇您先前在本教學課程中建立的 SQL 資料庫。如果在相同的訂閱中，請從下拉式功能表中選取資料庫。如果不是，請手動輸入 [伺服器名稱] 和 [資料庫] 欄位。 
	- **使用者名稱**：輸入 SQL 資料庫登入名稱。
	- **密碼**：輸入 SQL 資料庫登入密碼。
	- **資料表**：指定您要將輸出傳送至哪個資料表。目前請使用 **PassthroughReadings**。

	![][img.stream.analytics.config.output]

4.	按一下核取按鈕以建立您的輸出，並確認資料流分析可成功連接到指定的 SQL 資料庫。

###指定工作查詢
資料流分析用來說明轉換的簡單、宣告式查詢模型。若要深入了解語言，請參閱「Azure 資料流分析查詢語言參考」。  

本教學課程首先會說明將裝置溫度讀數輸出至 SQL 資料庫資料表的簡易通過查詢。

1.	按一下頁面頂端的 [**查詢**]。
2.	在程式碼編輯器中新增下列程式碼：

		SELECT DeviceId, Temperature FROM input
請確定輸入來源的名稱符合您先前指定的輸入名稱。
3.	按一下頁面底部的 [**儲存**]，然後按 [**是**] 加以確認。

##啟動工作
根據預設，資料流分析工作會從工作啟動時開始讀取傳入事件。由於事件中心包含要處理得現有資料，因此我們必須設定取用此歷史資料的工作。  

1.	按一下頁面頂端的 [**設定**]。
2.	將 [**啟動輸出**] 值變更為 [**自訂時間**，然後指定開始時間。請確定開始時間是您執行 BasicEventHubSample 之前的時間。  
3.	按一下頁面底部的 [**儲存**]，然後按 [**是**] 加以確認。
3.	按一下頁面頂端的 [**儀表板**]，然後按一下頁面底部的 [**啟動**]，再按 [**是**] 加以確認。在 [**快速概覽**] 窗格中，[**狀態**] 會變更為 [**啟動中**]，並且可能需要幾分鐘來完成啟動程序，然後進入 [**執行中**] 狀態。   


##檢視工作輸出

1.	在 Visual Studio 或 SQL Server Management Studio 中，連接到 SQL 資料庫，並執行下列查詢： 

		SELECT * FROM PassthroughReadings

2.	您會在事件中心裡看到對應至讀取事件的記錄。   

	![][img.stream.analytics.job.output1]

	您可以重新執行 BasicEventHubSample 應用程式以產生新的事件，並重新執行 SELECT * 查詢，以將其即時傳播至輸出。
	
	如果您有任何輸出遺失或不符預期的問題，請檢視工作的作業記錄 (其連結位於 [儀表板] 頁面的右窗格)。

##停止、更新和重新啟動工作
現在我們要對資料進行更有趣的查詢。
1.	在 [**儀表板**] 或 [**監視器**] 頁面上，按一下 [**停止**]。
2.	在 [**查詢**] 頁面上，將現有查詢取代為下列查詢，然後按一下 [**儲存**]：

		SELECT DateAdd(second,-5,System.TimeStamp) as WinStartTime, system.TimeStamp as WinEndTime, DeviceId, Avg(Temperature) as AvgTemperature, Count(*) as EventCount 
		FROM input
		GROUP BY TumblingWindow(second, 5), DeviceId

	這個新的查詢會使用事件發送至事件中心的時間做為時間戳記，尋找和預測每 5 秒的平均溫度讀數，和位於 5 秒時間範圍內的事件數目。
3.	在 [**輸出**] 頁面上，按一下 [**編輯**]。將輸出資料表從 PassthroughReadings 變更為 AvgReadings，然後按一下 [核取] 圖示。

4.	在 [**儀表板**] 頁面上，按一下 [**啟動**]。

##檢視工作輸出

1.	在 Visual Studio 或 SQL Server Management Studio 中，連接到 SQL 資料庫，並執行下列查詢：

		SELECT * from AvgReadings

2.	您會看到每 5 秒間隔的記錄顯示每個裝置的平均溫度和事件數目： 

	![][img.stream.analytics.job.output2]
 
3.	 若要繼續檢視執行中工作所處理的事件，請重新執行 BasicEventHubSample 應用程式。







##<a name="nextsteps"></a>後續步驟
在本教學課程中，您已了解如何使用資料流分析來處理天氣資料。若要深入了解，請參閱下列文章：


- [Azure 資料流分析簡介][stream.analytics.introduction]
- [Azure 資料流分析開發人員指南][stream.analytics.developer.guide]
- [調整 Azure 資料流分析工作][stream.analytics.scale]
- [Azure 資料流分析的限制與已知問題][stream.analytics.limitations]
- [Azure 資料流分析查詢語言參考][stream.analytics.query.language.reference]
- [Azure 資料流分析管理 REST API 參考][stream.analytics.rest.api.reference]




[img.stream.analytics.event.hub.client.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHClientOuput.png
[img.stream.analytics.event.hub.shared.access.policy.config]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHSharedAccessPolicyConfig.png
[img.stream.analytics.job.output2]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput2.png
[img.stream.analytics.job.output1]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput1.png
[img.stream.analytics.config.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureOutput.png
[img.stream.analytics.config.input]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureInput.png



[img.get.started.flowchart]: ./media/stream-analytics-get-started/StreamAnalytics.get.started.flowchart.png
[img.job.quick.create]: ./media/stream-analytics-get-started/StreamAnalytics.quick.create.png
[img.stream.analytics.portal.button]: ./media/stream-analytics-get-started/StreamAnalyticsPortalButton.png
[img.event.hub.policy.configure]: ./media/stream-analytics-get-started/StreamAnalytics.Event.Hub.policy.png
[img.create.table]: ./media/stream-analytics-get-started/StreamAnalytics.create.table.png
[img.stream.analytics.job.output]: ./media/stream-analytics-get-started/StreamAnalytics.job.output.png
[img.stream.analytics.operation.logs]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.png
[img.stream.analytics.operation.log.details]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.details.png


[azure.sql.database.firewall]: http://msdn.microsoft.com/zh-tw/library/azure/ee621782.aspx
[azure.event.hubs.documentation]: http://azure.microsoft.com/zh-tw/services/event-hubs/
[azure.sql.database.documentation]: http://azure.microsoft.com/zh-tw/services/sql-database/

[sql.database.introduction]: http://azure.microsoft.com/zh-tw/services/sql-database/
[event.hubs.introduction]: http://azure.microsoft.com/zh-tw/services/event-hubs/
[azure.blob.storage]: http://azure.microsoft.com/zh-tw/documentation/services/storage/
[azure.sdk.net]: ../dotnet-sdk/

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.scale]: ../stream-analytics-scale-jobs/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093




[azure.management.portal]: https://manage.windowsazure.com

