<properties
   pageTitle="SQL 資料倉儲中的 PolyBase 教學課程 | Microsoft Azure"
   description="瞭解 PolyBase 是什麼及如何用於資料倉儲案例。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="sahajs;barbkess"/>


# 使用 PolyBase 載入資料
PolyBase 技術可讓您查詢和聯結多個來源的資料，且完全使用 Transact-SQL 命令。

使用 PolyBase 時，您可以執行下列步驟來查詢儲存在 Azure blob 儲存體中的資料，再將資料載入 SQL 資料倉儲資料庫：

- 建立資料庫主要金鑰和認證。
- 建立 PolyBase 物件：外部資料來源、外部檔案格式和外部資料表。 
- 查詢儲存在 Azure blob 儲存體中的資料。
- 從 Azure blob 儲存體將資料載入 SQL 資料倉儲。


## 必要條件
若要逐步執行本教學課程，您需要：

- Azure 儲存體帳戶
- 在 Azure blob 儲存體中儲存為分隔符號文字檔的資料

首先，您將建立當 PolyBase 連接到 Azure blob 儲存體和查詢資料時所需要的物件。

## 建立資料庫主要金鑰
連接到伺服器上的使用者資料庫來建立資料庫主要金鑰。這個金鑰在下一步用來加密認證密碼。

```
-- Creating master key
CREATE MASTER KEY;
```

參考主題：[CREATE MASTER KEY (Transact-SQL)][]。

## 建立資料庫範圍認證
若要存取 Azure blob 儲存體，您需要建立資料庫範圍認證，其中儲存 Azure 儲存體帳戶的驗證資訊。連接到資料倉儲資料庫，並為您想要存取的每個 Azure 儲存體帳戶建立資料庫範圍認證。指定身分識別名稱和 Azure 儲存體帳戶金鑰做為密碼。身分識別名稱並不會影響到 Azure 儲存體的驗證。

若要查看資料庫範圍的認證是否已存在，請使用 sys.database\_credentials，而不是只顯示伺服器認證的 sys.credentials。

```
-- Check for existing database-scoped credentials.
SELECT * FROM sys.database_credentials;

-- Create a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret 
WITH IDENTITY = 'joe'
,    Secret = '<azure_storage_account_key>'
;
```

參考主題：[CREATE CREDENTIAL (Transact-SQL)][]。

若要卸除資料庫範圍認證，您只需要使用下列語法：

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret
;
```

參考主題：[DROP CREDENTIAL (Transact-SQL)][]。

## 建立外部資料來源
外部資料來源是儲存 Azure blob 儲存體資料位置及存取資訊的資料庫物件。對於您想要存取的每個 Azure 儲存體容器，您需要定義外部資料來源。

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH
(
    TYPE = HADOOP
,   LOCATION ='wasbs://mycontainer@test.blob.core.windows.net'
,   CREDENTIAL = ASBSecret
)
;
```

參考主題：[CREATE EXTERNAL DATA SOURCE (Transact-SQL)][]。

若要卸除外部資料來源，其語法如下：

```
-- Dropping external data source
DROP EXTERNAL DATA SOURCE azure_storage
;
```

參考主題：[卸除外部資料來源 (Transact-SQL)][]。

## 建立外部檔案格式
外部檔案格式是指定外部資料格式的資料庫物件。在此範例中，我們在文字檔中有未壓縮的資料，且欄位以縱線字元 ('|') 分隔。

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(   
    FORMAT_TYPE = DELIMITEDTEXT 
,	FORMAT_OPTIONS  (
                        FIELD_TERMINATOR ='|'
                    ,   USE_TYPE_DEFAULT = TRUE
                    )
)
;
```

PolyBase 可以處理壓縮和未壓縮的資料，包括分隔的文字、Hive RCFILE 和 HIVE ORC 格式。

參考主題：[CREATE EXTERNAL FILE FORMAT (Transact-SQL)][]。

若要卸除外部檔案格式，其語法如下：

```
-- Dropping external file format
DROP EXTERNAL FILE FORMAT text_file_format
;
```
參考主題：[卸除外部檔案格式 (Transact-SQL)][]。

## 建立外部資料表

外部資料表定義類似於關聯式資料表定義。主要的差異在於資料的位置和格式。外部資料表定義儲存在 SQL 資料倉儲資料庫。資料儲存在資料來源所指定的位置。

LOCATION 選項指定從資料來源根目錄到資料的路徑。在此範例中，資料位於 'wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/'。相同資料表的所有檔案都必須在 Azure BLOB 中相同的邏輯資料夾底下。

您也可以選擇指定拒絕選項 (REJECT\_TYPE、REJECT\_VALUE、REJECT\_SAMPLE\_VALUE)，以決定 PolyBase 如何處理從外部資料來源收到的錯誤記錄。

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] 
(
     [SensorKey]     int    NOT NULL 
,    [CustomerKey]   int    NOT NULL 
,    [GeographyKey]  int        NULL 
,    [Speed]         float  NOT NULL 
,    [YearMeasured]  int    NOT NULL
)
WITH 
(
    LOCATION    = '/Demo/'
,   DATA_SOURCE = azure_storage
,   FILE_FORMAT = text_file_format      
)
;
```

> [AZURE.NOTE]請注意，您目前無法在外部資料表上建立統計資料。

參考主題：[CREATE EXTERNAL TABLE (Transact-SQL)][]。

您剛才建立的物件會儲存在 SQL 資料倉儲資料庫。您可以在 SQL Server Data Tools (SSDT) 物件總管中檢視它們。

若要卸除的外部資料表，您需要使用下列語法：

```
--Dropping external table
DROP EXTERNAL TABLE [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE]卸除外部資料表時，您必須使用 `DROP EXTERNAL TABLE`。您**不能**使用 `DROP TABLE`。

參考主題：[卸除外部資料表 (Transact-SQL)][]。

另外值得注意的是，外部資料表會同時顯示在 `sys.tables` 以及更明顯的 `sys.external_tables` 目錄檢視中。

## 替換儲存體金鑰

有時您基於安全性考量，會想要變更存取金鑰至您的 blob 儲存體。

要執行這項工作的最佳方式是遵循稱為「替換金鑰」的程序。您可能已經注意到您在 blob 儲存體帳戶有兩個儲存體金鑰。如此一來您就可以轉換

替換 Azure 儲存體帳戶金鑰的程序只需要簡單的三個步驟

1. 建立以次要儲存體存取金鑰為基礎的第二個資料庫範圍認證
2. 建立以新的認證為基礎的第二個外部資料來源
3. 卸除並建立指向新的外部資料來源的外部資料表

當您移轉所有的外部資料表到新的外部資料來源時，您就可以執行清除工作：
 
1. 卸除第一個外部資料來源
2. 卸除以主要儲存體存取金鑰為基礎的第一個資料庫範圍認證
3. 登入 Azure 並重新產生主要存取金鑰供下一次使用

## 查詢 Azure blob 儲存體資料
針對外部資料表的查詢只使用資料表名稱，如同關聯式資料表一樣。


```

-- Query Azure storage resident data via external table. 
SELECT * FROM [ext].[CarSensor_Data]
;

```

> [AZURE.NOTE]外部資料表上的查詢可能會失敗，並顯示「查詢已中止 -- 從外部來源讀取時已達最大拒絕閾值」錯誤。這表示您的外部資料包含「錯誤」記錄。如果實際的資料類型/資料行數目不符合外部資料表的資料行定義，或資料不符合指定的外部檔案格式，則會將資料記錄視為「錯誤」。若要修正此問題，請確定您的外部資料表及外部檔案格式定義皆正確，且這些定義與您的外部資料相符。萬一外部資料記錄的子集有錯誤，您可以使用 CREATE EXTERNAL TABLE DDL 中的拒絕選項，選擇拒絕這些查詢記錄。


## 從 Azure blob 儲存體載入資料
此範例將 Azure blob 儲存體中的資料載入至 SQL 資料倉儲資料庫。

直接儲存資料可免除查詢時的資料傳輸時間。搭配 columnstore 索引儲存資料可讓分析查詢的查詢效能提升 10 倍。

此範例使用 CREATE TABLE AS SELECT 陳述式來載入資料。新的資料表繼承查詢中指名的資料行。它會從外部資料表定義繼承這些資料行的資料型別。

CREATE TABLE AS SELECT 是效能很高的 Transact-SQL 陳述式，可替代 INSERT...SELECT。它原本是針對分析平台系統中的大量平行處理 (MPP) 引擎所開發，現在已納入 SQL 資料倉儲中。

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX
,	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS 
SELECT * 
FROM   [ext].[CarSensor_Data]
;
```

請參閱 [CREATE TABLE AS SELECT (Transact-SQL)][]。


## 解決 PolyBase UTF-8 需求
現在 PolyBase 支援載入以 UTF-8 編碼的資料檔案。因為 UTF-8 使用與 ASCII 相同的字元編碼，PolyBase 也支援載入 ASCII 編碼的資料。不過，PolyBase 不支援其他字元編碼，例如 UTF-16 / Unicode 或延伸的 ASCII 字元。延伸的 ASCII 包括具有重音符號的字元，例如德文常見的母音變化。

若要解決這項需求的最佳解答是重新撰寫為 UTF-8 編碼。

有數種方法能完成這項操作：以下是使用 Powershell 的兩種方法：

### 簡單的小檔案範例

以下是一行會建立檔案的簡單 Powershell 指令碼。
 
```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

不過，儘管將資料重新編碼的方法非常簡單，但絕非最有效率的作法。下列的 io 資料流範例快得多很多，並可達到相同的結果。

### 較大檔案的 IO 資料流處理範例

下列程式碼範例更為複雜，但在資料流處理來自來源的資料至目標的資料列時更具效率。應用此方法在較大的檔案上。

```
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

## 後續步驟
如需更多開發祕訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load with PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx

[卸除外部資料來源 (Transact-SQL)]: https://msdn.microsoft.com/zh-TW/library/mt146367.aspx
[卸除外部檔案格式 (Transact-SQL)]: https://msdn.microsoft.com/zh-TW/library/mt146379.aspx
[卸除外部資料表 (Transact-SQL)]: https://msdn.microsoft.com/zh-TW/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/zh-TW/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/zh-TW/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/zh-TW/library/ms189450.aspx

<!---HONumber=Oct15_HO1-->