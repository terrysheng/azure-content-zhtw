<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="使用 DocumentDB 建置具有 ASP.NET MVC 的 Web 應用程式 | Azure" description="了解如何使用 DocumentDB 建立待辦事項清單 Web 應用程式。您將從 Azure 主控的 ASP.NET MVC Web 應用程式儲存和存取資料."  metaKeywords="NoSQL, DocumentDB, database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />


<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="ryancraw" />

<a name="_Toc395809351">使用 DocumentDB 建置具有 ASP.NET MVC 的 Web 應用程式</a>
=======================================================================================================
<a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">概觀</a>
==============================================================================================

<a name="_Toc395637759">案例</a>
------------------------------------

為了特別說明客戶要如何有效率地利用 Azure DocumentDB 來儲存和查詢 JSON 文件，本文件提供如何使用 Azure DocumentDB 建置待辦事項清單 Web 應用程式的完整逐步解說。

本逐步解說說明如何使用 Azure 所提供的 DocumentDB 服務，來儲存和存取 Azure 上所託管 ASP.NET MVC Web 應用程式的資料，並假設您先前已有使用 ASP.NET MVC 和 Azure 網站的經驗。

您將了解：

1\. 建立和佈建 DocumentDB 帳戶

2\. 建立 ASP.NET MVC 應用程式

3\. 從 Web 應用程式連線到 Azure DocumentDB 和使用 Azure DocumentDB

4\. 將 Web 應用程式部署至 Azure 網站

按照本逐步解說進行後，您將建置簡單的網頁型工作管理應用程式，以便建立、擷取和完成工作。在 Azure DocumentDB 中，這些工作將會儲存為 JSON 文件。

![Alt text](./media/documentdb-dotnet-application/image1.png)


<a name="_Toc395637760">Prerequisites</a>
================================================================

在依照本文中的指示進行之前，您應確定已安裝下列項目：

Git for Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (或免費版本的 [Visual Studio Express][] 英文))

Azure SDK for .NET 2.3 版或更高版本，可透過 [Microsoft Web Platform Installer (英文) 取得][]

本文件中的所有螢幕擷取畫面都是使用已套用 Update 3 的 Visual Studio 2013 以及 Azure SDK for .NET 2.4 版所取得。如果您的系統是設定使用不同的版本，則您的畫面和選項可能不會完全相符，但只要您符合上述必要條件，本方案應該還是有效。

<a name="_Toc395637761">建立 DocumentDB 資料庫帳戶</a>
================================================================

若要在 Azure 中佈建 DocumentDB 資料庫帳戶，請開啟 Azure 管理入口網站，並按一下首頁上的 [Azure Gallery] 磚，或按一下畫面左下角的 "+"。

![Alt text](./media/documentdb-dotnet-application/image2.png)


這會開啟 [Azure Gallery]，您可以在其中從多個可用 Azure 服務進行選取。在 Gallery 中，從類別清單中選取 [資料、儲存體及備份]。

![Alt text](./media/documentdb-dotnet-application/image3.png)

從這裡，選取 Azure DocumentDB 的選項

![Alt text](./media/documentdb-dotnet-application/image4.png)


然後選取畫面底部的 [建立]

![Alt text](./media/documentdb-dotnet-application/image5.png)

這會開啟 [New DocumentDB] 刀鋒，您可以在其中指定新帳戶的名稱、區域、規模、資源群組和其他設定。

![Alt text](./media/documentdb-dotnet-application/image6.png)


一旦您提供了帳戶值，請按一下 [建立]，就會開始佈建程序並開始建立您的資料庫帳戶。
佈建程序完成時，您應該會看到入口網站通知區域中顯示的通知；如果您選擇建立磚，您的 [開始] 畫面中顯示的磚也會變更為顯示完整動作。

![Alt text](./media/documentdb-dotnet-application/image7.png)


佈建完成之後，按一下開始畫面中的 [DocumentDB] 磚，這個新建立 DocumentDB 帳戶的主要刀鋒就會出現。

![Alt text](./media/documentdb-dotnet-application/image8.png) 
![Alt text](./media/documentdb-dotnet-application/image9.png)


使用 [金鑰] 按鈕，存取端點 URL 和主要金鑰，將這些項目複製到剪貼簿並將它們保存在隨手可得的地方，因為我們將在接下來要建立的 Web 應用程式中使用這些值。

我們現在將從頭開始逐步解說如何建立新的 ASP.NET MVC 應用程式。您可以在 [[這裡]] 下載完整方案，供您參考。

<a name="_Toc395637762">建立新的 ASP.NET MVC 應用程式</a>
================================================================

在 Visual Studio 中，按一下 [檔案] - [新增專案]，然後選取可供建立新 ASP.NET MVC Web 應用程式的選項。

![Alt text](./media/documentdb-dotnet-application/image10.png)


選取您要建立專案的位置，以及專案名稱 (我會使用「待辦」)，再按一下 [確定]。

![Alt text](./media/documentdb-dotnet-application/image11.png)


如果您打算在 Azure 中託管應用程式，則請選取 [Host in the cloud] 右下角的方塊。我們已選擇託管於雲端，並執行 Azure 網站中所託管的應用程式。選取此選項將會預先佈建 Azure 網站，讓您在需要部署最終的運作應用程式時更為容易。如果您希望在各處都能托管，或不想預先設定，請取消勾選 [在雲端托管] 選項。

選取 [確定]，並讓 Visual Studio 執行有關 Scaffolding 空的 ASP.NET MVC 範本的作業。如果您選擇在雲端托管，則至少會有一個其他畫面出現，要求您登入 Azure 帳戶並提供新網站的部分值。請繼續提供所有這些 Azure 值，然後繼續進行。 

在這裡我並沒有選擇 [資料庫伺服器]，因為我們並未使用 Azure SQL Database Server，稍後我們會在管理入口網站中建立新的 Azure DocumentDB 帳戶。 

![Alt text](./media/documentdb-dotnet-application/image11_1.png)

Visual Studio 建立好未定案 MVC 應用程式之後，您便擁有可以在本機執行的空白 ASP.NET 應用程式。

我們會跳過在本機執行的步驟，因為我確定我們都已看過 ASP.NET "Hello World" 應用程式。讓我們直接跳到將 DocumentDB 新增至此專案並建置應用程式的步驟。

</h1>
<a name="_Toc395637767">將 DocumentDB 新增至專案</a>
=============================================================

前面的步驟可處理此方案所需的大部分 ASP.NET MVC 配管作業。現在，讓我們進入本教學課程的真正目的：將 Azure DocumentDB 新增至 Web 應用程式。

### 

### <a name="_Toc395637764">安裝 NuGet 封裝</a>

DocumentDB .NET SDK 會隨 NuGet 封裝而分散。在 Visual Studio 中使用 NuGet 封裝管理員 (您可以在專案上按右鍵並選擇 [管理 NuGet 封裝] 以取得)。

![Alt text](./media/documentdb-dotnet-application/image21.png)

搜尋 Online 中的 "Azure DocumentDB"，並安裝該封裝。這會下載和安裝 DocumentDB 封裝，以及所有依存項目 (例如 Newtonsoft.Json)。

**注意：**服務在預覽狀態時，NuGet 封裝會標記為「預先發行」，因此您需要包含 [包括預先發行] 選項，否則您會找不到封裝。

或者，您也可以使用「封裝命令主控台] 輸入以下資料以安裝封裝：

    Install-Package Microsoft.Azure.Documents.Client -Pre

安裝之後，您的 Visual Studio 方案應該類似下列已新增兩個新參考的方案；

![Alt text](./media/documentdb-dotnet-application/image22.png)


<a name="_Toc395637763">設定 ASP.NET MVC 應用程式</a>
==================================================================

### 

### <a name="_Toc395637764">新增模型</a>

首先，讓我們在 MVC 中建立 **M** (模型)。在 [方案總管] 中， *Models* 資料夾上按一下滑鼠右鍵，然後依序按一下 [**新增**] 和 [**類別**]

![Alt text](./media/documentdb-dotnet-application/image12.png)

將新的類別命名為 **Item**，然後將下列程式碼新增至這個新的類別

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
		
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
		
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
		
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

DocumentDB 中的所有資料都會透過線路傳遞，並儲存為 JSON。如需透過 JSON.NET 控管物件序列化/取消序列化，您可以使用剛才在「項目類別」中建立的示簵 JsonProperty 屬性。您**無需**這樣做，但我想確定所有屬性都依 JSON camelCase 命名慣例命名。 

使用 JSON 時，您不但可以控管屬性名稱格式，也可以跟我命名 Description 屬性一樣重新命名您的 .NET 屬性。 

如果希望，您在這裡也可以使用 JsonConverter 物件以便完整控管序列化方式。  

為了取得 Visual Studio 以解決這裡使用的 "JsonProperty" 屬性，您需要在類別檔案區段中使用以下陳述式：

    using Newtonsoft.Json;

### <a name="_Toc395637765">新增控制器</a>

之前都是談論 M，現在讓我們在 MVC 中建立 **C** (控制器類別)。
在 [**方案總管**] 中， *Controllers* 資料夾上按一下滑鼠右鍵，然後依序按一下 [**新增**] 和 [**控制器**]。

![Alt text](./media/documentdb-dotnet-application/image13.png)

![Alt text](./media/documentdb-dotnet-application/image14.png)


在 [**新增 Scaffold**] 對話方塊中，按一下 [**MVC 5 Controller - Empty**]。按一下 [**新增**]。

![Alt text](./media/documentdb-dotnet-application/image15.png)

將新的控制器命名為 **ItemController。**

Visual Studio 現在會新增 ItemController，您的 [方案總管] 應該會如下所示。

![Alt text](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766">新增檢視</a>

最後，讓我們在 MVC 中建立 **V** (檢視)。


#### 新增項目索引檢視

在 [方案總管] 中展開 [***Views***]  資料夾，以及先前新增  *ItemController* 時，Visual Studio 為您建立之 (空白) [Item] 資料夾的位置。在 ***Item*** 上按一下滑鼠右鍵，然後選擇 [新增] 新的檢視。

![Alt text](./media/documentdb-dotnet-application/image17.png)

在 [新增檢視] 對話方塊中。呼叫您的檢視 "***Index***"、使用 ***List*** 範本、選取先前建立的 ***Item (Todo.Models)*** 做為類別，最後在 [方案] 中使用 ***~Views/Shared/_Layout.cshtml*** 做為 [配置] 頁面。

![Alt text](./media/documentdb-dotnet-application/image18.png)


設定所有這些值之後，按一下 [新增]，並讓 Visual Studio 為您建立檢視。Visual Studio 將會建立範本檢視。完成之後，將會開啟建立的 cshtml 檔案。我們之後才會回頭使用此文件，因此可以先在 Visual Studio 中將其關閉。

#### 新增項目檢視

使用與上面類似的方式，根據下面所示的範例，建立用於建立新 Item 的新檢視；

![Alt text](./media/documentdb-dotnet-application/image19.png)

#### 新增編輯項目檢視

<a name="_Toc395888515"></a>
============================

最後，使用與之前相同的方式新增最後一個檢視來編輯項目；

![Alt text](./media/documentdb-dotnet-application/image20.png)


完成這項作業之後，先將 Visual Studio 中的 cshtml 文件關閉，因為我們稍後才會回頭使用這些檢視。

</h1>
<a name="_Toc395637769">組建 DocumentDB</a>
------------------------------------------------

### <a name="_Toc395637770">列出未完成項目</a>

開啟 **ItemController**，並移除 Visual Studio 所新增類別內的所有程式碼 (但保留該類別)。我們將使用 DocumentDB 重新拼湊各個部分。

在目前已是空白的 ItemController 類別內，新增下列程式碼片段；

    public ActionResult Index()
    {
        var items = DocumentDBRepository.GetIncompleteItems();
        return View(items);
    }

此程式碼也會使用名為 DocumentDBRepository 的 "pseudo 保存庫" 類別，我們尚未建立這個類別。實際上這只是一個 Helper 類別，包含所有 DocumentDB 指定程式碼。在本逐步解說中，我們不會像您在建置實際應用程式時的可能作法一樣，使用儲存機制模式來實作有插入依存項目、factory 及保存庫模式的完整資料存取層。 
在本逐步解說中，我們只會將所有資料存取邏輯直接放入一個專案，簡化所有程序並著重在 DocumentDB 指定項目。

在專案中新增類別，並命名為 **DocumentDBRepository。**
使用下列程式碼取代類別檔案中的程式碼：

    public static class DocumentDBRepository
    {
        private static string databaseId;
        private static String DatabaseId
        {
			get
			{
				if (string.IsNullOrEmpty(databaseId))
				{
					databaseId = ConfigurationManager.AppSettings["database"];
				}
			
				return databaseId;
			}
        }
			  
        private static string collectionId;
        private static String CollectionId
        {
			get
			{
				if (string.IsNullOrEmpty(collectionId))
				{
					collectionId = ConfigurationManager.AppSettings["collection"];
				}
			
				return collectionId;
			}
        }
				
        private static Database database;
        private static Database Database
        {
			get
			{
				if (database == null)
				{
					database = ReadOrCreateDatabase();
				}
				
				return database;
			}
        }
		
        private static DocumentCollection collection;
        private static DocumentCollection Collection
        {
			get
			{
				if (collection == null)
				{
					collection = ReadOrCreateCollection(Database.SelfLink);
				}
				
				return collection;
			}
        }
		
        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
					string endpoint = ConfigurationManager.AppSettings["endpoint"];
					string authKey = ConfigurationManager.AppSettings["authKey"];
					Uri endpointUri = new Uri(endpoint);
					client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }
    } 

#### 

花一點時間解析 Visual Studio 中所有的命名空間。只要已順利安裝 NuGet 封裝，就能輕鬆地在 Visual Studio 中解析所有命名空間。ReadOrCreateDatabase 和 ReadOrCreateCollection 方法的參照仍處於未解析狀態，待新增之後才會解析，我們稍後會進行到這部分。 

曾經在這裡使用兩種方法呼叫，以讀取或建立 DocumentDB 資料庫及文件集合。
因此，請將下列兩種方法加入至類別：

    private static DocumentCollection ReadOrCreateCollection(string databaseLink)
    {
        var col = Client.CreateDocumentCollectionQuery(databaseLink)
                          .Where(c => c.Id == CollectionId)
                          .AsEnumerable()
                          .FirstOrDefault();

        if (col == null)
        {
            col = Client.CreateDocumentCollectionAsync(databaseLink, new DocumentCollection { Id = CollectionId }).Result;
        }
		
        return col;
    }

    private static Database ReadOrCreateDatabase()
    {
        var db = Client.CreateDatabaseQuery()
                        .Where(d => d.Id == DatabaseId)
                        .AsEnumerable()
                        .FirstOrDefault();
		
        if (db == null)
        {
            db = Client.CreateDatabaseAsync(new Database { Id = DatabaseId }).Result;
        }
		
        return db;
    }

</h1>

#### 

如此可以設定資料庫、DocumentCollection，並建立相同的程式碼以透過 DocumentClient 連線到 DocumentDB。 

我們正在從設定中讀取部分值，因此請開啟 **web.config** 並在 
<AppSettings\> 區段下新增以下程式碼行：

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>


</h4>

現在讓我們加入一些程式碼來執行工作。 
首先我們希望能夠在待辦事項應用程式中執行的作業是顯示未完成項目，下列方法可達到此目的，請將它複製並貼到正在使用的儲存機制類別內的任意位置。
    
    public static List<Item> GetIncompleteItems()
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => !d.Completed)
                .AsEnumerable()
                .ToList<Item>();
    }
    
在您需要手動新增下列 using 指示詞之後，會解析  *CreateDocumentQuery* 的參考：

    using Microsoft.Azure.Documents.Linq;

此時，應該已經可以建置方案，而不會發生任何錯誤。

如果您現在執行應用程式，將會前往首頁控制器及該控制器的 [索引] 檢視。這是我們在一開始時所選擇的 MVC 範本專案預設行為，但是我們不想要這樣的行為！讓我們變更此 MVC 應用程式上的路由以改變此行為。

開啟 ***App\_Start\RouteConfig.cs*** 並尋找以 "defaults:" 開頭的程式碼行並加以變更如下：

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

如果您未在 URL 中指定控制路由行為的值，這會讓 ASP.NET MVC 知道改用 "Item" (而非 "Home") 作為控制器，並使用使用者索引作為檢視。
現在如果您執行應用程式，它會呼叫至您的 **ItemController** ，並將 **GetIncompleteItems** 方法的結果傳回 Views\Item\Index 檢視。 

如果您建置並立即執行此專案，您現在應該會看到如下的外觀：    

![Alt text](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771">新增項目</a>

讓我們將一些項目放入資料庫中，而不再只是看到空白方格。

我們已經有用於建立的檢視，並且在索引檢視上有將使用者帶到建立檢視的按鈕。讓我們將一些程式碼新增至控制器和儲存機制，以在 DocumentDB 中保留記錄。

開啟 **ItemController.cs***，並新增下列程式碼片段，讓 ASP.NET MVC 知道如何處理「建立」動作，在這個案例中，只會呈現先前建立的相關聯 Create.cshtml 檢視。

    public ActionResult Create()
    { 
	return View(); 
    }

現在此控制器需要更多程式碼，以接受建立檢視所提交的資料。

新增下一個程式碼區塊，讓 ASP.NET MVC 知道如何處理此控制器的表單 POST。
	
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Create([Bind(Include = "Id,Name,Description,Completed")] Item item)  
    {
		if (ModelState.IsValid)  
		{  
		    await repo.CreateDocument(item);  
		    return RedirectToAction("Index");  
		}   
    	return View(item);   
    }

**安全性注意事項**：此處所使用的 [ValidateAntiForgeryToken] 屬性可協助應用程式防止跨網站偽造要求攻擊。這不光只是新增此屬性，您的檢視也必須使用這個防偽權杖。如需此主題的詳細資訊以及如何正確實作此作業的範例，請參閱[防止跨網站偽造要求][]。本文結尾處下載連結中的原始程式碼已經有完整的實作。

**安全性注意事項**：我們也會在方法參數上使用[Bind] 屬性，以協助防範 overposting 攻擊。如需詳細資訊，請參閱 [ASP.NET MVC 中的基本 CRUD 作業][]

現在，一切皆已就緒，項目控制器將項目安全地從表單傳遞到儲存機制類別的 CreateDocument 方法，因此請將下列方法新增至 DocumentDBRepository 類別。

    public static async Task<Document> CreateItemAsync(Item item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item);
    }

此方法只會接受傳遞給它的物件，並將它保留在DocumentDB 中。
這包括將新項目新增至資料庫所需的程式碼。


### <a name="_Toc395637772">編輯項目</a>

我們最後還要做一件事，那就是能夠編輯資料庫中的項目，並能夠在我們完成工作時將項目標示為已完成。與新增的檢視相同，編輯的檢視已經新增，因此，我們只需重新將某些其他程式碼新增至控制器以及DocumentDBRepository 類別即可。

將下列程式碼新增至 ItemController 類別；

    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    {
        if (ModelState.IsValid)
        {
            await DocumentDBRepository.UpdateItemAsync(item);
            return RedirectToAction("Index");
        }

        return View(item);
    }
	
    public ActionResult Edit(string id)
    {
        if (id == null)
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
        }

        Item item = (Item)DocumentDBRepository.GetItem(id);
        if (item == null)
        {
            return HttpNotFound();
        }

        return View(item);
    }

第一個方法會處理當使用者按一下 [索引] 檢視中的 [編輯] 連結時所發生的Http Get。此方法會從DocumentDB 中提取 Document，並將它傳遞給 [編輯] 檢視。

[編輯] 檢視會接著對 IndexController 執行 Http Post。 
新增的第二個方法會處理此作業，將更新物件傳遞至 DocumentDB 並保留在資料庫中。

將下列程式碼新增至 DocumentDBRepository 類別；

    public static Item GetItem(string id)
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => d.Id == id)
                    .AsEnumerable()
                    .FirstOrDefault();
    }
	
    public static async Task<Document> UpdateItemAsync(Item item)
    {
        Document doc = Client.CreateDocumentQuery(Collection.DocumentsLink)
                            .Where(d => d.Id == item.Id)
                            .AsEnumerable()
                            .FirstOrDefault();

        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

這兩個方法中的第一個方法會從 DocumentDB 提取項目，這會傳遞回 ItemController，接著傳遞給 [編輯] 檢視。

第二個新增的方法會使用從 ItemController 傳入的Document 版本，來取代 DocumentDB 中的文件。

這樣便大功告成了，這些就是我們必須執行應用程式的所有作業：列出未完成項目、新增項目，最後是編輯項目。

</h3>
<a name="_Toc395637773">在本機執行您的應用程式</a>
=========================================================

### 

若要在本機電腦上測試應用程式，請在 Visual Studio 中按 F5 鍵，這樣應該會建置應用程式，並啟動含有先前所看過之空白方格頁面的瀏覽器：

![Alt text](./media/documentdb-dotnet-application/image24.png)

1\.使用所提供的 [項目]、[項目名稱] 和 [類別] 欄位來輸入資訊，然後按一下 [**建立新物件**] 連結，並提供一些值。將 [已完成] 核取方塊保持為未選取狀態，否則，新項目會以已完成的狀態新增，且不會出現在初始清單中。

![Alt text](./media/documentdb-dotnet-application/image25.png)

如果按 [建立]，系統會將您重新導向回到 [索引] 頁面，而且您的項目應該會顯示在 [清單] 中。

![Alt text](./media/documentdb-dotnet-application/image26.png)

依需要新增一些項目至 [待辦事項] 清單。

2\.按一下清單上 [項目] 旁邊的 [編輯]，您便會被帶到 [編輯] 檢視，您可在此更新任何物件的屬性 (包括 [已完成] 旗標)。這會將項目有效地標記為已完成，並將該項目從未完成工作的清單中移除。

![Alt text](./media/documentdb-dotnet-application/image27.png)

3\.若要完成工作，您只需核取此核取方塊，然後按一下 [**儲存**]。您便會被重新導向回到清單頁面，且此清單中現在不會再出現該項目。

</h3>
<a name="_Toc395637774">將應用程式部署至 Azure 網站</a>
================================================================

### 

您已經擁有可在DocumentDB 正常運作的完整應用程式，我們現在要將此應用程式部署至 Azure 網站。

如果您在建立空白 ASP.NET MVC 專案時選取了 [Host in the cloud]，Visual Studio 可讓這項作業變得十分簡單，且為您完成大部分的工作。若要發佈此應用程式，您只需要在 [方案總管] 的 [專案] 上按一下滑鼠右鍵 (請確定您已經沒有在本機上執行此應用程式)，然後選取 [發佈]。

![Alt text](./media/documentdb-dotnet-application/image28.png)

系統應該已根據您的認證設定好所有項目；實際上，系統已在 Azure 中建立位於所顯示 [目的地 URL] 的網站，您只需要按一下 [**發佈**] 即可。

![Alt text](./media/documentdb-dotnet-application/image29.png)

幾秒後，Visual Studio 便會發佈 Web 應用程式並啟動瀏覽器，您可以在瀏覽器中看到您方便好用的應用程式已在 Azure 中執行！

</h3>



<a name="_Toc395637775">結論</a>
======================================

### 

恭喜！您剛剛已經使用 Azure DocumentDB 建置您的第一個 ASP.NET MVC 應用程式，並將它發佈至 Azure 網站。您可以在 [[這裡][]] 下載完整應用程式 (包括我們在此略過的詳細資料和刪除功能) 的原始程式碼。


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/zh-tw/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[這裡]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[防止跨網站偽造要求]: http://go.microsoft.com/fwlink/?LinkID=517254
[ASP.NET MVC 中的基本 CRUD 作業]: http://go.microsoft.com/fwlink/?LinkId=317598

<!--HONumber=27-->
