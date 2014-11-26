<properties title="Monitor your web app's availability and responsiveness" pageTitle="Web tests in Application Insights" description="Make sure your web application is available and responsive. Get alerts if it becomes unavailable or responds slowly." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# 監視 Web 應用程式的可用性和回應性

開發 Web 應用程式之後，您可以設定 Web 測試來監視其可用性和回應性。Application Insights 會定期傳送來自全球各地的 Web 要求，如果應用程式回應太慢或完全沒有回應，則會警告您。

您可以為公用網際網路可存取的任何 HTTP 端點設定 Web 測試。

1.  [建立新的應用程式？][建立新的應用程式？]

-   [設定 Web 測試][設定 Web 測試]
-   [查看監視器報告][查看監視器報告]
-   [編輯或停用測試][編輯或停用測試]
-   [後續步驟][後續步驟]

## <a name="create"></a>1. 建立新的應用程式？

如果您已開始對此 Web 應用程式[監視使用情形和健全狀況][監視使用情形和健全狀況]，且想要在同樣的地方查看可用性資料，請跳過此步驟。

但如果您想要將這些結果分開，請登入 Microsoft Azure 預覽，在 Application Insights 中建立新的應用程式。

![New \> Application Insights][New \> Application Insights]

## <a name="setup"></a>2. 設定 Web 測試

在 Application Insights 的應用程式主要分頁中，點選空白 Web 測試磚。

![Click the empty availability test][Click the empty availability test]

> *已經有 Web 測試？請點選 Web 測試磚，然後選擇 [Add Webtest]。*

命名測試並提供您要測試的 URL。此 URL 必須可在公用網際網路上看見。

![Fill at least the URL of your website][Fill at least the URL of your website]

-   我們的伺服器會將測試位置的 Web 要求傳送至您的 URL。請選擇兩個或三個，以便區分網站的問題與網路問題。不能選取三個以上。

-   成功準則 - 指定可接受的 HTTP 傳回碼 - 通常是 200。

    您也可以指定在每個正確回應中可找到的字串。必須是單純字串，不含萬用字元。別忘了，如果內容變更，則可能需要更新。

-   警示 - 依預設，如果持續 15 分鐘重複失敗，則會傳送警示給您。但您可以將它變更為更敏感，也可以變更通知的電子郵件地址。

### 測試更多 URL

您可以隨意為更多 URL 加入更多測試。例如，除了測試首頁，您也可以測試搜尋的 URL 來確定資料庫在執行中。

![On the web tests blade, choose Add][On the web tests blade, choose Add]

## <a name="monitor"></a>3. 查看監視器報告

經過 1-2 分鐘後，關閉再重新開啟應用程式分頁(在此版本中，不會自動重新整理)。

![Summary results on the home blade][Summary results on the home blade]

這是摘要。如果您為此應用程式定義多個 Web 測試，則全部匯集在這裡。

點選 Web 測試分頁來查看 Web 測試清單。

開啟特定的 Web 測試。

![Click a specific webtest][Click a specific webtest]

在特定的 Web 測試分頁中，向下捲動至 [失敗的測試] 並挑選結果。

![Click a specific webtest][1]

結果會顯示失敗的原因。

![Webtest run result][Webtest run result]

如需詳細資訊，請下載結果檔案並在 Visual Studio 中檢查。

## <a name="edit"></a>4. 編輯或停用測試

開啟個別測試來編輯或停用。

![Edit or disable a web test][Edit or disable a web test]

當您在服務上進行維護時，您可能會想要停用 Web 測試。

## <a name="next"></a>後續步驟

[搜尋診斷記錄][搜尋診斷記錄]

[疑難排解][疑難排解]

## <a name="next"></a>詳細資訊

-   [Application Insights][Application Insights]
-   [將 Application Insights 加入至專案][監視使用情形和健全狀況]
-   [立即監視即時 Web 伺服器][立即監視即時 Web 伺服器]
-   [在 Application Insights 中探索度量][在 Application Insights 中探索度量]
-   [診斷記錄搜尋][搜尋診斷記錄]
-   [使用 Web 測試來追蹤可用性][使用 Web 測試來追蹤可用性]
-   [使用事件和度量來追蹤使用量][使用事件和度量來追蹤使用量]
-   [問與答及疑難排解][疑難排解]

<!--Link references-->

  [建立新的應用程式？]: #create
  [設定 Web 測試]: #setup
  [查看監視器報告]: #monitor
  [編輯或停用測試]: #edit
  [後續步驟]: #next
  [監視使用情形和健全狀況]: ../app-insights-monitor-application-health-usage/
  [New \> Application Insights]: ./media/appinsights/appinsights-11newApp.png
  [Click the empty availability test]: ./media/appinsights/appinsights-12avail.png
  [Fill at least the URL of your website]: ./media/appinsights/appinsights-13availChoices.png
  [On the web tests blade, choose Add]: ./media/appinsights/appinsights-16anotherWebtest.png
  [Summary results on the home blade]: ./media/appinsights/appinsights-14availSummary.png
  [Click a specific webtest]: ./media/appinsights/appinsights-15webTestList.png
  [1]: ./media/appinsights/appinsights-17-availViewDetails.png
  [Webtest run result]: ./media/appinsights/appinsights-18-availDetails.png
  [Edit or disable a web test]: ./media/appinsights/appinsights-19-availEdit.png
  [搜尋診斷記錄]: ../app-insights-search-diagnostic-logs/
  [疑難排解]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [立即監視即時 Web 伺服器]: ../app-insights-monitor-performance-live-website-now/
  [在 Application Insights 中探索度量]: ../app-insights-explore-metrics/
  [使用 Web 測試來追蹤可用性]: ../app-insights-monitor-web-app-availability/
  [使用事件和度量來追蹤使用量]: ../app-insights-track-usage-custom-events-metrics/
