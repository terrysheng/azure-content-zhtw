<properties 
	pageTitle="Application Insights 中分析的完整教學課程" 
	description="分析 (Application Insights 的強大搜尋工具) 中所有主要查詢的簡短範例。" 
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


 
# Application Insights 中分析的教學課程


[分析](app-insights-analytics.md) 是 [Application Insights](app-insights-overview.md) 的強大搜尋功能。這些頁面說明 Analytics 查詢語言。


[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]
 
讓我們逐步解說可讓您快速入門的一些基本查詢。

## 連接到您的 Application Insights 資料

在 Application Insights 中，從 app 的[概觀刀鋒視窗](app-insights-dashboards.md)開啟 [分析]：

![開啟 portal.azure.com，開啟您的 Application Insights 資源，然後按一下 [分析]。](./media/app-insights-analytics/001.png)

	
## [Take](app-insights-analytics-aggregations.md#take)：顯示 n 個資料列

記錄使用者作業的資料點 (通常是 Web 應用程式收到的 HTTP 要求) 會儲存在名為 `requests` 的資料表。每個資料列都是從應用程式中的 Application Insights SDK 接收的遙測資料點。

讓我們先檢查資料表的幾個範例資料列︰

![results](./media/app-insights-analytics-tour/010.png)

> [AZURE.NOTE] 先將游標置於陳述式中的某處，再按一下 [執行]。您可以將陳述式分割成多行，但不要在單一陳述式中放置空白行。空白行可方便您在視窗中保有數個不同的查詢。


選擇資料行並調整其位置︰

![按一下結果右上方的資料行選取範圍](./media/app-insights-analytics-tour/030.png)


展開任何項目以查看詳細資料︰
 
![選擇 [資料表]，並使用 [設定資料行]](./media/app-insights-analytics-tour/040.png)

> [AZURE.NOTE] 按一下資料行的標頭，以重新排列網頁瀏覽器中可用的結果。但請注意，對大型結果集而言，下載至瀏覽器的資料列數目有限。請注意，以這種方式排序並不一定會顯示實際的最高或最低項目。為此，您應該使用 `top` 或 `sort` 運算子。

## [Top](app-insights-analytics-aggregations.md#top) 和 [sort](app-insights-analytics-aggregations.md#sort)

`take` 可用來取得快速的結果範例，但它不會依特定順序顯示資料表中的資料列。若要取得已排序的檢視，請使用 `top` (適用於某個範例) 或 `sort` (整個資料表)。

顯示前 n 個資料列 (依特定資料行排序)︰

```AIQL

	requests | top 10 by timestamp desc 
```

* 語法︰大部分運算子都有關鍵字參數 (例如 `by`)。
* `desc` = 遞減順序，`asc` = 遞增。

![](./media/app-insights-analytics-tour/260.png)

`top...` 是 `sort ... | take...` 比較有效率的說法。我們可以撰寫︰

```AIQL

	requests | sort by timestamp desc | take 10
```

結果會相同，但執行速度比較慢一點 (您也可以撰寫 `order`，這是 `sort` 的別名)。

資料表檢視中的資料行標頭也可用來排序畫面上的結果。當然，如果您已使用 `take` 或 `top` 只擷取部分的資料表，您只會重新排列您所擷取的記錄。


## [Project](app-insights-analytics-aggregations.md#project)：選取、重新命名和計算資料行

使用 [`project`](app-insights-analytics-aggregations.md#project)，只挑出您想要的資料行︰

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)


您也可以將資料行重新命名並定義新的資料行︰

```AIQL

    requests 
    | top 10 by timestamp desc 
    | project timestamp, 
               timeOfDay = floor(timestamp % 1d, 1s), 
               name, 
               response = resultCode
```

![結果](./media/app-insights-analytics-tour/270.png)

在純量運算式中︰

* `%` 是很常見的模數運算子。 
* `1d` (這是數字 1，再加上 'd') 是一個時間範圍常值，表示一天。以下是其他一些時間範圍常值︰`12h`、`30m`、`10s``0.01s`。
* `floor` (別名 `bin`) 會將一個值無條件捨去為您提供的基值的最近倍數。所以 `floor(aTime, 1s)` 會將時間無條件捨去為最接近的秒數。

[運算式](app-insights-analytics-scalars.md)可以包含所有常見的運算子 (`+`、`-`...)，而且有一些實用的函式。

## [Extend](app-insights-analytics-aggregations.md#extend)：計算資料行

如果您只要將資料行加入現有的資料行，請使用 [`extend`](app-insights-analytics-aggregations.md#extend)：

```AIQL

    requests 
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

如果您要保留所有現有的資料行，使用 [`extend`](app-insights-analytics-aggregations.md#extend) 比 [`project`](app-insights-analytics-aggregations.md#project) 精簡。

## [Summarize](app-insights-analytics-aggregations.md#summarize)：彙總資料列群組

`Summarize` 會對資料列群組套用指定的彙總函式。

例如，Web 應用程式回應要求所花的時間會在 `duration` 欄位中報告。我們來看看所有要求的平均回應時間︰

![](./media/app-insights-analytics-tour/410.png)

或者，我們可以將結果分成不同名稱的要求︰


![](./media/app-insights-analytics-tour/420.png)

`Summarize` 會將串流中的資料點收集到 `by` 子句評估為相同的群組中。`by` 運算式中的每個值 (即上述範例中的各作業名稱) 各會在結果資料表中產生一個資料列。

或者，我們可以依一天當中的時間將結果分組︰

![](./media/app-insights-analytics-tour/430.png)

請注意我們如何使用 `bin` 函式 (也稱為 `floor`)。如果我們只使用 `by timestamp`，每個輸入資料列最終都會在它自己的小群組內。針對任何連續純量 (如時間或數字)，我們必須將連續範圍分成可管理的離散值數目，而 `bin` (實際上就只是我們所熟悉的無條件捨去 `floor` 函式) 是最簡單的作業方式。

我們可以使用相同的技巧來減少字串的範圍︰


![](./media/app-insights-analytics-tour/440.png)

請注意，您可以在彙總運算式或 by 子句中使用 `name=` 設定結果資料行的名稱。

### 計算資料點

`sum(itemCount)` 是用來計算事件的建議彙總。在許多情況下，itemCount==1，因此函式只會計算群組中的資料列數目。但是當[取樣](app-insights-sampling.md)進行時，只有一小部分的原始事件會保留做為 Application Insights 中的資料點，因此您看到的每一個資料點會有 `itemCount` 個事件。因此，加總 itemCount 可正確估算事件的原始數目。


![](./media/app-insights-analytics-tour/510.png)

另外還有 `count()` 彙總，其適用於您確實想要計算群組中的資料列數目時。


[彙總函式](app-insights-analytics-aggregations.md)有範圍。


## 製作結果圖表



```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

根據預設，結果會顯示成資料表︰

![](./media/app-insights-analytics-tour/225.png)


有比資料表檢視更好的顯示方式。讓我們使用直條圖選項來看看圖表檢視中的結果︰

![按一下 [圖表]，然後選擇 [直條圖] 並指派 x 和 y 軸](./media/app-insights-analytics-tour/230.png)

請注意，雖然我們並未依時間排序結果 (如資料表顯示中所示），但圖表顯示一律會以正確的順序顯示日期時間。


## [Where](app-insights-analytics-aggregations.md#where)︰篩選條件

如果您已針對應用程式的[用戶端](app-insights-javascript.md)與伺服器端設定 Application Insights 監視，則資料庫中的某些遙測資料會來自瀏覽器。

我們看看從瀏覽器報告的例外狀況︰

```AIQL

    exceptions 
    | where device_Id == "browser" 
    |  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-insights-analytics-tour/250.png)

`where` 運算子會採用布林運算式。以下是其相關的一些重點︰

 * `and`、`or`：布林運算子
 * `==`、`<>`︰等於和不等於
 * `=~`、`!=`︰不區分大小寫的字串等於和不等於。有更多的字串比較運算子。

深入了解[純量運算式](app-insights-analytics-scalars.md)。

### 篩選事件

尋找失敗的要求︰

```AIQL

    requests 
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` 具有字串類型，所以我們必須[將它轉換](app-insights-analytics-scalars.md#casts)以進行數值比較。

彙整不同的回應︰

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
    | summarize count() 
      by resultCode
```

## 時間表

顯示每天有多少個事件︰

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

選取圖表顯示選項：

![時間圖](./media/app-insights-analytics-tour/080.png)

折線圖的 x 軸必須是 DateTime 類型。

## 多個系列 

在 `summarize by` 子句中使用多個值，為每組值建立個別的資料列︰

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-insights-analytics-tour/090.png)

若要在圖表上顯示多行，請按一下 [分割依據] 並選擇一個資料行。

![](./media/app-insights-analytics-tour/100.png)



## 每日平均週期

平均一天的使用量如何變化？

依時間模數一天計算要求 (分類收納成數小時)︰

```AIQL

    requests
    | extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
    | summarize event_count=count() by hour
```

![平均一天的時數折線圖](./media/app-insights-analytics-tour/120.png)

>[AZURE.NOTE] 請注意，我們目前必須將持續時間轉換成日期時間，才能顯示於圖表上。


## 比較多個每日系列

在不同州內一天當中的使用量如何變化？

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

依州別分割圖表︰

![依 client\_StateOrProvince 分割](./media/app-insights-analytics-tour/130.png)


## 繪製分佈圖

有多少個不同長度的工作階段？

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id 
    | extend sessionDuration = max_timestamp - min_timestamp 
    | where sessionDuration > 1s and sessionDuration < 3m 
    | summarize count() by floor(sessionDuration, 3s) 
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

需要最後一行才能轉換成日期時間 - 折線圖的 x 軸目前只能是日期時間。

`where` 子句會排除單次發生的工作階段 (sessionDuration==0) 及設定 x 軸的長度。


![](./media/app-insights-analytics-tour/290.png)



## [百分位數](app-insights-analytics-aggregations.md#percentiles)

哪些持續時間範圍涵蓋不同的工作階段百分比？

使用上述查詢，但取代最後一行︰

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id 
    | extend sesh = max_timestamp - min_timestamp 
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s) 
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

我們也移除了 where 子句中的上限，以便取得正確的數據，包括有多個要求的所有工作階段︰

![結果](./media/app-insights-analytics-tour/180.png)

我們可以從中看到︰

* 5% 的工作階段的持續時間小於 3 分鐘 34 秒； 
* 50% 的工作階段的持續時間小於 36 分鐘；
* 5% 的工作階段的持續時間超過 7 天

若要取得每個國家/地區的個別分解圖，我們只需透過兩個 summarize運算子個別地帶入 client\_CountryOrRegion 資料行︰

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp 
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
	  by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)


## [Join](app-insights-analytics-aggregations.md#join)

我們可以存取數個資料表，包括要求和例外狀況。

若要尋找傳回失敗回應之要求的相關例外狀況，我們可以聯結 `session_Id` 上的資料表：

```AIQL

    requests 
    | where toint(responseCode) >= 500 
    | join (exceptions) on operation_Id 
    | take 30
```


在執行聯結之前，使用 `project` 只選取我們需要的資料行是相當好的做法。在相同的子句中，我們會將時間戳記資料行重新命名。



## [Let](app-insights-analytics-aggregations.md#let)︰將結果指派給變數

使用 [let](./app-insights-analytics-syntax.md#let-statements) 來分隔前一個運算式的各個部分。結果不變：

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id 
    | take 30
```

> 秘訣︰在分析用戶端中，不要在這些部分之間放入空白行。務必執行所有一切。


[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->