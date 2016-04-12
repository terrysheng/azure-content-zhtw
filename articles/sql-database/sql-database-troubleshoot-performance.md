<properties
	pageTitle="為 Azure SQL Database 中的資料庫效能進行疑難排解。"
	description="為資料庫效能進行疑難排解的快速步驟。"
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="v-shysun"/>

# 使用 Azure SQL Database 來為資料庫效能進行疑難排解
您可以隨時變更單一資料庫的[服務層級](sql-database-service-tiers.md)或增加彈性資料庫集區的 eDTU 以提升查詢效能，但建議您先找出改善與最佳化的時機。遺漏索引與查詢最佳化不足是資料庫效能不佳的常見原因。

## 評估和調整資料庫效能的步驟
1.	在 [Azure 入口網站](https://portal.azure.com)中，按一下 [SQL Database]、選取資料庫，然後使用監視圖表尋找接近其上限的資源。預設會顯示 DTU 耗用量。按一下 [編輯] 來變更所顯示的時間範圍和值。
2.	使用[查詢效能深入解析](sql-database-query-performance.md)評估使用 DTU 的查詢，然後使用[索引建議程式](sql-database-index-advisor.md)建議並建立索引。
3.	您可以使用動態管理檢視 (DMV)、擴充的事件 (Xevent) 和 SSMS 中的查詢存放區即時取得效能參數。如需詳細的監視和調整秘訣，請參閱[效能指引主題](sql-database-performance-guidance.md)。

## 使用更多資源提升資料庫效能的步驟
1.	若是單一資料庫，您可以視需要[變更服務層級](sql-database-scale-up.md)以提升資料庫效能。
2.	若是多個資料庫，請考慮使用[彈性資料庫集區](sql-database-elastic-pool-guidance.md)自動調整資源。

如果持續發生效能問題，請連絡支援人員開啟支援案例。

<!---HONumber=AcomDC_0330_2016-->