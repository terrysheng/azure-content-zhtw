<properties 
	pageTitle="在 Eclipse 中利用 Java 開始使用 Application Insights" 
	description="使用 Eclipse 外掛程式來加入效能和使用量計數器監視到您使用 Application Insights 的 Java 網站" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/02/2016" 
	ms.author="awills"/>
 
# 在 Eclipse 中利用 Java 開始使用 Application Insights

Application Insights SDK 會透過 Java Web 應用程式傳送遙測，使得您可以分析使用量和效能。Application Insights 適用的 Eclipse 外掛程式會自動在您的專案中安裝 SDK，使得您可以取得內建的遙測，加上可以用來編寫自訂遙測的 API。


## 必要條件

目前外掛程式可用於 Eclipse 中的 Maven 專案和動態網站專案。([將 Application Insights 加入至其他類型的 Java 專案][java]。)

您需要：

* Oracle JRE 1.6 或更新版本
* [Microsoft Azure](https://azure.microsoft.com/) 訂用帳戶。(您可以從[免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。)
* [Eclipse IDE for Java EE Developers](http://www.eclipse.org/downloads/)、Indigo 或更新版本。
* Windows 7 或更新版本，或 Windows Server 2008 或更新版本

## 在 Eclipse 上安裝 SDK (一次)

您只需在每一部機器上執行一次此動作。此步驟會安裝工具組，然後工具組會將 SDK 加入到每個動態網站專案。

1. 在 Eclipse 中，按一下 [說明]，然後按一下 [安裝新軟體]。

    ![[說明] -> [安裝新軟體]](./media/app-insights-java-eclipse/0-plugin.png)

2. SDK 位在 Azure 工具組的 http://dl.windowsazure.com/eclipse。
3. 取消勾選 **[連絡所有更新網站...]**

    ![針對 Application Insights SDK，請清除 [連絡所有更新網站]](./media/app-insights-java-eclipse/1-plugin.png)

對每個 Java 專案遵循其餘的步驟。

## 在 Azure 中建立 Application Insights 資源

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 建立新 Application Insights 資源。  

    ![按一下 + 並選擇 [Application Insights]](./media/app-insights-java-eclipse/01-create.png)  
3. 將應用程式類型設定為 Java Web 應用程式。  

    ![填寫名稱，選擇 [Java Web 應用程式]，然後按一下 [建立]](./media/app-insights-java-eclipse/02-create.png)  
4. 尋找新資源的檢測金鑰。您很快需要將此資訊貼上到程式碼專案中。  

    ![在新資源概觀中，按一下 [屬性] 並複製檢測金鑰](./media/app-insights-java-eclipse/03-key.png)


## 將 Application Insights 加入至專案

1. 從 Java Web 專案的內容功能表將 Application Insights 加入。

    ![在新資源概觀中，按一下 [屬性] 並複製檢測金鑰](./media/app-insights-java-eclipse/02-context-menu.png)

2. 將您從 Azure 入口網站取得的檢測金鑰貼上。

    ![在新資源概觀中，按一下 [屬性] 並複製檢測金鑰](./media/app-insights-java-eclipse/03-ikey.png)


金鑰會隨著遙測的每個項目傳送，並告知 Application Insights 在您的資源中顯示它。

## 執行應用程式並查看度量

執行您的應用程式。

返回 Microsoft Azure 中的 Application Insights 資源。

[概觀] 分頁上會顯示 HTTP 要求資料。(如果沒有出現，請稍等片刻，然後按一下 [重新整理]。)

![伺服器回應、要求計數和失敗](./media/app-insights-java-eclipse/5-results.png)
 

逐一點選任何圖表以查看更詳細的度量。

![依名稱的要求計數](./media/app-insights-java-eclipse/6-barchart.png)


[深入了解度量。][metrics]

 

而檢視要求的屬性時，您可以查看與它關聯的遙測事件，例如要求和例外狀況。
 
![此要求的所有追蹤](./media/app-insights-java-eclipse/7-instance.png)


## 用戶端遙測

在 [快速啟動] 刀鋒視窗中，按一下 [取得程式碼] 來監視我的網頁：

![在您的應用程式概觀刀鋒視窗中，選擇 [快速入門]，取得程式碼以監視我的網頁。複製指令碼。](./media/app-insights-java-eclipse/02-monitor-web-page.png)

在 HTML 檔案的標頭插入程式碼片段。

#### 檢視用戶端資料

開啟您已更新的網頁，並使用它們。等待一、兩分鐘，然後回到 Application Insights，並開啟 [使用量] 刀鋒視窗。(從 [概觀] 刀鋒視窗上，向下捲動並按一下 [自使用量]。)

頁面檢視、使用者和工作階段度量將出現在 [使用量] 刀鋒視窗上：

![工作階段、使用者和頁面檢視](./media/app-insights-java-eclipse/appinsights-47usage-2.png)

[深入了解設定用戶端遙測。][usage]

## 發行您的應用程式

現在將您的應用程式發佈至伺服器供人使用，然後查看入口網站顯示的遙測。

* 請確定您的防火牆允許應用程式將遙測傳送至這些連接埠：

 * dc.services.visualstudio.com:443
 * dc.services.visualstudio.com:80
 * f5.services.visualstudio.com:443
 * f5.services.visualstudio.com:80


* 在 Windows 伺服器上，安裝：

 * [Microsoft Visual C++ 可轉散發套件](http://www.microsoft.com/download/details.aspx?id=40784)

    (這會啟用效能計數器。)

## 例外狀況與要求失敗

會自動收集未處理的例外狀況：

![](./media/app-insights-java-eclipse/21-exceptions.png)

若要收集其他例外狀況的資料，您有兩個選項：

* [在您的程式碼中插入 TrackException 的呼叫](app-insights-api-custom-events-metrics.md#track-exception)。 
* [在伺服器上安裝 Java 代理程式](app-insights-java-agent.md)。指定您想要觀看的方法。


## 監視方法呼叫和外部相依性

[安裝 Java 代理程式](app-insights-java-agent.md)以記錄指定的內部方法和透過 JDBC 發出的呼叫與計時資料。


## 效能計數器

在您的 [概觀] 刀鋒視窗中，向下捲動並按一下 [伺服器] 並排顯示。您會看到一些效能計數器。


![向下捲動，按一下 [伺服器] 磚](./media/app-insights-java-eclipse/11-perf-counters.png)

### 自訂效能計數器集合

若要停用效能計數器的一組標準集合，請將下列程式碼加入 ApplicationInsights.xml 檔案的根節點下：

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### 收集其他效能計數器

您可以指定要收集的其他效能計數器。

#### JMX 計數器 (由虛擬機器公開)

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

*	`displayName` – Application Insights 入口網站中顯示的名稱。
*	`objectName` – JMX 物件名稱。
*	`attribute` – 要提取的 JMX 物件名稱的屬性
*	`type` (選用) - JMX 物件屬性的類型：
 *	預設值：簡易類型，例如 int 或 long。
 *	`composite`：效能計數器資料的格式為 'Attribute.Data'
 *	`tabular`：效能計數器資料的格式為資料表列



#### Windows 效能計數器

每個 [Windows 效能計數器](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)是類別的成員 (以欄位是類別成員的相同方式)。類別可以是全域，或可以有一定數量或指定的執行個體。

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

*	displayName – Application Insights 入口網站中顯示的名稱。
*	categoryName – 與此效能計數器有關聯的效能計數器類別 (效能物件)。
*	counterName – 效能計數器的名稱。
*	instanceName – 效能計數器類別執行個體的名稱，或如果類別包含單一執行個體，則為空白字串 ("")。如果 categoryName 為 Process，而您要收集的效能計數器來自應用程式執行所在的目前 JVM 處理程序，請指定 `"__SELF__"`。

您的效能計數器會在[計量瀏覽器][metrics]中以自訂度量的形式顯示。

![](./media/app-insights-java-eclipse/12-custom-perfs.png)


### Unix 效能計數器

* [使用 Application Insights 外掛程式安裝 collectd](app-insights-java-collectd.md)，來取得各種不同的系統和網路資料。

## 可用性 Web 測試

Application Insights 可讓您定期測試網站，以檢查網站運作中且正常回應。[若要設定][availability]，請向下捲動來按一下 [可用性]。

![向下捲動，並依序按一下 [可用性]、[加入 Web 測試]](./media/app-insights-java-eclipse/31-config-web-test.png)

您將取得回應時間的圖表，以及若網站關閉還會取得電子郵件通知。

![Web 測試範例](./media/app-insights-java-eclipse/appinsights-10webtestresult.png)

[深入了解 Web 測試的可用性。][availability]

## 診斷記錄

如果您使用 Logback 或 Log4J (v1.2 或 v2.0) 進行追蹤，您可以將追蹤記錄自動傳送到 Application Insights，您可以在其中探索及搜尋記錄。

[深入了解診斷記錄][javalogs]

## 自訂遙測 

在 Java Web 應用程式中插入幾行程式碼，以了解使用者對它進行的動作或協助診斷問題。

您可以在網頁 JavaScript 和伺服器端 Java 中插入程式碼。

[貼上了解自訂遙測][track]



## 後續步驟

#### 偵測並診斷問題

* [加入 Web 用戶端遙測][usage]，從 Web 用戶端取得效能遙測。
* [設定 Web 測試][availability]，以確認應用程式處於線上狀態且能夠回應。
* [搜尋事件和記錄][diagnostic]以協助診斷問題。
* [擷取 Java、Log4J 或 Logback 追蹤][javalogs]

#### 追蹤流量

* [加入 Web 用戶端遙測][usage]，以監視頁面檢視和基本使用者度量。
* 在用戶端、伺服器或兩者，[追蹤自訂事件和度量][track]，以深入了解應用程式的使用情況。


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=AcomDC_0302_2016-->