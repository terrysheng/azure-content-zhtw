<properties 
	pageTitle="疑難排解 Java Web 專案中的 Application Insights" 
	description="疑難排解指南以及問題和答案。" 
	services="application-insights" 
	documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="awills"/>
 
# Application Insights for Java 的疑難排解和問答集

[Java 中的 Visual Studio Application Insights][java] 疑問或問題？以下是一些秘訣。


## 建置錯誤

*在 Eclipse 中，透過 Maven 或 Gradle 加入 Application Insights SDK 時，我收到建置或總和檢查碼驗證錯誤。*

* 如果相依性 <version> 元素使用具有萬用字元的模式 (例如 <version>[0.9,)</version>)，請嘗試改為指定特定版本 (例如 <version>0.9.1</version>)

## 沒有資料 

*我已成功加入 Application Insights Insights 並執行我的應用程式，但在入口網站中從未看到資料。*

* 請稍等片刻，然後按一下 [重新整理]。重新整理目前不是自動的。
* 檢查 ApplicationInsights.xml 檔案 (位於專案的 resources 資料夾) 中已定義檢測機碼
* 確認  xml 檔案中沒有 `<DisableTelemetry>true</DisableTelemetry>` 節點。
* 在防火牆中，您可能必須開啟 TCP 連接埠 80 和 443，以允許連出流量送往 dc.services.visualstudio.com 和 f5.services.visualstudio.com。
* 在 Microsoft Azure 開始面板中，查看服務狀態對應。如果看到一些警示指示，請等待它們恢復 [正常]，然後關閉再重新開啟 Application Insights 應用程式刀鋒視窗。
* 在 ApplicationInsights.xml 檔案 (位於專案的 resources 資料夾) 的根節點下加入 <SDKLogger /> 元素，以開啟記錄到 IDE 主控台視窗，並檢查前面加上 [Error] 的項目。


## 沒有使用狀況資料

*我看到要求和回應時間的相關資料，但沒有頁面檢視、瀏覽器或使用者資料的相關資料。*

您已成功設定應用程式從伺服器傳送遙測。現在，您的下一步是[設定網頁，以從網頁瀏覽器傳送遙測][usage]。

或者，如果您的用戶端是[電話或其他裝置][platforms]中的應用程式，則可以從該處傳送遙測。 

使用相同的檢測機碼來設定用戶端和伺服器遙測。資料會出現在相同的 Application Insights 資源中，而且您可以相互關聯來自用戶端和伺服器的事件。



## 停用遙測

*如何停用遙測收集？*

在程式碼中：

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**或** 

更新 ApplicationInsights.xml (位於專案的 resources 資料夾)。在根節點下加入下列程式碼：

    <DisableTelemetry>true</DisableTelemetry>

如果使用 XML 方法，則必須在變更值時重新啟動應用程式。

## 變更目標

*如何變更我的專案將資料傳送到哪一個 Azure 資源？*

* [取得新資源的檢測機碼。][java]
* 如果您使用 Azure Toolkit for Eclipse 將 Application Insights 加入專案，請在 Web 專案上按一下滑鼠右鍵，並依序選取 [**Azure**] 和 [**設定 Application Insights**]，然後變更機碼。
* 否則，請更新專案之 resources 資料夾的 ApplicationInsights.xml 中的機碼。


## Azure 開始畫面

*我正在查看 [Azure 入口網站](http://portal.azure.com)。地圖是否告知有關我的應用程式的相關資訊？*

否，它會顯示世界各地 Azure 伺服器的健全狀況。

*從 Azure 開始面板 (主畫面) 中，如何找到我應用程式的相關資料？*

假設您[設定 Application Insights 的應用程式][java]，並按一下 [瀏覽]，且選取 [Application Insights]，然後選取您為應用程式所建立的應用程式資源。日後若要更快速地從該處開始，您可以將應用程式釘選至開始面板。

## 內部網路伺服器

*可以在我的內部網路上監視伺服器嗎？*

是，前提是您的伺服器可以透過公用網際網路將遙測傳送至 Application Insights 入口網站。 

在防火牆中，您可能必須開啟 TCP 連接埠 80 和 443，以允許連出流量送往 dc.services.visualstudio.com 和 f5.services.visualstudio.com。

## 資料保留 

*資料保留在入口網站多久的時間？是否安全？*

請參閱[資料保留和隱私權][data]。

## 後續步驟

*設定 Java 伺服器應用程式的 Application Insights。我還可以做什麼？*

* [監視網頁可用性][availability]
* [監視網頁使用狀況][usage]
* [追蹤使用狀況並診斷裝置應用程式中的問題][platforms]
* [撰寫程式碼以追蹤應用程式使用狀況][track]
* [擷取診斷記錄][javalogs]


## 取得說明

* [堆疊溢位](http://stackoverflow.com/questions/tagged/ms-application-insights)

[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]





<!--HONumber=49-->