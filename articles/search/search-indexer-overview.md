<properties
	pageTitle="Azure 搜尋服務中的索引子 | Microsoft Azure | 雲端託管搜尋服務"
	description="耙梳資料庫以擷取可搜尋的資料並填入 Azure 搜尋服務索引。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="01/24/2016"
	ms.author="heidist"/>

# Azure 搜尋服務中的索引子
> [AZURE.SELECTOR]
- [Overview](search-indexer-overview.md)
- [Portal](search-import-data-portal.md)
- [Blob Storage (preview)](search-howto-indexing-azure-blob-storage.md)
- [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)

Azure 搜尋服務中的**索引子**是一種編目程式，其可從外部資料來源擷取可搜尋的資料和中繼資料，並根據索引和資料來源之間的欄位對欄位對應填入索引。這種方法有時稱為「提取模型」，因為您不需要撰寫任何程式碼來將資料推送至索引，服務就會自行提取資料。

您可以使用索引子做為擷取資料的唯一手段，或結合使用多項技術 (包含使用索引子) 來僅載入索引中的某些欄位。

索引子可以依需要執行，也可以依週期性的資料重新整理排程，最多每十五分鐘執行一次。若想更頻繁地進行更新，則 Azure 搜尋服務和外部資料來源中都必須要有可同時更新資料的發送模型。

索引子會從保有連接字串等資訊的**資料來源**提取資料。目前支援下列資料來源：

- [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) (或 Azure 虛擬機器中的 SQL Server)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md) (目前僅供預覽。從 PDF、Office 文件、HTML、XML 擷取文字。)

資料來源和使用資料來源的索引子是各自獨立設定與管理，這表示多個索引子可使用同一個資料來源來一次載入多個索引。

[.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx) 和[服務 REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx) 支援管理索引子和資料來源。或者，您也可以在使用 [匯入資料] 精靈時於入口網站中設定索引子。

<!---HONumber=AcomDC_0128_2016-->