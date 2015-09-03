<properties 
	pageTitle="在 Power BI 中查看 Application Insights 資料" 
	description="使用 Power BI 監視您應用程式的效能與使用量。" 
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
	ms.date="08/04/2015" 
	ms.author="awills"/>
 
# Application Insights 資料的 Power BI 檢視

[Microsoft Power BI](https://powerbi.microsoft.com/) 以豐富多元的視覺方式呈現您的資料，以及將多個來源的資訊整合的能力。您可以將有關您 Web 或裝置應用程式效能與使用量的遙測資料，從 Application Insights 串流處理到 Power BI。

![Application Insights 使用量資料的 Power BI 檢視範例](./media/app-insights-export-power-bi/010.png)

在本文中，我們將示範如何從 Application Insights 匯出資料，並使用「串流分析」將資料移入 Power BI。[串流分析](http://azure.microsoft.com/services/stream-analytics/)是我們做為配接器使用的 Azure 服務。

![Application Insights 使用量資料的 Power BI 檢視範例](./media/app-insights-export-power-bi/020.png)

## 影片

Noam Ben Zeev 會示範我們在本文中的描述。

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## 使用 Application Insights 監視您的應用程式

如果您還沒嘗試過，現在就是開始的好時機。Application Insights 可以監視許多平台上的任何裝置或 Web App，包含 Windows、iOS、Android、J2EE 等。[開始使用](app-insights-get-started.md)。

## 在 Azure 中建立儲存體

連續匯出一律會將資料輸出至 Azure 儲存體帳戶，因此您必須先建立儲存體。

1. 在 [Azure 入口網站](https://portal.azure.com)的訂用帳戶中建立儲存體帳戶。

    ![在 Azure 入口網站中，依序選擇 [新增]、[資料]、[儲存體]](./media/app-insights-export-power-bi/030.png)

2. 建立容器

    ![在新的儲存體中，依序選取 [容器]、[新增]](./media/app-insights-export-power-bi/040.png)

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

現在請休息一下，讓其他人使用您的應用程式一段時間。遙測資料會送過來，而您會在[計量瀏覽器](app-insights-metrics-explorer.md)中看到統計圖表，並在[診斷搜尋](app-insights-diagnostic-search.md)中看到個別事件。

此外，資料會匯出至您的儲存體。

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

請務必將 [日期格式] 設為 YYYY-MM-DD (含連接號)。

[路徑前置詞模式] 會指定串流分析在儲存體中尋找輸入檔案的方式。您必須將它設定為可對應「連續匯出」儲存資料的方式。請設定如下：

    webapplication27_100000000-0000-0000-0000-000000000000/PageViews/{date}/{time}

在此範例中：

* `webapplication27` 是 Application Insights 資源的名稱。 
* `1000...` 是 Application Insights 資源的檢測金鑰。 
* `PageViews` 是我們想要分析的資料類型。可用的類型取決於您在「連續匯出」中設定的篩選。檢查匯出的資料以查看其他可用的類型，並查看[匯出資料模型](app-insights-export-data-model.md)。
* `/{date}/{time}` 是要依字面意思寫入資訊的格式。

若要取得 Application Insights 資源的名稱和 iKey，請在資源的概觀頁面中開啟 Essentials，或開啟 [設定]。

#### 完成初始設定

確認序列化格式：

![確認並關閉精靈](./media/app-insights-export-power-bi/150.png)

關閉精靈，並等候設定完成。

## 設定輸出

現在選取您的工作並設定輸出。

![選取新的通道，依序按一下 [輸出]、[新增]、[Power BI]](./media/app-insights-export-power-bi/160.png)

授權串流分析存取您的 Power BI 資源，然後建立輸出、目標 Power BI 資料集和資料表的名稱

![創建三個名稱](./media/app-insights-export-power-bi/170.png)

## 設定查詢

查詢會控管從輸入到輸出的轉譯。

![選取工作並按一下 [查詢]。貼上下面的範例。](./media/app-insights-export-power-bi/180.png)

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
* 我們會使用 GetElements，因為事件名稱是在巢狀 JSON 陣列中。然後 Select 會取用事件名稱，以及時間週期內具有該名稱之執行個體數目的計數。Group By 子句會將項目分組到 1 分鐘的時間週期內。

## 執行工作

您可以選取一個啟動工作的過去日期。

![選取工作並按一下 [查詢]。貼上下面的範例。](./media/app-insights-export-power-bi/190.png)

請等候直到作業執行。

## 在 Power BI 中查看結果

開啟 Power BI 並選取您定義為串流分析工作之輸出的資料集與資料表。

![在 Power BI 中，選取您的資料集和欄位。](./media/app-insights-export-power-bi/200.png)

現在您可以使用報表中的此資料集和 [Power BI](https://powerbi.microsoft.com) 中的儀表板。


![在 Power BI 中，選取您的資料集和欄位。](./media/app-insights-export-power-bi/210.png)

## 影片

Noam Ben Zeev 會示範如何匯出至 Power BI。

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## 相關項目

* [連續匯出](app-insights-export-telemetry.md)
* [Application Insights](app-insights-overview.md)
* [更多範例和逐步解說](app-insights-code-samples.md)

<!----HONumber=August15_HO8-->