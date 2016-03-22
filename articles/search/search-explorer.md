<properties
    pageTitle="使用 Azure 入口網站查詢 Azure 搜尋服務索引 | Microsoft Azure | 雲端託管搜尋服務"
    description="在 Azure 入口網站的搜尋總管中發出搜尋查詢。"
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/10/2016"
    ms.author="ashmaka"
/>
# 使用 Azure 入口網站查詢 Azure 搜尋服務索引
> [AZURE.SELECTOR]
- [概觀](search-query-overview.md)
- [入口網站](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

本指南將示範如何在 Azure 入口網站中查詢 Azure 搜尋服務索引。

在開始閱讀本逐步解說前，請先[建立好 Azure 搜尋服務索引](search-what-is-an-index.md)，並[在索引中填入資料](search-what-is-data-import.md)。

## I.前往 Azure 搜尋服務刀鋒視窗
1. 按一下 [Azure 入口網站](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)左側功能表中的 [所有資源]
2. 選取您的 Azure 搜尋服務

## II.選取您想要搜尋的索引
1. 選取您想要從 [索引] 圖格搜尋的索引。

![](./media/search-explorer/pick-index.png)

## III.按一下 [搜尋總管] 圖格
![](./media/search-explorer/search-explorer-tile.png)

## III.開始搜尋
1. 若要搜尋 Azure 搜尋服務索引，請先在 [查詢字串] 欄位輸入文字，然後按 [搜尋]。
 * 使用 [搜尋總管] 時可以指定任何[查詢參數](https://msdn.microsoft.com/library/dn798927.aspx)

2. 在 [結果] 區段中，查詢結果會以原始 JSON 呈現，當您對 Azure 搜尋服務 REST API 發出搜尋要求時，即會在 HTTP 回應本文中收到。
3. 查詢字串會自動剖析為適當的要求 URL，以便對 Azure 搜尋服務 REST API 提交 HTTP 要求

![](./media/search-explorer/search-bar.png)

<!---HONumber=AcomDC_0316_2016-->