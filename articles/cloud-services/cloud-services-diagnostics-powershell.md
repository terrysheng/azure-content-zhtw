<properties 
	pageTitle="使用 PowerShell 在 Azure 雲端服務中啟用診斷 | Microsoft Azure" 
	description="了解如何使用 PowerShell 啟用雲端服務的診斷" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="sbtron" 
	manager="" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/09/2016" 
	ms.author="saurabh"/>


# 使用 PowerShell 在 Azure 雲端服務中啟用診斷

您可以使用 Azure 診斷延伸模組，從雲端服務收集診斷資料 (例如應用程式記錄檔、效能計數器等)。本文描述如何使用 PowerShell 啟用雲端服務的 Azure 診斷延伸模組。如需這篇文章所需要的必要條件，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。

## 啟用診斷延伸模組做為部署雲端服務的一部分

對於可以啟用診斷擴充來做為雲端服務佈署一部分的連續整合類型案例來說，這種方法是很有用的。當您建立新的雲端服務部署時，可以啟用診斷擴充，方法是將 *ExtensionConfiguration* 參數傳入 [New-AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx) Cmdlet。*ExtensionConfiguration* 參數接受以 [New-AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx) Cmdlet 建立的診斷組態陣列。

下列範例示範如何為某個雲端服務 (其中的 WebRole 和 WorkerRole 各自擁有不同的診斷組態) 啟用診斷。

	$service_name = "MyService"
	$service_package = "CloudService.cspkg"
	$service_config = "ServiceConfiguration.Cloud.cscfg"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

	$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
	$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath
	 
	New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig) 

如果診斷組態檔以某個儲存體帳戶名稱來指定 StorageAccount 元素，則 New-AzureServiceDiagnosticsExtensionConfig Cmdlet 會自動使用該儲存體帳戶。但該儲存體帳戶所屬的訂用帳戶，必須與雲端服務部署時所屬的訂用帳戶相同，這個方法才有作用。

從 Azure SDK 2.6 開始，由 MSBuild 發佈目標輸出所產生的擴充設定檔，會包含以服務組態檔 (.cscfg) 中所指定的診斷組態字串為基礎的儲存體帳戶名稱。下列指令碼示範在部署雲端服務時，如何從發佈目標輸出來剖析擴充設定檔，以及如何設定每個角色的診斷擴充。

	$service_name = "MyService"
	$service_package = "C:\build\output\CloudService.cspkg"
	$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"
	
	#Find the Extensions path based on service configuration file
	$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"
	
	$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
	$diagnosticsConfigurations = @()
	foreach ($extPath in $diagnosticsExtensions)
	{
	#Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
	$roleName = ""
	$roles = $extPath -split ".",0,"simplematch"
	if ($roles -is [system.array] -and $roles.Length -gt 1)
	    {
	    $roleName = $roles[1] 
	    $x = 2
	    while ($x -le $roles.Length)
	        {
	           if ($roles[$x] -ne "PubConfig")
	            {
	                $roleName = $roleName + "." + $roles[$x]
	            }
	            else
	            {
	                break
	            }
	            $x++
	        }
	    $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
	    $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
	    $diagnosticsConfigurations += $diagnosticsconfig
	    }
	}
	New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations

Visual Studio Online 使用類似的方法，來自動部署搭配診斷擴充的雲端服務。請參閱 [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) 來取得完整的範例。

如果您沒有在診斷組態中指定 StorageAccount，就必須將 StorageAccountName 參數傳入 Cmdlet。如果您已指定 StorageAccountName 參數，則 Cmdlet 一定會使用在此參數中指定的儲存體帳戶，而非在診斷組態檔中指定的儲存體帳戶。

如果診斷儲存體帳戶和雲端服務分別屬於不同的訂用帳戶，您必須明確地將 StorageAccountName 和 StorageAccountKey 參數傳入 Cmdlet。當診斷儲存體帳戶屬於同一個訂用帳戶時，您就不需要使用 StorageAccountKey 參數，因為 Cmdlet 會在啟用診斷擴充時自動查詢並設定金鑰值。不過，當診斷儲存體帳戶屬於不同的訂用帳戶時，Cmdlet 可能就無法自動取得金鑰，而您必須透過 StorageAccountKey 參數來明確指定金鑰。

	$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
	$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
 

## 在現有的雲端服務上啟用診斷延伸模組

您可以使用 [Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) Cmdlet，在執行中的雲端服務上啟用或更新診斷組態。


	$service_name = "MyService"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

	$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
	$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath
	
	Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name 
	  

## 取得目前的診斷延伸模組組態
使用 [Get AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) Cmdlet 取得雲端服務目前的診斷組態。
	
	Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## 移除診斷延伸模組
若要在雲端服務上關閉診斷，您可以使用 [Remove-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx) Cmdlet。

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

如果您在未使用 *Role* 參數的情況下使用 *Set-AzureServiceDiagnosticsExtension* 或 *New-AzureServiceDiagnosticsExtensionConfig* 啟用診斷延伸模組，則您可以在未使用 *Role* 參數的情況下使用 *Remove-AzureServiceDiagnosticsExtension* 移除延伸模組。如果啟用延伸模組時使用了 *Role* 參數，則移除延伸模組時也必須使用該參數。

若要從每個個別的角色移除診斷延伸模組：

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## 後續步驟

- 如需使用 Azure 診斷和其他技術疑難排解問題的詳細指引，請參閱[在 Azure 雲端服務和虛擬機器中啟用診斷](cloud-services-dotnet-diagnostics.md)。
- [診斷組態結構描述](https://msdn.microsoft.com/library/azure/dn782207.aspx)說明診斷延伸模組的各種 XML 組態選項。
- 若要了解如何啟用虛擬機器的診斷延伸模組，請參閱[使用 Azure 資源管理員範本建立具有監控和診斷功能的 Windows 虛擬機器](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)  

<!---HONumber=AcomDC_0323_2016-->