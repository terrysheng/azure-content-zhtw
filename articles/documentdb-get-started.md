<properties 
	pageTitle="開始使用 DocumentDB .NET SDK | Azure" 
	description="了解如何建立和設定 Azure DocumentDB 帳戶、建立資料庫、建立集合，以及在 NoSQL 文件資料庫帳戶內儲存 JSON 文件。" 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2015" 
	ms.author="mimig"/>

#開始使用 DocumentDB .NET SDK  

本指南示範如何使用 [Microsoft Azure DocumentDB (預覽)](https://portal.azure.com/#gallery/Microsoft.DocumentDB) 和 [DocumentDB .NET SDK](http://go.microsoft.com/fwlink/p/?linkid=402989)。DocumentDB 是一個 NoSQL 文件資料庫服務，有提供[一些 API 和 SDK](http://go.microsoft.com/fwlink/p/?linkid=522476)。本文中的程式碼範例是以 C# 撰寫並使用 DocumentDB .NET SDK，此 SDK 是以 NuGet 封裝的形式封裝及散佈。 

本文中涵蓋的案例包括建立和設定 DocumentDB 帳戶、建立資料庫、建立集合，以及將 JSON 文件儲存在帳戶內。這些範例中每個都是 [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) 上所提供之完整方案的一部分。您可以[下載方案](#GetSolution) 以檢視範例程式碼的內容，或就檢閱本文中的範例。

##<a id="Connect"></a>連接到 DocumentDB 帳戶

我們將從建立新的 [DocumentClient](http://go.microsoft.com/fwlink/p/?linkid=522477) 類別執行個體開始，以便建立與 DocumentDB 帳戶的連線。   我們的 C# 應用程式中需要有下列參考：  

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
 
下一步，利用 DocumentDB 帳戶端點及與此帳戶關聯的主要或次要存取金鑰，可將 **DocumentClient** 具現化。使用如下的程式碼建立用戶端：   

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";
    
    // Create a new instance of the DocumentClient.
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

> [AZURE.WARNING] 請勿將認證儲存在原始程式碼中。為了讓這個範例簡單明瞭，原始程式碼中顯示認證。請參閱 [Azure 網站：應用程式字串與連接字串的運作方式](http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) (英文) 以取得生產環境如何儲存認證的詳細資訊。 

EndpointUrl 和 AuthorizationKey 值為 DocumentDB 帳戶的 URI 和主要金鑰，您可以從 DocumentDB 帳戶的 [Azure 預覽管理入口網站](https://portal.azure.com)分頁取得。 

![][1]
 
這些金鑰可授與 DocumentDB 帳戶和所含資源的管理存取權。DocumentDB 也支援使用資源金鑰，這些金鑰可讓用戶端根據您已授與的權限來讀取、寫入及刪除 DocumentDB 帳戶中的資源，而不需要帳戶金鑰。如需資源金鑰的詳細資訊，請參閱 [DocumentDB 資源模型和概念]的＜權限＞區段(../documentdb-resources/)。

現在您已經知道如何連接到 DocumentDB 帳戶和建立 **DocumentClient** 類別的執行個體，接下來讓我們看看如何使用 DocumentDB 資源。  

##<a id="CreateDB"></a>建立資料庫
可以使用 **DocumentClient** 類別的 [CreateDatabaseAsync](http://go.microsoft.com/fwlink/p/?linkid=522478) 方法建立 DocumentDB 資料庫。  

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>建立集合  

可以使用 **DocumentClient** 類別的 [CreateDocumentCollectionAsync](http://go.microsoft.com/fwlink/p/?linkid=522479) 方法建立 DocumentDB 集合。在上一個步驟中建立的資料庫有一些屬性，其中一個是 [CollectionsLink](http://go.microsoft.com/fwlink/p/?linkid=522481) 屬性。憑著此資訊，我們現在可以建立集合。  

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
##<a id="CreateDoc"></a>建立文件	
可以使用 **DocumentClient** 類別的 [CreateDocumentAsync](http://go.microsoft.com/fwlink/p/?linkid=522482) 方法建立 DocumentDB 文件。在上一個步驟中建立的集合有一些屬性，其中一個是 [DocumentsLink](http://go.microsoft.com/fwlink/p/?linkid=522483) 屬性。憑著此資訊，我們現在可以插入一份或更多文件。針對此範例的目的，我們將假設有一個 Family 類別，此類別描述一個家族的屬性，例如名稱、姓別和年齡。  

    // Create the Andersen family document.
	Family AndersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents =  new Parent[] {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay"}
        },
        Children = new Child[] {
            new Child { 
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

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, AndersenFamily);
    
    // Create the WakeField family document.
    Family WakefieldFamily = new Family
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

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, WakefieldFamily);
 

##<a id="Query"></a>查詢 DocumentDB 資源
DocumentDB 對每個集合中儲存的 JSON 文件，支援豐富的查詢。下列範例程式碼示範可在我們於前一個步驟中插入的文件上執行的各種查詢 (同時使用 DocumentDB SQL 語法和 LINQ)。  

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = \"AndersenFamily\"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery(documentCollection.DocumentsLink)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery(documentCollection.DocumentsLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    // Query the documents using DocumentSQL with one join.
    var items = client.CreateDocumentQuery<dynamic>(documentCollection.DocumentsLink,
        "SELECT f.id, c.FirstName AS child " +
        "FROM Families f " +
        "JOIN c IN f.Children");

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

    // Query the documents using LINQ with one join.
    items = client.CreateDocumentQuery<Family>(documentCollection.DocumentsLink)
        .SelectMany(family => family.Children
            .Select(children => new
            {
                family = family.Id,
                child = children.FirstName
            }));

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }
	
##<a id="GetSolution"></a>取得完整的方案
若要建置包含本文中所有範例的 GetStarted 方案，您將需要下列項目：

-   [DocumentDB 帳戶][documentdb-create-account]。
-   GitHub 上提供的 [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) 方案。 

若要在 Visual Studio 2013 中還原對 DocumentDB.NET SDK 的參考，請在 [方案總管] 中的 GetStarted 方案上按一下滑鼠右鍵，然後按一下 [啟用 NuGet 封裝還原]，便會還原參考。接下來，在 App.config 檔案中，如[連接至 DocumentDB 帳戶]中所述更新 EndpointUrl 和 AuthorizationKey 值(#Connect)。 

##<a id="NextSteps"></a>後續步驟
-	了解如何[監視 DocumentDB 帳戶](http://go.microsoft.com/fwlink/p/?LinkId=402378)。
-	如需有關程式設計模型的詳細資料，請參閱 [DocumentDB 文件頁面](http://go.microsoft.com/fwlink/p/?LinkID=402319)上的＜開發＞一節。


[連接到 DocumentDB 帳戶]: #Connect
[建立資料庫]: #CreateDB
[建立集合]: #CreateColl
[建立文件]: #CreateDoc
[查詢 DocumentDB 資源]: #Query
[後續步驟]: #NextSteps
[doc-landing-page]: ../documentation/services/documentdb/
[documentdb-create-account]: ../documentdb-create-account/
[documentdb-manage]: ../documentdb-manage/

[1]: ./media/documentdb-get-started/gs1.png

<!--HONumber=47-->
