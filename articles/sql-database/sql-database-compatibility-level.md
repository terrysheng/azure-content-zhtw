<properties 
	pageTitle="SQL Database 中的相容性層級 | Microsoft Azure" 
	description="說明如何設定 Azure SQL Database 資料庫的相容性層級，以及受影響的功能。"
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2015" 
	ms.author="genemi"/>


# SQL Database 中的相容性層級


對於 Azure SQL Database，本主題描述您可以使用 Transact-SQL 陳述式 **ALTER DATABASE** 選擇的功能選項。相容性層級概念的設計目地是降低任何升級的風險。


由相容性層級啟用或停用的大部分功能都是查詢最佳化工具的一部分。Microsoft 會在下列時機於相容性層級上意外啟動新的查詢最佳化工具功能：


- 升級的內容會改變舊版功能的語意。
- 查詢最佳化可能會傷害某些不常見但合法之 SQL 查詢的效能。


如果您的查詢執行效果在版本升級至 Azure SQL Database 伺服器或資料庫之後變得較差，您可以選擇降低相容性層級。較低的設定可以停用一組少數的最佳化工具增強功能，可能包括不適用於您的查詢的功能。


## 相容性層級的運作方式


Microsoft SQL Server 多年來都具備可設定的相容性層級。從 V12 版開始，Azure SQL Database 也會採用相容性層級。部分新功能的啟用是由每個 SQL Database 資料庫內的相容性層級設定來控制。可能的設定包括：


- 110：最低的可能設定，因此也是排除最新功能的設定。
- 120：鬆散對應至 SQL Database V11 (表示 `SELECT @@version;` 傳回 **11.**0.0.0)。
 - V11 版也稱為 SQL Database 的 'SAWA V2'。
 - 此 120 是 Microsoft SQL Server 2014 中的最大值。
- 130：鬆散對應至 V12 (表示 `SELECT @@version;` 傳回 **12.**0.0.0)。
 - 此 130 是 Microsoft SQL Server 2016 中的最大值。


比方說，120 設定表示您的資料庫可存取在層級 120 啟用的功能子集，*加上*在 110 等任何較低層級設定啟用的功能。設定在 120 時，您的資料庫並沒有層級 130 功能的存取權。


## 讀取或設定相容性層級


### 讀取相容性層級


```
SELECT d.name, d.compatibility_level
	FROM sys.databases AS d
	WHERE d.name = db_name();
```


### 設定相容性層級


```
ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL = 130;
```


如需詳細資訊，請參閱：


- [ALTER TABLE 相容性層級 (Transact-SQL)](http://msdn.microsoft.com/library/bb510680.aspx)
- [sys.databases (Transact-SQL)](http://msdn.microsoft.com/library/ms178534.aspx)


## 記憶體中資料表和資料行存放區索引


在層級 120 和 130 啟用的大部分相容性層級功能都和*記憶體中*的資料表和索引相關。因此重要的項目包括：


- 記憶體最佳化資料表
- 資料行存放區索引


牽涉到記憶體中的項目時，相容性層級 130 可提供下列優點：


- 啟用查詢最佳化工具以*批次*模式處理更多查詢的功能。
 - 牽涉到許多資料列時，批次模式的速度比*列*模式更快。
- 新增 **SORT** 運算子。
- 新增視窗彙總。


如需有關記憶體中資料表和資料行存放區索引的詳細資訊，請參閱：


- [記憶體最佳化資料表](http://msdn.microsoft.com/library/dn133165.aspx)
- [CREATE CLUSTERED COLUMNSTORE INDEX (Transact-SQL)](http://msdn.microsoft.com/library/dn511016.aspx)
- [已描述的資料行存放區索引](http://msdn.microsoft.com/library/gg492088.aspx)


## 需要最小層級 130 的一般功能


基數估計器 (CE) 會在資料表中產生並儲存接近數目資料列的相關統計資料。查詢最佳化工具的許多部分使用基數資訊。


CE 所使用的演算法已經有所改進。改進造成變更，而變更可能會導致不常見的特殊化案例迴歸。


| 130 是最小的<br/>必要<br/>層級 | 查詢區域，<br/>一般 | <br/>查詢計劃<br/>改進的詳細資料 |
| :-- | :-- | :-- |
| 130 | 基數估計器 (CE) | 相較於層級 120 的舊版基數估計器 (CE)，改善 CE。<br/><br/>您可能會在查詢計劃中看到：**CardinalityEstimationModelVersion="130"**<br/><br/>** 追蹤旗標** [**9481**](http://www.sqlservergeeks.com/sql-server-2014-trace-flags-9481/) 可以在資料庫位於層級 130 時開啟以使用層級 120 的 CE。當您的資料庫位於相容性層級 130 時，<br/><br/>**追蹤旗標** [**4199**](http://support.microsoft.com/kb/974006) 可以設定為關閉以從 hotfix 中選擇查詢最佳化工具。此旗標僅適用於層級 130 完全超出預覽並公開上市 (GA) 發行之後所實作的 hotfix。如需詳細資料，請參閱：<br/><br/>● [DBCC TRACEON](http://msdn.microsoft.com/library/ms187329.aspx) |
| 130 | 記憶體中資料表的平行查詢計劃 | 查詢可以使用多個執行緒並且可以針對記憶體中資料表平行執行，表示該資料表以 **MEMORY\_OPTIMIZED = YES** 子句建立。平行處理原則可更快速執行查詢。<br/><br/>一般的 Transact-SQL 與使用者預存程序支援這項增強功能。但編譯成 DLL 的原生預存程序不支援它。 |


## 需要最小層級 130 的資料行存放區索引功能


在相容性層級 130，查詢最佳化工具增強功能可能會導致新的查詢計劃。對牽涉到資料行存放區索引的變更計劃而言，變更通常會牽涉到下列其中一項：


- 進一步子作業必須複製資料的情況減少。
- 排序等作業從*列*處理變更為***批次***處理。


在大部分情況下，計劃變更會牽涉以下項目以改善查詢的效能：


- 平行插入資料行存放區索引。
 - 層級 130 不提供非叢集索引的平行掃描。
- 增加 **tempdb** 資料庫的使用。


| 130 是最小的<br/>必要<br/>層級 | 查詢區域，<br/>資料行存放區索引 | <br/>查詢計劃<br/>改進的詳細資料 |
| :-- | :-- | :-- |
| 130 | 函式查詢 | 切換至批次模式可在下列情況下改進效能：<br/><br/>• 包含排序。<br/><br/>• 包含*多個*不同函式的彙總<br/>(來自下列清單中兩個不同項目符號的函式)：<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT** *或* **COUNT\_BIG**<br/>&nbsp;&nbsp;&nbsp;▫ **AVG** *或* **SUM**<br/>&nbsp;&nbsp;&nbsp;▫ **CHECKSUM\_AGG**<br/>&nbsp;&nbsp;&nbsp;▫ **STDEV** *或* **STDEVP**<br/><br/>• 視窗彙總函式<br/>(描述於 [MSDN 的這裡](http://msdn.microsoft.com/library/ms189461.aspx) 和 [ Kathi Kellenberger 的這裡](http://www.bidn.com/blogs/KathiKellenberger/sql-server/4397/what-is-a-window-aggregate-function))：<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT**, **COUNT\_BIG**, **SUM**, **AVG**, **MIN**, **MAX**, **CLR**<br/><br/>• 視窗 [使用者定義的](http://msdn.microsoft.com/library/ms131057.aspx) 彙總：<br/>&nbsp;&nbsp;&nbsp;▫ [**CHECKSUM\_AGG**](http://msdn.microsoft.com/library/ms188920.aspx), [**STDEV**](http://msdn.microsoft.com/library/ms190474.aspx), [**STDEVP**](http://msdn.microsoft.com/library/ms176080.aspx), [**VAR**](http://msdn.microsoft.com/library/ms186290.aspx), [**VARP**](http://msdn.microsoft.com/library/ms188735.aspx), [**GROUPING**](http://msdn.microsoft.com/library/ms178544.aspx)<br/><br/>• 視窗彙總分析函式：<br/>&nbsp;&nbsp;&nbsp;▫ [**LAG**](http://msdn.microsoft.com/library/hh231256.aspx), [**LEAD**](http://msdn.microsoft.com/library/hh213125.aspx), [**FIRST\_VALUE**](http://msdn.microsoft.com/library/hh213018.aspx), [**LAST\_VALUE**](http://msdn.microsoft.com/library/hh231517.aspx), [**PERCENTILE\_CONT**](http://msdn.microsoft.com/library/hh231473.aspx), [**PERCENTILE\_DISC**](http://msdn.microsoft.com/library/hh231327.aspx), [**CUME\_DIST**](http://msdn.microsoft.com/library/hh231078.aspx), [**PERCENT\_RANK**](http://msdn.microsoft.com/library/hh213573.aspx) |
| 130 | 單一執行緒的序列查詢計劃 | 在單一執行緒上執行的查詢可以利用批次模式執行。這樣可以使查詢執行得更快。<br/><br/>查詢計劃可能會設計為單一執行緒，或者查詢可能會在 **MAXDOP 1** 下執行。 |
| 130 | 平行插入 | 您的查詢計劃可以執行一些平行插入。 < b /<br/>本主題稍後的[範例](#ExampleQueryParallelCciByCompatLevel)會示範這個平行處理原則。 |
| 130 | 反半聯結 | 此運算子現在可利用批次模式執行。 |


## 需要最小層級 120 的一般功能


| 120 是最小的<br/>必要<br/>層級 | 查詢區域，<br/>一般 | <br/>查詢計劃<br/>改進的詳細資料 |
| :-- | :-- | :-- |
| 120 | [Altering MEMORY\_OPTIMIZED 資料表](http://msdn.microsoft.com/library/dn269114.aspx) | 可讓您在具有 **MEMORY\_OPTIMIZED = YES** 的資料表上執行 Transact-SQL **ALTER TABLE** 作業。<br/><br/>資料庫應用程式可以繼續執行，但存取資料表的作業會封鎖，直到 **ALTER TABLE** 完成。 |
| 120 | [建立和管理記憶體最佳化物件的儲存體](http://msdn.microsoft.com/library/dn133174.aspx) | 記憶體中 OLTP 引擎已整合至 SQL Server。這可讓您同時在相同資料庫中擁有 **MEMORY\_OPTIMIZED** 資料表和傳統磁碟資料表。 |
| 120 | [記憶體中 OLTP 的 Transact-SQL 支援](http://msdn.microsoft.com/library/dn133180.aspx) | 已增強多種 Transact-SQL 命令來支援記憶體中線上交易處理 (OLTP)。<br/><br/>其中一個範例是 [CREATE PROCEDURE](http://msdn.microsoft.com/library/ms187926.aspx) 命令上的新 **NATIVE\_COMPILATION** 關鍵字。 |
| 120 | 基數估計器 (CE) | 相較於層級 110 的舊版基數估計器 (CE) 改善 CE。<br/><br/>您可能會在查詢計劃中看到：**CardinalityEstimationModelVersion="120"**<br/><br/>如需**追蹤旗標 4199**如何與相容性層級值互動的詳細資料，請參閱 [KB 974006](http://support.microsoft.com/kb/974006)。|


## 需要最小層級 120 的資料行存放區索引功能


本節描述會在相容性層級 120 或更高層級啟用的[資料行存放區索引的功能](http://msdn.microsoft.com/library/dn934994.aspx)。


| 120 是最小的<br/>相容性<br/>層級 | 資料行存放區索引<br/>功能名稱 | 資料行存放區索引<br/>功能描述 |
| :-- | :-- | :-- |
| 120 | 快照隔離 (SI) 層級和 <br/><br/>Read Committed 快照隔離 (RCSI) 等級。 | 查詢計劃牽涉到資料行存放區索引時，SI 和 RCSI 會防止部分完成交易的資料不會包含在查詢結果中，且不需要過多的鎖定。 |
| 120 | 索引重組 | 已刪除資料列會遭到移除，並且沒有明確的索引重建。<br/><br/>**ALTER INDEX...REORGANIZE** 會在資料表和索引仍可線上運作時從資料行存放區索引移除已刪除的資料列。 |
| 120 | 可在 AlwaysOn 上存取的[可讀取次要複本](http://msdn.microsoft.com/library/ff878253.aspx) | 您可以藉由將分析查詢卸載到 AlwaysOn 次要複本來改善作業分析的效能。 |
| 120 | 彙總向下推送，<br/>在彙總函式的資料表掃描階段 | 在查詢計劃中提早完成過渡計算以提高效能，可讓稍後的階段複製較少資料。<br/><br/>套用至 **MIN**、**MAX**、**SUM**、**COUNT**、**AVG**。<br/><br/>只有在資料類型是八個位元組或更少時適用，但不適用於字串。 |
| 120 | 向下推送以字串為基礎的 **WHERE** 子句 | 述詞下推最佳化可以加快比較字串資料類型 &#x5b;var&#x5d;char 或 n&#x5b;var&#x5d;char 的查詢。此最佳化：<br/><br/>• 適用於一般比較運算子，包括使用點陣圖篩選的 **LIKE**。<br/><br/>• 只有一個字串述詞時才運作。<br/><br/>• 使用此產品支援的所有定序。<br/><br/>如果您想要點陣圖篩選的更多詳細資料，請參閱這篇部落格文章：[查詢執行點陣圖篩選簡介](http://blogs.msdn.com/b/sqlqueryprocessing/archive/2006/10/27/query-execution-bitmaps.aspx)。 |



<a id="ExampleQueryParallelCciByCompatLevel" name="ExampleQueryParallelCciByCompatLevel"></a>


&nbsp;


## 根據相容性層級的查詢計劃範例變更


關於 Transact-SQL **INSERT...SELECT** 陳述式，本節會顯示其相容性層級 120 和 130 之間的查詢計劃變更。


#### 來源資料表結構描述


下表包含至少 300,000 個資料列。如果資料太少使得平行處理原則並不重要時，進階查詢計劃可能不需要考慮平行處理原則。


```
CREATE TABLE [dbo].[ccitestoriginal](
	[FinanceKey] [int] NOT NULL,
	[DateKey] [int] NOT NULL,
	[OrganizationKey] [int] NOT NULL,
	[DepartmentGroupKey] [int] NOT NULL,
	[ScenarioKey] [int] NOT NULL,
	[AccountKey] [int] NOT NULL,
	[Amount] [float] NOT NULL,
	[Date] [datetime] NULL
) ON [PRIMARY];
GO

CREATE CLUSTERED COLUMNSTORE INDEX [cci_ccitestoriginal]
	ON [dbo].[ccitestoriginal]
	WITH (DROP_EXISTING = OFF) ON [PRIMARY];
GO
```


#### 產生查詢計劃的指令碼


將這些步驟套用到以下的 Transact-SQL 指令碼：


1. 在 **Ssms.exe** 中連接到您的資料庫。
2. 將 Transact-SQL 指令碼貼到 **Ssms.exe** 查詢視窗。
3. 編輯指令碼以確保 **120** 成為 **ALTER DATABASE** 陳述式上的值。
4. 只執行指令碼在 **INSERT INTO** 陳述式*之前*的部分。
5. 啟用功能表選項：[查詢] > [包括實際執行計劃 (Ctrl + M)]。
6. 只執行 **INSERT INTO** 陳述式。<br/><br/>
7. 停用功能表選項：[查詢] > [包括實際執行計劃 (Ctrl + M)]。
8. 最後，只執行指令碼在 **INSERT INTO** 陳述式*之後*的部分。
9. 請注意 [執行計劃] 索引標籤 (接近 [結果] 索引標籤) 中適用於 **120** 的查詢計畫。<br/>-- -- -- -- -- --
10. 編輯指令碼讓 **130** 成為 **ALTER DATABASE** 上的值。
11. 重新執行指令碼，如先前步驟中所述。
12. 請注意適用於 **130** 的查詢計劃是不同的，而且包含平行處理原則。


&nbsp;


```
go
SET NOCOUNT ON;
go

ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL =
	120
	--130
;
go

DROP TABLE ccitest_into_work;
go

SELECT *
	INTO ccitest_into_work  -- Create an empty copy of the table.
	FROM ccitestoriginal
	WHERE 1=2;
go

CREATE CLUSTERED COLUMNSTORE INDEX ccitest_into_work_cci
	ON ccitest_into_work;
go

SET NOCOUNT OFF;
go
	
	-- Run this INSERT statement alone!
	--
	-- First run all the preceding Transact-SQL statements.
	-- Then run this one INSERT statement alone.
	-- Then run all remaining Transact-SQL statements.

INSERT INTO ccitest_into_work WITH (TABLOCK)
	SELECT TOP 300000 *
		FROM ccitestoriginal;
go

SET NOCOUNT ON;
go

DROP TABLE ccitest_into_work;
go
```


#### 顯示兩個計劃



<br/>**120：**以下是相容性層級為 **120** 時的查詢計劃。


![層級 120 的計劃][1-Plan-at-level-120]


<br/>**130：**以下是相容性層級為 **130** 時的查詢計劃。


**130** 計劃包含 **120** 計劃沒有的*平行處理原則*。


這個計劃的顯示在 **Ssms.exe** 相當寬廣。為了在此呈現較佳的顯示效果，螢幕擷取畫面會分成兩個部分。第二個部分會接續第一個部分。


![層級 130 的計劃][2-Plan-at-level-130]


## 資料庫中的預設相容性層級


當您升級您的 Azure SQL Database 伺服器時，例如從 V11 版升級到 V12 版，每個資料庫中的相容性層級會維持不變。升級之後，您可以使用 **ALTER DATABASE** 陳述式在任何指定的資料庫中的增加相容性層級。


任何新建立的資料庫都會有 SQL Database 版本所允許的最高相容性層級。



<!-- Image references. -->

[1-Plan-at-level-120]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b-120.png

[2-Plan-at-level-130]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b12-130.png

<!---HONumber=Sept15_HO4-->