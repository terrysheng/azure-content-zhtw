<properties 
	pageTitle="Application Insights - Analytics 中的查詢範例" 
	description="Application Insights - Analytics 中的查詢範例，適用於 Application Insights 的強大搜尋工具。" 
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

# Application Insights 的 Analytics 範例

[Analytics](app-analytics.md) 可讓您從您的應用程式透過 [Application Insights](app-insights-overview.md) 所收集的遙測資料執行強大的查詢。這些頁面說明其查詢語言。另外還有[語言教學](app-analytics-tour.md)，建議您從這裡開始。


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## 查詢最佳做法 

有數個您可以遵循的建議及避免事項，讓您更快速執行查詢。

建議：

-	先使用時間篩選器。Application Insights - Analytics 已針對使用時間篩選器高度最佳化。
-	在查詢的開始處 (緊接時間篩選器之後) 放置預期會去除大多數資料的篩選器
-	檢查大部分的篩選器是否有在查詢開始時 (在您開始使用 'extend' 之前) 顯示 
-	尋找完整權杖時盡量透過 'has' 關鍵字而非 'contains'。'has' 具有較好的效能，因為它不需要查詢子字串。
-	盡量在特定的資料行中尋找而不要使用 '*' (全文搜索所有資料行)
-	使用聯結時 - 只從聯結兩側投影所需的資料行 (這可以減少將承載從某部電腦提取至另一部電腦)

避免：

-	嘗試新查詢，而不在結尾使用 'limit [small number]' 或 'count'。在未知的資料集上執行未繫結的查詢可能會產生以 GB 為單位的結果傳回到用戶端，導致回應緩慢及叢集忙碌。
-	如果您發現正在超過 1B 以上的記錄套用轉換 (JSON、字串等) - 請重新調整您的查詢以減少送入轉換的資料量





<a name="activities"></a>
## 從開始和停止事件取得工作階段

假設我們有一事件記錄檔，其中某些事件標記了擴充的活動或工作階段的開始或結束。

|名稱|City|SessionId|Timestamp|
|---|---|---|---|
|啟動|倫敦|2817330|2015-12-09T10:12:02.32|
|遊戲|倫敦|2817330|2015-12-09T10:12:52.45|
|啟動|曼徹斯特|4267667|2015-12-09T10:14:02.23|
|停止|倫敦|2817330|2015-12-09T10:23:43.18|
|取消|曼徹斯特|4267667|2015-12-09T10:27:26.29|
|停止|曼徹斯特|4267667|2015-12-09T10:28:31.72|

每個事件都有 SessionId，因此符合開始和停止事件的問題就會有相同的識別碼。

```AIQL
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on activityId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

使用 [`let`](app-analytics-syntax.md#let-statements) 命名資料表的投射，該投影會在進入聯結之前盡可能地精簡。[`Project`](app-analytics-queries.md#project-operator) 用於變更時間戳記的名稱，以便開始及結束時間可以同時出現在結果中。它也會選取我們要在結果中看到的其他資料行。[`join`](app-analytics-queries.md#join-operator) 會針對相同活動配對開始及結束項目，為每個活動建立資料列。最後，`project` 會再次加入資料行以顯示活動的持續時間。


|City|SessionId|StartTime|StopTime|持續時間|
|---|---|---|---|---|
|倫敦|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|曼徹斯特|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### 取得工作階段 (不使用工作階段識別碼)

現在，讓我們假設開始與結束事件沒有方便我們配對的工作階段識別碼。但我們有工作階段執行位置的用戶端 IP 位址。假設每個用戶端位址一次只會產生一個工作階段，我們可以將來自相同 IP 位址的每個開始事件與下一個結束事件配對。

```AIQL
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize argmin(duration, *) by bin(StartTime,1s), ClientIp
```

聯結會將來自相同用戶端 IP 位址的開始時間與所有的停止時間配對。因此我們先移除與較早停止時間的配對。

然後以開始時間和 IP 來分組，來取得每個工作階段的群組。必須為 StartTime 參數提供 `bin` 函式：如果沒有，Analytics 將會自動使用 1 小時量化，這會將部分開始時間與錯誤的停止時間配對。

`argmin` 會挑選出每個群組中含有最短持續時間的資料列，而 `*` 參數會傳遞所有其他的資料行，但會在每個資料行名稱前加上 "min\_" 前置詞。


![](./media/app-analytics-samples/040.png)

接著我們可以加上一些程式碼，以方便調整大小的量化來計算持續時間。我們比較偏好橫條圖，因此我們使用 `1s` 劃分來將時間範圍轉換為數字。


      // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Prepare for display in a bar chart:
    | sort by duration asc 


![](./media/app-analytics-samples/050.png)


### 真實範例

```AIQL

Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  	 
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

<a name="concurrent-activities"><a/>
## 一段時間的並行工作階段圖表

假設我們有含有活動開始及結束時間的活動資料表。我們要查看一段時間的圖表，該圖表顯示在任意時間中有多少活動正在同時執行。

以下是我們將會呼叫 `X` 的範例輸入：

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

我們想要一個 1 分鐘量化的圖表，所以我們想要建立一些以 1 分鐘為間隔單位，可以為每個執行中活動執行計算的項目。

以下是中繼結果︰

     X | extend samples = range(bin(StartTime, 1m), Stop, 1m)

`range` 會使用指定之間隔產生值的陣列：

|SessionId | StartTime | StopTime | 範例|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

但我們不保留那些陣列，改為[將它們展開](app-analytics-queries.md#mvexpand-operator)：

    X | mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)


|SessionId | StartTime | StopTime | 範例|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | 10:01:00|
| a | 10:03:33 | 10:06:31 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| a | 10:03:33 | 10:06:31 | 10:03:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:03:00|
| a | 10:03:33 | 10:06:31 | 10:04:00|
| c | 10:03:12 | 10:04:30 | 10:04:00|
|...||||

現在可以將這些資料以取樣時間群組，來計算每個活動的發生次數：

```
X
| mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* 我們需要 todatetime()，因為 `mvexpand` 會產生動態類型的資料行。
* 我們需要 bin()，因為對於數值和日期而言，如果未提供 bin 函式，summarize 一律會套用具有預設間隔的 bin 函式。 


| count\_SessionId | 範例|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|

這可以轉譯為橫條圖或時間圖表。


## 聯結類別

聯結運算子的確切類別是以某種關鍵字來指定。目前，Analytics支援六種聯結運算子：內部聯結左側重複資料 (預設值)、標準內部聯結、左方外部、右方外部、完整外部和左方反半聯結。
 
預設聯結類別 (沒有指定類型) 讓我們使用兩個範例資料表來解釋聯結的作業：
 
資料表 X


|金鑰 |Value1 
|---|---
|a |1 
|b |2 
|b |3 
|c |4 

資料表 Y

|金鑰 |Value2 
|---|---
|b |10 
|c |20 
|c |30 
|d |40 

 
刪除聯結索引鍵左側的重複資料後，預設聯結會執行內部聯結 (重複資料刪除會保留第一筆記錄)。假設陳述式：

    X | join Y on Key 

則有效的聯結左側 (刪除重複資料後的資料表 X) 會是：

|金鑰 |Value1 
|---|---
|a |1 
|b |2 
|c |4 

且聯結的結果會是：

|金鑰 |Value1 |金鑰 |Value2 
|---|---|---|---
|b |2 |b |10 
|c |4 |c |20 
|c |4 |c |30 

(請注意，索引鍵 'a' 和 'd' 不會出現在輸出中，因為在左側和右側沒有相符的索引鍵)。
 
(在過去，這是 Analytics 初始版本所支援的第一個聯結實作；它適用於我們想要在相同的相互關聯識別碼底下，將兩個事件的一般記錄/追蹤分析相互關聯的案例 (每個事件均符合某些篩選條件)，回復我們正在尋找之現象的所有樣子，並忽略多個提供之追蹤記錄的表象。)
 
### 內部聯結 (kind=inner) 

這是 SQL 世界中眾所周知的標準內部聯結。只要左側的記錄與右側的記錄有相同的聯結索引鍵，就會產生輸出記錄。
 
    X | join kind=inner Y on Key 

產生結果：

|金鑰 |Value1 |金鑰 |Value2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 

請注意，來自右側的 (b,10) 聯結了兩次：與左側的 (b,2) 和 (b,3) 兩者聯結；左側的 (c,4) 也聯結了兩次：與右側的 (c,20) 和 (c,30) 兩者聯結。

### 左外部聯結 (kind=leftouter) 

資料表 X 和 Y 的左外部聯結結果包含左資料表 (X) 的所有記錄，即使聯結條件在右資料表 (Y) 中找不到任何符合的記錄。
 
假設︰

    X | join kind=leftouter Y on Key 

結果：

|金鑰 |Value1 |金鑰 |Value2 
|---|---|---|---
|a |1 |null |null 
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
 
### 右外部聯結 (kind=rightouter) 

類似於左外部聯結，但資料表的處理方式相反。
 
假設︰

    X | join kind=rightouter Y on Key 

結果：


|金鑰 |Value1 |金鑰 |Value2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
|null |null |d |40 
 
### 完整外部聯結 (kind=fullouter) 

就概念而言，完整外部聯結結合了套用左外部聯結與右外部聯結的效果。其中聯結的資料表將不會配對，對於每個缺少配對資料列的資料表資料行，結果集將會有 NULL 值。針對相符的記錄，會在結果集中產生單一的資料列 (包含從兩個資料表填入的資料行)。
 
假設︰

    X | join kind=fullouter Y on Key 

結果：

|金鑰 |Value1 |金鑰 |Value2 
|---|---|---|---
|a |1 |null |null 
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
|null |null |d |40 
 
### 左方反聯結 (kind=leftanti) 

左方反聯結會傳回左側中與右側任何記錄都不相符的所有記錄。
 
    X | join kind=leftanti Y on Key 
 
結果：

|金鑰 |Value1 
|---|---
|a |1 
 
反聯結建立「NOT IN」查詢模型。



## 搭配內嵌字典對應進行聯結

```AIQL

let TeamFoundationJobResult = range i from 1 to 1 step 1 
  | extend recordsJson = "[ 
    { 'key': -1, 'value': 'None'}, 
    { 'key': 0, 'value': 'Succeeded'}, 
    { 'key': 1, 'value': 'PartiallySucceeded'}, 
    { 'key': 2, 'value': 'Failed'}, 
    { 'key': 3, 'value': 'Stopped'}, 
    { 'key': 4, 'value': 'Killed'}, 
    { 'key': 5, 'value': 'Blocked'}, 
    { 'key': 6, 'value': 'ExtensionNotFound'}, 
    { 'key': 7, 'value': 'Inactive'}, 
    { 'key': 8, 'value': 'Disabled'}, 
    { 'key': 9, 'value': 'JobInitializationError'} 
  ]" 
  | mvexpand record = parsejson(replace("'", """, recordsJson)) 
  | project Result = toint(record.key), ResultString = tostring(record.value); 
JobHistory 
  | where PreciseTimeStamp > ago(1h)  
  | where Service  <> "AX" 
  | where Plugin has "Analytics"  
  | sort by PreciseTimeStamp desc 
  | join kind=leftouter TeamFoundationJobResult on Result 
  | extend ExecutionTimeSpan = totimespan(ExecutionTime) 
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage  
```





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->