<properties linkid="develop-net-tutorials-website-with-mongodb-mongolab" urlDisplayName="Web Site with MongoDB on MongoLab" pageTitle="Create a Web Site that uses MongoDB on MongoLab (.NET)" metaKeywords="" description="Learn how to create an Azure web site that stores data in MongoDB hosted by MongoLab." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create a C# ASP.NET Application on Azure with MongoDB using the MongoLab Add-On" authors="" solutions="" manager="" editor="" />

使用 MongoLab 附加元件在有 MongoDB 的 Azure 上建立 C\# ASP.NET 應用程式
=======================================================================

*作者：Eric Sedor，MongoLab*

各位冒險家，大家好！歡迎使用 MongoDB-as-a-Service。在本教學課程中，您將：

1.  [佈建資料庫](#provision) - Azure Store [MongoLab](http://mongolab.com) 附加元件將提供 MongoLab 的雲端資料庫平台所管理的 Azure 雲端中代管的 MongoDB 資料庫。
2.  [建立應用程式](#create) - 它將是用於記事的簡單 C\# ASP.NET MVC 應用程式。
3.  [部署應用程式](#deploy) - 結合多個組態勾點有助於推送程式碼。
4.  [管理資料庫](#manage) - 最後，我們將展示 MongoLab 的 Web 型資料庫管理入口網站，您可在其中輕鬆搜尋、呈現和修改資料。

在本教學課程的任何時間有任何問題，歡迎寄送電子郵件至 <support@mongolab.com>。

快速啟動
--------

如果已經有要使用的 Azure 應用程式和網站，或者您比較熟悉 Azure Store，都可以使用本節快速啟動。若非如此，請進行以下的[佈建資料庫](#provision)。

1.  開啟 Azure Store。

    ![存放區](./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-store.png)
2.  購買 MongoLab 附加元件。

    ![MongoLab](./media/partner-mongodb-web-sites-dotnet-use-mongolab/entry-mongolab.png)
3.  按一下 [附加元件] 清單中的 MongoLab 附加元件，然後按一下 **[連線資訊]**。

    ![ConnectionInfoButton](./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-connectioninfo.png)
4.  將 MONGOLAB\_URI 複製到剪貼簿。

    ![ConnectionInfoScreen](./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab_connectioninfo.png)
     **此 URI 包含您的資料庫使用者名稱和密碼。這是機密資訊，請勿洩露。**
5.  將該值新增到 Azure Web 應用程式的 [組態] 功能表之中出現的 [連接字串] 清單：

    ![WebSiteConnectionStrings](./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-websiteconnectionstring.png)
6.  在 **[名稱]** 中，輸入 MONGOLAB\_URI。
7.  在 **[值]** 中，貼上我們在上一節取得的連接字串。
8.  在 [類型] 下拉式清單中選取 **[自訂]** (而非預設的 **SQLAzure**)。
9.  在 Visual Studio 中，選取 [工具] \> [Library Package Manager] \> **[Package Manager Console]** 安裝 Mongo C\# 驅動程式。在 PM 主控台上，輸入 **Install-Package mongocsharpdriver**，並按下 **Enter**。
10. 在程式碼中設置勾點，以便從環境變數取得 MongoLab 連線 URI：

        using MongoDB.Driver;  
 		...
 		private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
 		...
 		MongoServer server = MongoServer.Create(connectionString);  

    注意：Azure 會在原始宣告的連線字串前面加上 **CUSTOMCONNSTR\_** 首碼，所以程式碼會參考 **CUSTOMCONNSTR\_MONGOLAB\_URI.** 而非 **MONGOLAB\_URI**。

現在開始完整教學課程...

佈建資料庫
----------

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

建立應用程式
------------

在本節中，您將了解如何建立 C\# ASP.NET Visual Studio 專案，並逐步使用 C\# MongoDB 驅動程式建立簡單 Note 應用程式。您會想要造訪您的網站並寫下記事，然後檢視所有留下的記事。

您將使用 Visual Studio Express 2012 for Web 進行這項開發。

### 建立專案

範例應用程式將先使用 Visual Studio 範本。務必使用 .NET Framework 4.0。

1.  選取 [檔案] \> **[新增專案]**。[新增專案] 對話方塊隨即顯示：

     ![NewProject](./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-csharp-newproject.png)
2.  選取 [已安裝] \> [範本] \> [Visual C\#] \> **[Web]**。
3.  從 .NET 版本下拉式功能表中，選取 **[.NET Framework 4]** (*附註：Framework 4.5 目前不適用*)。

    ![ProjectFramework](./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-dotNet-Framework4-mongolab.png)
4.  選擇 **[ASP.NET MVC 4 Web 應用程式]**。
5.  輸入 *mongoNotes* 做為**專案名稱**。如果您選擇不同的名稱，您需要修改整個教學課程中提供的程式碼。
6.  按一下 **[確定]**。[專案範本] 對話方塊隨即顯示：

    ![ProjectTemplate](./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-csharp-projecttemplate.png)
7.  選取 **[網際網路應用程式]** 並按一下 **[確定]**。隨即建立專案。
8.  選取 [工具] \> [Library Package Manager] \> **[Package Manager Console]**。在 PM 主控台上，輸入 **Install-Package mongocsharpdriver**，並按下 **Enter**。

     ![PMConsole](./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-csharp-pmconsole.png)
     MongoDB C\# 驅動程式隨即整合到專案中，而且下列一行將自動新增至 *packages.config* 檔案：

        < package id="mongocsharpdriver" version="1.8" targetFramework="net40" / >

### 新增記事模型

首先建立 Notes 的模型，其中只有簡單的日期和文字內容。

1.  在 [方案總管] 中以滑鼠右鍵按一下 **[模型]**，並選取 [新增] \> **[類別]**。將這個新類別命名為 *Note.cs*。
2.  將對於此類別自動產生的程式碼改為以下內容：

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Web;
		using MongoDB.Bson.Serialization.Attributes;
		using MongoDB.Bson.Serialization.IdGenerators;
		using MongoDB.Bson;
				
		namespace mongoNotes.Models
		{
		    public class Note
		    {
		        public Note()
		        {
		            Date = DateTime.UtcNow;
		        }
	            
	            private DateTime date;
		
		        [BsonId(IdGenerator = typeof(CombGuidGenerator))]
		        public Guid Id { get; set; }
		
		        [BsonElement("Note")]
		        public string Text { get; set; }
		
		        [BsonElement("Date")]
		        public DateTime Date {
		            get { return date.ToLocalTime(); }
		            set { date = value;}
		        }
		    }
		}

### 新增資料存取層

務必建立存取 MongoDB 的方法以擷取和儲存記事。您的資料存取層將使用 Note 模型，此後將繫結到 HomeController。

1.  在 [方案總管] 中以滑鼠右鍵按一下 **[mongoNotes]**，並選取 [新增] \> **[新增資料夾]**。將資料夾命名為 **DAL**。
2.  在 [方案總管] 中以滑鼠右鍵按一下 **[DAL]**，並選取 [新增] \> **[類別]**。將這個新類別命名為 *Dal.cs*。
3.  將對於此類別自動產生的程式碼改為以下內容：

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Web;
		using mongoNotes.Models;
		using MongoDB.Driver;
		using System.Configuration;

		namespace mongoNotes
		{
		    public class Dal : IDisposable
		    {
		        private MongoServer mongoServer = null;
		        private bool disposed = false;
		
		        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
		
		        private string dbName = "myMongoApp";
		        private string collectionName = "Notes";
		
		        // Default constructor.        
		        public Dal()
		        {
		        }
		   
		        public List<Note> GetAllNotes()
		        {
		            try
		            {
		                MongoCollection<Note> collection = GetNotesCollection();
		                return collection.FindAll().ToList<Note>();
		            }
		            catch (MongoConnectionException)
		            {
		                return new List<Note>();
		            }
		        }
		
		        // Creates a Note and inserts it into the collection in MongoDB.
		        public void CreateNote(Note note)
		        {
		            MongoCollection<Note> collection = getNotesCollectionForEdit();
		            try
		            {
		                collection.Insert(note, SafeMode.True);
		            }
		            catch (MongoCommandException ex)
		            {
		                string msg = ex.Message;
		            }
		        }
		
		        private MongoCollection<Note> GetNotesCollection()
		        {
		            MongoServer server = MongoServer.Create(connectionString);
		            MongoDatabase database = server[dbName];
		            MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
		            return noteCollection;
		        }
		
		        private MongoCollection<Note> getNotesCollectionForEdit()
		        {
		            MongoServer server = MongoServer.Create(connectionString);
		            MongoDatabase database = server[dbName];
		            MongoCollection<Note> notesCollection = database.GetCollection<Note>(collectionName);
		            return notesCollection;
		        }
		
		        # region IDisposable
		
		        public void Dispose()
		        {
		            this.Dispose(true);
		            GC.SuppressFinalize(this);
		        }
		
		        protected virtual void Dispose(bool disposing)
		        {
		            if (!this.disposed)
		            {
		                if (disposing)
		                {
		                    if (mongoServer != null)
		                    {
		                        this.mongoServer.Disconnect();
		                    }
		                }
		            }
		
		            this.disposed = true;
		        }
		
		        # endregion
		    }
		}

4.  請注意下列程式碼：
			
		private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
		private string dbName = "myMongoApp"; 

    您將在此存取稍後將佈建的環境變數。如果您基於開發目的而執行本機 mongo 實例，則您可能會想要暫時將此值設定為「localhost」。

	另外，也設定資料庫名稱。具體而言，將 **dbName** 值設定為在佈建 MongoLab 附加元件時輸入的名稱。

1. 最後，檢查 **GetNotesCollection()** 中的下列程式碼：

        MongoServer server = MongoServer.Create(connectionString);
        MongoDatabase database = server[dbName];
        MongoCollection<Note> notesCollection = database.GetCollection<Note>  

	這不需要任何變更；只要了解這只是呈現如何以 MongoCollection 物件執行插入、更新和查詢，如 **GetAllNotes()** 中的以下程式碼所示：

        collection.FindAll().ToList<Note>();

如需利用 MongoDB C\# 驅動程式的詳細訊息，請參閱 mongodb.org 的 [CSharp 驅動程式快速入門](http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "CSharp 驅動程式快速入門") (英文)。

### 新增建立檢視

您現在將新增建立新記事的檢視。

1.  在 [方案總管] 中以滑鼠右鍵按一下 [檢視] \> **[首頁]** 項目，並選取 [新增] \> **[檢視]**。將這個新檢視命名為 **Create**，並且按一下 **[新增]**。
2.  將對於此檢視 (**Create.cshtml**) 自動產生的程式碼改為以下內容：

		@model mongoNotes.Models.Note
		
		<script src="@Url.Content("~/Scripts/jquery-1.5.1.min.js")" type="text/javascript"></script>
		<script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
		<script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
		
		@using (Html.BeginForm("Create", "Home")) {
		    @Html.ValidationSummary(true)
		    <fieldset>
		        <legend>New Note</legend>
		        <h3>New Note</h3>		
		        <div class="editor-label">
		            @Html.LabelFor(model => model.Text)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.Text)
		        </div>
		       <p>
		       	    <input type="submit" value="Create" />
		   	   </p>
		   </fieldset>
		}

### 修改 index.cshtml

接著，在您的網站上放置一個用於檢視和建立記事的簡單配置。

1.  在 [檢視] \> **[首頁]** 下開啟 **Index.cshtml**，然後將其中的內容改為以下內容：

		@model IEnumerable<mongoNotes.Models.Note>
		
		@{
		    ViewBag.Title = "Notes";
		}
		
		<h2>My Notes</h2>

		<table border="1">
		    <tr>
		        <th>Date</th>
		        <th>Note Text</th>		
		    </tr>
		
		@foreach (var item in Model) {
		    <tr>
		        <td>
		            @Html.DisplayFor(modelItem => item.Date)
		        </td>
		        <td>
		            @Html.DisplayFor(modelItem => item.Text)
		        </td>		
		    </tr>
		}
		
		</table>
		<div>  @Html.Partial("Create", new mongoNotes.Models.Note())</div>

### 修改 HomeController.cs

最後，HomeController 需要將您的資料存取層具現化，然後將它應用於您的檢視。

1.  在 [方案總管] 的 **[控制器]** 下開啟 **HomeController.cs**，然後將其中的內容改為以下內容：

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Web;
		using System.Web.Mvc;
		using mongoNotes.Models;
		using System.Configuration;
		
		namespace mongoNotes.Controllers
		{
		    public class HomeController : Controller, IDisposable
		    {
		        private Dal dal = new Dal();
		        private bool disposed = false;
		        //
		        // GET: /Task/
		
		        public ActionResult Index()
		        {
		            return View(dal.GetAllNotes());
		        }
		
		        //
		        // GET: /Task/Create
		
		        public ActionResult Create()
		        {
		            return View();
		        }
		
		        //
		        // POST: /Task/Create
		
		        [HttpPost]
		        public ActionResult Create(Note note)
		        {
		            try
		            {
		                dal.CreateNote(note);
		                return RedirectToAction("Index");
		            }
		            catch
		            {
		                return View();
		            }
		        }
		
		        public ActionResult About()
		        {
		            return View();
		        }
		
		        # region IDisposable
		
		        new protected void Dispose()
		        {
		            this.Dispose(true);
		            GC.SuppressFinalize(this);
		        }
		
		        new protected virtual void Dispose(bool disposing)
		        {
		            if (!this.disposed)
		            {
		                if (disposing)
		                {
		                    this.dal.Dispose();
		                }
		            }
		
		            this.disposed = true;
		        }
		
		        # endregion
		
		    }
		}

部署應用程式
------------

開發應用程式之後，即可建立 Azure 網站來代管應用程式、設定該網站，並部署程式碼。本節的重點是使用 MongoDB 連線字串 (URI)。您將使用此 URI 在您的網站中設定環境變數，使該 URI 與您的程式碼分開。您應將該 URI 視為機密訊息，因為其中包含連線至您資料庫的認證。

### 建立新網站並取得發行設定檔案

在 Azure 中建立網站非常簡便，尤其是 Azure 會為 Visual Studio 自動產生發行設定檔。

1.  在 Azure 入口網站，按一下 **[新增]**。

    ![新增](./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-new.png)
2.  按一下 [運算] \> [網站] \> **[快速建立]**。

    ![CreateSite](./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-newwebsite.png)
3.  輸入 URL 首碼。選取所需的名稱，但是請注意，這必須是唯一的名稱 (可能無法使用「mongoNotes」)。
4.  按一下 **[建立網站]**。
5.  網站建立完成時，按一下網站清單中的網站名稱。網站儀表板隨即顯示。

    ![WebSiteDashboard](./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-websitedashboard.png)
6.  在 **[Quick Glance]** 下按一下「 **[Download publish profile]**，然後將 .PublishSettings 檔案儲存到所選的目錄。

    ![DownloadPublishProfile](./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-website-downloadpublishprofile.png)

### 取得 MongoLab 連線字串

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

### 將連線字串新增到網站的環境變數

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

### 發行網站

1.  在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下 **mongoNotes** 專案，並選取 **[發行]**。[發行] 對話方塊隨即顯示：

    ![發行](./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-vspublish.png)
2.  按一下 **[匯入]**，並且從所選的下載目錄中選取 .PublishSettings 檔案。此檔案將自動填入 [發行] 對話方塊中的值。
3.  按一下 **[驗證連線]** 測試檔案。
4.  驗證成功後，按一下**[發行]**。發行完成後，將開啟新的瀏覽器索引標籤，其中顯示該網站。
5.  輸入一些記事文字，並按一下 **[建立]**，然後查看結果！

    ![HelloMongoAzure](./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-sampleapp.png)

管理資料庫
----------

[WACOM.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

恭喜！您已經啟動由 MongoLab 代管的 MongoDB 資料庫支援的 C\# ASP.NET 應用程式！現在，您擁有 MongoLab 資料庫，如果對於資料庫有任何問題或疑慮，或者要取得 MongoDB 或 C\# 驅動程式的協助，可連絡 <support@mongolab.com>。祝您好運！

