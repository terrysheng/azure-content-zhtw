<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="使用 DocumentDB 建置具有 ASP.NET MVC 的 Web 應用程式 | Azure" description="了解如何使用 DocumentDB 建立待辦事項清單 Web 應用程式。您將從 Azure 主控的 ASP.NET MVC Web 應用程式儲存和存取資料。"  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />


<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="ryancraw" />

<a name="_Toc395809351">Build a web application with ASP.NET MVC using DocumentDB</a>
=======================================================================================================
<a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Overview</a>
==============================================================================================

<a name="_Toc395637759">Scenario</a>
------------------------------------

To highlight how customers can efficiently leverage Azure DocumentDB to store and query JSON documents, this document provides an end-to-end walk-through of building a ToDo List web application using Azure Document DB.

This walk-through shows you how to use DocumentDB service provided by Azure to store and access data from an ASP.NET MVC web application hosted on Azure and presumes that you have some prior experience using ASP.NET MVC and Azure Websites.

You will learn:

1\. Creating and provisioning a DocumentDB Account

2\. Creating a ASP.NET MVC Application

3\. Connecting to and using Azure DocumentDB from your web application

4\. Deploying the Web Application to Azure Websites

By following this walk-through, you will build a simple web-based task-management application that allows creating, retrieving and completing of tasks. The tasks will be stored as JSON documents in Azure DocumentDB.

![Alt text](./media/documentdb-dotnet-application/image1.png)


<a name="_Toc395637760">Prerequisites</a>
================================================================

Before following the instructions in this article, you should ensure that you have the following installed:

Git for Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (or [Visual Studio Express][] which is the free version)

Azure SDK for .NET version 2.3 or higher, available through [Microsoft Web Platform Installer][]

All the screen shots in this document have been taken using Visual Studio 2013 with Update 3 applied and Azure SDK for .NET version 2.4. If your system is configured with different versions it is possible that your screens and options won't match entirely, but if you meet the above prerequisites this solution should work.

<a name="_Toc395637761">Create a DocumentDB database account</a>
================================================================

To provision a DocumentDB database account in Azure, open the Azure Management Portal and either Click the Azure Gallery tile on the homepage or click "+" in the lower left hand corner of the screen.

![Alt text](./media/documentdb-dotnet-application/image2.png)


This will open the Azure Gallery, where you can select from the many available Azure services. In the Gallery, select "Data, storage and backup" from the list of categories.

![Alt text](./media/documentdb-dotnet-application/image3.png)

From here, select the option for Azure DocumentDB

![Alt text](./media/documentdb-dotnet-application/image4.png)


Then select "Create" from the bottom of the screen

![Alt text](./media/documentdb-dotnet-application/image5.png)

This will open up the "New DocumentDB" blade where you can specify the name, region, scale, resource group and other settings for your new account.

![Alt text](./media/documentdb-dotnet-application/image6.png)


Once you're done supplying the values for your account, click "Create" and the provisioning process will begin creating your database account.
When the provisioning process is complete, you should see a notification appear in the notifications area of the portal and the tile on your start screen (if you selected to create one) will change to show the completed action.

![Alt text](./media/documentdb-dotnet-application/image7.png)


Once provisioning is complete, clicking the DocumentDB tile from the start screen will bring up the main blade for this newly created DocumentDB account.

![Alt text](./media/documentdb-dotnet-application/image8.png) 
![Alt text](./media/documentdb-dotnet-application/image9.png)


Using the "Keys" button, access your endpoint URL and the Primary Key, copy these to your clipboard and keep them handy as we will use these values in the web application we will use these values in the web application we will create next.

We will now walk through how to create a new ASP.NET MVC application from the ground-up. For your reference the complete solution can be downloaded [here].

<a name="_Toc395637762">Create a new ASP.NET MVC application</a>
================================================================

In Visual Studio, click File - New Project and select the option to create a new ASP.NET MVC Web Application.

![Alt text](./media/documentdb-dotnet-application/image10.png)


Select where you would like to create the project, what you want the project to be called (I'm using "todo") and click Ok.

![Alt text](./media/documentdb-dotnet-application/image11.png)


If you plan on hosting your application in Azure then select the box on the lower right to "Host in the cloud". We've selected to host in the cloud, and run the application hosted in an Azure Website. Selecting this option will pre-provision an Azure Website for you and make life a lot easier when it comes times to deploy the final working application. If you want to host this elsewhere or don't want to configure Azure upfront, then just un-check the "Host in the Cloud" option.

Select OK and let Visual Studio do its thing around scaffolding the empty ASP.NET MVC template. If you chose to host this in the Cloud you will see at least one additional screen asking you to login to your Azure account and provide some values for your new Website. Proceed to supply all these Azure values and continue. 

I haven't chosen a "Database server" here because we're not using an Azure SQL Database Server here, we're going to be creating a new Azure DocumentDB Account later on in the management portal. 

![Alt text](./media/documentdb-dotnet-application/image11_1.png)

Once Visual Studio has finished creating the boilerplate MVC application you have an empty ASP.NET application that you can run locally.

We'll skip running locally because I'm sure we've all seen the ASP.NET "Hello World" application. Let's go straight to adding DocumentDB to this project and building our application.

</h1>
<a name="_Toc395637767">Adding DocumentDB to your project</a>
=============================================================

That takes care of most of the ASP.NET MVC plumbing that we need for
this solution. Now let's get to the real purpose of this tutorial, adding Azure DocumentDB to our web application.

### 

### <a name="_Toc395637764">Installing the NuGet package</a>

The DocumentDB .NET SDK is packaged and distributed as a NuGet package. Using the NuGet package manager in Visual Studio (which you can get to by Right-Clicking on the Project and choosing "Manage NuGet Packages"

![Alt text](./media/documentdb-dotnet-application/image21.png)

Search for Online for "Azure DocumentDB" and install the package. This will download and install the DocumentDB package as well as all dependencies, like Newtonsoft.Json.

**NOTE:** While the service is still in preview, the NuGet package is marked as "Prerelease" so you need to include the option to "Include Prerelease", else you will not find the package. 

Alternatively you could use the Package Command Console to install the package by typing;

    Install-Package Microsoft.Azure.Documents.Client -Pre

Once installed your Visual Studio solution should resemble the following with two new references added;

![Alt text](./media/documentdb-dotnet-application/image22.png)


<a name="_Toc395637763">Setting up the ASP.NET MVC application</a>
==================================================================

### 

### <a name="_Toc395637764">Add a Model</a>

Let's begin by creating the **M** in MVC, the model. In Solution Explorer, right-click the *Models* folder and then click **Add**, then **Class**

![Alt text](./media/documentdb-dotnet-application/image12.png)

Name your new Class, **Item** and then add the following code in to this new Class

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

All data in DocumentDB is passed over the wire, and stored, as JSON. To control the way your objects are serialized/deserialized by JSON.NET you can use the JsonProperty attribute as demonstrated in the Item class we just created. You don't **have** to do this but i wanted to ensure that my properties followed the JSON camelCase naming conventions. 

Not only can you control the format of the property name when it goes in to JSON, but you can entirely rename your .NET properties like I did with the Description property. 

You can, if you like, use JsonConverter objects here as well to completely control how serialization is handled.  

In order to get Visual Studio to resolve the "JsonProperty" attribute used here you need to add the following using statement to the using section of your class file;

    using Newtonsoft.Json;

### <a name="_Toc395637765">Add a Controller</a>

That takes care of the M, now let's create the **C** in MVC, a controller class.
In **Solution Explorer**, right-click the *Controllers* folder and then click **Add**, then **Controller**.

![Alt text](./media/documentdb-dotnet-application/image13.png)

![Alt text](./media/documentdb-dotnet-application/image14.png)


In the **Add Scaffold** dialog box, click **MVC 5 Controller - Empty.** Click **Add.**

![Alt text](./media/documentdb-dotnet-application/image15.png)

Name your new Controller, **ItemController.**

Visual Studio will now add the ItemController your Solution Explorer should look like similar to below.

![Alt text](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766">Add Views</a>

And finally, let's create the **V** in MVC, a view.


#### Add Item Index View

Expand the ***Views***  folder in Solution Explorer and location the (empty) Item folder which Visual Studio would've created for you when you added the *ItemController* earlier. Right click on ***Item*** and choose to Add a new View.

![Alt text](./media/documentdb-dotnet-application/image17.png)

In the "Add View" dialog. Call your view "***Index***", use the ***List*** Template, select the ***Item (todo.Models)*** which we created earlier as the class and finally use the ***~Views/Shared/_Layout.cshtml*** in the Solution as the Layout page.

![Alt text](./media/documentdb-dotnet-application/image18.png)


Once all these values are set, click Add and let Visual Studio create your view for you. Visual Studio will create a template view. Once it is done, it will open the cshtml file created. We can close this document in Visual Studio as we will come back to it later.

#### Add New Item View

In a similar fashion to above, create a new View for creating new Items as per the example shown below;

![Alt text](./media/documentdb-dotnet-application/image19.png)

#### Add Edit Item View

<a name="_Toc395888515"></a>
============================

And finally, add one last View for editing an Item in the same way as before;

![Alt text](./media/documentdb-dotnet-application/image20.png)


Once this is done, close the cshtml documents in Visual Studio as we will return to these Views later.

</h1>
<a name="_Toc395637769">Wiring up DocumentDB</a>
------------------------------------------------

### <a name="_Toc395637770">Listing Incomplete Items</a>

Open the **ItemController** and remove all the code within the class (but leave the class) that Visual Studio added. We'll rebuild it piece by piece using DocumentDB.

Add the following code snippet within the now empty ItemController class;

    public ActionResult Index()
    {
        var items = DocumentDBRepository.GetIncompleteItems();
        return View(items);
    }

This code also uses a "pseudo repository" class called DocumentDBRepository which we have yet to create. This is actually just a Helper class that contains all the DocumentDB specific code. For the purposes of this walk-through we aren't going to implement a full data access layer with dependency injection, and factories and repository patterns, as you would probably be doing if you were building a real world application. 
For the purposes of this walk-through we're just going to put all the data access logic directly in to one project to keep things simple and focus on the DocumentDB specific bits.

Add a new Class to your project and call it **DocumentDBRepository.**
Replace the code in the class file with the following;

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

Spend some time resolving all the namespaces in Visual Studio. All namespaces will be easy to resolve in Visual Studio as long as the NuGet package was installed successfully. The references to the ReadOrCreateDatabase and ReadOrCreateCollection methods will remain unresolved until we have added them, which comes next. 

There are two method calls used here for reading or creating DocumentDB Databases and Document Collections.
So add the following two methods to this class;

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

This takes care setting up the Database, a DocumentCollection, and creating some code to connect to DocumentDB through the DocumentClient. 

We're reading some values from configuration, so open the **web.config** and add the following lines under the
<AppSettings\> section;

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
    
在您需要手動新增下列 using 指示詞之後，會解析 *CreateDocumentQuery* 的參考；

    using Microsoft.Azure.Documents.Linq;

此時，應該已經可以建置方案，而不會發生任何錯誤。

如果您現在執行應用程式，將會前往首頁控制器及該控制器的 [索引] 檢視。這是我們在一開始時所選擇的 MVC 範本專案預設行為，但是我們不想要這樣的行為！讓我們變更此 MVC 應用程式上的路由以改變此行為。

開啟 ***App\_Start\RouteConfig.cs*** 並找到以 "defaults:" 開頭的那一行，變更它使其與下列類似；

    defaults:new { controller = "Item", action = "Index", id = UrlParameter.Optional }

如果您未在 URL 中指定控制路由行為的值，這會讓 ASP.NET MVC 知道改用 "Item" (而非 "Home") 作為控制器，並使用使用者索引作為檢視。
現在如果您執行應用程式，它會呼叫至您的 **ItemController**，並將 **GetIncompleteItems** 方法的結果傳回 Views\Item\Index 檢視。 

如果您建置並立即執行此專案，您現在應該會看到如下的外觀 ；    

![Alt text](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771">新增項目</a>

讓我們將一些項目放入資料庫中，而不再只是看到空白方格。

我們已經有用於建立的檢視，並且在索引檢視上有將使用者帶到建立檢視的按鈕。讓我們將一些程式碼新增至控制器和儲存機制，以在 DocumentDB 中保留記錄。

開啟 ***ItemController.cs***，並新增下列程式碼片段，讓 ASP.NET MVC 知道如何處理「建立」動作，在這個案例中，只會呈現先前建立的相關聯 Create.cshtml 檢視。

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

**安全性注意事項**：此處所使用的 [ValidateAntiForgeryToken] 屬性可協助應用程式防止跨網站偽造要求攻擊。這不光只是新增此屬性，您的檢視也必須使用這個防偽權杖。如需此主題的詳細資訊以及如何正確實作此作業的範例，請參閱 [防止跨網站偽造要求][]。本文結尾處下載連結中的原始程式碼已經有完整的實作。

**安全性注意事項**：我們也會在方法參數上使用 [Bind] 屬性，以協助防範 overposting 攻擊。如需詳細資訊，請參閱 [ASP.NET MVC 中的基本 CRUD 作業][]

現在，一切皆已就緒，項目控制器將  (安全地) 將項目從表單傳遞到儲存機制類別的 CreateDocument 方法，因此請將下列方法新增至 DocumentDBRepository 類別。

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

系統應該已根據您的認證設定好所有項目；實際上，系統已在 Azure 中顯示的 [目的地 URL] 上建立網站，您只需要按一下 [**發佈**] 即可。

![Alt text](./media/documentdb-dotnet-application/image29.png)

幾秒後，Visual Studio 便會發佈 Web 應用程式並啟動瀏覽器，您可以在瀏覽器中看到您方便好用的應用程式已在 Azure 中執行！

</h3>



<a name="_Toc395637775">結論</a>
======================================

### 

恭喜！您剛剛已經使用 Azure DocumentDB 建置您的第一個 ASP.NET MVC 應用程式，並將它發佈至 Azure 網站。您可以在 [這裡][] 下載完整應用程式 (包括我們在此略過的詳細資料和刪除功能) 的原始程式碼。


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[開始]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[防止跨網站偽造要求]: http://go.microsoft.com/fwlink/?LinkID=517254
[ASP.NET MVC 中的基本 CRUD 作業]: http://go.microsoft.com/fwlink/?LinkId=317598
