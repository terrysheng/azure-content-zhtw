<properties
	pageTitle="使用 App Service Mobile Apps 受管理的用戶端程式庫 (Windows | Xamarin) | Microsoft Azure"
	description="了解如何搭配 Windows 和 Xamarin 應用程式針對 Azure App Service Mobile Apps 使用 .NET 用戶端。"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/05/2015" 
	ms.author="glenga"/>

# 如何針對 Azure Mobile Apps 使用受管理的用戶端

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##概觀 

本指南將示範如何在 Windows 和 Xamarin 應用程式中針對 Azure App Service Mobile Apps 使用受管理的用戶端程式庫，來執行一般案例。如果您不熟悉 Mobile Apps，您應考慮先完成 [Mobile Apps 快速入門](app-service-mobile-windows-store-dotnet-get-started.md)教學課程。在本指南中，我們會著重於用戶端受管理的 SDK。若要深入了解 .NET 後端的伺服器端 SDK，請參閱[使用 .NET 後端](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

##<a name="setup"></a>設定和必要條件

我們假設您已建立並發佈您的行動應用程式後端專案 (包含在一個資料表中)。在本主題使用的程式碼中，資料表的名稱為 `TodoItem`，且其內容包含下列資料行：`Id`、`Text` 和 `Complete`。這就是當您完成[快速入門教學課程](app-service-mobile-windows-store-dotnet-get-started.md)時所建立的相同資料表

C# 中對應的具類型用戶端類型如下：


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

請注意 [JsonPropertyAttribute](http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm) 是用來定義用戶端類型與資料表之間的 PropertyName 對應。

##<a name="create-client"></a>作法：建立行動應用程式用戶端

下列程式碼將建立用來存取行動應用程式後端的 `MobileServiceClient` 物件。


	MobileServiceClient client = new MobileServiceClient(
		"MOBILE_APP_URL", "", "");

在上述程式碼中，以行動應用程式後端 URL 取代 `MOBILE_APP_URL`，這位於 Azure Preview 入口網站的 [行動應用程式] 刀鋒視窗中。

##<a name="instantiating"></a>作法：建立資料表參考

存取或修改後端資料表中資料的所有程式碼，都會在 `MobileServiceTable` 物件上呼叫函數。透過在 `MobileServiceClient` 的執行個體上呼叫 [GetTable](https://msdn.microsoft.com/library/azure/jj554275.aspx) 方法來取得資料表的參考，如下所示：

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

這是具類型的序列化模型。也支援不具類型的序列化模型。下列範例會建立不具類型的資料表的參考：

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

在不具類型的查詢中，您必須指定基礎 OData 查詢字串。

##<a name="querying"></a>作法：查詢行動應用程式中的資料

本節將說明如何對行動應用程式後端發出查詢，包括下列功能：

- [篩選傳回的資料]
- [排序傳回的資料]
- [以分頁方式傳回資料]
- [選取特定資料欄]
- [按識別碼查詢資料]

>[AZURE.NOTE]系統會強制使用伺服器控制的頁面大小，以防止傳回所有資料列。這可避免預設的大型資料集要求對服務造成負面影響。若要傳回 50 筆以上的資料列，請依照[以分頁方式傳回資料]的說明，使用 `Take` 方法。

### <a name="filtering"></a>作法：篩選傳回的資料

下列程式碼說明如何在查詢中包含 `Where` 子句，以篩選資料。它會從 `todoTable` 傳回其 `Complete` 屬性等於 `false` 的所有項目。`Where` 函數會套用資料列篩選述語來查詢資料表。

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

您可以使用訊息檢查軟體 (例如瀏覽器開發人員工具或 [Fiddler]) 來檢視傳送至後端的要求 URI。如果您查看下面的要求 URI，您會注意到我們打算修改查詢字串本身：

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

此要求通常會被概略轉譯成 Azure 端上的下列 SQL 查詢：

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

傳遞至 `Where` 方法的函數可以有任意數目的條件。以下面的程式碼行為例：

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false
		   && todoItem.Text != null)
	   .ToListAsync();

會被概略轉譯 (針對先前顯示的相同要求) 成

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

上述 `where` 陳述式將會尋找 `Complete` 狀態設為 false，且 `Text` 不是 Null 的項目。

我們也可以將它改寫成多行程式碼：

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

這兩種方法是相同的，而且可以交換使用。在一個查詢中串連多個述語的舊有選項較為精簡，也是我們給您的建議。

`where` 子句可支援被轉譯成 OData 子集的作業。這包括關係運算子 (==、!=、<、<=、>、>=)、算術運算子 (+、-、/、*、%)、精確度位數 (Math.Floor、Math.Ceiling)、字串函數 (Length、Substring、Replace、IndexOf、StartsWith、EndsWith)、日期屬性 (Year、Month、Day、Hour、Minute、Second)、物件的存取屬性，及結合上述所有的運算式。

### <a name="sorting"></a>作法：排序傳回的資料

下列程式碼說明如何在查詢中加入 `OrderBy` 或 `OrderByDescending` 函數，以將資料排序。它會從 `todoTable` 傳回項目，並依據 `Text` 欄位以遞增順序排列。

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

### <a name="paging"></a>作法：以分頁方式傳回資料

依預設，後端僅會傳回前 50 筆資料列。您可以提高傳回的資料列數，方法是呼叫 [Take] 方法。使用 `Take` 搭配 [Skip] 方法來要求查詢傳回之總資料集的特定「頁面」。執行下列查詢時，會傳回資料表中的前三個項目。

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

下列已修訂查詢會略過前三個結果，並傳回接下來的後三個結果。實際上這就是第二「頁」資料，頁面大小為三個項目。

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

您也可以使用 [IncludeTotalCount] 方法，確保查詢會忽略指定的任何採取分頁/限制子句，而取得已傳回 <i>all</i> 記錄的總數：

	query = query.IncludeTotalCount();

這是傳遞硬式編碼分頁值至 `Take` 和 `Skip` 方法的簡化案例。在實際的應用程式中，您可以搭配頁面巡覽區控制項或類似的 UI 來使用類似上述的查詢，讓使用者導覽至上一頁和下一頁。

>[AZURE.NOTE]若要覆寫行動應用程式後端中的 50 個資料列限制，您也必須將 [EnableQueryAttribute](https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx) 套用到公用的 GET 方法並指定分頁行為。套用到方法時，下列設定最多傳回 1000 個資料列：

    [EnableQuery(MaxTop=1000)]


### <a name="selecting"></a>作法：選取特定資料欄

若要指定將哪些屬性集包含在結果中，您可以在查詢中加入 `Select` 子句。例如，下列程式碼將示範如何只選取一個欄位以及如何選取及格式化多個欄位：

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}", 
						todoItem.Text.PadRight(30), todoItem.Complete ? 
						"Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();

到目前為止，上述的所有函數都是加法，因此我們可以持續呼叫這些函數，每一次查詢受到的影響就會越大。再提供一個範例：

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>作法：按識別碼查詢資料

`LookupAsync` 函數可用來查閱具有特定識別碼之資料庫中的物件。

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="lookingup"></a>作法：執行不具類型的查詢

使用具類型的資料表物件執行查詢時，您必須明確指定 OData 查詢字串，如下列範例所示：

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

您將收到可用作屬性包的 JSON 值。如需有關 JToken 和 Json.NET 的詳細資訊，請參閱 [Json.NET](http://json.codeplex.com/)

##<a name="inserting"></a>作法：將資料插入行動應用程式後端

所有用戶端類型必須包含名為 **識別碼** 的成員，其預設為字串。需要有此 **Id** 才能執行 CRUD 作業和離線。下列程式碼將說明如何將新的資料列插入資料表中。參數包含要作為 .NET 物件插入的資料。

	await todoTable.InsertAsync(todoItem);

如果傳遞至 `todoTable.InsertAsync` 呼叫的 `todoItem` 中，沒有包含唯一自訂識別碼值，則識別碼值會由伺服器產生，且設定在傳回用戶端的 `todoItem` 物件中。

若要插入不具類型的資料，您可以充份利用 Json.NET，如下所示。

	JObject jo = new JObject();
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

以下是使用電子郵件地址作為唯一字串 id 的範例。

	JObject jo = new JObject();
	jo.Add("id", "myemail@emaildomain.com");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);


###使用識別碼值

Mobile Apps 支援資料表 **id** 資料欄使用唯一自訂字串值。這可讓應用程式使用自訂的值，例如電子郵件地址或使用者名稱作為識別碼。

字串識別碼為您提供下列優點：

+ 不需要往返存取資料庫就能產生識別碼。
+ 輕鬆合併不同資料表或資料庫的記錄。
+ 識別碼值與應用程式邏輯的整合更理想。

當插入的記錄上未設定字串識別碼值時，行動應用程式後端會產生唯一值做為識別碼。您可以使用 `Guid.NewGuid()` 方法在用戶端上或在後端中產生您自己的識別碼值。

##<a name="modifying"></a>作法：修改行動應用程式後端中的資料

下列程式碼將說明如何使用含有新資訊的相同 ID 來更新現有的執行個體。參數包含要作為 .NET 物件更新的資料。

	await todoTable.UpdateAsync(todoItem);

若要插入不具類型的資料，您可以利用 Json.NET，如下所示：JObject jo = new JObject(); jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D"); jo.Add("Text", "Hello World"); jo.Add("Complete", false); var inserted = await table.UpdateAsync(jo);

請注意，進行更新時，必須指定 ID。這是後端識別要更新哪個執行個體的方式。您可以從 `InsertAsync` 呼叫的結果取得 ID。當您嘗試更新項目但未提供 "Id" 值時，就會引發 `ArgumentException`。


##<a name="deleting"></a>作法：刪除行動應用程式後端中的資料

下列程式碼將說明如何刪除現有的執行個體。您可以透過 `todoItem` 上所設定的 [Id] 欄位來識別執行個體。

	await todoTable.DeleteAsync(todoItem);

若要刪除不具類型的資料，您可以充份利用 Json.NET，如下所示：

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

請注意，提出刪除要求時，必須指定 ID。其他屬性不會傳遞至服務，或者服務會將它們忽略。`DeleteAsync` 呼叫的結果通常會是 `null`。您可以從 `InsertAsync` 呼叫的結果取得所要傳入的 ID。當您嘗試刪除項目但尚未設定 "Id" 欄位時，後端會傳回 `MobileServiceInvalidOperationException`。

##<a name="#custom-api"></a>作法：呼叫自訂 API

自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

若要呼叫自訂 API，您可以呼叫用戶端上的其中一個 [InvokeApiAsync] 方法多載。例如，下列程式碼字行會傳送 POST 要求至後端上的 **completeAll** API：

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

請注意，這是具類型的方法呼叫，它會要求定義 **MarkAllResult** 傳回類型。具類型的和不具類型的方法皆受支援。這個範例幾乎沒什麼用處，因為它是具類型的、不傳送任何裝載、沒有查詢參數，而且不會變更要求標頭。如需 [InvokeApiAsync] 更實際的範例，以及更完整的討論，請參閱 [Azure 行動服務用戶端 SDK 中的自訂 API] (英文)。

##作法：註冊推播通知

Mobile Apps 用戶端可讓您向 Azure 通知中樞註冊推播通知。註冊時，您會取得從特定平台「推送通知服務 (PNS)」取得的控制代碼。然後您就可以在建立註冊時提供此值以及任何標記。下列程式碼會為您的 Windows 應用程式向 Windows 通知服務 (WNS) 註冊推播通知：

		private async void InitNotificationsAsync()
		{
		    // Request a push notification channel.
		    var channel =
		        await PushNotificationChannelManager
		            .CreatePushNotificationChannelForApplicationAsync();

		    // Register for notifications using the new channel and a tag collection.
			var tags = new List<string>{ "mytag1", "mytag2"};
		    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, tags);
		}

請注意，在此範例中，註冊中會包含兩個標記：如需有關 Windows 應用程式的詳細資訊，請參閱[新增推播通知給您的應用程式](app-service-mobile-windows-store-dotnet-get-started-push.md)。

<!--- Remove until Xamarin.Android push is supported.
Xamarin apps require some additional code to be able to register a Xamarin app running on iOS or Android app with the Apple Push Notification Service (APNS) and Google Cloud Messaging (GCM) services, respectively. For more information see **Add push notifications to your app** ([Xamarin.iOS](partner-xamarin-mobile-services-ios-get-started-push.md#add-push) | [Xamarin.Android](partner-xamarin-mobile-services-android-get-started-push.md#add-push)).

>[AZURE.NOTE]When you need to send notifications to specific registered users, it is important to require authentication before registration, and then verify that the user is authorized to register with a specific tag. For example, you must check to make sure a user doesn't register with a tag that is someone else's user ID. For more information, see [Send push notifications to authenticated users](mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md).
>-->

## 作法：註冊發送範本以傳送跨平台通知

若要註冊範本，只要在用戶端應用程式中使用 **MobileService.GetPush().RegisterAsync()** 方法傳遞範本。

        MobileService.GetPush().RegisterAsync(channel.Uri, newTemplates());

您的範本類型會是 JObject，並且可能包含下列 JSON 格式的多個範本：

        public JObject newTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template="ToastText01"><text id="1">$(message)</text></binding></visual></toast>";
            
            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

方法 **RegisterAsync()** 也接受次要磚：

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

若要利用這些已註冊的範本傳送通知，請使用[通知中樞 API](https://msdn.microsoft.com/library/azure/dn495101.aspx)。

##<a name="optimisticconcurrency"></a>作法：使用開放式並行存取

在部分案例中，兩個或多個用戶端可能會同時對相同項目寫入變更。在沒有偵測到任何衝突的情況下，最後寫入將覆寫任何先前的更新，即使這不是您想要的結果。*開放式並行存取控制項*會假設每筆交易都可以認可，因此不會使用任何資源鎖定。在認可交易之前，開放式並行存取控制項會驗證沒有其他交易已修改此資料。如果資料已修改，則會復原認可的交易。

Mobile Apps 支援開放式並行存取控制項，方法是使用 `__version` 系統屬性資料行來追蹤對每個項目的變更，該資料行是針對行動應用程式後端中的每個資料表所定義的。每當更新記錄時，Mobile Apps 會將該筆記錄的 `__version` 屬性設定為新值。在每次更新要求期間，要求所提供的該筆記錄 `__version` 屬性會與伺服器上該筆記錄的相同屬性進行比對。如果隨著要求傳遞的版本與後端不符，則用戶端程式庫會引發 `MobileServicePreconditionFailedException<T>`。例外狀況所提供的類型是來自包含該筆記錄伺服器版本的後端記錄。接著應用程式可以使用此資訊，來判斷是否要針對後端的正確 `__version` 值來執行更新要求以認可變更。

為了要啟用開放式並行存取，應用程式在 `__version` 系統屬性的資料表類別上定義了資料行。下列定義提供了範例。

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "__version")]
        public byte[] Version { set; get; }
    }


使用不具類型之資料表的應用程式會在資料表的 `SystemProperties` 上設定 `Version` 旗標，以啟用開放式並行存取，如下所示。

	//Enable optimistic concurrency by retrieving __version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;


下列程式碼將示範如何在一偵測到寫入衝突時立即解決。正確的 `__version` 值必須包含在 `UpdateAsync()` 呼叫中，才能認可方案。

	private async void UpdateToDoItem(TodoItem item)
	{
    	MobileServicePreconditionFailedException<TodoItem> exception = null;

	    try
    	{
	        //update at the remote table
    	    await todoTable.UpdateAsync(item);
    	}
    	catch (MobileServicePreconditionFailedException<TodoItem> writeException)
	    {
        	exception = writeException;
	    }

    	if (exception != null)
    	{
			// Conflict detected, the item has changed since the last query
        	// Resolve the conflict between the local and server item
	        await ResolveConflict(item, exception.Item);
    	}
	}


	private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
	{
    	//Ask user to choose the resoltion between versions
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
        	                                        serverItem.Text, localItem.Text),
                                                	"CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the
	        // item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another
        	// change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}

如需詳細資訊，請參閱 [Azure Mobile Apps 中的離線資料同步處理](app-service-mobile-offline-data-sync.md)。


##<a name="binding"></a>作法：將 Mobile Apps 資料繫結至 Windows 使用者介面

本節說明如何在 Windows app 中使用 UI 元素來顯示傳回的資料物件。若要查詢 `todoTable` 中的未完成項目，並將它顯示在非常簡單的清單中，您可以執行下列範例程式碼來將清單來源與查詢繫結。使用 `MobileServiceCollection` 建立 Mobile Apps 感知繫結集合。

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

受管理執行階段中的部分控制項支援名為 [ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916) 的介面。此介面允許控制項在使用者捲動時要求額外資料。通用 Windows 8.1 應用程式可透過 `MobileServiceIncrementalLoadingCollection` 為此介面提供內建支援，以自動處理控制項的呼叫。若要在 Windows 應用程式中使用 `MobileServiceIncrementalLoadingCollection`，請執行下列動作：

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


若要在 Windows Phone 8 和 "Silverlight" 應用程式上使用新的集合，請在 `IMobileServiceTableQuery<T>` 與 `IMobileServiceTable<T>`上使用 `ToCollection` extension method若要實際載入資料，請呼叫 `LoadMoreItemsAsync()`。

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

當您使用藉由呼叫 `ToCollectionAsync` 或 `ToCollection` 來建立的集合時，您會取得可繫結至 UI 控制項的集合。此集合有分頁感知功能，例如，控制項可要求集合「載入更多項目」，集合便會為此控制項執行此動作。此時，控制項會在未涉及使用者程式碼的情況下啟動流程。不過，因為集合會從網路中載入資料，因此載入有時候可能會失敗。若要處理這類失敗，您可以覆寫 `MobileServiceIncrementalLoadingCollection` 上的 `OnException` 方法，以處理呼叫控制項執行的 `LoadMoreItemsAsync` 時，所造成的例外狀況。

最後，想像您的資料表有許多欄位，但您只想要在控制項中顯示其中部分欄位。您可以使用上述[選取特定資料欄](#selecting)一節中的指引，以選取要在 UI 中顯示的特定資料欄。

<!--- We want to just point to the authentication topic when it's done
##<a name="authentication"></a>How to: Authenticate users

Mobile Apps supports authenticating and authorizing app users using a variety of external identity providers: Facebook, Google, Microsoft Account, Twitter, and Azure Active Directory. You can set permissions on tables to restrict access for specific operations to only authenticated users. You can also use the identity of authenticated users to implement authorization rules in server scripts. For more information, see the tutorial [Add authentication to your app].

Two authentication flows are supported: a _server flow_ and a _client flow_. The server flow provides the simplest authentication experience, as it relies on the provider's web authentication interface. The client flow allows for deeper integration with device-specific capabilities as it relies on provider-specific device-specific SDKs.

###Server flow
To have App Service manage the authentication process in your Windows apps,
you must register your app with your identity provider. Then in your mobile App backed, you need to configure the application ID and secret provided by your provider. For more information, see the tutorial [Add authentication to your app].

Once you have registered your identity provider, simply call the [LoginAsync method] with the [MobileServiceAuthenticationProvider] value of your provider. For example, the following code initiates a server flow sign-in by using Facebook.

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
				message =
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

If you are using an identity provider other than Facebook, change the value of [MobileServiceAuthenticationProvider] above to the value for your provider.

In this case, App Service manages the OAuth 2.0 authentication flow by displaying the sign-in page of the selected provider and generating an App Service authentication token after successful sign-on with the identity provider. The [LoginAsync method] returns a [MobileServiceUser], which provides both the [userId] of the authenticated user and the [MobileServiceAuthenticationToken], as a JSON web token (JWT). This token can be cached and re-used until it expires. For more information, see [Caching the authentication token].

###Client flow

Your app can also independently contact the identity provider and then provide the returned token to App Service for authentication. This client flow enables you to provide a single sign-in experience for users or to retrieve additional user data from the identity provider.

####Single sign-in using a token from Facebook or Google

In the most simplified form, you can use the client flow as shown in this snippet for Facebook or Google.

	var token = new JObject();
	// Replace access_token_value with actual value of your access token obtained
	// using the Facebook or Google SDK.
	token.Add("access_token", "access_token_value");

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				// Change MobileServiceAuthenticationProvider.Facebook
				// to MobileServiceAuthenticationProvider.Google if using Google auth.
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message =
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}


####Single sign-in using Microsoft Account with the Live SDK

To be able to authenticate users, you must register your app at the Microsoft account Developer Center. You must then connect this registration with your Mobile App backend. Complete the steps in [Register your app to use a Microsoft account login](mobile-services-how-to-register-microsoft-authentication.md) to create a Microsoft account registration and connect it to your Mobile App backend. If you have both Windows Store and Windows Phone 8/Silverlight versions of your app, register the Windows Store version first.

The following code authenticates using Live SDK and uses the returned token to sign-in to your Mobile App backend. 

	private LiveConnectSession session;
 	//private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
			// The wl.basic scope is requested.
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }


###<a name="caching"></a>Caching the authentication token
In some cases, the call to the login method can be avoided after the first time the user authenticates. You can use [PasswordVault] for Windows Store apps to cache the current user identity the first time they log in and every subsequent time you check whether you already have the user identity in our cache. When the cache is empty, you still need to send the user through the login process.

	// After logging in
	PasswordVault vault = new PasswordVault();
	vault.Add(new PasswordCredential("Facebook", user.UserId, user.MobileServiceAuthenticationToken));

	// Log in
	var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
	if (creds != null)
	{
		user = new MobileServiceUser(creds.UserName);
		user.MobileServiceAuthenticationToken = vault.Retrieve("Facebook", creds.UserName).Password;
	}
	else
	{
		// Regular login flow
		user = new MobileServiceuser( await client
			.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
		var token = new JObject();
		// Replace access_token_value with actual value of your access token
		token.Add("access_token", "access_token_value");
	}

	 // Log out
	client.Logout();
	vault.Remove(vault.Retrieve("Facebook", user.UserId));


For Windows Phone apps, you may encrypt and cache data using the [ProtectedData] class and store sensitive information in isolated storage.

-->

##<a name="errors"></a>作法：處理錯誤

下列範例示範如何處理後端所傳回的例外狀況：

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and App Service has assigned an Id, the item is added to the CollectionView
		try
		{
			await todoTable.InsertAsync(todoItem);
			items.Add(todoItem);
		}
		catch (MobileServiceInvalidOperationException e)
		{
			// Handle error
		}
	}


##<a name="unit-testing"></a>作法：設計單位測試

`MobileServiceClient.GetTable` 所傳回的值和查詢均為介面。這可讓它們在測試用途中更容易被模仿，因此您可以建立 `MyMockTable : IMobileServiceTable<TodoItem>` 來實作您的測試邏輯。

##<a name="customizing"></a>作法：自訂用戶端

本節說明您可以自訂要求標頭，以及在回應中自訂 JSON 物件序列化的方式。

### <a name="headers"></a>作法：自訂要求標頭

若要支援您的特定應用程式案例，您可能需要自訂與行動應用程式後端的通訊。例如，您可能會想要在每封連出要求上新增自訂標頭，或甚至變更回應狀態碼。您可以提供自訂的 [DelegatingHandler] 來進行，如下列範例所示：

    public async Task CallClientWithHandler()
    {
        MobileServiceClient client = new MobileServiceClient(
            "AppUrl", "", "",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage> 
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Add a custom header to the request.
            request.Headers.Add("x-my-header", "my value");
            var response = await base.SendAsync(request, cancellationToken);
            // Set a differnt response status code.
            response.StatusCode = HttpStatusCode.ServiceUnavailable;
            return response;
        }
    }

此程式碼在要求中新增新的 **x-my-header** 標頭，並且任意設定回應代碼為無法使用。在實際案例中，您會根據應用程式所需的一些自訂邏輯來設定回應狀態碼。

### <a name="serialization"></a>作法：自訂序列化

Mobile Apps 用戶端程式庫使用 Json.NET 在用戶端上將 JSON 回應轉換為 .NET 物件。您可以在訊息中設定此序列化在 .NET 類型和 JSON 之間的行為。[MobileServiceClient] 類別會公開 [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm) 類型的 `SerializerSettings` 屬性。

使用此屬性，您可以設定其中一個 Json.NET 屬性，如下所示：

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

此屬性會在序列化期間將所有屬性轉換為小寫。

<!-- Anchors. -->
[篩選傳回的資料]: #filtering
[排序傳回的資料]: #sorting
[以分頁方式傳回資料]: #paging
[選取特定資料欄]: #selecting
[按識別碼查詢資料]: #lookingup

<!-- Images. -->



<!-- URLs. -->
[Add authentication to your app]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[LoginAsync method]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md/#Commands_to_manage_mobile_services
[Optimistic Concurrency Tutorial]: mobile-services-windows-store-dotnet-handle-database-conflicts.md
[MobileServiceClient]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[Azure 行動服務用戶端 SDK 中的自訂 API]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx

<!---HONumber=Nov15_HO4-->