<properties 
   pageTitle="在資源管理員中使用範本部署使用靜態公用 IP 的 VM | Microsoft Azure"
   description="了解如何在資源管理員中使用範本部署使用靜態公用 IP 的 VM"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/08/2015"
   ms.author="telmos" />

# 使用範本部署使用靜態公用 IP 的 VM

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## 範本檔案中的公用 IP 資源

您可以檢視和下載[範例範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json)。

下節依據上述案例顯示公用 IP 資源的定義。

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('webVMSetting').pipName]",
        "location": "[variables('location')]",
        "properties": {
          "publicIPAllocationMethod": "Static"
        },
        "tags": {
          "displayName": "PublicIPAddress - Web"
        }
      },

請注意，**publicIPAllocationMethod** 屬性設定為 *Static*。這個屬性可以是 *Dynamic* (預設值) 或 *Static*。將它設定為 static 可確保這個公用 IP 的 IP 位址永遠不會變更。

下一節顯示上述的公用 IP 與 NIC 的關聯。

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('webVMSetting').nicName]",
        "location": "[variables('location')]",
        "tags": {
          "displayName": "NetworkInterface - Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
          "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Static",
                "privateIPAddress": "[variables('webVMSetting').ipAddress]",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
                },
                "subnet": {
                  "id": "[variables('frontEndSubnetRef')]"
                }
              }
            }
          ]
        }
      },

請注意，**publicIPAddress** 屬性指向名為 **variables('webVMSetting').pipName** 的資源的識別碼。這是上述公用 IP 資源的名稱。

最後，上述 NIC 會列在建立的 VM 的 **networkProfile** 屬性中。

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## 使用按一下即部署來部署範本

公用儲存機制中可用的範例範本會使用一個包含預設值的參數檔案，這些預設值可用來產生上述案例。若要使用「按一下即部署」來部署此範本，請依循[此連結](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP)，按一下 [部署至 Azure]，視情況取代預設參數值，再依循入口網站中的指示。

## 使用 PowerShell 部署範本

若要使用 PowerShell 部署您下載的範本，請依照下列步驟執行。

1. 如果您從未使用過 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)，並遵循步驟 1 到 3 中的指示。

2. 如有必要，在 PowerShell 主控台中執行 **New-AzureRmResourceGroup** Cmdlet 以建立新的資源群組。如果您已經建立資源群組，請移至步驟 3。

		New-AzureRmResourceGroup -Name StaticPublicIP -Location westus

	預期的輸出：

		ResourceGroupName : StaticPublicIP
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. 在 PowerShell 主控台中，執行 **New-AzureRmResourceGroupDeployment** Cmdlet 來部署範本。

		New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
		    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
		    -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json

	預期的輸出：

		DeploymentName    : DeployVM
		ResourceGroupName : PIPTEST
		ProvisioningState : Succeeded
		Timestamp         : 1/8/2016 7:04:44 PM
		Mode              : Incremental
		TemplateLink      : 
		                    Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
		                    ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
		                    ContentVersion : 1.0.0.0
		                    
		Parameters        : 
		                    Name                      Type                       Value     
		                    ========================  =========================  ==========
		                    vnetName                  String                     WTestVNet 
		                    vnetPrefix                String                     192.168.0.0/16
		                    frontEndSubnetName        String                     FrontEnd  
		                    frontEndSubnetPrefix      String                     192.168.1.0/24
		                    storageAccountNamePrefix  String                     iaasestd  
		                    stdStorageType            String                     Standard_LRS
		                    osType                    String                     Windows   
		                    adminUsername             String                     adminUser 
		                    adminPassword             SecureString                         
		                    
		Outputs           : 

## 使用 Azure CLI 部署範本

若要使用 Azure CLI 部署範本，請依照下列步驟執行。

1. 如果您從未使用過 Azure CLI，請參閱[安裝和設定 Azure CLI](xplat-cli.md)，並依照指示進行，直到選取您的 Azure 帳戶和訂用帳戶為止。
2. 執行 **azure config mode** 命令，以切換為資源管理員模式，如下所示。

		azure config mode arm

	此為上述命令的預期輸出內容：

		info:    New mode is arm

3. 開啟[參數檔案](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json)，選取其內容，然後將該內容儲存至您電腦中的一個檔案。在此範例中，我們將參數檔案儲存為 *parameters.json*。

4. 執行 **azure group deployment create** Cmdlet，以使用先前所下載及修改的範本和參數檔案部署新的 VNet。輸出後顯示的清單可說明所使用的參數。

		azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e parameters.json

	預期的輸出：

		info:    Executing command group create
		+ Getting resource group PIPTEST2
		+ Creating resource group PIPTEST2
		info:    Created resource group PIPTEST2
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/PIPTEST2
		data:    Name:                PIPTEST2
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

<!---HONumber=AcomDC_0114_2016-->