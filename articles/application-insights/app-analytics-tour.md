<properties 
	pageTitle="Application Insights 分析導覽" 
	description="Application Insights 分析 (Application Insights 的強大搜尋工具) 中所有主要查詢的簡短範例。" 
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


 
# Application Insights 分析導覽


Application Insights 分析是強大的診斷搜尋引擎，適合用於 [Application Insights](app-insights-overview.md) 遙測。


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]
 
讓我們逐步解說可讓您快速入門的一些基本查詢。

## 連接到您的 Application Insights 資料

在 Application Insights 中，從您的應用程式的[概觀刀鋒視窗](app-insights-dashboards.md)開啟 [分析]。

![開啟 portal.azure.com，開啟您的 Application Insights 資源，然後按一下 [分析]。](./media/app-analytics/001.png)

## Count 資料列

名為度量的資料表中會儲存效能計數器等度量。每個資料列都是從應用程式中的 Application Insights SDK 接收的遙測資料點。為了了解資料表有多大，我們會透過管道將其內容傳送至只會計算資料列的運算子︰

```AIQL
	
    requests | count
```

> [AZURE.NOTE] 先將游標置於陳述式中的某處，再按一下 [執行]。您可以將陳述式分割成多行，但不要在單一陳述式中放置空白行。若要在視窗中顯示數個查詢，請以空白行分隔。

結果如下︰


![](./media/app-analytics-tour/010.png)

	
`Count` 是可以在管道中排列的許多[查詢運算子](app-analytics-queries.md)之一，用於篩選、重塑和聯結多個階段中的資料。
	
## Take：顯示 n 個資料列


讓我們看看一些資料 - 範例 5 資料列中的內容為何？

```AIQL

	requests | take 5
```

以下是我們看到的內容：

![results](./media/app-analytics-tour/020.png)

選擇資料行並調整其位置︰

![按一下結果右上方的資料行選取範圍](./media/app-analytics-tour/030.png)

展開任何項目以查看詳細資料︰
 
![選擇 [資料表]，並使用 [設定資料行]](./media/app-analytics-tour/040.png)


## Top 和 sort

`take` 可用來取得快速的結果範例，但它不會依特定順序顯示資料表中的資料列。若要取得已排序的檢視，請使用 `top` (適用於某個範例) 或 `sort` (整個資料表)。

顯示前 n 個資料列 (依特定資料行排序)︰

```AIQL

	requests | top 10 by timestamp desc 
```

* *語法︰*大部分運算子都有關鍵字參數 (例如 `by`)。
* `desc` = 遞減順序，`asc` = 遞增。

![](./media/app-analytics-tour/260.png)

`top...` 是 `sort ... | take...` 比較有效率的說法。我們可以撰寫︰

```AIQL

	requests | sort by timestamp desc | take 10
```

結果會相同，但執行速度比較慢一點 (您也可以撰寫 `order`，這是 `sort` 的別名)。

資料表檢視中的資料行標頭也可用來排序畫面上的結果。當然，如果您已使用 `take` 或 `top` 來擷取部分的資料表，您只會重新排列您所擷取的記錄。


## Project：選取、重新命名和計算資料行

使用 `project` 挑出您想要的資料行︰

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-analytics-tour/240.png)


您也可以將資料行重新命名並定義新的資料行︰

```AIQL

    requests 
    | top 10 by timestamp desc 
    | project timestamp, 
               timeOfDay = floor(timestamp % 1d, 1s), 
               name, 
               response = resultCode
```

![結果](./media/app-analytics-tour/270.png)

在純量運算式中︰

* `%` 是很常見的模數運算子。 
* `1d` (這是數字 1，再加上 'd') 是一個時間範圍常值，表示一天。以下是其他一些時間範圍常值︰`12h`、`30m`、`10s``0.01s`。
* `floor` (別名 `bin`) 會將一個值無條件捨去為您提供的基值的最近倍數。所以 `floor(aTime, 1s)` 會將時間無條件捨去為最接近的秒數。

[運算式](app-analytics-scalars.md)可以包含所有常見的運算子 (`+`、`-`...)，而且有一些實用的函數。

## Extend：計算資料行

如果您只要將資料行加入至現有的資料行，請使用 `extend`：

```AIQL

    requests 
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

如果您要保留所有現有的資料行，使用 `extend` 比 `project` 精簡。

## Summarize：彙總資料列群組

查看某個資料表範例，我們可以看到報告不同遙測資料的欄位。例如，`exception | take 20` 可快速顯示在稱為 `outerExceptionType` 的欄位中報告的例外狀況訊息。

但我們可以詢問每種類型已報告的例外狀況數量，而不必費力進行個別的執行個體︰

```AIQL

	exceptions 
    | summarize count() by outerExceptionType
```

![](./media/app-analytics-tour/210.png)

`Summarize` 會將 `by` 子句中具名的欄位中有相同值的資料列群組在一起，並為每個群組產生單一結果資料列。所以在此情況下，每個例外狀況類型都有一個資料列。彙總函數 `count()` 會計算每個群組中的資料列，並在結果中提供一個資料行。


有一些[彙總函數](app-analytics-aggregations.md)，您可以在一個 summarize 運算子中使用其中幾個函數，以產生數個計算資料行。

例如，讓我們列出發生這些例外狀況的 HTTP 要求。再次檢查例外狀況資料表範例，您會注意到 HTTP 要求路徑報告於名為 `operation_Name` 的資料行中。

```AIQL

    exceptions 
    | summarize count(), makeset(operation_Name)
      by outerExceptionType	      
```

![](./media/app-analytics-tour/220.png)

彙總函數 `makeset()` 會建立每個群組中所有指定值的集合。當它發生於此範例時，只有一項作業會引發每個例外狀況。


summarize 的結果有：

* `by` 中具名的每個資料行；
* 加上每個彙總運算式的一個資料行；
* 每一組 `by` 值的一個資料列。


## 依純量值彙總


您可以在 by 子句中使用純量 (數值、時間或間隔) 值。但數字通常會填滿連續的範圍。若要將資料點分組，您要將它們指派至離散值的 bin。`bin` 函數適合用於︰

```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

![](./media/app-analytics-tour/225.png)

`bin` 會將所有時間戳記降低為 1 天的時間間隔。這是 `floor` 的別名，也就是大部分語言所熟悉的函數。它只會將每個值降到您指定的模數的最近倍數，以便 `summarize` 將資料列指派給大小適中的群組 (若沒有該函數，則幾分之一秒就會有一個結果資料列，如此便完全無法彙整資料)。

我們可以處理得比此處的資料表檢視還要好。讓我們使用直條圖選項來看看圖表檢視中的結果︰

![按一下 [圖表]，然後選擇 [直條圖] 並指派 x 和 y 軸](./media/app-analytics-tour/230.png)

請注意，雖然我們並未依時間排序結果 (如資料表顯示中所示），但圖表顯示一律會以正確的順序顯示日期時間。


## Where︰篩選條件

如果您已針對應用程式的[用戶端](app-insights-javascript.md)與伺服器端設定 Application Insights 監視，則資料庫中的某些遙測資料會來自瀏覽器。

我們看看從瀏覽器報告的例外狀況︰

```AIQL

    exceptions 
    | where device_Id == "browser" 
    |  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-analytics-tour/250.png)

`where` 運算子會採用布林運算式。以下是其相關的一些重點︰

 * `and`、`or`︰布林運算子
 * `==`、`<>`︰等於和不等於
 * `=~`、`!=`︰不區分大小寫的字串等於和不等於。有更多的字串比較運算子。

深入了解[純量運算式](app-analytics-scalars.md)。

### 篩選事件

尋找失敗的要求︰

```AIQL

    requests 
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` 具有字串類型，所以我們必須[將它轉換](app-analytics-scalars.md#casts)以便進行數值比較。

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

![時間圖](./media/app-analytics-tour/080.png)

折線圖的 x 軸必須是 DateTime 類型。

## 多個系列 

在 `summarize by` 子句使用多個值，為每個值組合建立個別的資料列︰

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-analytics-tour/090.png)

若要在圖表上顯示多行，請按一下 [分割依據] 並選擇一個資料行。

![](./media/app-analytics-tour/100.png)



## 每日平均週期

平均一天的使用量如何變化？

依時間模數一天計算要求 (分類收納成數小時)︰

```AIQL

    requests
    | extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
    | summarize event_count=count() by hour
```

![平均一天的時數折線圖](./media/app-analytics-tour/120.png)

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

![依 client\_StateOrProvince 分割](./media/app-analytics-tour/130.png)


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

`where` 子句會排除單次發生的工作階段 (sessionDuration = = 0) 及設定 x 軸的長度。


![](./media/app-analytics-tour/290.png)



## 百分位數

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

![結果](./media/app-analytics-tour/180.png)

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

![](./media/app-analytics-tour/190.png)


## Join

我們有三個資料表的存取權︰度量、例外狀況和事件。`event` 包含要求報告、頁面檢視、自訂事件等等。

若要尋找傳回失敗回應之要求的相關例外狀況，我們可以聯結 `session_Id` 上的資料表：

```AIQL

    requests 
    | where toint(responseCode) >= 500 
    | join (exceptions) on operation_Id 
    | take 30
```


在執行聯結之前，使用 `project` 只選取我們需要的資料行是相當好的做法。在相同的子句中，我們會將時間戳記資料行重新命名。



## Let︰將結果指派給變數

使用 [let](./app-analytics-syntax.md#let-statements) 來分隔前一個運算式的各個部分。結果不變：

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id 
    | take 30
```

> 提示︰在 AI 分析用戶端中，不要在這些部分之間放入空白行。務必執行所有一切。


[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->