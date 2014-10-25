<properties title="Query with Azure DocumentDB" pageTitle="Query with DocumentDB | Azure" description="DocumentDB's SQL query language supports a subset of ANSI SQL grammar and adds document-oriented support. Queries are served through up-to-date indexes that don't require index management."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# 使用 Azure DocumentDB 執行查詢

## 動機

DocumentDB 的 SQL 查詢語言支援一部分的 ANSI SQL 文法，且巢狀資料結構、陣列和物件建構的形式增加文件導向的支援。DocumentDB 的索引可以有效率地處理查詢，對於文件中的所有屬性，索引會自動保持最新，不需要額外負擔來管理索引。開發人員在查詢內也可以透過使用者定義函數 (UDF)，充分運用 JavaScript 的完整功能。

## 為何選擇 SQL 查詢？

DocumentDB 支援 SQL，提供一個非常簡單又易懂的介面來查詢資料。SQL 是一套結構簡單、功能強大且廣為應用程開發人員所採用的語言。DocumentDB 嚴格遵守開放式平台和標準，例如 HTTP、JSON 和 JavaScript，現在又加上 SQL。
SQL querying is supported via the REST API as well as .NET, Node.js and JavaScript client SDKs.在預存程序和觸發程序中，也支援使用伺服器端 JavaScript API 執行 SQL 查詢。除了 SQL 查詢，DocumentDB 還支援透過 .NET SDK 的 LINQ 來查詢。

# 資料類型

在 DocumentDB，文件和中繼資料的資料格式是以 JavaScript Object Notation (JSON) 來表達。由於 JavaScript 應用程式的增長，JSON 已被廣泛採用，且已發展成為一種平台共用的資料交換格式。由於 JSON 具有自我描述的特性，所以也非常適用於無結構描述的資料庫。

DocumentDB 查詢中支援的基本資料類型與 JSON 相同。JSON 有一套簡單的類型系統，包含：

-   字串
-   數字 (IEEE754 雙精度)
-   布林 – true 和 false
-   Null

在 JSON 和 DocumentDB 中，利用 { } 運算子建立巢狀物件和利用 [ ] 運算子建立陣列，可以表示更複雜的資料類型。

# 查詢文法

使用 DocumentDB SQL 語言時，使用者可以利用 **SELECT** 陳述式從 JSON 文件中擷取資料。以下是針對含有社交網路貼文的文件集合所執行的 SQL 陳述式範例。

     SELECT p.message, p.user.id AS user_id, p.tags[0] AS first_tag 
     FROM posts p 
     WHERE p.type = "Comment"

查詢的輸出是 JSON 文件片段的陣列，符合指定限制的每個文件都傳回一個片段。

    {
      "Documents": [
        {
            "message": "JSON rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        },
        {
            "message": "SQL rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        }
      ]
    }

本文最後附有 SQL 查詢語言的完整正規文法 (BNF)。

# FROM 子句

**FROM** 子句通常是目前要執行查詢的集合名稱。例如，這會選取名為 “posts” 的集合中的所有文件。如果名稱不正確，則會傳回錯誤給使用者。

    SELECT * 
    FROM posts

**ROOT** 關鍵字可用來代替依名稱參考集合 – 因為透過 SDK 或 REST API，可鎖定查詢特定集合的範圍。

    SELECT * 
    FROM ROOT

FROM 的輸出屬性可以使用別名。使用 **JOIN** 來聯結多個 FROM 來源時，這很有用。稍後說明這部分。

    SELECT P.* 
    FROM posts P

也可以從巢狀屬性中查詢。例如，如果 posts 文件包含張貼文件之使用者的子文件，則可以使用下列語法來查詢。

    SELECT U.* 
    FROM posts.user U

FROM 陳述式的來源也可以是純量陣列 (已有排序集合)。

    SELECT E
    FROM ["documentdb", "json", "sql"] E

評估純量運算式時也可以完全省略 FROM 陳述式。

`SELECT "documentdb"`

# WHERE 子句 (篩選)

DocumentDB SQL 語言支援選用的 **WHERE** 子句，可根據文件必須滿足的指定條件來篩選結果集。篩選運算式中可以針對文件中的任何屬性或巢狀路徑進行邏輯比較。

例如，查詢中可以使用 message 等任何屬性。DocumentDB 的自動索引讓篩選過程變得很有效率。

    SELECT * 
    FROM posts p 
    WHERE p.message = "DocumentDB now supports SQL!"

透過點表示法，可篩選巢狀屬性。路徑可以是任意長度，視文件的結構描述而定。

    SELECT * 
    FROM posts p 
    WHERE p.user.name = "Azure DocumentDB"

透過陣列取值運算子，可參考陣列內的個別值。利用 **IN** 關鍵字 (稍後討論) 可支援陣列展開。

    SELECT * 
    FROM posts p 
    WHERE p.views[0] = 100

範圍比較 (\>、\<、\>=、\<=、!=) 可運用在數值上。數字也可以與算術運算式的結果比較，如下所示。

    SELECT * 
    FROM posts p 
    WHERE p.views[0] > 100 * 2

利用 AND、OR 和 NOT 運算子，可結合邏輯運算子，如同在一般 SQL 中一樣。這些都透過指定屬性的索引交集而有效率地處理。

    SELECT * 
    FROM posts p 
    WHERE ((p.user.name = "Azure DocumentDB")
    OR (p.views[0] = 100 AND p.message = "DocumentDB now supports SQL!"))

## 處理遺漏和 Null 屬性

支援以 NULL 當做關鍵字，且屬性可以與 null 比較，如同其他純量一樣。

    SELECT * 
    FROM posts p 
    WHERE p.message IS NULL

因為文件的結構描述可能很快改變或事前未知，此語言也支援特殊運算子來檢查遺漏的屬性 – 透過 ISUNDEFINED 運算子，類似 JavaScript 中的 **undefined**。

    SELECT * 
    FROM posts p 
    WHERE p.message IS UNDEFINED

> 注意：與 JavaScript 相似，= undefined 一律傳回 false。

# SELECT 子句 (投影)

**SELECT** 子句可用來從查詢中擷取特定的屬性。星號 (\*) 運算子會傳回符合查詢的完整文件。

    SELECT * 
    FROM posts

SELECT 子句中可以擷取指定的欄位。欄位可以是不可部分完成 (字串、數字、布林) 或複雜 (陣列、JSON 物件)。SELECT 子句中的複雜類型 (例如下列範例中的 “user”) 會傳回屬性之下的整個 JSON 物件。SELECT 子句中的屬性必須有唯一名稱或別名。

    SELECT posts.id, posts.user
    FROM posts

> 注意：因為 JSON 並不排序，所以文件中的個別屬性不會依任何特定的順序傳回。

> 注意：必須明確限定欄位名稱。因為文件沒有固定的結構描述，所以必須如此，查詢執行階段才能執行正確的繫結。

另一種存取屬性的方法是使用字典查閱語法，如下所示：

    SELECT posts.id, posts["user"], posts["from"]
    FROM posts

> 注意：應該利用字典查閱語法來逸出含有保留關鍵字的屬性名稱，如上述範例中的 FROM。

## 運算式評估

純量運算式也可以與運算式一起使用。未指定名稱時，將使用自動產生的名稱當做預留位置 ($1、$2、$3 等)。

    SELECT ((2 + 11 % 7) – 2)/3
    FROM posts

下列運算式可以在 SQL 查詢運算式內使用。運算子具有強型別，如同在 SQL 中一樣。例如，等於 (=) 是強式等號，表示 5 != “5”。

|------------|-----------------------------------------|
| 算術運算子 | +、-、\*、/、% (模數)                   |
| 邏輯運算子 | AND、OR、NOT                            |
| 位元運算子 | & (位元 AND)、| (位元 OR)、^ (位元 XOR) |
| 字串運算子 | || (串連)                               |
| 比較運算子 | =, !=, \>, \<, \>=, \<=                 |

## JSON 轉換

SQL 查詢可以傳回 JSON 片段，而不只是完整文件 – **VALUE** 關鍵字可做為此用途。例如，下列文件會傳回 “2” 而非 {“id”: “2”}.

    SELECT VALUE posts.id
    FROM posts

如同在 JavaScript 中一樣，可使用 {} 和 [] 運算子來建構更複雜的 JSON 運算式。這可讓查詢靈活地轉換結果集的形狀。

    SELECT {"user_name": p.user.name, "recent_views": [p.views[0], p.views[1]]}
    FROM posts p

> 注意：值可以是任何有效的運算式，但標籤必須是常值 (字串)

# 聯結和反覆運算

在文件資料庫中，參考的資料內嵌為子文件或，或在陣列內。因此，SQL 語言使用 **JOIN** 關鍵字，支援文件內部，亦稱為自我聯結。

    SELECT p.id, tag 
    FROM posts p 
    JOIN tag IN p.tags

> 注意：聯結在正式上為交叉聯結，但因為資料是內嵌，所以成為內部聯結。

在範例中，**IN** 運算子用來逐一查看來源的所有元素。IN 子句的來源可以是陣列或物件。與物件一起使用時，Iterator 會逐一查看每一個屬性。IN 也可以如下所示直接使用。IN 也可以與陣列常值一起使用。

    SELECT tag 
    FROM tag IN posts.tags

SQL 陳述式內可以使用多重聯結，如下所示：

    SELECT p.id, tag, liked
    FROM posts p 
    JOIN tag IN p.tags
    JOIN liked IN p.likes  

# 使用者定義函數 (UDF)

DocumentDB 查詢以 JavaScript 使用者定義函數的形式，支援可程式化延伸模組。每一個集合可以有一或多個儲存的 **UDF**，可轉換 JSON 片段，並於指令碼中直接以名稱來參考片段。

例如，使用 create 使用者定義函數，“regex\_match” 函數可以定義為：

    function regex_match(input, pattern) {
        getContext().getResponse().setBody(pattern.test(input));
    }

然後，在查詢中可以參考它，如下列範例所示：

    SELECT *
    FROM posts p
    WHERE regex_match(p.message, "doc.*db") = true

> 注意：UDF 只能存取輸入參數。UDF 內不支援任何 DocumentDB 儲存操作 (讀取、寫入、查詢等)。

> 注意：目前，查詢內只能使用一個 UDF。

UDF 可以處理並傳回 JSON 片段，而不只是常值。例如，“array\_count” 函數可以定義為：

    function array_count(input) {
        getContext().getResponse().setBody(input.length);
    }

然後，在查詢中用來尋找文件內某個陣列的大小：

    SELECT *
    FROM posts p
    WHERE array_count(p.likes) > 5

UDF 也可以在查詢的 SELECT 子句或 FROM 子句內指定。例如：

    SELECT array_count(p.likes) AS count
    FROM posts p

如需有關建立和管理 UDF 的詳細資訊，請參閱 JavaScript 程式設計文件。

# 分頁

每次查詢執行會根據用戶端設定的分頁大小而傳回一批結果。為了讀取查詢的所有結果，應用程式必須將每一個結果集分頁，直到讀取完所有資料為止。例如，若要讀取不超過 10 個文件，用戶端可以將分頁大小修改為 10，以限制傳回的文件數上限。請注意，查詢可能傳回比分頁大小更少的結果，某些查詢甚至不會傳回結果。若要讀取查詢的所有結果，用戶端應該使用回應接續 Token 來提取下一個批次，直到批次是空的為止。

    // Fetch pages of results up to 10 at a time. FeedOptions is optional
    DocumentServiceQuery<Database> query = (
        from db in client.CreateDatabaseQuery(new FeedOptions { MaxItemCount = 10 })
        where db.Name == dbName
        select db).AsDocumentServiceQuery();

    while (query.HasMoreResults)
    {
        databases.AddRange(await query.ExecuteNextAsync<Database>());
    }

這不是以 SQL 文法中的 TOP 或 LIMIT 關鍵字來實作，因為在基礎用戶端 SDK/REST API 中已透過接續 Token 提供此功能。如需詳細資訊和範例，請參閱 REST API 文件集中的 GET 文件，或 SDK 中的 ReadFeed 方法。

# 查詢一致性行為

DocumentDB 支援四種可由開發人員調整的一致性層級 – 增強式、陳舊-過期、工作階段和最終。查詢提供與一致性層級相同的保證。依預設，查詢的結果保證符合用戶端所要求的一致性層級。DocumentDB 的索引為記錄結構，設計為永遠保持最新且與資料一致，而不論數量。插入或更新文件時，它們會立即反映在查詢結果中。

對於需要最終一致性的應用程式，可選擇性地設定索引原則來使用「緩慢索引」。在此情況下，每當集合閒置時，就會以最終一致的方式更新索引。總結說明，下表顯示在不同資料庫帳戶組態下的查詢一致性行為。

| 資料一致性 | 索引原則 | 查詢行為  |
|------------|----------|-----------|
| 增強式     | 一致     | 增強式    |
| 界限-陳舊  | 一致     | 界限-陳舊 |
| 工作階段   | 一致     | 工作階段  |
| 最終       | 一致     | 最終      |
| 增強式     | 緩慢     | 最終      |
| 界限-陳舊  | 緩慢     | 最終      |
| 工作階段   | 緩慢     | 最終      |
| 最終       | 緩慢     | 最終      |

如需詳細資訊，請參閱索引原則和組態的相關文件。

# API 和 SDK

在 REST API、用戶端 SDK 和伺服器端 JavaScript API 中，可使用 SQL 文法執行查詢。

## 使用 REST API 執行查詢

應用程式可以使用 REST API，針對集合來 POST (傳遞) 查詢。查詢要求中應該包含下列項目

-   標頭 x-ms-documentdb-isquery：True 表示這是查詢
-   標頭 Content-Type：application/sql 表示使用 SQL 語言
-   Body，含有 SELECT 陳述式
    <!-- -->

    POST .../docs/executeQuery HTTP/1.1
    authorization:...
    x-ms-continuation:
    x-ms-activity-id:82342881-769e-4113-a662-a85c7617ed5b
    x-ms-date:Fri, 30 May 2014 22:46:13 GMT
    Match:
    x-docdb-resource-id:9MEKcum9C2g=
    x-docdb-entity-id:
    x-ms-documentdb-isquery:True
    Cache-Control:no-cache
    x-ms-version:2014-02-25
    User-Agent:Microsoft.Azure.Documents.Client/1.0.0.0
    Content-Type:application/sql
    Host:...
    Content-Length:59
    Expect:100-continue

    SELECT b.title FROM books b WHERE b.title = 'War and Peace'

> 注意：不支援在查詢中使用 GET 或使用查詢字串。

## 使用 .NET SDK 執行查詢

.NET SDK 支援使用 CreateDocumentQuery 方法執行查詢，此方法支援將 SQL 查詢當做字串。查詢的輸出是 IQueryable 介面，可使用用戶端 LINQ 來處理。

    IQueryable<dynamic> results = client.CreateDocumentQuery(collectionId).AsSQL<dynamic>(
       "SELECT b.title FROM books b WHERE b.title = 'War and Peace'");

如需詳細資訊，請參閱 .NET SDK 文件。

## 預存程序和觸發程序內的查詢

預存程序和觸發程序的伺服器端 JavaScript API 也支援使用 SQL 執行查詢。

    collection.queryDocuments(collection.GetSelfLink(),
    "SELECT b.title FROM books b WHERE b.title = 'War and Peace'",
    callback);

如需詳細資訊，請參閱 JavaScript 伺服器端 SDK 文件。

# 附錄 A – SQL 語法

下列鐵路圖顯示 DocumentDB 查詢語言的正規 SQL 文法

![][]
![][1]
![][2]
![][3]
![][4]
![][5]
![][6]
![][7]
![][8]
![][9]
![][10]
![][11]
![][12]
![][13]
![][14]
![][15]
![][16]

  []: ./media/documentdb-query/query1.png
  [1]: ./media/documentdb-query/query2.png
  [2]: ./media/documentdb-query/query3.png
  [3]: ./media/documentdb-query/query4.png
  [4]: ./media/documentdb-query/query5.png
  [5]: ./media/documentdb-query/query6.png
  [6]: ./media/documentdb-query/query7.png
  [7]: ./media/documentdb-query/query8.png
  [8]: ./media/documentdb-query/query9.png
  [9]: ./media/documentdb-query/query10.png
  [10]: ./media/documentdb-query/query11.png
  [11]: ./media/documentdb-query/query12.png
  [12]: ./media/documentdb-query/query13.png
  [13]: ./media/documentdb-query/query14.png
  [14]: ./media/documentdb-query/query15.png
  [15]: ./media/documentdb-query/query16.png
  [16]: ./media/documentdb-query/query17.png
