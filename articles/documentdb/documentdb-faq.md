<properties 
	pageTitle="DocumentDB 資料庫問題 - 常見問題集 | Microsoft Azure" 
	description="取得 Azure DocumentDB (適用於 JSON 之 NoSQL 文件資料庫服務) 的常見問題的解答。解答產能、效能層級和調整的相關資料庫問題。" 
	keywords="資料庫問題, 常見問題集, Database questions, frequently asked questions, documentdb, azure, Microsoft azure"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="mimig"/>


#DocumentDB 常見問題集

## Microsoft Azure DocumentDB 基本概念的相關資料庫問題

### 什麼是 Microsoft Azure DocumentDB？ 
Microsoft Azure DocumentDB 是一種極具調整性的 NoSQL 文件「資料庫即服務」，可在無結構描述的資料上進行豐富的查詢，協助提供可設定和可靠的效能，且支援快速開發，這些完全都是透過受管理的平台，背後有 Microsoft Azure 強大的功能與先進的技術做後盾。如果關鍵要求在於可預測的輸送量、低遲性和無結構描述的資料模型，則 DocumentDB 就是 Web、行動、遊戲和 IoT 應用程式最適當的解決方案。DocumentDB 透過原生 JSON 資料模型，提供結構描述的彈性和豐富的檢索能力，且包含多文件交易式支援與整合式 JavaScript。
  
如需更多資料庫問題、解答，以及部署和使用此服務的指示，請參閱 [DocumentDB 文件頁面](https://azure.microsoft.com/documentation/services/documentdb/)。

### DocumentDB 是何種資料庫？
DocumentDB 是 NoSQL 文件導向的資料庫，以 JSON 格式儲存資料。DocumentDB 支援巢狀、獨立式資料結構，可透過豐富的 DocumentDB [SQL 查詢文法](documentdb-sql-query.md)來查詢。DocumentDB 透過[預存程序、觸發程序和使用者定義函數](documentdb-programming.md)，提供伺服器端 JavaScript 高效能的交易式處理。資料庫也支援可由開發人員調整的一致性層級與相關聯的[效能層級](documentdb-performance-levels.md)。
 
### DocumentDB 資料庫有類似關聯式資料庫 (RDBMS) 的資料表嗎？
沒有，DocumentDB 將資料儲存在 JSON 文件集合中。如需 DocumentDB 資源的詳細資訊，請參閱 [DocumentDB 資源模型和概念](documentdb-resources.md)。

### DocumentDB 資料庫支援無結構描述的資料嗎？
是，DocumentDB 可讓應用程式儲存任意的 JSON 文件，而不需要結構描述定義或提示。透過 DocumentDB SQL 查詢介面就可立即查詢資料。

### DocumentDB 支援 ACID 交易嗎？
是，DocumentDB 支援以 JavaScript 預存程序和觸發程序表達的跨文件交易。交易以每個集合的單一分割為範圍，且以 ACID 語意執行，也就是全有或全無，與其他並行執行的程式碼和使用者要求隔離。如果伺服器端執行 JavaScript 應用程式碼期間擲回例外狀況，則會回復整個交易。

### DocumentDB 有哪些一般使用案例？  
對於新的 Web、行動、遊戲和 IoT 應用程式，其中規模、可預測的效能、毫秒回應時間的快速排序和查詢無結構描述的資料都很重要時，DocumentDB 是不錯的選擇。DocumentDB 有助於快速開發，且支援應用程式資料模型的持續反覆運算。管理使用者產生之內容和資料的應用程式，就是 [DocumentDB 的常見使用案例](documentdb-use-cases.md)。

### DocumentDB 如何提供可預測的效能？
要求單位 (RU) 是 DocumentDB 中輸送量的計量。1 RU 會對應至 1KB 文件的 GET 的輸送量。在 DocumentDB 中的每個作業，包括讀取、寫入、SQL 查詢和預存程序執行具有決定性的要求單位值，根據完成作業所需的輸送量。而不是思考 CPU、IO 和記憶體以及它們分別如何影響您的應用程式輸送量，您可以單一要求單位計量來思考。

每個 DocumentDB 集合可以保留每秒輸送量要求單位的已佈建輸送量。對於任何規模的應用程式，您可以將個別要求設為基準以測量其要求單位值，以及將佈建集合設為基準以處理所有要求的要求單位的總和。您也可以隨著應用程式發展需求，相應增加或減少集合的輸送量。如需要求單位的詳細資訊以及協助確定您的集合需求，請參閱[管理效能和容量](documentdb-manage.md)。

### DocumentDB 是否符合 HIPAA 標準？
是，DocumentDB 符合 HIPAA 標準。HIPAA 建立了使用、洩漏與保護個別辨識健全狀況資訊的需求。如需詳細資訊，請參閱 [Microsoft Trust Center (Microsoft 信任中心)](https://www.microsoft.com/zh-TW/TrustCenter/Compliance/HIPAA)。

### DocumentDB 的儲存體限制有哪些？ 
對於集合可以儲存在 DocumentDB 中的資料總量，理論上沒有任何限制。如果您想要在單一集合中儲存超過 250 GB 的資料，請[連絡支援服務](documentdb-increase-limits.md)以增加您的帳戶配額。

### DocumentDB 的輸送量限制有哪些？ 
如果您的工作負載可以大致平均分配給夠大的分割索引鍵數目，則在 DocumentDB 中集合可以支援的輸送量總量理論上沒有限制。如果您希望在單一集合或帳戶中每秒超過 250000 個要求單位，請[連絡支援服務](documentdb-increase-limits.md)以增加您的帳戶配額。

### Microsoft Azure DocumentDB 的費用是多少？
請參閱 [DocumentDB 價格詳細資料](https://azure.microsoft.com/pricing/details/documentdb/)頁面，以取得詳細資料。DocumentDB 使用量費用取決於使用中的集合數目、集合已在線上的時數，以及每個集合的已使用儲存體和已佈建輸送量。

### 有免費的帳戶嗎？
如果您不熟悉 Azure，可以註冊 [Azure 免費帳戶](https://azure.microsoft.com/pricing/free-trial/)，就能得到 30 天免費試用以及美金 200 元，讓您試用所有 Azure 服務。或者，如果您有 Visual Studio 訂用帳戶，就可以享有[免費的 Azure 信用額度每月美金 150 元](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，可用於所有 Azure 服務。

### 如何取得 DocumentDB 的其他說明？
如果您需要協助，請在 [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb) 和 [Azure DocumentDB MSDN 開發人員論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB)聯繫我們，或者是和 [DocumentDB 工程小組安排一對一的談話](http://www.askdocdb.com/)。若想隨時知道最新的 DocumentDB 新聞和功能，請追蹤我們的 [Twitter](https://twitter.com/DocumentDB)。

## 設定 Microsoft Azure DocumentDB

### 我如何註冊 Microsoft Azure DocumentDB？
[Azure 入口網站][azure-portal]中已提供 Microsoft Azure DocumentDB。首先，您必須註冊 Microsoft Azure 訂用帳戶。註冊 Microsoft Azure 訂用帳戶之後，您可以將 DocumentDB 帳戶加入 Azure 訂用帳戶。如需加入 DocumentDB 帳戶的指示，請參閱[建立 DocumentDB 資料庫帳戶](documentdb-create-account.md)。

### 什麼是主要金鑰？
主要金鑰是可存取帳戶的所有資源的安全性權杖。擁有此金鑰的人，對資料庫帳戶中的所有資源具有讀取和寫入存取權。分配主要金鑰時，務必謹慎。[Azure 入口網站][azure-portal]的 **[金鑰]** 刀鋒視窗提供主要金鑰和次要金鑰。如需金鑰的詳細資訊，請參閱[檢視、複製和重新產生存取金鑰](documentdb-manage-account.md#keys)。

### 我如何建立資料庫？
您可以如[建立 DocumentDB 資料庫](documentdb-create-database.md)所述使用 [Azure 入口網站]()、利用其中一套 [DocumentDB SDK](documentdb-sdk-dotnet.md) 或透過 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 來建立資料庫。

### 什麼是集合？
集合是 JSON 文件和相關聯 JavaScript 應用程式邏輯的容器。集合是計費實體，其中的[成本](documentdb-performance-levels.md)是由與集合相關聯的效能層級所決定。集合可以跨越一或多個分割/伺服器，也可以進行調整以處理幾乎無限量的儲存體或輸送量。

集合也是 DocumentDB 的帳務實體。每個集合根據佈建的輸送量和使用的儲存體空間，以小時為單位計費。如需詳細資訊，請參閱 [DocumentDB 定價](https://azure.microsoft.com/pricing/details/documentdb/)。

### 我如何設定使用者和權限？
您可以使用其中一套 [DocumentDB SDK](documentdb-sdk-dotnet.md) 或透過 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 來建立使用者和權限。

## 針對 Microsoft Azure DocumentDB 進行開發的相關資料庫問題

### 如何開始針對 DocumentDB 進行開發？
[SDK](documentdb-sdk-dotnet.md) 適用於 .NET、Python、Node.js、JavaScript 和 Java。開發人員也可以利用 [RESTful HTTP API](https://msdn.microsoft.com/library/azure/dn781481.aspx)，從各種平台和語言來與 DocumentDB 資源互動。

GitHub 上提供 DocumentDB [.NET](documentdb-dotnet-samples.md)、[Java](https://github.com/Azure/azure-documentdb-java)、[Node.js](documentdb-nodejs-samples.md) 和 [Python](documentdb-python-samples.md) 的 SDK。

### DocumentDB 支援 SQL 嗎？
DocumentDB SQL 查詢語言是 SQL 支援的查詢功能增強子集。DocumentDB 的 SQL 查詢語言提供豐富的階層式和關聯式運算子，且透過 JavaScript 型使用者定義函數 (UDF) 支援擴充性。JSON 文法允許將 JSON 文件模型化為樹狀目錄，並以標籤當作樹狀節點 - 這是由 DocumentDB 的自動編製索引技術及 DocumentDB 的 SQL 查詢方言所使用。如需如何使用 SQL 文法的詳細資訊，請參閱[查詢 DocumentDB][query] 一文。

### DocumentDB 支援什麼資料類型？
DocumentDB 支援的基本資料類型與 JSON 相同。JSON 有一套簡單的類型系統，包含字串、數值 (IEEE754 雙精度)、布林值 - true、false 及 Null。透過使用 { } 運算子建立巢狀物件和使用 [ ] 運算子建立陣列，可以在 JSON 和 DocumentDB 中表示更複雜的資料類型 (例如 DateTime、Guid、Int64 和 Geometry)。

### DocumentDB 如何提供並行存取？
DocumentDB 透過 HTTP 實體標記或 ETag，支援開放式並行存取控制 (OCC)。每一個 DocumentDB 資源都有一個 ETag，DocumentDB 用戶端會將最新讀取的版本包含在寫入要求中。如果 ETag 是最新，表示已認可變更。如果值已從外部變更，則伺服器會拒絕寫入，並傳回「HTTP 412 預先指定的條件失敗」回應碼。用戶端必須讀取最新版的資源，再重試要求。

### 我如何在 DocumentDB 中執行交易？
DocumentDB 透過 JavaScript 預存程序和觸發程序，支援語言整合式交易。如果是單一分割集合，則指令碼內的所有資料庫作業會在集合範圍內的快照隔離底下執行，如果集合已分割，則在集合中具有相同分割索引鍵值的文件底下執行。文件版本 (ETag) 的快照是在交易開始時取得，且只有當指令碼成功執行時才會認可。如果 JavaScript 擲回錯誤，則會回復交易。如需詳細資訊，請參閱 [DocumentDB 伺服器端程式設計](documentdb-programming.md)。

### 我如何將大量文件插入至 DocumentDB？ 
有三種方式可將文件大量插入 DocumentDB：

- 使用資料移轉工具，如[將資料匯入 DocumentDB](documentdb-import-data.md) 所述。
- 使用 Azure 入口網站的文件總管，如[使用文件總管大量加入文件](documentdb-view-json-document-explorer.md#BulkAdd)所述。
- 使用預存程序，如 [DocumentDB 伺服器端程式設計](documentdb-programming.md)所述。

### DocumentDB 支援資源連結快取嗎？
是，因為 DocumentDB 是 RESTful 服務，資源連結是固定不變且可快取。DocumentDB 用戶端可以指定 "If-None-Match" 標頭來讀取任何資源，例如文件或集合，且只有當伺服器上的版本變更時才會更新本機複本。




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 

<!---HONumber=AcomDC_0330_2016-->