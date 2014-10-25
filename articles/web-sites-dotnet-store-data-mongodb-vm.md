<properties linkid="develop-dotnet-website-with-mongodb-vm" urlDisplayName="Website with MongoDB VM" pageTitle=".NET website with MongoDB on a virtual machine - Azure" metaKeywords="Azure Git ASP.NET MongoDB, Git .NET, Git MongoDB, ASP.NET MongoDB, Azure MongoDB, Azure ASP.NET, Azure tutorial" description="A tutorial that teaches you how to use Git to deploy an ASP.NET app to an Azure website connected to MongoDB on a virtual machine." metaCanonical="" services="web-sites,virtual-machines" documentationCenter=".NET" title="Create an Azure website that connects to MongoDB running on a virtual machine in Azure" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# 建立 Azure 網站以便連接至在 Azure 的虛擬機器上執行的 MongoDB

使用 Git，即可將 ASP.NET 應用程式部署至 Azure 網站。在本教學課程中，您將建置簡易的前端 ASP.NET MVC 工作清單應用程式，以連接至在 Azure 的虛擬機器中執行的 MongoDB 資料庫。[MongoDB][] (英文) 是受歡迎的高效能開放原始碼 NoSQL 資料庫。在開發電腦上執行和測試 ASP.NET 應用程式之後，您將使用 Git 將此應用程式上傳至 Azure 網站。

[WACOM.INCLUDE [create-account-and-websites-and-vms-note][]]

## 概觀

在本教學課程中，您將：

-   [建立虛擬機器和安裝 MongoDB][]
-   [在部署電腦上建立和執行 My Task List ASP.NET 應用程式][]
-   [建立 Azure 網站][]
-   [使用 Git 將 ASP.NET 應用程式部署至網站][]

## 背景知識

下列知識雖然並非必要，但是對本教學課程很實用：

-   MongoDB 的 C# 驅動程式。如需針對 MongoDB 開發 C# 應用程式的詳細資訊，請參閱 MongoDB＜[CSharp Language Center][]＞(英文)。
-   ASP .NET Web 應用程式架構。您可以在 [ASP.net 網站][]了解此架構。
-   ASP .NET MVC Web 應用程式架構。您可以在 [ASP.NET MVC 網站][]了解此架構。
-   Azure。您可以在 [Azure][] 開始進行讀取。

## 準備工作

您將在這一節中了解如何在 Azure 中建立虛擬機器和安裝 MongoDB，然後設定您的開發環境。

<span id="virtualmachine"></span></a>

### 建立虛擬機器和安裝 MongoDB

本教學課程假設您已在 Azure 中建立虛擬機器。建立虛擬機器之後，您必須在這部虛擬機器上安裝 MongoDB：

-   若要建立 Windows 虛擬機器和安裝 MongoDB，請參閱＜[在 Azure 中執行 Windows Server 的虛擬機器上安裝 MongoDB][]＞。
-   此外，若要建立 Linux 虛擬機器和安裝 MongoDB，請參閱＜[在 Azure 中執行 CentOS Linux 的虛擬機器上安裝 MongoDB][]＞。

在 Azure 中建立虛擬機器並安裝 MongoDB 之後，請務必記住虛擬機器的 DNS 名稱 (例如，"testlinuxvm.cloudapp.net") 以及您在端點中指定之 MongoDB 的外部連接埠。稍後在教學課程中需要此資訊。

### 安裝 Visual Studio

從安裝和執行 [Visual Studio Express 2013 for Web][] 或 [Visual Studio 2013][] 著手。

Visual Studio 為整合式開發環境 (IDE)。就如同您使用 Microsoft Word 來撰寫文件，您會使用 IDE 來建立應用程式。本教學課程採用 Microsoft Visual Studio 2013，但您可以使用 Microsoft Visual Studio Express 2013，這是免費的 Microsoft Visual Studio 版本。

<span id="createapp"></span></a>

## 在部署電腦上建立和執行 My Task List ASP.NET 應用程式

在這一節中，您將使用 Visual Studio 建立名為 "My Task List" 的 ASP.NET 應用程式。您會在本機執行此應用程式，但該應用程式將會連接至 Azure 上的虛擬機器並使用您在那裡建立的 MongoDB 執行個體。

### 建立應用程式

在 Visual Studio 中按一下 [新增專案]。

![Start Page New Project][]

在 [新增專案] 視窗的左窗格中，選取 [Visual C#]，然後選取 [Web]。在中間窗格中，選取 [ASP.NET Web 應用程式]。在底部將您的專案命名為 "MyTaskListApp"，然後按一下 [確定]。

![New Project Dialog][]

在 [新增 ASP.NET 專案] 對話方塊中，選取 [MVC]，然後按一下 [確定]。

![Select MVC Template][]

在專案完成後，範本所建立的預設頁面隨即出現。

![Default ASP.NET MVC Application][]

### 安裝 MongoDB C# 驅動程式

MongoDB 透過驅動程式提供 C# 應用程式的用戶端支援，您必須將該驅動程式安裝在本機開發電腦上。C# 驅動程式可透過 NuGet 取得。

若要安裝 MongoDB C# 驅動程式：

1.  在 \[方案總管] 的 **MyTaskListApp** 專案之下，於 [參考] 上按一下滑鼠右鍵並選取 \[Manage NuGet Packages]。

    ![Manage NuGet Packages][]

2.  在 \[Manage NuGet Packages] 視窗的左窗格中，按一下 [線上]。在右邊的 [線上搜尋] 方塊中，輸入 "mongocsharpdriver"。按一下 [安裝] 以安裝驅動程式。

    ![Search for MongoDB C# Driver][]

3.  按一下 [我接受] 以接受 10gen, Inc. 授權條款。

4.  在安裝驅動程式後，按一下 [關閉]。
    ![MongoDB C# 驅動程式已安裝][]

MongoDB C# 驅動程式現已安裝。**MongoDB.Driver.dll** 和 **MongoDB.Bson.dll** 程式庫的參考便已新增至專案。

![MongoDB C# Driver References][]

### 新增模型

在 \[方案總管] 中，於 *Models* 資料夾上按一下滑鼠右鍵，然後 [新增] 一個新 [類別]，將其命名為 *TaskModel.cs*。在 *TaskModel.cs*，以下列程式碼取代現有程式碼：

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MongoDB.Bson.Serialization.Attributes;
    using MongoDB.Bson.Serialization.IdGenerators;
    using MongoDB.Bson;

    namespace MyTaskListApp.Models
    {
        public class MyTask
        {
            [BsonId(IdGenerator = typeof(CombGuidGenerator))]
            public Guid Id { get; set; }

            [BsonElement("Name")]
            public string Name { get; set; }

            [BsonElement("Category")]
            public string Category { get; set; }

            [BsonElement("Date")]
            public DateTime Date { get; set; }

            [BsonElement("CreatedDate")]
            public DateTime CreatedDate { get; set; }

        }
    }

### 新增資料存取層

在 \[方案總管] 中，於 *MyTaskListApp* 專案上按一下滑鼠右鍵，然後 [新增] 一個名為 *DAL* 的 [新資料夾]。於 *DAL* 資料夾上按一下滑鼠右鍵，然後 [新增] 一個新 [類別]。將類名命名為 *Dal.cs*。在 *Dal.cs*，以下列程式碼取代現有程式碼：

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using System.Configuration;

    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            private MongoServer mongoServer = null;
            private bool disposed = false;

            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net"
            private string connectionString = "mongodb://<vm-dns-name>";

            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";

            // Default constructor.        
            public Dal()
            {
            }        

            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    MongoCollection<MyTask> collection = GetTasksCollection();
                    return collection.FindAll().ToList<MyTask>();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask >();
                }
            }

            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                MongoCollection<MyTask> collection = GetTasksCollectionForEdit();
                try
                {
                    collection.Insert(task, SafeMode.True);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }

            private MongoCollection<MyTask> GetTasksCollection()
            {
                MongoServer server = MongoServer.Create(connectionString);
                MongoDatabase database = server[dbName];
                MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }

            private MongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoServer server = MongoServer.Create(connectionString);
                MongoDatabase database = server[dbName];
                MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
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

### 新增控制器

在 \[方案總管] 中開啟 *Controllers\\HomeController.cs* 檔案，然後以下列程式碼取代現有程式碼：

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.Mvc;
    using MyTaskListApp.Models;
    using System.Configuration;

    namespace MyTaskListApp.Controllers
    {
        public class HomeController : Controller, IDisposable
        {
            private Dal dal = new Dal();
            private bool disposed = false;
            //
            // GET: /MyTask/

            public ActionResult Index()
            {
                return View(dal.GetAllTasks());
            }

            //
            // GET: /MyTask/Create

            public ActionResult Create()
            {
                return View();
            }

            //
            // POST: /MyTask/Create

            [HttpPost]
            public ActionResult Create(MyTask task)
            {
                try
                {
                    dal.CreateTask(task);
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

### 設定網站樣式

若要變更頁面最上方的標題，請在 \[方案總管] 中開啟 *Views\\Shared\\\_Layout.cshtml* 檔案，並以 "My Task List Application" 取代導覽列標題中的 "Application name"，使其看起來如下：

    @Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

若要設定 Task List 功能表，請開啟 *\\Views\\Home\\Index.cshtml* 檔案並以下列程式碼取代現有程式碼：

    @model IEnumerable<MyTaskListApp.Models.MyTask>

    @{
        ViewBag.Title = "My Task List";
    }

    <h2>My Task List</h2>

    <table border="1">
        <tr>
            <th>Task</th>
            <th>Category</th>
            <th>Date</th>
            
        </tr>

    @foreach (var item in Model) {
        <tr>
            <td>
                @Html.DisplayFor(modelItem => item.Name)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Category)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Date)
            </td>
            
        </tr>
    }

    </table>
    <div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>

若要新增建立新工作的功能，請在 *Views\\Home\\* 資料夾上按一下滑鼠右鍵，然後 [新增] 一個 [檢視]。將檢視命名為 *Create*。使用下列程式碼來取代此程式碼：

    @model MyTaskListApp.Models.MyTask

    <script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>

    @using (Html.BeginForm("Create", "Home")) {
        @Html.ValidationSummary(true)
        <fieldset>
            <legend>New Task</legend>

            <div class="editor-label">
                @Html.LabelFor(model => model.Name)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Name)
                @Html.ValidationMessageFor(model => model.Name)
            </div>

            <div class="editor-label">
                @Html.LabelFor(model => model.Category)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Category)
                @Html.ValidationMessageFor(model => model.Category)
            </div>

            <div class="editor-label">
                @Html.LabelFor(model => model.Date)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Date)
                @Html.ValidationMessageFor(model => model.Date)
            </div>

            <p>
                <input type="submit" value="Create" />
            </p>
        </fieldset>
    }

\[方案總管] 看起來如下所示：

![方案總管][]

### 設定 MongoDB 連接字串

在 \[方案總管] 中開啟 *DAL/Dal.cs* 檔案。尋找下列程式碼行：

    private string connectionString = "mongodb://<vm-dns-name>";

以執行 MongoDB 之虛擬機器的 DNS 名稱來取代 `<vm-dns-name>`，該 MongoDB 是您在本教學課程的[建立虛擬機器和安裝 MongoDB][] 步驟中所建立的資料庫。若要尋找虛擬機器的 DNS 名稱，請移至 Azure 管理入口網站，選取 [虛擬機器]，然後尋找 [DNS 名稱]。

如果虛擬機器的 DNS 名稱為 "testlinuxvm.cloudapp.net"，且 MongoDB 是在預設連接埠 27017 上接聽，則連接字串程式碼行如下所示：

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

如果虛擬機器端點為 MongoDB 指定不同的外部連接埠，您可以在連接字串中指定連接埠：

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

如需 MongoDB 連接字串的詳細資訊，請參閱＜[連接][]＞(英文)。

### 測試本機部署

若要在部署電腦上執行您的應用程式，請選取 [偵錯] 功能表中的 [開始偵錯] 或按 **F5**。IIS Express 會啟動，瀏覽器會開啟並啟動應用程式的首頁。您可以新增一項工作，該工作將會新增至在 Azure 中您的虛擬機器上執行的 MongoDB 資料庫。

![My Task List Application][]

## <span class="short-header">將應用程式部署至 Azure 網站</span>將 ASP.NET 應用程式部署至 Azure 網站

在本節中，您將建立網站並使用 Git 部署 My Task List ASP.NET 應用程式。

<span id="createwebsite"></span></a>

### 建立 Azure 網站

在本節中，您將會建立 Azure 網站。

1.  開啟網頁瀏覽器並瀏覽至 [Azure 管理入口網站][]。使用您的 Azure 帳戶進行登入。
2.  按一下頁面底部的 [+New]，然後按一下 [網站]，最後按一下 [快速建立]。
3.  輸入應用程式 URL 的唯一前置詞。
4.  選取區域。
5.  按一下 [建立網站]。

![Create a new web site][]

1.  您的網站將會迅速建立並且列在 [網站] 中。

![WAWSDashboardMyTaskListApp][]

<span id="deployapp"></span></a>

### 使用 Git 將 ASP.NET 應用程式部署至網站

在本節中，您將會使用 Git 部署 My Task List 應用程式。

1.  按一下 [網站] 中您的網站名稱，然後按一下 [儀表板]。在右側的 [Quick Glance] 之下，按一下 [設定從原始檔控制進行部署]。
2.  在 [Where is your source code?] 頁面上，選擇 [Local Git repository]，然後按 [下一步] 箭頭。
3.  Git 儲存機制應會迅速建立起來。請記下結果頁面上的指示，您將在下一節中使用這些指示。

    ![Git Repository is Ready][]

4.  [Push my local files to Azure] 底下有將程式碼發佈至 Azure 的相關指示。指示類似如下範例：

    ![Push local files to Azure][]

5.  如果您未安裝 Git，請使用步驟 1 的 [Get it here] 連結進行安裝。
6.  遵循步驟 2 的指示，認可您的本機檔案。
7.  遵循步驟 3 的指示，新增遠端 Azure 儲存機制並將您的檔案發佈至 Azure 網站。
8.  完成部署時，您將會看到下列確認：

    ![Deployment Complete][]

9.  您的 Azure 網站現在即可使用。檢查網站的 [儀表板] 頁面和 [網站 URL] 欄位，以尋找您網站的 URL。遵循本教學課程中的程序，您的網站將會以下列 URL 開始提供服務：<http://mytasklistapp.azurewebsites.net>。

## 摘要

您現在已成功將 ASP.NET 應用程式部署至 Azure 網站。若要檢視網站，請按一下 [儀表板] 頁面的 [網站 URL] 欄位。如需針對 MongoDB 開發 C# 應用程式的詳細資訊，請參閱＜[CSharp Language Center][]＞(英文)。

<!-- HYPERLINKS --> 
<!-- IMAGES --> 
<!-- TOC BOOKMARKS -->

  [MongoDB]: http://www.mongodb.org
  [create-account-and-websites-and-vms-note]: ../includes/create-account-and-websites-and-vms-note.md
  [建立虛擬機器和安裝 MongoDB]: #virtualmachine
  [在部署電腦上建立和執行 My Task List ASP.NET 應用程式]: #createapp
  [建立 Azure 網站]: #createwebsite
  [使用 Git 將 ASP.NET 應用程式部署至網站]: #deployapp
  [CSharp Language Center]: http://docs.mongodb.org/ecosystem/drivers/csharp/
  [ASP.net 網站]: http://www.asp.net/
  [ASP.NET MVC 網站]: http://www.asp.net/mvc
  [Azure]: http://www.windowsazure.com
  [在 Azure 中執行 Windows Server 的虛擬機器上安裝 MongoDB]: /zh-TW/manage/windows/common-tasks/install-mongodb/
  [在 Azure 中執行 CentOS Linux 的虛擬機器上安裝 MongoDB]: /zh-TW/manage/linux/common-tasks/mongodb-on-a-linux-vm/
  [Visual Studio Express 2013 for Web]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
  [Visual Studio 2013]: http://www.microsoft.com/visualstudio/eng/2013-downloads
  [Start Page New Project]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
  [New Project Dialog]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
  [Select MVC Template]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
  [Default ASP.NET MVC Application]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
  [Manage NuGet Packages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
  [Search for MongoDB C# Driver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
  [MongoDB C# 驅動程式已安裝]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
  [MongoDB C# Driver References]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
  [方案總管]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
  [連接]: http://www.mongodb.org/display/DOCS/Connections
  [My Task List Application]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [Create a new web site]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
  [WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
  [Git Repository is Ready]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
  [Push local files to Azure]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
  [Deployment Complete]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png
