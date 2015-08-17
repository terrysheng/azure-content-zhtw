<properties 
	pageTitle="在 DocumentDB 中自動編製索引 | Microsoft Azure" 
	description="了解自動編製索引在 Azure DocumentDB 中運作的方式。" 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="mimig" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/03/2015" 
	ms.author="arramac"/>
	
# 在 Azure DocumentDB 中自動編製索引

本文為在 DocumentDB 中自動編製索引的運作方式的介紹，並摘錄自將在 [2015 年 VLDB 會議](http://www.vldb.org/2015/)中發表的 "Schema-Agnostic Indexing with Azure DocumentDB" 文章。閱讀本文後，將會回答您下列問題：

- DocumentDB 如何從 JSON 文件推斷結構描述？
- DocumentDB 如何跨不同的文件建置索引？
- DocumentDB 如何大規模地執行自動編製索引？

##<a id="HowDocumentDBIndexingWorks"></a>DocumentDB 索引編製的運作方式

[Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) 是真正為 JSON 建置的無結構描述資料庫用途。它不會要求或需要任何結構描述或次要索引定義，就能大規模地編製資料索引。這可讓您使用 DocumentDB 快速地定義並逐一查看應用程式資料模型。將文件新增至集合時，DocumentDB 會自動為所有文件屬性編製索引，讓它們可供您查詢。自動編製索引可讓您儲存屬於完全任意結構描述的文件，而不需擔心結構描述或次要索引。

DocumentDB 利用 JSON 的簡易性及其缺乏結構描述規格，達到排除資料庫與應用程式撰寫模型之間阻抗失配的目的。它不會對文件做出任何假設，並允許 DocumentDB 集合內的文件除了執行個體特定值之外，在結構描述上也有所變化。相較於其他文件資料庫，DocumentDB 的資料庫引擎是直接在 JSON 語法層級運作，無須了解文件結構描述的概念，使得文件的結構和執行個體值之間的界限變得模糊不清。這種情況相對地可讓它自動編製文件索引，而不需要結構描述或次要索引。

DocumentDB 中的索引編製利用了 JSON 語法允許**以樹狀結構表示**文件的這一點。若要以樹狀結構表示 JSON 文件，必須建立一個虛擬根節點，做為其下文件中其餘實際節點的父系。每個包括 JSON 文件中陣列索引的標籤都會成為樹狀結構的節點。下圖說明範例 JSON 文件及其對應的樹狀結構表示法。

>[AZURE.NOTE]JSON 具有自我描述的特性，也就是說，每個文件都包含結構描述 (中繼資料) 和資料，例如 `{"locationId", 5, "city": "Moscow"}` 會顯示有兩個屬性 `locationId` 和 `city`，而且具有數值和字串屬性值。DocumentDB 能夠推斷文件的結構描述，並在插入或取代文件時編製索引，而不需要您定義結構描述或次要索引。


**以樹狀結構表示 JSON 文件：**

![以樹狀結構表示文件](media/documentdb-indexing/DocumentsAsTrees.png)

例如，在以上所示的範例中：

- 上述範例中 JSON 屬性的 `{"headquarters": "Belgium"}` 屬性會對應至 path/headquarters/Belgium。
- JSON 陣列 `{"exports": [{"city": “Moscow"}`、`{"city": Athens"}]}` 會對應至路徑 `/exports/[]/city/Moscow` 和 `/exports/[]/city/Athens`。

使用自動編製索引時，(1) 文件樹狀結構中的每個路徑都會進行索引編製 (除非開發人員已明確設定編製索引原則來排除特定的路徑模式)。(2) DocumentDB 集合的每個文件更新都會導致索引結構的更新 (亦即，導致新增或移除節點)。自動編製文件索引的主要需求之一，就是確保編製索引和查詢具有深層巢狀結構 (例如 10 層) 的文件的成本，與包含深度只有一個層級的機碼值組的一般 JSON 文件的成本相同。因此，標準化的路徑表示法是建置自動編製索引及查詢子系統的基礎。

就路徑而言，統一處理結構描述和執行個體值的一個重要含意是，在邏輯上，就像是個別文件一樣，顯示的兩個文件索引 (保留路徑以及包含該路徑之文件識別碼之間的對應) 也可以用樹狀結構表示。DocumentDB 使用這個事實來建置索引樹狀結構，而且這個樹狀結構是從結合代表集合內個別文件的所有樹狀結構建構出來的。DocumentDB 集合中的索引樹狀結構會隨著時間成長，因為新文件會新增或更新到集合中。


**以樹狀結構表示 DocumentDB 索引：**

![以樹狀結構表示索引](media/documentdb-indexing/IndexAsTree.png)

儘管 DocumentDB 的 SQL 和 JavaScript 查詢語言具有無結構描述的特性，還是會提供關聯式投影和篩選、跨文件的階層式導覽，以及叫用完全以 JavaScript 撰寫的 UDF。DocumentDB 的查詢執行階段可以支援這些查詢，因為它可以直接針對資料的這個索引樹狀結構表示法運作。

預設的索引編製原則會自動為所有文件的所有屬性編製索引，並提供一致的查詢 (亦即，文件寫入時，會同步更新索引)。DocumentDB 如何支援索引樹狀結構的大規模一致更新？ DocumentDB 使用寫入最佳化、無鎖定以及將記錄結構化的索引維護技術。也就是說，DocumentDB 既可以支援大量的快速寫入，同時又能提供一致的查詢。

DocumentDB 的索引編製功能是針對儲存效率而設計，並可處理多租用戶。基於成本效益，索引的磁碟儲存體額外負荷很低且可預測。索引更新也會在為每個 DocumentDB 集合配置的系統資源預算內執行。

##<a name="NextSteps"></a> 後續步驟
- 既然您已經看到索引編製的運作方式，請了解如何[使用 DocumentDB SQL 進行查詢](documentdb-sql-query.md)
- 在[這裡](documentdb-indexing-policies.md)了解如何自訂 DocumentDB 索引
 

<!---HONumber=August15_HO6-->