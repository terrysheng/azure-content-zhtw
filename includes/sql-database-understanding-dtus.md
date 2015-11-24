資料庫交易單位 (DTU) 是 SQL Database 中的測量單位，代表以實際量值為基礎的資料庫相對功率：資料庫交易。我們用了一組線上交易處理 (OLTP) 要求的典型作業，然後測量在完全載入條件下每秒有多少次交易完成 (以上是簡短版本，您也可以閱讀[基準測試概觀](../articles/sql-database/sql-database-benchmark-overview.md)中繁雜的詳細資料)。

基本資料庫有 5 個 DTU，表示每秒可完成 5 筆交易，而高階 P11 資料庫則有 1,750 個 DTU。

![SQL Database 簡介：不同層級和等級的單一資料庫 DTU。](./media/sql-database-understanding-dtus/single_db_dtus.png)

### DTU 與 eDTU

單一資料庫的 DTU 就相當於彈性資料庫的 eDTU。舉例來說，基本彈性資料庫集區中的資料庫最多可提供 5 個 eDTU。這與單一基本資料庫的效能相同。差別在於彈性資料庫不會取用集區中的任何 eDTU，除非有必要。

![SQL Database 簡介：不同層級的彈性集區。](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

舉個簡單的範例對您會有所幫助。取得有 1000 DTU 的基本彈性資料庫集區，並卸除其中 800 個資料庫。在任何時間點，只要 800 個資料庫中僅有 200 個正在使用 (5 DTU X 200 = 1000)，您就不會達到集區容量的限制，而且資料庫效能也不會降低。這個範例是為了清楚起見而簡化。實際計算時會稍微複雜一點。入口網站會替您做好這項工作，並根據資料庫使用量歷程記錄提供建議。若要了解此建議的運作方式，或自行進行計算，請參閱[彈性資料庫集區的價格和效能考量](../articles/sql-database/sql-database-elastic-pool-guidance.md)。

<!---HONumber=Nov15_HO4-->