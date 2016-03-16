<properties pageTitle="使用 PowerShell 在執行 Windows 的虛擬機器中啟用 Azure 診斷 | Microsoft Azure" services="virtual-machines" documentationCenter="" description="了解如何在執行 Windows 的虛擬機器中啟用 Azure 診斷" authors="sbtron" manager="" editor="""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="saurabh"/>


# 使用 PowerShell 在執行 Windows 的虛擬機器中啟用 Azure 診斷

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure 診斷是 Azure 中可對部署的應用程式啟用診斷資料收集的功能。您可以使用診斷延伸模組，從執行 Windows 的 Azure 虛擬機器 (VM) 收集診斷資料 (例如應用程式記錄檔或效能計數器)。本文描述如何使用 Windows PowerShell 啟用 VM 的診斷延伸模組。如需這篇文章所需要的必要條件，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。

## 如果您使用資源管理員部署模型，請啟用診斷延伸模組

將延伸模組組態新增至資源管理員範本，即可在透過 Azure 資源管理員部署模型建立 Windows VM 時啟用診斷延伸模組。請參閱[使用 Azure 資源管理員範本建立具有監視和診斷的 Windows 虛擬機器](virtual-machines-extensions-diagnostics-windows-template.md)。

若要在透過資源管理員部署模型所建立的現有 VM 上啟用診斷延伸模組，您可以使用 [Set-AzureRMVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt603499.aspx) Powershell Cmdlet，如下所示。


	$vm_resourcegroup = "myvmresourcegroup"
	$vm_name = "myvm"
	$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

	Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


$diagnosticsconfig\_path 是包含診斷組態之 XML 檔案的路徑，如下面的[範例](#sample-diagnostics-configuration)所述。

如果診斷組態檔以儲存體帳戶名稱指定 **StorageAccount** 元素，則 Set-AzureRMVMDiagnosticsExtension 指令碼會自動設定診斷延伸模組，以將診斷資料傳送至該儲存體帳戶。此儲存體帳戶必須與 VM 位於相同的訂用帳戶才有作用。

如果未在診斷組態中指定 **StorageAccount**，則必須將 StorageAccountName 參數傳入到 Cmdlet。如果已指定 StorageAccountName 參數，則 Cmdlet 一律會使用在此參數中指定的儲存體帳戶，而非使用在診斷組態檔中指定的儲存體帳戶。

如果診斷儲存體帳戶位於與 VM 不同的訂用帳戶，您必須明確地將 StorageAccountName 和 StorageAccountKey 參數傳入至 Cmdlet。當診斷儲存體帳戶位於相同的訂用帳戶時，並不需要 StorageAccountKey，因為 Cmdlet 可以在啟用診斷延伸模組時自動查詢和設定金鑰值。不過，如果診斷儲存體帳戶位於不同的訂用帳戶，則 Cmdlet 可能無法自動取得金鑰，而您必須明確地透過 StorageAccountKey 參數指定金鑰。

	Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

在 VM 上啟用診斷延伸模組之後，您就可以使用 [Get-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603678.aspx) Cmdlet 取得目前的設定。

	Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

Cmdlet 所傳回的 PublicSettings 包含 Base64 編碼格式的 XML 組態。若要讀取 XML，您必須將它解碼。

	$publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
	$encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
	$xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
	Write-Host $xmlconfig

[Remove-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603782.aspx) Cmdlet 可以用來從 VM 移除診斷延伸模組。

## 如果您使用傳統部署模型，請啟用診斷延伸模組

您可以使用 [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) Cmdlet 在透過傳統部署模型所建立的 VM 上啟用診斷延伸模組。下列範例示範如何透過已啟用診斷延伸模組的傳統部署模型，建立新的 VM。

	$VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
	$VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
	$VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
	New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

若要在透過傳統部署模型所建立的現有 VM 上啟用診斷延伸模組，請先使用 [Get-AzureVM](https://msdn.microsoft.com/library/mt589152.aspx) Cmdlet 來取得 VM 組態。然後更新 VM 組態，以使用 [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) Cmdlet 納入診斷延伸模組。最後，使用 [Update-AzureVM](https://msdn.microsoft.com/library/mt589121.aspx) 將更新後的組態套用至 VM。

	$VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
	$VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
	Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## 範例診斷組態

下列 XML 可用於具有上述指令碼的診斷公用組態。此範例組態會連同 Windows 事件記錄檔中的應用程式、安全性和系統通道的錯誤和診斷基礎結構記錄檔中的任何錯誤，將各種效能計數器一併移轉至診斷儲存體帳戶。

您需要更新組態以包含下列各項：

- 需要以 VM 的資源識別碼更新 [計量] 元素的 resourceID 屬性。
	- 可以使用下列模式來建構資源識別碼："/subscriptions/{具有 VM 之訂用帳戶的訂用帳戶 ID}/resourceGroups/{VM 的資源群組名稱}/providers/Microsoft.Compute/virtualMachines/{VM 名稱}"。
	- 例如，如果 VM 執行所在的訂用帳戶的訂用帳戶識別碼為 **11111111-1111-1111-1111-111111111111**、資源群組的資源群組名稱為 **MyResourceGroup** 和 VM 名稱為 **MyWindowsVM**，則 resourceID 的值會是：

		```
		<Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
		```

	- 如需有關如何根據效能計數器和計量組態產生計量的詳細資訊，請參閱[儲存體中的 Azure 診斷計量資料表](virtual-machines-extensions-diagnostics-windows-template.md#wadmetrics-tables-in-storage)

- 需要以診斷儲存體帳戶名稱更新 **StorageAccount** 元素。

	```
	<?xml version="1.0" encoding="utf-8"?>
	<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <WadCfg>
	      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
	        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
	        <PerformanceCounters scheduledTransferPeriod="PT1M">
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU utilization" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU privileged time" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU user time" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
	        <annotation displayName="CPU frequency" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Processes" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Threads" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Handles" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Memory usage" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory available" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory committed" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory commit limit" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory paged pool" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory non-paged pool" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active time" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active read time" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active write time" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk operations" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk read operations" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk write operations" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk speed" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk read speed" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk write speed" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average queue length" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average read queue length" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average write queue length" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk free space (percentage)" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk free space (MB)" locale="zh-TW"/>
	      </PerformanceCounterConfiguration>
	    </PerformanceCounters>
		<Metrics resourceId="(Update with resource ID for the VM)" >
	        <MetricAggregation scheduledTransferPeriod="PT1H"/>
	        <MetricAggregation scheduledTransferPeriod="PT1M"/>
	    </Metrics>
	    <WindowsEventLog scheduledTransferPeriod="PT1M">
	      <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
	      <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
	      <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
	    </WindowsEventLog>
	      </DiagnosticMonitorConfiguration>
	    </WadCfg>
	    <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
	</PublicConfig>
	```

## 後續步驟
- 如需使用 Azure 診斷功能和其他技術疑難排解問題的詳細指引，請參閱[在 Azure 雲端服務和虛擬機器中啟用診斷](cloud-services-dotnet-diagnostics.md)。
- [診斷組態結構描述](https://msdn.microsoft.com/library/azure/mt634524.aspx)說明診斷延伸模組的各種 XML 組態選項。

<!---HONumber=AcomDC_0121_2016-->