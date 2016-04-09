<properties
   pageTitle="在 SQL 資料倉儲中使用 PolyBase 的指南 | Microsoft Azure"
   description="在 SQL 資料倉儲案例中使用 PolyBase 的指導方針和建議。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="sahajs;barbkess;sonyama"/>


# 在 SQL 資料倉儲中使用 PolyBase 的指南

本指南提供在 SQL 資料倉儲中使用 PolyBase 的實用資訊。

如要開始使用，請參閱[使用 PolyBase 載入資料][]教學課程。


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

> [AZURE.NOTE] 外部資料表上的查詢可能會失敗，並顯示「*查詢已中止 -- 從外部來源讀取時已達最大拒絕閾值*」錯誤。這表示您的外部資料包含「*錯誤*」記錄。如果實際的資料類型/資料行數目不符合外部資料表的資料行定義，或資料不符合指定的外部檔案格式，則會將資料記錄視為「錯誤」。若要修正此問題，請確定您的外部資料表及外部檔案格式定義皆正確，且這些定義與您的外部資料相符。萬一外部資料記錄的子集有錯誤，您可以使用 CREATE EXTERNAL TABLE DDL 中的拒絕選項，選擇拒絕這些查詢記錄。


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

Azure 資料倉儲尚未支援自動建立或自動更新統計資料。為了獲得查詢的最佳效能，在首次載入資料，或是資料中發生重大變更之後，建立所有資料表的所有資料行統計資料非常重要。如需統計資料的詳細說明，請參閱主題群組＜開發＞之中的[統計資料][]主題。以下是快速範例，說明如何在此範例中建立載入資料表的統計資料。

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
若要瞭解將資料移到 SQL 資料倉儲的詳細資訊，請參閱[資料移轉概觀][]。

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[使用 PolyBase 載入資料]: sql-data-warehouse-get-started-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[development overview]: sql-data-warehouse-overview-develop.md
[統計資料]: sql-data-warehouse-develop-statistics.md
[資料移轉概觀]: sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!---HONumber=AcomDC_0309_2016-->