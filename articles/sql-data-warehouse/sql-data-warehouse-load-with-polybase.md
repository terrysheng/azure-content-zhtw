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
   ms.date="11/04/2015"
   ms.author="sahajs;barbkess"/>


# 使用 PolyBase 載入資料
PolyBase 技術可讓您查詢和聯結多個來源的資料，且完全使用 Transact-SQL 命令。

使用 PolyBase 時，您可以執行下列步驟來查詢儲存在 Azure blob 儲存體中的資料，再將資料載入 SQL 資料倉儲資料庫：

- 建立資料庫主要金鑰和認證。
- 建立 PolyBase 物件：外部資料來源、外部檔案格式和外部資料表。 
- 查詢儲存在 Azure blob 儲存體中的資料。
- 從 Azure blob 儲存體將資料載入 SQL 資料倉儲。
- 將資料從 SQL 資料倉儲匯出至 Azure blob 儲存體。


## 必要條件
若要逐步執行本教學課程，您需要：

- Azure 儲存體帳戶
- 在 Azure blob 儲存體中儲存為分隔符號文字檔的資料

首先，您將建立當 PolyBase 連接到 Azure blob 儲存體和查詢資料時所需要的物件。

> [AZURE.IMPORTANT]PolyBase 支援的 Azure 儲存體帳戶類型為︰
> 
> + 標準本機備援儲存體 (標準-LRS)
> + 標準異地備援儲存體 (標準-GRS)
> + 標準讀取權限異地備援儲存體 (標準-RAGRS)
>
> PolyBase 「不」支援標準區域備援儲存體 (標準-ZRS) 及進階本機備援儲存體 (進階-LRS) 帳戶類型。如果您要建立新的 Azure 儲存體帳戶，請確定您從定價層選取 PolyBase 支援的儲存體帳戶類型。

## 步驟 1： 將認證儲存在您的資料庫中
若要存取 Azure blob 儲存體，您需要建立資料庫範圍認證，其中儲存 Azure 儲存體帳戶的驗證資訊。請依照下列步驟儲存與您資料庫搭配使用的認證。

1. 連線到您的 SQL 資料倉儲資料庫。
2. 使用 [CREATE MASTER KEY (Transact-SQL)][] 為您的資料庫建立主要金鑰。如果您的資料庫已經主要金鑰，您就不需要建立另一個主要金鑰。在下一個步驟中，這個金鑰會用來為您的認證「密碼」加密。

    ```
    -- Create a E master key
    CREATE MASTER KEY;
    ```

1. 檢查看看您是否已經有任何資料庫認證。方法是使用 sys.database\_credentials 系統檢視，而不是使用只會顯示伺服器認證的 sys.credentials。

    ``` -- Check for existing database-scoped credentials.SELECT * FROM sys.database\_credentials;

3. 使用 [CREATE CREDENTIAL (Transact-SQL)][] 為您想要存取的每個 Azure 儲存體帳戶建立資料庫範圍認證。在此範例中，IDENTITY 是易記的認證名稱。它不會影響對 Azure 儲存體的驗證。SECRET 是您的 Azure 儲存體帳戶金鑰。

    -- 建立資料庫範圍認證 CREATE DATABASE SCOPED CREDENTIAL ASBSecret WITH IDENTITY = 'joe' , Secret = '<azure_storage_account_key>' ; ```

1. 如果您需要卸除某個資料庫範圍認證，請使用 [DROP CREDENTIAL (Transact-SQL)][]：

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret
;
```

## 步驟 2：建立外部資料來源
外部資料來源是儲存 Azure blob 儲存體資料位置及存取資訊的資料庫物件。請使用 [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][] 為您想要存取的每個 Azure 儲存體 Blob 定義外部資料來源。

    ```
    -- Create an external data source for an Azure storage blob
    CREATE EXTERNAL DATA SOURCE azure_storage 
    WITH
    (
        TYPE = HADOOP,
        LOCATION ='wasbs://mycontainer@test.blob.core.windows.net',
        CREDENTIAL = ASBSecret
    )
    ;
    ```

如果您需要卸除外部資料表，請使用 [DROP EXTERNAL DATA SOURCE][]：

    ```
    -- Drop an external data source
    DROP EXTERNAL DATA SOURCE azure_storage
    ;
    ```

## 步驟 3：建立外部檔案格式
外部檔案格式是指定外部資料格式的資料庫物件。PolyBase 可以處理壓縮和未壓縮的資料，包括分隔的文字、Hive RCFILE 和 HIVE ORC 格式。

請使用 [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][] 來建立外部檔案格式。此範例指定檔案中的資料為未壓縮的文字，而欄位是以縱線字元 ('|') 分隔。

```
-- Create an external file format for a text-delimited file.
-- Data is uncompressed and fields are separated with the
-- pipe character.
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(   
    FORMAT_TYPE = DELIMITEDTEXT, 
    FORMAT_OPTIONS  
    (
        FIELD_TERMINATOR ='|',
        USE_TYPE_DEFAULT = TRUE
    )
)
;
```

如果您需要卸除某個外部檔案格式，請使用 [DROP EXTERNAL FILE FORMAT]。

```
-- Dropping external file format
DROP EXTERNAL FILE FORMAT text_file_format
;
```

## 建立外部資料表

外部資料表定義類似於關聯式資料表定義。主要的差異在於資料的位置和格式。

- 外部資料表定義會以中繼資料的形式儲存在 SQL 資料倉儲資料庫中。 
- 資料會儲存在資料來源所指定的外部位置。

請使用 [CREATE EXTERNAL TABLE (Transact-SQL)][] 來定義外部資料表。

LOCATION 選項指定從資料來源根目錄到資料的路徑。在此範例中，資料是位於 'wasbs://mycontainer@test.blob.core.windows.net/path/Demo/'。同一個資料表所用的所有檔案，都必須位於 Azure Blob 儲存體中相同的邏輯資料夾底下。

您也可以選擇指定拒絕選項 (REJECT\_TYPE、REJECT\_VALUE、REJECT\_SAMPLE\_VALUE)，以決定 PolyBase 如何處理從外部資料來源收到的錯誤記錄。

```
-- Creating an external table for data in Azure blob storage.
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] 
(
     [SensorKey]     int    NOT NULL,
     [CustomerKey]   int    NOT NULL,
     [GeographyKey]  int        NULL,
     [Speed]         float  NOT NULL,
     [YearMeasured]  int    NOT NULL,
)
WITH 
(
    LOCATION    = '/Demo/',
    DATA_SOURCE = azure_storage,
    FILE_FORMAT = text_file_format      
)
;
```

您剛才建立的物件會儲存在 SQL 資料倉儲資料庫。您可以在 SQL Server Data Tools (SSDT) 物件總管中檢視它們。

若要卸除的外部資料表，您需要使用下列語法：

```
--Dropping external table
DROP EXTERNAL TABLE [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE]卸除外部資料表時，您必須使用 `DROP EXTERNAL TABLE`。您「不能」使用 `DROP TABLE`。

參考主題：[DROP EXTERNAL TABLE (Transact-SQL)][]。

另外值得注意的是，在 `sys.tables` 和 `sys.external_tables` 目錄檢視中 (後者更為明確) 都會顯示外部資料表。

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

> [AZURE.NOTE]針對外部資料表的查詢可能會失敗，並顯示「查詢已中止 -- 從外部來源讀取時已達最大拒絕閾值」錯誤訊息。這表示您的外部資料包含「錯誤」記錄。如果實際的資料類型/資料行數目不符合外部資料表的資料行定義，或資料不符合指定的外部檔案格式，則會將資料記錄視為「錯誤」。若要修正此問題，請確定您的外部資料表及外部檔案格式定義皆正確，且這些定義與您的外部資料相符。萬一外部資料記錄的子集有錯誤，您可以使用 CREATE EXTERNAL TABLE DDL 中的拒絕選項，選擇拒絕這些查詢記錄。


## 從 Azure blob 儲存體載入資料
此範例將 Azure blob 儲存體中的資料載入至 SQL 資料倉儲資料庫。

直接儲存資料可免除查詢時的資料傳輸時間。搭配 columnstore 索引儲存資料可讓分析查詢的查詢效能提升 10 倍。

此範例使用 CREATE TABLE AS SELECT 陳述式來載入資料。新的資料表繼承查詢中指名的資料行。它會從外部資料表定義繼承這些資料行的資料型別。

CREATE TABLE AS SELECT 是高效能 TRANSACT-SQL 陳述式，可將資料平行載入到您的 SQL 資料倉儲的所有計算節點。它原本是針對分析平台系統中的大量平行處理 (MPP) 引擎所開發，現在已納入 SQL 資料倉儲中。

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

## 建立新載入資料的統計資料

Azure 資料倉儲尚未支援自動建立或自動更新統計資料。為了獲得查詢的最佳效能，在首次載入資料，或是資料中發生重大變更之後，建立所有資料表的所有資料行統計資料非常重要。如需統計資料的詳細說明，請參閱「開發」主題群組中的「[統計資料][]」主題。以下是快速範例，說明如何在此範例中建立載入資料表的統計資料。

```
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## 將資料匯出至 Azure Blob 儲存體
這一節說明如何將資料從 SQL 資料倉儲匯出至 Azure Blob 儲存體。此範例使用 CREATE EXTERNAL TABLE AS SELECT (高效能 Transact-SQL 陳述式) 將資料從所有計算節點平行匯出。

下列範例會使用 dbo.Weblogs 資料表中的資料行定義和資料從 dbo 建立外部資料表 Weblogs2014。外部資料表定義會儲存在 SQL 資料倉儲中，而 SELECT 陳述式的結果會匯出至資料來源所指定的 blob 容器下的 "/archive/log2014/" 目錄。以指定的文字檔案格式匯出的資料。

```
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```


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
[統計資料]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-------HONumber=AcomDC_1210_2015--->