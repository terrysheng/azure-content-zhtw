<properties 
	pageTitle="在 Application Insights 中監視 Docker 主機" 
	description="Docker 主機效能計數器、事件和例外狀況可以與來自容器化應用程式的遙測一起顯示在 Application Insights 上。" 
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
	ms.date="11/20/2015" 
	ms.author="awills"/>
 
# 在 Application Insights 中監視 Docker 主機

[Docker](https://www.docker.com/) 主機的效能計數器和例外狀況報告可以在 Application Insights 上繪製成圖表。在您的主機的容器中安裝 [Application Insights](app-insights-overview.md) 應用程式，它會顯示主機的整體效能計數器，以及其他映像。

使用 Docker，您可以在完成所有相依性的輕量級容器中散發應用程式。它們會在執行 Docker 引擎的任何主機電腦上執行。

Application Insights 可以監視您的 Docker 主機和容器的效能和活動。

![範例](./media/app-insights-docker/00.png)

(當然，如果您在容器中擁有任何應用程式的來源，在每個項目上[安裝 Application Insights SDK](app-insights-java-live) 以取得詳細的效能記錄和使用狀況追蹤。)

## 建立 Application Insights 資源

Application Insights 資源是您將在其中檢視和分析資料的位置。它裝載於 Microsoft Azure 入口網站。

1.	您將需要 [Microsoft Azure](https://azure.com) 訂用帳戶。(有隨用隨付的選項，如果您未使用服務就不必支付任何費用，而且您可以使用 Application Insights 的免費層。)
2.	登入 [Azure 入口網站](https://portal.azure.com)，並建立新的 Application Insights 資源。選擇 [其他] 做為應用程式類型。(這個選項只會影響您看到的初始選取圖表，而且並不重要)。

    ![範例](./media/app-insights-docker/01-new.png)
3.	您可以使用 [瀏覽]、[所有]，永遠取得新的監視資源。但是您也可以選擇將您的應用程式的磚放在 [首頁] 儀表板，每當您移至入口網站時都會看到。

    立即開啟新資源。
4.	新增 Docker 磚：選擇 [編輯]，然後選擇 [新增磚]，並且從資源庫拖曳 Docker 磚。它只會在一開始顯示空白圖表。

    ![範例](./media/app-insights-docker/03.png)

5. 開啟 [Essentials] 下拉式清單，然後複製檢測金鑰。您將會使用這個項目以告知 SDK 傳送遙測的位置。

保持該瀏覽器視窗就緒，因為您稍後即會返回以查看您的遙測。


## 在您的主機上安裝 Application Insights
 
現在您有其他位置可以顯示遙測，您可以設定會收集並傳送它的應用程式。1.登入您的 Docker 主機。2.在 Docker 中樞搜尋 Microsoft Application Insights 映像，並將它加入至主機。3.設定檢測金鑰：

    docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444

4. 執行映像。

## 檢視遙測

返回 Azure 入口網站中的 Application Insights 資源。

您很快就會看到來自 Docker 容器的資料抵達，尤其是如果您在 Docker 引擎上有其他容器執行。

逐一點選任何圖表以查看詳細資料。

以下是一些您可以取得的檢視。

### 依據主機的效能計數器，依據映像的活動


![範例](./media/app-insights-docker/10.png)

按一下任何映像名稱以取得詳細資訊。若要自訂檢視，按一下任何圖表、方格標題，或使用 [新增圖表]。

[深入了解計量瀏覽器](app-insights-metrics-explorer.md)。

## 個別事件


![範例](./media/app-insights-docker/12.png)

若要調查個別事件，請按一下 [[搜尋](app-insights-diagnostic-search.md)]。搜尋和篩選以尋找您想要的事件。按一下任何事件以查看詳細資料。
 
## 依據容器名稱的例外狀況
 

![範例](./media/app-insights-docker/14.png)



## 問答集

*Application Insights 可以給我哪些無法從 Docker 取得的功能？*

* 依據容器和映像的效能計數器的詳細分解圖。
* 將容器和應用程式資料整合在一個儀表板中。
* [匯出遙測](app-insights-export-telemetry.md)以進行資料庫、Power BI 或其他儀表板的進一步分析。

*如何從應用程式本身取得遙測？*

* 在應用程式中安裝 Application Insights SDK。針對下列項目深入了解：[Java Web Apps](app-insights-java-get-started.md)、[Windows Web Apps](app-insights-asp-net.md)。

<!---HONumber=AcomDC_1125_2015-->