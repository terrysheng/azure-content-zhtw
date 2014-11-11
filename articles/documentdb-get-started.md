<properties title="Get started with a DocumentDB account" pageTitle="Get started with a DocumentDB account | Azure" description="Learn how to create and configure an Azure DocumentDB account, create databases, create collections, and store JSON documents within the account." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="paulettm" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

# 開始使用 DocumentDB 帳戶

本指南示範如何開始使用 **Azure DocumentDB (預覽)**。相關範例是以 C# 程式碼撰寫並使用 DocumentDB .NET SDK。涵蓋的案例包括建立和設定 DocumentDB 帳戶、建立資料庫、建立集合，以及將 JSON 文件儲存在帳戶內。如需使用 Azure DocumentDB 的詳細資訊，請參閱＜後續步驟＞一節。

若要使用此入門指南，您必須有 DocumentDB 帳戶和帳戶的存取金鑰 (主要或次要)。如需詳細資訊，請參閱：

-   [建立 Document DB 帳戶][建立 Document DB 帳戶]

## 目錄

-   [連接到 DocumentDB 帳戶][連接到 DocumentDB 帳戶]
-   [建立資料庫][建立資料庫]
-   [建立集合][建立集合]
-   [建立文件][建立文件]
-   [查詢 DocumentDB 資源][查詢 DocumentDB 資源]
-   [後續步驟][後續步驟]

## <span id="Connect"></span></a>連接到 DocumentDB 帳戶

有各種 SDK 和 API 可供透過程式設計來使用 DocumentDB。下列範例以 C# 程式碼顯示並使用 DocumentDB .NET SDK。

我們一開始先建立 DocumentClient，以建立 DocumentDB 帳戶的連線。我們的 C# 應用程式中需要有下列參考：

    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Microsoft.Azure.Documents;  

利用 DocumentDB 帳戶端點及與帳戶相關聯的主要或次要存取金鑰，可將 DocumentClient 具現化。

從 DocumentDB 帳戶的 Azure 管理預覽入口網站分頁中，可取得 DocumentDB 帳戶端點和金鑰。

![][0]

> 請注意，[金鑰] 分頁中的 DocumentDB 存取金鑰會授與對於 DocumentDB 帳戶及其資源的管理存取權。DocumentDB 也支援使用資源金鑰，可讓用戶端根據您已授與的權限來讀取、寫入和刪除 DocumentDB 帳戶中的資源，而不需要帳戶金鑰。

使用如下範例的程式碼建立用戶端。

    private static string endpointUrl = "<your endpoint URI>";
    private static string authorizationKey = "<your key>";

    //Create a new instance of the DocumentClient
    var client = new DocumentClient(new Uri(endpointUrl), authorizationKey);  

**警告：**請勿將認證儲存在原始程式碼中。為了讓這個範例簡單明瞭，原始程式碼中顯示認證。請參閱 [Windows Azure 網站：應用程式字串與連接字串的運作方式][Windows Azure 網站：應用程式字串與連接字串的運作方式] (英文) 以取得如何儲存認證的詳細資訊。

現在，您已知道如何連接到 DocumentDB 帳戶和建立 DocumentClient 的執行個體，接下來說明使用 DocumentDB 資源。

## <span id="CreateDB"></span></a>建立資料庫

使用 .NET SDK 時，可透過 DocumentClient 的 CreateDatabaseAsync 方法來建立 DocumentDB 資料庫。

    //Create a Database
     Database database = await client.CreateDatabaseAsync(
        new Database
        {
        Id = "FamilyRegistry"
        });

## <span id="CreateColl"></span></a>建立集合

使用 .NET SDK 時，可透過 DocumentClient 的 CreateDocumentCollectionAsync 方法來建立 DocumentDB 集合。上一個步驟中建立的資料庫有許多屬性，其中一個是 SelfLink 屬性。憑著此資訊，我們現在可以建立集合。

        //Create a document collection 
    documentCollection = new DocumentCollection
        {
            Id = "FamilyCollection"
        };

        documentCollection = await client.CreateDocumentCollectionAsync(database.SelfLink,documentCollection); 

## <span id="CreateDoc"></span></a>建立文件

使用 .NET SDK 時，可透過 DocumentClient 的 CreateDocumentAsync 方法來建立 DocumentDB 文件。上一個步驟中建立的集合有許多屬性，其中一個是 DocumentsLink 屬性。憑著此資訊，我們現在可以插入 1 份或更多文件。基於此範例的目的，我們假設有一個 Family 類別描述一個家族的屬性，例如名稱、姓別和年齡。

    private static async Task CreateDocuments(string    colSelfLink)
    {
        Family AndersonFamily = new Family
        {
            Id = "AndersenFamily",
            LastName = "Andersen",
            Parents =  new Parent[] {
                new Parent { FirstName = "Thomas" },
                new Parent { FirstName = "Mary Kay"}
            },
            Children = new Child[] {
                new Child
                { 
                    FirstName = "Henriette Thaulow", 
                    Gender = "female", 
                    Grade = 5, 
                    Pets = new [] {
                        new Pet { GivenName = "Fluffy" } 
                    }
                } 
            },
            Address = new Address { State = "WA", County = "King", City = "Seattle" },
            IsRegistered = true
        };

        await client.CreateDocumentAsync(colSelfLink, AndersonFamily);

        Family WakefieldFamily = new Family
        {
            Id = "WakefieldFamily",
            Parents = new [] {
                new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
                new Parent { FamilyName= "Miller", FirstName= "Ben" }
            },
            Children = new Child[] {
                new Child
                {
                    FamilyName= "Merriam", 
                    FirstName= "Jesse", 
                    Gender= "female", 
                    Grade= 8,
                    Pets= new Pet[] {
                        new Pet { GivenName= "Goofy" },
                        new Pet { GivenName= "Shadow" }
                    }
                },
                new Child
                {
                    FamilyName= "Miller", 
                    FirstName= "Lisa", 
                    Gender= "female", 
                    Grade= 1
                }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
        };

        await client.CreateDocumentAsync(colSelfLink, WakefieldFamily);

    }

## <span id="Query"></span></a>查詢 DocumentDB 資源

DocumentDB 對每個集合中儲存的 JSON 文件，支援豐富的查詢。以下的範例程式碼示範可在前一個步驟所插入的文件上執行的各種查詢 (使用 DocumentDB SQL 語法與 LINQ)。

    //
    //Querying the documents using DocumentDB SQL for the Andersen family
    //
    foreach (var family in client.CreateDocumentQuery(collectionLink, 
    "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
    Console.WriteLine("\tRead {0} from SQL", family);
    }

    //
    //Querying the documents using LINQ for the Andersen family
    //
    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
     Console.WriteLine("\tRead {0} from LINQ", family);
    }

    //
    //Querying the documents using LINQ lambdas for the Andersen family
    //
    foreach (var family in client.CreateDocumentQuery(collectionLink)
    .Where(f => f.Id == "AndersenFamily")
    .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    //
    //DocumentDB SQL -  using <> interchangably with != for "not equals"
    //
    families = client.CreateDocumentQuery<Family>(colSelfLink, "SELECT * FROM Families f WHERE f.id <> 'AndersenFamily'");

    //   
    // LINQ - combine equality and inequality
    //
    families = from f in client.CreateDocumentQuery<Family>(colSelfLink)
           where f.Id == "Wakefield" && f.Address.City != "NY"
           select f; 

## <span id="NextSteps"></span></a>後續步驟

-   了解如何[監視 DocumentDB 帳戶][監視 DocumentDB 帳戶] (英文)。
-   如需程式設計模型的詳細資訊，請參閱 [DocumentDB 文件頁面][DocumentDB 文件頁面]的＜開發＞一節

  [建立 Document DB 帳戶]: ../documentdb-create-account/
  [連接到 DocumentDB 帳戶]: #Connect
  [建立資料庫]: #CreateDB
  [建立集合]: #CreateColl
  [建立文件]: #CreateDoc
  [查詢 DocumentDB 資源]: #Query
  [後續步驟]: #NextSteps
  [0]: ./media/documentdb-get-started/gs1.png
  [Windows Azure 網站：應用程式字串與連接字串的運作方式]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [監視 DocumentDB 帳戶]: http://go.microsoft.com/fwlink/p/?LinkId=402378
  [DocumentDB 文件頁面]: http://go.microsoft.com/fwlink/p/?LinkID=402319
