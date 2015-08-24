資源|預設限制
---|---
資料庫大小|視效能層級而定 <sup>1</sup>
登入|視效能層級而定 <sup>1</sup>
記憶體使用量|16 MB 記憶體授權超過 20 秒
工作階段|視效能層級而定 <sup>1</sup>
Tempdb 大小|5 GB
交易持續時間|24 小時<sup>2</sup>
每筆交易鎖定|100 萬
每筆交易的大小|2 GB
每一交易使用的總記錄空間百分比|20%
最大並行要求 (背景工作執行緒)|視效能層級而定 <sup>1</sup>


<sup>1</sup>SQL Database 具有效能層，例如基本、標準和進階。標準和進階也分為不同效能層級。如需每一層和服務層級的詳細限制，請參閱 [Azure SQL Database 服務層和效能層級](https://msdn.microsoft.com/library/azure/dn741336.aspx)。

<sup>2</sup>如果交易鎖定基礎系統工作所需的資源，則最大持續時間為 20 秒。

<!---HONumber=August15_HO7-->