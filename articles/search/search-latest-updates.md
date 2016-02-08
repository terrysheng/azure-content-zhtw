<properties 
	pageTitle="Azure 搜尋服務最新更新中的新功能 | Microsoft Azure | 雲端託管搜尋服務" 
	description="描述服務之最新更新內容的 Azure 搜尋服務版本資訊" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/23/2016" 
	ms.author="heidist"/>

#Azure 搜尋服務的最新更新事項#

Azure 搜尋服務是 Microsoft Azure 上的雲端託管搜尋服務。這是正式運作版，並為支援的[搜尋服務 REST API 的 2015-02-28 版本](https://msdn.microsoft.com/library/azure/dn798935.aspx)組態提供可用性為 99.9% 的服務等級協定 (SLA)。

##各版本的主要功能以及發行日期

我們會透過 [REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)、[.NET SDK](http://go.microsoft.com/fwlink/?LinkId=528216) 或 [Azure 入口網站](https://portal.azure.com)的服務儀表板單獨或共同發佈各種功能。.NET 程式庫和 REST API 都有多種版本。如果我們推出新的功能，舊的 API 仍會保持正常運作。若要瞭解版本政策的詳細資訊，請造訪[搜尋服務版本](https://msdn.microsoft.com/library/azure/dn864560.aspx)。

預覽版和正式運作版 (GA) 的功能都繫結至相同類別的 API 版本。

- 預覽版的功能是實驗性的，且可能會在正式運作版上市之前就遭到變更，甚至放棄。預覽版功能都會一直存在於 [REST API 的預覽版本](search-api-2015-02-28-preview.md)中，有時會在 [.NET SDK](http://go.microsoft.com/fwlink/?LinkId=528216) 中。功能相關文件將會說明如何使用該功能。
- GA 的功能已經穩定，且不大可能會變更。我們會把 GA 功能的任何變更，都當做重大變更來宣佈。

純粹為入口網站或工具式的功能都會隨時間而改變，因此不會歸類為預覽版或正式運作版的變更。

##2016 年的新功能

功能|已發行|狀態|詳細資料
-------|--------|------|-------
[自訂分析器](https://azure.microsoft.com/blog/custom-analyzers-in-azure-search/)|2016 年 1 月|[預覽](search-api-2015-02-28-preview.md)|使用者定義的 Tokenizer 和權杖篩選器組態。請參閱 MSDN 上的 [Azure 搜尋服務中的分析](https://msdn.microsoft.com/library/azure/mt605304.aspx)。
[Azure Blob 儲存體索引子](search-howto-indexing-azure-blob-storage.md)|2016 年 1 月|[預覽](search-api-2015-02-28-preview.md)|PDF、Office 文件、XML、HTML，或甚至是視訊和音訊檔案，都可以合併或內嵌到 Azure 搜尋服務索引中。
[搜尋總管](search-explorer.md)|2016 年 1 月|[入口網站](https://portal.azure.com)|用來對索引進行臨機操作查詢的內建查詢工具。
[Azure 搜尋服務的 Power BI 內容套件](http://blogs.msdn.com/b/powerbi/archive/2016/01/19/visualizing-azure-search-data-with-power-bi.aspx)|2016 年 1 月|工具|利用 Azure 搜尋服務的新 Power BI 內容套件，來進行服務資料的視覺化及分析工作。透過搜尋分析來提供。
[搜尋分析](https://azure.microsoft.com/blog/analyzing-your-azure-search-traffic/)|2016 年 1 月|入口網站|啟用資料收集功能，來深入解析使用者的搜尋活動。
[.NET SDK 1.0.1 預覽版](https://msdn.microsoft.com/library/azure/dn951165.aspx)|2016 年 1 月|預覽|這是第三個重複推出的 .NET 用戶端程式庫，Microsoft.Azure.Search.dll。這個版本有重大變更。如需移轉的指南，請參閱[升級至 Azure 搜尋服務 .NET SDK 版本 1.0.1 預覽版](search-dotnet-sdk-migration.md)

##2015 年的新功能

功能|已發行|狀態|詳細資料
-------|--------|------|-------
Lucene 語言分析器|2015 年 10 月|GA|這項功能目前已正式推出，您可透過服務 REST API 及 .NET SDK 來使用。
[Microsoft 自然語言處理器](search-api-2015-02-28-Preview.md)|2015 年 10 月|GA|這項功能目前已正式推出，您可透過服務 REST API 及 .NET SDK 來使用。 
[Lucene 查詢語法支援](https://msdn.microsoft.com/library/azure/mt589323.aspx)|2015 年 9 月|[預覽](search-api-2015-02-28-preview.md)|新增 Lucene 的查詢分析器。如要使用新語法，您必須在搜尋文件作業中指定 `queryType`。
[自然語言處理器](search-language-support.md)|2015 年 9 月|[預覽](search-api-2015-02-28-preview.md)|新增 Microsoft 的語言處理器，以增加整體的語言數目，以及為其他使用者提供另一種實作方式。
搜尋、建議和查閱查詢中的 POST|2015 年 9 月|[預覽](search-api-2015-02-28-preview.md)|適用於服務 REST API。
[管理 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx)|2015 年 9 月|GA|管理 REST API 的第二個版本。包含 checkNameAvailability 檢查是否特定服務名稱已在使用中、複本範圍從原先的 1-6 變成 1-12、SKU 屬性已從屬性包移動到服務承載的最頂層、建立搜尋服務的回應主體已更新來容納設定的重新配置。
.NET SDK 0.10.0 預覽版本|2015 年 8 月|預覽|這是第二個重複推出的 .NET 用戶端程式庫，Microsoft.Azure.Search.dll。此版本新增透過 .NET 類別來建立、管理和使用[資料來源類別](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx)及[索引子類別](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx)的支援。此外，Azure SQL 索引子開始支援索引地理位置點。
fieldMapping 建構|2015 年 4 月|預覽|索引子目前支援 fieldMapping 建構，當實際欄位名稱和外部資料庫及 Azure 搜尋服務索引不同時，會提供欄位指派。請參閱[索引子](search-api-indexers-2015-02-28-Preview.md)一文，以了解索引子文件的 `2015-02-28-preview` 版本。
欄位類型轉換|2015 年 4 月|預覽|索引子現在也支援欄位類型轉換，讓您在原始欄位代表 JSON 陣列時，可以將 SQL 資料表中的字串欄位對應到搜尋索引中的字串集合欄位。
[服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)|2015 年 3 月|GA|Azure 搜尋服務 REST API 的第一個正式運作版。此版本包含舊版的功能，它也包含[索引子](http://go.microsoft.com/fwlink/p/?LinkID=528210)。[建議工具](https://msdn.microsoft.com/library/azure/dn798936.aspx)透過新增中置比對支援，取代了上一個實作 (僅比對字首) 中限制較多的預先輸入查詢支援。此實作可以在詞彙的任何地方找到符合項目，並且支援模糊比對。[標記提升](http://go.microsoft.com/fwlink/p/?LinkId=528212)啟用了新的評分設定檔案例。特別的是，此功能會運用保存的資料 (如購物喜好設定)，讓您可以依據個人化資訊提升個別使用者的搜尋結果品質。 
.NET SDK 0.9.6 預覽版本|2015 年 3 月|預覽|此為第一個 Azure 搜尋服務專用的 .NET SDK 公用版本。它包含用戶端程式庫 Microsoft.Azure.Search.dll，其中擁有兩個命名空間：[Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx) 和 [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)
管理 REST API|2015 年 3 月|GA|管理 REST API 的第一個版本，包含在 Azure 搜尋服務正式運作版中。這個版本與舊版預覽的功能沒有不同。
[Microsoft 自然語言處理器](search-api-2015-02-28-Preview.md)|2015 年 3 月|預覽|新增更多語言，並為 Office 和 Bing 支援的所有語言提供廣泛的詞幹分析。
[moreLikeThis=](search-api-2015-02-28-Preview.md)|2015 年 3 月|預覽|與 `search=` 互斥的搜尋參數，會觸發替代的查詢執行路徑。`moreLikeThis=` 會透過比較可搜尋的欄位，尋找與指定文件相似的文件，而不是根據輸入的搜尋詞彙對 `search=` 進行全文搜尋。

##2014 年的新功能

功能|已發行|狀態|詳細資料
-------|--------|------|-------
Lucene 語言分析器|November 2014|預覽|已新增來為 Lucene 發佈的自訂語言分析器提供多語言支援。 
引進入口網站支援來建立索引|November 2014|[入口網站](https://portal.azure.com)|索引、分析器，及評分設定檔可以建構在入口網站中。
管理 api-版本 2014-07-31-預覽|2014 年 10 月|預覽|管理 REST API 的第一個公開預覽版本。我們會根據收到的要求來提供此 API 版本的文件。
搜尋服務 REST API|2014 年 8 月|預覽|服務 REST API 的第一個公開預覽版本 (api-version-2014-07-31-Preview)。這是索引和文件作業、微調搜尋結果用的評分設定檔、地理空間支援的 REST API。這個版本支援在 Azure 入口網站中佈建服務。我們會根據收到的要求來提供此 API 版本的文件。REST API 服務單獨建自己的版本。














 

<!---HONumber=AcomDC_0128_2016-->