<properties
	pageTitle="使用 REST 呼叫在 Azure 搜尋服務中建立查詢 | Microsoft Azure | 雲端託管搜尋服務"
	description="使用 .NET 程式庫或 SDK 在 Azure 搜尋服務中建立搜尋查詢，並使用搜尋參數來篩選、排序和 facet 搜尋結果。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/17/2016"
	ms.author="heidist"/>

# 使用 REST 呼叫在 Azure 搜尋服務中建立查詢
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

本文將說明如何使用 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 建構對索引的查詢。下列內容部分是來自[搜尋文件 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx)。請參閱上層文章以取得更多內容。

匯入的必要條件包括備妥現有的索引 (隨著提供可搜尋資料的文件一起載入)。

若要使用 REST API 搜尋索引，您將發出 GET HTTP 要求。查詢參數會在 HTTP 要求的 URL 內定義。

**要求和要求標頭**：

在 URL 中，您將必須提供您的服務名稱、索引名稱和適當的 API 版本。URL 結尾的查詢字串是您將用來提供查詢參數的位置。查詢字串中的其中一個參數必須是適當的 API 版本 (發行這份文件時最新的 API 版本是 "2015-02-28")。

對於要求標頭，您必須定義內容類型，並提供服務的主要或次要系統管理金鑰。

	GET https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Azure 搜尋服務提供許多選項可建立功能極為強大的查詢。若要深入了解查詢字串的所有不同參數，請造訪[此頁面](https://msdn.microsoft.com/library/azure/dn798927.aspx)。

**範例**：

以下是各種不同查詢字串的幾個範例。這些範例會使用名為 "hotels" 的空索引：

搜尋整個索引中的 "quality" 一詞：

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=quality&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

搜尋整個索引：

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

搜尋整個索引和依據特定欄位排序 (lastRenovationDate)：

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

成功的查詢要求會造成狀態碼「200 確定」，而可在回應主體中找到 JSON 格式的搜尋結果。若要深入了解，請瀏覽[此頁面](https://msdn.microsoft.com/library/azure/dn798927.aspx)的「回應」一節。

<!---HONumber=AcomDC_0224_2016-->