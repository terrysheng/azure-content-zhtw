<properties
   pageTitle="Azure SQL Database Web 和 Business 版本終止常見問題集 | Microsoft Azure"
   description="了解 Azure SQL Web 和 Business 資料庫將淘汰，並了解新服務層的特性和功能。"
   services="sql-database"
   documentationCenter="na"
   authors="stevestein"
   manager="jeffreyg"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="02/23/2016"
   ms.author="sstein" />

# Web 和 Business 版本終止常見問題集

Azure SQL Web 和 Business 資料庫現已淘汰。基本、標準、高階和彈性層取代淘汰 Web 和商務資料庫。

為了協助您升級 Web 和 Business 資料庫，SQL Database 服務建議為每個資料庫根據其歷史工作負載選擇適當的服務層和效能層級 (定價層)。

**若要取得定價層建議：**

- [使用 Azure 入口網站升級至 SQL Database V12](sql-database-upgrade-server-portal.md)
- [使用 PowerShell 升級至 SQL Database V12](sql-database-upgrade-server-powershell.md)
- [變更 Web 或 Business 資料庫的定價層](sql-database-service-tier-advisor.md)



## 為什麼 Azure 入口網站沒有將我的 Web 和 Business 版本資料庫顯示為「已淘汰」？

因為 Web 和 Business 版本資料庫於 2015 年 9 月後將無法使用，入口網站會將 Web 和 Business 資料庫標示為「已淘汰」。已淘汰標籤也可作為任何 Web 和商務資料庫應該升級為標準、基本或高階的提醒。如需深入了解將現有的 Web 或 Business 資料庫升級至新的服務層，請參閱[升級至 Azure SQL Database V12](sql-database-upgrade-server-portal.md)。

## 哪一個新服務層是將我現有的 Web 或 Business 資料庫升級的最佳選擇？

為您的現有 Web 或 Business 資料庫選取適當的新服務層和效能層級，取決於您的應用程式的特定功能和效能需求。

使用上述的定價層建議，或者需要深入了解如何選取適當的新服務層，請參閱[升級至 Azure SQL Database V12](sql-database-upgrade-server-portal.md)。

## Microsoft 為什麼推出新服務層？

根據客戶意見反應，Azure SQL Database 正推出新服務層，協助客戶更輕鬆支援關聯式資料庫工作負載。新層次專為跨輕量到重量交易應用程式需求設計的七個層級範圍，提供可預測的效能。此外，新的層次還提供許多業務續航力功能、更強的執行時間 SLA、較低成本使用較大的資料庫大小，以及改善的計費體驗。

## 我可以在何處進一步了解新的服務層？

如需新服務層和效能模型的詳細資訊，請參閱[服務層](sql-database-service-tiers.md)。如需新服務層的詳細價格資訊，請參閱 [SQL Database 價格](https://azure.microsoft.com/pricing/details/sql-database/)。

## 基本、標準和高階中無法使用哪些特性或功能？

同盟功能將隨著 Web 和 Business 版本淘汰。我們鼓勵需要橫向擴充其資料庫的客戶改為對 [Azure SQL Database](sql-database-elastic-scale-get-started.md) 使用或移轉至[彈性資料庫工具](sql-database-elastic-scale-get-started.md)，如此可簡化對使用分區化的應用程式的建置和管理。.NET 用戶端程式庫可讓應用程式定義將資料對應到分區，並將 OLTP 要求遞送到適當的資料庫的方式。為了支援重新設定資料如何分散在分區之間的管理作業，納入了您可以在 Azure 訂用帳戶中管理的 Azure 雲端服務範本。除了[彈性資料庫工具](sql-database-elastic-scale-get-started.md)，Microsoft 將根據與客戶深入合作的經驗，繼續建立和發佈[自訂分區化模式與實例指南](https://msdn.microsoft.com/library/azure/dn764977.aspx)。需要橫向擴充功能的新客戶應該查看[彈性資料庫工具](sql-database-elastic-scale-get-started.md)及/或連絡 Microsoft 支援服務，以評估他們的選項。

Microsoft 也會變更 Premium 資料庫的資料庫複製體驗。先前由於 Premium 資料庫配額有限，T-SQL 中 CREATE DATABASE … AS A COPY OF 會建立沒有保留的資源的 Suspended Premium 資料庫，它是以與 Business 資料庫的相同費率收費。隨著 Premium 配額現在能更自由運用，已不再支援 Suspended Premium。資料庫複本現在將使用與來源相同的版本和效能層級建立，並將據此計費。如果需要，客戶可以選擇將複製的資料庫降級為不同的服務層或效能層級，以降低成本。現有 Suspended Premium 資料庫將隨著這個版本轉換為 Business 版本。預期還原時間點的推出將減少製作資料庫備份的需求。

## 基本、標準和高階如何改善我的計費體驗？

基本、標準和高階 Azure SQL Database 是以小時計費，您可以在 24 小時內向上或向下調整每個資料庫 4 次。將根據您每小時選擇的最高服務層和效能層級，以固定費率向您計費。此外，效能層級 (範例：Basic、S1 和 P2) 會在帳單中細分，讓您更易於查看針對每個效能層級在單一月份中產生的資料庫天數或時數。Web 和 Business 資料庫仍會繼續基於「資料庫單位」根據資料庫大小計費。請瀏覽 [SQL Database 價格頁面](https://azure.microsoft.com/pricing/details/sql-database/)，以深入了解新服務層之間的價格和差異。


## 另請參閱

[Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)

[Web 和 Business 價格](https://azure.microsoft.com/pricing/details/sql-database/web-business/)

[服務層](sql-database-service-tiers.md)

[升級至 Azure SQL Database V12](sql-database-upgrade-server-portal.md)

<!---HONumber=AcomDC_0224_2016-->