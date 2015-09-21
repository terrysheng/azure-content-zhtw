<properties
	pageTitle="何謂 Azure 搜尋服務"
	description="Azure 搜尋服務的技術概觀和功能摘要"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

# 何謂 Azure 搜尋服務？

Azure 搜尋服務是完全受管理的雲端服務，可讓開發人員使用 .NET SDK 或 REST API 建置豐富的搜尋應用程式。它包含範圍涵蓋您整個內容的全文檢索，再加上那些常見於商業 Web 搜尋引擎的類似進階搜尋行為，例如根據局部詞彙輸入的自動提示查詢、符合結果醒目提示，以及多面向瀏覽。自然語言支援是內建功能，其會使用適用於指定之語言的語言規則。

您可以根據增加的搜尋或儲存容量調整服務。例如，零售商可以增加容量，以滿足與假日購物或促銷活動相關聯的額外容量。

Azure 搜尋服務是 API 型服務，適用於知道如何使用 Web 服務和 HTTP 的開發人員和系統整合者。Azure 搜尋服務可去掉管理雲端搜尋服務的複雜度，並簡化搜尋型 Web 和行動應用程式的建立方式。

##運作方式

Azure 搜尋服務是 [PaaS 服務](https://wikipedia.org/wiki/Platform_as_a_service)，可將伺服器和基礎結構管理委託給 Microsoft，留下您只需填入搜尋資料的立即可用服務，然後可從您的應用程式存取此服務。根據設定服務的方式，您將使用與其他 Azure 搜尋服務訂閱者共用的搜尋服務，或提供只供您服務使用之專用資源的標準定價層。標準搜尋是可調整的，其選項可滿足由於儲存體或查詢負載而增加的需求。

Azure 搜尋服務會將您的資料儲存在可以透過全文檢索查詢搜尋的索引中。您可以在 Azure 入口網站，或使用用戶端程式庫或 REST API 以程式設計方式建立這些索引的結構描述。一旦定義結構描述之後，您就可以將資料上傳至 Azure 搜尋服務，接著編製索引。

您可以使用發送或提取模型，將資料上傳至索引。提取模型是透過可以針對需要或排定的更新而設定的索引子提供的 (請參閱[索引子作業 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn946891.aspx))，可讓您輕鬆地從 Azure DocumentDB、Azure SQL Database 或裝載於 Azure VM 的 SQL Server 擷取資料和資料變更。發送模式是透過用於將更新的文件傳送到索引的 SDK 或 REST API 提供的。只要資料具有 JSON 格式，您幾乎可以從任何資料集發送資料。如需載入資料的指引，請參閱[新增、更新或刪除文件](https://msdn.microsoft.com/library/azure/dn798930.aspx)或[如何使用 .NET SDK](search-howto-dotnet-sdk.md)。

有些開發人員將選擇索引子，原因是它提供的方便性。對於其他開發人員，發送模型值得一些額外的工作。選擇發送模式的原因有兩個。第一個，您可以避免編目程式樣式機制置於資料伺服器的額外負載。第二個，您可以避免排定的編製索引隨附的固有延遲。在網路星期一或黑色星期五，您想要搜尋以反映有關可用庫存的即時狀態。發送模型可以提供您該精確程度。

##您將建置和儲存的內容

一般工作流程是在本機開發環境中建置結構描述和文件，然後使用 .NET SDK 或 REST API 上傳、處理，最終查詢資料。所有已編製索引的資料會持續保存在 Azure 搜尋服務內，以取得更好的效能，並確保搜尋作業間的一致性。

您可以使用入口網站的內建編輯器，來建立索引結構描述和計分設定檔，這相當適合於建立原型。需要可重複之自動化方法的開發人員可能偏好在程式碼中建立索引。較新的功能會先加入至 API，因此，根據應用程式的需求，程式設計方式可能是您的唯一選項。

當建置結構描述時，您將定義搜尋應用程式中支援的欄位及其屬性。欄位包含可搜尋的資料，例如產品名稱、說明、客戶意見、品牌、價格、促銷通知等等。屬性會通知可執行的作業類型。更常用之屬性的範例包括欄位是否支援全文檢索搜尋 (searchable=true)、篩選條件 (filterable=true) 或 Facet (facetable=true)。

欄位也包含與您的搜尋應用程式相關的不可搜尋資料，例如篩選條件和計分設定檔內部使用的值，或也許是其他儲存體平台中儲存之內容的 URL (例如，保存在 BLOB 儲存體中的範例、影片或影像檔案)。僅供內部使用之欄位的經常引用範例為毛利率或另一個指出潛在營收的值。或許您的搜尋應用程式需要促銷為貴公司帶來更高財務利益的特定項目。您的結構描述可以包含允許這類搜尋行為的欄位屬性。

文件是搜尋結果中由搜尋引擎傳回的詳細資料。比方說，如果您的搜尋應用程式是線上目錄，則對於每一個 SKU，您將有一份文件，而且將使用從符合搜尋詞彙之文件傳回的值建置搜尋結果頁面。

##觀看實作示範

觀看我們的影片來了解案例和功能。如需影片內容的連結，請造訪 [Azure 搜尋：教學課程、影片示範和範例](search-video-demo-tutorial-list.md)。

##深入探討功能

Azure 搜尋服務會以多種類別提供值，包括佈建和級別、可程式性、存取和控制，以及您選擇要在自訂搜尋應用程式中實作的功能。

下列功能檢查清單可協助您評估 Azure 搜尋服務，以取得搜尋應用程式的需求。可在 Azure 搜尋服務的最新更新中找到新功能通知。您也可以檢閱 Azure 搜尋服務功能要求網頁，以檢查尚未實作之功能的狀態。

特定功能的相關問題？ 請嘗試 [MSDN 上的 Azure 搜尋服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)。您也可以檢閱 [Azure 搜尋服務功能要求網頁](http://feedback.azure.com/forums/263029-azure-search)，以檢查尚未實作之功能的狀態。

###容量和級別的功能和限制

服務可以做為標準或共用服務部署執行。標準搜尋支援可根據工作負載調整的專用資源。共用服務是免費的，而且僅供測試服務功能之用，因為沒有任何效能保證。

**可調整**，增加儲存體和文件計數 (資料分割) 或查詢負載 (複本)。每個複本會執行一份索引。高可用性需要 2 個複本用於查詢工作負載，以及 3 個複本用於讀寫 (查詢和編製索引) 工作負載。如需容量規劃的詳細資訊，請參閱限制和條件約束 (Azure 搜尋服務)。

Azure 搜尋服務會自動涵蓋您已對這項服務所配置之資料分割的索引和文件。這表示您無法將索引和文件釘成一組資料分割和複本。

資料分割和複本是整個服務資源，具有所有複本上執行的所有索引。如果您需要索引隔離，或者也許您有地理上將服務和資源散佈在不同資料中心的需求，則可以建立第二個服務。

對儲存體以及載入至服務的索引和文件數目有所限制。您的有效限制將為先到者：即將耗盡實體儲存體，或即將達到索引和文件計數的上限。如需詳細資訊，請參閱[限制和條件約束 (Azure 搜尋)](search-limits-quotas-capacity.md)。

###可程式性

REST API 由 HTTP 要求和回應組成，其內容以 JSON 格式表示。有一個用於存取服務的 API，以及一個用於管理服務的 API。如需詳細資訊，請參閱 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 和 [Azure 搜尋服務管理 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx) 。

.NET SDK 包含的類別可讓您輕鬆地使用 Azure 搜尋服務，而無需直接使用 HTTP 和 JSON。如需詳細資訊，請參閱[如何使用 Azure 搜尋服務 .NET SDK](search-howto-dotnet-sdk.md)。

###存取和控制

搜尋僅能透過 HTTPS 存取。

從主應用程式到 Azure 搜尋服務的驗證是透過 HTTP 標頭中的管理 APPI 金鑰進行。沒有任何根據使用者的驗證或授權模型。不過，您可以使用 $filter，依使用者身分識別來限制存取。您也可以使用多個可以指派給不同用戶端應用程式的查詢 API 金鑰。如需金鑰管理的詳細資訊，請參閱[在 Microsoft Azure 上管理搜尋服務](search-manage.md)。如需 $filter 的詳細資訊，請參閱[搜尋文件 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx)。

###索引和文件

您可以有多個索引 (如需根據定價層的限制，請參閱[限制和條件約束 (Azure 搜尋)](search-limits-quotas-capacity.md))。請注意，目前不支援聯結索引。搜尋要求可以指定一個索引。

**文件**包含欄位及相關聯的屬性。欄位包含可搜尋的文字、主要 (或甚至以獨佔方式) 用於篩選條件和計分設定檔的值，以及很可能是資料存放區中的 URL 或內容指標，例如影像。許多搜尋應用程式會使用多種形式的儲存體。影像或視訊可以更廉價地儲存在其他儲存媒體中，例如 Azure Blob 儲存體。

**索引子**可以用來排定時間，對 Azure SQL Database、Azure VM 上的 SQL Server 或 Azure DocumentDB 中已變更的資料進行索引更新。如需詳細資料，請參閱 [索引子作業 (Azure 搜尋服務 REST API)]((https://msdn.microsoft.com/library/azure/dn946891.aspx)。

可以對布林篩選條件使用 OData 語法，以及對全文檢索搜尋使用簡單查詢語法來提出**查詢**。如需詳細資訊，請參閱 [Azure 搜尋服務的 OData 運算式語法](https://msdn.microsoft.com/library/azure/dn798921.aspx)和 [Azure 搜尋服務中的簡單查詢語法](https://msdn.microsoft.com/library/azure/dn798920.aspx) 。

您可以根據文件識別碼來查閱特定文件，以快速擷取特定項目。當使用者按一下特定的搜尋結果，而且您想要查閱有關該文件的特定詳細資料時，這樣做很有用。如需詳細資料，請參閱[查閱文件 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798929.aspx)。

##搜尋應用程式功能

對於任何具有可搜尋屬性的欄位，都會啟用**全文檢索搜尋** (預設為根據文字欄位)。全文檢索搜尋根據前置詞比對，這表示相符項目根據搜尋詞彙的第一個部分，而不是字中間或字尾。如需如何定義索引的步驟，請參閱[建立索引 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx) 或[在入口網站中建立索引](search-create-index-portal.md)。

**計分設定檔**用來建置根據商務目標將搜尋最佳化的排名模型。例如，您可能想要新推出的產品或折扣的產品，出現在搜尋結果中較高的位置。您也可以根據您所追蹤並個別儲存的客戶搜尋喜好設定，使用標記進行個人化計分來建置計分設定檔。如需詳細資料，請參閱[將評分設定檔新增至搜尋索引 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798928.aspx)。

使用自然語言處理堆疊的多個選項，內建五十個不同語言的**語言支援**，包括知名的 Lucene 分析器和提供 Microsoft Office 和 Bing 動力的 Microsoft 專屬分析器 (僅限預覽) 。請參閱[語言支援 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn879793.aspx) (若為 Lucene) 和 [Azure 搜尋服務 REST API 版本 2015-02-28 預覽](search-api-2015-02-28-Preview.md) (若為自然語言處理器)。

**多面向導覽**指的是用於自我引導搜尋的分類樹狀結構，通常根據品牌、模型、大小，或任何對您資料有意義的類別。多面向導覽是透過索引中的屬性來實作，並結合了查詢中提供的 Facet 欄位。如需詳細資料，請參閱[多面向導覽](search-faceted-navigation.md)。

自動提示或自動完成查詢的**建議**是透過索引中的屬性來支援。Azure 搜尋服務同時支援模糊比對和字間比對 (比對欄位內容的任何部分)。您可以同時執行兩者；它們不會互斥。請參閱[建立索引 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx)，以取得啟用建議的相關資訊，以及參閱[建議 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798936.aspx)，以取得如何使用它們。

**篩選**可以用來實作多面向導覽、用於查詢建構，或在全域用來將搜尋作業限制為篩選您在程式碼中建立的準則。特定欄位的篩選是透過索引結構描述，以及透過 $Filter 搜尋參數來啟用。如需詳細資訊，請參閱[建立索引 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx) 和[搜尋文件 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx)。

特定欄位的**排序**也是透過索引結構描述來啟用，然後透過 $orderBy 參數做為搜尋參數來實作。如需詳細資訊，請再次參閱[建立索引 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx) 和[搜尋文件 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx)。

針對查詢傳回之搜尋點擊的**計數**，以及節流一次傳回多少個點擊的能力，是透過 $top 和 $skip 來實作。如需詳細資訊，請參閱[搜尋文件 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx)。

**反白顯示的點擊**是透過反白顯示查詢參數來指定，並可讓您顯示一段文字給使用者，其中反白顯示從使用者輸入的搜尋詞彙找到的關鍵字。如需查詢參數的詳細資訊，請參閱[搜尋文件 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx)。

##報告和分析

資源使用情況會顯示在服務儀表板上，如此就能快速了解如何使用您的儲存體。

儲存體耗用量、文件計數和索引計數都可在入口網站中取得。您也可以使用 API。如需詳細資訊，請參閱[取得索引統計資料 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798942.aspx)。

沒有任何內建的機制，用於測量查詢輸送量或其他服務作業。此外，目前不支援記錄或分析搜尋結果 (例如，擷取未產生任何結果之搜尋詞彙的清單，或報告搜尋要求的來源)。

##立即試用

請造訪[建立 Azure 搜尋服務](search-create-service-portal.md)來設定服務，或使用[試用 Azure App Service](http://go.microsoft.com/fwlink/p/?LinkId=618214)，以不需設定或訂用帳戶的免費一小時工作階段。

您也可以嘗試下列教學課程：

[如何在 .NET 中使用 Azure 搜尋](search-howto-dotnet-sdk.md) [開始使用 Azure 搜尋 .NET](search-get-started-dotnet.md) [Azure 搜尋：教學課程、影片示範和範例](search-video-demo-tutorial-list.md)

<!---HONumber=Sept15_HO2-->