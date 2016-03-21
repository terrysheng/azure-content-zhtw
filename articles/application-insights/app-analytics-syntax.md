<properties 
	pageTitle="Application Analytics 中的陳述式" 
	description="Application Analytics 中的查詢、運算式和 let 陳述式，適用於 Application Insights 的強大搜尋工具。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/01/2016" 
	ms.author="awills"/>


 
# Application Analytics 中的陳述式

[Application Analytics](app-analytics.md) 是強大的搜尋引擎，適合用於 [Application Insights](app-insights-overview.md) 遙測。這些頁面說明 Application Analytics 查詢語言，CSL。


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## 資料模型

在 CSL 中：

* 「資料庫」不包含或是包含多個具名「資料表」。
* 「資料表」 包含：
 * 一個或多個具名「資料行」。 每個資料行都具有「類型」。
 * 一個或多個「資料列」。
* 每個資料列都有一或多個「資料格」，資料表的每個資料行各一個。
* 資料格可包含資料行類型的值，或是 `null`。


## 實體名稱

每個資料行、資料表或資料庫都在其容器內部擁有唯一名稱。

可透過它的名稱 (如果內容明確) 參考到實體，或透過其容器來限制資格。例如，`MyColumn` 也可參考為 `MyTable.MyColumn` 或 `MyDatabase.MyTable.MyColumn`。

名稱的長度上限為 1024 個字元。它們會區分大小寫，且可能會包含字母、數字和底線 (`_`)。

此外，名稱可以加上引號，以便包含其他字元。例如：

|||
|---|---|
|`['path\\file\n\'x\'']` | 使用 `` 來逸出字元|
|`["d-e.=/f#\n"]` | |
|`[@'path\file']`| 沒有逸出- `` 是常值|
|`[@"\now & then"]` | |
|`[where]` | 使用語言關鍵字做為名稱|

資料庫的名稱也可以是有效名稱 (請參閱[跨資料庫查詢](#cross-database-queries))

```
database("MyDb").Table
```


## 陳述式

在 CSL 中有四種陳述式：

### 資料查詢
  
有關儲存於 Application Analytics 中資料的唯讀要求。例如：

* `event` - 傳回名為 "event" 資料表中的所有記錄。
* `event | count` - 傳回 "event" 中的記錄數目。

    
## let 陳述式

#### 概觀
藉由一個或多個 let 陳述式，就可以在資料查詢陳述式上加上前置詞。這些陳述式會允許將名稱 (有效的實體名稱) 繫結到運算式。由 let 陳述式定義的名稱，可在接下來的資料查詢陳述式中使用一或多次。

let 陳述式可以將名稱繫結到下列類型的運算式中：
1. 純量
2. 表格式資料 

當繫結至表格式資料時 (這可將 let 陳述式提升為 "view")，將會允許陳述式參與 "union *" 作業。

#### 語法

`let <name> = <expression>`

<expression> 可能是沒有引數、或具有一或多個引數的 Lambda 宣告：

`() {...}`

`(<arg0>:<type0>, ...) {...}`

範例：下列範例會將名稱 'x' 繫結至純量常值 '1'：

```
let x=1;
range y from x to x step x
```

下列範例會將一個小時量的記錄檔繫結至名稱「Window」，然後在 Window 上執行自我聯結：


```
let Window=Logs | where Timestamp > ago(1h);
Window | where  ... | join (Window | where …) on ActivityId| ...
```

下列兩個範例示範使用 let 陳述式搭配 Lambda 運算式：


```
let Test = () { range x from 1 to 10 step 1 };
Test | count

let step=1;
let Test = (start:long, end:long) { range x from start to end step 1 };
Test(1, 10) | count
```

使用 'view' 關鍵字將 let 陳述式提升為參與 union * 作業的檢視。


```
let Test1 = view () { range x from start to end step 1 };
let Test2 = view () { range x from start to end step 1 };
union * | count
// Result: 20
```


## restrict 陳述式

#### 概觀
它可以利用以下陳述式將存取查詢限制在存取陳述式清單的範圍中：


```
restrict access to (<entity1, entity2, ...>);
```
 
該陳述式將只允許存取 restrict 陳述式所允許的那些實體 (let 陳述式或表格式實體)。
 
範例：

```
// Restricting access to Test statement only
let Test = () { range x from 1 to 10 step 1 };
restrict access to (Test);
Test
 
// Assume that there is a table called Table1, Table2 in the database
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
 
// When those statements appear before the command - the next works
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
View1 |  count
 
// When those statements appear before the command - the next access is not allowed
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
Table1 |  count
```


## 查詢撰寫

「查詢」遵循模式：

- 來源 | 篩選器 | 篩選器...

例如：


```
Logs | where Timestamp > ago(1d) | count
```

* 「來源」是資料庫的名稱，或是先前定義的結果資料表。
* 每個「篩選器」會叫用查詢運算子，例如 `where` 或 `count`，搭配適當的參數。參數可以是「純量運算式」、巢狀「資料查詢」或資料行名稱。

例如：


```
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
``` 

## Let 陳述式

let 陳述式可用於定義函式 — 命名採用多個引數或不採用引數的運算式，並傳回值。進一步的陳述式接著可「叫用」那些函式。

### 具名值

定義代表純量值的名稱：


```
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

定義代表查詢結果的名稱：


```
let Cities = Events | summarize dcount(city) by country;
Cities | take 10
```

這特別適合自我聯結：


```
let Recent = Events | where timestamp > ago(7d);
Recent | where name contains "session_started" 
| project start = timestamp, session_id
| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
    on session_id
| extend duration = stop - start 
```

## 具名函式

定義會傳回純量結果的函式：


```
let square = (n:long){n*n};
// yield 9 rows
Events | take square(3)    
```

定義會傳回查詢結果的函式：


```
let TopEvents= (n:long, when:datetime){Events 
                | where timestamp > when | take n};
TopEvents(5, ago(7d)) 
```

具名函式的參數必須是純量。





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->