<properties 
   pageTitle="針對 Azure 資料湖分析工作使用 U-SQL 視窗函式 | Azure" 
   description="了解如何使用 U-SQL 視窗函式。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/11/2016"
   ms.author="jgao"/>


# 針對 Azure 資料湖分析工作使用 U-SQL 視窗函式  

視窗函式是在 2003 年的 ISO/ANSI SQL Standard 中導入的。U-SQL 採用 ANSI SQL Standard 所定義之視窗函式的子集。

視窗函式可用來進行名為*視窗*之資料列集內的計算。視窗由 OVER 子句所定義。視窗函式可高效率地解決一些重要的案例。

本教學課程/學習指南將使用兩個範例資料集，引導您了解可套用視窗函式的部分範例案例。如需詳細資訊，請參閱 [U-SQL 參考](http://go.microsoft.com/fwlink/p/?LinkId=691348)。

視窗函式可分類為：

- [報告彙總函式](#reporting-aggregation-functions)，如 SUM 或 AVG
- [排名函式](#ranking-functions)，例如 DENSE\_RANK、ROW\_NUMBER、NTILE 和 RANK
- [分析函式](#analytic-functions)，例如累加分配、百分位數，或從相同結果集中的前一個資料列存取資料，而不使用自我聯結

**必要條件：**

- 完成下列兩個教學課程：

    - [開始使用適用於 Visual Studio 的 Azure 資料湖工具](data-lake-analytics-data-lake-tools-get-started.md)。
    - [開始針對 Azure 資料湖分析工作使用 U-SQL](data-lake-analytics-u-sql-get-started.md)。
- 依照[開始使用適用於 Visual Studio 的 Azure 資料湖工具](data-lake-analytics-data-lake-tools-get-started.md)中的指示，建立資料湖分析帳戶。
- 依照[開始針對 Azure 資料湖分析工作使用 U-SQL](data-lake-analytics-u-sql-get-started.md) 中的指示，建立 Visual Studio U-SQL 專案。

## 範例資料集

本教學課程使用兩個資料集：

- QueryLog 

    QueryLog 代表人員在搜尋引擎中搜尋的項目清單。每個查詢記錄檔包含：
    
        - Query - What the user was searching for.
        - Latency - How fast the query came back to the user in milliseconds.
        - Vertical - What kind of content the user was interested in (Web links, Images, Videos).
    
    將下列指令碼複製並貼到您的 U-SQL 專案，以建構 QueryLog 資料列集：
    
        @querylog = 
            SELECT * FROM ( VALUES
                ("Banana"  , 300, "Image" ),
                ("Cherry"  , 300, "Image" ),
                ("Durian"  , 500, "Image" ),
                ("Apple"   , 100, "Web"   ),
                ("Fig"     , 200, "Web"   ),
                ("Papaya"  , 200, "Web"   ),
                ("Avocado" , 300, "Web"   ),
                ("Cherry"  , 400, "Web"   ),
                ("Durian"  , 500, "Web"   ) )
            AS T(Query,Latency,Vertical);
    
    在實務上，資料最有可能儲存在資料檔中。您將使用下列程式碼，在定位點分隔符號檔案內存取該資料：
    
        @querylog = 
        EXTRACT 
            Query    string, 
            Latency  int, 
            Vertical string
        FROM "/Samples/QueryLog.tsv"
        USING Extractors.Tsv();

- 員工

    員工資料集包含下列欄位：
   
        - EmpID - Employee ID.
        - EmpName  Employee name.
        - DeptName - Department name. 
        - DeptID - Deparment ID.
        - Salary - Employee salary.

    將下列指令碼複製並貼到您的 U-SQL 專案，以建構「員工」資料列集：

        @employees = 
            SELECT * FROM ( VALUES
                (1, "Noah",   "Engineering", 100, 10000),
                (2, "Sophia", "Engineering", 100, 20000),
                (3, "Liam",   "Engineering", 100, 30000),
                (4, "Emma",   "HR",          200, 10000),
                (5, "Jacob",  "HR",          200, 10000),
                (6, "Olivia", "HR",          200, 10000),
                (7, "Mason",  "Executive",   300, 50000),
                (8, "Ava",    "Marketing",   400, 15000),
                (9, "Ethan",  "Marketing",   400, 10000) )
            AS T(EmpID, EmpName, DeptName, DeptID, Salary);
    
    下列陳述式示範如何藉由從資料檔中擷取資料列集加以建立。
    
        @employees = 
        EXTRACT 
            EmpID    int, 
            EmpName  string, 
            DeptName string, 
            DeptID   int, 
            Salary   int
        FROM "/Samples/Employees.tsv"
        USING Extractors.Tsv();

當您測試教學課程中的範例時，您必須包含資料列集定義。使用 U-SQL 時，您必須僅定義所使用的資料列集。有些範例只需要一個資料列集。

您也必須新增下列陳述式，以將結果資料列集輸出到資料檔：

    OUTPUT @result TO "/wfresult.csv" 
        USING Outputters.Csv();
 
 大部分的範例會在結果中使用名為 **@result** 的變數。

## 比較視窗函式與分組

「視窗化」與「分組」在概念上相關，但也有所不同。了解此關聯性將有所幫助。

### 使用彙總與分組

下列查詢會使用彙總來計算所有員工的薪資總和：

    @result = 
        SELECT 
            SUM(Salary) AS TotalSalary
        FROM @employees;
    
>[AZURE.NOTE] 如需測試和檢查輸出的相關指示，請參閱[開始針對 Azure 資料湖分析工作使用 U-SQL](data-lake-analytics-u-sql-get-started.md)。

結果是具有單一資料行的單一資料列。$165000 是整份資料表中 [薪資] 值的總和。

|TotalSalary
|-----------
|165000

>[AZURE.NOTE] 如果您剛剛才接觸視窗函式，記住輸出中的數字將會有幫助。

下列陳述式使用 GROUP BY 子句來計算每個部門薪資總計：

    @result=
        SELECT DeptName, SUM(Salary) AS SalaryByDept
        FROM @employees
        GROUP BY DeptName;

結果為：

|DeptName|SalaryByDept
|--------|------------
|Engineering|60000
|HR|30000
|[行政人員]|50000
|行銷|25000

SalaryByDept 資料行的總和為 $165000，符合最後一個指令碼中的金額。
 
在這兩個案例中，輸出資料列的數字都少於輸入資料列：
 
- 未使用 GROUP BY 時，彙總會將所有資料列摺疊成單一資料列。 
- 使用 GROUP BY 時會有 N 個輸出資料列，其中，N 是出現在資料中的相異值。在此情況下，輸出中將會有 4 個資料列。

###  使用視窗函式

下列範例中的 OVER 子句是空的。這會將「視窗」定義為包含所有資料列。在此範例中的 SUM 會套用到它前面的 OVER 子句。

您可以將此查詢解讀為：「所有資料列視窗內的薪資總和」。

    @result=
        SELECT
            EmpName,
            SUM(Salary) OVER( ) AS SalaryAllDepts
        FROM @employees;

不同於 GROUP BY，輸出資料列的數量會與輸入資料列一樣多：

|EmpName|TotalAllDepts
|-------|--------------------
|Noah|165000
|Sophia|165000
|Liam|165000
|Emma|165000
|Jacob|165000
|Olivia|165000
|Mason|165000
|Ava|165000
|Ethan|165000


值 165000 (所有薪資的總計) 會放在每個輸出資料列中。該總計來自於所有資料列的「視窗」，因此其中包含所有的薪資。

下一個範例示範如何讓「視窗」更完善，以列出所有員工、部門，以及部門的薪資總計。PARTITION BY 會新增到 OVER 子句中。

    @result=
    SELECT
        EmpName, DeptName,
        SUM(Salary) OVER( PARTITION BY DeptName ) AS SalaryByDept
    FROM @employees;

結果為：

|EmpName|DeptName|SalaryByDep
|-------|--------|-------------------
|Noah|Engineering|60000
|Sophia|Engineering|60000
|Liam|Engineering|60000
|Mason|[行政人員]|50000
|Emma|HR|30000
|Jacob|HR|30000
|Olivia|HR|30000
|Ava|行銷|25000
|Ethan|行銷|25000

同樣地，輸入資料列與輸出資料列的數量是相同的。不過，每個資料列都有對應部門的薪資總計。




## 報告彙總函式

視窗函式也支援下列彙總：

- COUNT
- SUM
- 最小值
- 最大值
- 平均
- STDEV
- VAR

語法：

    <AggregateFunction>( [DISTINCT] <expression>) [<OVER_clause>]

注意：

- 根據預設，彙總函式 (COUNT 除外) 會忽略 null 值。
- 當彙總函式與 OVER 子句一起指定時，將不允許在 OVER 子句中使用 ORDER BY 子句。

### 使用 SUM

下列範例讓每個輸入資料列新增各部門的薪資總計：
 
    @result=
        SELECT 
            *,
            SUM(Salary) OVER( PARTITION BY DeptName ) AS TotalByDept
        FROM @employees;

輸出如下：

|EmpID|EmpName|DeptName|DeptID|Salary|TotalByDept
|-----|-------|--------|------|------|-----------
|1|Noah|Engineering|100|10000|60000
|2|Sophia|Engineering|100|20000|60000
|3|Liam|Engineering|100|30000|60000
|7|Mason|[行政人員]|300|50000|50000
|4|Emma|HR|200|10000|30000
|5|Jacob|HR|200|10000|30000
|6|Olivia|HR|200|10000|30000
|8|Ava|行銷|400|15000|25000
|9|Ethan|行銷|400|10000|25000

### 使用 COUNT

下列範例會將額外的欄位新增至每個資料列，以顯示每個部門的員工總數。

    @result =
        SELECT *, 
            COUNT(*) OVER(PARTITION BY DeptName) AS CountByDept 
        FROM @employees;

結果：

|EmpID|EmpName|DeptName|DeptID|Salary|CountByDept
|-----|-------|--------|------|------|-----------
|1|Noah|Engineering|100|10000|3
|2|Sophia|Engineering|100|20000|3
|3|Liam|Engineering|100|30000|3
|7|Mason|[行政人員]|300|50000|1
|4|Emma|HR|200|10000|3
|5|Jacob|HR|200|10000|3
|6|Olivia|HR|200|10000|3
|8|Ava|行銷|400|15000|2
|9|Ethan|行銷|400|10000|2


### 使用 MIN 和 MAX

下列範例會將額外的欄位新增至每個資料列，以顯示每個部門的最低薪資：

    @result =
        SELECT 
            *,
            MIN(Salary) OVER( PARTITION BY DeptName ) AS MinSalary
        FROM @employees;

結果：

|EmpID|EmpName|DeptName|DeptID|Salary|MinSalary
|-----|-------|--------|------|-------------|----------------
|1|Noah|Engineering|100|10000|10000
|2|Sophia|Engineering|100|20000|10000
|3|Liam|Engineering|100|30000|10000
|7|Mason|[行政人員]|300|50000|50000
|4|Emma|HR|200|10000|10000
|5|Jacob|HR|200|10000|10000
|6|Olivia|HR|200|10000|10000
|8|Ava|行銷|400|15000|10000
|9|Ethan|行銷|400|10000|10000

將 MIN 取代為 MAX，然後再試一次。


## 排名函式

排名函式會依照 PARTITION BY 和 OVER 子句的定義，針對每個分割中的每個資料列傳回排名值 (長數值)。排名的順序由 OVER 子句中的 ORDER BY 所控制。

支援的排名函式如下：

- RANK
- DENSE\_RANK 
- NTILE
- ROW\_NUMBER

**語法：**

	[ RANK() | DENSE_RANK() | ROW_NUMBER() | NTILE(<numgroups>) ]
	    OVER (
	        [PARTITION BY <identifier, > …[n]]
	        [ORDER BY <identifier, > …[n] [ASC|DESC]] 
	) AS <alias>

- ORDER BY 子句對於排名函式是選擇性的。如果已指定 ORDER BY，它將會決定排名的順序。如果未指定 ORDER BY，U-SQL 將會根據它會讀取記錄的順序來指派值。因此，在未指定 ORDER BY 子句的情況下，會導致資料列號碼、排名或密度排名的值不具決定性。
- NTILE 需要評估為正整數的運算式。此數字會指定每個分割必須劃分成的群組數目。此識別碼只會與 NTILE 排名函式一起使用。 

如需 OVER 子句的詳細資訊，請參閱 [U-SQL 參考]()。

ROW\_NUMBER、RANK 和 DENSE\_RANK 都會指派數字給視窗中的資料列。與其個別加以檢視，查看它們如何回應相同的輸入，會更加一目瞭然。

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank, 
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank 
    FROM @querylog;
        
請注意，OVER 子句都相同。結果：

|查詢|Latency:int|Vertical|RowNumber|Rank|DenseRank
|-----|-----------|--------|--------------|---------|--------------
|Banana|300|映像|1|1|1
|Cherry|300|映像|2|1|1
|Durian|500|映像|3|3|2
|Apple|100|Web|1|1|1
|Fig|200|Web|2|2|2
|Papaya|200|Web|3|2|2
|Fig|300|Web|4|4|3
|Cherry|400|Web|5|5|4
|Durian|500|Web|6|6|5

### ROW\_NUMBER

在每個視窗內 (Vertical，無論是 Image 或 Web)，依 Latency 排序的資料列號碼會漸次加 1。

![U-SQL 視窗函式 ROW\_NUMBER](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-row-number-result.png)

### RANK

不同於 ROW\_NUMBER()，RANK() 會考量視窗的 ORDER BY 子句中所指定的 Latency 值。

RANK 從 (1,1,3) 開始，因為 Latency 的頭兩個值是相同的。下一個值是 3，因為 Latency 值已到達 500。關鍵點在於，即使重複的值指定了相同的排名，RANK 數值仍將「跳至」下一個 ROW\_NUMBER 值。您可以在 Web 垂直中看到這種模式以 (2,2,4) 的序列重複。

![U-SQL 視窗函式 RANK](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-rank-result.png)

### DENSE\_RANK
	
DENSE\_RANK 類似於 RANK，唯一差別在於它不會「跳至」下一個 ROW\_NUMBER，而是會移至序列中的下一個數字。請注意範例中的序列 (1,1,2) 和 (2,2,3)。

![U-SQL 視窗函式 DENSE\_RANK](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-dense-rank-result.png)

### 備註

- 如果未指定 ORDER BY，則會將排名函式套用到資料列集，不含任何排序。這會導致套用排名函式的行為不具決定性
- 除非下列條件成立，否則無法保證使用 ROW\_NUMBER 的查詢所傳回的資料列在每次執行時都會有完全相同的排序。

	- 分割資料行的值是唯一的。
	- ORDER BY 資料行的值是唯一的。
	- 分割資料行和 ORDER BY 資料行的值組合是唯一的。

### NTILE

NTILE 會將已排序分割中的資料列分配到指定數目的群組中。群組會編號，從 1 開始。


下列範例會依據查詢延遲的順序，將每個分割中的資料列集分割為 4 個群組，並傳回每個資料列的群組號碼。

Image 垂直有 3 個資料列，因此它有 3 個群組。

Web 垂直有 6 個資料列，兩個額外的資料列會分配到前兩個群組。這就是為什麼群組 1 和群組 2 中會有 2 個資料列，而群組 3 和群組 4 中只有 1 個資料列。

    @result =
        SELECT 
            *,
            NTILE(4) OVER(PARTITION BY Vertical ORDER BY Latency) AS Quartile   
        FROM @querylog;
		
結果：

|查詢|延遲|Vertical|Quartile
|-----|-----------|--------|-------------
|Banana|300|映像|1
|Cherry|300|映像|2
|Durian|500|映像|3
|Apple|100|Web|1
|Fig|200|Web|1
|Papaya|200|Web|2
|Fig|300|Web|2
|Cherry|400|Web|3
|Durian|500|Web|4

NTILE 會使用參數 ("numgroups")。Numgroups 是一個正整數或長常數運算式，會指定每個分割必須劃分成的群組數目。

- 如果分割中的資料列數目可由 numgroups 整除，則群組會有相同的大小。 
- 如果分割中的資料列數目無法由 numgroups 整除，這會使群組出現相差一碼的兩個大小。在 OVER 子句所指定的順序中，較大的群組會排在較小的群組前面。 

例如：

- 100 個資料列分割成 4 個群組：[ 25, 25, 25, 25 ]
- 102 個資料列分割成 4 個群組：[ 26, 26, 25, 25 ]


### 每個分割的前 N 筆記錄 (使用 RANK、DENSE\_RANK 或 ROW\_NUMBER)

許多使用者只想要選取每個群組的前 n 個資料列。傳統的 GROUP BY 無法達到此目的。

您在「排名函式」一節的開頭已看過下列範例。其中並未顯示每個分割的前 n 筆記錄：

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

結果：

|查詢|延遲|Vertical|Rank|DenseRank|RowNumber
|-----|-----------|--------|---------|--------------|--------------
|Banana|300|映像|1|1|1
|Cherry|300|映像|1|1|2
|Durian|500|映像|3|2|3
|Apple|100|Web|1|1|1
|Fig|200|Web|2|2|2
|Papaya|200|Web|2|2|3
|Fig|300|Web|4|3|4
|Cherry|400|Web|5|4|5
|Durian|500|Web|6|5|6

### 前 N 個 (使用 DENSE RANK)

下列範例會從每個群組中傳回前 3 筆記錄，且每個視窗化分割中的資料列沒有循序排名編號的間距。

    @result =
    SELECT 
        *,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE DenseRank <= 3;

結果：

|查詢|延遲|Vertical|DenseRank
|-----|-----------|--------|--------------
|Banana|300|映像|1
|Cherry|300|映像|1
|Durian|500|映像|2
|Apple|100|Web|1
|Fig|200|Web|2
|Papaya|200|Web|2
|Fig|300|Web|3

### 前 N 個 (使用 RANK)

    @result =
        SELECT 
            *,
            RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE Rank <= 3;

結果：

|查詢|延遲|Vertical|Rank
|-----|-----------|--------|---------
|Banana|300|映像|1
|Cherry|300|映像|1
|Durian|500|映像|3
|Apple|100|Web|1
|Fig|200|Web|2
|Papaya|200|Web|2


### 前 N 個 (使用 ROW\_NUMBER)

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE RowNumber <= 3;

結果：
    
|查詢|延遲|Vertical|RowNumber
|-----|-----------|--------|--------------
|Banana|300|映像|1
|Cherry|300|映像|2
|Durian|500|映像|3
|Apple|100|Web|1
|Fig|200|Web|2
|Papaya|200|Web|3

### 指派全域唯一資料列號碼

為每個資料列指派全域唯一號碼，通常會很有用。這用在排名函式上十分方便 (並且比使用歸納器更有效率)。

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER () AS RowNumber
        FROM @querylog;

<!-- ################################################### -->
## 分析函式

分析函式可用來了解值在視窗中的分佈情形。分析函式最常見的使用案例，就是計算百分位數。

**支援的分析視窗函式**

- CUME\_DIST 
- PERCENT\_RANK
- PERCENTILE\_CONT
- PERCENTILE\_DISC

### CUME\_DIST  

CUME\_DIST 會計算指定的值在值群組中的相對位置。它會計算延遲小於或等於目前的查詢延遲 (具有相同垂直) 之查詢的百分比。就採用遞增排序的資料列 R 而言，R 的 CUME\_DIST 是值小於或等於 R 之值的資料列數目，除以分割或查詢結果集所評估的資料列數目所得之數。CUME\_DIST 會傳回 0 < x <= 1 範圍內的數值。

**語法**

    CUME_DIST() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
    ) AS <alias>

下列範例使用 CUME\_DIST 函式來計算垂直中每個查詢的延遲百分位數。

    @result=
        SELECT 
            *,
            CUME_DIST() OVER(PARTITION BY Vertical ORDER BY Latency) AS CumeDist
        FROM @querylog;

結果：
    
|查詢|延遲|Vertical|CumeDist
|-----|-----------|--------|---------------
|Durian|500|映像|1
|Banana|300|映像|0\.666666666666667
|Cherry|300|映像|0\.666666666666667
|Durian|500|Web|1
|Cherry|400|Web|0\.833333333333333
|Fig|300|Web|0\.666666666666667
|Fig|200|Web|0\.5
|Papaya|200|Web|0\.5
|Apple|100|Web|0\.166666666666667

分割索引鍵為 “Web” 的分割中有 6 個資料列 (第 4 個資料列和以下)：

- 有 6 個資料列的值等於或小於 500，因此 CUME\_DIST 等於 6/6=1
- 有 5 個資料列的值等於或小於 400，因此 CUME\_DIST 等於 5/6=0.83
- 有 4 個資料列的值等於或小於 300，因此 CUME\_DIST 等於 4/6=0.66
- 有 3 個資料列的值等於或小於 200，因此 CUME\_DIST 等於 3/6=0.5。有兩個資料列具有相同的延遲值。
- 有 1 個資料列的值等於或小於 100，因此 CUME\_DIST 等於 1/6=0.16。 


**使用注意事項：**

- 相等值一律會評估為相同的累加分配值。
- NULL 值會被視為最低的可能值。
- 您必須指定 ORDER BY 子句來計算 CUME\_DIST。
- CUME\_DIST 類似於 PERCENT\_RANK 函式

附註：如果 SELECT 陳述式後面沒有 OUTPUT，則不允許 ORDER BY 子句。因此，OUTPUT 陳述式中的 ORDER BY 子句會決定結果資料列集的顯示順序。


### PERCENT\_RANK

PERCENT\_RANK 會計算資料列群組內某資料列的相對排名。PERCENT\_RANK 可用來評估某值在資料列集或分割內的相對位置。PERCENT\_RANK 傳回的值範圍會大於 0 且小於或等於 1。不同於 CUME\_DIST，PERCENT\_RANK 的第一個資料列一律為 0。
	
**語法**

    PERCENT_RANK() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
        ) AS <alias>

**注意事項**

- 任何集合中第一個資料列的 PERCENT\_RANK 皆為 0。
- NULL 值會被視為最低的可能值。
- 您必須指定 ORDER BY 子句來計算 PERCENT\_RANK。
- CUME\_DIST 類似於 PERCENT\_RANK 函式 


下列範例使用 PERCENT\_RANK 函式來計算垂直中每個查詢的延遲百分位數。

指定的 PARTITION BY 子句會依垂直分割結果集中的資料列。OVER 子句中的 ORDER BY 子句會排序每個分割中的資料列。

PERCENT\_RANK 函式所傳回的值代表查詢在垂直內的延遲排名 (以百分比表示)。


    @result=
        SELECT 
            *,
            PERCENT_RANK() OVER(PARTITION BY Vertical ORDER BY Latency) AS PercentRank
        FROM @querylog;

結果：

|查詢|Latency:int|Vertical|PercentRank
|-----|-----------|--------|------------------
|Banana|300|映像|0
|Cherry|300|映像|0
|Durian|500|映像|1
|Apple|100|Web|0
|Fig|200|Web|0\.2
|Papaya|200|Web|0\.2
|Fig|300|Web|0\.6
|Cherry|400|Web|0\.8
|Durian|500|Web|1

### PERCENTILE\_CONT 和 PERCENTILE\_DISC

這兩個函式會根據資料行值的連續或離散分佈來計算百分位數。

**語法**

    [PERCENTILE_CONT | PERCENTILE_DISC] ( numeric_literal ) 
        WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ] )
        OVER ( [ PARTITION BY <identifier,>…[n] ] ) AS <alias>

**numeric\_literal** - 要計算的百分位數。此值必須介於 0.0 到 1.0 的範圍內。

WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ]) - 指定用來排序和計算百分位數的數值清單。僅允許一個資料行識別碼。運算式必須評估為數值類型。不允許其他資料類型。預設排序順序為遞增。

OVER ([ PARTITION BY <identifier,>…[n] ] ) - 根據套用百分位數函式的分割索引鍵，將輸入資料列集分成多個分割。如需詳細資訊，請參閱本文的「排名」一節。附註：資料集中的任何 null 值都會被忽略。

**PERCENTILE\_CONT** 會根據資料行值的連續分佈來計算百分位數。其結果會以內插值取代，且可能不會等於資料行中的任何特定值。

**PERCENTILE\_DISC** 會根據資料行值的離散分佈來計算百分位數。其結果會等於資料行中的特定值。換句話說，相較於 PERCENTILE\_CONT，PERCENTILE\_DISC 一律會傳回實際 (原始輸入) 值。

您可以檢視兩者在下列範例中的運作情形；它們會嘗試尋找每個垂直內之延遲的中間值 (百分位數=0.50)

    @result = 
        SELECT 
            Vertical, 
            Query,
            PERCENTILE_CONT(0.5) 
                WITHIN GROUP (ORDER BY Latency)
                OVER ( PARTITION BY Vertical ) AS PercentileCont50,
            PERCENTILE_DISC(0.5) 
                WITHIN GROUP (ORDER BY Latency) 
                OVER ( PARTITION BY Vertical ) AS PercentileDisc50 
        
        FROM @querylog;

結果：

|查詢|Latency:int|Vertical|PercentileCont50|PercentilDisc50
|-----|-----------|--------|-------------------|----------------
|Banana|300|映像|300|300
|Cherry|300|映像|300|300
|Durian|500|映像|300|300
|Apple|100|Web|250|200
|Fig|200|Web|250|200
|Papaya|200|Web|250|200
|Fig|300|Web|250|200
|Cherry|400|Web|250|200
|Durian|500|Web|250|200


就 PERCENTILE\_CONT 而言，由於可以插補值，因此 Web 的中間值是 250，即使 Web 垂直中沒有查詢的延遲是 250 亦然。

PERCENTILE\_DISC 不會插補值，因此 Web 的中間值是 200 - 也就是在輸入資料列中找到的實際值。











## 另請參閱

- [Microsoft Azure 資料湖分析概觀](data-lake-analytics-overview.md)
- [使用 Azure 入口網站開始使用資料湖分析](data-lake-analytics-get-started-portal.md)
- [使用 Azure PowerShell 開始使用資料湖分析](data-lake-analytics-get-started-powershell.md)
- [使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)
- [使用 Azure 資料湖分析互動式教學課程](data-lake-analytics-use-interactive-tutorials.md)
- [使用 Azure 資料湖分析來分析網站記錄](data-lake-analytics-analyze-weblogs.md)
- [開始使用 Azure 資料湖分析 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)
- [使用 Azure 入口網站管理 Azure 資料湖分析](data-lake-analytics-manage-use-portal.md)
- [使用 Azure PowerShell 管理 Azure 資料湖分析](data-lake-analytics-manage-use-powershell.md)
- [使用 Azure 入口網站監視和疑難排解 Azure 資料湖分析作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0218_2016-->