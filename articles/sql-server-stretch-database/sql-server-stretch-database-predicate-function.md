<properties
	pageTitle="使用篩選述詞選取要移轉的資料列 (Stretch Database) | Microsoft Azure"
	description="了解如何使用篩選述詞選取要移轉的資料列。"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# 使用篩選述詞選取要移轉的資料列 (Stretch Database)

如果您將歷程資料儲存在個別的資料表上，您可以設定 Stretch Database 以移轉整個資料表。相反地，如果您的資料表同時包含歷程及目前的資料，您可以指定篩選述詞以選取要移轉的資料列。篩選述詞必須呼叫嵌入資料表值函式。本主題說明如何撰寫嵌入資料表值函式以選取要移轉的資料列。

在 CTP 3.1 一直到 RC1 中，指定述詞的選項已不再於 [啟用 Stretch 資料庫精靈] 中提供。您必須使用 ALTER TABLE 陳述式以利用此選項設定 Stretch Database。如需詳細資訊，請參閱 [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)。

如果您不指定篩選述詞，便會移轉整個資料表。

    > If you provide a filter predicate that performs poorly, data migration also performs poorly. Stretch Database applies the filter predicate to the table by using the CROSS APPLY operator.

## 嵌入資料表值函式的基本需求
Stretch Database 篩選函式嵌入資料表值函式所需的嵌入資料表值函式看起來如下列範例所示。

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE <predicate>
```
函式的參數必須是來自資料表的資料欄識別碼。

需要結構描述繫結，以避免篩選述詞使用的資料欄被卸除或改變。

### 傳回值
如果函式傳回非空白結果，則該資料列便符合移轉資格。否則，在函式沒有傳回任何資料列的情況下，便代表該資料列不符合移轉資格。

### 條件
&lt;*述詞*&gt; 可以包含一個條件，或是包含以 AND 邏輯運算子結合的數個條件。

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
每個條件皆可以包含一個基本條件，或是包含以 OR 邏輯運算子結合的數個基本條件。

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### 基本條件
基本條件可以執行下列其中一個比較。

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

-   比較函式參數及常數運算式。例如，`@column1 < 1000`。

    以下是檢查 [date] 資料欄的值是否為 &lt; 1/1/2016 的範例。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
    GO

    ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   將 IS NULL 或 IS NOT NULL 運算子套用至函式參數。

-   使用 IN 運算子來比較函式參數及常數值清單。

    以下是檢查 [shipment\_status] 資料欄的值是否為 `IN (N'Completed', N'Returned', N'Cancelled')` 的範例。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

### 比較運算子
支援下列比較運算子。

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### 常數運算式
您在篩選述詞中使用的常數可以式任何可在定義函式時評估之具決定性的運算式。常數運算式可以包含下列項目。

-   常值。例如，`N’abc’, 123`。

-   代數運算式。例如，`123 + 456`。

-   具決定性的函式。例如，`SQRT(900)`。

-   使用 CAST 或 CONVERT 之具決定性的轉換。例如，`CONVERT(datetime, '1/1/2016', 101)`。

### 其他運算式
在您將 BETWEEN 和 NOT BETWEEN 運算子取代為對等的 AND 和 OR 運算式之後，如果產生的述詞符合此處所述的規則，便代表您可以使用 BETWEEN 和 NOT BETWEEN 運算子。

您不能使用子查詢或不具決定性的函式，例如 RAND() 或 GETDATE()。

## 有效函式的範例

-   下列範例使用 AND 邏輯運算子結合兩個基本條件。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
      WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   下列範例使用數個條件和使用 CONVERT 之具決定性的轉換。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
    GO
    ```

-   下列範例使用數學運算子和函式。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < SQRT(400) + 10
    GO
    ```

-   下列範例使用 BETWEEN 和 NOT BETWEEN 運算子。此使用方法有效，因為在您將 BETWEEN 和 NOT BETWEEN 運算子取代為對等的 AND 和 OR 運算式之後，所產生的述詞符合此處所述的規則。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 BETWEEN 0 AND 100
    			AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
    GO
    ```
    在您將 BETWEEN 和 NOT BETWEEN 運算子取代為對等的 AND 和 OR 運算式之後，前面的函式與後面的函式相等。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
    GO
    ```

## 無效函式的範例

-   下列函式無效，因為它包含不具決定性的轉換。

    ```tsql
    CREATE FUNCTION dbo.fn_example5(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < CONVERT(datetime, '1/1/2016')
    GO
    ```

-   下列函式無效，因為它包含不具決定性的函式呼叫。

    ```tsql
    CREATE FUNCTION dbo.fn_example6(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < DATEADD(day, -60, GETDATE())
    GO
    ```

-   下列函式無效，因為它包含子查詢。

    ```tsql
    CREATE FUNCTION dbo.fn_example7(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
    GO
    ```

-   下列函式無效，因為使用代數運算子或內建函式的運算式必須在您定義函式時評估為常數。您無法包含以代數運算式或函式呼叫呈現的資料欄參考。

    ```tsql
    CREATE FUNCTION dbo.fn_example8(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 % 2 =  0
    GO

    CREATE FUNCTION dbo.fn_example9(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE SQRT(@column1) = 30
    GO
    ```

-   下列函式無效，因為它在您將 BETWEEN 運算子取代為對等的 AND 運算式之後，違反此處所述的規則。

    ```tsql
    CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```
    在您將 BETWEEN 運算子取代為對等的 AND 運算式之後，前面的函式與後面的函式相等。此含式無效，因為主要條件只能使用 OR 邏輯運算子。

    ```tsql
    CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```

## Stretch Database 套用篩選述詞的方法
Stretch Database 使用 CROSS APPLY 運算子將篩選述詞套用至資料表，並決定合格的資料列。例如：

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
如果函式針對資料列傳回非空白結果，便代表該資料列符合移轉資格。

## 將篩選式詞新增到資料表
透過執行 ALTER TABLE 陳述式並將現有的嵌入資料表值函式指定為 FILTER\_PREDICATE 參數的值，來將篩選述詞新增到資料表。例如：

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
	MIGRATION_STATE = <desired_migration_state>
) )
```
在您將函式做為述詞繫結至資料表之後，下列項目皆會成立。

-   發生下一次的資料移轉時，只有其函式傳回非空白值的資料列會被移轉。

-   函式所使用的資料欄皆為結構描述繫結。只要有資料表將該函式做為其篩選述詞使用，您便無法變更這些資料欄。

## 從資料表移除篩選述詞
若要移轉整個資料表，而非選取的資料列，請透過將現有的 FILTER\_PREDICATE 設定為 Null 來移除它。例如：

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = NULL,
	MIGRATION_STATE = <desired_migration_state>
) )
```
當您移除篩選述詞之後，資料表中的所有資料列皆符合移轉資格。

## 取代現有的篩選述詞
您可以透過再次執行 ALTER TABLE 陳述式並為 FILTER\_PREDICATE 參數指定新的值，來取代先前指定的篩選述詞。例如：

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
	MIGRATION_STATE = <desired_migration_state>
```
新的嵌入資料表值函式擁有下列需求。

-   新函式必須比先前函式含有較少限制。

-   所有舊函式中的運算子皆必須存在於新函式中。

-   新函式不能包含不存在於舊函式中的運算子。

-   不能變更運算子引數的順序。

-   只有 `<, <=, >, >=` 比較之一部分的常數值，能以使述詞含有較少限制的方式受到變更。

### 有效取代的範例
假設下列函式為目前的篩選述詞。

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -100 OR @column2 > 100)
GO
```
下列函式為有效取代，因為新的日期常數 (指定之後的截止日期) 能使述詞含有較少限制。

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
			AND (@column2 < -50 OR @column2 > 50)
GO
```

### 無效取代的範例
下列函式不是有效取代，因為新的日期常數 (指定之前的截止日期) 不能使述詞含有較少限制。

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
			AND (@column2 < -100 OR @column2 > 100)
GO
```
下列函式不是有效取代，因為其中一個比較運算子已被移除。

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -50)
GO
```
下列函式不是有效取代，因為已新增包含 AND 邏輯運算子的新條件。

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -100 OR @column2 > 100)
			AND (@column2 <> 0)
GO
```

## 卸除篩選述詞
只要有資料表將該函式做為其篩選述詞使用，您便無法卸除嵌入資料表值函式。

## 查看套用至資料表的篩選述詞
若要檢查套用至資料表的篩選述詞，請開啟目錄檢視 **sys.remote\_data\_archive\_tables** 並查看 **filter\_predicate** 資料欄的值。如果值為 Null，便代表整個資料表皆符合封存資格。如需詳細資訊，請參閱 [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)。

## 另請參閱

[ALTER TABLE (TRANSACT-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0323_2016-->