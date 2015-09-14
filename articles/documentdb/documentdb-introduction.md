<properties 
	pageTitle="Microsoft Azure DocumentDB 簡介 - 免費試用 | Microsoft Azure"
	description="了解 Azure DocumentDB (NoSQL 文件資料庫) 及其對於雲端和行動應用程式的價值。同時也了解它如何管理資料及如何用於應用程式開發。"
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
	ms.date="09/01/2015"
	ms.author="mimig"/>

# Microsoft Azure DocumentDB 簡介

本文針對開發人員、IT 專業人員和業務決策者，提供 Microsoft Azure DocumentDB (完整管理的 NoSQL 文件資料庫服務) 簡介。

若要快速了解 DocumentDB 與觀看其運作方式，請遵循這三個步驟：

1. 請觀看兩分鐘的[什麼是 DocumentDB？](http://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/)影片，當中會介紹使用 DocumentDB 的優點。
2. 請觀看三分鐘的[在 Azure 上建立 DocumentDB](http://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) 影片，當中會強調如何透過 Azure Preview 入口網站開始使用 DocumentDB。
3. 請瀏覽[查詢遊樂場](http://www.documentdb.com/sql/demo)，您可以在其中逐步執行不同的活動，以了解 DocumentDB 中可用的豐富查詢功能。接著，請前往 [沙箱] 索引標籤，執行您自訂的 SQL 查詢並實驗 DocumentDB。

然後，回到這篇文章，我們將進行深入探討，讓您在此找到下列問題的答案：

-	[什麼是 DocumentDB，它為雲端和行動應用程式提供什麼價值？](#what-is-azure-documentdb)
-	[在 DocumentDB 中是如何管理資料，以及要如何存取它？](#data-management)
-	[如何使用 DocumentDB 來開發應用程式？](#develop)
-	[建置 DocumentDB 應用程式的後續步驟是什麼？](#next-steps)  

## 什麼是 Azure DocumentDB？  

新式應用程式會快速地產生、使用和回應非常大量的資料。不僅這些應用程式發展地極為快速，基礎資料結構描述也是如此。因此，開發人員越來越常選擇無結構描述的 NoSQL 資料庫，做為簡單、快速、可調整的方案來儲存和處理資料，同時還保有快速逐一查看應用程式資料模型和非結構化資料摘要的能力。不過，許多無結構描述資料庫都不允許複雜的查詢和交易式處理，以致於很難進行進階資料管理。這就是 DocumentDB 的切入點。Microsoft 開發了 DocumentDB，以滿足管理現今應用程式資料的這些需求。

DocumentDB 是確實針對新式行動和 Web 應用程式設計的無結構描述 NoSQL 文件資料庫服務。DocumentDB 提供一樣快速的讀取和寫入、結構描述彈性，以及依需求輕鬆擴大和縮小資料庫的能力。它不會假設或不需要 JSON 文件的任何結構描述。根據預設，它會自動為資料庫中的所有文件編制索引，且不預期或需要任何結構描述或建立次要索引。DocumentDB 使用 SQL 語言來提供複雜的特定查詢、支援定義完善的一致性層級，以及使用預存程序、觸發程序和 UDF 的常見程式設計模型提供 JavaScript 語言整合式多文件交易處理。

DocumentDB 原生支援 JSON 文件，能夠輕鬆地反覆運算應用程式的結構描述。它採用廣為使用的 JSON 和 JavaScript，以消除應用程式定義物件與資料庫結構描述之間的不相符。JavaScript 的深入整合也讓開發人員能夠在資料庫交易中，於資料庫引擎內既直接又有效率地執行應用程式邏輯。

Azure DocumentDB 提供下列重要功能和優點：

-	**運用常見的 SQL 語法進行特定查詢：**在 DocumentDB 內儲存異質 JSON 文件，並透過常見的 SQL 語法來查詢這些文件。DocumentDB 使用高度並行、無鎖定、記錄結構化索引技術，來自動編製所有文件內容的索引。這樣一來，不需要指定結構描述提示、次要索引或檢視，就能進行大量且即時的查詢。在[查詢 DocumentDB](documentdb-sql-query.md) 中深入了解。 

-	**在資料庫內執行 JavaScript：**使用標準 JavaScript，以預存程序、觸發程序和使用者定義函數 (UDF) 的形式表示應用程式邏輯。這允許您的應用程式邏輯操作 JSON 資料，而不需要擔心應用程式與資料庫結構描述之間的不相符。DocumentDB 可讓您直接在資料庫引擎內以完整的交易方式執行 JavaScript 應用程式邏輯。JavaScript 的深入整合則可讓您以獨立交易的形式，從 JavaScript 程式內執行 INSERT、REPLACE、DELETE 和 SELECT 作業。在[DocumentDB 伺服器端程式設計](documentdb-programming.md)中深入了解。

-	**可調整的一致性層級：**有四個定義良好的一致性層級可供選擇，以讓您在一致性與效能兩者間做出最好的取捨。針對查詢和讀取作業，DocumentDB 提供四個不同的一致性層級：「增強式」、「界限-陳舊」、「工作階段」和「最終」。這些細微且定義完善的一致性層級可讓您在一致性、可用性與延遲三者間做出合理取捨。在[使用一致性層級以最大化 DocumentDB 中的可用性和效能](documentdb-consistency-levels.md)中深入了解。

-	**完全受管理：**不需要管理資料庫和電腦資源。做為受到完整管理的 Microsoft Azure 服務，您不需要管理虛擬機器、部署和設定軟體，或處理複雜的資料層升級。每個資料庫都會自動進行備份，防範區域性失敗。您可以輕鬆地新增 DocumentDB 帳戶，並在需要時佈建容量，因此能夠將所有精神放在應用程式，而非操作與管理資料庫。

-	**可彈性調整的輸送量和儲存體：**輕鬆相應增加或相應減少 DocumentDB 以符合應用程式需求。透過增減預留的 SSD 備用儲存體和輸送量的基本單位 (集合)，即可調整大小。隨著應用程式的成長，您可以建立更多單位，依據可預測的效能彈性調整 DocumentDB。

-	**開放式設計：**使用現有技能和工具讓您快速上手。針對 DocumentDB 進行程式設計十分簡單、容易達成，而且不需要採用新工具或符合 JSON 或 JavaScript 的自訂擴充功能。您可以透過簡單的 RESTful HTTP 介面來存取所有資料庫功能，包括 CRUD、查詢和 JavaScript 處理。DocumentDB 既採用現有的格式、語言和標準，同時又能提供凌駕於它們之上的高價值資料庫功能。

您可以使用 DocumentDB 來儲存需要查詢抓取和交易式處理的彈性資料集。應用程式案例可能包括互動式 Web 和行動應用程式的使用者資料，以及儲存、抓取和處理應用程式 JSON 資料。資料庫可以儲存任意數目的 JSON 文件，因為 DocumentDB 相當適用於在網際網路上依規模執行的應用程式。

##<a name="data-management"></a>Azure DocumentDB 資源
Azure DocumentDB 透過定義完善的資料庫資源管理資料。這些資源會進行複寫來達到高可用性，並且會透過其邏輯 URI 來進行唯一定址。DocumentDB 針對所有資源提供簡單的 HTTP 式 RESTful 程式設計模型。

DocumentDB 資料庫帳戶是可讓您存取 Azure DocumentDB 的唯一命名空間。在建立資料庫帳戶之前，您必須先擁有 Azure 訂用帳戶，此帳戶可讓您存取各種 Azure 服務。

DocumentDB 內的所有資源都會被建立模型，並儲存為 JSON 文件。管理資源時，是以項目 (含有中繼資料的 JSON 文件) 和摘要 (即項目集合) 的形式來管理。項目集包含在其各自的摘要內。

下圖顯示 DocumentDB 資源之間的關係：

![][1]

資料庫帳戶是由一組資料庫所組成，每個資料庫都包含多個集合，而集合可包含預存程序、觸發程序、UDF、文件和相關附件。資料庫也有相關聯的使用者，每個使用者都有一組可存取其他各種集合、預存程序、觸發程序、UDF、文件或附件的權限。資料庫、使用者、權限和集合是系統所定義、具有已知結構描述的資源，而文件、預存程序、觸發程序、UDF 和附件則包含使用者定義的任意 JSON 內容。

##<a name="develop"></a>使用 Azure DocumentDB 進行開發
Azure DocumentDB 公開資源的方式是透過 REST API，此 API 可供任何能發出 HTTP/HTTPS 要求的語言呼叫。另外，DocumentDB 還提供了幾種熱門語言的程式設計程式庫。這些程式庫可透過處理詳細資料 (例如位址快取、例外狀況管理、自動重試等) 來簡化使用 Azure DocumentDB 的各個層面。程式庫目前適用於下列語言和平台：

下載 | 文件
--- | ---
[.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) | [.NET 程式庫](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) | [Node.js 程式庫](http://dl.windowsazure.com/documentDB/nodedocs/)
[Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) | [Java 程式庫](http://dl.windowsazure.com/documentdb/javadoc/)
[JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) | [JavaScript 程式庫](http://dl.windowsazure.com/documentDB/jsclientdocs/)
n/a | [伺服器端 JavaScript SDK](http://dl.windowsazure.com/documentDB/jsserverdocs/)
[Python SDK](https://pypi.python.org/pypi/pydocumentdb) | [Python 程式庫](http://dl.windowsazure.com/documentDB/pythondocs/)

除了基本的建立、讀取、更新和刪除作業之外，DocumentDB 還提供可用來擷取 JSON 文件的豐富 SQL 查詢介面，並在伺服器端支援以交易方式執行 JavaScript 應用程式邏輯。查詢和指令碼執行介面可以透過所有平台程式庫以及 REST API 來取得。

### SQL 查詢
Azure DocumentDB 支援使用根植於 JavaScript 類型系統的 SQL 語言及支援豐富階層式查詢的運算式來查詢文件。DocumentDB 查詢語言是可用來查詢 JSON 文件的簡單卻功能強大的介面。此語言支援 ANSI SQL 文法的子集，並新增 JavaScript 物件、陣列、物件建構和函式叫用的深入整合。DocumentDB 提供其查詢模型，而沒有來自開發人員的任何明確結構描述或編製索引提示。

使用者定義函式 (UDF) 可以向 DocumentDB 進行註冊，然後在 SQL 查詢中供參考，進而延伸文法來支援自訂應用程式邏輯。這些 UDF 是以 JavaScript 程式的形式撰寫，並在資料庫內執行。

若為 .NET 開發人員，DocumentDB 也提供 LINQ 查詢提供者做為 [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx) 的一部分。

### 交易和 JavaScript 執行
DocumentDB 可讓您將應用程式邏輯撰寫成完全以 JavaScript 撰寫的具名程式。這些程式會針對集合進行註冊，而可對指定之集合內的文件進行資料庫操作。JavaScript 可以註冊成觸發程序、預存程序或使用者定義函式 (UDF) 來供執行。觸發程序和預存程序可以建立、讀取、更新和刪除文件，而使用者定義函式則可在查詢執行邏輯中執行，而不需要對集合進行寫入存取。

在 DocumentDB 內執行 JavaScript 的作法是仿造自關聯式資料庫系統所支援的概念，以 JavaScript 做為 Transact-SQL 的新式取代項目。所有 JavaScript 邏輯都是以隔離的快照在環境 ACID 交易內執行。在執行期間，如果 JavaScript 擲回例外狀況，則會中止整個交易。

## 後續步驟
如果您已經有 Azure 帳戶，可以透過[建立 DocumentDB 資料庫帳戶](documentdb-create-account.md)，開始使用 [Azure Preview 入口網站](https://portal.azure.com/#gallery/Microsoft.DocumentDB)中的 DocumentDB。

若您沒有 Azure 帳戶，您可以：

- 註冊 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)，得到 30 天免費試用以及美金 200 元，讓您試用所有 Azure 服務。 
- 如果您有 MSDN 訂用帳戶，就可以享有[免費的 Azure 信用額度每月美金 150 元](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，可用於所有 Azure 服務。 

然後，當您準備好深入了解時，請造訪我們的[學習途徑](http://azure.microsoft.com/documentation/learning-paths/documentdb/)，以巡覽所有您適用的學習資源。


[1]: ./media/documentdb-introduction/resources1.png
 

<!---HONumber=September15_HO1-->