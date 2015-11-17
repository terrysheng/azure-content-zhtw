<properties
	pageTitle="NoSQL 資料庫 - 開始使用 DocumentDB .NET SDK | Microsoft Azure"
	description="了解如何建立資料庫並設定 Azure DocumentDB 帳戶。建立資料庫、集合，以及在 NoSQL 資料庫帳戶內儲存 JSON 文件。"
	keywords="建立資料庫, 建立資料庫, nosql 資料庫, nosql 資料庫, nuget, documentdb, azure, Microsoft azure"
	services="documentdb"
	documentationCenter=".net"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article" 
	ms.date="11/05/2015"
	ms.author="anhoh"/>

#開始使用 DocumentDB .NET SDK  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

歡迎開始使用 DocumentDB .NET SDK！ 完成本教學課程之後，您將會有一個主控台應用程式，可用來建立和查詢 DocumentDB 資源。

本文將討論：

- 建立和連接到 DocumentDB 帳戶
- 設定 Visual Studio 方案
- 建立資料庫
- 建立集合
- 建立 JSON 文件
- 查詢集合
- 刪除資料庫

沒有時間嗎？ 別擔心！ 您可以在 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) 上找到完整的方案。請參閱[取得完整的解決方案](#GetSolution)，以取得簡要指示。

之後，請使用此頁面頂端或底部的投票按鈕，將意見反應提供給我們。如果想要我們直接與您連絡，歡迎在留言中留下電子郵件地址。

讓我們開始吧！

## 必要條件

請確定您具有下列項目：

- 使用中的 Azure 帳戶。如果您沒有帳戶，您可以註冊[免費的 Azure 試用](http://azure.microsoft.com/pricing/free-trial/)。
- [Visual Studio 2013 / Visual Studio 2015](http://www.visualstudio.com/)。

## 步驟 1：建立 DocumentDB 帳戶

讓我們建立 DocumentDB 帳戶。如果您已經擁有想要使用的帳戶，就可以跳到[安裝 Visual Studio 方案](#SetupVS)。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> 步驟 2：安裝 Visual Studio 方案

1. 在電腦上開啟 **Visual Studio**。
2. 從 [檔案] 功能表中，選取 [新增]，然後選擇 [專案]。
3. 在 [新增專案] 對話方塊中，依序選取 [範本] / [Visual C#] / [主控台應用程式]、為專案命名，然後按一下 [確定]。
4. 在 [**方案總管**] 中，以滑鼠右鍵按一下 Visual Studio 方案底下的新主控台應用程式。
5. 然後在無需離開功能表的情況下，按一下 [**管理 NuGet 封裝...**]
6. 在 [**管理 NuGet 封裝**] 視窗的最左側窗格上，依序按一下 [**線上**] / [**nuget.org**]。
7. 在 [**線上搜尋**] 輸入方塊中，搜尋 **DocumentDB 用戶端程式庫**。
8. 在結果中，尋找 [**Microsoft Azure DocumentDB 用戶端程式庫**]，並按一下 [**安裝**]。DocumentDB 用戶端程式庫的封裝識別碼為 [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)

太棒了！ 現在已完成安裝程式，讓我們開始撰寫一些程式碼。

##<a id="Connect"></a> 步驟 3：連接到 DocumentDB 帳戶

首先，在 Program.cs 檔案中，將這些參考新增到 C# 應用程式的開頭：

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

> [AZURE.IMPORTANT]若要完成此應用程式，請務必加入上述的相依性。

接著，儲存 DocumentDB 帳戶端點以及主要或次要存取金鑰 (可於 [Azure Preview 入口網站](https://portal.azure.com)中找到)。

![顯示 DocumentDB 帳戶的 Azure Preview 入口網站螢幕擷取畫面，內含反白顯示的 [主動式] 集線器、[DocumentDB 帳戶] 刀鋒視窗上反白顯示的 [金鑰] 按鈕、[金鑰] 刀鋒視窗上反白顯示的 [URI]、[主要金鑰] 和 [次要金鑰] 值][keys]

    private const string EndpointUrl = "<your endpoint URI>";
    private const string AuthorizationKey = "<your key>";

讓我們建立 **DocumentClient** 的新執行個體，來開始入門示範應用程式。建立新的且名為 **GetStartedDemo** 的非同步工作呼叫，並將新的 **DocumentClient** 具現化。

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

從 **Main** 方法呼叫非同步工作 (類似下列程式碼)。

	public static void Main(string[] args)
    {
		try
    	{
        	GetStartedDemo().Wait();
		}
		catch (Exception e)
		{
			Exception baseException = e.GetBaseException();
			Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
		}
	}

> [AZURE.WARNING]請勿將認證儲存在原始程式碼中。為了讓這個範例簡單明瞭，原始程式碼中會顯示認證。如需如何在實際執行環境中儲存認證的相關資訊，請參閱 [Azure 網站：應用程式字串與連接字串的運作方式 (英文)](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)。查看我們在 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs) 上的範例應用程式，以取得在原始程式碼外儲存認證的相關範例。

現在，您已經知道如何連接到 DocumentDB 帳戶和建立 **DocumentClient** 類別的執行個體，接下來說明使用 DocumentDB 資源。

## 步驟 4：建立資料庫
您可透過使用 **DocumentClient** 類別的 [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) 方法來建立[資料庫](documentdb-resources.md#databases)。資料庫是分割給多個集合之文件儲存體的邏輯容器。在您建立 **DocumentClient** 之後，在 **GetStartedDemo** 方法中建立新的資料庫。

	// Check to verify a database with the id=FamilyRegistry does not exist
	Database database = client.CreateDatabaseQuery().Where(db => db.Id == "FamilyRegistry").AsEnumerable().FirstOrDefault();

	// If the database does not exist, create a new database
    if (database == null)
    {
    	database = await client.CreateDatabaseAsync(
    		new Database
            {
            	Id = "FamilyRegistry"
            });

		// Write the new database's id to the console
		Console.WriteLine(database.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateColl"></a>步驟 5：建立集合  

> [AZURE.WARNING]**CreateDocumentCollectionAsync** 會建立具有價格含意的新 S1 集合。如需詳細資訊，請造訪[價格頁面](https://azure.microsoft.com/pricing/details/documentdb/)。

您可以使用 **DocumentClient** 類別的 [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) 方法建立[集合](documentdb-resources.md#collections)。集合是 JSON 文件和相關聯 JavaScript 應用程式邏輯的容器。新建立的集合將會對應至 [S1 效能層級](documentdb-performance-levels.md)。在 **GetStartedDemo** 方法中建立資料庫之後，建立名為 **FamilyCollection** 的新集合。

    // Check to verify a document collection with the id=FamilyCollection does not exist
    DocumentCollection documentCollection = client.CreateDocumentCollectionQuery("dbs/" + database.Id).Where(c => c.Id == "FamilyCollection").AsEnumerable().FirstOrDefault();

	// If the document collection does not exist, create a new collection
    if (documentCollection == null)
    {
    	documentCollection = await client.CreateDocumentCollectionAsync("dbs/" + database.Id,
        	new DocumentCollection
            {
            	Id = "FamilyCollection"
            });

		// Write the new collection's id to the console
		Console.WriteLine(documentCollection.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateDoc"></a>步驟 6：建立文件
您可以使用 **DocumentClient** 類別的 [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) 方法來建立[文件](documentdb-resources.md#documents)。文件會是使用者定義的 (任意) JSON 內容。現在可插入一或多份文件。如果您已經有想要儲存於資料庫中的資料，就可以使用 DocumentDB 的[資料移轉工具](documentdb-import-data.md)。

首先，我們需要建立**Parent**、**Child**、**Pet**、**Address** 和 **Family** 類別。藉由在 **GetStartedDemo** 方法之後新增下列內部子類別來建立這些類別。

    internal sealed class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    internal sealed class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    internal sealed class Pet
    {
        public string GivenName { get; set; }
    }

    internal sealed class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

    internal sealed class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
    }

接下來，在您的 **GetStartedDemo** 非同步方法內建立文件。

    // Check to verify a document with the id=AndersenFamily does not exist
    Document document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "AndersenFamily").AsEnumerable().FirstOrDefault();

	// If the document does not exist, create a new document
	if (document == null)
	{
	    // Create the Andersen Family document
	    Family andersonFamily = new Family
	    {
	        Id = "AndersenFamily",
	        LastName = "Andersen",
	        Parents = new Parent[] {
	            new Parent { FirstName = "Thomas" },
	            new Parent { FirstName = "Mary Kay"}
	        },
	        Children = new Child[] {
	            new Child
	            { 
	                FirstName = "Henriette Thaulow", 
	                Gender = "female", 
	                Grade = 5, 
	                Pets = new Pet[] {
	                    new Pet { GivenName = "Fluffy" } 
	                }
	            } 
	        },
	        Address = new Address { State = "WA", County = "King", City = "Seattle" },
	        IsRegistered = true
	    };
	
	    // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
	    await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, andersonFamily);
	}

    // Check to verify a document with the id=AndersenFamily does not exist
    document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "WakefieldFamily").AsEnumerable().FirstOrDefault();

    if (document == null)
    {
        // Create the WakeField document
        Family wakefieldFamily = new Family
        {
            Id = "WakefieldFamily",
            Parents = new Parent[] {
                new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
                new Parent { FamilyName= "Miller", FirstName= "Ben" }
            },
            Children = new Child[] {
                new Child {
                    FamilyName= "Merriam", 
                    FirstName= "Jesse", 
                    Gender= "female", 
                    Grade= 8,
                    Pets= new Pet[] {
                        new Pet { GivenName= "Goofy" },
                        new Pet { GivenName= "Shadow" }
                    }
                },
                new Child {
                    FamilyName= "Miller", 
                    FirstName= "Lisa", 
                    Gender= "female", 
                    Grade= 1
                }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
        };

        // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
        await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, wakefieldFamily);
	}

您現在已經在 DocumentDB 帳戶中建立下列資料庫、集合和文件。

![說明帳戶、資料庫、集合和文件之間階層式關聯性的圖表](./media/documentdb-get-started/account-database.png)

##<a id="Query"></a>步驟 7：查詢 DocumentDB 資源

DocumentDB 支援對儲存於每個集合的 JSON 文件進行豐富[查詢](documentdb-sql-query.md)。下列範例程式碼示範可在我們於前一個步驟中插入的文件上執行的各種查詢 (同時使用 DocumentDB SQL 語法和 LINQ)。將這些查詢加入至 **GetStartedDemo** 非同步方法。

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = "AndersenFamily"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

下圖說明如何針對您所建立的集合呼叫 DocumentDB SQL 查詢語法，相同邏輯也可以套用至 LINQ 查詢。

![說明查詢範圍和意義的圖表](./media/documentdb-get-started/collection-documents.png)

因為 DocumentDB 查詢已侷限於單一集合，所以查詢中的 [FROM](documentdb-sql-query.md#from-clause) 關鍵字是選擇性的。因此，"FROM Families f" 可以換成 "FROM root r"，或您選擇的任何其他變數名稱。依預設，DocumentDB 會推斷該系列、根或您選擇的變數名稱來參考目前的集合。

##<a id="DeleteDatabase"></a>步驟 8：刪除資料庫

刪除已建立的資料庫將會移除資料庫和所有子系資源 (集合、文件等)。您可以刪除資料庫和文件用戶端，方法是在 **GetStartedDemo** 非同步方法的結尾處加入下列程式碼片段。

    // Clean up/delete the database
    await client.DeleteDatabaseAsync("dbs/" + database.Id);
	client.Dispose();

##<a id="Run"></a>步驟 9：執行您的應用程式！

您現在可以開始執行應用程式。在 **Main** 方法的結尾處加入下列程式碼行，這可讓您在應用程式完成執行之前讀取主控台輸出。

	Console.ReadLine();

現在，在 Visual Studio 中按 F5，即可在偵錯模式下建置應用程式。

您現在應該可以看到入門應用程式的輸出。輸出將會顯示新增的查詢結果，而且應該符合以下的範例文字。

	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
    	{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from SQL
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ query

恭喜！ 您已建立您的第一支 DocumentDB 應用程式！

##<a id="GetSolution"></a>取得完整的方案
若要建置包含本文中所有範例的 GetStarted 方案，您將需要下列項目：

-   [DocumentDB 帳戶][documentdb-create-account]。
-   您可以在 GitHub 上找到 [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) 方案。

若要在 Visual Studio 中還原 DocumentDB .NET SDK 的參考，請在 [方案總管] 的 **GetStarted** 方案上按一下滑鼠右鍵，然後按一下 [啟用 NuGet 封裝還原]。接下來，在 App.config 檔案中更新 EndpointUrl 和 AuthorizationKey 值，如[連接至 DocumentDB 帳戶](#Connect)中所述。

## 後續步驟

-   需要更複雜的 ASP.NET MVC 範例嗎？ 請參閱[使用 DocumentDB 建置具有 ASP.NET MVC 的 Web 應用程式](documentdb-dotnet-application.md)。
-	了解如何[監視 DocumentDB 帳戶](documentdb-monitor-accounts.md) (英文)。
-	在 [Query Playground](https://www.documentdb.com/sql/demo) 中，針對範例資料集執行查詢。
-	如需深入了解程式設計模型，請參閱 [DocumentDB 文件頁面](../../services/documentdb/)中的＜開發＞一節。

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/keys.png
 

<!---HONumber=Nov15_HO3-->