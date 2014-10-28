<properties title="Search diagnostic logs with Application Insights" pageTitle="Search diagnostic logs" description="Search logs generated with Trace, NLog, or Log4Net." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# 使用 Application Insights 搜尋診斷記錄

您可以從 System.Diagnostics.Trace、NLog 和 Log4Net 中擷取和搜尋診斷記錄。Application Insights 提供有效率又簡單易用的工具，讓您從一或多個來源收集和調查已記錄的事件，可輔助應用程式健全狀況監控功能。

監控的 Web 應用程式可以託管於內部部署或虛擬機器中，也可以是 Microsoft Azure 網站。

1.  [加入記錄配接器][加入記錄配接器]

-   [設定診斷收集][設定診斷收集]
-   [插入記錄陳述式、建置和部署][插入記錄陳述式、建置和部署]
-   [檢視記錄資料][檢視記錄資料]
-   [搜尋資料][搜尋資料]
-   [後續步驟][後續步驟]

## <a name="add"></a>1. 加入記錄配接器

1.  在 Visual Studio 中，[將 Application Insights 加入至 Web 服務專案][將 Application Insights 加入至 Web 服務專案] (如果尚未這樣做)。

    如果是在將記錄加入至專案之後才加入 Application Insights，您可以發現記錄配接器已設定 - 這時只需要[重新部署專案][插入記錄陳述式、建置和部署]和[檢視資料][檢視記錄資料]。

2.  在 [方案總管] 中，在專案的操作功能表中選擇 [管理 NuGet 封裝]。
3.  選取 [線上] \> [全部]，選取 [Include Prerelease]，然後搜尋 "Microsoft.ApplicationInsights"

    ![Get the prerelease version of the appropriate adapter][Get the prerelease version of the appropriate adapter]

4.  選取適當封裝的發行前版本 - 下列其中一個：

-   Microsoft.ApplicationInsights.TraceListener
-   Microsoft.ApplicationInsights.NLogTarget
-   Microsoft.ApplicationInsights.Log4NetAppender

## <a name="configure"></a>2. 設定診斷收集

### System.Diagnostics.Trace

在 Web.config 中，將下列程式碼插入至 `<configuration>` 區段：

    <system.diagnostics>
     <trace autoflush="true" indentsize="0">
      <listeners>
       <add name="myAppInsightsListener"  
          type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, 
         Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
     </trace>
    </system.diagnostics> 

### NLog

在 Nlog.config 中，將下列片段合併至 `<extensions>`、`<targets>` 和 `<rules>` 區段。必要時建立這些區段。

    <extensions> 
     <add  assembly="Microsoft.ApplicationInsights.NLogTarget" /> 
    </extensions> 

    <targets>
     <target xsi:type="ApplicationInsightsTarget" name="aiTarget"/>
    </targets>

    <rules>
     <logger name="*" minlevel="Trace" writeTo="aiTarget"/>
    </rules>

### Log4Net

在 Web.config 中，將這些片段合併至 `<configsections>` 和 `<log4net>` 區段：

    <configSections>
      <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
    </configSections>

    <log4net>
     <root>
      <level value="ALL" /> <appender-ref ref="aiAppender" />
     </root>
     <appender name="aiAppender" type="Microsoft.ApplicationInsights.Log4NetAppender.ApplicationInsightsAppender, Microsoft.ApplicationInsights.Log4NetAppender">
      <layout type="log4net.Layout.PatternLayout">
       <conversionPattern value="%date [%thread] %-5level %logger - %message%newline" />
      </layout>
     </appender>
    </log4net>

## <a name="deploy"></a>3. 插入記錄陳述式、建置和部署

使用您選擇的記錄架構來插入事件記錄呼叫。例如，若是使用 Log4Net，您的呼叫可能如下：

    log.Warn("Slow response - database01");

在開發和正式運作環境中，記錄的事件都會傳送至 Application Insights。

## <a name="view"></a>4. 檢視記錄資料

在 Application Insights 中，開啟診斷搜尋。

![Open diagnostic search][Open diagnostic search]

選取任何記錄事件來查看詳細資料。

![Open diagnostic search][1]

可用的欄位視記錄架構和您在呼叫中使用的參數而定。

您可以使用單純字串 (無萬用字元) 來篩選項目內的欄位資料。

## <a name="search"></a>5. 搜尋資料

設定時間範圍並搜尋詞彙。搜尋較短的範圍會比較快。

![Open diagnostic search][2]

請注意您是搜尋詞彙，而不是子字串。詞彙是英數字串，包含 '.' 和 '\_' 之類的標點符號。例如：

<table>
  <tr><th>詞彙</th><th>「不」符合</th><th>但這些符合</th></tr>
  <tr><td>HomeController.About</td><td>about<br/>home</td><td>h*about<br/>home*</td></tr>
  <tr><td>IsLocal</td><td>local<br/>is<br/>*local</td><td>isl*<br/>islocal<br/>i*l</td></tr>
  <tr><td>New Delay</td><td>w d</td><td>new<br/>delay<br/>n* AND d*</td></tr>
</table>

以下是您可以使用搜尋運算式：

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><p>範例查詢</p></th>
<th align="left"><p>效果</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><span class="code">slow</span></p></td>
<td align="left"><p>在日期範圍中尋找欄位含有詞彙 &quot;slow&quot; 的所有事件</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">database??</span></p></td>
<td align="left"><p>符合 database01、databaseAB、...</p>
<p>? 不允許出現在搜尋詞彙的開頭。</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">database*</span></p></td>
<td align="left"><p>符合 database、database01、databaseNNNN</p>
<p>* 不允許出現在搜尋詞彙的開頭</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">apple AND banana</span></p></td>
<td align="left"><p>尋找同時含有這些詞彙的事件。請使用大寫 &quot;AND&quot;，而不是 &quot;and&quot;。</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">apple OR banana</span></p>
<p><span class="code">apple banana</span></p></td>
<td align="left"><p>尋找含有任一詞彙的事件。請使用 &quot;OR&quot;，而不是 &quot;or&quot;。</p>
<p>簡短格式。</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">apple NOT banana</span></p>
<p><span class="code">apple -banana</span></p></td>
<td align="left"><p>尋找含有一個詞彙但不含另一個詞彙的事件。</p>
<p>簡短格式。</p></td>
</tr>
<tr class="odd">
<td align="left"><p>app* AND banana NOT (grape OR pear)</p>
<p><span class="code">app* AND banana -(grape pear)</span></p></td>
<td align="left"><p>邏輯運算子和括號。</p>
<p>簡短格式。</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">message:slow</span></p>
<p><span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span></p>
<p><span class="code">properties.logEventInfo.level:Error</span></p></td>
<td align="left"><p>符合指定的欄位。依預設會搜尋所有欄位。若要查看可用的欄位，請選取事件來查看詳細資料。</p></td>
</tr>
</tbody>
</table>

## <a name="add"></a>後續步驟

-   [將 Application Insights 加入至專案][將 Application Insights 加入至 Web 服務專案]
-   [設定可用性和回應性測試][設定可用性和回應性測試]
-   [疑難排解][疑難排解]

## 詳細資訊

-   [Application Insights][Application Insights]
-   [將 Application Insights 加入至專案][將 Application Insights 加入至 Web 服務專案]
-   [立即監視即時 Web 伺服器][立即監視即時 Web 伺服器]
-   [在 Application Insights 中探索度量][在 Application Insights 中探索度量]
-   [診斷記錄搜尋][診斷記錄搜尋]
-   [使用 Web 測試來追蹤可用性][設定可用性和回應性測試]
-   [使用事件和度量來追蹤使用量][使用事件和度量來追蹤使用量]
-   [問與答及疑難排解][疑難排解]

<!--Link references-->

  [加入記錄配接器]: #add
  [設定診斷收集]: #configure
  [插入記錄陳述式、建置和部署]: #deploy
  [檢視記錄資料]: #view
  [搜尋資料]: #search
  [後續步驟]: #next
  [將 Application Insights 加入至 Web 服務專案]: ../app-insights-monitor-application-health-usage/
  [Get the prerelease version of the appropriate adapter]: ./media/appinsights/appinsights-36nuget.png
  [Open diagnostic search]: ./media/appinsights/appinsights-30openDiagnostics.png
  [1]: ./media/appinsights/appinsights-32detail.png
  [2]: ./media/appinsights/appinsights-31search.png
  [設定可用性和回應性測試]: ../app-insights-monitor-web-app-availability/
  [疑難排解]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [立即監視即時 Web 伺服器]: ../app-insights-monitor-performance-live-website-now/
  [在 Application Insights 中探索度量]: ../app-insights-explore-metrics/
  [診斷記錄搜尋]: ../app-insights-search-diagnostic-logs/
  [使用事件和度量來追蹤使用量]: ../app-insights-track-usage-custom-events-metrics/
