<properties
   pageTitle="疑難排解 |Microsoft Azure"
   description="疑難排解 SQL 資料倉儲的問題。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="mausher;sonyama;barbkess"/>

# 疑難排解
下列主題列出一些客戶在使用 SQL 資料倉儲時常見的問題。

## 連線能力
連接到 Azure SQL 資料倉儲可能會因為下列幾個常見的原因而失敗：

- 未設定防火牆規則
- 使用不支援的工具/通訊協定

### 防火牆規則
為確保只有已知的 IP 位址可以存取資料庫，Azure SQL 資料庫受到伺服器及資料庫層級的防火牆所保護。防火牆預設受到保護，亦即您必須允許您的 IP 位址存取，才能連線。

若要設定防火牆的存取，請執行 [[佈建](sql-data-warehouse-get-started-provision.md)] 頁面中的[設定您用戶端 IP 的伺服器防火牆存取](sql-data-warehouse-get-started-provision.md/#step-4-configure-server-firewall-access-for-your-client-ip)一節。

### 使用不支援的工具/通訊協定
SQL 資料倉儲支援 [Visual Studio 2013/2015年](sql-data-warehouse-get-started-connect.md) 作為開發環境及 [SQL Server Native Client 10/11 (ODBC)](https://msdn.microsoft.com/library/ms131415.aspx) 來連接用戶端。

若要深入了解，請參閱我們 [[連接](sql-data-warehouse-get-started-connect.md)] 頁面。

## 查詢效能
SQL 資料倉儲會使用一般的 SQL Server 建構來執行查詢 (含統計資料)。[統計資料](sql-data-warehouse-develop-statistics.md)是包含資料庫資料行中，值之範圍與頻率的相資訊的物件。查詢引擎會使用這些統計資料最佳化查詢執行，以及改善查詢效能。您可以使用下列查詢，判斷您的上次更新了哪些統計資料。

```
SELECT
	sm.[name]								    AS [schema_name],
	tb.[name]								    AS [table_name],
	co.[name]									AS [stats_column_name],
	st.[name]									AS [stats_name],
	STATS_DATE(st.[object_id],st.[stats_id])	AS [stats_last_updated_date]
FROM
	sys.objects				AS ob
	JOIN sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
	JOIN sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND	st.[object_id]		= sc.[object_id]
	JOIN sys.columns		AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
	JOIN sys.types           AS ty	ON	co.[user_type_id]	= ty.[user_type_id]
	JOIN sys.tables          AS tb	ON	co.[object_id]		= tb.[object_id]
	JOIN sys.schemas         AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE
	1=1
	AND st.[user_created] = 1;
```

若要深入了解，請參閱我們 [[統計資料](sql-data-warehouse-develop-statistics.md)] 頁面。

## 關鍵效能概念

請參閱下列文章，以瞭解一些關鍵效能與調整的其他概念：

- [效能和延展性][]
- [並行模型][]
- [設計資料表][]
- [為資料表選擇雜湊散發索引鍵][]
- [用來改善效能的統計資料][]

## 後續步驟
請參閱[開發概觀][]文章，以取得有關建置 SQL 資料倉儲解決方案的一些指導。

<!--Image references-->

<!--Article references-->

[效能和延展性]: sql-data-warehouse-performance-scale.md
[並行模型]: sql-data-warehouse-develop-concurrency.md
[設計資料表]: sql-data-warehouse-develop-table-design.md
[為資料表選擇雜湊散發索引鍵]: sql-data-warehouse-develop-hash-distribution-key
[用來改善效能的統計資料]: sql-data-warehouse-develop-statistics.md
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=AcomDC_0309_2016-->