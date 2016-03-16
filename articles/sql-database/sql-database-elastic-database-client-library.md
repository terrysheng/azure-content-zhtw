<properties
    pageTitle="Azure SQL Database - 用戶端程式庫"
    description="建置可調整的 .NET 資料庫應用程式"
    services="sql-database"
    documentationCenter=""
    manager="jeffreyg"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/01/2016"
    ms.author="ddove;sidneyh"/>

# 彈性資料庫用戶端程式庫概觀

**彈性資料庫用戶端程式庫**，可讓您使用 Microsoft Azure 上的 Azure SQL Database 數百或甚至上千個集合的用戶端應用程式，來輕鬆開發分區化應用程式。這類設計通常用於軟體即服務 (SaaS) 應用程式，其通常為單一租用戶架構 -- 其中每個租用戶是使用資料庫佈建。建立和管理這類應用程式是程式庫的目標。

「彈性資料庫」用戶端程式庫現在已在 [GitHub](https://github.com/Azure/elastic-db-tools) 上以開放原始碼軟體的形式提供。若要安裝程式庫，請參閱 [Microsoft Azure SQL Database：Elastic Scale](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。用戶端程式庫會特別去的彈性資料庫工具組件[彈性資料庫功能](sql-database-elastic-scale-introduction.md)。

## 用戶端功能

使用*分區化* (下文中會討論) 來開發、縮放和管理相應放大應用程式，對開發人員和系統管理員而言都是一項挑戰。用戶端程式庫讓這兩個角色工作的更輕鬆。下圖指出彈性資料庫用戶端程式庫所提供的主要功能。圖中顯示一個具有許多資料庫的環境，而每個資料庫對應至一個分區。雖然每個客戶 (租用戶) 一個資料庫也是一樣，但是在此範例中，許多客戶使用範圍對應共同位於相同資料庫。這些工具透過下列特定功能，讓開發分區化 Azure SQL Database 應用程式變得更輕鬆：

關於本文使用的詞彙定義，請參閱[彈性資料庫工具字彙](sql-database-elastic-scale-glossary.md)。

![彈性擴縮功能][1]

1.  **分區對應管理**：為管理分區的集合，會建立稱為「分區對應管理員」的特殊資料庫。分區對應管理可讓應用程式管理其分區的各種中繼資料。開發人員可利用此功能將資料庫註冊為分區 (描述個別分區化索引鍵或索引鍵範圍至這些資料庫的對應)，並隨著資料庫的數量和組成發展來維護此中繼資料，以反映容量變更。若沒有彈性資料庫用戶端程式庫，實作分區化時您必須花費大量時間撰寫管理程式碼。如需詳細資訊，請參閱[分區對應管理](sql-database-elastic-scale-shard-map-management.md)。

* **資料相依路由**：想像有一個要求進入應用程式。應用程式必須根據要求的分區化索引鍵值，判斷哪個資料庫中有這個索引鍵值的資料，然後對此它開啟連接來處理要求。資料相依路由可讓您輕鬆地呼叫一次應用程式的分區對應，就能開啟連接。資料相依路由是基礎結構程式碼的另一個領域，現在由彈性資料庫用戶端程式庫中的功能所涵蓋。如需詳細資訊，請參閱[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)。

* **多分區查詢 (MSQ)**：當要求牽涉到數個 (或所有) 分區時，多分區查詢就派上用場。多分區查詢在所有分區或一組分區上執行相同的 T-SQL 程式碼。使用 UNION ALL 語意可將參與分區的結果合併成整體的結果集。經由用戶端程式庫公開的功能可處理許多工作，包括：連接管理、執行緒管理、錯誤處理和中繼結果處理。MSQ 可以查詢多達數百個分區。如需詳細資訊，請參閱[多分區查詢](sql-database-elastic-scale-multishard-querying.md)。

一般而言，使用彈性資料庫工具的客戶在提交分區內作業，而不是有其本身語意的跨分區作業時，就可以獲得完整的 T-SQL 功能。

## 後續步驟

請嘗試示範用戶端功能的[範例應用程式](sql-database-elastic-scale-get-started.md)。

若要安裝文件庫，請移至[彈性資料庫用戶端程式庫](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。

如需有關使用分割合併工具的指示，請參閱[分割合併工具概觀](sql-database-elastic-scale-overview-split-and-merge.md)。

[彈性資料庫用戶端程式庫現在已開放原始碼！](https://azure.microsoft.com/blog/elastic-database-client-library-is-now-open-sourced/)


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-database-client-library/glossary.png

<!---HONumber=AcomDC_0204_2016-->