<properties 
    pageTitle="DocumentDB 索引編製原則 | Azure" 
    description="了解 DocumentDB 中索引編製的運作方式，以及了解如何設定索引編製原則。" 
    services="documentdb" 
    documentationCenter="" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="07/06/2015" 
    ms.author="mimig"/>


# DocumentDB 索引編製原則

DocumentDB 是真正無結構描述的資料庫。它不會假設或不需要 JSON 文件的任何結構描述。這可讓您快速地定義並逐一查看應用程式資料模型。將文件新增至集合時，DocumentDB 會自動為所有文件屬性編製索引，讓它們可供您查詢。自動索引編制功能可讓您儲存不同類型的文件。

自動編製文件索引功能是由將寫入最佳化、無鎖定且將記錄結構化的索引維護技術所啟用。DocumentDB 既支援大量的快速寫入，同時又能提供一致的查詢。

DocumentDB 索引編製子系統是設計來支援：

-  在沒有任何結構描述或索引定義的情況下，進行有效率、豐富階層式及關聯式查詢
-  在處理持續性的大量寫入時，維持查詢結果一致性。為了透過一致的查詢來取得高寫入輸送量工作負載，將會逐漸、有效率並連線，同時持續處理後續的寫入量。
- 儲存體效率。基於成本效益，索引的磁碟儲存體額外負荷有所限制且可預測。
- 多租用戶。執行索引更新時，會在為每個 DocumentDB 集合配置的系統資源預算內執行。 

針對大部分應用程式，您可以使用預設的自動索引編製原則，因為它能提供最大的彈性並在效能與儲存效率之間做出最佳取捨。而另一方面，指定自訂的索引編製原則，則是可讓您在查詢效能、寫入效能及索引儲存額外負荷之間進行細微的取捨。

例如，您可藉由將特定文件或文件內的路徑自索引編製作業中排除，既減少用於編製索引的儲存空間，也降低用於維護索引的插入時間成本。您可以變更索引的類型，使其更適用於範圍查詢，或是提高索引精確度 (以位元組為單位)，以改善查詢效能。本文說明 DocumentDB 中提供的各種索引編製組態選項，以及如何針對您的工作負載自訂索引編製原則。

閱讀本文後，您將能夠回答下列問題：

- DocumentDB 預設如何支援編製所有屬性的索引？
- 如何覆寫要在索引編製中包含或排除的屬性？
- 我要如何設定最終更新的索引？
- 如何設定索引編製來執行 Order By 或範圍查詢？


## DocumentDB 索引的運作方式

DocumentDB 中的索引利用 JSON 文法允許**以樹狀結構表示**文件的事實。若要以樹狀結構表示 JSON 文件，必須建立一個虛擬根節點，做為其下文件中其餘實際節點的父系。每個包括 JSON 文件中陣列索引的標籤都會成為樹狀結構的節點。下圖說明範例 JSON 文件及其對應的樹狀結構表示法。

![索引編製原則](media/documentdb-indexing-policies/image001.png)

例如，上述範例中的 JSON 屬性 {"headquarters": "Belgium"} 會對應至路徑 /"headquarters"/"Belgium"。JSON 陣列 {"exports": {"city": “Moscow"}, {"city": Athens"}} 會對應至路徑 /"exports"/0/"city"/"Moscow" 和 /"exports"/1/"city"/"Athens"。

>[AZURE.NOTE]路徑表示法模糊了文件中結構/結構描述與執行個體值之間的界線，讓 DocumentDB 真正變成沒有結構描述。

在 DocumentDB 中，文件會組織成可使用 SQL 來查詢或在單一交易的範圍內處理的集合。每個集合都可透過設定擁有自己的索引編製原則 (以路徑的方式表示)。在下一節中，我們將探討如何設定 DocumentDB 集合的索引編製行為。

## 設定集合的索引原則

下列範例示範如何透過使用 DocumentDB REST API，在集合建立期間設定自訂索引原則。範例顯示以路徑、索引類型及精確度表示的索引編製原則。

    POST https://<REST URI>/colls HTTP/1.1
    Accept: application/json 

    {
       "id":"customIndexCollection",
       "indexingPolicy":{
          "automatic":true,
          "indexingMode":"Consistent",
          "includedPaths":[
             {
                "path":"/*",
                "indexes":[
    
                ]
             }
          ],
          "excludedPaths":[
             {
                "path":"/nonIndexedContent/*"
             }
          ]
       }
    }
     ...


     HTTP/1.1 201 Created

>[AZURE.NOTE]索引編製原則的 JSON 結構描述已經使用 REST API 2015-06-03 版進行變更，以支援針對 .NET SDK 1.2.0 和 Java、Python 等字串的「範圍」索引，Node.js SDK 1.1.0 支援新的原則結構描述。較舊的 SDK 使用 REST API 2015-04-08 版，並支援舊版的索引編製原則結構描述。
>
>集合的索引編製原則必須在建立時指定。不允許在建立集合之後修改索引編製原則，但未來的 DocumentDB 版本中將會支援。
>
>根據預設，DocumentDB 會使用雜湊索引為文件內的所有路徑一致地編製索引。內部時間戳記 (\\_ts) 路徑會與範圍索引儲存在一起。

### 自動編製索引

您可以選擇是否要讓集合自動編製所有文件的索引。根據預設，會自動編製所有文件的索引，但您可以選擇將它關閉。關閉索引編製功能時，便只能透過文件自己的連結或使用識別碼透過查詢來存取文件。

在關閉自動索引編製功能的情況下，您仍然可以選擇性地只將特定的文件新增到索引中。相反地，您也可以讓自動索引編製功能保持開啟，並選擇性地只排除特定的文件。當您只需要查詢文件的子集時，索引編製功能開/關組態相當有用。

您可以將 automatic 屬性的值設定為 true 或 false 來設定預設原則。若要針對單一文件進行覆寫，您可以在插入或取代文件時，設定 x-ms-indexingdirective 要求標頭。

例如，下列範例示範如何包含明確使用 [DocumentDB .NET SDK](https://github.com/Azure/azure-documentdb-java) 和 [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) 屬性的文件。

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(defaultCollection.SelfLink,
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });
        


### 索引編製模式

您可以選擇同步 (一致 (**Consistent**)) 和非同步 (延遲 (**Lazy**)) 索引更新。每次在集合中的文件上發生插入、取代或刪除文件時，預設會同步更新索引。這可讓查詢遵守與文件讀取操作相同的一致性層級，而不會有任何需要索引趕上的延遲。

雖然 DocumentDB 的寫入已經過最佳化處理，並支援持續的大量文件寫入以及同步索引維護，但是您還是可以設定讓特定集合以延遲方式更新其索引。當發生資料寫入暴增，而您想要拉長時間來分攤編製內容索引所需的工作時，延遲索引編製會相當有用。這可讓您有效地使用您佈建的輸送量，並在尖峰時間，以最少的延遲為寫入要求提供服務。不過，在開啟延遲索引編製功能的情況下，不論為資料庫帳戶設定的一致性層級為何，查詢結果最終仍是會保持一致。

下列範例示範如何搭配使用 .NET SDK 與一致自動編製索引，以在插入所有文件時建立 DocumentDB 集合。


     // Default collection creates a hash index for all string and numeric    
     // fields. Hash indexes are compact and offer efficient
     // performance for equality queries.
     
     var collection = new DocumentCollection { Id ="defaultCollection" };
     
     // Optional. Override Automatic to false for opt-in indexing of documents.
     collection.IndexingPolicy.Automatic = true;
     
     // Optional. Set IndexingMode to Lazy for bulk import/read heavy        
     // collections. Queries might return stale results with Lazy indexing.
     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
     
     collection = await client.CreateDocumentCollectionAsync(database.SelfLink, collection);

### 索引類型和精確度

用於索引項目的類型或配置會直接影響索引的儲存和效能。以使用較高精確度的配置來說，查詢通常較快。不過，索引的儲存額外負荷也較高。選擇較低的精確度代表在查詢執行期間可能需要處理較多的文件，但是儲存額外負荷將會比較低。

任何路徑的值的索引精確度可以介於 1 到 8，或設為 -1，表示路徑必須利用所需的最大精確度。可以使用索引的陣列設定每個路徑，一個用於每個資料類型 (字串和數字)，並指定個別的精確度。

還有兩種支援的索引類型 – 雜湊 (Hash) 和範圍 (Range)。選擇「**雜湊**」索引類型可進行有效率的高品質查詢。針對大多數使用案例，雜湊索引並不需要比預設值 (3 個位元組) 更高的精確度。

選擇「**範圍**」索引類型可進行範圍查詢 (使用 >、<、>=、<=、!=) 以及 **Order By** 查詢。Order By 查詢預設也需要以最大索引精確度 (-1) 建立範圍索引，才能保證結果的總順序。

如果路徑有大範圍的值，建議使用較高的精確度，例如 6 個位元組。其中一個需要較高精確度範圍索引的常見使用案例，就是儲存為 Epoch 時間的時間戳記。

如果您的使用案例並不需要 Order BY 和/或有效率的範圍查詢，則預設的雜湊索引可提供最佳的儲存與效能取捨。請注意，若要支援排序依據或範圍查詢，您必須指定自訂/非預設索引原則。

下列範例示範如何使用 .NET SDK 提高集合中範圍索引的精確度。請注意，這裡會使用特殊的路徑 "/" (將在下一節中說明)。

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };
    
    rangeDefault.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


### 索引路徑

您可以選擇編製索引時必須包含或排除文件內的哪些路徑。針對事先知道查詢模式的情況，這將可改善寫入效能並降低索引儲存。

索引路徑的開頭為根 (/)，且通常結尾為 ? 萬用字元運算子，代表有多個可能的首碼值。例如，若要為 SELECT * FROM Families F WHERE F.familyName = "Andersen" 提供服務，您必須在集合的索引原則中包含 /familyName/? 的索引路徑。

索引路徑也可以使用 * 萬用字元運算子來指定路徑首碼底下的遞迴行為。例如，使用 /"payload"/* 可將 payload 屬性下的所有項目自索引編製作業中排除。

以下是指定索引路徑的常見模式：

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>路徑</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>描述/使用案例</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /*
                </p>
            </td>
            <td valign="top">
                <p>
                    集合的預設路徑。遞迴並套用至整個文件樹狀結構。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /prop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    為類似下列的查詢 (類型分別為雜湊或範圍) 提供服務而必要的索引路徑：
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop > 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /prop/*
                </p>
            </td>
            <td valign="top">
                <p>
                    指定之標籤底下所有路徑的索引路徑。使用下列查詢
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop.nextprop = "value"
                </p>

            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Index path required to serve iteration and JOIN queries against arrays of scalars like ["a", "b", "c"]:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Index path required to serve iteration and JOIN queries against arrays of objects like [{subprop: "a"}, {subprop: "b"}]:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"
                </p>
            </td>
        </tr>        
        <tr>
            <td valign="top">
                <p>
                    /prop/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    為查詢 (類型分別為雜湊或範圍) 提供服務而必要的索引路徑：
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
            </td>
        </tr>
    </tbody>
</table>

>[AZURE.NOTE]設定自訂的索引路徑時，您必須為由特殊路徑 "/*" 所表示的整份文件樹狀目錄指定預設的索引規則。

下列範例會設定一個採用範圍索引編製且自訂的有效位數值為 20 個位元組：

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" 
        });
        
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);


當查詢使用 Order By，但沒有針對所查詢之路徑的最大精確度的範圍索引時，DocumentDB 就會傳回錯誤。如果沒有範圍索引 (任何精確度)，但是若有可以為其他索引提供服務的其他篩選器來提供服務，則會對於具有像是 > = 之範圍運算子的查詢傳回錯誤。只要在 REST API 中使用 x-ms-documentdb-enable-scans 標頭，或使用 .NET SDK 利用 EnableScanInQuery 要求選項，仍然可以在沒有範圍索引的情況下執行範圍查詢。

可以從索引編製完全排除類似的路徑。下一個範例示範如何使用 "*" 萬用字元將文件 (也稱為樹狀子目錄) 的整個區段自索引編製作業中排除。

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, excluded);


## 效能微調

評估不同的索引原則組態時，您應該測量 DocumentDB API 的原則的儲存體和輸送量含意。

若要檢查集合的儲存體配額和使用量，請對集合資源執行 HEAD 或 GET 要求，並檢查 x-ms-request-quota 和 x-ms-request-usage 標頭。在 .NET SDK 中，[ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) 中的 [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) 和 [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) 屬性包含這些對應值。

     // Measure the document size usage (which includes the index size) against   
     // different policies.        
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


若要測量每一個寫入作業 (建立、更新或刪除) 的索引編製負荷，請檢查 x-ms-request-charge 標頭 (或 .NET SDK 的 [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) 中同等的 [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) 屬性) 來測量這些作業所耗用的要求單位數量。

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
     
     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                  
     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }
     
     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## 索引編製原則規格的變更
在 2015 年 7 月 7 日的 REST API 2015-06-03 版引進索引編製原則結構描述中的變更。SDK 版本中的對應類別具有符合結構描述的新實作。

在 JSON 規格中實作下列變更：

- 索引編製原則支援字串的範圍索引
- 每個路徑可以有多個索引定義，一個定義用於一種資料類型
- 索引編製精確度針對數字支援 1-8、針對字串支援 1-100 和支援 -1 (最大精確度)
- 路徑區段不需要雙引號來逸出每個路徑。例如，您可以針對 /title/? 新增路徑，而不是 /"title"/?
- 代表「所有路徑」的根路徑可以表示為 /* (除了 / 以外)

如果您有程式碼，會佈建具有以 .NET SDK 1.1.0 版或較舊的版本撰寫的自訂索引編製原則的集合，您必須變更您的應用程式程式碼，處理這些變更以移至 SDK 1.2.0 版。如果您沒有會設定索引編製原則的程式碼，或打算繼續使用舊的 SDK 版本，則不需要任何變更。

以下是一些範例，顯示 API 版本 2015-06-03 與先前版本 2015-04-08 之間的差異。

**先前的索引編製原則 JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/"nonIndexedContent"/*"
       ]
    }

**目前的索引編製原則 JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }

## 後續步驟

請遵循下列連結以取得索引原則管理範例，以及深入了解 DocumentDB 的查詢語言。

1.	[DocumentDB.NET 索引管理程式碼範例](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2.	[DocumentDB REST API 集合作業](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3.	[使用 DocumentDB SQL 進行查詢](documentdb-sql-query.md)

 

<!---HONumber=July15_HO3-->