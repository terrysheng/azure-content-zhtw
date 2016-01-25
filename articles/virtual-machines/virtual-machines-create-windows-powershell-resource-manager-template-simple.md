<properties
	pageTitle="使用範本建立 VM | Microsoft Azure"
	description="利用 Resource Manager 範本和 Azure PowerShell 建立新的 Windows 虛擬機器。"
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="davidmu"/>

# 利用 Resource Manager 範本和 PowerShell 建立 Windows 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。


您可以使用 Resource Manager 範本搭配 Azure PowerShell，輕鬆建立新的 Windows 型 Azure 虛擬機器 (VM)。這個範本建立的單一虛擬機器會採用 Windows ，而且是在新資源群組的單一子網路的新虛擬網路上執行。

![](./media/virtual-machines-create-windows-powershell-resource-manager-template-simple/windowsvm.png)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## 建立 Windows VM

請依照下列步驟，搭配 Azure PowerShell 並使用 Github 範本儲存機制中的 Resource Manager 範本建立 Windows VM。

在下列命令，填入您想要用於部署的名稱、新資源群組的名稱和應建立資源的位置。


		$deployName="<deployment name>"
		$RGName="<resource group name>"
		$locName="<Azure location, such as West US>"
		$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
		New-AzureRmResourceGroup –Name $RGName –Location $locName
		New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI


執行 **New-AzureRmResourceGroupDeployment** 命令時，系統會提示您提供 JSON 檔案 "parameters" 區段中的參數值。指定所有參數值後，這個命令會建立資源群組和虛擬機器。

您應該會看到如下的結果：


		cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
		Supply values for the following parameters:
		(Type !? for Help.)
		newStorageAccountName: newsaacct
		adminUsername: WinAdmin1
		adminPassword: *********
		dnsNameForPublicIP: contoso
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
											Name                   Type                       Value
											===============        =========================  ==========
											newStorageAccountName  String                     newsaacct
											adminUsername          String                     WinAdmin1
											adminPassword          SecureString
											dnsNameForPublicIP     String                     contoso
											windowsOSVersion       String                     2012-R2-Datacenter
		Outputs           :


## 後續步驟

請參閱[使用 Azure 資源管理員和 PowerShell 管理虛擬機器](virtual-machines-deploy-rmtemplates-powershell.md)，了解如何管理您剛才建立的虛擬機器。

<!---HONumber=AcomDC_0114_2016-->