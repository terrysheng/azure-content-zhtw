<properties
	pageTitle="使用資源管理員和 PowerShell 管理 Azure VM |Microsoft Azure"
	description="使用 Azure 資源管理員、範本和 PowerShell 管理虛擬機器。"
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/10/2015"
	ms.author="davidmu"/>

# 使用 Azure 資源管理員和 PowerShell 部署以及管理虛擬機器

> [AZURE.SELECTOR]
- [Portal](virtual-machines-windows-tutorial.md)
- [PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

管理 Microsoft Azure 中的資源時，使用 Azure PowerShell 與資源管理員範本可提供您許多功能和彈性。您可以使用本文中的工作來建立及管理虛擬機器資源。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]本文涵蓋的內容包括以資源管理員部署模型管理資源。您也可以使用[傳統部署模型](virtual-machines-windows-tutorial-classic-portal.md)管理這些資源。

這些工作會使用資源管理員範本和 PowerShell：

- [建立虛擬機器](#windowsvm)
- [使用特殊化磁碟建立虛擬機器](#customvm)
- [利用外部負載平衡器在虛擬網路中建立多個虛擬機器](#multivm)

這些工作只會使用 PowerShell：

- [移除資源群組](#removerg)
- [顯示虛擬機器的相關資訊](#displayvm)
- [啟動虛擬機器](#start)
- [停止虛擬機器](#stop)
- [重新啟動虛擬機器](#restart)
- [刪除虛擬機器](#delete)

開始之前，確定 Azure PowerShell 已經準備就緒。

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]



## Azure 資源管理員範本和資源群組

本文中的部分工作會為您示範如何使用 Azure 資源管理員範本和 Powershell，自動部署和管理 Azure 虛擬機器。

在 Microsoft Azure 中執行的應用程式，大部分在建立時會使用不同雲端資源類型的組合，例如一或多個虛擬機器和儲存體帳戶、SQL 資料庫或虛擬網路。有了 Azure 資源管理員範本之後，您就可以使用 JSON 的資源說明、相關設定和部署參數，來管理這些不同的資源。

定義好 JSON 資源範本後，您可以將其搭配 PowerShell 命令使用，在 Azure 中部署已定義的資源。您可以在 PowerShell 命令殼層中個別執行以下命令，或者合併到內含其他自動化邏輯的指令碼。

您使用 Azure 資源管理員範本建立的資源，會部署到新的或現有 *Azure 資源群組*。資源群組可以讓您將多個部署的資源當做一個邏輯群組，統一進行管理，這樣您就可以管理群組/應用程式的整體週期。

如果您想了解如何設計範本，請參閱[設計 Azure 資源管理員範本](resource-group-authoring-templates.md)。

### 建立資源群組

若要建立資源的工作，如果沒有資源群組，您將需要資源群組。

在下列命令中，將*資源群組名稱*取代為新資源群組的名稱，將 *Azure 位置*取代為您要找到資源的 Azure 資料中心位置，並執行命令：

	New-AzureResourceGroup -Name "resource group name" -Location "Azure location"

## <a id="windowsvm"></a>工作：建立虛擬機器

這項工作使用來自範本庫的範本。若要深入了解範本，請參閱[在美國西部部署簡單的 Windows VM](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/)。

![](./media/virtual-machines-deploy-rmtemplates-powershell/windowsvm.png)

在下列命令中，將*部署名稱*取代為您想要用於部署的名稱，將*資源群組名稱*取代為現有資源群組的名稱，並執行命令：

	New-AzureResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

以下是範例：

	New-AzureResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

系統會提示您輸入 JSON 檔案的 **parameters** 區段中的參數值：

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso

它會傳回類似下列畫面：

	VERBOSE: 10:56:59 AM - Template is valid.
	VERBOSE: 10:56:59 AM - Create template deployment 'TestDeployment'.
	VERBOSE: 10:57:08 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 10:57:11 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 10:57:11 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is running
	VERBOSE: 10:57:38 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is succeeded
	VERBOSE: 10:57:40 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 10:57:45 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 10:57:45 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:01:59 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded


	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 6:02:13 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    	Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     saacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso9875
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

如果您想要看到完成這項工作的視訊，請看這裡：

[AZURE.VIDEO deploy-a-windows-virtual-machine-with-azure-resource-manager-templates-and-powershell]

## <a id="customvm"></a>工作：使用特殊化磁碟建立虛擬機器

這項工作使用來自範本庫的範本。若要深入了解範本，請參閱[從特殊化 VHD 磁碟建立 VM](https://azure.microsoft.com/documentation/templates/201-vm-from-specialized-vhd/)。

在下列命令中，將*部署名稱*取代為您想要用於部署的名稱，將*資源群組名稱*取代為現有資源群組的名稱，並執行命令：

	New-AzureResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

以下是範例：

	New-AzureResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

系統會提示您輸入 JSON 檔案的 **parameters** 區段中的參數值：

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	osDiskVhdUri: http://saacct.blob.core.windows.net/vhds/osdiskforwindows.vhd
	osType: windows
	location: West US
	vmSize: Standard_A3
	...

> [AZURE.NOTE]以上所示的範例會使用存在於 saacct 儲存體帳戶中的 vhd 檔案。磁碟的名稱會提供做為範本的參數。

如果您想要看到完成這項工作的視訊，請看這裡：

[AZURE.VIDEO create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell]

## <a id="multivm"></a>工作：利用外部負載平衡器在虛擬網路中建立多個虛擬機器

這項工作使用來自範本庫的範本。若要深入了解範本，請參閱[從特殊化 VHD 磁碟建立 VM](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/)。

![](./media/virtual-machines-deploy-rmtemplates-powershell/multivmextlb.png)

在下列命令中，將*部署名稱*取代為您想要用於部署的名稱，將*資源群組名稱*取代為現有資源群組的名稱，並執行命令：

	New-AzureResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"

系統會提示您輸入 JSON 檔案的 **parameters** 區段中的參數值：

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	adminUserName: WebAdmin1
	adminPassword: *******
	dnsNameforLBIP: web07
	backendPort: 80
	vmNamePrefix: WEBFARM
	...

如果您想要看到完成這項工作的視訊，請看這裡：

[AZURE.VIDEO deploy-multi-vm-app-with-a-virtual-network-and-load-balancer-in-azure-resource-manager]

## <a id="removerg"></a>工作：移除資源群組

在下列命令中，將*資源群組名稱*取代為您想要移除的資員群組名稱，並執行命令：

	Remove-AzureResourceGroup  -Name "resource group name"

> [AZURE.NOTE]您可以使用 **– Force** 參數，略過確認提示。

系統會要您確認是否沒有使用 -Force 參數：

	Confirm
	Are you sure you want to remove resource group 'BuildRG'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

如果您想要看到完成這項工作的視訊，請看這裡：

[AZURE.VIDEO removing-a-resource-group-in-azure]

## <a id="displayvm"></a>工作：顯示虛擬機器的相關資訊

在下列命令中，將*資源群組名稱*取代為包含虛擬機器的資源群組名稱，將 *VM 名稱*取代為電腦的名稱，並執行命令：

	Get-AzureVM -ResourceGroupName "resource group name" -Name "VM name"

它會傳回類似下列畫面：

	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          : {
	                             "VirtualMachineSize": "Standard_D1"
	                           }
	Id                       : /subscriptions/{subscription-id}/resourceGroups/BuildRG/providers/Microso
	                           ft.Compute/virtualMachines/MyWindowsVM
	InstanceView             : null
	Location                 : westus
	Name                     : MyWindowsVM
	NetworkProfile           : {
	                             "NetworkInterfaces": [
	                               {
	                                 "Primary": null,
	                                 "ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/Bu
	                           ildRG/providers/Microsoft.Network/networkInterfaces/myVMNic"
	                               }
	                             ]
	                           }
	OSProfile                : {
	                             "AdminPassword": null,
	                             "AdminUsername": "WinAdmin1",
	                             "ComputerName": "MyWindowsVM",
	                             "CustomData": null,
	                             "LinuxConfiguration": null,
	                             "Secrets": [],
	                             "WindowsConfiguration": {
	                               "AdditionalUnattendContents": [],
	                               "EnableAutomaticUpdates": true,
	                               "ProvisionVMAgent": true,
	                               "TimeZone": null,
	                               "WinRMConfiguration": null
	                             }
	                           }
	Plan                     : null
	ProvisioningState        : Succeeded
	StorageProfile           : {
	                             "DataDisks": [],
	                             "ImageReference": {
	                               "Offer": "WindowsServer",
	                               "Publisher": "MicrosoftWindowsServer",
	                               "Sku": "2012-R2-Datacenter",
	                               "Version": "latest"
	                             },
	                             "OSDisk": {
	                               "OperatingSystemType": "Windows",
	                               "Caching": "ReadWrite",
	                               "CreateOption": "FromImage",
	                               "Name": "osdisk",
	                               "SourceImage": null,
	                               "VirtualHardDisk": {
	                                 "Uri": "http://saacct.blob.core.windows.net/vhds/osdiskforwindowssimple.vhd"
	                               }
	                             },
	                             "SourceImage": null
	                           }
	Tags                     : {}
	Type                     : Microsoft.Compute/virtualMachines

如果您想要看到完成這項工作的視訊，請看這裡：

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>工作：啟動虛擬機器

在下列命令中，將*資源群組名稱*取代為包含虛擬機器的資源群組名稱，將 *VM 名稱*取代為電腦的名稱，並執行命令：

	Start-AzureVM -ResourceGroupName "resource group name" -Name "VM name"

它會傳回類似下列畫面：

	EndTime             : 4/28/2015 11:11:41 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	Status              : Succeeded
	TrackingOperationId : e1705973-d266-467e-8655-920016145347
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	StatusCode          : OK

如果您想要看到完成這項工作的視訊，請看這裡：

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>工作：停止虛擬機器

在下列命令中，將*資源群組名稱*取代為包含虛擬機器的資源群組名稱，將 *VM 名稱*取代為電腦的名稱，並執行命令：

	Stop-AzureVM -ResourceGroupName "resource group name" -Name "VM name"

系統會要您確認：

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

它會傳回類似下列畫面：

	EndTime             : 4/28/2015 11:09:08 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:06:55 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 0c94dc74-c553-412c-a187-108bdb29657e
	RequestId           : 5cc9ddba-0643-4b5e-82b6-287b321394ee
	StatusCode          : OK

如果您想要看到完成這項工作的視訊，請看這裡：

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>工作：重新啟動虛擬機器

在下列命令中，將*資源群組名稱*取代為包含虛擬機器的資源群組名稱，將 *VM 名稱*取代為電腦的名稱，並執行命令：

	Restart-AzureVM -ResourceGroupName "resource group name" -Name "VM name"

它會傳回類似下列畫面：

	EndTime             : 4/28/2015 11:16:26 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:16:25 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 390571e0-c804-43ce-88c5-f98e0feb588e
	RequestId           : 7dac33e3-0164-4a08-be33-96205284cb0b
	StatusCode          : OK

如果您想要看到完成這項工作的視訊，請看這裡：

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>工作：刪除虛擬機器

在下列命令中，將*資源群組名稱*取代為包含虛擬機器的資源群組名稱，將 *VM 名稱*取代為電腦的名稱，並執行命令：

	Remove-AzureVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE]您可以使用 **– Force** 參數，略過確認提示。

系統會要您確認是否沒有使用 -Force 參數：

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

它會傳回類似下列畫面：

	EndTime             : 4/28/2015 11:21:55 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:20:13 AM -07:00
	Status              : Succeeded
	TrackingOperationId : f74fad9e-f6bc-46ae-82b1-bfad3952aa44
	RequestId           : 6a30d2e0-63ca-43cf-975b-058631e048e7
	StatusCode          : OK

如果您想要看到完成這項工作的視訊，請看這裡：

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## 其他資源
[Azure 快速入門範本](http://azure.microsoft.com/documentation/templates/)和[應用程式架構](virtual-machines-app-frameworks.md)

[Azure 資源管理員提供的 Azure 運算、網路和儲存提供者](virtual-machines-azurerm-versus-azuresm.md)

[Azure 資源管理員概觀](resource-group-overview.md)

[虛擬機器文件](http://azure.microsoft.com/documentation/services/virtual-machines/)

<!---HONumber=Sept15_HO3-->