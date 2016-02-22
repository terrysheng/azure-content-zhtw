<properties
   pageTitle="SQL 資料倉儲容量規格 | Microsoft Azure"
   description="SQL 資料倉儲的容量規格下限和上限。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/10/2016"
   ms.author="barbkess;jrj;sonyama"/>

# SQL 資料倉儲容量規格

SQL 資料倉儲在查詢處理和系統檢視表方面有所限制，非常類似於 SQL Server。有些限制會因為平行和可調整的基礎架構而不同。以下是 SQL 資料倉儲的限制摘要。

## 連線

| 類別 | 說明 | 最大值 |
| :---------------- | :------------------------------------------- | :----------------- |
| 工作階段 | 同時開啟的工作階段 | 1024<br/><br/>我們支援 1024 個可提交並行要求至資料倉儲的並行連線。請注意，可同時執行的查詢數目有所限制。超過限制時，要求會進入內部佇列等待處理。|
| 工作階段 | 準備陳述式的最大記憶體 | 20 MB |
| 登入 | 每一部伺服器的登入數目。 | 500,000 |


## 查詢處理

| 類別 | 說明 | 最大值 |
| :---------------- | :------------------------------------------- | :----------------- |
| 查詢 | 使用者資料表上的並行查詢。 | 32<br/><br/>額外的查詢將會移至內部佇列等候處理。無論同一時間執行的查詢數目，每個查詢已最佳化來充分利用大規模平行處理架構。|
| 查詢 | 使用者資料表上已排入佇列的查詢。 | 1000 |
| 查詢 | 系統檢視表上的並行查詢。 | 100 |
| 查詢 | 系統檢視表上已排入佇列的查詢 | 1000 |
| 查詢 | 參數個數上限 | 2098 |
| 批次 | 大小上限 | 65,536*4096 |


## 資料定義語言 (DDL)

| 類別 | 說明 | 最大值 |
| :---------------- | :------------------------------------------- | :----------------- |
| 資料表 | 每個資料庫的資料表 | 20 億 |
| 資料表 | 每個資料表的資料行 | 1024 個資料行 |
| 資料表 | 每個資料行的位元組 | 8000 個位元組 |
| 資料表 | 每個資料列的位元組，已定義的大小 | 8060 個位元組<br/><br/>每個資料列的位元組數目計算方式同於已啟用頁面壓縮的 SQL Server。不得超過 8060 個位元組。若要估計資料列的大小，請參閱中 MSDN 的[估計叢集索引的大小](https://msdn.microsoft.com/library/ms178085.aspx)中的資料列大小計算。<br/><br/>如需 SQL 資料倉儲資料類型大小的清單，請參閱 [CREATE TABLE (Azure SQL 資料倉儲)](https://msdn.microsoft.com/library/mt203953.aspx)。 |
| 資料表 | 每個資料列的位元組，移動資料時的內部緩衝區大小 | 32,768<br/><br/>在分散式 SQL 資料倉儲系統內，SQL 資料倉儲使用內部緩衝區來移動資料列。負責移動資料列的服務稱為資料移動服務 (DMS)，它會以不同於 SQL Server 的格式儲存資料列。<br/><br/>如果資料列無法填入內部緩衝區，就會發生查詢編譯錯誤或內部資料移動錯誤。若要避免這個問題，請參閱 [DMS 緩衝區大小的詳細資料](#details-about-the-dms-buffer-size)。|
| 資料表 | 每個資料表的資料分割 | 15,000<br/><br/>為了獲得高效能，建議在能夠支援業務需求的情況下，將您需要的資料分割數目降至最低。資料分割數目增加時，DDL 和 DML 作業的負荷會加重，導致效能變慢。|
| 資料表 | 每個資料分割界限值的字元。| 4000 |
| 索引 | 每個資料表的非叢集索引。 | 999<br/><br/>僅適用於資料列存放區資料表。|
| 索引 | 每個資料表的叢集索引。 | 1<br><br/>適用於資料列存放區資料表和資料行存放區資料表。|
| 索引 | 資料行存放區索引資料列群組中的資料列 | 1024<br/><br/>每個資料行存放區索引都實作為多個資料行存放區索引。請注意，如果您將 1024 個資料列插入 SQL 資料倉儲的資料行存放區索引，資料列不會全部移至相同的資料列群組。|
| 索引 | 叢集資料行存放區索引的並行組建。 | 32<br/><br/>適用於叢集資料行存放區索引全部都在不同資料表上建置時。每個資料表只允許有 1 個叢集資料行存放區索引建置。其他要求會在佇列中等候。|
| 索引 | 索引鍵的大小。 | 900 個位元組。<br/><br/>僅適用於資料列存放區索引。<br/><br/>varchar 資料行上可以建立大小上限超過 900 個位元組的索引，只是在建立索引時，資料行中現有的資料不能超過 900 個位元組。不過，後續在資料行上執行 INSERT 或 UPDATE 動作時，如果總計大小超過 900 個位元組，將會失敗。|
| 索引 | 每個索引的索引鍵資料行。 | 16<br/><br/>僅適用於資料列存放區索引。叢集資料行存放區索引包含所有資料行。|
| 統計資料 | 結合資料行值的大小。 | 900 個位元組。 |
| 統計資料 | 每個統計資料物件的資料行。 | 32 |
| 統計資料 | 每個資料表的資料行上建立的統計資料。 | 30,000 |
| 預存程序 | 最大巢狀層級。 | 8 |
| 檢視 | 每個檢視表的資料行 | 1,024 |


## 資料操作語言 (DML)

| 類別 | 說明 | 最大值 |
| :---------------- | :------------------------------------------- | :----------------- |
| SELECT 結果 | 每個資料列的資料行 | 4096<br/><br/>在 SELECT 結果中，每個資料列絕對不能超過 4096 個資料行。不保證一定可以有 4096 個。如果查詢計畫需要暫存資料表，可能會限定每個資料表最多 1024 個資料行。|
| SELECT 結果 | 每個資料列的位元組 | >8060<br/><br/>在 SELECT 結果中，每個資料列的位元組數目可能超過資料表資料列允許的 8060 個位元組上限。如果 SELECT 陳述式的查詢計畫需要暫存資料表，可能會限定 8060 個位元組的資料表上限。|
| SELECT 結果 | 每個資料行的位元組 | >8000<br/><br/>在 SELECT 結果中，每個資料行的位元組數目可能超過資料表資料行允許的 8000 個位元組上限。如果 SELECT 陳述式的查詢計畫需要暫存資料表，可能會限定 8000 個位元組的資料表上限。|
| SELECT | 巢狀子查詢 | 32<br/><br/>SELECT 陳述式中一定不能超過 32 個巢狀子查詢。不保證一定可以有 32 個。例如，JOIN 可以將子查詢加入查詢計畫中。子查詢的數目也受限於可用記憶體。|
| SELECT | 每個 JOIN 的資料行 | 1024 個資料行<br/><br/>JOIN 中絕對不能超過 1024 個資料行。不保證一定可以有 1024 個。如果 JOIN 計畫需要比 JOIN 結果更多資料行的暫存資料表，暫存資料表會受限於 1024 的限制。 |
| SELECT | 每個 GROUP BY 資料行的位元組。 | 8060<br/><br/>GROUP BY 子句中的資料行最多可以有 8060 個位元組。|
| SELECT | 每個 ORDER BY 資料行的位元組 | 8060 個位元組。<br/><br/>ORDER BY 子句中的資料行最多可以有 8060 個位元組。|
| INSERT | 每個資料行的位元組，固定和變動寬度。 | 8000 個位元組。嘗試插入超過資料行定義的位元組數目會導致錯誤。|
| INSERT | 每個資料列的位元組，變動寬度資料行。 | >8060 個位元組。超過 8060 個位元組的資料會放在溢位存放區域。|
| UPDATE | 每個資料行的位元組，固定和變動寬度。 | 8000 個位元組。嘗試更新資料行的值時，如果需要超過資料行定義的位元組數目，將會導致錯誤。|
| 每個陳述式的識別項和常數 | 參考的識別項和常數個數。 | 65,535<br/><br/>SQL 資料倉儲限制單一查詢運算式中可包含的識別項和常數個數。此限制為 65,535。超過此數字會導致 SQL Server 錯誤 8632。如需詳細資訊，請參閱[內部錯誤：到達運算式服務的限制](http://support.microsoft.com/kb/913050/)。|

## 系統檢視表

| 系統檢視表 | 最大資料列數 |
| :--------------------------------- | :------------|
| sys.dm\_pdw\_component\_health\_alerts | 10,000 |
| sys.dm\_pdw\_dms\_cores | 100 |
| sys.dm\_pdw\_dms\_workers | 最近 1000 個 SQL 要求的 DMS 背景工作角色總數。 |
| sys.dm\_pdw\_dms\_worker\_pairs | 10,000 |
| sys.dm\_pdw\_errors | 10,000 |
| sys.dm\_pdw\_exec\_requests | 10,000 |
| sys.dm\_pdw\_exec\_sessions | 10,000 |
| sys.dm\_pdw\_request\_steps | 儲存在 sys.dm\_pdw\_exec\_requests 中的最近 1000 個 SQL 要求的步驟總數。 |
| sys.dm\_pdw\_os\_event\_logs | 10,000 |
| sys.dm\_pdw\_sql\_requests | 儲存在 sys.dm\_pdw\_exec\_requests 中的最近 1000 個 SQL 要求。 |


## DMS 緩衝區大小的詳細資料。

SQL 資料倉儲會使用內部緩衝區在後端計算節點之間移動資料列。負責移動資料列的服務稱為資料移動服務 (DMS)，它並使用不同於 SQL Server 的儲存格式。

為了改善平行查詢效能，DMS 會將所有可變長度資料填補到 SQL 資料庫定義的大小上限。例如，`nvarchar(2000) NOT NULL` 的值 'hello' 實際上會使用 DMS 緩衝區的 4002 個位元組。2000 個字元的每個字元使用 2 個位元組，再加上 NULL 結束字元的 2 個位元組。

> [AZURE.NOTE] 當 DMS 嘗試移動的資料列超過 32,768 個位元組的 DMS 緩衝區大小時，將會發生內部錯誤。如果您的資料列大小超過 DMS 緩衝區大小，您必須修改資料表定義，使資料列可放入 DMS 緩衝區。

### 如何決定 DMS 緩衝區的資料列大小
此範例說明 DMS 定義的可變長度資料大小，並顯示如何計算資料列的 DMS 緩衝區大小。
 
在 DMS 緩衝區，可變長度的資料大小是下列各項的總和：

- 定義的字元大小。
- NULL 類型使用 8 個位元組作為 NULL 指標。
- ASCII 類型使用 1 個位元組作為 NULL 結束字元。
- Unicode 類型使用 2 個位元組作為 NULL 結束字元。
             
| 資料類型 | DMS 緩衝區大小 |
| :---------------------- | :-------------------------- |                
| char(1000) NULL | 1009 個位元組 = 1000 +8 + 1 |
| char(1000) NOT NULL | 1001 個位元組 = 1000 + 1 |
| nchar(1000 NULL | 2010 個位元組 = 2000 + 8 + 2 |
| varchar(1000) NULL | 1009 個位元組 = 1000 + 8 + 1 |
| varchar(1000) NOT NULL | 1009 個位元組 = 1000 + 1 |
| nvarchar(1000) NULL | 2010 個位元組 = 2000 + 8 + 2 |
| nvarchar(1000) NOT NULL | 2010 個位元組 = 2000 + 2 |
                
在 DMS 緩衝區，固定寬度資料行使用 SQL Server 原生大小。如果類型是可為 Null，DMS 需要額外的 8 個位元組。關於 SQL Server 大小，請參閱 sys.types 中的 max\_length 欄位。

例如：

| 固定寬度資料類型 | DMS 緩衝區大小 |
| :-------------------- | :----------------- |
| int NULL | 12 個位元組 = 4 + 8 |
| int NOT NULL | 4 個位元組 = 4 + 0 | 
                
下列資料列的 DMS 定義緩衝區大小合計為 31,134 位元組，可放入 DMS 緩衝區。

| 資料欄 | 資料類型 | 資料行大小 |
| :----- | :------------------ | :------------------------ |
| col1 | datetime2 (7) NULL | 20 個位元組 = 8 + 8 |
| col2 | float (4) NULL | 12 個位元組 = 4 + 8 |
| col3 | nchar (6) NULL | 22 個位元組 = 12 + 8 + 2 |
| col4 | char (7000) NULL | 7009 個位元組 = 7000 + 8 + 1 |
| col5 | nvarchar (4000) | 8002 個位元組 = 8000 + 2. |
| col6 | varchar (8000) NULL | 8009 個位元組 = 8000 + 8 + 1 |
| col7 | varbinary (8000) | 8000 個位元組 |
| col8 | binary (60) | 60 個位元組 |
                
              
### 超過 DMS 緩衝區大小的範例。

這個範例示範如何成功地將資料列插入 SQL 資料倉儲，但之後當 DMS 因為散發不相容聯結而需要移動資料列時，就會導致 DMS 溢位錯誤。該記取的經驗教訓是建立可放入 DMS 緩衝區的較小資料列。

在下列範例中，我們會建立資料表 T1。當所有 nvarchar 變數都有 4000 個 Unicode 字元時，最大可能的資料列大小會超過 40,000 個位元組，這已超過 DMS 緩衝區大小上限。

因為 nvarchar 實際定義的大小使用 26 個位元組，而資料列定義小於 8060 個位元組，所以可放在 SQL Server 頁面上。因此，即使當 DMS 嘗試將這個資料列載入至 DMS 緩衝區時失敗，CREATE TABLE 陳述式仍會成功。

````
CREATE TABLE T1
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (c0) )
;
````
下一個步驟示範我們可以成功地使用 INSERT 將資料插入資料表。此陳述式不使用 DMS，而是直接將資料載入至 SQL Server，因此不會引發 DMS 緩衝區溢位失敗。整合服務也會成功載入這個資料列。</para>

````
--The INSERT operation succeeds because the row is inserted directly into SQL Server without requiring DMS to buffer the row.
INSERT INTO T1
VALUES (
    25,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
````

為了準備展示資料移動，此範例會建立第二個資料表，並以 CustomerKey 作為散發資料行。

````
--This second table is distributed on CustomerKey. 
CREATE TABLE T2
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (CustomerKey) )
;

INSERT INTO T2
VALUES (
    32,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
````
由於這兩個資料表不是根據 CustomerKey 散發，T1 和 T2 之間根據 CustomerKey 的聯結是散發不相容。DMS 必須載入至少一個資料列，並將它複製到不同的散發。

````
SELECT * FROM T1 JOIN T2 ON T1.CustomerKey = T2.CustomerKey;
````

如同預期，DMS 無法執行該聯結，因為當填補 nvarchar 資料行時，資料列會超過 32,768 個位元組的 DMS 緩衝區大小。將會出現下列錯誤訊息。

````
Msg 110802, Level 16, State 1, Line 126

An internal DMS error occurred that caused this operation to fail. Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Workers.DmsSqlNativeException, Message: SqlNativeBufferReader.ReadBuffer, error in OdbcReadBuffer: SqlState: , NativeError: 0, 'COdbcReadConnection::ReadBuffer: not enough buffer space for one row | Error calling: pReadConn-&gt;ReadBuffer(pBuffer, bufferOffset, bufferLength, pBytesRead, pRowsRead) | state: FFFF, number: 81, active connections: 8', Connection String: Driver={SQL Server Native Client 11.0};APP=DmsNativeReader:P13521-CMP02\sqldwdms (4556) - ODBC;Trusted_Connection=yes;AutoTranslate=no;Server=P13521-SQLCMP02,1500
````

## 後續步驟
如需更多的參考資訊，請參閱 [SQL 資料倉儲參考概觀][]。

<!--Image references-->

<!--Article references-->
[SQL 資料倉儲參考概觀]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!---HONumber=AcomDC_0211_2016-->