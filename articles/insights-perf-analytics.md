<properties 
	pageTitle="監視應用程式效能" 
	description="負載和回應時間、 相依性資訊和設定警示在效能上的圖表。" 
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="awills"/>

# 監視應用程式效能

在[Azure 入口網站](http://portal.azure.com)您可以設定監視的應用程式相依性，在您的 web 應用程式或虛擬機器中收集的統計資料和詳細資料。

透過採用「擴充功能」，Azure 可支援應用程式效能監視 (或「APM」)。*APM*) 利用*擴充功能*安裝 Node.js。這些擴充功能會安裝到您的應用程式，並會收集資料然後回報給監視服務。

## 新增擴充程式

1. 按一下**瀏覽**按一下 [瀏覽]，然後選取您要檢測的 Web 應用程式或虛擬機器。

2. 按一下連結**應用程式監視**在磚**監視**安裝 Node.js。

3. 選擇您想要使用，例如延伸模組提供者**Application Insights**或**New Relic**安裝 Node.js。

![Web 應用程式](./media/insights-perf-analytics/05-extend.png)

或者，如果您正在使用虛擬機器：

![虛擬機器](./media/insights-perf-analytics/10-vm1.png)


## 探索程式碼

使用您的應用程式來產生一些遙測一段時間。

1. 接著，從您的 Web 應用程式或虛擬機器刀鋒視窗，您將會看到已安裝的擴充功能
2. 按一下代表您的應用程式瀏覽至該提供者的資料列：![按一下 [重新整理]](./media/insights-perf-analytics/06-overview.png)

您也可以使用**瀏覽**若要直接移至 Application Insights 元件或您所用的 New Relic 帳戶。

當您進入 Application Insights 的分頁，比方說，您可以：-開啟效能：

![在 Application Insights 概觀 blade 中，按一下 [效能] 磚](./media/insights-perf-analytics/07-dependency.png)

- 鑽研以查看個別要求：

![在方格中，按一下要查看相關的要求的相依性。](./media/insights-perf-analytics/08-requests.png)

- 以下是時間的範例，顯示 SQL 相依性，包括 SQL 呼叫和相關的統計資料，例如的平均持續時間和標準差數目所花費量。 

![](./media/insights-perf-analytics/01-example.png) 

### 適用於特定類型之應用程式的額外要求

New Relic 可以自動安裝而不需任何其他的檢測，但 Application Insights 有一個額外的需求。

![在 [新增專案] 對話方塊中，選取 [Application Insights]。](./media/insights-perf-analytics/03-add.png)

當系統要求您登入時，使用您的 Azure 帳戶的認證。

## 後續步驟

* [監視服務的健全狀況度量](insights-how-to-customize-monitoring.md)確保您的 Web 服務可用且迅速回應。
* [啟用監視和診斷](insights-how-to-use-diagnostics.md)您的服務收集詳細的頻率高的度量。
* [接收警示通知](insights-receive-alert-notifications.md)每當操作事件發生的事件或超過臨界值標準。
* 在 HDInsight 叢集 2.0 或 3.0 版上使用[適用於 JavaScript 應用程式和網頁 application Insights](application-insights/app-insights-web-track-usage.md)若要取得有關造訪的網頁瀏覽器的用戶端分析。
* [監視任何網站的可用性和回應性](application-insights/app-insights-monitor-web-app-availability.md)使用 Application Insights，您可以查明您的頁面已關閉。
