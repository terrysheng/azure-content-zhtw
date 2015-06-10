<properties
   pageTitle="Azure 雲端服務的 Application Insights"
   description="使用 Application Insights 有效地監視您的 Web 和背景工作角色"
   services="application-insights"
   documentationCenter=""
   authors="soubhagyadash"
   manager="victormu"
   editor="alancameronwills"/>

<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.tgt_pltfrm="ibiza"
   ms.topic="article"
   ms.workload="tbd"
   ms.date="05/21/2015"
   ms.author="sdash"/>

# Azure 雲端服務的 Application Insights


*Application Insights 目前僅供預覽*

[Microsoft Azure 雲端服務應用程式](http://azure.microsoft.com/services/cloud-services/)可以由 [Visual Studio Application Insights][start] 監視可用性、效能、失敗和使用方式。

<!-- For illustration purposes, we have added Application Insights to this [sample Azure cloud service](sample link). This code is available [here](git link), for you to follow along with the steps below. -->

## 為每個角色建立 Application Insights 資源

Application Insights 資源是您在其中分析和顯示遙測資料的位置。

1.  在 [Azure 入口網站][portal] 中，建立新的 Application Insights 資源。針對應用程式類型，選擇 ASP.NET 應用程式。 

    ![按一下 [新增]，然後按一下 [Application Insights]](./media/app-insights-cloudservices/01-new.png)

2.  取得檢測金鑰的副本。您馬上需要這個項目以設定 SDK。

    ![按一下 [屬性]，選取金鑰，然後按下 CTRL+C](./media/app-insights-cloudservices/02-props.png)


最好能夠從每個 Web 和背景工作角色針對資料建立不同的資源。

或者，您可以從所有角色僅將資料傳送至一個資源，但是設定[預設屬性][apidefaults]，讓您可以篩選或群組每個角色的結果。

## <a name="sdk"></a>在每個專案中安裝 SDK


1. 在 Visual Studio 中，編輯桌面應用程式專案的 NuGet 封裝。![以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 封裝]](./media/app-insights-cloudservices/03-nuget.png)

2. 安裝 Web 應用程式適用的 Application Insights SDK。

    ![選取 [**線上**]、[**包括發行前版本**]，然後搜尋 "Application Insights"](./media/app-insights-cloudservices/04-ai-nuget.png)

    (或者，您可以選擇 Web Apps 適用的 Application Insights SDK。這可提供一些內建的效能計數器遙測。)

3. 設定 SDK 以將資料傳送給 Application Insights 資源。

    開啟 `ApplicationInsights.config` 並插入下面這行：

    `<InstrumentationKey>` *您複製的檢測金鑰* `</InstrumentationKey>`

    使用您從 Application Insights 資源複製的檢測金鑰。

    或者，您可以[在執行階段設定金鑰][apidynamicikey]。這可讓您直接從不同環境 (開發、測試、生產環境) 將遙測傳送至不同資源。

## 執行您的應用程式

在您的開發電腦上以偵錯模式執行您的專案，或是將它發佈至 Azure。您可以使用它數分鐘的時間來產生一些遙測資料。

## <a name="monitor"></a>檢視遙測

返回 [Azure 入口網站][portal]，並且瀏覽至您的 Application Insights 資源。


在 [概觀] 圖表中尋找資料。剛開始的時候，您只會看見一或兩個資料點。例如：

![Click through to more data](./media/app-insights-asp-net/12-first-perf.png)

按一下任何圖表以查看詳細度量。[深入了解度量。][perf]

現在發佈應用程式並觀看資料累積情形。


#### 沒有資料？

* 開啟 [[搜尋][diagnostic]] 磚來查看個別事件。
* 使用應用程式、開啟不同頁面，以產生一些遙測。
* 請稍等片刻，然後按一下 [重新整理]。
* 請參閱[疑難排解][qna]。


## 完成安裝

若要取得您的應用程式的完整 360 度檢視，您需要執行一些動作：


* [將 JavaScript SDK 加入至網頁][client]，以取得瀏覽器型遙測，例如 Web 檢視計數、頁面載入時間、指令碼例外狀況，並讓您在頁面指令碼中撰寫自訂遙測。
* 加入相依性追蹤，診斷由資料庫或應用程式使用的其他元作所造成的問題：
 * [在您的 Azure Web 應用程式或 VM 中][azure]
 * [在您的內部部署 IIS 伺服器中][redfield]
* 從您最喜愛的記錄架構[擷取記錄追蹤][netlogs]
* 在用戶端、伺服器或兩者，[追蹤自訂事件和度量][api]，以深入了解應用程式的使用情況。
* [設定 Web 測試][availability]，以確認應用程式處於線上狀態且能夠回應。

#### 背景工作角色的追蹤要求

您可以藉由與追蹤 HTTP 要求相同的方式追蹤背景工作角色，來擷取背景工作角色呼叫的效能。在 Application Insights 中，要求遙測類型會測量一個單位的具名伺服器端工作，可以進行計時，而且可以獨立成功或失敗。由 SDK 會自動擷取 HTTP 要求時，您可以插入自己的程式碼，來追蹤對背景工作角色的要求。

以下是背景工作角色的典型執行迴圈：

```C#

    while (true)
    {
      Stopwatch s1 = Stopwatch.StartNew();
      var startTime = DateTimeOffset.UtcNow;
      try
      {
        // ... get and process messages ...

        s1.Stop();
        telemetryClient.TrackRequest("CheckItemsTable",
            startTime, s1.Elapsed, SUCCESS_CODE, true);
      }
      catch (Exception ex)
      {
        string err = ex.Message;
        if (ex.InnerException != null)
        {
           err += " Inner Exception: " + ex.InnerException.Message;
        }
        s1.Stop();
        telemetryClient.TrackRequest("CheckItemsTable", 
            startTime, s1.Elapsed, FAILURE_CODE, false);
        telemetryClient.TrackException(ex);

        // Don't flood if we have a bug in queue process loop.
        System.Threading.Thread.Sleep(60 * 1000);
      }
    }
```




[api]: app-insights-api-custom-events-metrics.md
[apidefaults]: app-insights-api-custom-events-metrics.md#default-properties
[apidynamicikey]: app-insights-api-custom-events-metrics.md#dynamic-ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

<!---HONumber=58-->