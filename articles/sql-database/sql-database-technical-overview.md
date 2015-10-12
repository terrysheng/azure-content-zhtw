<properties
   pageTitle="什麼是 SQL Database | Microsoft Azure"
   description="探索 Azure SQL Database、Microsoft 的關聯式資料庫管理系統 (RDBMS) 與 PaaS 解決方案在雲端中的技術詳細資料和功能。"
   services="sql-database"
   documentationCenter=""
   authors="shontnew"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/30/2015"
   ms.author="shkurhek"/>

# SQL Database 簡介

SQL Database 是以領先市場的 Microsoft SQL Server 引擎為基礎，位於雲端並擁有許多關鍵任務功能的關聯式資料庫服務。SQL Database 提供可預測的效能、無停機時間的延展性、商務持續性和資料保護等功能，且全都幾乎免管理。如此一來，您便可以專注於快速開發應用程式及加快上市時間，而不是耗費在管理虛擬機器與基礎結構上。由於 SQL Database 是以 [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) 引擎為基礎，因此可支援現有 SQL Server 工具、程式庫和 API，讓您可以更輕鬆地移動和延伸至雲端。


如果準備好了，幾分鐘後就可以開始；如果想更深入了解，請觀賞這段 30 分鐘的影片。


> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]


本文將介紹 SQL Database 核心概念和功能的效能、延展性和管理能力，並提供連結讓您進一步了解詳細資料。如果您已經準備開始，可以立即[建立您的第一個 SQL Database](sql-database-get-started.md)，或者[建立彈性資料庫集區](sql-database-elastic-pool-portal.md)。

## 無須停機即可調整效能和規模
基本、標準和高階服務層皆提供 SQL Database。每個服務層提供[不同層級的效能和功能](sql-database-service-tiers.md)，以支援輕量到重量級的資料庫工作負載。您可以在小型資料庫中建置第一個應用程式，一個月只需少許花費。接著可依據應用程式在全世界受歡迎的程度，隨時以手動或程式設計方式[變更服務層](sql-database-scale-up.md)，且應用程式或客戶皆無須停機。

對於許多企業和應用程式而言，只要能夠建立資料庫，並依需求調高或調低單一資料庫的效能即可，尤其是當使用模式相當容易預測時更是如此。但如果您有無法預測的使用模式，則管理成本和商務模式就會變得相當困難。

SQL Database 中的[彈性資料庫集區](sql-database-elastic-pool.md)可解決此問題。概念很簡單。您可以將效能配置給集區，並針對集區的整體效能 (而非單一資料庫的效能) 付款。您不需要調高或調低資料庫的效能。集區中的資料庫 (即*彈性資料庫*) 可依據需求自動擴大或縮減規模。彈性資料庫會取用集區的資源，但不會超過其限制，因此您的成本可在資料庫使用情形無法預測的狀況下維持可預測性。此外，您還可以[將資料庫移入/移出集區](sql-database-elastic-pool-portal.md)，並將您的應用程式從數個資料庫擴充至數千個，而且全都在您可掌控的預算之內。

因此，不論您要使用單一或彈性資料庫，都將不再受到限制。您可以混合使用彈性資料庫集區與單一資料庫，並變更單一資料庫和集區的服務層，以製作創新的設計。此外，透過 Azure 功能強大而無遠弗屆的特性，您可以使用 SQL Database 混合和搭配 Azure 服務，滿足您獨特新型應用程式的設計需求、有效運用成本和資源，並且產生新的商機。

但是，您要如何比較資料庫和資料庫集區的相對效能？ 當您調高和調低效能時，要如何知道該在何處停止？ 答案就是使用單一資料庫的資料庫交易單位 (DTU)，以及彈性資料庫和資料庫集區的彈性 DTU (eDTU)。

## 了解 DTU

資料庫交易單位 (DTU) 是 SQL Database 中的測量單位，代表以實際量值為基礎的資料庫相對功率：資料庫交易。我們用了一組線上交易處理 (OLTP) 要求的典型作業，然後測量在完全載入條件下每秒有多少次交易完成 (以上是簡短版本，您也可以閱讀[基準測試概觀](https://msdn.microsoft.com/library/azure/dn741327.aspx)中繁雜的詳細資料)。

基本資料庫有 5 個 DTU，表示每秒可完成 5 筆交易，而高階 P11 資料庫則有 1,750 個 DTU。

![單一資料庫 DTU](./media/sql-database-technical-overview/single_db_dtus.png)

單一資料庫的 DTU 就相當於彈性資料庫的 eDTU。舉例來說，基本彈性資料庫集區中的資料庫最多可提供 5 個 eDTU。這與單一基本資料庫的效能相同。差別在於彈性資料庫不會取用集區中的任何 eDTU，除非有必要。

![彈性集區和 eDTU](./media/sql-database-technical-overview/sqldb_elastic_pools.png)

舉個簡單的範例對您會有所幫助。取得有 1,000 DTU 的基本彈性資料庫集區，並移除其中 800 個資料庫。在任何時間點，只要 800 個資料庫中僅有 200 個正被使用 (5 DTU X 200 = 1000)，您就不會達到集區容量的限制，而且資料庫效能也不會降低。這個範例是為了清楚起見而簡化。實際計算時會稍微複雜一點。入口網站會替您做好這項工作，並根據資料庫使用量歷程記錄提供建議。若要了解此建議的運作方式或自行計算，請參閱[彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。

## 讓您的應用程式和業務持續運作

Azure 領先業界的 99.99% 可用性服務等級協定 [(SLA)](http://azure.microsoft.com/support/legal/sla/) (由 Microsoft 管理之資料中心的全球網路提供支援)，可協助讓您的應用程式 24 小時全年無休地運作。使用每個 SQL Database 時，您可以利用內建的資料保護、容錯功能，以及可能會需要另外設計、購買、建置和管理的資料保護功能。即便如此，根據您的業務需求，您也可能會要求額外層級的保護，以確保在發生嚴重損壞、錯誤或其他事件時，應用程式和業務可以快速復原。使用 SQL Database 時，每個服務層會提供不同的功能選單，讓您用來立即上線運作。您可以使用時間點還原將資料庫回復成先前的狀態，最久可至 35 天前。此外，如果裝載資料庫的資料中心發生中斷情形，您可以容錯移轉至其他區域中的資料庫複本。或者您可以使用複本進行升級，或將其重新放置到不同區域。

![SQL Database 異地複寫](./media/sql-database-technical-overview/azure_sqldb_map.png)


如需可用於不同服務層之其他商務持續性功能的詳細資料，請參閱[商務持續性](sql-database-business-continuity.md)。

## 保護您的資料
SQL Server 的資料安全性向來是一項可靠的傳統，而 SQL Database 支援與其相同的多項功能，可限制存取權、保護資料，以及協助您監視活動。如需您可在 SQL Database 中使用之安全性選項的快速概要，請參閱[保護您的 SQL Database](sql-database-security.md)。如需更完整的安全性功能檢視，請參閱 [SQL Server Database Engine 和 SQL Database 的資訊安全中心](https://msdn.microsoft.com/library/bb510589)。如需 Azure 平台安全性的相關資訊，也請造訪 [Azure 信任中心](http://azure.microsoft.com/support/trust-center/security/)。

## 後續步驟

- 如需單一資料庫及彈性資料庫的定價和計算機，請參閱[定價頁面](http://azure.microsoft.com/pricing/details/sql-database/)。

- 從[建立您的第一個資料庫](sql-database-get-started.md)開始。然後以 [C#](sql-database-connect-query.md)、[Java](sql-database-develop-java-simple-windows.md)、[Node.js](sql-database-develop-nodejs-simple-windows.md)、[PHP](sql-database-develop-php-retry-windows.md)、[Python](sql-database-develop-python-simple-windows.md) 或 [Ruby](sql-database-develop-ruby-simple-linux) 建置您的第一個應用程式。

<!---HONumber=Oct15_HO1-->