<properties
	pageTitle="使用 REST API 將資料匯入至 Azure 搜尋服務 | Microsoft Azure | 雲端託管搜尋服務"
	description="如何使用 REST API 將資料上傳至 Azure 搜尋服務中的索引。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/17/2015"
	ms.author="heidist"/>

# 使用 REST API 將資料匯入至 Azure 搜尋服務
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

本文將說明如何使用 [Azure 搜尋服務 .REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 填入索引。下列內容部分是來自[加入、更新或刪除文件 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798930.aspx)。請參閱上層文章以取得更多內容。

若要使用 REST API 將文件推送至索引，您會發出 POST HTTP 要求至您的服務 URL 端點。

**要求和要求標頭**：

在 URL 中，您將必須提供您的服務名稱和適當的 API 版本 (發行這份文件時最新的 API 版本是 "2015-02-28")。

在要求標頭中，您必須定義內容類型，並提供服務的主要系統管理金鑰。

	POST https://[servicename].search.windows.net/indexes/[indexname]/docs/index?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**要求本文**：


	{
		"value": [
			{
				"@search.action": "upload",
				"hotelId": "1",
				"baseRate": 199.0,
				"description": "Best hotel in town",
				"description_fr": "Meilleur hôtel en ville",
				"hotelName": "Fancy Stay",
				"category": "Luxury",
				"tags": ["pool", "view", "wifi", "concierge"],
				"parkingIncluded": false,
				"smokingAllowed": false,
				"lastRenovationDate": "2010-06-27T00:00:00Z",
				"rating": 5,
				"location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
			},
			{
				"@search.action": "upload",
				"hotelId": "2",
				"baseRate": 79.99,
				"description": "Cheapest hotel in town",
				"description_fr": "Hôtel le moins cher en ville",
				"hotelName": "Roach Motel",
				"category": "Budget",
				"tags": ["motel", "budget"],
				"parkingIncluded": true,
				"smokingAllowed": true,
				"lastRenovationDate": "1982-04-28T00:00:00Z",
				"rating": 1,
				"location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
			}
		]
	}

在此情況下，我們使用 "upload" 做為我們的搜尋動作。更新和刪除現有的文件時，您可以使用 "merge"、"mergeOrUpload" 和 "delete"。

更新您的索引時若成功，將會收到狀態碼「200 確定」。如果在要求中至少有一個項目未成功建立索引，就會收到「207」狀態碼。

如需文件動作和成功/錯誤回應的詳細資訊，請參閱[此頁面](https://msdn.microsoft.com/library/azure/dn798930.aspx)。

<!---HONumber=Nov15_HO4-->