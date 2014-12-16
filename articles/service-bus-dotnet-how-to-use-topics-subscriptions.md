<properties urlDisplayName="Service Bus Topics" pageTitle="如何使用服務匯流排主題 (.NET) - Azure" metaKeywords="開始使用 Azure 服務匯流排主題, Azure 發行訂閱訊息, Azure 訊息主題和訂閱 C# " description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for .NET applications. " metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Topics/Subscriptions" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />





# 如何使用服務匯流排主題/訂閱

<span>本指南將示範如何使用服務匯流排主題和訂閱。這些範例均以 C\# 撰寫並使用 .NET API。所涵蓋的案例包括**建立主題和訂閱、建立訂閱篩選器、傳送訊息**至主題、**接收訂閱的訊息**，及**刪除主題和訂閱**。如需主題和訂閱的詳細資訊，請參閱[後續步驟][]一節。 </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

<h2>設定應用程式以使用服務匯流排</h2>

當您建立使用服務匯流排的應用程式時，您必須新增服務匯流排組件的參照，並包含對應的命名空間。

<h2>取得服務匯流排 NuGet 封裝</h2>

服務匯流排 **NuGet** 封裝是取得服務匯流排 API，
並對應用程式進行設定，以使用所有服務匯流排相依性的最簡單方法。NuGet Visual Studio 擴充功能可讓您更輕鬆地在 Visual Studio 和 Visual Studio Express 2012 for Web 中安裝並更新程式庫與工具。要取得服務匯流排 API，並對應用程式進行設定，以使用所有服務匯流排相依性的最簡單方法，便是使用服務匯流排 NuGet 封裝。

若要在應用程式中安裝 NuGet 封裝，請執行下列動作：

1.  在 [方案總管] 中以滑鼠右鍵按一下 [**參考**]，然後按一下 [
    **Manage NuGet Packages**]。
2.  搜尋 "WindowsAzure"，並選取 [**Azure 服務匯流排**] 項目。按一下 [**安裝**] 完成安裝作業，然後關閉此對話方塊。

    ![][7]

您現在可以開始對服務匯流排撰寫程式碼。

<h2>如何設定服務匯流排連接字串</h2>

服務匯流排使用連接字串來儲存端點和認證。您可以將連接字串置於設定檔中，而非硬式編碼至程式碼中：

- 使用 Azure 雲端服務時，建議您使用 Azure 服務設定系統 (`*.csdef` and `*.cscfg` 檔案) 來儲存連接字串。
- 使用 Azure 網站或 Azure 虛擬機器時，建議您使用 .NET 設定系統 (例如 `web.config` 檔案) 來儲存連接字串。

在這兩種情況下，您都可以使用 `CloudConfigurationManager.GetSetting` 方法擷取連接字串，如本指南稍後所示範。

### <a name="config-connstring"> </a>在使用雲端服務時設定連接字串

服務設定機制為 Azure 雲端服務專案所獨有，可讓您從 Azure 管理入口網站動態變更組態設定，而無需重新部署應用程式。例如，在您的服務定義 (`*.csdef`) 檔案中新增一個設定，如下所示：

	<ServiceDefinition name="WindowsAzure1">
	...
		<WebRole name="MyRole" vmsize="Small">
	    	<ConfigurationSettings>
	      		<Setting name="Microsoft.ServiceBus.ConnectionString" />
    		</ConfigurationSettings>
  		</WebRole>
	...
	</ServiceDefinition>

You then specify values in the service configuration (`*.cscfg`) file:

	<ServiceConfiguration serviceName="WindowsAzure1">
	...
		<Role name="MyRole">
			<ConfigurationSettings>
				<Setting name="Microsoft.ServiceBus.ConnectionString" 
						 value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
			</ConfigurationSettings>
		</Role>
	...
	</ServiceConfiguration>

使用從管理入口網站擷取的發行者和金鑰值，如上一節所述。

### 在使用網站或虛擬機器時設定連接字串

使用網站或虛擬機器時，建議您使用 .NET 設定系統 (例如 `web.config`)。您可以使用 `<appSettings>` 元素儲存連接字串：

	<configuration>
	    <appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
			     value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
		</appSettings>
	</configuration>

使用從管理入口網站擷取的發行者和金鑰值，如上一節所述。

<h2>如何建立主題</h2>

您可以透過 **NamespaceManager** 類別，來執行服務匯流排主題和訂閱的管理作業。**NamespaceManager** 類別提供建立、列舉及刪除佇列的方法。 

本範例會使用 Azure **CloudConfigurationManager** 類別，以及包含服務匯流排服務命名空間基底位址的連接字串和具備管理此連接字串權限的適當認證，來建構 **NamespaceManager** 物件。此連接字串的格式是 

	Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedSecretIssuer=<issuerName>;SharedSecretValue=<yourDefaultKey>

例如，基於上一節中的組態設定：

	// Create the topic if it does not exist already
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic("TestTopic");
    }

**CreateTopic** 方法的超載可讓您調整主題的屬性 (例如，針對要在傳送至主題的訊息套用的 [存留時間] 設定預設值)。您可以使用 **TopicDescription** 類別來套用這些設定。下列範例將示範如何使用大小上限為 5 GB 和預設訊息存留時間為 1 分鐘的設定，來建立名為 "TestTopic" 的佇列。

	// Configure Topic Settings
    TopicDescription td = new TopicDescription("TestTopic");
    td.MaxSizeInMegabytes = 5120;
    td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

	// Create a new Topic with custom settings
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic(td);
    }

**注意：**您可以在 **NamespaceManager** 物件上使用 **TopicExists**  方法，來檢查服務命名空間內是否已有指定名稱的主題存在。

<h2>如何建立訂閱</h2>

您也可以使用 **NamespaceManager** 類別來建立主題訂閱。訂閱是具名的，它們能擁有選用的篩選器，以限制傳遞至訂閱之虛擬佇列的訊息集合。

### 使用預設 (MatchAll) 篩選器建立訂閱

**MatchAll** 篩選器是預設篩選器，如果在建立新訂閱時沒有指定篩選器，便會使用此篩選器。使用 **MatchAll** 篩選器時，所有發佈至主題的訊息都會被置於訂閱的虛擬佇列中。下列範例將建立名為 "AllMessages" 的訂閱，並使用預設的 **MatchAll** 篩選器。

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### 使用篩選器建立訂閱

您也可以設定篩選器，讓您指定傳送至主題的哪些訊息應出現在特定主題訂閱中。

訂閱所支援的最具彈性篩選器類型是實作 SQL92 子集的 **SqlFilter**。SQL 篩選器會對發佈至主題之訊息的屬性運作。如需可與 SQL 篩選器搭配使用的運算式詳細資料，請檢閱 [SqlFilter.SqlExpression][] 語法。

以下範例將建立名為 "HighMessages" 並帶有只選取自訂 **MessageNumber** 屬性大於 3 的訊息之 **SqlFilter** 的訂閱：

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
		new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
		"HighMessages", 
		highMessagesFilter);

Similarly, the following example creates a subscription named
"LowMessages" with a **SqlFilter** that only selects messages that have
a **MessageNumber** property less than or equal to 3:

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
		new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
		"LowMessages", 
		lowMessagesFilter);

當訊息傳送到 "TestTopic" 時，一律會將該訊息傳遞到已訂閱 "AllMessages" 主題訂閱的接收者，並選擇性地將它傳遞到已訂閱 "HighMessages" 和 "LowMessages" 主題訂閱的接收者 (視訊息內容而定)。

<h2>如何傳送訊息至主題</h2>

若要傳送訊息至服務匯流排主題，應用程式會使用連接字串來建立 **TopicClient** 物件。

以下的程式碼將示範如何使用 **CreateFromConnectionString** API 呼叫，來為上面所建立的 "TestTopic" 佇列建立 **TopicClient** 物件：

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

   	TopicClient Client = 
		TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

	Client.Send(new BrokeredMessage());
 

傳送至服務匯流排主題的訊息是 **BrokeredMessage** 類別的執行個體。**BrokeredMessage** 物件具有一組標準屬性 (例如 **Label** 和 **TimeToLive**)、一個用來保存自訂應用程式特定屬性的目錄，以及一堆任意的應用程式資料。應用程式可設定訊息內文，方法是將任何可序列化物件傳遞到 **BrokeredMessage** 的建構函式，接著系統便會使用適當的 **DataContractSerializer** 來序列化物件。也可以提供 **System.IO.Stream**。

下列範例將示範如何傳送五則測試訊息至上述程式碼片段中所取得的 "TestTopic" **TopicClient**。請注意迴圈反覆運算上每個訊息的 **MessageNumber** 屬性值的變化 (這可判斷接收訊息的訂閱為何)：

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = 
		new BrokeredMessage("Test message " + i);

       // Set additional custom app-specific property
       message.Properties["MessageNumber"] = i;

       // Send message to the topic
       Client.Send(message);
     }

服務匯流排主題支援 256 Kb 的訊息大小上限 (包含標準和自訂應用程式屬性的標頭可以容納 64 Kb 的大小上限)。主題中所保存的訊息數目沒有限制，但主題所保存的訊息大小總計會有最高限制。此佇列大小會在建立時定義，上限是 5 GB。

<h2>如何自訂閱接收訊息</h2>

自訂閱接收訊息的最簡單方式是使用 **SubscriptionClient** 物件。**SubscriptionClient** 物件可以在兩種不同模式下運作：**ReceiveAndDelete** 和 **PeekLock**。

使用 **ReceiveAndDelete** 模式時，接收是一次性作業；也就是說，當服務匯流排收到訂閱中訊息的讀取要求時，它會將此訊息標示為已使用，並將它傳回應用程式。**ReceiveAndDelete** 模式是最簡單的模型，且最適合可容許在發生失敗時不處理訊息的應用程式案例。若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

在 **PeekLock** 模式 (此為預設模式) 中，接收程序會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。在應用程式完成處理訊息 (或可靠地儲存此訊息以供未來處理) 之後，它可透過呼叫所接收訊息上的 **Complete**，來完成接收程序的第二個階段。當服務匯流排看到 **Complete** 呼叫時，它會將訊息標示為已取用，並將它從訂閱中移除。

以下範例將示範如何使用預設的 **PeekLock** 模式來接收與處理訊息。若要指定其他 **ReceiveMode** 值，您可以使用 **CreateFromConnectionString** 的另一個超載。此範例會建立一個無限迴圈，並在訊息抵達 "HighMessages" 訂閱時處理訊息。請注意，至 "HighMessages" 訂閱的路徑會以 "<*主題路徑*\>/subscriptions/<*訂閱名稱*\>" 的格式提供。

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client = 
		SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

	Client.Receive();
     
	// Continuously process messages received from the HighMessages subscription 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();

       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("MessageNumber: " + 
				message.Properties["MessageNumber"]);

             // Remove message from subscription
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in subscription
             message.Abandon();
          }
       }
    } 

<h2>如何處理應用程式當機與無法讀取的訊息</h2>

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。如果接收端應用程式因為某些原因無法處理訊息，它可以呼叫所接收訊息上的 **Abandon** 方法 (而不是 **Complete** 方法)。這將導致服務匯流排將訂閱中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在訂閱內鎖定訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

如果應用程式在處理訊息之後，尚未發出 **Complete** 要求時當機，則會在應用程式重新啟動時將訊息重新傳遞給該應用程式。這通常稱為**至少處理一次**，也就是說，每個訊息至少會被處理一次，但在特定狀況下，可能會重新傳遞相同	 訊息。如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。通常您可使用訊息的 **MessageId** 屬性來達到此目的，該屬性將在各個傳遞嘗試中會保持不變。

<h2>如何刪除主題和訂閱</h2>

下列範例示範如何將名為
**TestTopic** 的主題從 **HowToSample** 服務命名空間中刪除：

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

刪除主題也將會刪除對主題註冊的任何訂閱。您也可以個別刪除訂閱。下列程式碼將示範如何將名為 **HighMessages** 的訂閱從 **TestTopic** 主題中刪除：

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

<h2>後續步驟</h2>

了解基本的服務匯流排主題和訂閱之後，請參考下列連結以取得更多資訊。

-   請參閱 MSDN 參考：[佇列、主題和訂閱][] (英文)。
-   [SqlFilter][] 的 API 參考資料。
-   建置從從服務匯流排佇列傳送和接收訊息的
工作應用程式：[服務匯流排代理訊息 .NET
教學課程][]。

  [後續步驟]: #nextsteps
  [什麼是服務匯流排主題和訂閱]: #what-is
  [建立服務命名空間]: #create-namespace
  [取得命名空間的預設管理認證]: #obtain-creds
  [設定應用程式使用服務匯流排]: #configure-app
  [作法：設定服務匯流排連線字串]: #set-up-connstring
  [作法：設定連接字串]: #config-connstring
  [作法：建立主題]: #create-topic
  [作法：建立訂閱]: #create-subscriptions
  [作法：傳送訊息至主題]: #send-messages
  [作法：自訂閱接收訊息]: #receive-messages
  [作法：處理應用程式當機與無法讀取的訊息]: #handle-crashes
  [作法：刪除主題和訂閱]: #delete-topics
  
  [概念主題]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/sb-topics-01.png
  [Azure 管理入口網站]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  
  [佇列、主題和訂閱]: http://msdn.microsoft.com/zh-tw/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/zh-tw/library/microsoft.servicebus.messaging.sqlfilter.aspx
  [服務匯流排代理訊息 .NET 教學課程]: http://msdn.microsoft.com/zh-tw/library/hh367512.aspx
