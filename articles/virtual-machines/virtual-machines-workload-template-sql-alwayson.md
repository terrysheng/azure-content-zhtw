<properties
	pageTitle="使用 Azure 資源管理員範本部署 SQL Server AlwaysOn | Microsoft Azure"
	description="使用資源管理員範本和 Azure Preview 入口網站、Azure PowerShell 或 Azure CLI 輕鬆部署支援 SQL Server AlwaysOn 的五部伺服器。"
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="06/29/2015"
	ms.author="davidmu"/>

# 使用 Azure 資源管理員範本部署 SQL Server AlwaysOn

依照本文中的指示，使用 Azure 資源管理員範本部署 SQL Server AlwaysOn。此範本會在新的虛擬網路中兩個不同的子網路上建立五部虛擬機器。

![](./media/virtual-machines-workload-template-sql-alwayson/five-server-sqlao.png)

您可以使用 Azure Preview 入口網站、Azure PowerShell 或 Azure CLI 來執行範本。

## Azure Preview 入口網站

若要使用 Azure 資源管理員範本和 Azure Preview 入口網站部署此工作負載，請按一下[這裡](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsql-server-2014-alwayson-dsc%2Fazuredeploy.json)。

![](./media/virtual-machines-workload-template-sql-alwayson/azure-portal-template.png)

1.	在 [範本] 窗格中，按一下 [儲存]。
2.	按一下 [參數]。在 [參數] 窗格中輸入新值、從允許的值選取，或接受預設值，然後按一下 [確定]。
3.	如有需要，按一下 [訂用帳戶]，然後選取正確的 Azure 訂用帳戶。
4.	按一下 [資源群組]，然後選取現有的資源群組。或者，按一下 [或建立新的] 為此工作負載建立一個新的資源群組。
5.	如有需要，按一下 [資源群組位置]，然後選取正確的 Azure 位置。
6.	如有需要，按一下 [**法律條款**] 檢閱使用範本的條款和合約。
7.	按一下 [建立]。

視不同範本而定，可能需要一些時間讓 Azure 建置工作負載。範本執行完成時，您在現有或新的資源群組中就會有一個新的、包含五部伺服器的 SQL Server AlwaysOn 組態。

## Azure PowerShell

在開始之前，請確定您已安裝正確版本的 Azure PowerShell、已登入，而且您已切換至新的資源管理員模式。如需詳細資訊，請按一下[這裡](virtual-machines-deploy-rmtemplates-powershell.md#setting-up-powershell-for-resource-manager-templates)。

在下列命令中填寫 Azure 部署名稱、新的資源群組名稱，以及 Azure 資料中心位置。移除括弧內的所有項目，包括 < and > 字元。

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

範例如下。

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

接下來，在 Azure PowerShell 提示字元中執行命令區塊。

當執行 **New-AzureResourceGroupDeployment** 命令時，會提示您提供一系列參數的值。當您指定了所有參數值之後，**New-AzureResourceGroupDeployment** 會建立和設定虛擬機器。

範本執行完成時，您在新的資源群組中就會有一個新的、包含五部伺服器的 SQL Server AlwaysOn 組態。

## Azure CLI

在開始之前，請確定您已安裝正確版本的 Azure CLI、已登入，而且您已切換至新的資源管理員模式。如需詳細資訊，請按一下[這裡](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready)。

首先，您要建立新的資源群組。使用下列命令並指定群組名稱和您要部署的 Azure 資料中心位置。

	azure group create <group name> <location>

接下來，使用下列命令並指定新資源群組的名稱和 Azure 部署的名稱。

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json <group name> <deployment name>

範例如下。

	azure group create sqlao eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sql-server-2014-alwayson-dsc/azuredeploy.json sqlao sqldevtest

當執行 **azure group deployment create** 命令時，會提示您提供一系列參數的值。當您指定了所有參數值之後，Azure 會建立和設定虛擬機器。

範本執行完成時，您在新的資源群組中就會有一個新的、包含五部伺服器的 SQL Server AlwaysOn 組態。


## 其他資源

[使用 Azure 資源管理員範本和 Azure PowerShell 部署和管理虛擬機器](virtual-machines-deploy-rmtemplates-powershell.md)

[Azure 資源管理員提供的 Azure 運算、網路和儲存提供者](virtual-machines-azurerm-versus-azuresm.md)

[Azure 資源管理員概觀](../resource-group-overview.md)

[使用 Azure 資源管理員範本和 Azure CLI 部署和管理虛擬機器](virtual-machines-deploy-rmtemplates-azure-cli.md)

[虛擬機器文件](http://azure.microsoft.com/documentation/services/virtual-machines/)

[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=August15_HO8-->