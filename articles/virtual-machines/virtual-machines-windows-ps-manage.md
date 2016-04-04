<properties
	pageTitle="管理 Azure 資源管理員 VM | Microsoft Azure"
	description="使用 Azure 資源管理員、範本和 PowerShell 管理虛擬機器。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="davidmu"/>

# 使用 Azure 資源管理員和 PowerShell 部署以及管理虛擬機器

> [AZURE.SELECTOR]
- [PowerShell](virtual-machines-windows-ps-manage.md)
- [CLI](virtual-machines-linux-cli-deploy-templates.md)

<br>


管理 Microsoft Azure 中的資源時，使用 Azure PowerShell 與資源管理員範本可提供您許多功能和彈性。您可以使用本文中的工作來管理虛擬機器資源。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

這些工作只會使用 PowerShell：

- [顯示虛擬機器的相關資訊](#displayvm)
- [啟動虛擬機器](#start)
- [停止虛擬機器](#stop)
- [重新啟動虛擬機器](#restart)
- [刪除虛擬機器](#delete)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## <a id="displayvm"></a>顯示虛擬機器的相關資訊

在下列命令中，以包含虛擬機器的資源群組名稱取代「資源群組名稱」，再以電腦名稱取代「VM 名稱」，然後執行此命令：

	Get-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

它會傳回類似下列畫面：


	ResourceGroupName        : rg1
	Id                       : /subscriptions/{subscription-id}/resourceGroups/
															rg1/providers/Microsoft.Compute/virtualMachines/vm1
	Name                     : vm1
	Type                     : Microsoft.Azure.Management.Compute.Models.VirtualMachineGetResponse
	Location                 : westus
	Tags                     : {}
	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          :  {
																"VirtualMachineSize": "Standard_D1"
															}
	InstanceView             : null
	Location                 : westus
	Name                     : vm1
	NetworkProfile           :  {
																"NetworkInterfaces": [
																	{
																		"Primary": null,
																		"ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/
																		rg1/providers/Microsoft.Network/networkInterfaces/nc1"
																	}
																]
															}
	OSProfile                :  {
																"AdminPassword": null,
																"AdminUsername": "WinAdmin1",
																"ComputerName": "vm1",
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
	StorageProfile           : 	{
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
																		"Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
																	}
																}
															}
	DataDiskNames            :  {}
	NetworkInterfaceIDs      : { /subscriptions/{subscription-id}/resourceGroups/
																rg1/providers/Microsoft.Network/networkInterfaces/nc1}

如果您想要看到完成這項工作的視訊，請看這裡：

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>啟動虛擬機器

在下列命令中，以包含虛擬機器的資源群組名稱取代「資源群組名稱」，再以電腦名稱取代「VM 名稱」，然後執行此命令：

	Start-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

它會傳回類似下列畫面：

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 06935ddf-6e89-48d2-b46a-229493e3e9d1
	Output              :
	Error               :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	EndTime             : 4/28/2015 11:11:41 AM -07:00
	TrackingOperationId : c1aa0a70-4f4f-4d6c-a8ac-7ea35c004ce0

如果您想要看到完成這項工作的視訊，請看這裡：

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>停止虛擬機器

在下列命令中，以包含虛擬機器的資源群組名稱取代「資源群組名稱」，再以電腦名稱取代「VM 名稱」，然後執行此命令：

	Stop-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

系統會要您確認：

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

它會傳回類似下列畫面：

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	Output              :
	Error               :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	EndTime             : 4/28/2015 11:11:41 AM -07:00
	TrackingOperationId : e1705973-d266-467e-8655-920016145347

如果您想要看到完成這項工作的視訊，請看這裡：

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>重新啟動虛擬機器

在下列命令中，以包含虛擬機器的資源群組名稱取代「資源群組名稱」，再以電腦名稱取代「VM 名稱」，然後執行此命令：

	Restart-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

它會傳回類似下列畫面：

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 4b05891c-fdff-4c9a-89ca-e4f1d7691aed
	Output              :
	Error               :
	StartTime           : 1/5/2016 12:06:53 PM -08:00
	EndTime             : 1/5/2016 12:06:54 PM -08:00
	TrackingOperationId : 5aeeab89-45ab-41b9-84ef-9e9a7e732207


如果您想要看到完成這項工作的視訊，請看這裡：

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>刪除虛擬機器

在下列命令中，以包含虛擬機器的資源群組名稱取代「資源群組名稱」，再以電腦名稱取代「VM 名稱」，然後執行此命令：

	Remove-AzureRmVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE] 您可以使用 **– Force** 參數，略過確認提示。

系統會要您確認是否沒有使用 -Force 參數：

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

它會傳回類似下列畫面：

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 2d723b40-ce1f-4b11-a603-dc659a13b6f0
	Output              :
	Error               :
	StartTime           : 1/5/2016 12:10:28 PM -08:00
	EndTime             : 1/5/2016 12:12:12 PM -08:00
	TrackingOperationId : d138ab29-83bf-4948-9d13-dab87db1a639

如果您想要看到完成這項工作的視訊，請看這裡：

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

<!---HONumber=AcomDC_0323_2016-->