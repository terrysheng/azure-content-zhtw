<properties title="Data retention and storage in Application Insights" pageTitle="Application Insights 中的資料保留和儲存" description="保留和隱私權政策聲明" metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-11-14" ms.author="awills" />

# Application Insights 中的資料保留和儲存 

*Application Insights 目前為預覽階段。*


本文針對我們如何儲存您應用程式傳送給 Application Insights 的遙測，提供相關問題的解答。

Application Insights 會將資料儲存在美國的 Microsoft Azure 伺服器中。如需 Microsoft Azure 原則的詳細資料，請下載[安全性、 隱私權及法規遵循白皮書](http://go.microsoft.com/fwlink/?LinkId=392408)。下面回答一些 Application Insights 的相關特性。 

#### 儲存多少資料？ 

在 Application Insights 免費「預覽」階段，針對每個帳戶，我們會儲存： 

* 每秒最多 500 個遙測訊息 (或每分鐘 3 萬個)。 

* 每個月最多 1 千萬個頁面檢視或事件。 

如果超過這些限制會如何？ 

* 如果是短時間超過限制，我們會在計算之後留下訊息通知。您不會在 [診斷搜尋] 中看到它們。事件計數將會是正確的。平均度量會根據我們所保留的事件，因此應該有效。 

* 如果超過每月限制，則會停止事件記錄，因此您的度量在某個日期之後會顯示為零。 

####遙測會保留多久？ 

* 如果是執行個體資料，7 天內在 [診斷搜尋] 中都可看見。個別的頁面檢視、事件、記錄訊息、追蹤及例外狀況。 

* 如果是彙總的資料，13 個月內在 [度量總管] 中都可看見。度量、事件及例外狀況的相關統計資料 (類型、失敗的函式等等的計數) 會以 1 分鐘 (或更少) 粒紋保留 30 天，以及以 1 小時粒紋保留 13 個月。 

####資料存放在哪裡？ 

* 在美國。 

有提供將資料儲存在歐洲或其他地方的選項嗎？ 

* 尚未提供。 

####我的遙測資料有多安全？ 

資料會儲存在 Microsoft Azure 伺服器中及「Azure Preview 入口網站」中的帳戶，帳戶限制如＜Azure 安全性、隱私權及法規遵循＞文件中所述。如果是「Visual Studio Online 入口網站」中的帳戶，則適用＜VS Online 資料保護＞文件。 

Microsoft 人員對您的資料存取會受到限制。我們只有在獲得您的許可及為了支援您使用 Application Insights 而有必要時，才會存取您的資料。 

####資料在 Application Insights 伺服器中會加密嗎？ 

目前尚未。 

####個人識別資訊 (PII) 會傳送到 Application Insights 嗎？ 

是。既包括您自訂遙測中的程式碼傳送到 Application Insights 的 PII，也包括標準遙測中所含的 PII。Azure 隱私權聲明適用於 Application Insights。 

有數種方法可將資料傳送到入口網站。接著，資料就會顯示在 [診斷搜尋] 中。您組織的成員也可以匯出並下載資料。 

* 如果您安裝「狀態監視器」，或將 Application Insights 加入您的專案中，當發生例外狀況或效能警示時，就會擷取堆疊追蹤。這些可能包括實際的參數資料，例如 SQL 資料。 

* 如果您在程式碼中插入遙測呼叫 (例如 TrackEvent)，或是擷取記錄架構訊息，屬性可能會包括 PII。Microsoft Online Services 合約也適用。特別針對 Application Insights，您必須遵守適用於隱私權和資料收集的法令和規章。在收集資訊之前，您應該判斷是否需要進行「通知」或取得其他「同意」。 


####使用 Application Insights 時，會傳送哪些遙測？ 

有數個您可以安裝的元件。(請參閱 [Application Insights - 開始使用][start]。) 

<table>
<tr><th>您設定的事項</th><th>傳送到入口網站的遙測</th><th>是否可能包含機密資料？</th></tr>
<tr><td><a href="../app-insights-start-monitoring-app-health-usage/">將 Application Insights 加入至 Web 專案中</a></td>
  <td>度量：伺服器要求計數、伺服器回應時間</td>
  <td>否</td></tr>
<tr><td></td>
  <td>來自伺服器的例外狀況報告</td><td>堆疊傾印可能包含參數值</td></tr>
<tr><td><a href="../app-insights-web-track-usage-custom-events-metrics/">追蹤自訂事件和度量</a></td>
  <td>事件與您程式碼所附加的屬性</td>
  <td>是，如果您的程式碼在屬性或標題中傳送 PII</td></tr>
<tr><td><a href="../app-insights-search-diagnostic-logs/#trace">記錄和追蹤遙測</a></td><td>記錄和追蹤訊息</td><td>記錄訊息可能包含 PII</td></tr>
<tr><td><a href="../app-insights-web-track-usage/">在您的網頁中插入 AI 指令碼</a></td>
  <td>匿名的使用者和帳戶識別碼</td><td>否</td></tr>
<tr><td></td><td>匿名使用者工作階段的開始和結束</td><td>否</td></tr>
<tr><td></td><td>網頁 URI、載入時間、工作階段計時</td><td>否</td></tr>
<tr><td><a href="../app-insights-monitor-performance-live-website-now/">在您的伺服器上安裝狀態監視器</a></td>
  <td>相依性呼叫和持續時間</td>
  <td>呼叫資料可能包含參數，例如 SQL 欄位</td></tr>
<tr><td></td><td>CPU、記憶體、網路及其他資源計數器</td><td>否</td></tr>
<tr><td><a href="../app-insights-monitor-web-app-availability/">Web 測試</a></td><td>會從 Web 看到可用性和回應時間</td><td>否</td></tr>
</table>

## <a name="video"></a>視訊

#### 簡介

> [AZURE.VIDEO application-insights-introduction]

#### 開始使用

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]



<!--HONumber=35.2-->
 