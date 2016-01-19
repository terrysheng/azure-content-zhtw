<properties
	pageTitle="使用入口網站將資料匯入至 Azure 搜尋服務 | Microsoft Azure | 雲端託管搜尋服務"
	description="如何使用入口網站將資料上傳至 Azure 搜尋服務中的索引"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="Azure Portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# 使用入口網站將資料匯入至 Azure 搜尋服務
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST API](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Azure 入口網站的 Azure 搜尋服務儀表板上包含 [匯入資料] 命令，可引導您在 Azure 搜尋服務中進行資料擷取。此命令依賴內建索引子功能來搜耙現有的資料來源，並根據在資料來源中找到的資料列集建立並上傳文件。

使用精靈時，資料匯入為包含 3 部分的建構：

- 資料來源連接
- 資料上傳到其中的目標索引 (此精靈通常可為您產生)
- 現在或定期執行的排程

若要使用索引子或 [匯入資料] 命令，主要資料來源必須是其中一個支援的資料來源：Azure SQL Database、Azure VM 上的 SQL Server 關聯式資料庫或 Azure DocumentDB。

您可以只從單一資料表、檢視或對等的資料結構匯入。您可能必須先在應用程式資料來源中建立此資料結構，以便在搜尋索引中取得適當的中繼資料和資料輸入。

##設定資料匯入

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 開啟 Azure 搜尋服務的服務儀表板。以下提供一些尋找儀表板的方法。
	- 在 Jumpbar 中，按一下 [首頁]。首頁上有您訂用帳戶中每個服務的磚。按一下圖格即可開啟服務儀表板。
	- 在動態工具列中，依序按一下 [全部瀏覽] > [篩選依據] > [搜尋服務]，即可在清單中找到您的搜尋服務。

3. 您將會在服務儀表板的頂端看見命令列，其中包括 [匯入資料]。按一下 [匯入資料] 即可滑動開啟 [匯入資料] 刀鋒視窗。

4. 按一下 [連接到您的資料] 可指定索引子所使用的資料來源定義。選項包括：
	- 	現有的資料來源指的是先前為索引子建立的資料來源定義。如果您的搜尋服務中已經定義索引子，您可以針對另一次匯入重新規劃資料來源定義。
	- 	Azure SQL 用來指定 Azure 上的 SQL Database 或 Azure VM 上的 SQL Server 資料庫的資料來源連接。 
	- 	DocumentDB 用來指定該資料來源類型的資料來源連接。 

   對於 Azure SQL 和 DocumentDB，資料庫必須存在您的訂用帳戶內。您可以貼上連接字串 (如果您知道的話)，或選擇先前由具有寫入權限的人員為您的訂用帳戶建立的資料來源。

5. 按一下 [自訂目標索引] 來完成預設索引。
	- [索引鍵] 為必要項目。您為索引鍵選取的欄位必須是包含唯一值的字串欄位。
	- 通常會為您填入欄位名稱和類型。您可以變更資料類型。
	- 選取每個欄位的屬性：
		- Retrievable 會在搜尋結果中傳回欄位。
		- Filterable 允許在篩選運算式中參考欄位。
		- Sortable 允許欄位用於排序。
		- Facetable 可讓欄位進行多面向導覽。
		- Searchable 能夠進行全文檢索搜尋。
	- 如果您要在欄位層級指定語言分析器，請按一下 [分析器] 索引標籤。如需詳細資訊，請參閱[以多種語言建立文件的索引](search-language-support.md)。
	- 如果您要啟用自動完成或預先輸入查詢建議，請按一下 [建議工具]。

6. 按一下 [匯入資料] 以使用 [立即執行] 選項執行匯入作業，或設定週期性排程。

您剛完成的資料匯入作業會在幕後建立索引子。您現在可以直接編輯索引子，以變更其元件部分。
	
##編輯現有的索引子

在服務儀表板中，按兩下 [索引子] 磚即可顯示為您的訂用帳戶建立的所有索引子清單。按兩下其中一個索引子，加以執行、編輯或刪除。

<!---HONumber=AcomDC_0114_2016-->