<properties 
	pageTitle="Application Insights Analytics 中的彙整陳述式和彙總函式" 
	description="Application Insights Analytics (適用於 Application Insights 的強大搜尋工具) 中的彙總函式和彙整陳述式參考資料。" 
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
	ms.date="03/06/2016" 
	ms.author="awills"/>


# Application Insights Analytics 中的彙總

[Application Insights Analytics](app-analytics.md) 是強大的搜尋引擎，適合用於 [Application Insights](app-insights-overview.md) 遙測。這些頁面說明 Application Insights Analytics 查詢語言 AIQL。

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]



## summarize 運算子

產生資料表來彙總輸入資料表的內容。

    purchases
    | summarize avg(Price) 
      by Fruit, Supplier

![](./media/app-analytics-aggregations/01.png)

* 
* 彙總運算式會對每個群組的成員進行評估。
* 'by' 運算式之值的每個不同組合都有一個輸出資料列。 
* 每個彙總運算式和每個 'by' 運算式都有一個輸出資料行。其他所有輸入資料行則會遭到捨棄。

### 語法

    T | summarize
         [  [ Column = ] Aggregation [ , ... ]]
         [ by
            [ Column = ] GroupExpression [ , ... ]]

**引數**

* 預設值為衍生自運算式的名稱。
* 請參閱下面的彙總函式清單。
*  







    exceptions
	| summarize count()
      by tostring(customDimensions.ClientRequestId)

### 依具有離散值的資料行進行彙整

進行查詢以顯示不同 HTTP 要求的平均回應時間，來分離出具有不同回應碼的任何要求︰

    requests 
    | summarize count(), avg(duration) 
      by operation_Name, resultCode

![結果](./media/app-analytics-aggregations/03.png)


* 彙總運算式 (例如 count 和 avg) 必須從本文所述的彙總函式來形成。其引數可以是任何純量函式。
* 群組運算式 (在 'by' 之後) 可以是任何純量運算式，但如果它們只是欄位名稱的話效能會更好。

### 依數值資料行彙整



    requests
    | summarize count() 
      by bin(duration, 1000)/1000

![結果](./media/app-analytics-aggregations/04.png)


 
## 秘訣

* 在執行 `summarize` 之前，請先使用 `where` 移除所有不再需要的資料列。
 * 移除 null 值。群組中若有 null 值將會讓彙總結果變為 null。 

```

        requests 
        | where isnotnull(duration) 
        | summarize count(), avg(duration)
          by operation_Name
```

* 雖然您可以為彙總與群組運算式提供任意運算式，但更有效率的方法是使用簡單的欄位名稱，或對數值欄位套用 `bin()`。





## 範例

尋找「活動」資料表中所有記錄的最小和最大時間戳記。由於沒有 group-by 子句，因此輸出中只有一個資料列︰

```

requests | summarize Min = min(timestamp), Max = max(timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`



#### 工作階段持續時間

事件記錄檔中的工作階段有多個事件。請透過尋找每個工作階段中的最舊和最新事件來找出每個工作階段的開始和結尾︰

```

    requests 
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) by session_Id 
    | extend duration = bin(stop - start, 1s)
```

`extend` 作業會使用 `bin` (也稱為 `floor`) 新增 [持續時間] 資料行，以將其四捨五入至最接近的秒數。

![](./media/app-analytics-aggregations/minmax.png)

#### 範例︰平均持續時間

現在讓我們尋找不同城市用戶端的平均工作階段持續時間︰

```

    requests
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) 
      by session_Id, client_City 
    | extend duration = stop - start
    | summarize duration_by_city=bin(avg(duration),1s) by client_City
    | top 50 by duration_by_city
```

我們將 `client_City` 資料行新增到 `by` 子句，讓它傳遞到第一個彙整作業。假設一個用戶端工作階段的所有事件都發生在同一個城市，其不會新增到彙整輸出數目。


![](./media/app-analytics-aggregations/durationcity.png)


#### 範例： 

找出每個用戶端城市在一天當中最忙碌的時間。這裡所說的「最忙碌」是指最大工作階段數目在平日開始的時間。

```
requests  
| summarize start=min(timestamp) by session_Id, city=client_City 
| extend timeofday=start % 1d 
| summarize popularity=dcount(session_Id) by bin(timeofday, 1h), city 
| summarize argmax(popularity, *) by city  
| sort by max_pop_tod asc
```

## 

## any 

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
## argmin、argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 





**範例**

針對每個要求名稱顯示最久要求的發生時間︰

    requests | summarize argmax(duration, timestamp) by name

顯示最久要求的所有詳細資料，而非只顯示時間戳記︰

    requests | summarize argmax(duration, *) by name


尋找每個度量的最小值，以及其時間戳記和其他資料︰

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-analytics-aggregations/argmin.png)
 


## avg

    avg(Expression)



## buildschema

    buildschema(DynamicExpression)





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

在此結構描述中，一些有效的路徑會是 (假設這些範例索引在範圍內)︰

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
* 
* "z" 陣列中的每個項目皆為整數或字串。
* "t" 是字串陣列。
* 每個屬性皆可隱含選用，且任何陣列都可以是空的。

#### 結構描述模型

所傳回結構描述的語法如下︰

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
	Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

它們等同於 TypeScript 類型註釋的子集，並編碼為 AIQL 動態值。在 Typescript 中，範例結構描述會是︰

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


## count

    count([ Predicate ])




   

## dcount

    dcount( Expression [ ,  Accuracy ])





 * 
 * 
 * 

**範例**

    pageViews 
    | summarize countries=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-analytics-aggregations/dcount.png)

## makelist

    makelist(Expr [ ,  MaxListSize ] )



* 

## makeset

    makeset(Expression [ , MaxSetSize ] )


  
*  

**範例**

    pageViews 
    | summarize countries=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-analytics-aggregations/makeset.png)




## max、min

    max(Expr)


    
    min(Expr)






<a name="percentile"></a> <a name="percentiles"></a>
## percentile、percentiles

    percentile(Expression, Percentile)

其精確度取決於百分位數區域中的母體密度。
    
    percentiles(Expression, Percentile1 [ , Percentile2 ] )



**範例**




    request 
    | summarize percentile(duration, 95)
      by name

省略 "by..." 可計算整份資料表。

同時計算不同要求名稱的數個百分位數︰

    
    requests 
    | summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-analytics-aggregations/percentiles.png)

結果顯示，針對 /Events/Index 要求，5% 的要求在小於 2.44 秒內回應，有一半在 3.52 秒內，有 5% 超過 6.85 秒。


計算多個統計資料︰

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### 百分位數中的估計誤差



幾個重點如下︰

* 估計誤差的範圍會隨著要求的百分位數值而變化。最佳精確度位於 [0..100] 級別的尾端，百分位數 0 和 100 正好是數據分佈的最小值和最大值。精確度會逐漸向級別中央減少。在中位數時最差，且上限為 %1。 
* 誤差範圍是在秩上測得，而非在值上。假設 percentile(X, 50) 傳回 Xm 的值。此估計可保證最少 49%、最多 51% 的 X 值小於 Xm。Xm 與 X 的實際中位數值之間的差異理論上沒有限制。

## stdev

     stdev(Expr)



## variance

    variance(Expr)



## sum

    sum(Expr)






[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0316_2016-->