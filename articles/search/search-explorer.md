<properties
	pageTitle="在 Azure 入口網站中使用搜尋總管查詢 Azure 搜尋服務索引 | Microsoft Azure | 雲端託管搜尋服務"
	description="搜尋總管是用來在 Azure 入口網站中查詢 Azure 搜尋服務索引的無程式碼方法。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="01/23/2016"
	ms.author="heidist"/>

# 在 Azure 入口網站中使用搜尋總管查詢 Azure 搜尋服務索引
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

**搜尋總管**是內建於 Azure 入口網站中，用來對 Azure 搜尋服務索引進行無程式碼查詢的查詢工具。它會連接到服務中的任何索引，並提供搜尋方塊以供輸入搜尋字串和運算式。系統會根據您提供的輸入產生有效的查詢語法。結果則會顯示在入口網站頁面中。

搜尋總管適合用來學習查詢語法、執行非經常性的臨機操作查詢，或調整查詢運算式然後再嘗試將它放在程式碼中。若要使用搜尋總管，您必須已經擁有 Azure 搜尋服務和索引。請參閱[在入口網站中建立 Azure 搜尋服務](search-create-service-portal.md)和[使用入口網站將資料匯入至 Azure 搜尋服務](search-import-data-portal.md)以取得這些工作的說明。

## 開啟搜尋總管

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 開啟 Azure 搜尋服務的服務儀表板。以下提供一些尋找儀表板的方法。
	- 在 Jumpbar 中，按一下 [首頁]。首頁上有您訂用帳戶中每個服務的磚。按一下圖格即可開啟服務儀表板。
	- 在動態工具列中，依序按一下 [全部瀏覽] > [篩選依據] > [搜尋服務]，即可在清單中找到您的搜尋服務。

3. 您將會在服務儀表板的頂端看見命令列，其中包括 [搜尋總管]。

  	![][1]

4. 按一下 [搜尋總管] 以滑動開啟 [搜尋總管] 刀鋒視窗。
5. 設定索引和 API 版本。搜尋總管會自動連線到索引清單中的第一個索引，但是您可以按一下 [變更索引] 來切換到其他索引。[設定 API 版本] 可讓您指定正式上市版本或預覽版本。某些查詢語法僅供預覽。
6. 如果您是依照[開始使用 Azure 搜尋服務](search-get-started-portal.md)，並根據美國地質調查局 (USG) 的羅德島州資料建立和填入索引，您可以使用此搜尋字詞來確認搜尋總管中會傳回相同的 3 個結果：`roger williams +school -building`

請注意為回應輸入的搜尋字詞所自動產生的查詢語法。

![][2]

## 後續步驟

如需查詢語法和範例的詳細資訊，請參閱 MSDN 上的[搜尋文件](https://msdn.microsoft.com/library/azure/dn798927.aspx)。

請造訪下列連結，以了解其他用來建立或管理搜尋服務或索引的無程式碼方法：

- [在入口網站中建立 Azure 搜尋服務](search-create-service-portal.md)
- [使用入口網站將資料匯入至 Azure 搜尋服務](search-import-data-portal.md)
- [在 Azure 上管理搜尋服務](search-manage.md)

<!--Image References-->
[1]: ./media/search-explorer/AzSearch-SearchExplorer-Btn.png
[2]: ./media/search-explorer/AzSearch-SearchExplorer-Example.png

<!---HONumber=AcomDC_0128_2016-->