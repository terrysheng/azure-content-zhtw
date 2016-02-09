<properties
	pageTitle="使用 .NET 在 Azure 搜尋服務中建立查詢 | Microsoft Azure | 雲端託管搜尋服務"
	description="使用 .NET 程式庫或 SDK 在 Azure 搜尋服務中建立搜尋查詢，並使用搜尋參數來篩選、排序和 Facet 處理搜尋結果。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/10/2015"
	ms.author="heidist"/>

#使用 .NET 在 Azure 搜尋服務中建立查詢
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

本文將說明如何使用 [Azure 搜尋服務 .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) 建立查詢。以下內容屬於[如何從 .NET 應用程式使用 Azure 搜尋服務](search-howto-dotnet-sdk.md)的一部分。請參閱上層文章中的端對端步驟。

建立查詢的必要條件包括先前已建立您的搜尋服務連線，通常是透過 `SearchServiceClient` 完成。

下列程式碼片段會建立一個方法，以將搜尋字串輸入傳遞至 SearchDocuments 方法。

	private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
	{
		// Execute search based on search text and optional filter
		var sp = new SearchParameters();
	
		if (!String.IsNullOrEmpty(filter))
		{
			sp.Filter = filter;
		}
	
		DocumentSearchResult<Hotel> documentSearchResult = indexClient.Documents.Search<Hotel>(searchText, sp);
		foreach (SearchResult<Hotel> result in documentSearchResult.Results)
		{
			Console.WriteLine(result.Document);
		}
	}
	
首先，此方法會建立新的 SearchParameters 物件。該物件用於指定查詢的其他選項，例如排序、篩選、分頁及 Facet。在此範例中，我們只設定 Filter 屬性。

下一步是實際執行搜尋查詢，我們透過 Documents.Search 方法完成此步驟。在此方法中，我們傳遞搜尋文字做為字串，並再加上先前建立的搜尋參數。我們也指定了 Hotel 做為 Documents.Search 的類型參數，藉此告訴 SDK 將搜尋結果中的文件還原序列化為 Hotel 類型的物件。

最後，此方法會反覆查詢搜尋結果中的所有相符項，然後將每個文件列印至主控台。

我們來進一步了解如何呼叫此方法：

	SearchDocuments(indexClient, searchText: "fancy wifi");
	SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

在第一個呼叫中，我們要尋找含有 "fancy" 或 "wifi" 查詢字詞的所有文件。在第二個呼叫中，搜尋字詞設為 "*"，這表示「全部尋找」。如需搜尋查詢運算式語法的詳細資訊，請參閱 [Azure 搜尋服務中的簡單查詢語法](https://msdn.microsoft.com/library/azure/dn798920.aspx)。

第二個呼叫使用 OData $filter 運算式，類別為 eq 'Luxury'。這會將搜尋限制為只傳回類別欄位完全符合 "Luxury" 字串的文件。您可以在 [Azure 搜尋服務的 OData 運算式語法](https://msdn.microsoft.com/library/azure/dn798921.aspx)找到 OData 語法的詳細資訊。

<!---HONumber=AcomDC_0204_2016-->