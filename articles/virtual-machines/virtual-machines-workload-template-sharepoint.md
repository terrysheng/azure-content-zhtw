<properties
	pageTitle="使用 ARM 範本部署 SharePoint 伺服器陣列 | Microsoft Azure"
	description="使用資源管理員範本和 Azure Preview 入口網站、Azure PowerShell 或 Azure CLI 輕鬆部署一個包含 3 部或 9 部伺服器的 SharePoint 伺服器陣列。"
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# 使用 Azure 資源管理員範本部署 SharePoint 伺服器陣列

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。您無法以傳統部署模型建立此資源。

按照本文中的指示，使用資源管理員範本部署一個新的、包含三部伺服器或九部伺服器的 SharePoint Server 2013 伺服器陣列。

## 部署一個包含三部伺服器的 SharePoint 伺服器陣列

如果是基本的 SharePoint Server 2013 伺服器陣列，資源管理員範本會在新的虛擬網路中三個不同的子網路上，建立三部虛擬機器。

![](./media/virtual-machines-workload-template-sharepoint/three-server-sharepoint-farm.png)

您可以使用 Azure Preview 入口網站、Azure PowerShell 或 Azure CLI 來執行範本。

> [AZURE.NOTE]您也可以在 Azure Preview 入口網站的 Azure Marketplace 中使用 [SharePoint 2013 非 HA 伺服器陣列](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)項目建立這個組態。

### Azure Preview 入口網站

若要使用資源管理員範本和 Azure Preview 入口網站來部署此工作負載，請按一下[這裡](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-three-vm%2Fazuredeploy.json)。

![](./media/virtual-machines-workload-template-sharepoint/azure-portal-template.png)

1.	按一下 [參數]。在 [參數] 窗格中輸入新值、從允許的值選取，或接受預設值，然後按一下 [確定]。
2.	如有需要，按一下 [訂用帳戶]，然後選取正確的 Azure 訂用帳戶。
3.	按一下 [資源群組]，然後選取現有的資源群組。或者，按一下 [或建立新的]，為此工作負載建立新的資源群組。
4.	如有需要，按一下 [資源群組位置]，然後選取正確的 Azure 位置。
6.	按一下 [法律條款]，檢閱使用範本的條款和合約，然後按一下 [購買]。
7.	按一下 [建立]。

視不同範本而定，可能需要一些時間讓 Azure 建置工作負載。完成時，您在現有或新的資源群組中就會有一個新的、包含三部伺服器的 SharePoint 伺服器陣列。

### Azure PowerShell

> [AZURE.NOTE]這份文件包含 Azure PowerShell Preview 1.0 的命令。若要在 Azure PowerShell 0.9.8 和先前版本中執行這些命令，請將 **New-AzureRMResourceGroup** 取代為 **New-AzureResourceGroup**、將 **New-AzureRMResourceGroupDeployment** 取代為 **New-AzureResourceGroupDeployment**，以及將 **Switch-AzureMode AzureResourceManager** 命令加在 **New-AzureResourceGroup** 命令前面。如需詳細資訊，請參閱 [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/)。

在下列命令中填寫 Azure 部署名稱、新的資源群組名稱，以及 Azure 資料中心位置。移除括弧內的所有項目，包括 < and > 字元。

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

範例如下。

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

接下來，在 Azure PowerShell 提示字元中執行命令區塊。

執行 **New-AzureRmResourceGroupDeployment** 命令時，系統會提示您提供一系列參數的值。在您指定所有參數值之後，**New-AzureRmResourceGroupDeployment** 便會建立和設定虛擬機器。

範本執行完成時，您在新的資源群組中就會有一個新的、包含三部伺服器的 SharePoint 伺服器陣列。

### Azure CLI

在開始之前，請確定您已安裝正確版本的 Azure CLI、已登入，而且您已切換至新的資源管理員模式。如需詳細資訊，請按一下[這裡](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready)。

首先，您要建立新的資源群組。使用下列命令並指定群組名稱和您要部署的 Azure 資料中心位置。

	azure group create <group name> <location>

接下來，使用下列命令並指定新資源群組的名稱和 Azure 部署的名稱。

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json <group name> <deployment name>

範例如下。

	azure group create sp3serverfarm eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json sp3serverfarm spdevtest

執行 **azure group deployment create** 命令時，系統會提示您提供一系列參數的值。當您指定了所有參數值之後，Azure 會建立和設定虛擬機器。

現在，您在新的資源群組中有一個包含三部伺服器的新 SharePoint 伺服器陣列。

## 部署一個包含九部伺服器的 SharePoint 伺服器陣列

如果是高可用性的 SharePoint Server 2013 伺服器陣列，資源管理員範本會在新的虛擬網路中四個不同的子網路上，建立九部虛擬機器。

![](./media/virtual-machines-workload-template-sharepoint/nine-server-sharepoint-farm.png)

> [AZURE.NOTE]您也可以在 Azure Preview 入口網站的 Azure Marketplace 中使用 [SharePoint 2013 HA 伺服器陣列](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)項目建立這個組態。

### Azure Preview 入口網站

若要使用資源管理員範本和 Azure Preview 入口網站來部署此工作負載，請按一下[這裡](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-server-farm-ha%2Fazuredeploy.json)。

![](./media/virtual-machines-workload-template-sharepoint/azure-portal-template.png)

1.	按一下 [參數]。在 [參數] 窗格中輸入新值、從允許的值選取，或接受預設值，然後按一下 [確定]。
2.	如有需要，按一下 [訂用帳戶]，然後選取正確的 Azure 訂用帳戶。
3.	按一下 [資源群組]，然後選取現有的資源群組。或者，按一下 [或建立新的]，為此工作負載建立新的資源群組。
4.	如有需要，按一下 [資源群組位置]，然後選取正確的 Azure 位置。
5.	按一下 [法律條款]，檢閱使用範本的條款和合約，然後按一下 [購買]。
6.	按一下 [建立]。

視不同範本而定，可能需要一些時間讓 Azure 建置工作負載。完成時，您在現有或新的資源群組中就會有一個新的、包含九部伺服器的 SharePoint 伺服器陣列。

### Azure PowerShell

> [AZURE.NOTE]這份文件包含 Azure PowerShell Preview 1.0 的命令。若要在 Azure PowerShell 0.9.8 和先前版本中執行這些命令，請將 **New-AzureRMResourceGroup** 取代為 **New-AzureResourceGroup**、將 **New-AzureRMResourceGroupDeployment** 取代為 **New-AzureResourceGroupDeployment**，以及將 **Switch-AzureMode AzureResourceManager** 命令加在 **New-AzureResourceGroup** 命令前面。如需詳細資訊，請參閱 [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/)。

在下列命令中填寫 Azure 部署名稱、新的資源群組名稱，以及 Azure 資料中心位置。移除括弧內的所有項目，包括 < and > 字元。

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

範例如下。

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

接下來，在 Azure PowerShell 命令提示字元中執行命令區塊。

執行 **New-AzureRmResourceGroupDeployment** 命令時，系統會提示您提供一系列參數的值。在您指定所有參數值之後，**New-AzureRmResourceGroupDeployment** 便會建立和設定虛擬機器。

範本執行完成時，您在新的資源群組中就會有一個新的、包含九部伺服器的 SharePoint 伺服器陣列。

### Azure CLI

在開始之前，請確定您已安裝正確版本的 Azure CLI、已登入，而且您已切換至新的資源管理員模式。如需詳細資訊，請按一下[這裡](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready)。

首先，建立新的資源群組。使用下列命令並指定群組名稱和您要部署的 Azure 資料中心位置。

	azure group create <group name> <location>

接下來，使用下列命令並指定新資源群組的名稱和 Azure 部署的名稱。

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json <group name> <deployment name>

範例如下。

	azure group create sphaserverfarm eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json sphaserverfarm spdevtest

執行 **azure group deployment create** 命令時，系統會提示您提供一系列參數的值。當您指定了所有參數值之後，Azure 會建立和設定虛擬機器。

範本執行完成時，您在新的資源群組中就會有一個新的、包含九部伺服器的 SharePoint Server 2013 伺服器陣列。


## 其他資源

[在 Azure 基礎結構服務中架設的 SharePoint 伺服器陣列](virtual-machines-sharepoint-infrastructure-services.md)

[使用 Azure 資源管理員範本和 Azure PowerShell 部署和管理虛擬機器](virtual-machines-deploy-rmtemplates-powershell.md)

[Azure 資源管理員提供的 Azure 運算、網路和儲存提供者](virtual-machines-azurerm-versus-azuresm.md)

[Azure 資源管理員概觀](../resource-group-overview.md)

[使用 Azure 資源管理員範本和 Azure CLI 部署和管理虛擬機器](virtual-machines-deploy-rmtemplates-azure-cli.md)

[虛擬機器文件](http://azure.microsoft.com/documentation/services/virtual-machines/)

[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=AcomDC_1125_2015-->