<properties
   pageTitle="Azure 雲端服務的 Application Insights"
   description="使用 Application Insights 有效地監視您的 Web 和背景工作角色"
   services="application-insights"
   documentationCenter=""
   authors="soubhagyadash"
   manager="douge"
   editor="alancameronwills"/>

<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.tgt_pltfrm="ibiza"
   ms.topic="article"
   ms.workload="tbd"
   ms.date="11/15/2015"
   ms.author="sdash"/>

# Azure 雲端服務的 Application Insights


*Application Insights 目前僅供預覽*

[Microsoft Azure 雲端服務應用程式](https://azure.microsoft.com/services/cloud-services/)可以由 [Visual Studio Application Insights][start] 監視可用性、效能、失敗和使用方式。當您取得有關應用程式在現實世界的效能和效率的意見反應時，您可以在每個開發生命週期中針對設計方向做出明智的抉擇。

![範例](./media/app-insights-cloudservices/sample.png)

您需要 [Microsoft Azure](http://azure.com) 的訂用帳戶。使用 Microsoft 帳戶登入，可能是針對 Windows、XBox Live 或其他 Microsoft 雲端服務具備的帳戶。


#### 使用 Application Insights 檢測的範例應用程式

看一看這個[範例應用程式](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)，其中將 Application Insights 新增到雲端服務，搭配兩個背景工作角色託管於 Azure 中。

以下將告訴您如何透過相同的方式來調整您的雲端服務專案。

## 為每個角色建立 Application Insights 資源

Application Insights 資源是您在其中分析和顯示遙測資料的位置。

1.  在 [Azure 入口網站][portal] 中，建立新的 Application Insights 資源。針對應用程式類型，選擇 ASP.NET 應用程式。 

    ![按一下 [新增]，然後按一下 [Application Insights]](./media/app-insights-cloudservices/01-new.png)

2.  取得檢測金鑰的副本。您馬上需要這個項目以設定 SDK。

    ![按一下 [屬性]，選取金鑰，然後按下 CTRL+C](./media/app-insights-cloudservices/02-props.png)


最好能夠從每個 Web 和背景工作角色針對資料建立不同的資源。

或者，您可以從所有角色僅將資料傳送至一個資源，但是設定[預設屬性][apidefaults]，讓您可以篩選或群組每個角色的結果。

## <a name="sdk"></a>在每個專案中安裝 SDK


1. 在 Visual Studio 中，編輯雲端應用程式專案的 NuGet 封裝。

    ![以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 封裝]](./media/app-insights-cloudservices/03-nuget.png)


2. 新增 [Application Insights for Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet 封裝。此 SDK 版本包含新增伺服器內容 (如角色資訊) 的模組。若為背景工作角色，請使用 Windows 服務的 Application Insights。

    ![搜尋「Application Insights」](./media/app-insights-cloudservices/04-ai-nuget.png)


3. 設定 SDK 以將資料傳送給 Application Insights 資源。

    在 `ServiceConfiguration.Cloud.cscfg` 檔案中將檢測金鑰設定為組態設定。([範例程式碼](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg))。
 
    ```XML
     <Role name="WorkerRoleA"> 
      <Setting name="APPINSIGHTS_INSTRUMENTATIONKEY" value="YOUR IKEY" /> 
     </Role>
    ```
 
    在適合的啟動函式中，從組態設定設定檢測金鑰：

    ```C#
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```

    請注意，Azure 診斷報告會使用組態設定的相同名稱 `APPINSIGHTS_INSTRUMENTATIONKEY`。


    對於應用程式中的每個角色執行這項操作。請參閱範例：
 
 * [Web 角色](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
 * [背景工作角色](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
 * [針對網頁](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13)   

4. 將 ApplicationInsights.config 設定為一律複製到輸出目錄。 

    (在 .config 檔案中，您會看到訊息詢問您將檢測金鑰放至該處。不過，針對雲端應用程式，最好是從 .cscfg 檔案中設定。這可確保角色會在入口網站中正確識別。)


#### 執行和發佈應用程式

執行應用程式，並且登入 Azure。開啟您建立的 Application Insights 資源，您會看到個別資料點顯示在[搜尋](app-insights-diagnostic-search.md)中，並且在[計量瀏覽器](app-insights-metrics-explorer.md)中彙總資料。

新增更多遙測 - 請參閱以下章節 - 然後再發佈應用程式以取得即時診斷和使用方式的意見反應。


#### 沒有資料？

* 開啟 [[搜尋][diagnostic]] 磚來查看個別事件。
* 使用應用程式、開啟不同頁面，以產生一些遙測。
* 請稍等片刻，然後按一下 [重新整理]。
* 請參閱[疑難排解][qna]。



## 更多遙測

以下各節說明如何從您的應用程式的不同層面取得其他的遙測。


## 從背景工作角色追蹤要求

在 Web 角色中，要求模組會自動收集有關 HTTP 要求的資料。如需有關如何覆寫預設收集行為的範例，請參閱[範例 MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)。

您可以藉由與追蹤 HTTP 要求相同的方式追蹤背景工作角色，來擷取背景工作角色呼叫的效能。在 Application Insights 中，要求遙測類型會測量一個單位的具名伺服器端工作，可以進行計時，而且可以獨立成功或失敗。由 SDK 會自動擷取 HTTP 要求時，您可以插入自己的程式碼，來追蹤對背景工作角色的要求。

請參閱檢測兩個範例背景工作角色以報告要求：[WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) 和 [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## Azure 診斷

[Azure 診斷](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)資料包含角色管理事件、效能計數器，以及應用程式記錄檔。您可以將這些項目傳送至 Application Insights，以便看到它們與您的遙測的其餘部分，讓診斷問題更容易。

如果角色意外失敗或無法啟動，Azure 診斷特別有用。

1. 以滑鼠右鍵按一下 [角色] \(不是專案！) 以開啟其 [屬性]，然後選取 [啟用診斷]、[將診斷傳送至 Application Insights]。

    ![搜尋「Application Insights」](./media/app-insights-cloudservices/21-wad.png)

    **或者如果您的應用程式已發佈且執行**，開啟 [伺服器總管] 或 [雲端總管]，以滑鼠右鍵按一下您的應用程式並選取相同的選項。

3.  選取相同的 Application Insights 資源做為其他遙測。

    如果您想要，可以在不同的服務組態 (雲端、本機) 設定不同的資源，以便分隔開發資料和即時資料。

3. 選擇性[排除某些 Azure 診斷](app-insights-azure-diagnostics.md)，這些診斷是您想要轉送至 Application Insights 的項目。預設值為所有項目。

### 檢視 Azure 診斷事件

哪裡可以找到診斷：

* 效能計數器顯示為自訂度量。 
* Windows 事件記錄檔顯示為追蹤和自訂事件。
* 應用程式記錄檔、ETW 記錄檔和任何診斷基礎結構記錄檔顯示為追蹤。

若要查看效能計數器和事件計數，開啟[計量瀏覽器](app-insights-metrics-explorer.md)並加入新的圖表：


![](./media/app-insights-cloudservices/23-wad.png)

使用[搜尋](app-insights-diagnostic-search.md)在各種 Azure 診斷傳送的追蹤記錄檔中搜尋。例如，如果您的角色中有未處理的例外狀況造成該角色當機和回收，該資訊會顯示在 [Windows 事件記錄檔] 的 [應用程式] 通道。您可以使用搜尋功能來查看 Windows 事件記錄檔錯誤並取得例外狀況的完整堆疊追蹤，讓您尋找問題的根本原因。


![](./media/app-insights-cloudservices/25-wad.png)

## 應用程式診斷

Azure 診斷會自動包含您的應用程式使用 System.Diagnostics.Trace 所產生的記錄項目。

但是如果您已經使用 Log4N 或 NLog 架構，您也可以[擷取其記錄追蹤][netlogs]。

在用戶端、伺服器或兩者中[追蹤自訂事件和度量][api]，以深入了解應用程式的效能和使用情況。

## 相依項目

Application Insights SDK 可以報告應用程式對外部相依性的呼叫，例如 REST API 和 SQL Server。這可讓您查看是否有特定的相依性造成回應變慢或失敗。

若要追蹤相依性，您必須搭配 [Application Insights 代理程式](app-insights-monitor-performance-live-website-now.md) (也稱為「狀態監視器」) 設定 Web/背景工作角色。

若要使用 Application Insights 代理程式搭配 Web/背景工作角色：

* 新增 [AppInsightsAgent](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent) 資料夾和兩個檔案到 Web/背景工作角色專案中。請務必設定其建置屬性，使它們一律複製到輸出目錄。這些檔案將安裝代理程式。
* 新增啟動工作到 CSDEF 檔案，如[這裡](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L18)所示。
* 注意：*背景工作角色*需要三個環境變數，如[這裡](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L44)所示。Web 角色則不需要這個設定。

以下是您在 Application Insights 入口網站中所看到的範例：

* 自動相互關聯的要求與相依性的豐富診斷：

    ![](./media/app-insights-cloudservices/SMxacy4.png)

* Web 角色的效能，包含相依性資訊：

    ![](./media/app-insights-cloudservices/6yOBtKu.png)

* 以下是背景工作角色的要求與相依性資訊的螢幕擷取畫面：

    ![](./media/app-insights-cloudservices/a5R0PBk.png)

## 例外狀況

如需如何從不同的 Web 應用程式類型收集未處理的例外狀況的資訊，請參閱[在 Application Insights 中監視例外狀況](app-insights-asp-net-exceptions.md)。

範例 Web 角色具有 MVC5 以及 Web API 2 控制器。來自 2 的未處理例外狀況可如下擷取：

* 針對 MVC5 控制器，如[這裡](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12)設定 [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs)
* 針對 Web API 2 控制器，如[這裡](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25)設定 [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs)

針對背景工作角色，有兩種方式來追蹤例外狀況。

* TrackException(ex)
* 如果您已新增 Application Insights 追蹤接聽項 NuGet 套件，您可以使用 System.Diagnostics.Trace 來記錄例外狀況。[程式碼範例。](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

## 效能計數器

根據預設會收集下列計數器：

    * \Process(??APP_WIN32_PROC??)\% Processor Time
	* \Memory\Available Bytes
	* \.NET CLR Exceptions(??APP_CLR_PROC??)# of Exceps Thrown / sec
	* \Process(??APP_WIN32_PROC??)\Private Bytes
	* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
	* \Processor(_Total)\% Processor Time

此外，也會為 Web 角色收集下列計數器：

	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec	
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

您可以指定額外的自訂計數器，或其他 Windows 效能計數器，如[這裡](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14)所示

  ![](./media/app-insights-cloudservices/OLfMo2f.png)

## 背景工作角色的相互關聯遙測

當您可以看見導致失敗或高延遲要求的原因時，診斷體驗會更加豐富。使用 Web 角色，SDK 會在關聯的遙測間自動設定相互關聯。針對背景工作角色，您可以使用自訂遙測初始設定式，來設定一個通用 Operation.Id 內容屬性，讓所有的遙測可以達到此目的。這可讓您查看是否因為相依性或程式碼導致延遲/失敗問題，一目了然！

方式如下：

* 設定相互關聯識別碼到 CallContext 中，如[這裡](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36)所示。在此案例中，我們使用「要求 ID」做為相互關聯識別碼
* 新增自訂的 TelemetryInitializer 實作，其會將 Operation.Id 設定到前面所設定的 correlationId。如這裡所示：[ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)
* 新增自訂的遙測初始設定式。您可以在 ApplicationInsights.config 檔案，或在程式碼中執行，如[這裡](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)所示

就這麼簡單！ 入口網站體驗已經準備好協助您一覽所有相關聯的遙測：

![](./media/app-insights-cloudservices/bHxuUhd.png)



## 用戶端遙測

[將 JavaScript SDK 加入至網頁][client]，以取得瀏覽器型遙測，例如 Web 檢視計數、頁面載入時間、指令碼例外狀況，並讓您在頁面指令碼中撰寫自訂遙測。

## 可用性集合

[設定 Web 測試][availability]，以確認應用程式處於線上狀態且能夠回應。



## 範例

[此範例](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)監視具有 Web 角色和兩個背景工作角色的服務。

## 在 Azure 雲端服務中執行時發生的「找不到方法」例外狀況

您是否已針對 .NET 4.6 組建？ Azure 雲端服務角色不自動支援 4.6。請先[在每個角色上安裝 4.6](../cloud-services/cloud-services-dotnet-install-dotnet.md)，再執行您的應用程式。

## 相關主題

* [設定將 Azure 診斷傳送至 Application Insights](app-insights-azure-diagnostics.md)
* [使用 PowerShell 將 Azure 診斷傳送至 Application Insights](app-insights-powershell-azure-diagnostics.md)



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
[start]: app-insights-overview.md

<!----HONumber=AcomDC_0224_2016-->