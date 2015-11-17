<properties
	pageTitle="Azure 搜尋服務中的資料匯入 | Microsoft Azure | 雲端託管搜尋服務"
	description="如何將資料上傳至 Azure 搜尋服務中的索引"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# 將資料匯入至 Azure 搜尋服務
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST API](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Azure 搜尋服務是一項雲端託管搜尋服務，可提供搜尋引擎、搜尋功能和資料儲存空間。此服務可在持續性資料 (索引) 上運作，以提供用來處理索引、執行查詢或制訂搜尋結果的文件和資訊。除了這些高階作業，還有一些特定低階作業可進行文字分析、排名、篩選、排序、Facet 處理，以及其他用來計算或顯示搜尋結果的功能。

搜尋經驗的相關性取決於您上傳的資料品質以及需要重新整理資料的頻率。在本文中，我們將介紹可供匯入及重新整理索引的推送及提取 (搜耙) 方法。

索引必須已經存在，而且您上傳的資料必須符合結構描述，您才可以匯入資料。如需詳細資訊，請參閱 [Azure 搜尋服務中的索引](search-what-is-an-index.md)。

##資料集考量

您上傳的資料類型不受限制，只要結構描述和資料集構成 JSON 結構即可。

您載入的資料應來自您的自訂應用程式建立或取用的資料庫或資料來源。例如，如果您的應用程式為線上零售目錄，您為 Azure 搜尋服務建立的索引應從支援您的應用程式的產品庫存或銷售資料庫提取資料。

Azure 搜尋服務中的索引會從單一資料表、檢視、Blob 容器或對等項目取得資料。您可能需要在提供資料給 Azure 搜尋服務的資料庫或 noSQL 應用程式中建立資料結構。或者，對於 Azure SQL Database 或 DocumentDB 等資料來源，您可以建立索引子來搜耙外部資料表、檢視或 Blob 容器，以便將資料上傳到 Azure 搜尋服務。

##延遲和資料同步處理需求

下表摘要摘要常見的需求以及符合這些需求的建議。

|需求|建議|
|------------|---------------|
|接近即時的資料同步處理|用來將更新推送至索引的程式碼 (.NET 或 REST API)。資料擷取的其中一種提取方法為排程作業，但排程作業的執行速度趕不上主要資料來源中的快速變更。|
|Azure VM 上的 Azure SQL Database、DocumentDB 或 SQL Server|索引子會限定於特定的資料來源類型。如果主要資料來源位於支援的資料來源中，您可以使用索引子來搜耙資料來源及排程資料重新整理 (採用每小時間隔頻率)。您可以在入口網站或程式碼中設定索引子。|
|排程的資料重新整理|使用索引子 (請參閱上述資料)。|
|編目程式|使用索引子 (請參閱上述資料)。|
|無程式碼的原型或編輯|入口網站中的 [匯入資料精靈] 可設定索引子，有時還可產生初步結構描述 (如果主要資料庫中有足夠的資訊可這麼做)。此精靈包含用來設定排程資料重新整理的選項。(選擇性) 您可以新增語言分析器或 CORS 選項。但有幾個缺點：無法新增評分設定檔，也無法將入口網站中建立的結構描述匯出至 JSON 檔案，以便在程式碼中使用。| 

<!---HONumber=Nov15_HO3-->