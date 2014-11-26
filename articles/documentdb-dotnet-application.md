<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Build a web application with ASP.NET MVC using DocumentDB | Azure" description="Learn how to use DocumentDB to build a To Do List web application. You'll store and access data from an ASP.NET MVC web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="hawong" />

# <a name="_Toc395809351">使用 DocumentDB 建置具有 ASP.NET MVC 的 Web 應用程式</a>

# <a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">概觀</a>

## <a name="_Toc395637759">案例</a>

為了特別說明客戶要如何有效率地利用 Azure DocumentDB 來
儲存和查詢 JSON 文件，本文件提供如何
使用 Azure DocumentDB 建置待辦事項清單 Web 應用程式的
完整逐步解說。

本逐步解說說明如何使用 Azure 所提供的 DocumentDB 服務，
來儲存和存取 Azure 上所託管 ASP.NET MVC Web 應用程式
的資料，並假設您先前已有使用 ASP.NET MVC
和 Azure 網站的經驗。

您將了解：

1\. 建立和佈建 DocumentDB 帳戶

2\. 建立 ASP.NET MVC 應用程式

3\. 從 Web 應用程式連線到 Azure DocumentDB 和使用 Azure DocumentDB

4\. 將 Web 應用程式部署至 Azure 網站

按照本逐步解說進行後，您將建置簡單的網頁型
工作管理應用程式，以便建立、擷取和
完成工作。在 Azure DocumentDB 中，這些工作將會儲存為
JSON 文件。

![替代文字][替代文字]

# <a name="_Toc395637760">必要條件</a>

在依照本文中的指示進行之前，您應該確定
已安裝下列項目：

Git for Windows <http://www.git-scm.com/downloads> (英文)

Visual Studio 2013 (或免費版本的 [Visual Studio Express][Visual Studio Express] (英文))

Azure SDK for .NET 2.3 版或更高版本，可透過
[Microsoft Web Platform Installer][Microsoft Web Platform Installer] (英文) 取得

本文件中的所有螢幕擷取畫面都是使用已套用 Update 3 的 
Visual Studio 2013 以及 Azure SDK for .NET 2.4 版所取得。
如果您的系統是設定使用不同的版本，則您的畫面和選項可能不會完全
相符，但只要您符合上述必要條件，本方案應該還是有效。

# <a name="_Toc395637761">建立 DocumentDB 資料庫帳戶</a>

若要在 Azure 中佈建 DocumentDB 資料庫帳戶，請開啟 Azure
管理入口網站，並按一下首頁上的 [Azure Gallery] 磚，
或按一下畫面左下角的 "+"。

![替代文字][1]

這會開啟 [Azure Gallery]，您可以在其中從多個可用 Azure 服務
進行選取。在 Gallery 中，從類別清單中選取 [Data, storage and backup]。

![替代文字][2]

從這裡，選取 Azure DocumentDB 的選項

![替代文字][3]

然後選取畫面底部的 [建立]

![替代文字][4]

這會開啟 [New DocumentDB] 刀鋒，您可以在其中指定
新帳戶的名稱、區域、規模、資源群組
和其他設定。

![替代文字][5]

提供好帳戶值之後，按一下 [建立]，佈建程序
即會開始建立資料庫帳戶。佈建程序完成時，
您會在入口網站的通知區域中看到通知，而且
開始畫面上的磚 (如果您已選擇建立磚) 將會變更成
顯示已完成的動作。

![替代文字][6]

佈建完成之後，按一下開始畫面中的 [DocumentDB] 磚
，這個新建立 DocumentDB 帳戶的
主要刀鋒就會出現。

![替代文字][7]
![替代文字][8]

使用 [金鑰] 按鈕，存取端點 URL 和主要金鑰，
將這些項目複製到剪貼簿並將它們保存在隨手可得的地方，
因為我們將在接下來要建立的 Web 應用程式中使用這些值。

我們現在將從頭開始逐步解說如何建立新的
ASP.NET MVC 應用程式。您可以在 [這裡] 下載完整方案
，供您參考。

# <a name="_Toc395637762">建立新的 ASP.NET MVC 應用程式</a>

在 Visual Studio 中，按一下 [檔案] – [新增專案]，然後選取
可供建立新 ASP.NET MVC Web 應用程式的選項。

![替代文字][9]

選取您要建立專案的位置，然後按一下 [確定]。

![替代文字][10]

如果您打算在 Azure 中託管應用程式，則請選取 [Host in the cloud] 右下角的方塊。我們已選擇託管於雲端，並執行 Azure 網站中所託管的應用程式。選取
此選項將會預先佈建 Azure 網站，讓您在需要部署最終的運作應用程式時
更為容易。

選取 [確定]，並讓 Visual Studio 執行有關 Scaffolding
空的 ASP.NET MVC 範本的作業。您可能會看到其他畫面，要求您
登入 Azure 帳戶，並提供新網站的
一些值。請繼續提供所有這些 Azure 值，然後繼續進行。

Visual Studio 建立好未定案 MVC 應用程式之後，
您便擁有可以在本機執行的空白 ASP.NET 應用程式。

我們會跳過在本機執行的步驟，因為我確定我們都已看過 ASP.NET "Hello World" 應用程式。讓我們直接跳到將 DocumentDB 新增至此專案並建置應用程式的步驟。

# <a name="_Toc395637763">設定 ASP.NET MVC 應用程式</a>

### 

### <a name="_Toc395637764">新增模型</a>

首先，讓我們在 MVC 中建立 **M** (模型)。在 [方案總管] 中，
於 [Models] 資料夾上按一下滑鼠右鍵，然後依序按一下 [新增] 和
[類別]

![替代文字][11]

將新的類別命名為 **Item**，然後將下列程式碼新增至這個
新的類別

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public int ID { get; set; }
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

DocumentDB 中的所有資料都會透過線路傳遞，並儲存為 JSON。若要
控制 JSON.NET 序列化/還原序列化您物件的方式，
您可以使用上面的 JsonProperty 屬性，確保
屬性名稱遵循 JSON 的小寫屬性名稱慣例。
您不**需要**這麼做。

### <a name="_Toc395637765">新增控制器</a>

之前都是談論 M，現在讓我們在 MVC 中建立 **C**
(控制器類別)。
在 [方案總管] 中，於 [Controllers] 資料夾上按一下滑鼠右鍵，然後
依序按一下 [新增] 和 [控制器]。

![替代文字][12]

![替代文字][13]

在 [Add Scaffold] 對話方塊中，按一下 [MVC 5 Controller - Empty]。
按一下 [新增]。

![替代文字][14]

將新的控制器命名為 **ItemController**。

Visual Studio 現在會新增 ItemController，您的 [方案總管]
應該會如下所示。

![替代文字][15]

### <a name="_Toc395637766">新增檢視</a>

最後，讓我們在 MVC 中建立 **V** (檢視)。

#### 新增項目索引檢視

在 [方案總管] 中展開 **[Views]** 資料夾，以及先前新增 *ItemController* 時，
Visual Studio 為您建立之 (空白) [Item] 資料夾的
位置。在 ***Item*** 上按一下滑鼠右鍵，
然後選擇 [新增] 新的檢視。

![替代文字][16]

在 [新增檢視] 對話方塊中。呼叫您的檢視 "***Index***"、使用
***List*** 範本、選取先前建立的 ***Item (Todo.Models)***
做為類別，最後在 [方案] 中使用 ***\_Layout.cshtml***
做為 [配置] 頁面。

![替代文字][17]

設定所有這些值之後，按一下 [新增]，並讓 Visual Studio 為您建立
檢視。Visual Studio 將會建立範本檢視。完成之後，
將會開啟建立的 cshtml 檔案。我們之後才會回頭使用此文件，
因此可以先在 Visual Studio 中將其關閉。

#### 新增項目檢視

使用與上面類似的方式，根據下面所示的範例，建立用於建立
新 Item 的新檢視；

![替代文字][18]

#### 新增編輯項目檢視

# <a name="_Toc395888515"></a>

最後，使用與之前相同的方式新增最後一個檢視來編輯項目；

![替代文字][19]

完成這項作業之後，先將 Visual Studio 中的 cshtml 文件關閉，
因為我們稍後才會回頭使用這些檢視。

</h1>
# <a name="_Toc395637767">將 DocumentDB 新增至專案</a>

前面的步驟可處理此方案所需的大部分 ASP.NET MVC 配管作業。
現在，讓我們進入本教學課程的真正目的：將 Azure DocumentDB 新增至 Web 應用程式。

</h1>
## <a name="_Toc395637768">安裝 DocumentDB NuGet 封裝</a>

DocumentDB .NET SDK 會進行封裝，並以 NuGet 封裝的形式散佈。
使用 Visual Studio 中的 NuGet 封裝管理員 (在 [專案] 上按一下滑鼠右鍵，
然後選擇 [Manage NuGet Packages]，即可使用它)。

![替代文字][20]

搜尋 Online 中的 "Azure DocumentDB"，並安裝該封裝。這
會下載和安裝 DocumentDB 封裝，以及所有
依存項目 (例如 Newtonsoft.Json)。

安裝之後，您的 Visual Studio 方案應該類似下列已新增
兩個新參考的方案；

![替代文字][21]

</h1>
## <a name="_Toc395637769">組建 DocumentDB</a>

### <a name="_Toc395637770">列出未完成項目</a>

開啟 **ItemController**，並移除 Visual Studio 所新增類別內的所有程式碼
(但保留該類別)。我們將使用 DocumentDB 重新拼湊各個部分。

在目前已是空白的 ItemController 類別內，新增下列程式碼片段
；

    public async Task<ActionResult> Index() 
    {
        var items = await repo.GetIncompleteItems();
        return View(items);
    }

此片段使用 DocumentDB 的「虛擬儲存機制」類別，它實際只是含有所有
DocumentDB 特定程式碼的 Helper 類別。基於本逐步解說的目的，
我們不會像您在建置實際應用程式時的可能作法一樣，使用儲存機制模式
來實作有插入依存項目的完整資料存取層。基於本逐步解說的目的，
我們只會將所有資料存取邏輯放入一個專案，以方便進行說明。

將新的類別新增至專案，並將它稱為 **DocumentDBRepository**。
並將類別檔案中的程式碼更換為下列內容；

    public static class DocumentDBRepository
    {
        public static async Task<List<Item>> GetIncompleteItems()
        {
            return await Task<List<Item>>.Run( () => 
                Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => !d.Completed)
                    .AsEnumerable()
                    .ToList<Item>());
        }
    } 

#### 

花一些時間新增適當的 using 陳述式來解析參考。只要已順利安裝 NuGet 封裝，
就能輕鬆地在 Visual Studio 中解析所有參考。在您需要手動新增下列 using 
指示詞之後，會解析*CreateDocumentQuery* 參考；

    using Microsoft.Azure.Documents.Linq; 

我們需要將一些手動配管作業新增至虛擬
儲存機制類別，才能讓一切正常運作。將下面的程式碼
新增至儲存機制類別；

        private static Database database;
        private static Database Database
        {
            get
            {
                if (database == null)
                {
                    ReadOrCreateDatabase().Wait();
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
                    ReadOrCreateCollection(Database.SelfLink).Wait();
                }
                
                return collection;
            }
        }

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

        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
                    String endpoint = ConfigurationManager.GetSetting("endpoint")
                    string authKey = ConfigurationManager.GetSetting("authKey");
                    Uri endpointUri = new Uri(endpoint);
                    client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }

        private static async Task ReadOrCreateCollection(string databaseLink)
        {
            var collections = Client.CreateDocumentCollectionQuery(databaseLink)
                              .Where(col => col.Id == CollectionId).ToArray();
            
            if (collections.Any())
            {
                collection = collections.First();
            }
            else
            {
                collection = await Client.CreateDocumentCollectionAsync(databaseLink, 
                    new DocumentCollection { Id = CollectionId });
            }
        }
     
        private static async Task ReadOrCreateDatabase()
        {
            var databases = Client.CreateDatabaseQuery()
                            .Where(db => db.Id == DatabaseId).ToArray();
            
            if (databases.Any())
            {
                database = databases.First();
            }
            else
            {
                Database database = new Database { Id = DatabaseId };   
                database = await Client.CreateDatabaseAsync(database);
            }
        }

</h1>
#### 

當您第一次存取 GetIncompleteItems() 這類方法時，它會對
用戶端呼叫，以便與 Azure DocumentDB 建立連線。第一次對
 GetIncompleteItems() 呼叫時，也會嘗試讀取或建立此應用程式的
Database 和 DocumentCollection。此動作只需要
執行一次，之後只要快取這兩個資源的 SelfLink 屬性
並於應用程式中重複使用即可。

之後若想要在相同的應用程式內連線到 DocumentDB，
將會重複使用相同的 Client、Database 和 Collection 執行個體，
而不需要每次都建立新的執行個體。

開啟 **web.config**，並在<appsettings\>區段下方新增下列幾行；

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>

</h4>
此時，應該已經可以建置方案，而不會發生任何錯誤。

如果您現在執行應用程式，將會前往首頁控制器以及
該控制器的 [索引] 檢視。這是我們在一開始時所選擇的
MVC 範本專案預設行為，但是我們不想要這樣的行為！讓我們
變更此 MVC 應用程式上的路由，以變更此行為。

開啟 [App\_Start] 資料夾下方的 ***RouteConfig.cs***，並找到開頭為 "defaults:" 的行，
變更它使其與下列類似；

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

這現在會告知 ASP.NET MVC，如果您尚未在 URL 中指定值來控制路由行為，則不使用「首頁」，而是使用「項目」做為控制器並使用「索引」做為檢視。
現在，如果您執行應用程式，則會對 **ItemController** 呼叫，並將 **GetIncompleteItems** 方法的結果傳回給「檢視\\項目\\索引檢視」。
如果您立即執行此專案，則現在應該會看到與下列類似的內容；

![替代文字][22]

### <a name="_Toc395637771">新增項目</a>

現在，讓我們將一些項目放入資料庫中，而不再只是看到空白方格。

我們已經有用於建立的檢視，並且在索引檢視上有將使用者帶到建立檢視的
按鈕。讓我們將一些程式碼新增至
控制器和儲存機制，以在 DocumentDB 中保留記錄。

開啟 ***ItemController.cs***，並新增下列程式碼片段，
讓 ASP.NET MVC 知道如何處理「建立」動作，在這個案例中，
只會呈現先前建立的相關聯 Create.cshtml 檢視。

    public ActionResult Create()
    { 
          return View(); 
    }

現在此控制器需要更多程式碼，以接受建立檢視所提交的資料。

接著，新增下一個程式碼區塊，以告知 ASP.NET MVC 怎麼處理此控制器的
表單 POST。

    [HttpPost]  
    public async Task<ActionResult> Create(Item item)  
    {
        if (ModelState.IsValid)  
        {  
            await repo.CreateDocument(item);  
            return RedirectToAction("Index");  
        }   
        return View(item);   
    }

項目控制器現在會將項目從表單傳遞給
虛擬儲存機制類別的 CreateDocument 方法，因此，請將
下列方法新增至 DocumentDBRepository 類別。

    public static async Task<Document> CreateDocument(dynamic item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item); 
    }

此方法只會接受傳遞給它的物件，並將它保留在
DocumentDB 中。

這包括將新項目新增至資料庫所需的程式碼。

### <a name="_Toc395637772">編輯項目</a>

我們最後還要做一件事，就是能夠編輯
資料庫中的項目並在我們完成
工作時將項目標示為已完成。與新增相同，已新增用於編輯的檢視，因此，我們
只需重新將另外的程式碼新增至控制器以及
DocumentDBRepository 類別即可。

將下列程式碼新增至 ItemController 類別；

    public async Task<ActionResult> Edit(string id)    
    {  
        if (id == null)  
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);   
        }

        Item item = await repo.GetDocument(id);
        if (item == null)
        {       
            return HttpNotFound();
        }
        
        return View(item);
    }

    [HttpPost] 
    public async Task<ActionResult> Edit(Item item)  
    {
        if (ModelState.IsValid)
        {
             await repo.UpdateDocument(item);
             return RedirectToAction("Index");
        }
        
        return View(item); 
    }

第一個方法處理使用者按一下 [索引] 檢視中的 [編輯] 連結時所發生的
Http Get。此方法會從
DocumentDB 中提取 Document，並將它傳遞給 [編輯] 檢視。

### 

[編輯] 檢視接著會回頭對 IndexController 執行 Http Post，而
我們新增的第二個方法會處理將已更新的物件
傳遞給 DocumentDB 以便保留在資料庫中的這項作業。

將下列程式碼新增至 DocumentDBRepository 類別；

    public static async Task<Item> GetDocument(string id)
    {
        return await Task<Item>.Run(() =>
            Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => d.ID == id)
                .AsEnumerable()
                .FirstOrDefault());
    }

    public static async Task<Document> UpdateDocument(Item item)
    {
        var doc = Client.CreateDocumentQuery<Document>(Collection.DocumentsLink)
                    .Where(d => d.Id == item.ID)
                    .AsEnumerable().FirstOrDefault();
        
        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

這兩個方法中的第一個方法會從 DocumentDB 提取項目，以
傳遞回 ItemController，接著傳遞給 [編輯] 檢視。

我們剛剛新增的這兩個方法中的第二個方法會將
DocumentDB 中的文件取代為從 ItemController 傳入的
Document 版本。

全部到此完成，這些就是我們執行應用程式所需的所有作業：列出
未完成項目、新增項目，最後是編輯項目。

</h3>
# <a name="_Toc395637773">在本機執行您的應用程式</a>

### 

若要在本機電腦上測試應用程式，請在 Visual Studio 中按 F5 鍵，
這樣應該會建置應用程式並啟動含有先前所看過之空白方格頁面的
瀏覽器：

![替代文字][23]

1\. 使用針對 [項目]、[項目名稱] 和 [類別] 提供的欄位來輸入
資訊，然後按一下 [建立新物件] 連結並提供
一些值。讓 [已完成] 核取方塊保持未選取狀態，否則，會新增處於已完成狀態的新項目，
這樣一來，該項目就不會出現在初始清單中。

![替代文字][24]

如果按 [建立]，則會將您重新導向回 [索引] 頁面，而且
您的項目應該會顯示在 [清單] 中。

![替代文字][25]

依需要新增一些項目至 [待辦事項] 清單。

2\. 按一下清單上 [項目] 旁邊的 [編輯]，這樣會將您帶至
[編輯] 檢視，供您更新物件屬性
(包括 [已完成] 旗標)。這會將項目有效地標記為
已完成，並將該項目從未完成工作的清單中移除。

![替代文字][26]

3\. 若要完成工作，您只需要核取此核取方塊，然後按一下
[儲存]。這樣會將您重新導向回清單頁面，且此清單中現在不會再出現
該項目。

</h3>
# <a name="_Toc395637774">將應用程式部署至 Azure 網站</a>

### 

您已經擁有可在
DocumentDB 正常運作的完整應用程式，我們現在要將此應用程式部署至 Azure 網站。

如果您在建立空白 ASP.NET MVC 專案時選取了 [Host in the cloud]，
Visual Studio 可讓這項作業變得十分簡單，為您完成
大部分的工作。若要發佈此應用程式，您只需要
在 [方案總管] 的 [專案] 上按一下滑鼠右鍵 (請確定您已沒有在本機上執行此應用程式)，
然後選取 [發佈]。

![替代文字][27]

系統應該已根據您的認證設定好所有項目；
實際上，系統已在 Azure 中
建立位於所顯示 [目的地 URL] 的網站，您只需要按一下 [發佈] 即可。

![替代文字][28]

幾秒後，Visual Studio 就會發佈好 Web
應用程式並啟動瀏覽器，您可以在其中看到您方便好用的應用程式
已在 Azure 中執行！

</h3>
# <a name="_Toc395637775">結論</a>

### 

恭喜！您剛剛已經使用 Azure DocumentDB 建置您的第一個 ASP.NET MVC 應用程式，並將它發佈至 Azure 網站。您可以在這裡下載完整參考應用程式的原始程式碼。

[這裡] (<http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409>) (英文)

  [替代文字]: ./media/documentdb-dotnet-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/zh-tw/products/visual-studio-express-vs.aspx
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [1]: ./media/documentdb-dotnet-application/image2.png
  [2]: ./media/documentdb-dotnet-application/image3.png
  [3]: ./media/documentdb-dotnet-application/image4.png
  [4]: ./media/documentdb-dotnet-application/image5.png
  [5]: ./media/documentdb-dotnet-application/image6.png
  [6]: ./media/documentdb-dotnet-application/image7.png
  [7]: ./media/documentdb-dotnet-application/image8.png
  [8]: ./media/documentdb-dotnet-application/image9.png
  [9]: ./media/documentdb-dotnet-application/image10.png
  [10]: ./media/documentdb-dotnet-application/image11.png
  [11]: ./media/documentdb-dotnet-application/image12.png
  [12]: ./media/documentdb-dotnet-application/image13.png
  [13]: ./media/documentdb-dotnet-application/image14.png
  [14]: ./media/documentdb-dotnet-application/image15.png
  [15]: ./media/documentdb-dotnet-application/image16.png
  [16]: ./media/documentdb-dotnet-application/image17.png
  [17]: ./media/documentdb-dotnet-application/image18.png
  [18]: ./media/documentdb-dotnet-application/image19.png
  [19]: ./media/documentdb-dotnet-application/image20.png
  [20]: ./media/documentdb-dotnet-application/image21.png
  [21]: ./media/documentdb-dotnet-application/image22.png
  [22]: ./media/documentdb-dotnet-application/image23.png
  [23]: ./media/documentdb-dotnet-application/image24.png
  [24]: ./media/documentdb-dotnet-application/image25.png
  [25]: ./media/documentdb-dotnet-application/image26.png
  [26]: ./media/documentdb-dotnet-application/image27.png
  [27]: ./media/documentdb-dotnet-application/image28.png
  [28]: ./media/documentdb-dotnet-application/image29.png
