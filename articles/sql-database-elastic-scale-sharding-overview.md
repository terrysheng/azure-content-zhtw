<properties title="Sharding Overview" pageTitle="分區化概觀" description="Reasons for sharding: scale database resources to increase availability or performance." metaKeywords="sharding, scaling, elastic scale, Azure SQL Database" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />
#分區化概觀 

##分區化的原則 

**分區化**是一種將大量相同結構的資料分散在許多獨立資料庫的技術。為一般客戶或企業建立軟體即服務 (SAAS) 供應項目的開發人員尤其愛用。這些一般客戶通常稱為「租用戶」。需要使用分區化可能有各種原因： 

* 資料總量太大而超出單一資料庫的條件約束 
* 整體工作負載的交易輸送量超過單一資料庫的能力 
* 租用戶可能需要彼此實際隔離，因此每個租用戶需要個別的資料庫 
* 基於規範、效能或地理政治的理由，資料庫的不同區段可能需要位於不同的地理位置。 
 
當應用程式中的每一筆交易可以限制為單一分區化索引鍵的單一值時，分區化表現最佳。這可確保所有交易都在特定資料庫的範圍內發生。 

有些應用程式採用最簡單的方法，為每個租用戶建立個別的資料庫。這就是**單一租用戶分區化模式**，在租用戶的細微層級上提供隔離、備份/還原能力和資源縮放。使用單一租用戶分區化時，每個資料庫與特定的租用戶識別碼值 (或客戶索引鍵值) 相關聯，但該索引鍵不一定出現在資料本身。應用程式必須負責將每個要求遞送至適當的資料庫。 

![][1]

其他案例將多個租用戶一起放入資料庫中，而不是將它們隔離至個別的資料庫。這就是一般的**多租用戶分區化模式** - 可能是因為考量到成本、效率，或應用程式管理大量非常小的租用戶。在多租用戶分區化中，資料庫資料表中的資料列都設計成具有索引鍵 (識別租用戶識別碼) 或分區化索引鍵。同樣地，應用程式層負責將租用戶的要求遞送至適當的資料庫。 

在其他情況下，例如從分散式裝置擷取資料，分區化可用於填滿經過一段時間所分佈的一組資料庫。例如，一個不同的資料庫可專供每日或每週使用。在此情況下，分區化索引鍵可以是表示日期的整數 (出現在分區化資料表的所有資料列)，而擷取日期範圍資訊的查詢，必須由應用程式遞送至涵蓋相關範圍的資料庫子集。

不論使用何種分區化模型，有一個稱為**分區對應**的特殊資料結構，用來做為將分區化索引鍵值與資料庫相關聯的查閱資料表。這可讓應用程式對資料庫要求執行路由傳送。這稱為**資料相依路由**，也是應用程式使用分區化資料層所需的核心功能。[Elastic Scale 用戶端 API](http://go.microsoft.com/?linkid=9862592) 提供[管理分區對應](http://go.microsoft.com/?linkid=9862595)所需的一組豐富的功能，可在應用程式中啟用有效率、簡單易用的 [Data-depenendent routing capabilities](http://go.microsoft.com/?linkid=9862596)。 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-sharding-overview/tenancy.png
