<properties
   pageTitle="設定 Azure 診斷以將資料傳送至 Application Insights | Microsoft Azure"
   description="更新 Azure 診斷公用組態以傳送資料至 Application Insights。"
   services="multiple"
   documentationCenter=".net"
   authors="sbtron"
   manager=""
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/16/2015"
   ms.author="saurabh" />

# 設定 Azure 診斷以將資料傳送至 Application Insights

Azure 診斷會將資料儲存至 Azure 儲存體資料表。不過，您也可以在使用 Azure 診斷擴充 1.5 或更新版本時，藉由在組態中設定「接收器」和「通道」，將全部資料或資料的子集管線處理到 Application Insights。

這篇文章說明如何為 Azure 診斷擴充建立公用組態，以便將組態設定為傳送資料至 Application Insights。

## 將 Application Insights 設定為接收器

Azure 診斷擴充 1.5 在公用組態中推出 **<SinksConfig>** 元素。這會定義可以傳送 Azure 診斷資料的其他*接收器*位置。您可以指定 Application Insights 資源的詳細資料，要在其中隨著此 **<SinksConfig>** 傳送 Azure 診斷資料。**SinksConfig** 的範例看起來像這樣 -

	<SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig> 

針對 **Sink** 元素，*name* 屬性指定將用來唯一參考接收器的字串值。**ApplicationInsights** 元素指定 Application Insights 資源的檢測金鑰，在其中將會傳送 Azure 診斷資料。如果您沒有現有的 Application Insights 資源，請參閱[建立新的 Application Insights 資源](app-insights-create-new-resource.md)，以取得建立資源及取得檢測金鑰的詳細資訊。

如果您正在開發使用 Azure SDK 2.8 的雲端服務專案，封裝雲端服務專案時將會根據 **APPINSIGHTS\_INSTRUMENTATIONKEY** 服務組態設定，在公用組態中自動填入此檢測金鑰。請參閱[使用 Application Insights 搭配 Azure 診斷疑難排解雲端服務問題](cloud-services-dotnet-diagnostics-applicationinsights.md)。

**Channels** 元素可讓您針對將傳送到接收器的資料，定義一或多個 **Channels** 元素。通道可用作篩選器，並可讓您選取想要傳送給接收器的特定記錄層級。比方說，您可以收集詳細資訊記錄檔並將它們傳送至儲存體，但您可以選擇定義具有「錯誤」的記錄層級，並且當您透過該通道傳送記錄檔時，只會傳送錯誤記錄檔到該接收器。針對 **Channel**，*name* 屬性用來唯一參考該通道。*loglevel* 屬性可讓您指定通道將允許的記錄層級。可用的記錄層級，從最多到最少資訊依序為：詳細資訊 - 資訊 - 警告 - 錯誤 - 重大

## 將資料傳送至 Application Insights 接收器
定義 Application Insights 接收器之後，您可以藉由加入 *sink* 屬性到 **DiagnosticMonitorConfiguration** 節點下的元素來傳送資料到該接收器。加入 *sinks* 元素到每個節點會指定您想要從該節點及其下的任何節點收集資料，並傳送到指定的接收器。

例如，如果您想要傳送 Azure 診斷所要收集的所有資料，您可以將 *sink* 屬性直接加入 **DiagnosticMonitorConfiguration** 節點。將 *sinks* 的值設定為 **SinkConfig** 中所指定的接收器名稱。

	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">

如果只想要傳送錯誤記錄檔到 Application Insights 接收器，則可以將 *sinks* 的值設定為接收器名稱後面加上通道名稱，以句點隔開 (".")。例如，若只要將錯誤記錄檔傳送至 Application Insights 接收器，請使用上述 SinksConfig 中定義的 MyTopDiagdata 通道。
 
	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">

如果只想要傳送「詳細資訊」應用程式記錄檔至 Application Insights，則會加入 *sinks* 屬性至 **Logs** 節點。
	
	<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>

您也可以在階層的不同層級的組態中包含多個接收器。在此情況下，在階層最上層指定的接收器會做為全域設定，而在個別元素指定的接收器則做為該全域設定的覆寫。

以下的公用組態檔完整範例會將所有錯誤傳送至 Application Insights (在 **DiagnosticMonitorConfiguration** 節點指定)，此外還有應用程式記錄檔的「詳細資訊」層級記錄 (在**記錄**節點指定)。

    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
           sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
        <DiagnosticInfrastructureLogs />
        <PerformanceCounters>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="ApplicationInsights.MyLogData/>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
                sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
      </DiagnosticMonitorConfiguration>

    <SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>

![診斷公用組態](./media/azure-diagnostics-configure-applicationinsights/diagnostics-publicconfig.png)

對於這項功能有一些要注意的限制

- 通道只是為了配合記錄類型 (而不是效能計數器) 使用。如果您對效能計數器元素指定通道，將會忽略它。 
- 通道的記錄層級不能超過 Azure 診斷所要收集的記錄層級。例如：您不能在「記錄」元素中收集「應用程式記錄」錯誤，並且嘗試傳送「詳細資訊」記錄至 Application Insight 同步處理。*ScheduledTransferLogLevelFilter* 屬性一律必須收集與您正嘗試傳送到接收器的記錄相等或更多個記錄。 
- 您無法將 Azure 診斷擴充收集的任何 blob 資料傳送至 Application Insights。例如，*目錄*節點下指定的任何項目。針對損毀傾印，實際損毀傾印將仍可傳送至 blob 儲存體，並只會將損毀傾印所產生的通知傳送至 Application Insights。 


## 後續步驟

- 使用 [PowerShell](cloud-services-diagnostics-powershell.md) 來為您的應用程式啟用 Azure 診斷擴充。 
- 使用 [Visual Studio](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) 來為您的應用程式啟用 Azure 診斷擴充 

<!---HONumber=Nov15_HO4-->