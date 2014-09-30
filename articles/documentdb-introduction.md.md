<properties title="Introduction to Microsoft Azure DocumentDB" pageTitle="Introduction to Microsoft Azure DocumentDB | Azure" description="Learn about Azure DocumentDB and its value to cloud and mobile applications. Also, learn about how it manages data, and how you can use it in application development." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" " manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

# Microsoft Azure DocumentDB 簡介

本文針對開發人員、IT 專業人員和業務決策者，提供 Microsoft Azure DocumentDB 簡介。在閱讀本文後，您將了解：

-	什麼是 Azure DocumentDB，以及它帶給雲端和行動應用程式的價值
-	Azure DocumentDB 如何管理您的資料
-	如何使用 Azure DocumentDB 存取資料以及開發應用程式
-	若要打造第一個 Azure DocumentDB 應用程式下一步該怎麼做

## 什麼是 Azure DocumentDB？

新式應用程式會快速地產生、使用和回應非常大量的資料。不僅這些應用程式發展地極為快速，基礎資料結構描述也是如此。因此，開發人員越來越常選擇無結構描述的 NoSQL 資料庫做為簡單、快速、彈性的方案來儲存和處理資料，同時保有快速逐一查看應用程式資料模型和非結構化資料摘要的能力。不過，許多無結構描述資料庫都不允許非一般的查詢和交易式處理，因此您很難進行進階資料管理。所以，Microsoft 開發了 Azure DocumentDB，以在管理無結構描述資料時提供這些功能。

Microsoft Azure DocumentDB 是針對新式行動和 Web 應用程式設計的文件導向 NoSQL 資料庫服務。DocumentDB 提供一樣快速的讀取和寫入、結構描述彈性，以及依需求輕鬆擴大和縮小資料庫的能力。DocumentDB 使用 SQL 方言來提供複雜的特定查詢、支援定義完善的一致性層級，以及使用預存程序、觸發程序和 UDF 的常見程式設計模型提供 JavaScript 語言整合式多文件交易處理。

Azure DocumentDB 原生支援 JSON 文件，能夠輕鬆反覆運算應用程式的結構描述。它採用廣為使用的 JSON 和 JavaScript，以免應用程式類型系統與資料庫結構描述不符。JavaScript 的深入整合也讓開發人員能夠在資料庫交易的資料庫引擎內直接且有效率地執行應用程式邏輯。

Azure DocumentDB 提供下列重要功能和優點：
-	**運用常見的 SQL 語法進行特定查詢：**在 DocumentDB 內儲存異質 JSON 文件，並透過常見的 SQL 語法來查詢這些文件。DocumentDB 使用高度並行、無鎖定、記錄結構化索引技術，來自動編製所有文件內容的索引。這樣一來，不需要指定結構描述提示、次要索引或檢視，就能進行大量且即時的查詢。

-	**在資料庫內執行 JavaScript：**使用標準 JavaScript，以預存程序、觸發程序和使用者定義函數 (UDF) 的形式表示應用程式邏輯。這允許您的應用程式邏輯操作 JSON 資料，而不需要擔心應用程式與資料庫結構描述之間的阻抗不相符。DocumentDB 可讓您直接在資料庫引擎內以完整的交易方式執行 JavaScript 應用程式邏輯。JavaScript 的深入整合則可讓您以獨立交易的形式，從 JavaScript 程式內執行 INSERT、REPLACE、DELETE 和 SELECT 作業。

-	**可調整的一致性層級：**有四個定義良好的一致性層級可供選擇，以讓您在一致性與效能兩者間做出最好的取捨。針對查詢和讀取作業，DocumentDB 提供四個不同的一致性層級：「增強式」、「界限-陳舊」、「工作階段」和「最終」。這些細微且定義完善的一致性層級可讓您在一致性、可用性與延遲三者間做出合理取捨。

-	**受到完整管理：**不需要管理資料庫和電腦資源。做為受到完整管理的 Microsoft Azure 服務，您不需要管理虛擬機器、部署和設定軟體，或處理複雜的資料層升級。每個資料庫都會自動進行備份，防範區域性失敗。您可以輕鬆地新增 DocumentDB 帳戶，並在需要時佈建容量，因此能夠將所有精神放在應用程式，而非操作與管理資料庫。

-	**可彈性調整的輸送量和儲存體：**輕鬆擴大或縮小 DocumentDB 以符合應用程式的需要。透過增減預留的 SSD 備用儲存體和輸送量的基本單位，即可調整大小。隨著應用程式的成長，您可以購買更多容量單位，依據可預測的效能彈性調整 DocumentDB。

-	**開放式設計：**使用現有技能和工具讓您快速上手。對 DocumentDB 進行程式設計十分簡單、容易達成，而且不需要採用新工具或符合 JSON 或 JavaScript 的自訂擴充功能。您可以透過簡單的 RESTful HTTP 介面來存取所有資料庫功能，包括 CRUD、查詢和 JavaScript 處理。DocumentDB 採用現有格式、語言和標準，同時提供凌駕於它們之上的高價值資料庫功能。

您可以使用 Azure DocumentDB，來儲存需要查詢擷取和交易式處理的彈性資料集。應用程式案例可能包括互動式 Web 和行動應用程式的使用者資料，以及儲存、擷取和處理應用程式 JSON 資料。資料庫可以儲存任意數目的 JSON 文件，因此，DocumentDB 相當適合以網際網路規模執行的應用程式。

## Azure DocumentDB 資源
Azure DocumentDB 透過定義完善的資料庫資源管理資料。這些資源會進行複寫以便達到高可用性，並且可透過其邏輯 URI 進行唯一定址。DocumentDB 針對所有資源提供簡單的 HTTP 式 RESTful 程式設計模型。

DocumentDB 資料庫帳戶是可讓您存取 Azure DocumentDB 的唯一命名空間。在建立資料庫帳戶之前，您必須擁有 Azure 訂用帳戶，此訂用帳戶可讓您存取各種 Azure 服務的方案。

會建立 Azure DocumentDB 內所有資源的模型，並將其儲存為 JSON 文件。資源是以項目 (含有中繼資料的 JSON 文件) 和摘要 (即項目集合) 的形式進行管理。項目集包含在其各自的摘要內。

下圖顯示 Azure DocumentDB 資源彼此間的關係：

![](./media/documentdb-introduction/intro.png) 

資料庫帳戶是由一組資料庫所組成，每個資料庫都包含多個集合，而集合可包含預存程序、觸發程序、UDF、文件和相關附件。資料庫也有相關聯的使用者，每個使用者都有一組可存取其他各種集合、預存程序、觸發程序、UDF、文件或附件的權限。資料庫、使用者、權限和集合是具有已知結構描述而由系統定義的資源，文件、預存程序、觸發程序、UDF 和附件則包含使用者定義的任意 JSON 內容。

## 對 Azure DocumentDB 進行開發
Azure DocumentDB 透過 REST API 公開資源，任何可提出 HTTP/HTTPS 要求的語言都可以呼叫 REST API。另外，Azure DocumentDB 還提供了幾種熱門語言的程式設計程式庫。這些程式庫可透過處理詳細資料 (例如位址快取、例外狀況管理、自動重試等) 來簡化使用 Azure DocumentDB 的各個層面。程式庫目前適用於下列語言和平台，以及正在研發的其他語言和平台：

-	[.NET (英文)](http://go.microsoft.com/fwlink/?LinkID=402989)
-	[Node.js (英文)](http://go.microsoft.com/fwlink/?LinkID=402990) 
-	[JavaScript (英文)](http://go.microsoft.com/fwlink/?LinkID=402991)
-	[Python (英文)](http://go.microsoft.com/fwlink/?LinkID=402992)

除了基本的「建立」、「讀取」、「更新」和「刪除」作業之外，Azure DocumentDB 還提供可用來擷取 JSON 文件的豐富 SQL 查詢介面，並在伺服器端支援以交易方式執行 JavaScript 應用程式邏輯。查詢和指令碼執行介面可以透過所有平台程式庫以及 REST API 來取得。

### SQL 查詢
Azure DocumentDB 支援使用根植於 JavaScript 類型系統的 SQL 語言以及支援豐富階層式查詢的運算式來查詢文件。DocumentDB 查詢語言是可用來查詢 JSON 文件的簡單卻功能強大的介面。此語言支援 ANSI SQL 文法的子集，並新增 JavaScript 物件、陣列、物件建構和函數叫用的深入整合。DocumentDB 提供其查詢模型，而沒有來自開發人員的任何明確結構描述或編製索引提示。

使用者定義函數 (UDF) 可以向 Azure DocumentDB 註冊，並在 SQL 查詢中進行參考，進而擴充文法來支援自訂應用程式邏輯。這些 UDF 是以 JavaScript 程式的形式撰寫，並在資料庫內執行。

針對 .NET 開發人員，Azure DocumentDB 也提供 LINQ 查詢提供者做為 .NET SDK 的一部分。

### 交易和 JavaScript 執行
Azure DocumentDB 可讓您將應用程式邏輯撰寫為完全以 JavaScript 撰寫的具名程式。這些程式會針對集合註冊，並支援對給定集合內的文件發出資料庫作業。應用程式 JavaScript 可以註冊以「觸發程序」、「預存程序」或「使用者定義函數 (UDF)」的形式執行。「觸發程序」和「預存程序」可以建立、讀取、更新和刪除文件，而 UDF 會在執行查詢的過程中執行，而不需要對集合進行寫入存取。

在 DocumentDB 內執行 JavaScript 的作法是仿造自關聯式資料庫系統所支援的概念，以 JavaScript 做為 T-SQL 的新式取代項目。所有 JavaScript 邏輯都是以隔離的快照在環境 ACID 交易內執行。在執行期間，如果 JavaScript 擲回例外狀況，則會中止整個交易。

## 後續步驟
若要開始使用 Azure DocumentDB，請探索以下資源：

-	[了解 DocumentDB 概念](/documentation/articles/documentdb-resources/)
-	[與 DocumentDB 互動](/documentation/articles/documentdb-interactions-with-resources/)
-	[建立資料庫帳戶](/documentation/articles/documentdb-create-account/)
-	[開始使用 DocumentDB](/documentation/articles/documentdb-get-started/)
