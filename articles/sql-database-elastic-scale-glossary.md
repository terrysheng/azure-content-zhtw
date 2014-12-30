<properties title="Azure Elastic Scale Glossary" pageTitle="Azure Elastic Scale 詞彙" description="Azure SQL Database 的 Elastic Scale 功能所用詞彙的說明" metaKeywords="sharding,elastic scale, Azure SQL DB sharding" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Elastic Scale 詞彙
以下是針對 Azure SQL Database 的 Elastic Scale 功能定義的詞彙。

![Elastic Scale terms][1]

**資料庫**：Azure SQL Database。 

**資料相依路由**：讓應用程式使用特定分區化金鑰連線到分區的功能。比較**多分區查詢**。

**全域分區對應**：分區化金鑰與**分區組中的個別分區之間的對應組**。GSM 儲存在 [**分區對應管理員**] 中。比較**本機分區對應**。

**清單分區對應**：個別對應分區化金鑰的分區對應。比較**範圍分區對應**。   

**本機分區對應**：儲存在分區的本機分區對應包含分區內小分區的對應。


**多分區查詢**：對於多個分區進行查詢的能力；使用 UNION ALL 語意 (也稱為「展開傳送查詢」) 傳回結果集。比較**資料相依路由**。

**範圍分區對應**：分區散發策略以連續值的多個範圍為基礎的分區對應。 


**參考資料表**：並未分區而在分區之間複寫的資料表。 

**分區**：儲存分區化資料集資料的 Azure SQL 資料庫。 

**分區彈性** (SE)：執行**水平調整**和**垂直調整**的能力。

**分區化資料表**：分區化 (也就是按照分區化金鑰值在分區之間散發資料) 的資料表。 

**分區化金鑰**：決定如何在分區之間散發資料的欄值。值類型可以是下列其中一個：int、bigint、varbinary 或 uniqueidentifier。 

**分區集**：散發到分區對應管理員之中同一個分區對應的分區集合。  

**小分區**：與分區的分區化金鑰單一值相關聯的所有資料。小分區是散發分區化資料表時可能資料移動的最小單位。 

**分區對應**：分區化金鑰與個別分區之間的對應組。

**分區對應管理員**：包含分區對應、分區位置和一個或多個分區集之對應的管理物件及資料存放區。

![Mappings][2]


##動詞

**水平調整**：對於分區對應新增或移除分區的分區集合擴充 (或縮小) 動作。

**合併**：將兩個分區的小分區移到一個分區並隨之更新分區對應的動作。

**小分區移動**：將一個小分區移到不同分區的動作。 

**分區**：按照分區化金鑰在多個資料庫之間水平分割相同結構資料的動作。

**分割**：將一個分區的多個小分區移到另一個 (通常是新的) 分區的動作。使用者提供分區化金鑰做為分割點。

**垂直調整**：向上 (或向下) 調整個別分區效能等級的動作。例如，將標準的分區變更為 Premium (基於效能考量)。 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]  

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png



<!--HONumber=35_1-->
