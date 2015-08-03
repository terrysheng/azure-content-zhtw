<properties 
	pageTitle="使用 Order By 排序 DocumentDB 資料 | Azure" 
	description="了解如何以 LINQ 和 SQL 在 DocumentDB 查詢中使用 ORDER BY，以及如何指定 ORDER BY 查詢的索引編製原則。" 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/19/2015" 
	ms.author="arramac"/>

# 使用 Order By 排序 DocumentDB 資料
Microsoft Azure DocumentDB 支援在 JSON 文件上使用 SQL 來查詢文件。您可以在 SQL 查詢陳述式中使用 ORDER BY 子句來排序查詢結果。

閱讀本文後，您將能夠回答下列問題：

- 如何使用 Order By 來進行查詢？
- 如何設定 Order by 的編製索引原則？
- 未來將推出哪些新功能？

本文章另提供[範例](#samples)和[常見問題集](#faq)。

如需 SQL 查詢的完整參考，請參閱 [DocumentDB 查詢教學課程](documentdb-sql-query.md)。

## 如何使用 Order By 來進行查詢
您現在查詢 DocumentDB 時於 SQL 陳述式中加入選擇性的 Order By 子句，就像 ANSI SQL 一樣。子句可以包含選擇性 ASC/DESC 引數，利用它來指定擷取結果時必須依循的順序。

### 使用 SQL 來進行排序
例如，以下是依照其標題之遞減順序擷取書籍的查詢。

    SELECT * 
    FROM Books 
    ORDER BY Books.Title DESC

### 使用 SQL 來進行排序並搭配篩選
您可以在文件內使用 Books.ShippingDetails.Weight 之類的任何巢狀屬性來進行排序，也可以在 WHERE 子句中指定其他篩選來搭配 Order By，如以下範例所示：

    SELECT * 
    FROM Books 
	WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### 使用適用於 .NET 的 LINQ 提供者來進行排序
如果您使用 .NET SDK 1.2.0 和更新版本，還可以在 LINQ 查詢中使用 OrderBy() 或 OrderByDescending() 子句，如以下範例所示：

    foreach (Book book in client.CreateDocumentQuery<Book>(booksCollection.SelfLink)
        .OrderBy(b => b.PublishTimestamp)) 
    {
        // Iterate through books
    }

### 使用藉助 .NET SDK 的分頁來進行排序
使用 DocumentDB SDK 中的原生分頁支援時，您可以一次擷取一個頁面的結果，如以下 .NET 程式碼片段所示。在這裡，我們使用 FeedOptions.MaxItemCount 和 IDocumentQuery 介面一次提取最多 10 筆結果。

    var booksQuery = client.CreateDocumentQuery<Book>(
        booksCollection.SelfLink,
        "SELECT * FROM Books ORDER BY Books.PublishTimestamp DESC"
        new FeedOptions { MaxItemCount = 10 })
      .AsDocumentQuery();
            
    while (booksQuery.HasMoreResults) 
    {
        foreach(Book book in await booksQuery.ExecuteNextAsync<Book>())
        {
            // Iterate through books
        }
    }

DocumentDB 支援對於每一個查詢使用單一數值、字串或布林值屬性的排序，即將推出其他查詢類型。如需詳細資訊，請參閱[未來將推出哪些新功能](#Whats_coming_next)。

## 設定 Order by 的編製索引原則

請回想支援兩種類型索引 (雜湊和範圍)的 DocumentDB，它可以針對特定路徑/屬性、資料類型 (字串/數字)，並且以不同的精確度值 (最大精確度或固定精確度值) 進行設定。因為 DocumentDB 使用雜湊索引做為預設值，您必須以具有數字、字串或兩者之「範圍」的自訂索引編製原則建立新集合，才能使用 Order By。

>[AZURE.NOTE]字串範圍索引是在 2015 年 7 月 7 日的 REST API 2015-06-03 版本中引進。若要針對字串建立 Order By 的原則，您必須使用 .NET SDK 的 SDK 1.2.0 版，或 Python、Node.js 或 Java SDK 的 1.1.0 版。
>
>在 REST API 2015-06-03 版之前的版本，對於字串和數字的預設集合索引編製原則是「雜湊」。已經變更為「雜湊」用於字串，以及「範圍」用於數字。

如需詳細資訊，請參閱 [DocumentDB 索引編制原則](documentdb-indexing-policies.md)。

### 針對所有屬性編製 Order By 的索引
以下是您如何針對出現在 JSON 文件內的任何/所有數字或字串屬性，以 Order By 的「所有範圍」索引建立集合。其中，"/*" 代表集合內的所有 JSON 屬性/路徑，-1 代表最大精確度。
                   
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(databaseLink, 
        booksCollection);  

>[AZURE.NOTE]請注意，Order By 只會傳回使用 RangeIndex 編製索引的資料類型 (字串和數字) 的結果。例如，如果您有預設的索引編製原則，只有數字的 RangeIndex，則針對具有字串值之路徑的 Order By 不會傳回任何文件。

### 針對單一屬性編制 Order By 的索引
以下是僅針對字串的 Title 屬性利用編制 Order By 索引來建立集合的方式。有兩種路徑，一個用於 Title 屬性 ("/Title/?") 與「範圍」索引編製，而另一個用於具有預設索引編製配置的其他每個屬性，「雜湊」用於字串及「範圍」用於數字。
    
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });
    
    // Use defaults which are:
    // (a) for strings, use Hash with precision 3 (just equality queries)
    // (b) for numbers, use Range with max precision (for equality, range and order by queries)
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*",
            Indexes = new Collection<Index> { 
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }            
        });

## 範例
請參閱示範如何使用 Order By 的 [Github 範例專案](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)，其內容包括使用 Order By 建立索引編製原則和分頁。這些範例是開放原始碼，我們鼓勵您提交提取要求，並附上可幫助其他 DocumentDB 開發人員的貢獻。請參閱[貢獻指導方針](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md)，以取得有關如何貢獻的指引。

## 未來將推出哪些新功能？

我們將藉由此處介紹的 Order By 支援擴充未來的服務更新。我們正在著手開發以下新增功能，並且會根據您的意見反應排定這些改善功能的發行順序：

- 動態索引編製原則：支援在集合建立後以及在 Azure 入口網站中修改索引編製原則
- 支援複合索引以提升 Order By 和針對多重屬性之 Order By 的效率。

## 常見問題集

**哪些平台/SDK 版本支援排序？**

若要使用 Order By 所需的索引編製原則建立集合，您必須下載最新的 SDK (針對 .NET 下載 1.2.0，針對 Node.js、JavaScript、Python 和 Java 下載 1.1.0)。也需要 .NET SDK 1.2.0 才能在 LINQ 運算式內使用 OrderBy() 和 OrderByDescending()。


**Order By 查詢的預期要求單位 (RU) 耗用量有多高？**

由於 Order By 利用 DocumentDB 索引來進行查閱，因此 Order By 查詢所耗用的要求單位數目將與不含 Order By 的同等查詢相似。就像 DocumentDB 上的其他所有作業一樣，要求單位的數目取決於文件大小/圖形，以及查詢的複雜性。


**Order By 的預期索引額外負荷有多高？**

索引編製的儲存額外負荷將與屬性的數目成正比。在最糟的情況下，索引額外負荷會是資料的 100%。Range/Order By 索引編製和預設雜湊索引編製之間的輸送量 (要求單位) 額外負荷並無差別。

**如何使用 Order By 查詢 DocumentDB 中的現有資料？**

[未來將推出哪些新功能](what's-coming-next)一節提及的動態索引編製原則改善功能推出後，該問題將可獲得支援。若要在現在執行這項操作，您必須先匯出資料，然後再將資料重新匯入以 Range/Order By 索引所建立的新 DocumentDB 集合。您可以使用 DocumentDB 匯入工具在集合之間移轉資料。

**Order By 目前的限制有哪些？**

您只能針對數值或字串屬性，在以最大精確度 (-1) 索引編製過索引的範圍時指定 Order By。

您無法執行以下工作：
 
- 將 Order By 用於內部字串屬性，如 id、_rid 及 _self (即將推出)。- 將 Order By 用於衍生自文件內部聯結之結果的屬性 (即將推出)。
- 依多個屬性執行 Order By (即將推出)。
- 將 Order By 用於針對資料庫、集合、使用者、權限或附件的查詢 (即將推出)。
- 將 Order By 用於計算的屬性 (如運算式或 UDF/內建函數的結果)。

## 後續步驟

取用 [Github 範例專案](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)並開始排序您的資料 ！

## 參考
* [DocumentDB 查詢參考](documentdb-sql-query.md)
* [DocumentDB 索引編製原則參考](documentdb-indexing-policies.md)
* [DocumentDB SQL 參考](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [DocumentDB Order By 範例](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)
 

<!---HONumber=July15_HO4-->