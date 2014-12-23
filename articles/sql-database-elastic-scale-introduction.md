<properties title="Azure SQL Database Elastic Scale" pageTitle="Azure SQL Database Elastic Scale" description="Easily scale database resources in the cloud using Elastic Scale feature of Azure SQL Database." metaKeywords="sharding,elastic scale, Azure SQL DB sharding" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Azure SQL Database Elastic Scale 
歡迎使用 Azure SQL Database Elastic Scale 公開預覽！ 

###承諾和挑戰
Azure SQL Database Elastic Scale 履行雲端運算的承諾，提供幾乎無限制的容量以及 Azure SQL DB 平台上的彈性。到目前為止，雲端服務提供者都能履行計算和 Blob 儲存體無限制容量方面的大部分承諾。不過，在論及雲端中可設定狀態的資料處理時，彈性仍然是一項挑戰，特別是涉及關聯式資料庫管理時。我們已知這些挑戰最主要出現在下列兩個案例中： 

* 針對工作負載的關聯式資料庫部分放大和縮小容量。
* 針對可設定狀態的資料庫工作負載及其資料，管理使用率作用點。

傳統上是購買更多硬體來裝載應用程式的資料層，以解決這些案例。不過，此選項在雲端有其限制，因為所有處理都發生在預先定義的商用硬體上。分區化 (或基於容量原因，將資料和處理分散至數個縮放單位) 不論在成本或彈性上，都是超越傳統相應增加方法的替代方式。過去幾年來，我們發現客戶都必須在 Azure SQL DB 之上實作自己的相應放大方法，才能順利使用分區化。對某些人而言，這意味著管理數百或數千個 Azure SQL 資料庫。這表示資料層中有大量程式碼都在處理分區化的複雜性，而不是應用程式的商務邏輯。 

在多年來與客戶的直接往來當中，我們看到從這些專案中形成許多種分區化模式。Azure SQL Database Elastic Scale 針對這些模式提供用戶端程式庫和服務供應項目。Elastic Scale 可讓您更輕鬆地開發、調整和管理 Azure 應用程式的可設定狀態資料層。

因此，您可以專注於應用程式的商務邏輯，而不是建立分區化的基礎結構。  


##功能 

使用分區化來開發、調整和管理相應放大應用程式，對開發人員和系統管理員而言都是一項挑戰。Azure SQL DB Elastic Scale 可讓這兩個角色工作的更輕鬆。圖中的數據概述此公開預覽版本所提供的主要功能。 
下半部顯示應用程式的資料層及其資料分佈在多個資料庫上，稱為「分區」。假設多個資料庫儲存數個分區的資料。 

關於本文使用的詞彙定義，請參閱 [Elastic Scale 名詞解釋](./sql-database-elastic-scale-glossary.md)。

###Elastic Scale 與分區化 

![][1]

此圖的左側和右側顯示開發人員和系統管理員。在提交分區內作業，而不是有其本身語意的跨分區作業時，客戶可以獲得完整的 T-SQL 功能。 
Azure SQL Database Elastic Scale 的公開預覽版透過下列特定的功能，讓開發分區化 Azure SQL DB 應用程式變得更輕鬆： 

* **分區對應管理**：分區對應管理 (1) 可讓應用程式管理其分區的各種中繼資料。分區對應管理是 Elastic Scale 用戶端程式庫的一項功能。開發人員可利用此功能來註冊分區、描述個別分區化索引鍵或索引鍵範圍至分區的對應，並隨著資料層中的分區配置進化來維護此中繼資料，以反映容量變更。分區對應管理構成樣板程式碼的一大部分，客戶在自行實作分區化時必須在應用程式中撰寫這些程式碼。如需詳細資訊，請參閱[分區對應管理](./sql-database-elastic-scale-shard-map-management.md)
 
* **資料相依路由**：想像有一個要求進入應用程式。應用程式必須根據要求的分區化索引鍵值，判斷哪個分區中保存這個分區化索引鍵值的資料，然後對此分區開啟連接 (2) 來處理要求。資料相依路由可讓您輕鬆地呼叫一次應用程式的分區對應，就能開啟連接。資料相依路由是基礎結構程式碼的另一個領域，現在由 Elastic Scale 用戶端程式庫中的功能所涵蓋。如需詳細資訊，請參閱[資料相依路由](./sql-database-elastic-scale-data-dependent-routing.md)

* **多分區查詢 (MSQ)**：當要求牽涉到數個 (或所有) 分區時，多分區查詢就派上用場。多分區查詢 (3) 在所有分區或一組分區上執行相同的 T-SQL 程式碼。使用 UNION ALL 語意可將參與分區的結果合併成整體的結果集。透過用戶端程式庫公開的功能可處理許多工作，包括：連線管理、執行緒管理、錯誤處理和暫時結果處理。MSQ 可以查詢多達數百個分區。如需詳細資訊，請參閱[多分區查詢](./sql-database-elastic-scale-multishard-querying.md)。

* **分區彈性**：這項功能可讓系統管理員透過 PowerShell 指令碼及利用 Azure 自動化服務，將其分區化環境的垂直 (向上和向下調整單一分區的版本) 和水平 (在分區對應中新增或移除分區) 縮放自動化。如需詳細資訊，請參閱[分區彈性](./sql-database-elastic-scale-elasticity.md)。

* **分割合併服務**：當容量需求隨著業務活絡而波動時，應用程式需要靈活地將資料重新分配至多個資料庫 (4)。在同時牽涉到資料移動的情況下，Elastic Scale 提供客戶裝載的服務體驗，可以放大和縮小分區化應用程式的資料層容量和管理作用區。它是根據一項基礎功能，可在不同分區之間隨需求移動 Shardlet，並與分區對應管理整合來維持一致的對應和正確的資料相依路由連接。如需詳細資訊，請參閱[使用 Elastic Scale 分割及合併](./sql-database-elastic-scale-overview-split-and-merge.md)

##常見的分區化模式

**分區化**是一種將大量相同結構的資料分散在許多獨立資料庫的技術。為一般客戶或企業建立軟體即服務 (SAAS) 供應項目的開發人員尤其愛用。這些一般客戶通常稱為「租用戶」。需要使用分區化可能有各種原因： 

* 資料總量太大而超出單一資料庫的條件約束 
* 整體工作負載的交易輸送量超過單一資料庫的能力 
* 租用戶可能需要彼此實際隔離，因此每個租用戶需要個別的資料庫 
* 基於規範、效能或地理政治的理由，資料庫的不同區段可能需要位於不同的地理位置。 

在其他情況下，例如從分散式裝置擷取資料，分區化可用於填滿一組暫時組織的資料庫。例如，一個不同的資料庫可專供每日或每週使用。在此情況下，分區化索引鍵可以是表示日期的整數 (出現在分區化資料表的所有資料列)，而擷取日期範圍資訊的查詢，必須由應用程式遞送至涵蓋相關範圍的資料庫子集。
 
當應用程式中的每一筆交易可以限制為單一分區化索引鍵的單一值時，分區化表現最佳。這可確保所有交易都在特定資料庫的範圍內發生。 

有些應用程式採用最簡單的方法，為每個租用戶建立個別的資料庫。這就是**單一租用戶分區化模式**，在租用戶的細微層級上提供隔離、備份/還原能力和資源縮放。使用單一租用戶分區化時，每個資料庫與特定的租用戶識別碼值 (或客戶索引鍵值) 相關聯，但該索引鍵不一定出現在資料本身。應用程式必須負責將每個要求遞送至適當的資料庫。 

![][2]

其他案例將多個租用戶一起放入資料庫中，而不是將它們隔離至個別的資料庫。這就是一般的**多租用戶分區化模式** - 可能是因為考量到成本、效率，或應用程式管理大量非常小的租用戶。在多租用戶分區化中，資料庫資料表中的資料列都設計成具有索引鍵 (識別租用戶識別碼) 或分區化索引鍵。同樣地，應用程式層負責將租用戶的要求遞送至適當的資料庫。 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-intro/overview.png
[2]:./media/sql-database-elastic-scale-intro/tenancy.png
