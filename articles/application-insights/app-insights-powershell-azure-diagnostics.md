<properties
    pageTitle="使用 PowerShell 將 Azure 診斷傳送至 Application Insights | Microsoft Azure"
    description="自動設定 Azure 診斷以透過管道傳送至 Application Insights。"
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
	ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
	ms.date="11/17/2015"
    ms.author="awills"/>

# 使用 PowerShell 將 Azure 診斷傳送至 Application Insights

[Microsoft Azure](https://azure.com) 可以[設定為傳送 Azure 診斷](app-insights-azure-diagnostics.md) 至 [Visual Studio Application Insights](app-insights-overview.md)。診斷與 Azure 雲端服務和 Azure VM 相關。可輔助您從應用程式使用 Application Insights SDK 傳送的遙測資料。在 Azure 中自動建立新資源的程序中，您可以使用 PowerShell 設定診斷。

## 啟用診斷延伸模組做為部署雲端服務的一部分

`New-AzureDeployment` Cmdlet 具有 `ExtensionConfiguration` 參數，其採用診斷組態的陣列。使用 `New-AzureServiceDiagnosticsExtensionConfig` Cmdlet 建立可以診斷組態。例如：

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## 在現有的雲端服務上啟用診斷延伸模組

在現有的服務上，使用 `Set-AzureServiceDiagnosticsExtension`。

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## 取得目前的診斷延伸模組組態

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## 移除診斷延伸模組

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

如果您在未使用 Role 參數的情況下使用 `Set-AzureServiceDiagnosticsExtension` 或 `New-AzureServiceDiagnosticsExtensionConfig` 啟用診斷延伸模組，則您可以在未使用 Role 參數的情況下使用 `Remove-AzureServiceDiagnosticsExtension` 移除延伸模組。如果啟用延伸模組時使用了 Role 參數，則移除延伸模組時也必須使用該參數。

若要從每個個別的角色移除診斷延伸模組：

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## 另請參閱

* [使用 Application Insights 監視 Azure 雲端服務應用程式](app-insights-cloudservices.md)
* [將 Azure 診斷傳送至 Application Insights](app-insights-azure-diagnostics.md)
* [自動化設定警示](app-insights-powershell-alerts.md)

<!---HONumber=AcomDC_0128_2016-->