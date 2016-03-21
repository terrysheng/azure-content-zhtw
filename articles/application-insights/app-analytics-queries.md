<properties 
	pageTitle="Application Insights Analytics 中的運算子和查詢" 
	description="Application Insights Analytics (適用於 Application Insights 的強大搜尋工具) 中用來進行查詢的運算子參考。" 
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
	ms.date="03/07/2016" 
	ms.author="awills"/>



# Application Insights Analytics 中的查詢語法


[Application Insights Analytics](app-analytics.md) 是強大的搜尋引擎，適合用於 [Application Insights](app-insights-overview.md) 遙測。這些頁面說明 Application Insights Analytics 查詢語言 AIQL。


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

遙測的查詢是由來源串流參考後接篩選條件管線所組成。例如：


```AIQL
requests
| where client_City == "London" and timestamp > ago(3d)
| count
```
    
每個以縱線字元 `|` 做為前置詞的篩選條件即為含有一些參數的運算子執行個體。前面管線所得到的結果會以資料表的形式做為運算子的輸入。大部分情況下，任何參數皆是輸入資料行的[純量運算式](app-analytics-scalars.md)。但在某些情況下，參數是輸入資料行的名稱或是第二個資料表。查詢的結果永遠是資料表，即使它只有一個資料行和一個資料列。

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

## count 運算子

`count` 運算子會傳回輸入記錄集內的記錄 (資料列) 數目。

語法

    T | count

引數

* T︰要計算記錄數目的表格式資料。

傳回

此函式會傳回含有一筆記錄且資料行類型為 `long` 的資料表。唯一資料格的值是 T 中的記錄數目。

範例

```AIQL
requests | count
```



## extend 運算子

     T | extend duration = stopTime - startTime

在資料表中附加一個或多個經過計算的資料行。


語法

    T | extend ColumnName = Expression [, ...]

引數

* T：輸入資料表。
* ColumnName：要新增的資料行名稱。 
* Expression︰現有資料行的計算。

傳回

輸入資料表的複本，其中含有指定的其他資料行。

秘訣

* 如果您也想要捨棄或重新命名某些資料行，請改用 [`project`](#project-operator)。
* 請勿只是為了讓名稱縮短以利用於長運算式中就使用 `extend`。`...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    資料表原本的資料行已編制索引，但縮短後的新名稱會另外定義尚未編製索引的資料行，而可能導致查詢的執行速度變慢。

範例

```AIQL
traces
| extend
    Age = now() - timestamp
```


## join 運算子

    Table1 | join (Table2) on CommonColumn

藉由比對指定資料行的值來合併兩個資料表的資料列。


語法

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

引數

* Table1 - 聯結的「左側」。
* Table2 - 聯結的「右側」。其可以是會輸出資料表的巢狀查詢運算式。
* CommonColumn - 在兩個資料表中的名稱皆相同的資料行。
* Kind - 指定兩個資料表中的資料列比對方式。

傳回

含有下列項目的資料表︰

* 兩個資料表中的每個資料行各自佔據一個資料行，包括用來比對的索引鍵。如果名稱有衝突，右側的資料行會自動重新命名。
* 輸入資料表間的每個相符項目各自佔據一個資料列。其中一個資料表中選取的資料列如果和另一個資料表中的資料列在所有 `on` 欄位的值皆相同，就代表是相符項目。 

* `Kind` (未指定)

    左側只會有一個資料列符合 `on` 索引鍵的每個值。此資料列與右側資料列的每個相符項目都會在輸出中佔有一個資料列。

* `Kind=inner`
 
     左側和右側相符資料列的每個組合都會在輸出中佔有一個資料列。

* `kind=leftouter` (或 `kind=rightouter` 或 `kind=fullouter`)

     除了內部的相符項目，左側 (和/或右側) 的每個資料列即使不相符也會佔有一個資料列。在此情況下，不相符的輸出資料格會包含 null。

* `kind=leftanti`

     傳回左側中與右側不相符的所有記錄。結果資料表中只有左側的資料行。
 
如果好幾個資料列在這些欄位具有相同的值，每一個組合都會佔有一個資料列。

秘訣

若要獲得最佳效能︰

* 在 `join` 前面使用 `where` 和 `project` 以減少輸入資料表中的資料列和資料行數目。 
* 如果某個資料表一定會比另一個資料表還小，請將它做為聯結的左側 (管線)。
* 聯結相符項目的資料行必須具有相同名稱。如果必須重新命名其中一個資料表中的資料行，請使用 project 運算子。

範例

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

[關於聯結類別](app-analytics-samples.md#join-flavors)。

## let 子句

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

* type：`bool`、`int`、`long`、`double`、`string`、`timespan`、`datetime`、`guid`、[`dynamic`](app-analytics-scalars.md#dynamic-type)
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

## limit 運算子

     T | limit 5

傳回輸入資料表中的資料列，最多可傳回所指定的數目。此運算子無法保證會傳回哪些記錄 (若要傳回特定記錄，請使用 [`top`](#top-operator))。

別名 `take`

語法

    T | limit NumberOfRows


秘訣

當您在進行互動工作時，`Take` 可讓您輕鬆、有效率地查看結果的樣本。但請注意，它無法保證一定會產生任何特定資料列，或以任何特定順序產生資料列。

即使您不使用 `take`，傳回給用戶端的資料列數目還是會受到隱含限制。若要提高此限制，請使用 `notruncation` 用戶端要求選項。



## mvexpand 運算子

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

請參閱[將作用中活動在不同時間的計數製成圖表](app-analytics-samples.md#concurrent-activities)。


## parse 運算子

    T | parse "I am 63 next birthday" with "I am" Year:int "next birthday"

    T | parse kind=regex "My 62nd birthday" 
        with "My" Year:regex("[0..9]+") regex("..") "birthday"

從字串中擷取值。可使用簡單比對或規則運算式比對。

`with` 子句中的項目會依序和來源字串進行比對。每個項目都會擷取一段來源文字。如果是純文字字串，比對游標會一直往後移動到相符項目的位置。如果是含有類型名稱的資料行，游標會往後移動到足以剖析指定類型的位置 (字串相符項目會一直往後移動到找到下一個元素的相符項目為止)。 如果是 regex，則會比對規則運算式 (而且所產生的資料行一律具有字串類型)。

語法

    T | parse StringExpression with [SimpleMatch | Column:Type] ...

    T | parse kind=regex StringExpression 
        with [SimpleMatch | Column : regex("Regex")] ...

引數

* T：輸入資料表。
* kind︰simple 或 regex。預設值是 simple。
* StringExpression：評估為屬於字串或可以轉換為字串的運算式。
* SimpleMatch：符合文字的下一個部分的字串。
* Column︰指定要指派比對的新資料行。
* Type︰指定如何剖析來源字串的下一個部分。
* Regex：用來比對字串的下一個部分的規則運算式。 

傳回

會根據資料行清單擴充的輸入資料表。


範例

`parse` 運算子透過在相同的 `string` 運算式上使用多個 `extract` 應用程式來簡化資料表的 `extend`。當資料表的 `string` 資料行有好幾個值，而您想要將這些值個別分割到一個資料行時，最適合使用此運算子，開發人員追蹤 ("`printf`"/"`Console.WriteLine`") 陳述式所產生的資料行便是一例。

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


## project 運算子

    T | project cost=price*quantity, price

選取要納入、重新命名或捨棄的資料行，以及插入新的計算資料行。結果中的資料行順序是由引數順序來指定。只有引數中指定的資料行才會包含在結果中︰輸入中的其他任何資料行則會遭到捨棄。(另請參閱 `extend`。)


語法

    T | project ColumnName [= Expression] [, ...]

引數

* T：輸入資料表。
* ColumnName：要出現在輸出中的資料行名稱。如果沒有任何運算式，則該名稱的資料行必須出現在輸入中。 
* Expression︰參考輸入資料行的選擇性純量運算式。 

    所傳回的新計算資料行名稱可以和輸入中的現有資料行同名。

傳回

具有命名為引數的資料行，且資料列數目和輸入資料表相同的資料表。

範例

下列範例示範幾種可以使用 `project` 運算子完成的操作。輸入資料表 `T` 有三個類型為 `int` 的資料行：`A`、`B` 和 `C`。

```AIQL
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```


[更多範例](app-analytics-samples.md#activities)。


## range 運算子

    range LastWeek from ago(7d) to now() step 1d

產生單一資料行的值資料表。請注意它沒有管線輸入。

|LastWeek|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|



語法

    range ColumnName from Start to Stop step Step

引數

* ColumnName：輸出資料表中的單一資料行名稱。
* Start︰輸出中的最小值。
* Stop︰輸出中產生的最大值 (或最大值界限，如果 step 跨越此值)。
* Step︰兩個連續值之間的差異。 

引數必須是數字、日期或時間範圍值。引數不能參考任何資料表的資料行 (如果您想要根據輸入資料表計算範圍，請使用 [range 函式](app-analytics-scalars.md#range)，或許再搭配 [mvexpand 運算子](#mvexpand-operator))。

傳回

具有名稱為 ColumnName 之單一資料行的資料表，其值為 Start、Start + Step、...，直到 Stop (含)。

範例

```AIQL
range Steps from 1 to 8 step 3
```

具有名稱為 `Steps` 之單一資料行的資料表，其類型為 `long`，其值則為 `1`、`4` 和 `7`。

範例

    range LastWeek from bin(ago(7d),1d) to now() step 1d

過去七天的午夜資料表。bin(floor) 函式會逐次減少至一天的開始。

範例

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

## reduce 運算子

    exceptions | reduce by outerMessage

嘗試將相似的記錄群組在一起。對於每個群組，運算子會輸出其認定最能描述該群組的 `Pattern`，以及該群組中的記錄 `Count`。


![](./media/app-analytics-queries/reduce.png)

語法

    T | reduce by  ColumnName [ with threshold=Threshold ]

引數

* ColumnName：要檢查的資料行。它必須是字串類型。
* Threshold：範圍 {0..1} 內的值。預設值為 0.001。若為大型輸入，臨界值應該小一點。 

傳回

兩個資料行，分別是 `Pattern` 和 `Count`。許多時候，Pattern 會是資料行中的完整值。它有時可以識別常見詞彙，並將變動部分取代為 '*'。

例如，`reduce by city` 的結果可能包括︰

|模式 | Count |
|---|---|
| San * | 5182 |
| Saint * | 2846 |
| Moscow | 3726 |
| * -on- * | 2730 |
| 巴黎 | 27163 |




## sort 運算子 

    T | sort by country asc, price desc

按照一個或多個資料行的順序排序輸入資料表的資料列。

別名 `order`

語法

    T  | sort by Column [ asc | desc ] [ `,` ... ]

引數

* T：要排序的輸入資料表。
* Column︰做為排序依據之 T 的資料行。值的類型必須是數值、日期、時間或字串。
* `asc` 按照遞增順序由低至高排序。預設值是 `desc`，由高遞減至低。

範例

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Traces 資料表中具有特定 `ActivityId` 的所有資料列，按其時間戳記排序。

## summarize 運算子

產生資料表來彙總輸入資料表的內容。
 
    requests
	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

顯示每個國家/地區之數目、平均要求持續時間和一組城市的資料表。每個不同的國家/地區在輸出中會各佔據一個資料列。輸出資料行會顯示計數、平均持續時間、城市和國家/地區。其他所有輸入資料行則會遭到忽略。


    T | summarize count() by price_range=bin(price, 10.0)

顯示有多少項目的價格落在 [0,10.0]、[10.0,20.0] 等依此類推的間隔中的資料表。此範例有一個用於放置計數的資料行，以及一個用於放置價格範圍的資料行。其他所有輸入資料行則會遭到忽略。

[更多範例](app-analytics-aggregations.md)。



語法

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

引數

* Column︰結果資料行的選擇性名稱。預設值為衍生自運算式的名稱。
* Aggregation︰`count()` 或 `avg()` 等[彙總函式](app-analytics-aggregations.md)的呼叫，以資料行名稱做為引數。請參閱[彙總函式清單](app-analytics-aggregations.md)。
* GroupExpression：可提供一組相異值的資料行運算式。它通常是已提供一組受限值的資料行名稱，或是以數值或時間資料行做為引數的 `bin()`。 

如果您提供不使用 `bin()` 的數值或時間運算式，AI Analytics 會自動為它套用 `1h` 間隔的時間，或 `1.0` 的數字。

如果您沒有提供 GroupExpression，整份資料表會彙整在單一輸出資料列。



傳回

輸入資料列會各自分組到具有相同 `by` 運算式值的群組。然後指定的彙總函式會針對每個群組進行計算，以便為每個群組產生資料列。結果會包含 `by` 資料行，而且每個經過計算的彙總至少會佔有一個資料行 (某些彙總函式會傳回多個資料行)。

`by` 值有多少不同組合，結果就會有多少資料列。如果您想要彙總數值範圍，請使用 `bin()` 將範圍減少為離散值。

注意

雖然您可以為彙總與群組運算式提供任意運算式，但更有效率的方法是使用簡單的資料行名稱，或對數值資料行套用 `bin()`。



## take 運算子

[limit](#limit-operator) 的別名


## top 運算子

    T | top 5 by Name desc

傳回按指定資料行排序的前 N 個記錄。


語法

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [, ... ]

引數

* NumberOfRows：要傳回之 T 的資料列數目。
* Sort\_expression：用來排序資料列的運算式。它通常只是資料行名稱。您可以指定多個 sort\_expression。
* `asc` 或 `desc` (預設值) 可能會出現，以控制實際上是從範圍的「下限」或「上限」進行選取。


秘訣

`top 5 by name` 表面上相當於 `sort by name | take 5`。不過，前者的執行速度較快而且一定會傳回排序後的結果，而 `take` 則不一定如此。


## union 運算子

     Table1 | union Table2, Table3

取得兩個或以上的資料表並傳回這些資料表中的資料列。

語法

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

引數

* Table1、Table2 ...
 *  資料表名稱，例如 `events`；或
 *  查詢運算式，例如 `(events | where id==42)`
 *  使用萬用字元指定的一組資料表。例如，`E*` 會形成資料庫中名稱開頭為 `E` 之所有資料表的聯集。
* `kind`： 
 * `inner` - 結果中會有所有輸入資料表共有之資料行的子集。
 * `outer` - 結果中會有任何輸入中出現的所有資料行。輸入資料列未定義的資料格會設為 `null`。
* `withsource=`*ColumnName：*如果指定，輸出中會包含名稱為 ColumnName 的資料行，其值會指出哪一個來源資料表貢獻了每個資料列。

傳回

所含資料列數目和所有輸入資料表中的資料列數目一樣多的資料表。

範例

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
名稱開頭為 "tt" 的所有資料表的聯集。


範例

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

## where 運算子

     T | where fruit=="apple"

篩選資料表以建立滿足述詞的資料列子集。

別名 `filter`

語法

    T | where Predicate

引數

* T︰要篩選記錄的表格式輸入。
* Predicate︰T 之資料行的 `boolean` [運算式](app-analytics-scalars.md#boolean)。它會針對 T 中的每個資料列進行評估。

傳回

Predicate 是 `true` 之 T 中的資料列。

秘訣

若要取得最快效能︰

* 在資料行名稱和常數之間使用簡單比較 ('Constant' 表示資料表常數，因此 `now()` 和 `ago()` 都沒問題，並且是使用 [`let` 陳述式](app-analytics-syntax.md#let-statements)指派的純量值)。

    例如，`where Timestamp >= ago(1d)` 比 `where floor(Timestamp, 1d) == ago(1d)` 更好。

* 最簡單的詞彙優先︰如果您使用 `and` 連結多個子句，請先放置只包含一個資料行的子句。因此 `Timestamp > ago(1d) and OpId == EventId` 比反過來要好。


範例

```AIQL
Traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

不超過 1 小時、來自稱為 "Kuskus" 的來源，而且有兩個相同值之資料行的記錄。

請注意，我們將兩個資料行之間的比較放在最後，因為它不能利用索引和強制執行掃描。





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->