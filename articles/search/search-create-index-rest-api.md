<properties
	pageTitle="使用 REST API 建立 Azure 搜尋服務索引 | Microsoft Azure | 雲端託管搜尋服務"
	description="使用 Azure 搜尋服務和 HTTP REST API 在程式碼中建立索引。"
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
	ms.date="11/17/2015"
	ms.author="heidist"/>

# 使用 REST API 建立 Azure 搜尋服務索引
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

本文將說明如何使用 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 建立索引。下列內容部分是來自[建立索引 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx)。請參閱上層文章以取得更多內容。

建立索引的必要條件包括先前已建立您的搜尋服務連線，通常是透過 'httpClient' 完成。

## 定義索引結構描述

若要使用 REST API 建立索引，您將會發出 POST 要求到 Azure 搜尋服務 URL 端點。您會在要求主體中使用 JSON 定義索引的結構。

**要求和要求標頭**：

在下列範例中，您將必須使用您的服務的 URL 端點，特別要注意服務名稱和適當的 API 版本 (發行這份文件時最新的 API 版本是 "2015-02-28")。在要求標頭中，您也必須提供[建立服務](https://msdn.microsoft.com/library/azure/dn798941.aspx/)時收到的服務主要系統管理金鑰。

	POST https://[servicename].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**要求本文**：

此位置可供您用來識別您的索引名稱 (此情況中的 "hotels") 以及[欄位的名稱、類型和屬性](https://msdn.microsoft.com/library/azure/dn798941.aspx)。

	{
		"name": "hotels",  
		"fields": [
			{"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
			{"name": "baseRate", "type": "Edm.Double"},
			{"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
			{"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer:"fr.lucene"},
			{"name": "hotelName", "type": "Edm.String"},
			{"name": "category", "type": "Edm.String"},
			{"name": "tags", "type": "Collection(Edm.String)"},
			{"name": "parkingIncluded", "type": "Edm.Boolean"},
			{"name": "smokingAllowed", "type": "Edm.Boolean"},
			{"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
			{"name": "rating", "type": "Edm.Int32"},
			{"name": "location", "type": "Edm.GeographyPoint"}
		]
	}

對於成功的要求，您應該會看到狀態碼「201 已建立」。如需透過 REST API 建立索引的詳細資訊，請瀏覽[此頁面](https://msdn.microsoft.com/library/azure/dn798941.aspx)。

<!---HONumber=Nov15_HO4-->