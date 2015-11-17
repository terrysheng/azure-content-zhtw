<properties
	pageTitle="使用 .NET 建立 Azure 搜尋服務索引 | Microsoft Azure | 雲端託管搜尋服務"
	description="使用 Azure 搜尋服務 .NET SDK 或程式庫在程式碼中建立索引。"
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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# 使用 .NET 建立 Azure 搜尋服務索引
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

本文將說明如何使用 [Azure 搜尋服務 .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) 建立索引。以下內容屬於[如何從 .NET 應用程式使用 Azure 搜尋服務](search-howto-dotnet-sdk.md)的一部分。請參閱上層文章中的端對端步驟。

建立索引的必要條件包括先前已建立您的搜尋服務連線，通常是透過 `SearchServiceClient` 完成。確保無阻力的重新部署的最佳作法是刪除現有的同名索引 (若已存在)。

假設有名為 'hotels' 的索引，您可以為此建構一個方法，如下所示：

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

此方法使用指定的 `SearchServiceClient` 查看索引是否存在，如果存在，就加以刪除。

若要建立新的 "hotels" 索引，請建構如下所示的方法：

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

此方法會以定義新索引結構描述的 `Field` 物件清單，建立新的 `Index` 物件。每個欄位均有一個名稱、資料類型和一些屬性，以用於定義欄位的搜尋行為。除了欄位之外，您還可以新增評分設定檔、建議工具或 CORS 選項到 Index (基於簡化目的，範例已省略這些選項)。如需 Index 物件以及其組成部分的詳細資訊，請參閱 [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx) 中的 SDK 參考和 [Azure 搜尋服務 REST API 參考](https://msdn.microsoft.com/library/azure/dn798935.aspx)。

<!---HONumber=Nov15_HO3-->