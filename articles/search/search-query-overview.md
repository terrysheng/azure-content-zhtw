<properties
    pageTitle="查詢 Azure 搜尋服務索引 | Microsoft Azure | 雲端託管搜尋服務"
    description="在 Azure 搜尋服務中建立搜尋查詢，並使用搜尋參數來篩選及排序搜尋結果。"
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/10/2016"
    ms.author="ashmaka"/>

# 查詢 Azure 搜尋服務索引
> [AZURE.SELECTOR]
- [概觀](search-query-overview.md)
- [入口網站](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

在提交搜尋要求給 Azure 搜尋服務時，除了在應用程式搜尋方塊中實際輸入的單字以外，還可以指定幾個參數。這些查詢參數可讓您更深入地控制全文檢索搜尋體驗。

以下清單簡短說明查詢參數在 Azure 搜尋服務中的常見用法。如需查詢參數和其行為的完整說明，請參閱 [REST API](https://msdn.microsoft.com/library/azure/dn798927.aspx) 和 [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx) 的詳細資料頁面。

## 查詢類型

Azure 搜尋服務提供許多選項可建立功能極為強大的查詢。您將會使用的兩個主要查詢類型是 `search` 和 `filter`。`search` 查詢可在索引的所有可搜尋欄位中搜尋一或多個字詞，而且運作方式就如同您對 Google 或 Bing 等搜尋引擎的期待。`filter` 查詢可跨索引的所有可篩選欄位評估布林運算式。與 `search` 查詢不同的是，`filter` 查詢會完全符合欄位內容，也就是對字串欄位區分大小寫。

您可以同時或個別使用搜尋和篩選。如果同時使用，就會先將篩選套用到整個索引，再對篩選結果執行搜尋。由於篩選能夠減少搜尋查詢需要處理的資料集合，因此對於提升查詢效能方面是很實用的技術。

篩選運算式的語法是 [OData 篩選語言](https://msdn.microsoft.com/library/azure/dn798921.aspx)的子集。對於搜尋查詢，您可以使用[簡化語法](https://msdn.microsoft.com/library/azure/dn798920.aspx)或 [Lucene 查詢語法](https://msdn.microsoft.com/library/azure/mt589323.aspx)，下面會有這兩種語法的討論。

### 簡單查詢語法
[簡單查詢語法](https://msdn.microsoft.com/library/azure/dn798920.aspx)是 Azure 搜尋服務所使用的預設查詢語言。簡單查詢語法支援許多常見的搜尋運算子，包括 AND、OR、NOT、片語、尾碼和優先順序運算子。

### Lucene 查詢語法
[Lucene 查詢語法](https://msdn.microsoft.com/library/azure/mt589323.aspx)可讓您使用被開發做為 [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 一部分且廣為採用的易懂查詢語言。

使用此查詢語法可讓您輕鬆獲得下列功能︰[欄位範圍的查詢](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields)、[模糊搜尋](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy)、[鄰近搜尋](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity)、[詞彙提升](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost)、[規則運算式搜尋](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex)、[萬用字元搜尋](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard)、[語法基礎](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax)和[使用布林運算子的查詢](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean)。



## 排序結果
收到搜尋查詢的結果時，您可以要求 Azure 搜尋服務提供依特定欄位值排序的結果。根據預設，Azure 搜尋服務會以每份文件的搜尋分數排名 (衍生自 [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) 來排序搜尋結果。

如果您想要讓 Azure 搜尋服務在傳回結果時是以搜尋分數以外的值來排序，您可以使用 `orderby` 搜尋參數。您可以指定 `orderby` 參數的值，納入欄位名稱和 [`geo.distance()` 函式](https://msdn.microsoft.com/library/azure/dn798921.aspx)的呼叫以獲得地理空間值。每個運算式後面都可以加上 `asc` 來表示要以遞增順序要求結果，而加上 `desc` 則表示要以遞減順序要求結果。預設排名為遞增順序。

## 分頁
Azure 搜尋服務可讓您輕鬆地對搜尋結果分頁。透過使用 `top` 和 `skip` 參數，您就可以順利地發出搜尋要求，將所收到的整組搜尋結果分拆成方便管理且經過排序的子集，輕鬆實現良好的搜尋 UI 做法。在收到這些分拆成較小子集的結果時，您也會收到整組搜尋結果中所含文件的計數。

您可以在[如何在 Azure 搜尋服務中對搜尋結果分頁](search-pagination-page-layout.md)一文中深入了解如何對搜尋結果分頁。


## 搜尋結果醒目提示
在 Azure 搜尋服務中，只要使用 `highlight`、`highlightPreTag` 和 `highlightPostTag` 參數就可強調提示搜尋結果中符合搜尋查詢的確切部分。您可以指定哪些可搜尋欄位應該強調其相符的文字，以及指定要在 Azure 搜尋服務傳回的相符文字開頭和結尾附加的確切字串標記。

<!---HONumber=AcomDC_0316_2016-->