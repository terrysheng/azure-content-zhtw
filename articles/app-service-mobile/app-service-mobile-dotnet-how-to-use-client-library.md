<properties
	pageTitle="使用 App Service Mobile Apps 受管理的用戶端程式庫 (Windows | Xamarin) | Microsoft Azure"
	description="了解如何搭配 Windows 和 Xamarin 應用程式針對 Azure App Service Mobile Apps 使用 .NET 用戶端。"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="glenga"/>

# 如何針對 Azure Mobile Apps 使用受管理的用戶端

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##概觀

本指南將示範如何在 Windows 和 Xamarin 應用程式中針對 Azure App Service Mobile Apps 使用受管理的用戶端程式庫，來執行一般案例。在本指南中，我們會著重於用戶端受管理的 SDK。

## 參考文件

用戶端 SDK 的參考文件位於此處：[Azure Mobile Apps .NET 用戶端參考資料]。

##<a name="setup"></a>設定和必要條件

我們假設您已建立並發佈您的行動應用程式後端專案 (包含在一個資料表中)。在本主題使用的程式碼中，資料表的名稱為 `TodoItem`，且其內容包含下列資料行：`Id`、`Text` 和 `Complete`。

C# 中對應的具類型用戶端類型如下：

	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

請注意，[JsonPropertyAttribute] 是用來定義用戶端類型與資料表之間的 *PropertyName* 對應。



###



##



	MobileServiceClient client = new MobileServiceClient("MOBILE_APP_URL");

在上述程式碼中，以行動應用程式後端 URL 取代 `MOBILE_APP_URL`，這位於 [Azure 入口網站]的行動應用程式後端刀鋒視窗中。

## 

本文涵蓋下列主題：

* [建立資料表參考](#instantiating)
* 
* [篩選傳回的資料](#filtering)
* [排序傳回的資料](#sorting)
* [以分頁方式傳回資料](#paging)
* [選取特定資料欄](#selecting)
* 
* 
* 
* 
* 
* 
* 

###<a name="instantiating"></a>作法：建立資料表參考

存取或修改後端資料表中資料的所有程式碼，都會在 `MobileServiceTable` 物件上呼叫函數。透過在 `MobileServiceClient` 的執行個體上呼叫 [GetTable] 方法可取得資料表的參考，如下所示：

    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();

這是具類型的序列化模型。也支援不具類型的序列化模型。

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

在不具類型的查詢中，您必須指定基礎 OData 查詢字串。

###<a name="querying"></a>如何：查詢行動應用程式中的資料

本節將說明如何對行動應用程式後端發出查詢，包括下列功能：

- [篩選傳回的資料]
- [排序傳回的資料]
- [以分頁方式傳回資料]
- [選取特定資料欄]
- [按識別碼查詢資料]

>[AZURE.NOTE] 系統會強制使用伺服器控制的頁面大小，以防止傳回所有資料列。這可避免預設的大型資料集要求對服務造成負面影響。若要傳回 50 筆以上的資料列，請依照[以分頁方式傳回資料]的說明，使用 `Take` 方法。

###<a name="filtering"></a>作法：篩選傳回的資料

下列程式碼說明如何在查詢中包含 `Where` 子句，以篩選資料。它會從 `todoTable` 傳回其 `Complete` 屬性等於 `false` 的所有項目。[Where] 函數會套用資料列篩選述語來查詢資料表。

	// This query filters out completed TodoItems and items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

您可以使用訊息檢查軟體 (例如瀏覽器開發人員工具或 [Fiddler]) 來檢視傳送至後端的要求 URI。

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1



	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

傳遞至 `Where` 方法的函數可以有任意數目的條件。以下面的程式碼行為例：

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
	   .ToListAsync();



	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

上述 `WHERE` 陳述式將會尋找 `Complete` 狀態設為 false，且 `Text` 不是 Null 的項目。



	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

這兩種方法是相同的，而且可以交換使用。在一個查詢中串連多個述語的舊有選項較為精簡，也是我們給您的建議。

`Where` 子句可支援被轉譯成 OData 子集的作業。這包括關係運算子 (==、!=、<、<=、>、>=)、算術運算子 (+、-、/、*、%)、精確度位數 (Math.Floor、Math.Ceiling)、字串函數 (Length、Substring、Replace、IndexOf、StartsWith、EndsWith)、日期屬性 (Year、Month、Day、Hour、Minute、Second)、物件的存取屬性，及結合上述所有的運算式。

###<a name="sorting"></a>作法：排序傳回的資料

下列程式碼將說明如何透過在查詢中加上 [OrderBy] 或 [OrderByDescending] 函數來排序資料。它會從 `todoTable` 傳回項目，並依據 `Text` 欄位以遞增順序排列。

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

###<a name="paging"></a>作法：以分頁方式傳回資料

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

>[AZURE.NOTE]若要覆寫行動應用程式後端中的 50 個資料列限制，您也必須將 [EnableQueryAttribute] 套用到公用 GET 方法並指定分頁行為。套用到方法時，下列設定最多傳回 1000 個資料列：
>
>    

### <a name="selecting"></a>作法：選取特定資料欄

您可以指定結果中要包含的屬性集，方法是在查詢中加上 [Select] 子句。例如，下列程式碼將示範如何只選取一個欄位以及如何選取及格式化多個欄位：

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

[LookupAsync] 函數可用來查詢具有特定 ID 的資料庫物件。

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="untypedqueries"></a>如何：執行不具類型的查詢



	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

您將收到可用作屬性包的 JSON 值。

### <a name="inserting"></a>如何：將資料插入行動應用程式後端

所有用戶端類型都必須包含名為 **Id** 的成員，其預設為字串。需要有此 **Id** 才能執行 CRUD 作業和離線。參數包含要作為 .NET 物件插入的資料。

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

Mobile Apps 支援資料表的 **id** 資料行使用唯一自訂字串值。這可讓應用程式使用自訂的值，例如電子郵件地址或使用者名稱作為識別碼。

字串識別碼為您提供下列優點：

* 不需要往返存取資料庫就能產生識別碼。
* 輕鬆合併不同資料表或資料庫的記錄。
* 識別碼值與應用程式邏輯的整合更理想。

當插入的記錄上未設定字串識別碼值時，行動應用程式後端會產生唯一值做為識別碼。

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

###<a name="modifying"></a>如何：修改行動應用程式後端中的資料

參數包含要作為 .NET 物件更新的資料。

	await todoTable.UpdateAsync(todoItem);



	JObject jo = new JObject();
	jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

這是後端識別要更新哪個執行個體的方式。

###<a name="deleting"></a>如何：刪除行動應用程式後端中的資料



	await todoTable.DeleteAsync(todoItem);

若要刪除不具類型的資料，您可以充份利用 Json.NET，如下所示：

	JObject jo = new JObject();
	jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

請注意，提出刪除要求時，必須指定 ID。其他屬性不會傳遞至服務，或者服務會將它們忽略。`DeleteAsync` 呼叫的結果通常會是 `null`。您可以從 `InsertAsync` 呼叫的結果取得所要傳入的 ID。

###

在沒有偵測到任何衝突的情況下，最後寫入將覆寫任何先前的更新，即使這不是您想要的結果。「開放式並行存取控制項」會假設每筆交易都可以認可，因此不會使用任何資源鎖定。在認可交易之前，開放式並行存取控制項會驗證沒有其他交易已修改此資料。如果資料已修改，則會復原認可的交易。

Mobile Apps 支援開放式並行存取控制項，方法是使用 `version` 系統屬性資料行來追蹤對每個項目的變更，該資料行是針對行動應用程式後端中的每個資料表所定義的。每當更新記錄時，Mobile Apps 會將該筆記錄的 `version` 屬性設定為新值。在每次更新要求期間，要求所提供的該筆記錄 `\version` 屬性會與伺服器上該筆記錄的相同屬性進行比對。接著應用程式可以使用這項資訊，來決定是否要針對後端的正確 `version` 值來執行更新要求以認可變更。



    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public byte[] Version { set; get; }
    }


使用不具類型之資料表的應用程式會在資料表的 `SystemProperties` 上設定 `Version` 旗標，以啟用開放式並行存取，如下所示。

	//Enable optimistic concurrency by retrieving version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;



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
	    MessageDialog msgDialog = new MessageDialog(
            String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
            serverItem.Text, localItem.Text),
            "CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}



###<a name="binding"></a>如何：將 Mobile Apps 資料繫結至 Windows 使用者介面

本節說明如何在 Windows app 中使用 UI 元素來顯示傳回的資料物件。

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

受管理執行階段中的部分控制項支援名為 [ISupportIncrementalLoading] 的介面。此介面允許控制項在使用者捲動時要求額外資料。若要在 Windows 應用程式中使用 `MobileServiceIncrementalLoadingCollection`，請執行下列動作：

    MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
    items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

    ListBox lb = new ListBox();
    lb.ItemsSource = items;

若要在 Windows Phone 8 和 「Silverlight」應用程式上使用新的集合，請在 `IMobileServiceTableQuery<T>` 與 `IMobileServiceTable<T>` 上使用 `ToCollection` 擴充方法。若要實際載入資料，請呼叫 `LoadMoreItemsAsync()`。

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

當您使用藉由呼叫 `ToCollectionAsync` 或 `ToCollection` 來建立的集合時，您會取得可繫結至 UI 控制項的集合。此集合有分頁感知功能，例如，控制項可要求集合「載入更多項目」，集合便會為此控制項執行此動作。此時，控制項會在未涉及使用者程式碼的情況下啟動流程。不過，因為集合會從網路中載入資料，因此載入有時候可能會失敗。若要處理這類失敗，您可以覆寫 `MobileServiceIncrementalLoadingCollection` 上的 `OnException` 方法，以處理呼叫控制項執行的 `LoadMoreItemsAsync` 時，所造成的例外狀況。

最後，想像您的資料表有許多欄位，但您只想要在控制項中顯示其中部分欄位。您可以使用上述[選取特定資料欄](#selecting)一節中的指引，以選取要在 UI 中顯示的特定資料欄。

##

自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

若要呼叫自訂 API，您可以呼叫用戶端上的其中一個 [InvokeApiAsync] 方法多載。例如，下列程式碼行會將 POST 要求傳送至後端的 **completeAll** API：

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

請注意，這是具類型的方法呼叫，它會要求定義 **MarkAllResult** 傳回類型。具類型的和不具類型的方法皆受支援。


##

您可以在資料表上設定權限，以限制僅有通過驗證使用者可以存取特定操作。您也可以使用通過驗證使用者的身分識別來實作伺服器指令碼中的授權規則。如需詳細資訊，請參閱[將驗證新增至您的應用程式]教學課程。

支援兩個驗證流程：_伺服器流程_和_用戶端流程_。由於伺服器流程採用提供者的 Web 驗證介面，因此所提供的驗證體驗也最為簡單。因為用戶端流程採用提供者特定的裝置特定 SDK，因此可允許與裝置特定功能的深入整合。



###
例如，下列程式碼將透過使用 Facebook 來初始化伺服器流程登入。

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

如果您打算使用除了 Facebook 以外的識別提供者，請將上方的 [MobileServiceAuthenticationProvider] 值變更成您提供者。

您可以快取並重複使用此權杖，直到它到期為止。如需詳細資訊，請參閱[快取驗證權杖](#caching)。

###用戶端流程

此用戶端流程可讓您為使用者提供單一登入體驗，或從識別提供者擷取其他使用者資料。

####使用來自 Facebook 或 Google 的權杖單一登入

您可以最簡化形式來使用用戶端流程，如下列 Facebook 或 Google 的程式碼片段中所示。

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
				user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message = string.Format("You are now logged in - {0}", user.UserId);
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

####使用含有 Live SDK 的 Microsoft 帳戶單一登入

若要能夠驗證使用者，您必須在 Microsoft 帳戶開發人員中心註冊您的應用程式。如果您的 app 同時有 Windows 市集與 Windows Phone 8/Silverlight 版本，請先註冊 Windows 市集版本。



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
			// The wl.basic scope should always be requested.  Other scopes can be added
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



###<a name="caching"></a>快取驗證權杖
在某些情況下，在使用者首次驗證之後就可以避免呼叫登入方法。您可以使用 [PasswordVault]，Windows 市集應用程式便可在目前使用者首次登入時快取其使用者識別，之後您可以每次在快取中查看是否已有此使用者識別存在。當沒有快取時，您仍需透過登入程序傳送使用者。

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


若是 Windows Phone 應用程式，您可以使用 [ProtectedData] 類別來加密與快取資料，並在獨立的儲存體中儲存敏感資訊。

-->

### 

您可以使用 Active Directory Authentication Library (ADAL)，利用 Azure Active Directory 將使用者登入應用程式。

1. 請務必完成註冊原生用戶端應用程式的選擇性步驟。

2. 搜尋時，包含發行前版本。

3. 根據您使用的平台，將下列程式碼新增至您的應用程式。在每個程式碼中，進行下列取代：

* 

* 

* 

* 此值應與 \__https://contoso.azurewebsites.net/.auth/login/done_ 類似。

每個平台所需的程式碼如下：

**Windows：**

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
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

**Xamarin.iOS**

    private MobileServiceUser user;
    private async Task AuthenticateAsync(UIViewController view)
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }

**Xamarin.Android**

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
    }
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }

##



* 
* 
* 

###

Mobile Apps 用戶端可讓您向 Azure 通知中樞註冊推播通知。註冊時，您會取得從特定平台「推送通知服務 (PNS)」取得的控制代碼。然後您就可以在建立註冊時提供此值以及任何標記。下列程式碼會為您的 Windows 應用程式向 Windows 通知服務 (WNS) 註冊推播通知：

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

請注意，在此範例中，註冊中會包含兩個標記：如需 Windows 應用程式的詳細資訊，包括如何註冊範本，請參閱[將推播通知新增至您的應用程式]。



###<a name="package-sid"></a>如何：取得 Windows 市集封裝 SID



若要取得這個值：

1. 在 Visual Studio 方案總管中，以滑鼠右鍵按一下 Windows 市集應用程式專案，然後按一下 [市集] > [將應用程式與市集建立關聯...]。
2. 在精靈中按一下 [下一步]，使用 Microsoft 帳戶登入，在 [保留新的應用程式名稱] 中輸入您應用程式的名稱，然後按一下 [保留]。
3. 成功建立應用程式註冊之後，選取新的應用程式名稱，按一下 [下一步]，然後按一下 [關聯]。這會將所需的 Windows 市集註冊資訊新增至應用程式資訊清單。
4. 使用您的 Microsoft 帳戶登入 [Windows 開發人員中心]。在 [我的應用程式] 底下，按一下您剛才建立的應用程式註冊。
5. 按一下 [應用程式管理] > [應用程式身分識別]，然後向下捲動找到您的 [封裝 SID]。

許多使用封裝 SID 的情況會將其視為 URI，在這種情況下，您必須使用 _ms-app://_ 作為配置。記下您封裝 SID 的版本，封裝 SID 是由串連這個值作為首碼所形成。

Xamarin 應用程式需要一些額外的程式碼，才能將執行於 iOS 或 Android 應用程式上的應用程式，個別與 Apple Push Notification Service (APNS) 和 Google 雲端通訊 (GCM) 服務註冊。

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

###<a name="register-xplat"></a>作法：註冊推送範本以傳送跨平台通知



        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

您的範本類型會是 JObject，並且可能包含下列 JSON 格式的多個範本：

        public JObject myTemplates()
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

請注意，所有的標記都將因安全性而移除。若要將標記加入安裝或安裝的範本中，請參閱[使用適用於 Azure Mobile Apps 的 .NET 後端伺服器 SDK]。



##

###<a name="errors"></a>作法：處理錯誤



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



###<a name="headers"></a>作法：自訂要求標頭

若要支援您的特定應用程式案例，您可能需要自訂與行動應用程式後端的通訊。例如，您可能會想要在每封連出要求上新增自訂標頭，或甚至變更回應狀態碼。您可以提供自訂的 [DelegatingHandler] 來進行，如下列範例所示：

    public async Task CallClientWithHandler()
    {
        HttpResponseMessage[]
        MobileServiceClient client = new MobileServiceClient("AppUrl",
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
            // Change the request-side here based on the HttpRequestMessage
            request.Headers.Add("x-my-header", "my value");

            // Do the request
            var response = await base.SendAsync(request, cancellationToken);

            // Change the response-side here based on the HttpResponseMessage

            // Return the modified response
            return response;
        }
    }


<!-- Anchors. -->
[篩選傳回的資料]: #filtering
[排序傳回的資料]: #sorting
[以分頁方式傳回資料]: #paging
[選取特定資料欄]: #selecting
[按識別碼查詢資料]: #lookingup

<!-- Images. -->

<!-- Internal URLs. -->
[Azure Mobile Apps quickstart]: app-service-mobile-windows-store-dotnet-get-started.md
[將驗證新增至您的應用程式]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Work with .NET backend SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[使用適用於 Azure Mobile Apps 的 .NET 後端伺服器 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[How to use the Node.js backend SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[How to: Define a table controller]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[Define Tables using a Dynamic Schema]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Offline Data Sync in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[將推播通知新增至您的應用程式]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: app-service-mobile-how-to-configure-microsoft-authentication.md
[How to configure App Service for Active Directory login]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[Azure Mobile Apps .NET 用戶端參考資料]: https://msdn.microsoft.com/zh-TW/library/azure/mt419521(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/zh-TW/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx
[MobileServiceCollection]: https://msdn.microsoft.com/zh-TW/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/zh-TW/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/zh-TW/library/azure/jj554275(v=azure.10).aspx
[creates a reference to an untyped table]: https://msdn.microsoft.com/zh-TW/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/zh-TW/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/zh-TW/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/zh-TW/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/zh-TW/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/zh-TW/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/zh-TW/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/zh-TW/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/zh-TW/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/zh-TW/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/zh-TW/library/azure/dn250574(v=azure.10).aspx
[Select]: https://msdn.microsoft.com/zh-TW/library/azure/dn250569(v=azure.10).aspx
[Skip]: https://msdn.microsoft.com/zh-TW/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/zh-TW/library/azure/dn250536.(v=azure.10)aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/zh-TW/library/azure/dn250579(v=azure.10).aspx
[Azure 入口網站]: https://portal.azure.com/
[Azure Classic portal]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/zh-TW/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows 開發人員中心]: https://dev.windows.com/zh-TW/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Windows Live SDK]: https://msdn.microsoft.com/zh-TW/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notification Hubs APIs]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Mobile Apps Files Sample]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63
[Azure-Samples GitHub Repository]: https://github.com/Azure-Samples

<!-- External URLs -->
[JsonPropertyAttribute]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[OData v3 Documentation]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[SymbolSource]: http://www.symbolsource.org/
[SymbolSource instructions]: http://www.symbolsource.org/Public/Wiki/Using

<!---HONumber=AcomDC_0316_2016-->