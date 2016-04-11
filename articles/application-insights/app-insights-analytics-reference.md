<properties 
	pageTitle="Application Insights 中分析的參考資料" 
	description="分析 (強大的 Application Insights 搜尋工具) 中陳述式的參考資料。" 
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
	ms.date="03/30/2016" 
	ms.author="awills"/>

# 適用於分析的參考

[分析](app-insights-analytics.md)是 [Application Insights](app-insights-overview.md) 的強大搜尋功能。這些頁面說明 Analytics 查詢語言。

使用您的瀏覽器搜尋來尋找此頁面上的語言項目，其中結合了查詢、彙總和純量頁面的內容。

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]



## 查詢和運算子

遙測的查詢是由來源串流參考後接篩選條件管線所組成。例如：


```AIQL
requests
| where client_City == "London" and timestamp > ago(3d)
| count
```
    
每個以縱線字元 `|` 做為前置詞的篩選條件，即為含有一些參數的「運算子」執行個體。前面管線所得到的結果會以資料表的形式做為運算子的輸入。在大部分情況下，任何參數皆是輸入資料行的[純量運算式](##scalars)。但在某些情況下，參數是輸入資料行的名稱或是第二個資料表。查詢的結果永遠是資料表，即使它只有一個資料行和一個資料列。

查詢能以一個或多個 [let 子句](#let-clause)做為前置詞，以定義可在查詢中使用的純量、資料表或函式。

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> 以下查詢範例中使用 `T` 來表示前面的管線或來源資料表。
> 

### count 運算子

`count` 運算子會傳回輸入記錄集內的記錄 (資料列) 數目。

**語法**

    T | count

**引數**

* T︰要計算記錄數目的表格式資料。

**傳回**

此函式會傳回含有一個記錄且資料行類型為 `long` 的資料表。唯一資料格的值是 T 中的記錄數目。

**範例**

```AIQL
requests | count
```



### extend 運算子

     T | extend duration = stopTime - startTime

在資料表中附加一個或多個經過計算的資料行。


**語法**

    T | extend ColumnName = Expression [, ...]

**引數**

* T：輸入資料表。
* ColumnName：要新增的資料行名稱。 
* Expression︰現有資料行的計算。

**傳回**

輸入資料表的複本，其中含有指定的其他資料行。

**秘訣**

* 如果您也想要捨棄或重新命名某些資料行，請改用 [`project`](#project-operator)。
* 請勿只是為了讓名稱縮短以利用於長運算式中就使用 `extend`。`...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    資料表原本的資料行已編制索引，但縮短後的新名稱會另外定義尚未編製索引的資料行，而可能導致查詢的執行速度變慢。

**範例**

```AIQL
traces
| extend
    Age = now() - timestamp
```


### join 運算子

    Table1 | join (Table2) on CommonColumn

藉由比對指定資料行的值來合併兩個資料表的資料列。


**語法**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**引數**

* Table1 - 聯結的「左側」。
* Table2 - 聯結的「右側」。其可以是會輸出資料表的巢狀查詢運算式。
* CommonColumn - 在這兩個資料表中的名稱皆相同的資料行。
* Kind - 指定這兩個資料表中的資料列比對方式。

**傳回**

含有下列項目的資料表︰

* 兩個資料表中的每個資料行各自佔據一個資料行，包括用來比對的索引鍵。如果名稱有衝突，右側的資料行會自動重新命名。
* 輸入資料表間的每個相符項目各自佔據一個資料列。如果從其中一個資料表中選取的資料列，與另一個資料表中某個資料列的所有 `on` 欄位值皆相同，就代表是相符項目。 

* `Kind` (未指定)

    左側只會有一個資料列符合 `on` 索引鍵的每個值。此資料列與右側資料列的每個相符項目都會在輸出中佔有一個資料列。

* `Kind=inner`
 
     左側和右側相符資料列的每個組合都會在輸出中佔有一個資料列。

* `kind=leftouter` (或 `kind=rightouter` 或 `kind=fullouter`)

     除了內部的相符項目，左側 (和/或右側) 的每個資料列即使不相符也會佔有一個資料列。在此情況下，不相符的輸出資料格會包含 null。

* `kind=leftanti`

     傳回左側中與右側不相符的所有記錄。結果資料表中只有左側的資料行。
 
如果好幾個資料列在這些欄位具有相同的值，每一個組合都會佔有一個資料列。

**秘訣**

若要獲得最佳效能︰

* 在 `join` 前面使用 `where` 和 `project`，以減少輸入資料表中的資料列和資料行數目。 
* 如果某個資料表一定會比另一個資料表還小，請將它做為聯結的左側 (管線)。
* 聯結相符項目的資料行必須具有相同名稱。如果必須重新命名其中一個資料表中的資料行，請使用 project 運算子。

**範例**

從記錄檔中取得擴充的活動，其中的某些項目會標示活動的開始和結束。

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
    | where Name == "Start"
    | project Name, City, ActivityId, StartTime=timestamp
    | join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
    | project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```

### let 子句

表格式 let - 命名資料表

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

純量 let - 命名值

    let interval = 3d; 
    requests | where timestamp > ago(interval)

Lambda let - 命名函式

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

let 子句會將名稱繫結至表格式結果、純量值或函式。子句是查詢的前置詞，而繫結的範圍就是該查詢 (let 無法讓您命名稍後才會在工作階段中用到的項目)。

語法

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

* type：`bool`、`int`、`long`、`double`、`string`、`timespan`、`datetime`、`guid`、[`dynamic`](#dynamic-type)
* plain\_query：未在開頭加上 let 子句的查詢。

範例




    let rows(n:long) = range steps from 1 to n step 1;
    rows(10) | ...


自我聯結︰

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
    | project start = timestamp, session_id
    | join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
    | extend duration = stop - start 

### limit 運算子

     T | limit 5

傳回輸入資料表中的資料列，最多可傳回所指定的數目。此運算子無法保證會傳回哪些記錄 (若要傳回特定記錄，請使用 [`top`](#top-operator))。

別名 `take`

語法

    T | limit NumberOfRows


秘訣

當您在進行互動工作時，`Take` 可讓您輕鬆、有效率地查看結果的樣本。但請注意，它無法保證一定會產生任何特定資料列，或以任何特定順序產生資料列。

即使您不使用 `take`，傳回給用戶端的資料列數目還是會受到隱含限制。若要提高此限制，請使用 `notruncation` 用戶端要求選項。



### mvexpand 運算子

    T | mvexpand listColumn 

從動態類型 (JSON) 的資料格展開清單，讓每個項目各自佔據一個資料列。所展開資料列中的其他所有資料格則會重複。

(另請參閱會執行相反函式的 [`summarize makelist`](#summarize-operator)。)

範例

假設輸入資料表如下︰

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{"key":"value"}|
|2|"world"|[0,1,"k","v"]|

    mvexpand D

結果如下：

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{"key":"value"}|
|2|"world"|0|
|2|"world"|1|
|2|"world"|"k"|
|2|"world"|"v"|


語法

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

引數

* ColumnName：在結果中，具名資料行中的陣列會展開為多個資料列。 
* ArrayExpression：產生陣列的運算式。如果使用這種形式，則會加入新資料行，並保留現有資料行。
* Name︰新資料行的名稱。
* Typename：將展開的運算式轉換為特定類型
* RowLimit：從每個原始資料列所產生的資料列數目上限。預設值為 128。

傳回

具名資料行的任何陣列中或是陣列運算式中的每個值會佔據多個資料列。

展開的資料行一律具有動態類型。如果您想要計算或彙總多個值，請使用轉換函式，例如 `todatetime()` 或 `toint()`。

有兩種屬性包展開模式可受到支援︰

* `bagexpansion=bag`︰屬性包會展開為單一項目屬性包。這是預設展開模式。
* `bagexpansion=array`︰屬性包會展開為有兩個元素的 `[`*索引鍵*`,`*值*`]` 陣列結構，以便能夠統一存取索引鍵和值 (以及舉例來說，對屬性名稱執行相異計數彙總)。 

範例


    exceptions | take 1 
    | mvexpand details[0]

將例外狀況記錄分割成多個資料列，以容納詳細資料欄位中的每個項目。



### parse 運算子

    T | parse "I am 63 next birthday" with "I am" Year:int "next birthday"

    T | parse kind=regex "My 62nd birthday" 
        with "My" Year:regex("[0..9]+") regex("..") "birthday"

從字串中擷取值。可使用簡單比對或規則運算式比對。

`with` 子句中的項目會依序和來源字串進行比對。每個項目都會擷取一段來源文字。如果是純文字字串，比對游標會一直往後移動到相符項目的位置。如果是含有類型名稱的資料行，游標會往後移動到足以剖析指定類型的位置 (字串相符項目會一直往後移動到找到下一個元素的相符項目為止)。 如果是 regex，則會比對規則運算式 (而且所產生的資料行一律具有字串類型)。

**語法**

    T | parse StringExpression with [SimpleMatch | Column:Type] ...

    T | parse kind=regex StringExpression 
        with [SimpleMatch | Column : regex("Regex")] ...

**引數**

* T：輸入資料表。
* kind︰simple 或 regex。預設值是 simple。
* StringExpression：評估為屬於字串或可以轉換為字串的運算式。
* SimpleMatch：符合文字的下一個部分的字串。
* Column︰指定要指派比對的新資料行。
* Type︰指定如何剖析來源字串的下一個部分。
* Regex：用來比對字串的下一個部分的規則運算式。 

**傳回**

會根據資料行清單擴充的輸入資料表。


**範例**

`parse` 運算子透過在相同的 `string` 運算式上使用多個 `extract` 應用程式，來簡化資料表的 `extend`。當資料表的 `string` 資料行有好幾個值，而您想要將這些值分割到個別資料行時，最適合使用此運算子，開發人員追蹤 ("`printf`"/"`Console.WriteLine`") 陳述式所產生的資料行便是一例。

在下列範例中，假設資料表 `StormEvents` 的資料行 `EventNarrative` 包含 `{0} at {1} crested at {2} feet around {3} on {4} {5}` 格式的字串。下列運算會為資料表擴充兩個資料行︰`SwathSize` 和 `FellLocation`。


|EventNarrative|
|---|
|Brownsville 的 Green River 在 12 月 12 日 0930EST 左右上升到最高水位 18.8 英呎。Brownsville 所訂出的洪水水位是 18 英呎。一旦到達此水位就會造成輕微氾濫。河水會溢流出水閘與一些高度較低的河堤，以及一些低窪農業用地。|
|Boston 的 Rolling Fork River 在 12 月 12 日 1700EST 左右上升到最高水位 39.3 英呎。Boston 所訂出的洪水水位是 35 英呎。一旦到達此水位就會造成輕微氾濫，波及範圍會涵蓋一些低窪農業用地。|
|Woodbury 的 Green River 在 12 月 16 日 0600EST 左右上升到最高水位 36.7 英呎。Woodbury 所訂出的洪水水位是 33 英呎。一旦到達此水位就會造成輕微氾濫，讓 Woodbury 鎮周圍的一些低窪地區淹水。|
|Tell City 的 Ohio River 在 12 月 18 日 7 AM EST 左右上升到最高水位 39.0 英呎。Tell City 所訂出的洪水水位是 38 英呎。一旦到達此水位，河水就會開始溢出河堤。印第安納 66 號公路的 Rome 到 Derby 路段會淹水。|

```AIQL

StormEvents 
|  parse EventNarrative 
   with RiverName:string 
        "at" 
        Location:string 
        "crested at" 
        Height:double  
        "feet around" 
        Time:string 
        "on" 
        Month:string 
        " " 
        Day:long 
        "." 
        notImportant:string
| project RiverName , Location , Height , Time , Month , Day

```

|RiverName|位置|高度|時間|月|天|
|---|---|---|---|---|---|
|The Green River | Woodbury |36\.7| 0600EST | 12 月|16|
|The Rolling Fork River | 波士頓 |39\.3| 1700EST | 12 月|12|
|The Green River | Brownsville |18\.8| 0930EST | 12 月|12|
|The Ohio River | Tell City |39| 7 AM EST | 12 月|18|

此外，也可以使用規則運算式進行比對。這會產生相同的結果，但所有結果資料行會具有字串類型︰

```AIQL

StormEvents
| parse kind=regex EventNarrative 
  with RiverName:regex("(\\s?[a-zA-Z]+\\s?)+") 
  "at" Location:regex(".*") 
  "crested at " Height:regex("\\d+\\.\\d+") 
  " feet around" Time:regex(".*") 
  "on " Month:regex("(December|November|October)") 
   " " Day:regex("\\d+") 
   "." notImportant:regex(".*")
| project RiverName , Location , Height , Time , Month , Day
```


### project 運算子

    T | project cost=price*quantity, price

選取要納入、重新命名或捨棄的資料行，以及插入新的計算資料行。結果中的資料行順序是由引數順序來指定。只有引數中指定的資料行才會包含在結果中︰輸入中的其他任何資料行則會遭到捨棄。(另請參閱 `extend`)。


**語法**

    T | project ColumnName [= Expression] [, ...]

**引數**

* T：輸入資料表。
* ColumnName：要出現在輸出中的資料行名稱。如果沒有任何 Expression，則該名稱的資料行必須出現在輸入中。 
* Expression︰參考輸入資料行的選擇性純量運算式。 

    所傳回的新計算資料行名稱可以和輸入中的現有資料行同名。

**傳回**

具有命名為引數的資料行，且資料列數目和輸入資料表相同的資料表。

**範例**

下列範例示範幾種可以使用 `project` 運算子完成的操作。輸入資料表 `T` 有三個類型為 `int` 的資料行：`A`、`B` 和 `C`。

```AIQL
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```



### range 運算子

    range LastWeek from ago(7d) to now() step 1d

產生單一資料行的值資料表。請注意它沒有管線輸入。

|LastWeek|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|



**語法**

    range ColumnName from Start to Stop step Step

**引數**

* ColumnName：輸出資料表中的單一資料行名稱。
* Start︰輸出中的最小值。
* Stop︰輸出中產生的最大值 (或者如果 step 跨越此值，為則最大值界限)。
* Step︰兩個連續值之間的差異。 

引數必須是數字、日期或時間範圍值。引數不能參考任何資料表的資料行 (如果您想要根據輸入資料表計算範圍，請使用 [range 函式](#range)，或許再搭配 [mvexpand 運算子](#mvexpand-operator))。

**傳回**

具有名稱為 ColumnName 之單一資料行的資料表，其值為 Start、Start + Step、...，直到 Stop (含)。

**範例**

```AIQL
range Steps from 1 to 8 step 3
```

具有名稱為 `Steps` 之單一資料行的資料表，其類型為 `long`，其值則為 `1`、`4` 和 `7`。

**範例**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

過去七天的午夜資料表。bin(floor) 函式會逐次減少至一天的開始。

**範例**

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

示範如何使用 `range` 運算子來建立小型的特定維度資料表，然後將其用來在沒有值的來源資料導入零值。

### reduce 運算子

    exceptions | reduce by outerMessage

嘗試將相似的記錄群組在一起。對於每個群組，運算子會輸出其認定最能描述該群組的 `Pattern`，以及該群組中記錄的 `Count`。


![](./media/app-insights-analytics-queries/reduce.png)

**語法**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**引數**

* ColumnName：要檢查的資料行。它必須是字串類型。
* Threshold：範圍 {0..1} 內的值。預設值為 0.001。若為大型輸入，臨界值應該小一點。 

**傳回**

兩個資料行，分別是 `Pattern` 和 `Count`。許多時候，Pattern 會是資料行中的完整值。它有時可以識別常見詞彙，並將變動部分取代為 '*'。

例如，`reduce by city` 的結果可能包括︰

|模式 | Count |
|---|---|
| San * | 5182 |
| Saint * | 2846 |
| Moscow | 3726 |
| * -on- * | 2730 |
| 巴黎 | 27163 |


### Render 指示詞

    T | render [ table | timechart  | barchart | piechart ]

Render 會指示展示層顯示資料表的方式。它應該是管道的最後一個元素。這是在顯示器上使用控制項的便利替代項目，讓您能夠以特定的呈現方法來儲存查詢。


### sort 運算子 

    T | sort by country asc, price desc

按照一個或多個資料行的順序排序輸入資料表的資料列。

**別名** `order`

**語法**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**引數**

* T：要排序的資料表輸入。
* Column︰做為排序依據之 T 的資料行。值的類型必須是數值、日期、時間或字串。
* `asc` 按照遞增順序由低至高排序。預設值是 `desc`，由高遞減至低。

**範例**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Traces 資料表中具有特定 `ActivityId` 的所有資料列，按其時間戳記排序。

### summarize 運算子

產生資料表來彙總輸入資料表的內容。
 
    requests
	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

顯示每個國家/地區之數目、平均要求持續時間和一組城市的資料表。每個不同的國家/地區在輸出中會各佔據一個資料列。輸出資料行會顯示計數、平均持續時間、城市和國家/地區。其他所有輸入資料行則會遭到忽略。


    T | summarize count() by price_range=bin(price, 10.0)

顯示有多少項目的價格落在 [0,10.0]、[10.0,20.0] 等依此類推的間隔中的資料表。此範例有一個用於放置計數的資料行，以及一個用於放置價格範圍的資料行。其他所有輸入資料行則會遭到忽略。


**語法**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**引數**

* Column：結果資料行的選擇性名稱。預設值為衍生自運算式的名稱。 
* Aggregation︰`count()` 或 `avg()` 等彙總函式的呼叫，以資料行名稱做為引數。請參閱[彙總](#aggregations)。
* GroupExpression：可提供一組相異值的資料行運算式。它通常是已提供一組受限值的資料行名稱，或是以數值或時間資料行做為引數的 `bin()`。 

如果您提供數值或時間運算式而不使用 `bin()`，「分析」就會自動為它套用 `1h` 間隔的時間，或 `1.0` 的數字。

如果您沒有提供 GroupExpression，整份資料表就會彙整在單一輸出資料列中。



**傳回**

輸入資料列會各自分組到具有相同 `by` 運算式值的群組。然後指定的彙總函式會針對每個群組進行計算，以便為每個群組產生資料列。結果會包含 `by` 資料行，而且每個經過計算的彙總至少會佔有一個資料行 (某些彙總函式會傳回多個資料行)。

`by` 值有多少個不同組合，結果就會有多少個資料列。如果您想要彙總數值範圍，請使用 `bin()` 將範圍減少為離散值。

**注意**

雖然您可以為彙總與群組運算式提供任意運算式，但更有效率的方法是使用簡單的資料行名稱，或對數值資料行套用 `bin()`。



### take 運算子

[limit](#limit-operator) 的別名


### top 運算子

    T | top 5 by Name desc

傳回按指定資料行排序的前 N 個記錄。


**語法**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [, ... ]

**引數**

* NumberOfRows：要傳回之 T 的資料列數目。
* Sort\_expression：用來排序資料列的運算式。它通常只是資料行名稱。您可以指定多個 sort\_expression。
* `asc` 或 `desc` (預設值) 可能會出現，以控制實際上是從範圍的「下限」或「上限」進行選取。


**秘訣**

`top 5 by name` 表面上相當於 `sort by name | take 5`。不過，前者的執行速度較快且一定會傳回排序後的結果，而 `take` 則不一定如此。


### union 運算子

     Table1 | union Table2, Table3

取得兩個或以上的資料表並傳回這些資料表中的資料列。

**語法**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**引數**

* *Table1*, *Table2* ...
 *  資料表名稱，例如 `events`；或
 *  查詢運算式，例如 `(events | where id==42)`
 *  使用萬用字元指定的一組資料表。例如，`E*` 會形成資料庫中名稱開頭為 `E` 之所有資料表的聯集。
* `kind`： 
 * `inner` - 結果中會有所有輸入資料表共有之資料行的子集。
 * `outer` - 結果中會有任何輸入中出現的所有資料行。輸入資料列未定義的資料格會設為 `null`。
* `withsource=`*ColumnName：*如果指定，輸出將會包含名為 *ColumnName* 的資料行，其值會指出哪一個來源資料表貢獻了每個資料列。

**傳回**

所含資料列數目和所有輸入資料表中的資料列數目一樣多的資料表。

**範例**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
名稱開頭為 "tt" 的所有資料表的聯集。


**範例**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
過去一天已產生 `exceptions` 事件或 `traces` 事件的不同使用者數目。在結果中，'SourceTable' 資料行會指出 "Query" 或 "Command"。

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

這個更有效率的版本會產生相同的結果。它會先篩選每個資料表再建立聯集。

### where 運算子

     T | where fruit=="apple"

篩選資料表以建立滿足述詞的資料列子集。

**別名** `filter`

**語法**

    T | where Predicate

**引數**

* T︰要篩選記錄的表格式輸入。
* Predicate︰T 之資料行的 `boolean` [運算式](#boolean)。它會針對 T 中的每個資料列進行評估。

**傳回**

Predicate 是 `true` 之 T 中的資料列。

**秘訣**

若要取得最快效能︰

* 在資料行名稱和常數之間**使用簡單比較** ('Constant' 表示資料表常數，因此 `now()` 和 `ago()` 都沒問題，並且是使用 [`let` 子句](#let-clause)指派的純量值)。

    例如，`where Timestamp >= ago(1d)` 比 `where floor(Timestamp, 1d) == ago(1d)` 更好。

* **最簡單的詞彙優先**︰如果您使用 `and` 連結多個子句，請先放置只包含一個資料行的子句。因此 `Timestamp > ago(1d) and OpId == EventId` 比反過來要好。


**範例**

```AIQL
Traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

不超過 1 小時、來自稱為 "Kuskus" 的來源，而且有兩個相同值之資料行的記錄。

請注意，我們將兩個資料行之間的比較放在最後，因為它不能利用索引和強制執行掃描。



## 彙總與彙整

### any 

    any(Expression)

隨機選取一列群組，並傳回指定運算式的值。

例如，當某些資料行有大量類似值 (例如，「錯誤文字」資料行)，而您想要根據複合群組索引鍵的唯一值對該資料行進行一次取樣時，此函式就非常實用。

**範例**

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a> <a name="argmax"></a>
### argmin、argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

尋找群組中最小化/最大化 ExprToMaximize 的資料列，並傳回 ExprToReturn 的值 (或 `*` 以傳回整個資料列)。

**秘訣**：傳遞資料行會自動重新命名。若要確定您使用的是正確名稱，請先使用 `take 5` 檢查結果，再將結果以管線傳輸到另一個運算子。

**範例**

針對每個要求名稱顯示最久要求的發生時間︰

    requests | summarize argmax(duration, timestamp) by name

顯示最久要求的所有詳細資料，而非只顯示時間戳記︰

    requests | summarize argmax(duration, *) by name


尋找每個度量的最小值，以及其時間戳記和其他資料︰

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-aggregations/argmin.png)
 


### avg

    avg(Expression)

計算整個群組的 Expression 平均值。

### buildschema

    buildschema(DynamicExpression)

傳回容許 DynamicExpression 之所有值的最小結構描述。

參數資料行類型應該是 `dynamic` - 陣列或屬性包。

**範例**

    exceptions | summarize buildschema(details)

結果︰

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

請注意，`indexer` 是用來標記應該使用數字索引的位置。在此結構描述中，一些有效的路徑會是 (假設這些範例索引在範圍內)︰

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**範例**

假設輸入資料行有三個動態值︰

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


所產生的結構描述將是︰

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

結構描述指出：

* 根物件是具有四個屬性 (名稱分別是 x、y、z 和 t) 的容器。
* 名稱為 "x" 的屬性可以是 "int" 類型或 "string" 類型。
* 名稱為 "y" 的屬性可以是 "double" 類型，或是另一個具有 "w" 屬性且類型為 "string" 的容器。
* ``indexer`` 關鍵字指出 "z" 與 "t" 是陣列。
* "z" 陣列中的每個項目皆為整數或字串。
* "t" 是字串陣列。
* 每個屬性皆可隱含選用，且任何陣列都可以是空的。

##### 結構描述模型

所傳回結構描述的語法如下︰

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
	Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

它們等同於 TypeScript 類型註釋的子集，並編碼為動態值。在 Typescript 中，範例結構描述會是︰

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


### count

    count([ Predicate ])

傳回 Predicate 評估為 `true` 的資料列計數。如果未指定 Predicate，則傳回群組中的記錄總數。

**效能秘訣**︰使用 `summarize count(filter)` 而非 `where filter | summarize count()`

> [AZURE.NOTE] 避免使用 count() 來找出已發生的要求、例外狀況或其他事件的數目。在作業中[取樣](app-insights-sampling.md)時，資料點的數目將少於實際的事件數。請改用 `summarize sum(itemCount)...`。itemCount 屬性會反映由每個保留的資料點所呈現的原始事件數目。
   

### dcount

    dcount( Expression [ ,  Accuracy ])

傳回群組中 Expr 之相異值數目的估計值 (若要列出相異值，請使用 [`makeset`](#makeset))。

Accuracy (若已指定) 會控制速度和精確度之間的平衡。

 * `0` = 最不精確但最快速的計算。
 * `1` 預設值，會平衡精確度和計算時間；大約 0.8% 的誤差。
 * `2` = 最精確但最慢的計算；大約 0.4% 的誤差。

**範例**

    pageViews 
    | summarize countries=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-aggregations/dcount.png)

### makelist

    makelist(Expr [ ,  MaxListSize ] )

傳回群組中 Expr 所有值的 `dynamic` (JSON) 陣列。

* MaxListSize 是所傳回項目數目最大值的選擇性整數限制 (預設值是 *128*)。

### makeset

    makeset(Expression [ , MaxSetSize ] )

傳回 Expr 在群組中取得之一組相異值的 `dynamic` (JSON) 陣列 (秘訣︰若只要計算相異值，請使用 [`dcount`](#dcount))。
  
*  MaxSetSize 是所傳回項目數目最大值的選擇性整數限制 (預設值是 *128*)。

**範例**

    pageViews 
    | summarize countries=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-aggregations/makeset.png)

另請參閱相反函式的 [`mvexpand` 運算子](#mvexpand-operator)。


### max、min

    max(Expr)

計算 Expr 的最大值。
    
    min(Expr)

計算 Expr 的最小值。

**秘訣**︰這可為您提供其本身的最小值或最大值，例如最高或最低單價。但是如果您想要資料列中的其他資料行 (例如，最低價供應商的名稱)，請使用 [argmin 或 argmax](#argmin-argmax)。


<a name="percentile"></a> <a name="percentiles"></a>
### percentile、percentiles

    percentile(Expression, Percentile)

傳回群組中指定百分位數的 Expression 估計值。其精確度取決於百分位數區域中的母體密度。
    
    percentiles(Expression, Percentile1 [ , Percentile2 ] )

和 `percentile()` 一樣，但會計算多個百分位數值 (速度比個別計算每個百分位數還快)。

**範例**


針對每個要求名稱所計算，大於 95% 樣本集和小於 5% 樣本集之 `duration` 的值︰

    request 
    | summarize percentile(duration, 95)
      by name

省略 "by..." 可計算整份資料表。

同時計算不同要求名稱的數個百分位數︰

    
    requests 
    | summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-aggregations/percentiles.png)

結果顯示，針對 /Events/Index 要求，5% 的要求在小於 2.44 秒內回應，有一半在 3.52 秒內，有 5% 超過 6.85 秒。


計算多個統計資料︰

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

##### 百分位數中的估計誤差

百分位數彙總使用 [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf) 提供近似值。

幾個重點如下︰

* 估計誤差的範圍會隨著要求的百分位數值而變化。最佳精確度位於 [0..100] 級別的尾端，百分位數 0 和 100 正好是數據分佈的最小值和最大值。精確度會逐漸向級別中央減少。在中位數時最差，且上限為 %1。 
* 誤差範圍是在秩上測得，而非在值上。假設 percentile(X, 50) 傳回 Xm 的值。此估計可保證最少 49%、最多 51% 的 X 值小於 Xm。Xm 與 X 的實際中位數值之間的差異理論上沒有限制。

### stdev

     stdev(Expr)

傳回 Expr 對群組的標準差。

### variance

    variance(Expr)

傳回 Expr 對群組的變異數。

### sum

    sum(Expr)

傳回 Expr 對群組的總和。


## 純量

[casts](#casts) | [comparisons](#scalar-comparisons) <br/> [gettype](#gettype) | [hash](#hash) | [iff](#iff)| [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull)

支援的類型包括︰

| 類型 | 其他名稱 | 對等的 .NET 類型 |
| --------- | -------------------- | -------------------- |
| `bool` | `boolean` | `System.Boolean` |
| `datetime`| `date` | `System.DateTime` |
| `dynamic` | | `System.Object` |
| `guid` | `uuid`、`uniqueid` | `System.Guid` |
| `int` | | `System.Int32` |
| `long` | | `System.Int64` |
| `double` | `real` | `System.Double` |
| `string` | | `System.String` |
| `timespan`| `time` | `System.TimeSpan` |

### 轉換

您可以從某一類型轉換成另一種類型。一般來說，只要是合理的轉換便行得通︰

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
	toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

### 純量比較

||
---|---
`<` |小於
`<=`|小於或等於
`>` |大於
`>=`|大於或等於
`<>`|Not Equals
`!=`|Not Equals 
`in`| 右運算元是 (動態) 陣列，且左運算元等於右運算元的其中一個項目。
`!in`| 右運算元是 (動態) 陣列，且左運算元不等於右運算元的任何一個項目。




### gettype

**傳回**

表示其單一引數之基礎儲存體類型的字串。當值的種類是 `dynamic` 時特別有用︰在此情況下，`gettype()` 會顯示值的編碼方式。

**範例**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8" (*) `
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`



### 雜湊

**語法**

    hash(source [, mod])

**引數**

* source︰用來計算雜湊的來源純量。
* mod：要套用在雜湊結果的模數值。

**傳回**

所指定純量的 xxhash (長) 值，會對指定的 mod 值計算模數 (如果有指定)。

**範例**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### iff

`iff()` 函式會評估第一個引數 (述詞)，並根據述詞是 `true` 或 `false` 傳回第二個或第三個引數的值。第二個引數和第三個引數的類型必須相同。

**語法**

    iff(predicate, ifTrue, ifFalse)


**引數**

* predicate︰評估為 `boolean` 值的運算式。
* ifTrue：如果 predicate 評估為 `true`，會接受評估並從函式傳回其值的運算式。
* ifFalse：如果 predicate 評估為 `false`，會接受評估並從函式傳回其值的運算式。

**傳回**

如果 predicate 評估為 `true`，此函式會傳回 ifTrue 的值，否則會傳回 ifFalse 的值。

**範例**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a> <a name="isnotnull"/></a> <a name="notnull"/></a>
### isnull、isnotnull、notnull

    isnull(parsejson("")) == true

使用單一引數並指出其是否為 null。

**語法**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**傳回**

true 或 false，取決於值是 null 或不是 null。


|x|isnull(x)
|---|---
| "" | false
|"x" | false
|parsejson("")|true
|parsejson("")|false
|parsejson("{}")|false

**範例**

    T | where isnotnull(PossiblyNull) | count

請注意，還有其他方式能達成此效果︰

    T | summarize count(PossiblyNull)




## Boolean 

### 布林常值

	true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### 布林運算子

	and 
    or 

    

## 數字

[bin](#bin) | [floor](#floor) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### 數值常值

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### 算術運算子

|| |
|---|-------------|
| + | 加 |
| - | 減 | | * | 乘 | | / | 除 | | % | 模數 | || |`<` |小於 |`<=`|小於或等於 |`>` |大於 |`>=`|大於或等於 |`<>`|不等於 |`!=`|不等於




### bin

將值捨入為指定 bin 大小的整數倍數。常用於 [`summarize by`](#summarize-operator) 查詢。如果您有一組零散值，這些值會分組為一組較小的特定值。

別名 `floor`。

**語法**

     bin(value, roundTo)

**引數**

* value︰數字、日期或時間範圍。 
* roundTo：「bin 的大小」。用來分割 value 的數字、日期或時間範圍。 

**傳回**

低於 value 的 roundTo 最接近倍數。
 
    (toint((value/roundTo)-0.5)) * roundTo

**範例**

運算是 | 結果
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`| `datetime(1953-04-15)`


下列運算式會計算值區大小為 1 秒之持續時間的長條圖︰

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### floor

[`bin()`](#bin) 的別名。


### rand

隨機數字產生器。

* `rand()` - 0.0 到 1.0 之間的實數
* `rand(n)` - 0 到 n-1 之間的整數




### sqrt

平方根函式。

**語法**

    sqrt(x)

**引數**

* x：>= 0 的實數。

**傳回**

* `sqrt(x) * sqrt(x) == x` 的正數
* 如果引數為負數或無法轉換為 `real` 值，則為 `null`。 




### toint

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### todouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## 日期和時間


[ago](#ago) | [dayofweek](#dayofweek) | [getmonth](#getmonth)| [getyear](#getyear) | [now](#now) | [startofmonth](#startofmonth) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan)

### 日期和時間常值

|||
---|---
**datetime**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|時間一律是 UTC 格式。省略日期則會提供今天的時間。
`now()`|目前的時間。
`now(`-*timespan*`)`|`now()-`*timespan*
`ago(`*timespan*`)`|`now()-`*timespan*
**timespan**|
`2d`|2 天
`1.5h`|1\.5 小時 
`30m`|30 分鐘
`10s`|10 秒
`0.1s`|0\.1 秒
`100ms`| 100 毫秒
`10microsecond`|
`1tick`|100ns
`time("15 seconds")`|
`time("2")`| 2 天
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### 日期和時間運算式

運算是 |結果
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |小於
`<=`|小於或等於
`>` |大於
`>=`|大於或等於
`<>`|Not Equals
`!=`|Not Equals 




### ago

從目前的 UTC 時鐘時間減去指定的時間範圍。和 `now()` 一樣，此函式可在陳述式中多次使用，而且所參考的 UTC 時鐘時間在所有具現化皆相同。

**語法**

    ago(a_timespan)

**引數**

* a\_timespan︰要從目前的 UTC 時鐘時間 (`now()`) 減去的間隔。

**傳回**

    now() - a_timespan

**範例**

時間戳記為過去一小時的所有資料列︰

```AIQL

    T | where timestamp > ago(1h)
```



### dayofweek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

從上個星期日算起的整數天數，格式為 `timespan`。

**語法**

    dayofweek(a_date)

**引數**

* `a_date`：`datetime`。

**傳回**

上個星期日開始時之午夜起算的 `timespan`，捨入為整數天數。

**範例**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### getmonth

從日期時間取得月份 (1-12)。

**範例**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### getyear

從日期時間取得年度。

**範例**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### now

    now()
    now(-2d)

目前的 UTC 時鐘時間，可選擇性偏移指定的時間範圍。此函式可在陳述式中多次使用，而且所參考的時鐘時間在所有執行個體皆相同。

**語法**

    now([offset])

**引數**

* offset︰加入至目前的 UTC 時鐘時間的 `timespan`。預設值︰0。

**傳回**

目前的 UTC 時鐘時間，格式為 `datetime`。

    now() + offset

**範例**

決定從述詞所識別的事件算起的間隔︰

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

### startofmonth

    startofmonth(date)

包含日期的月份開始。

### startofyear

    startofyear(date)

包含日期的年度開始。


### todatetime

別名 `datetime()`。

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0])  // cast a dynamic type
     todatetime(b.c)   // cast a dynamic type

### totimespan

別名 `timespan()`。

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)


## String

[countof](#countof) | [extract](#extract) | [extractjson](#extractjson) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### 字串常值

規則和在 JavaScript 中相同。

字串可以用單引號或雙引號字元括住。

反斜線 (``) 可用來逸出字元，例如 `\t` (Tab)、`\n` (新行) 和括住引號字元的執行個體。

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double " quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### 模糊字串常值

模糊字串常值是 Analytics 在輸出字串時 (例如在追蹤時) 會模糊處理的字串。模糊化程序會以星號 (`*`) 字元取代所有模糊字元。

若要形成模糊字串常值，請在前面加上 `h` 或 'H'。例如：

```
h'hello'
h@'world' 
h"hello"
```

### 字串比較

運算子|說明|區分大小寫|真實範例
---|---|---|---
`==`|Equals |是| `"aBc" == "aBc"`
`<>`|不等於|是| `"abc" <> "ABC"`
`=~`|Equals |否| `"abc" =~ "ABC"`
`!~`|不等於 |否| `"aBc" !~ "xyz"`
`has`|右側 (RHS) 是左側 (LHS) 中的完整詞彙|否| `"North America" has "america"`
`!has`|RHS 不是 LHS 中的完整詞彙|否|`"North America" !has "amer"` 
`contains` | RHS 是 LHS 的子序列|否| `"FabriKam" contains "BRik"`
`!contains`| RHS 未出現在 LHS|否| `"Fabrikam" !contains "xyz"`
`containscs` | RHS 是 LHS 的子序列|是| `"FabriKam" contains "Kam"`
`!containscs`| RHS 未出現在 LHS|是| `"Fabrikam" !contains "Kam"`
`startswith`|RHS 是 LHS 開頭的子序列。|否|`"Fabrikam" startswith "fab"`
`matches regex`|LHS 包含 RHS 的相符項目|是| `"Fabrikam" matches regex "b.*k"`


如果您要測試是否有完整的語彙詞彙存在，也就是以非英數字元或欄位的開頭或結尾為界限的符號或英數字詞，請使用 `has` 或 `in`。`has` 的執行速度比 `contains` 或 `startswith` 還快。在下面兩個查詢中，第一個的執行速度較快︰

    EventLog | where continent has "North" | count;
	EventLog | where continent contains "nor" | count





### countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

計算子字串在字串中的出現次數。純文字字串的相符項目可能會重疊；regex 的相符項目則不會。

**語法**

    countof(text, search [, kind])

**引數**

* text：字串。
* search︰用來在 text 中進行比對的純文字字串或[規則運算式](app-analytics-reference.md#regular-expressions)。
* kind：`"normal"|"regex"`。預設值 `normal`。 

**傳回**

搜尋字串可在容器中相符的次數。純文字字串的相符項目可能會重疊；regex 的相符項目則不會。

**範例**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (而非 2！)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### extract

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

從文字字串取得[規則運算式](#regular-expressions)的相符項目。(選擇性) 此函式接著會將所擷取的子字串轉換為指定的類型。

**語法**

    extract(regex, captureGroup, text [, typeLiteral])

**引數**

* regex：[規則運算式](#regular-expressions)。
* captureGroup：指出要擷取之擷取群組的正 `int` 常數。0 代表整個相符項目、1 代表規則運算式中第一個 '('括號')' 所相符的值，2 或以上的數字代表後續的括號。
* text：要搜尋的 `string`。
* typeLiteral：選擇性的類型常值 (例如 `typeof(long)`)。如果提供，所擷取的子字串會轉換為此類型。 

**傳回**

如果 regex 在 text 中找到相符項目︰針對指定的擷取群組 captureGroup 進行比對的子字串，可選擇性地轉換為 typeLiteral。

如果沒有相符項目或類型轉換失敗︰`null`。

**範例**

搜尋範例字串 `Trace` 以取得 `Duration` 的定義。相符項目會轉換為 `real`，然後乘以時間常數 (`1s`)，讓 `Duration` 的類型變為 `timespan`。在此範例中，結果等於 123.45 秒︰

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

此範例等同 `substring(Text, 2, 4)`：

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a> <a name="isnotempty"></a> <a name="isempty"></a>
### isempty、isnotempty、notempty

    isempty("") == true

如果引數為空字串或 null，則為 true。另請參閱 [isnull](#isnull)。


**語法**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**傳回**

指出引數為空字串還是 null。

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("")|false
|parsejson("{}")|false


**範例**


    T | where isempty(fieldName) | count




### 取代

用另一個字串取代所有 regex 相符項目。

**語法**

    replace(regex, rewrite, text)

**引數**

* regex：用來搜尋 text 的[規則運算式](https://github.com/google/re2/wiki/Syntax)。它可以在 '('括號')' 中包含擷取群組。 
* rewrite：*matchingRegex* 所找到之任何相符項目的取代 regex。使用 `\0` 來代表整個相符項目、`\1` 來代表第一個擷取群組，`\2` 和以上的數字來代表後續的擷取群組。
* text：字串。

**傳回**

以 rewrite 的評估更換 regex 的所有相符項目後的 text。相符項目不會重疊。

**範例**

此陳述式︰

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

具有下列結果︰

| x | 字串 | 取代後|
|---|---|---|
| 1 | Number is 1.000000 | Number was: 1.000000|
| 2 | Number is 2.000000 | Number was: 2.000000|
| 3 | Number is 3.000000 | Number was: 3.000000|
| 4 | Number is 4.000000 | Number was: 4.000000|
| 5 | Number is 5.000000 | Number was: 5.000000|
 



### split

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

根據指定的分隔符號分割指定字串，並傳回具有所包含子字串的字串陣列。(選擇性) 可在特定子字串存在時將其傳回。

**語法**

    split(source, delimiter [, requestedIndex])

**引數**

* source：將根據指定的分隔符號分割的來源字串。
* delimiter︰將用來分割來源字串的分隔符號。
* requestedIndex︰以零為基礎的選擇性索引 `int`。如果提供，當要求的子字串存在時，傳回的字串陣列將會包含該子字串。 

**傳回**

包含指定來源字串之子字串並以指定分隔符號加以分隔的字串陣列。

**範例**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### strcat

    strcat("hello", " ", "world")

串連 1 到 16 個必須是字串的引數。

### strlen

    strlen("hello") == 5

字串長度。

### substring

    substring("abcdefg", 1, 2) == "bc"

從指定索引開始擷取指定來源字串中的子字串。(選擇性) 您可以指定所要求子字串的長度。

**語法**

    substring(source, startingIndex [, length])

**引數**

* source︰要從中擷取子字串的來源字串。
* startingIndex：所要求子字串的以零為基礎的起始字元位置。
* length：可用來指定子字串中要求之字元數目的選擇性參數。 

**傳回**

指定字串中的子字串。子字串開始於 startingIndex (以零為基礎的) 字元位置，並延續到字串結尾或長度字元 (如果有指定)。

**範例**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### tolower

    tolower("HELLO") == "hello"

將字串轉換為小寫。

### toupper

    toupper("hello") == "HELLO"

將字串轉換為大寫。



## GUID

    guid(00000000-1111-2222-3333-055567f333de)


## 陣列、物件和動態

[literals](#dynamic-literals) | [casting](#casting-dynamic-objects) | [operators](#operators) | [let clauses](#dynamic-objects-in-let-clauses) <br/> [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic)


以下是 Application Insights 例外狀況的查詢結果。`details` 中的值是陣列。

![](./media/app-analytics-scalars/310.png)

**編製索引︰**和在 JavaScript 一樣編制陣列和物件的索引︰

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* 但使用 `arraylength` 和其他 Analytics 函式 (不是 ".length"！)

**轉換** 有時您必須轉換從物件擷取到的項目，因為其類型可能不同。例如，`summarize...to` 就需要特定類型︰

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
    | summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**常值** 若要建立明確的陣列或屬性包物件，請將它撰寫為 JSON 字串並進行轉換︰

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand：**若要分開物件的屬性，使這些屬性各自佔有一個資料列，請使用 mvexpand：

    exceptions | take 1 
    | mvexpand details[0].parsedStack[0]


![](./media/app-analytics-scalars/410.png)


**treepath：**若要尋找複雜物件中的所有路徑︰

    exceptions | take 1 | project timestamp, details 
    | extend path = treepath(details) 
    | mvexpand path


![](./media/app-analytics-scalars/420.png)

**buildschema：**若要尋找可容許資料表中運算式所有值的最小結構描述︰

    exceptions | summarize buildschema(details)

結果︰

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

請注意，`indexer` 是用來標記應該使用數字索引的位置。在此結構描述中，一些有效的路徑會是 (假設這些範例索引在範圍內)︰

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### 陣列和物件常值

若要建立動態常值，請搭配使用 `parsejson` (別名 `todynamic`) 與 JSON 字串引數︰

* `parsejson('[43, 21, 65]')` - 數字陣列
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')` - 包含數字的動態類型單一值
* `parsejson('"21"')` - 包含字串的動態類型單一值

請注意，不同於 JavaScript，JSON 強制在字串兩邊使用雙引號 (`"`)。因此，使用單引號 (`'`) 引述 JSON 編碼的字串常值通常會比較容易。

此範例會建立動態值，然後使用其欄位︰

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### 動態類型的運算子和函式

|||
|---|---|
| *value* `in` *array*| 如果有 array 項目 == value 則為 true<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| 如果沒有 array 項目 == value，則為 true
|[`arraylength(`array`)`](#arraylength)| 如果不是陣列則為 null
|[`extractjson(`path,object`)`](#extractjson)|使用路徑來瀏覽至物件。
|[`parsejson(`source`)`](#parsejson)| 將 JSON 字串變成動態物件。
|[`range(`from,to,step`)`](#range)| 值的陣列
|[`mvexpand` listColumn](app-analytics-queries.md#mvexpand-operator) | 在指定資料格中複寫清單中每個值的資料列。
|[`summarize buildschema(`column`)`](app-analytics-queries.md#summarize-operator) |從資料行內容推斷類型結構描述
|[`summarize makelist(`column`)` ](app-analytics-queries.md#summarize-operator)| 將資料列群組壓平合併，並將資料行的值放在陣列中。
|[`summarize makeset(`column`)`](app-analytics-queries.md#summarize-operator) | 將資料列群組壓平合併，並將資料行的值放在陣列中，但不重複。

### let 子句中的動態物件


[let 子句](app-analytics-queries.md#let-clause)會將動態值儲存為字串，因此下列兩個子句是相等的，兩者在使用前都需要 `parsejson` (或 `todynamic`)︰

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### arraylength

動態陣列中的項目數目。

**語法**

    arraylength(array)

**引數**

* array：`dynamic` 值。

**傳回**

array 中的項目數，如果 array 不是陣列則為 `null`。

**範例**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

使用路徑運算式從 JSON 文字取出指定的項目。此函式可選擇性地將擷取出的字串轉換為特定類型。


**語法**

```

    string extractjson(jsonPath, dataSource)​​ 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))​​
```


**傳回**

此函式會對包含有效 JSON 字串的 dataSource 執行 JsonPath 查詢，並根據第三個引數選擇性地將該值轉換成另一種類型。



**範例**

[方括號] 標記法和點標記法是相等的︰

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**效能秘訣**

* 先套用 where 子句，再使用 `extractjson()`
* 請考慮改為搭配使用規則運算式相符項目與 [extract](#extract)。如果 JSON 是從範本產生，這麼做可以執行的非常快並且有效。
* 如果您需要從 JSON 中擷取不只一個值，請使用 `parsejson()`。
* 請考慮在擷取 JSON 時透過將資料行的類型宣告為動態以剖析 JSON。

### JSON 路徑運算式

|||
|---|---|
|`$`|根物件|
|`@`|目前的物件|
|`[0]`|陣列註標|
|`.` 或 `[0]` | 子系|

*(我們目前未實作萬用字元、遞迴、聯集或配量。)*




### parsejson

將 `string` 解譯為 [JSON 值](http://json.org/)，並以 `dynamic` 類型傳回值。當您需要從 JSON 複合物件擷取多個項目時，使用 `extractjson()` 會更合適。

**語法**

    parsejson(json)

**引數**

* json：JSON 文件。

**傳回**

json 所指定類型為 `dynamic` 的物件。

**範例**

在下列範例中，當 `context_custom_metrics` 是類似下面內容的 `string` 時︰

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

則下列片段會擷取物件中 `duration` 位置的值，並從中擷取兩個位置：`duration.value` 和 `duration.min` (分別為 `118.0` 和 `110.0`)。

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



#### range

`range()` 函式 (請勿與 `range` 運算子混淆) 會產生保有一系列等間距值的動態陣列。

**語法**

    range(start, stop, step)

**引數**

* start：所產生陣列中第一個項目的值。 
* stop︰所產生陣列中最後一個項目的值，或所產生陣列中大於最後一個項目，且位於從 start 算起整數倍數的 step 內的最小值。
* step︰陣列的兩個連續項目之間的差異。

**範例**

下列範例會傳回 `[1, 4, 7]`：

```AIQL
range(1, 8, 3)
```

下列範例會傳回保有 2015 年所有天數的陣列︰

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

將字串轉換為動態值。

### treepath

    treepath(dynamic_object)

列舉所有可識別動態物件中分葉的路徑運算式。

**傳回**

路徑運算式的陣列。

**範例**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

請注意，"[0]" 表示陣列存在，但未指定特定路徑所用的索引。



[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->