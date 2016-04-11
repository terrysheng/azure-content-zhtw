<properties 
	pageTitle="Application Insights 之 Analytics 的純量運算式" 
	description="Analytics 中的數字、字串、動態運算式和類型，強大的 Application Insights 搜尋工具。" 
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
	ms.date="03/21/2016" 
	ms.author="awills"/>

 
# Analytics 的純量運算式


[Analytics](app-analytics.md) 可讓您從您的應用程式透過 [Application Insights](app-insights-overview.md) 所收集的遙測資料執行強大的查詢。這些頁面說明其查詢語言。

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

---

[ago](#ago) | [arraylength](#arraylength) | [bin](#bin) [countof](#countof) | [dayofweek](#dayofweek) | [extract](#extract) | [extractjson](#extractjson) | [floor](#floor)
<br/>[getmonth](#getmonth) | [gettype](#gettype) [getyear](#getyear) | [hash](#hash) | [iff](#iff) | [isempty](#isempty) | [isnotempty](#isnotempty) | [isnull](#isnull) | [isnotnull](#isnotnull)
<br/> [now](#now) | [notempty](#notempty) | [notnull](#notnull) | [parsejson](#parsejson)| [rand](#rand) | [range](#range) | [replace](#replace) | [split](#split) | [sqrt](#sqrt)
<br/>[startofmonth](#startofmonth) | [startofyear](#startofyear) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [toupper](#toupper) | [treepath](#treepath)

---



「純量」是指可在資料表中佔有單一資料格的數字或字串之類的值。純量運算式是從純量函式和運算子建置而來，並且會評估為純量值。`sqrt(score)/100 > target+2` 便是純量運算式。

「純量」也包含陣列和複合物件，兩者也都能儲存在單一資料庫的資料格中。

純量運算式不同於[查詢](app-analytics-queries.md)，後者的結果為資料表。

## 純量

[casts](#casts) | [comparisons](#scalar-comparisons)
<br/> [gettype](#gettype) | [hash](#hash) | [iff](#iff)| [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull)

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

傳回

表示其單一引數之基礎儲存體類型的字串。當值的種類是 `dynamic` 時特別有用︰在此情況下，`gettype()` 會顯示值的編碼方式。

範例

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

語法

    hash(source [, mod])

引數

* source︰用來計算雜湊的來源純量。
* mod：要套用在雜湊結果的模數值。

傳回

所指定純量的 xxhash (長) 值，會對指定的 mod 值計算模數 (如果有指定)。

範例

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### iff

`iff()` 函式會評估第一個引數 (述詞)，並根據述詞是 `true` 或 `false` 傳回第二個或第三個引數的值。第二個引數和第三個引數的類型必須相同。

語法

    iff(predicate, ifTrue, ifFalse)


引數

* predicate︰評估為 `boolean` 值的運算式。
* ifTrue：如果 predicate 評估為 `true`，會接受評估並從函式傳回其值的運算式。
* ifFalse：如果 predicate 評估為 `false`，會接受評估並從函式傳回其值的運算式。

傳回

如果 predicate 評估為 `true`，此函式會傳回 ifTrue 的值，否則會傳回 ifFalse 的值。

範例

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>
### isnull、isnotnull、notnull

    isnull(parsejson("")) == true

使用單一引數並指出其是否為 null。

語法


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

傳回

true 或 false，取決於值是 null 或不是 null。


|x|isnull(x)
|---|---
| "" | false
|"x" | false
|parsejson("")|true
|parsejson("[]")|false
|parsejson("{}")|false

範例

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

[bin](#bin) | [floor](#floor) | [rand](#rand) | [range](#range) | [sqrt](#sqrt)
|[todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### 數值常值

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### 算術運算子

|| |
|---|-------------|
| + | 加 |
| - | 減 |
| * | 乘 |
| / | 除 |
| % | 模數 |
||
|`<` |小於
|`<=`|小於或等於
|`>` |大於
|`>=`|大於或等於
|`<>`|不等於
|`!=`|不等於




### bin

將值捨入為指定 bin 大小的整數倍數。常用於 [`summarize by`](app-analytics-queries.md#summarize-operator) 查詢。如果您有一組零散值，這些值會分組為一組較小的特定值。

別名 `floor`。

語法

     bin(value, roundTo)

引數

* value︰數字、日期或時間範圍。 
* roundTo：「bin 的大小」。用來分割 value 的數字、日期或時間範圍。 

傳回

低於 value 的 roundTo 最接近倍數。
 
    (toint((value/roundTo)-0.5)) * roundTo

範例

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

語法

    sqrt(x)

引數

* x：> = 0 的實數。

傳回

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
datetime|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|時間一律是 UTC 格式。省略日期則會提供今天的時間。
`now()`|目前的時間。
`now(`-timespan`)`|`now()-`*timespan*
`ago(`*timespan*`)`|`now()-`*timespan*
timespan|
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

語法

    ago(a_timespan)

引數

* a\_timespan︰要從目前的 UTC 時鐘時間 (`now()`) 減去的間隔。

傳回

    now() - a_timespan

範例

時間戳記為過去一小時的所有資料列︰

```AIQL

    T | where timestamp > ago(1h)
```



### dayofweek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

從上個星期日算起的整數天數，格式為 `timespan`。

語法

    dayofweek(a_date)

引數

* `a_date`：`datetime`。

傳回

上個星期日開始時之午夜起算的 `timespan`，捨入為整數天數。

範例

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### getmonth

從日期時間取得月份 (1-12)。

範例

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### getyear

從日期時間取得年度。

範例

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### now

    now()
    now(-2d)

目前的 UTC 時鐘時間，可選擇性偏移指定的時間範圍。此函式可在陳述式中多次使用，而且所參考的時鐘時間在所有執行個體皆相同。

語法

    now([offset])

引數

* offset︰加入至目前的 UTC 時鐘時間的 `timespan`。預設值︰0。

傳回

目前的 UTC 時鐘時間，格式為 `datetime`。

    now() + offset

範例

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

反斜線 (`\`) 可用來逸出字元，例如 `\t` (Tab)、`\n` (新行) 和括住引號字元的執行個體。

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


如果您要測試是否有完整的語彙詞彙存在，也就是以非英數字元或欄位的開頭或結尾為界限的符號或英數字元，請使用 `has` 或 `in`。`has` 的執行速度比 `contains` 或 `startswith` 還快。在下面兩個查詢中，第一個的執行速度較快︰

    EventLog | where continent has "North" | count;
	EventLog | where continent contains "nor" | count





### countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

計算子字串在字串中的出現次數。純文字字串的相符項目可能會重疊；regex 的相符項目則不會。

語法

    countof(text, search [, kind])

引數

* text：字串。
* search︰用來在 text 中進行比對的純文字字串或[規則運算式](app-analytics-reference.md#regular-expressions)。
* kind：`"normal"|"regex"`。預設值 `normal`。 

傳回

搜尋字串可在容器中相符的次數。純文字字串的相符項目可能會重疊；regex 的相符項目則不會。

範例

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

從文字字串取得[規則運算式](app-analytics-reference.md#regular-expressions)的相符項目。(選擇性) 此函式接著會將所擷取的子字串轉換為指定的類型。

語法

    extract(regex, captureGroup, text [, typeLiteral])

引數

* regex：[規則運算式](app-analytics-reference.md#regular-expressions)。
* captureGroup：指出要擷取之擷取群組的正 `int` 常數。0 代表整個相符項目、1 代表規則運算式中第一個 '('括號')' 所相符的值，2 或以上的數字代表後續的括號。
* text：要搜尋的 `string`。
* typeLiteral：選擇性的類型常值 (例如 `typeof(long)`)。如果提供，所擷取的子字串會轉換為此類型。 

傳回

如果 regex 在 text 中找到相符項目︰針對指定的擷取群組 captureGroup 進行比對的子字串，可選擇性地轉換為 typeLiteral。

如果沒有相符項目或類型轉換失敗︰`null`。

範例

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

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>
### isempty、isnotempty、notempty

    isempty("") == true

如果引數為空字串或 null，則為 true。
另請參閱 [isnull](#isnull)。


語法

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

傳回

指出引數為空字串還是 null。

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("[]")|false
|parsejson("{}")|false


範例


    T | where isempty(fieldName) | count




### 取代

用另一個字串取代所有 regex 相符項目。

語法

    replace(regex, rewrite, text)

引數

* regex：用來搜尋 text 的[規則運算式](https://github.com/google/re2/wiki/Syntax)。它可以在 '('括號')' 中包含擷取群組。 
* rewrite：*matchingRegex* 所找到之任何相符項目的取代 regex。使用 `\0` 來代表整個相符項目、`\1` 來代表第一個擷取群組，`\2` 和以上的數字來代表後續的擷取群組。
* text：字串。

傳回

以 rewrite 的評估更換 regex 的所有相符項目後的 text。相符項目不會重疊。

範例

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

語法

    split(source, delimiter [, requestedIndex])

引數

* source：將根據指定的分隔符號分割的來源字串。
* delimiter︰將用來分割來源字串的分隔符號。
* requestedIndex︰以零為基礎的選擇性索引 `int`。如果提供，當要求的子字串存在時，傳回的字串陣列將會包含該子字串。 

傳回

包含指定來源字串之子字串並以指定分隔符號加以分隔的字串陣列。

範例

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

語法

    substring(source, startingIndex [, length])

引數

* source︰要從中擷取子字串的來源字串。
* startingIndex：所要求子字串的以零為基礎的起始字元位置。
* length：可用來指定子字串中要求之字元數目的選擇性參數。 

傳回

指定字串中的子字串。子字串開始於 startingIndex (以零為基礎的) 字元位置，並延續到字串結尾或長度字元 (如果有指定)。

範例

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


## 陣列和物件 - 動態類型

[literals](#dynamic-literals) | [casting](#casting-dynamic-objects) | [operators](#operators) | [let clauses](#dynamic-objects-in-let-clauses)
 <br/> [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic)


以下是 Application Insights 例外狀況的查詢結果。`details` 中的值是陣列。

![](./media/app-analytics-scalars/310.png)

編製索引︰和在 JavaScript 一樣編制陣列和物件的索引︰

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* 但使用 `arraylength` 和其他 Analytics 函式 (不是 ".length"！)

轉換 有時您必須轉換從物件擷取到的項目，因為其類型可能不同。例如，`summarize...to` 就需要特定類型︰

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
    | summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

常值 若要建立明確的陣列或屬性包物件，請將它撰寫為 JSON 字串並進行轉換︰

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


mvexpand：若要分開物件的屬性使這些屬性各自佔有一個資料列，請使用 mvexpand：

    exceptions | take 1 
    | mvexpand details[0].parsedStack[0]


![](./media/app-analytics-scalars/410.png)


treepath：若要尋找複雜物件中的所有路徑︰

    exceptions | take 1 | project timestamp, details 
    | extend path = treepath(details) 
    | mvexpand path


![](./media/app-analytics-scalars/420.png)

buildschema：若要尋找可容許資料表中運算式所有值的最小結構描述︰

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


<a name="operators"></a>
### 動態類型的運算子和函式

|||
|---|---|
| value `in` array| 如果有 array 項目 == value 則為 true<br/>`where City in ('London', 'Paris', 'Rome')`
| value `!in` array| 如果沒有 array 項目 == value，則為 true
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

語法

    arraylength(array)

引數

* array：`dynamic` 值。

傳回

array 中的項目數，如果 array 不是陣列則為 `null`。

範例

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


語法

```

    string extractjson(jsonPath, dataSource)​​ 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))​​
```


傳回

此函式會對包含有效 JSON 字串的 dataSource 執行 JsonPath 查詢，並根據第三個引數選擇性地將該值轉換成另一種類型。



範例

[方括號] 標記法和點標記法是相等的︰

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...

#### JSON 路徑運算式

|||
|---|---|
|`$`|根物件|
|`@`|目前的物件|
|`.` 或 `[ ]` | 子系|
|`[ ]`|陣列註標|

(我們目前未實作萬用字元、遞迴、聯集或配量。)


效能秘訣

* 先套用 where 子句再使用 `extractjson()`
* 請考慮改為搭配使用規則運算式相符項目與 [extract](#extract)。如果 JSON 是從範本產生，這麼做可以執行的非常快並且有效。
* 如果您需要從 JSON 中擷取不只一個值，請使用 `parsejson()`。
* 請考慮在擷取 JSON 時透過將資料行的類型宣告為動態以剖析 JSON。



### parsejson

將 `string` 解譯為 [JSON 值](http://json.org/)，並以 `dynamic` 類型傳回值。當您需要從 JSON 複合物件擷取多個項目時，使用 `extractjson()` 會更合適。

語法

    parsejson(json)

引數

* json：JSON 文件。

傳回

json 所指定類型為 `dynamic` 的物件。

範例

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



### range

`range()` 函式 (請勿與 `range` 運算子混淆) 會產生保有一系列等間距值的動態陣列。

語法

    range(start, stop, step)

引數

* start：所產生陣列中第一個項目的值。 
* stop︰所產生陣列中最後一個項目的值，或所產生陣列中大於最後一個項目，且位於從 start 算起整數倍數的 step 內的最小值。
* step︰陣列的兩個連續項目之間的差異。

範例

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

傳回

路徑運算式的陣列。

範例

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

請注意，"[0]" 表示陣列存在，但未指定特定路徑所用的索引。



[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->