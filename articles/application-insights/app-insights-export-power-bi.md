<properties 
	pageTitle="從 Application Insights 使用串流分析匯出至 Power BI" 
	description="示範如何使用串流分析處理匯出的資料。" 
	services="application-insights" 
    documentationCenter=""
	authors="noamben" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/25/2015" 
	ms.author="awills"/>
 
# 從 Application Insights 使用串流分析將資料傳送至 Power BI

本文將說明如何使用[串流分析](http://azure.microsoft.com/services/stream-analytics/)來處理從 [Visual Studio Application Insights](app-insights-overview.md) [匯出](app-insights-export-telemetry.md)的資料。作為範例目標，我們將資料傳送到 [Microsoft Power BI](https://powerbi.microsoft.com/)。


> [AZURE.NOTE]從 Application Insights 將資料送入 Power BI 最簡單的方式是[使用配接器](https://powerbi.microsoft.com/zh-TW/documentation/powerbi-content-pack-application-insights/) (可在 [服務] 下的 Power BI 資源庫中找到)。本文中所描述的內容目前較多樣化，但也可示範如何利用 Application Insights 進行串流分析。

[Microsoft Power BI](https://powerbi.microsoft.com/) 以豐富多元的視覺方式呈現您的資料，且能整合多個來源的資訊。


![Application Insights 使用量資料的 Power BI 檢視範例](./media/app-insights-export-power-bi/010.png)

[串流分析](http://azure.microsoft.com/services/stream-analytics/)是一項 Azure 服務可以做為配接器，會持續處理從 Application Insights 匯出的資料。

![Application Insights 使用量資料的 Power BI 檢視範例](./media/app-insights-export-power-bi/020.png)




## 影片

Noam Ben Zeev 會示範我們在本文中的描述。

> [AZURE.VIDEO export-to-power-bi-from-application-insights]


**取樣** 如果您的應用程式傳送大量資料，且您是使用 Application Insights SDK for ASP.NET 版本 2.0.0-beta3 或更新版本，則調適性取樣功能可能會運作，並只傳送一部分的遙測資料。[深入了解取樣。](app-insights-sampling.md)

## 使用 Application Insights 監視您的應用程式

如果您還沒嘗試過，現在就是開始的好時機。Application Insights 可以監視許多平台上的任何裝置或 Web App，包含 Windows、iOS、Android、J2EE 等。[開始使用](app-insights-overview.md)

## 在 Azure 中建立儲存體

連續匯出一律會將資料輸出至 Azure 儲存體帳戶，因此您必須先建立儲存體。

1. 在 [Azure 入口網站](https://portal.azure.com)的訂用帳戶中建立「傳統」儲存體帳戶。

    ![在 Azure 入口網站中，依序選擇 [新增]、[資料]、[儲存體]](./media/app-insights-export-power-bi/030.png)

2. 建立容器

    ![在新的儲存體中，選取 [容器]，按一下容器磚，然後按一下 [新增]](./media/app-insights-export-power-bi/040.png)

3. 複製儲存體存取金鑰

    稍後您會需要使用它來設定串流分析服務的輸入。

    ![在儲存體中，依序開啟 [設定]、[金鑰]，然後複製主要存取金鑰](./media/app-insights-export-power-bi/045.png)

## 啟動對 Azure 儲存體的連續匯出

[連續匯出](app-insights-export-telemetry.md)會將資料從 Application Insights 移入 Azure 儲存體。

1. 在 Azure 入口網站中，瀏覽至您為應用程式建立的 Application Insights 資源。

    ![依序選擇 [瀏覽]、[Application Insights]、您的應用程式](./media/app-insights-export-power-bi/050.png)

2. 建立連續匯出。

    ![依序選擇 [設定]、[連續匯出]、[新增]](./media/app-insights-export-power-bi/060.png)


    選取您稍早建立的儲存體帳戶：

    ![設定匯出目的地](./media/app-insights-export-power-bi/070.png)
    
    設定您想要查看的事件類型：

    ![選擇事件類型](./media/app-insights-export-power-bi/080.png)

3. 可讓一些資料累積。請休息一下，讓其他人使用您的應用程式一段時間。遙測資料會送過來，而您會在[計量瀏覽器](app-insights-metrics-explorer.md)中看到統計圖表，並在[診斷搜尋](app-insights-diagnostic-search.md)中看到個別事件。

    此外，資料會匯出至您的儲存體。

4. 檢查匯出的資料。在 Visual Studio 中，依序選擇 [檢視]、[Cloud Explorer]，然後依序開啟 [Azure]、[儲存體]。(如果您沒有此功能表選項，您需要安裝 Azure SDK：開啟 [新增專案] 對話方塊，然後開啟 [Visual C#] / [Cloud] / [取得 Microsoft Azure SDK for .NET]。)

    ![](./media/app-insights-export-power-bi/04-data.png)

    記下衍生自應用程式名稱和檢測金鑰之路徑名稱的共同部分。

事件會以 JSON 格式寫入至 Blob 檔案。每個檔案可能會包含一或多個事件。因此我們想要讀取事件資料，並篩選出需要的欄位。該處有用這些資料所能做到的所有事情種類，但我們現在計劃要使用串流分析將資料傳送至 Power BI。

## 建立 Azure 串流分析執行個體

在[傳統 Azure 入口網站](https://manage.windowsazure.com/)中，選取 Azure 串流分析服務，然後建立新的串流分析工作：


![](./media/app-insights-export-power-bi/090.png)



![](./media/app-insights-export-power-bi/100.png)

建立新工作後，請展開其詳細資料：

![](./media/app-insights-export-power-bi/110.png)


#### 設定 Blob 位置

將此設定為從您的連續匯出 Blob 接收輸入：

![](./media/app-insights-export-power-bi/120.png)

現在您需要儲存體帳戶的主要存取金鑰 (您已在稍早記下此金鑰)。請將此金鑰設為儲存體帳戶金鑰。

![](./media/app-insights-export-power-bi/130.png)

#### 設定路徑前置詞模式 

![](./media/app-insights-export-power-bi/140.png)


請務必將 [日期格式] 設為 [YYYY-MM-DD] (含連接號)。

路徑前置詞模式會指定串流分析在存放區中尋找輸入檔案的位置。您需要將它設定為與連續匯出儲存資料的方式相對應。請設定如下：

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

在此範例中：

* `webapplication27` 是 Application Insights 資源名稱 (全部小寫 )。
* `1234...` 是 Application Insights 資源的檢測金鑰 (省略破折號)。 
* `PageViews` 是您想要分析的資料類型。可用的類型取決於您在「連續匯出」中設定的篩選。檢查匯出的資料以查看其他可用的類型，並查看[匯出資料模型](app-insights-export-data-model.md)。
* `/{date}/{time}` 是要依字面意思寫入資訊的格式。

> [AZURE.NOTE]檢查儲存區以確定您取得正確的路徑。

#### 完成初始設定

確認序列化格式：

![確認並關閉精靈](./media/app-insights-export-power-bi/150.png)

關閉精靈，並等候設定完成。

> [AZURE.TIP]您可以使用範例命令來下載一些資料。將其保留下來做為偵錯查詢的測試範例。

## 設定輸出

現在選取您的工作並設定輸出。

![選取新的通道，依序按一下 [輸出]、[新增]、[Power BI]](./media/app-insights-export-power-bi/160.png)

提供您的**工作或學校帳戶**來授權串流分析存取您的 Power BI 資源。接著自創一個輸出的名稱，以及目標 Power BI 資料集和資料表的名稱。

![創建三個名稱](./media/app-insights-export-power-bi/170.png)

## 設定查詢

查詢會控管從輸入到輸出的轉譯。

![選取工作並按一下 [查詢]。貼上下面的範例。](./media/app-insights-export-power-bi/180.png)


使用測試函式來確認您取得正確的輸出。填入您從輸入頁面所採用的範例資料。

#### 顯示事件計數的查詢

貼上此查詢：

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input 是我們提供給串流輸入的別名
* pbi-output 是我們所定義的輸出別名
* 我們會使用 [OUTER APPLY GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx)，因為事件名稱是在巢狀 JSON 陣列中。然後 Select 會取用事件名稱，以及時間週期內具有該名稱之執行個體數目的計數。[Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) 子句會將項目分組到 1 分鐘的時間週期內。


#### 顯示度量值的查詢


```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* 此查詢會切入度量遙測，來取得事件時間和度量值。度量值位於陣列中，因此我們使用 OUTER APPLY GetElements 模式來擷取資料列。在此情況下，"myMetric" 是度量的名稱。 

#### 包含維度屬性值的查詢

```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* 此查詢包括維度屬性值，而不需根據陣列索引中固定索引的特定維度。

## 執行工作

您可以選取一個啟動工作的過去日期。

![選取工作並按一下 [查詢]。貼上下面的範例。](./media/app-insights-export-power-bi/190.png)

請等候直到作業執行。

## 在 Power BI 中查看結果

以您的工作或學校帳戶開啟 Power BI，並選取您定義為串流分析工作輸出的資料集與資料表。

![在 Power BI 中，選取您的資料集和欄位。](./media/app-insights-export-power-bi/200.png)

現在您可以使用報告中的此資料集和 [Power BI](https://powerbi.microsoft.com) 中的儀表板。


![在 Power BI 中，選取您的資料集和欄位。](./media/app-insights-export-power-bi/210.png)

## 影片

Noam Ben Zeev 會示範如何匯出至 Power BI。

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## 相關項目

* [連續匯出](app-insights-export-telemetry.md)
* [屬性類型和值的詳細資料模型參考。](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)
* [更多範例和逐步解說](app-insights-code-samples.md)

<!---HONumber=AcomDC_1203_2015-->