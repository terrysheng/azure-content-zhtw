<properties linkid="obile-services-how-to-dotnet-client" urlDisplayName=".NET Client Library" pageTitle="Working with the Mobile Services .NET Client Library" metaKeywords="Azure Mobile Services, Mobile Service .NET client, .NET client" description="Learn how to use an .NET client for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use a .NET client for Azure Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

如何使用適用於 Azure 行動服務的 .NET 用戶端
===========================================

[.NET Framework](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/ ".NET Framework") [HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/ "HTML/JavaScript")[iOS](/en-us/develop/mobile/how-to-guides/work-with-ios-client-library/ "iOS")[Android](/en-us/develop/mobile/how-to-guides/work-with-android-client-library/ "Android")[Xamarin](/en-us/develop/mobile/how-to-guides/work-with-xamarin-client-library/ "Xamarin")

本指南將示範如何在 Windows 市集應用程式和 Windows Phone 應用程式中，使用適用於 Azure 行動服務的 .NET 用戶端來執行常見案例。涵蓋的案例包括查詢資料、插入、更新和刪除資料、驗證使用者，以及處理錯誤。如果您不熟悉行動服務，您應考慮首先完成「行動服務快速入門」教學課程 ([Windows 市集快速入門教學課程](http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started/)/[Windows Phone 快速入門教學課程](http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-wp8/)) 和「開始使用 .NET 中的資料」教學課程 ([Windows 市集資料教學課程](http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-data-dotnet/)/[Windows Phone 資料教學課程](http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-data-wp8/))。快速入門教學課程需要 [Mobile Services SDK](http://nuget.org/packages/WindowsAzure.MobileServices/)，來協助您設定帳戶並建立第一個行動服務。

目錄
----

-   [什麼是行動服務](#what-is)
-   [概念](#concepts)
-   [作法：建立行動服務用戶端](#create-client)
-   [作法：建立資料表參考](#instantiating)
-   [作法：從行動服務查詢資料](#querying)
    -   [篩選傳回的資料](#filtering)
    -   [排序傳回的資料](#sorting)
    -   [以分頁方式傳回資料](#paging)
    -   [選取特定資料欄](#selecting)
    -   [依識別碼查詢資料](#lookingup)
-   [作法：將資料插入行動服務](#inserting)
-   [作法：修改行動服務中的資料](#modifying)
-   [作法：刪除行動服務中的資料](#deleting)
-   [作法：使用開放式並行存取](#optimisticconcurrency)
-   [作法：將資料繫結到行動服務中的使用者介面](#binding)
-   [作法：驗證使用者](#authentication)
-   [作法：處理錯誤](#errors)
-   [作法：使用不具類型的資料](#untyped)
-   [作法：設計單位測試](#unit-testing)
-   [作法：自訂用戶端](#customizing)
    -   [自訂要求標頭](#headers)
    -   [自訂序列化](#serialization)
-   [後續步驟](#nextsteps)

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

設定設定和必要條件
------------------

我們假設您已建立行動服務和資料表。如需詳細資訊，請參閱[建立資料表](http://go.microsoft.com/fwlink/?LinkId=298592)。在本主題使用的程式碼中，資料表的名稱為 `TodoItem`，且其內容包含下列資料欄：`Id`、`Text` 和 `Complete`。

其對應的具類型用戶端 .NET 類型如下：

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

啟用動態結構描述時，Azure 行動服務會根據插入或更新要求中的物件自動產生新欄位。如需詳細資訊，請參閱[動態結構描述](http://go.microsoft.com/fwlink/?LinkId=296271)。

建立行動服務用戶端作法：建立行動服務用戶端
------------------------------------------

下列程式碼將建立用來存取行動服務的 `MobileServiceClient` 物件。

    MobileServiceClient client = new MobileServiceClient( 
        "AppUrl", 
        "AppKey" 
    ); 

在上面的程式碼中，使用行動服務 URL 和應用程式金鑰 (依此順序) 來取代 `AppUrl` 和 `AppKey`。您可在 Azure 管理入口網站上找到這兩項資訊，方法是選取您的行動服務，然後按一下 [儀表板]。

建立資料表參考作法：建立資料表參考
----------------------------------

可存取或修改行動服務資料表中資料的所有程式碼會呼叫 `MobileServiceTable` 物件上的函數。您可透過呼叫 `MobileServiceClient` 執行個體上的 [GetTable](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj554275.aspx) 函數，來取得資料表的參考。

    IMobileServiceTable<TodoItem> todoTable = 
        client.GetTable<TodoItem>();

此為具類型的序列化模型，請參閱上面的[不具類型的序列化模型](#untyped)討論。

查詢資料作法：從行動服務查詢資料
--------------------------------

本節將說明如何對行動服務發出查詢。小節將說明例如排序、篩選及分頁等其他方面。

> [WACOM.NOTE] 依預設，伺服器導向頁面大小可用來防止傳回所有資料列。如此可避免大型資料集的預設要求對服務造成負面影響。若要傳回 50 筆以上的資料列，請使用 `Take` 方法，如[以分頁方式傳回資料](#paging)中所述。

### 作法：篩選傳回的資料

下列程式碼將說明如何在查詢中包含 `Where` 子句來篩選資料。它會傳回其 `Complete` 屬性等於 `false` 的所有 `todoTable` 項目。`Where` 函數會套用資料列篩選述語來查詢資料表。

    // This query filters out completed TodoItems and 
    // items without a timestamp. 
    List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();

您可以使用訊息檢查軟體，例如瀏覽器開發人員工具或 Fiddler，以檢視傳送至行動服務的要求的 URI。如果您查看下面的要求 URI，您會注意到我們打算修改查詢字串本身：

    GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1                   

此要求在伺服器端通常大致上會轉譯成下列 SQL 查詢：

    SELECT * 
    FROM TodoItem           
    WHERE ISNULL(complete, 0) = 0

傳遞至 `Where` 方法的函數可以有任意數目的條件。以下面的程式碼行為例：

    // This query filters out completed TodoItems where Text isn't null
    List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false
           && todoItem.Text != null)
    .ToListAsync();

大致上會轉譯成 (針對先前所示的相同要求)

    SELECT * 
    FROM TodoItem 
    WHERE ISNULL(complete, 0) = 0
    AND ISNULL(text, 0) = 0

上面的 `where` 陳述式將找到 `Complete` 狀態設為 false，且 `Text` 不是 Null 的項目。

我們也可以將它改寫成多行程式碼：

    List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();

這兩種方法是相同的，而且可以交換使用。在一個查詢中串連多個述語的舊有選項較為精簡，也是我們給您的建議。

`where` 子句支援會被轉譯成行動服務 OData 子集的操作。這包括關係運算子 (==、!=、&lt;、&lt;=、\>、\>=)、算術運算子 (+、-、/、\*、%)、精確度位數 (Math.Floor、Math.Ceiling)、字串函數 (Length、Substring、Replace、IndexOf、StartsWith、EndsWith)、日期屬性 (Year、Month、Day、Hour、Minute、Second)、物件的存取屬性，及結合上述所有的運算式。

### 作法：排序傳回的資料

下列程式碼將說明如何透過在查詢中加上 `OrderBy` 或 `OrderByDescending` 函數來排序資料。它會從 `todoTable` 中傳回按 `Text` 欄位遞增排序的項目。

    // Sort items in ascending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)       
	List items = await query.ToListAsync();

    // Sort items in descending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)       
	List items = await query.ToListAsync();

### 作法：以分頁方式傳回資料

依預設，伺服器僅會傳回前 50 筆資料列。您可以提高傳回的資料列數，方法是呼叫 [Take](http://msdn.microsoft.com/zh-tw/library/windowsazure/dn250574.aspx) 方法。使用 `Take` 以及 [Skip](http://msdn.microsoft.com/zh-tw/library/windowsazure/dn250573.aspx) (英文頁面) 方法來要求查詢所傳回總資料集的特定頁面。執行下列查詢時，會傳回資料表中的前三個項目。

    // Define a filtered query that returns the top 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Take(3);                              
    List<TodoItem> items = await query.ToListAsync();

下列修訂過的查詢會略過前三個結果，傳回之後的三個結果。實際上這就是第二「頁」資料，頁面大小為三個項目。

    // Define a filtered query that skips the top 3 items and returns the next 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Skip(3)
                    .Take(3);                              
    List<TodoItem> items = await query.ToListAsync();

您也可以使用 [IncludeTotalCount](http://msdn.microsoft.com/zh-tw/library/windowsazure/dn250560.aspx) (英文) 方法，確保查詢會忽略指定的任何採取分頁/限制子句，而取得已傳回 *all* 記錄的總數：

    query = query.IncludeTotalCount();

這是傳遞硬式編碼分頁值至 `Take` 和 `Skip` 方法的簡化案例。在實際的應用程式中，您可以搭配頁面巡覽區控制項或類似的 UI 來使用類似上述的查詢，讓使用者導覽至上一頁和下一頁。

### 作法：選取特定資料欄

您可以指定結果中要包含的屬性集，方法是在查詢中加上 `Select` 子句。例如，下列程式碼將示範如何只選取一個欄位以及如何選取及格式化多個欄位：

    // Select one field -- just the Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => todoItem.Text);
    List<string> items = await query.ToListAsync();

    // Select multiple fields -- both Complete and Text info
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!": "Incomplete!"));
    List<string> items = await query.ToListAsync();

目前為止說明的所有函數只是不斷添加，只要一直呼叫即可，我們每一次會對查詢做更多變化。再舉一個例子：

    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Where(todoItem => todoItem.Complete == false)
                    .Select(todoItem => todoItem.Text)
                    .Skip(3).
                    .Take(3);
    List<string> items = await query.ToListAsync();

### 作法：依識別碼查詢資料

`LookupAsync` 函數可用來查詢具有特定 ID 的資料庫物件。

    // This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

插入資料作法：將資料插入行動服務
--------------------------------

**注意**

如果您想要執行某類型的插入、查詢、刪除或更新操作，則您必須建立名為 **Id** 的成員。這是此範例類別 **TodoItem** 具有成員名稱為 **Id** 的原因。有效的 ID 值必須存在於更新與刪除操作中。

下列程式碼將說明如何將新的資料列插入資料表中。參數包含要作為 .NET 物件插入的資料。

    await todoTable.InsertAsync(todoItem);

如果唯一自訂 id 值不包含在傳遞至 `todoTable.InsertAsync` 呼叫的 `todoItem` 中，則傳回用戶端的 `todoItem` 物件所設定的伺服器將會產生一個 ID 值。

行動服務支援以唯一的自訂字串值做為資料表識別碼。這可讓應用程式在行動服務資料表的識別碼資料行中使用自訂的值，例如電子郵件地址或使用者名稱。如果將新記錄插入至資料表時未提供字串識別碼值，則行動服務會產生唯一值做為識別碼。

支援字串識別碼對開發人員而言有下列好處

-   不需要往返存取資料庫就能產生識別碼。
-   輕鬆合併不同資料表或資料庫的記錄。
-   識別碼值與應用程式邏輯的整合更理想。

您也可以使用伺服器指令碼來設定識別碼值。下列指令碼範例會產生自訂 GUID 並指派給新記錄的識別碼。這類似於您未傳入任何值做為記錄的識別碼時由行動服務產生的識別碼值。

    //Example of generating an id. This is not required since Mobile Services
    //will generate an id if one is not passed in.
    item.id = item.id || newGuid();
    request.execute();

    function newGuid() {
        var pad4 = function(str) { return "0000".substring(str.length) + str; };
        var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
        return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
    }

如果應用程式提供識別碼的值，則行動服務會原封不動地儲存它。這包括開頭或結尾的空白字元。值中的空白字元將不會被去除。

`id` 的值必須是唯一的，且不可包含下列字元集的字元：

-   控制字元：[0x0000-0x001F] 和 [0x007F-0x009F]。如需詳細資訊，請參閱 [ASCII 控制碼 C0 和 C1](http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set) (英文)。
-   可以列印的字元：**"**(0x0022)、**+** (0x002B)、**/** (0x002F)、**?**(0x003F)、**\\** (0x005C)、**\`** (0x0060)
-   識別碼 "." 和 ".."

另外，您也可以在資料表中使用整數識別碼。若要使用整數識別碼，您必須使用 `--integerId` 命令並指定 `mobile table create` 選項來建立資料表。此命令需要在 Azure 的命令列介面 (CLI) 中執行。如需關於使用 CLI 的詳細資訊，請參閱[使用 CLI 管理行動服務資料表](http://www.windowsazure.com/zh-tw/manage/linux/other-resources/command-line-tools/#Mobile_Tables) (英文)。

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

修改資料作法：修改行動服務中的資料
----------------------------------

下列程式碼將說明如何使用含有新資訊的相同 ID 來更新現有的執行個體。參數包含要作為 .NET 物件更新的資料。

    await todoTable.UpdateAsync(todoItem);

若要插入不具類型的資料，您可以充份利用 Json.NET，如下所示。請注意，進行更新時必須指定 ID，因為那是行動服務識別要更新哪個執行個體的方式。您可以從 `InsertAsync` 呼叫的結果中取得此 ID。

    JObject jo = new JObject(); 
    jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World"); 
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

如果您嘗試在沒有提供 [Id] 值的情況下更新項目，則服務沒有辦法分辨要更新哪個執行個體，因此 Mobile Services SDK 將會發出 `ArgumentException`。

刪除資料作法：刪除行動服務中的資料
----------------------------------

下列程式碼將說明如何刪除現有的執行個體。您可以透過 `todoItem` 上所設定的 [Id] 欄位來識別執行個體。

    await todoTable.DeleteAsync(todoItem);

若要刪除不具類型的資料，您可以充份利用 Json.NET，如下所示。請注意，進行刪除要求時必須指定 ID，因為那是行動服務識別要刪除哪個執行個體的方式。刪除要求只需要 ID 即可；其他屬性不會傳遞至服務，如果有傳遞其他屬性，服務也會將他們忽略。`DeleteAsync` 呼叫的結果通常也會是 `null`。您可以從 `InsertAsync` 呼叫的結果中取得輸入的 ID。

    JObject jo = new JObject(); 
    jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

如果您嘗試在未設定 [Id] 欄位的情況下刪除項目，則服務沒有辦法分辨要刪除哪個執行個體，因此您將收到來自服務的 `MobileServiceInvalidOperationException`。同樣地，如果您嘗試在未設定 [Id] 欄位的情況下刪除不具類型的項目，您將會再次收到來自服務的 `MobileServiceInvalidOperationException`。

開放式並行存取作法：使用開放式並行存取
--------------------------------------

在部分案例中，兩個或多個用戶端可能會同時對相同項目寫入變更。在沒有偵測到任何衝突的情況下，最後寫入將覆寫任何先前的更新，即使這不是您想要的結果。開放式並行存取控制項會假設每筆交易都可以認可，因此不會使用任何資源鎖定。在認可交易之前，開放式並行存取控制項會驗證沒有其他交易已修改此資料。如果資料已修改，則會復原認可的交易。

行動服務支援開放式並行存取控制項，方法是使用 `__version` 系統屬性資料欄來追蹤對每個項目的變更，該資料欄是針對行動服務建立的每個資料表所定義的。每當更新記錄時，行動服務會將該筆記錄的 `__version` 屬性設定為新值。在每次更新要求期間，要求所提供的該筆記錄 `__version` 屬性會與伺服器上該筆記錄的相同屬性進行比對。如果隨著要求傳遞的版本與伺服器不符，則行動服務 .NET 用戶端程式庫會發出 `MobileServicePreconditionFailedException<T>`。例外狀況所提供的類型是來自包含該筆記錄伺服器版本的伺服器記錄。接著應用程式可以使用此資訊，來判斷是否要針對伺服器的正確 `__version` 值來執行更新要求以認可變更。

為了要啟用開放式並行存取，應用程式在 `__version` 系統屬性的資料表類別上定義了資料欄。下列定義提供了範例。

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

使用不具類型的資料表的應用程式可啟用開放式並行存取，方法是將資料表 `SystemProperties` 上的 `Version` 旗標設定如下：

    //Enable optimistic concurrency by retrieving __version 
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

下列程式碼將示範如何在一偵測到寫入衝突時立即解決。正確的 `__version` 值必須包含在 `UpdateAsync()` 呼叫中才能認可決定。

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
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n", 
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

如需為行動服務使用開放式並型存取的完整範例，請參閱[開放式並型存取教學課程](http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/) (英文)。

顯示資料作法：將資料繫結到行動服務中的使用者介面
------------------------------------------------

本節說明如何使用 UI 元素來顯示傳回的資料物件。若要查詢 `todoTable` 中的未完成項目，並將它顯示在非常簡單的清單中，您可以執行下列範例程式碼來將清單來源與查詢繫結。使用 `MobileServiceCollection` 建立行動服務感知繫結集合。

    // This query filters out completed TodoItems. 
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

    // itemsControl is an IEnumerable that could be bound to a UI list control
    IEnumerable itemsControl  = items;            
            
    // Bind this to a ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Windows 執行階段中的部分控制項支援名為 [ISupportIncrementalLoading](http://msdn.microsoft.com/zh-tw/library/windows/apps/Hh701916) 的介面。此介面允許控制項在使用者捲動時要求額外資料。Windows 市集應用程式可透過自動處理控制項呼叫的 `MobileServiceIncrementalLoadingCollection` 內建支援此介面。若要在 Windows 市集應用程式中使用 `MobileServiceIncrementalLoadingCollection`，請執行下列程式碼：

         MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
        items =  todoTable.Where(todoItem => todoItem.Complete == false)
                    .ToIncrementalLoadingCollection();

        ListBox lb = new ListBox();
        lb.ItemsSource = items;

若要在 Windows Phone 上使用新的集合，請在 `IMobileServiceTableQuery<T>` 和 `IMobileServiceTable<T>` 上使用 `ToCollection` 擴充功能方法。若要實際載入資料，請呼叫 `LoadMoreItemsAsync()`。

    MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection(); 
    await items.LoadMoreItemsAsync();         

使用透過呼叫 `ToCollectionAsync` 或 `ToCollection` 所建立的集合時，您會取得可繫結至 UI 控制項的集合。此集合有分頁感知功能，例如，控制項可要求集合「載入更多項目」，集合便會為此控制項執行此動作。此時，控制項會在未涉及使用者程式碼的情況下啟動流程。不過，因為集合會從網路中載入資料，因此載入有時候可能會失敗。若要處理此類失敗，您可以覆寫 `MobileServiceIncrementalLoadingCollection` 上的 `OnException` 方法，以便處理呼叫控制項所執行的 `LoadMoreItemsAsync` 時所造成的例外狀況。

最後，想像您的資料表有許多欄位，但您只想要在控制項中顯示其中部分欄位。您可以使用上述＜[選取特定資料欄](#selecting)＞一節中的指引，以選取要在 UI 中顯示的特定資料欄。

驗證作法：驗證使用者
--------------------

行動服務支援使用各種外部身分識別提供者來驗證和授權應用程式使用者：Facebook、Google、Microsoft 帳戶、Twitter 和 Azure Active Directory。您可以在資料表上設定權限，以限制只有已驗證的使用者才能存取特定的作業。您也可以在伺服器指令碼中，使用驗證的使用者的身分識別來實作授權規則。如需詳細資訊，請參閱「開始使用驗證」教學課程 ([Windows Store](http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-users-dotnet/) (英文)/[Windows Phone](http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-users-wp8/) (英文))

支援兩種驗證流程：*伺服器流程*和*用戶端流程*。由於伺服器流程採用提供者的 Web 驗證介面，因此所提供的驗證體驗也最為簡單。因為用戶端流程採用提供者特定的裝置特定 SDK，因此可允許與裝置特定功能的深入整合。

### 伺服器流程

若要讓行動服務管理 Windows 市集或 Windows Phone 應用程式中的驗證程序，您必須向識別提供者註冊您的應用程式。然後，在行動服務中，您需要設定提供者所提供的應用程式識別碼和密碼。如需詳細資訊，請參閱「開始使用驗證」教學課程 ([Windows Store](http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-users-dotnet/)/[Windows Phone](http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-users-wp8/))。

在註冊識別提供者之後，您只需使用提供者的 [MobileServiceAuthenticationProvider](http://msdn.microsoft.com/zh-tw/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx) 值來呼叫 [LoginAsync 方法](http://msdn.microsoft.com/zh-tw/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx)即可。例如，下列程式碼將透過使用 Facebook 來初始化伺服器流程登入。

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

如果您打算使用除了 Facebook 以外的識別提供者，請將上方的 [MobileServiceAuthenticationProvider](http://msdn.microsoft.com/zh-tw/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx) 值變更成您提供者。

在此案例中，行動服務會管理 OAuth 2.0 驗證流程，首先會顯示選取的提供者的登入頁面，然後在以身分識別提供者成功登入之後，產生行動服務驗證權杖。[LoginAsync 方法](http://msdn.microsoft.com/zh-tw/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx) 會傳回 [MobileServiceUser](http://msdn.microsoft.com/zh-tw/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx)，並提供通過驗證使用者的 [userId](http://msdn.microsoft.com/zh-tw/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx) 和 [MobileServiceAuthenticationToken](http://msdn.microsoft.com/zh-tw/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx)，以作為 JSON Web 權杖 (JWT)。您可以快取並重複使用此權杖，直到它到期為止。如需詳細資訊，請參閱[快取驗證權杖](#caching)。

**Windows 市集應用程式**

使用 Microsoft 帳戶登入提供者來驗證 Windows 市集應用程式的使用者時，也應該向行動服務註冊應用程式封裝。向行動服務註冊 Windows 市集應用程式封裝資訊之後，用戶端就能夠重複使用 Microsoft 帳戶登入認證來享受單一登入的方便性。如果您沒有執行此動作，Microsoft 帳戶登入使用者會在每次呼叫登入方法時j都會看到登入提示。若要了解如何註冊 Windows 市集應用程式封裝，請參閱[註冊 Windows 市集應用程式封裝以進行 Microsoft 驗證](/en-us/develop/mobile/how-to-guides/register-windows-store-app-package/)。在向行動服務註冊封裝資訊後，您便可以透過為 *useSingleSignOn* 參數提供 **true** 值以重複使用認證的方式呼叫 [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594) 方法。

### 用戶端流程

應用程式也可以個別連絡身分識別提供者，然後將傳回的權杖提供給行動服務來進行驗證。此用戶端流程可讓您為使用者提供單一登入的體驗，或從身分識別提供者擷取其他使用者資料。

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

如果使用 Microsoft 帳戶，請依如下所示登入：

    // Replace authentication_token_value with actual value of your Microsoft authentication token obtained through the Live SDK
    user = await client
        .LoginWithMicrosoftAccountAsync(authentication_token_value);

如需如何使用 Microsoft 帳戶提供單一登入體驗的範例，請參閱「使用單一登入驗證應用程式」教學課程 ([Windows 市集](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8/))。

### 快取驗證權杖

在某些情況下，可在使用者第一次驗證之後避免呼叫 login 方法。您可以使用 [PasswordVault](http://msdn.microsoft.com/zh-tw/library/windows/apps/windows.security.credentials.passwordvault.aspx)，Windows 市集應用程式便可在目前使用者首次登入時快取其使用者識別，之後您可以每次在快取中查看是否已有此使用者識別存在。當沒有快取時，您仍需透過登入程序傳送使用者。

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

若是 Windows Phone 應用程式，您可以使用 [ProtectedData](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.protecteddata%28VS.95%29.aspx) 類別來加密與快取資料，並在獨立的儲存體中儲存敏感資訊。

錯誤處理作法：處理錯誤
----------------------

在行動服務中，經常會遭遇、驗證和解決錯誤。

例如，您可在行動服務中註冊伺服器指令碼，並使用該指令碼來針對插入和更新資料執行各種操作，包括驗證與資料修改。假設定義和註冊一段伺服器指令碼來驗證和修改資料，如下所示：

    function insert(item, user, request) 
    {
    if (item.text.length > 10) {
          request.respond(statusCodes.BAD_REQUEST, { error:"Text cannot exceed 10 characters" });
    } else {
          request.execute();
       }
    }

此伺服器端指令碼會驗證傳送至行動服務的字串資料長度，並拒絕太長的字串，在此案例中是超過 10 個字元。

現在，行動服務正在伺服器端上驗證資料並傳送錯誤回應，您可以更新您的 .NET 應用程式以便處理驗證的錯誤回應。

    private async void InsertTodoItem(TodoItem todoItem)
    {
        // This code inserts a new TodoItem into the database.When the operation completes
        // and Mobile Services has assigned an Id, the item is added to the CollectionView
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

使用不具類型的資料作法：使用不具類型的資料
------------------------------------------

.NET 用戶端是專為強型別案例所設計的。不過，較弱型別體驗有時候非常方便；例如，當處理具有開放式結構描述的物件時便是如此。已依下列方式啟用該案例。在查詢中，您可以放棄 LINQ 並使用電傳格式。

    // Get an untyped table reference
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");           

    // Lookup untyped data using OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

您將收到可用作屬性包的 JSON 值。如需有關 JToken 和 Json.NET 的詳細資訊，請參閱 [Json.NET](http://json.codeplex.com/)

設計測試作法：設計單位測試
--------------------------

`MobileServiceClient.GetTable` 所傳回的值和查詢均為介面。這讓他們在測試用途中可以更容易被模仿，因此您可以建立 `MyMockTable :IMobileServiceTable<TodoItem>` 以實作您的測試邏輯。

自訂用戶端作法：自訂用戶端
--------------------------

### 作法：自訂要求標頭

您可能會想要在每封連出要求上附加自訂標頭，或變更回應狀態碼。您可以透過設定 DelegatingHandler 來達到此目的，如下所示：

    public async Task CallClientWithHandler()
    {
        MobileServiceClient client = new MobileServiceClient( 
            "AppUrl", 
            "AppKey" ,
            new MyHandler()
            ); 
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await table.InsertAsync(newItem);
    }
             
    public class MyHandler :DelegatingHandler
    {
        protected override async Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            request.Headers.Add("x-my-header", "my value");
            var response = awaitbase.SendAsync(request, cancellationToken);
            response.StatusCode = HttpStatusCode.ServiceUnavailable;
            return response;
        }
    }

### 作法：自訂序列化

[MobileServiceClient](http://msdn.microsoft.com/zh-tw/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) 類別提供類別 [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm) 的 `SerializerSettings` 屬性

透過此屬性，您可以設定許多 Json.NET 屬性，例如，包括一個可將所有屬性轉換為小寫的屬性：

    var settings = new JsonSerializerSettings();
    settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
    client.SerializerSettings = settings;

後續步驟
--------

現在，您已完成此作法概念性參考主題，接下來，請詳細了解如何在行動服務中執行重要工作：

-   [開始使用行動服務](/en-us/develop/mobile/tutorials/get-started)
    了解如何使用行動服務的基本概念。

-   [開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/)
    深入了解使用行動服務來儲存與查詢資料。

-   [開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    了解如何向身分識別提供者驗證應用程式的使用者。

-   [使用指令碼來驗證和修改資料](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    深入了解在行動服務中，使用伺服器指令碼來驗證和變更從應用程式傳送的資料。

-   [使用分頁縮小查詢範圍](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet)
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

-   [使用指令碼來授權使用者](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet)
    了解如何根據驗證的使用者來取得行動服務所提供的使用者識別碼，並用來篩選行動服務所傳回的資料。


