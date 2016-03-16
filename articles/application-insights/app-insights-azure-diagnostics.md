<properties
    pageTitle="將 Azure 診斷記錄傳送至 Application Insights"
    description="設定 Azure 雲端服務診斷記錄的詳細資料，該記錄會傳送至 Application Insights 入口網站。"
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
	ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="article"
	ms.date="11/17/2015"
    ms.author="awills"/>

# 設定 Azure 診斷以記錄至 Application Insights

當您在 Microsoft Azure 中設定雲端服務專案或虛擬機器時，[Azure 可以產生診斷記錄](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)。您可以將此記錄傳送至 Application Insights，以便分析它與 Application Insights SDK 從應用程式內傳送的診斷和使用狀況遙測。Azure 記錄檔會包含應用程式管理的事件，例如啟動、停止、當機，以及效能計數器。記錄檔也會包含應用程式中對 System.Diagnostics.Trace 的呼叫。

本文詳細說明診斷擷取的組態。

您必須在 Visual Studio 中安裝 Azure SDK 2.8。

## 取得 Application Insights 資源

為了達到最佳的體驗，[將 Application Insights SDK 新增至雲端服務應用程式的每個角色](app-insights-cloudservices.md)，或[您將在您的 VM 中執行的任何應用程式](app-insights-get-started.md)。然後您就可以傳送要分析的診斷資料，並且顯示相同的 Application Insights 資源。

或者，如果不想使用 SDK - 例如，如果應用程式已經處於線上狀態 - 您只能在 Azure 入口網站中[建立新的 Application Insights 資源](app-insights-create-new-resource.md)。選擇 **Azure 診斷**做為應用程式類型。


## 將 Azure 診斷傳送至 Application Insights

如果您可以更新應用程式專案，則在 Visual Studio 中選取每個角色、選擇其屬性，並且在 [設定] 索引標籤中，選取 [將診斷傳送至 Application Insights]。

如果您的應用程式已經處於線上狀態，則使用 Visual Studio 的伺服器總管或雲端服務總管以開啟應用程式的內容。選取 [傳送診斷至 Application Insights]。

在每個案例中，您都會被詢問您建立的 Application Insights 資源的詳細資料。

[深入了解設定雲端服務應用程式的 Application Insights](app-insights-cloudservices.md)。

## 設定 Azure 診斷配接器

只在您想要選取傳送至 Application Insights 的部分記錄檔時參閱。根據預設，會傳送所有項目，包括：Microsoft Azure 事件、效能計數器、追蹤從應用程式對 System.Diagnostics.Trace 的呼叫。

Azure 診斷會將資料儲存至 Azure 儲存體資料表。不過，您也可以在使用 Azure 診斷擴充 1.5 或更新版本時，藉由在組態中設定「接收器」和「通道」，將全部資料或資料的子集管線處理到 Application Insights。

### 將 Application Insights 設定為接收器

當您使用角色屬性來設定 [將資料傳送至 Application Insights] 時，Azure SDK (2.8 或更新版本) 會將 `<SinksConfig>` 元素新增至角色的公用 [Azure 診斷組態檔](https://msdn.microsoft.com/library/azure/dn782207.aspx)。

`<SinksConfig>` 會定義可以傳送 Azure 診斷資料的其他接收器位置。範例 `SinksConfig` 如下所示：

```xml

    <SinksConfig>
     <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
     </Sink>
    </SinksConfig> 

```

`ApplicationInsights` 元素指定檢測金鑰，它會識別在其中傳送 Azure 診斷資料的 Application Insights。當您選取資源時，會根據 `APPINSIGHTS_INSTRUMENTATIONKEY` 服務組態自動填入。(如果您想要手動設定它，從資源的 [Essentials] 下拉式清單取得金鑰。)

`Channels` 定義將會傳送至接收器的資料。通道的行為類似篩選器。`loglevel` 屬性可讓您指定通道將會傳送的記錄層級。可用值為：`{Verbose, Information, Warning, Error, Critical}`。

### 將資料傳送到接收器

將資料傳送到 Application Insights 接收器，方法是在 DiagnosticMonitorConfiguration 節點底下新增接收器屬性。將接收器元素新增至每個節點會指定您想要從該節點及其下的任何節點收集資料，並傳送到指定的接收器。

例如，由 Azure SDK 所建立的預設值是傳送所有的 Azure 診斷資料：

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

但是，如果您只想要傳送錯誤記錄檔，將接收器名稱限定為通道名稱：

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

請注意，我們使用我們所定義的接收器名稱，以及上面定義的通道名稱。

如果只想要傳送「詳細資訊」應用程式記錄檔至 Application Insights，則會將接收器屬性新增至 `Logs` 節點。

```xml

    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```

您也可以在階層的不同層級的組態中包含多個接收器。在此情況下，在階層最上層指定的接收器會做為全域設定，而在個別元素指定的接收器則做為該全域設定的覆寫。

以下的公用組態檔完整範例會將所有錯誤傳送至 Application Insights (在 `DiagnosticMonitorConfiguration` 節點指定)，此外還有應用程式記錄檔的「詳細資訊」層級記錄 (在 `Logs` 節點指定)。

```xml

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
            sinks="ApplicationInsights.MyLogData"/> 
       <!-- This specific info sent to this channel -->
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
```

![](./media/app-insights-azure-diagnostics/diagnostics-publicconfig.png)

對於這項功能有一些要注意的限制：

* 通道只是為了配合記錄類型 (而不是效能計數器) 使用。如果您對效能計數器元素指定通道，將會忽略它。 
* 通道的記錄層級不能超過 Azure 診斷所要收集的記錄層級。例如：您不能在「記錄」元素中收集「應用程式記錄」錯誤，並且嘗試傳送「詳細資訊」記錄至 Application Insight 同步處理。scheduledTransferLogLevelFilter 屬性一律必須收集與您正嘗試傳送到接收器的記錄相等或更多個記錄。 
* 您無法將 Azure 診斷擴充收集的任何 blob 資料傳送至 Application Insights。例如，目錄節點下指定的任何項目。針對損毀傾印，實際損毀傾印將仍可傳送至 blob 儲存體，並只會將損毀傾印所產生的通知傳送至 Application Insights。 

## 相關主題

* [使用 Application Insights 監視 Azure 雲端服務](app-insights-cloudservices.md)
* [使用 PowerShell 將 Azure 診斷傳送至 Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Azure 診斷組態檔](https://msdn.microsoft.com/library/azure/dn782207.aspx)

<!---HONumber=AcomDC_1125_2015-->