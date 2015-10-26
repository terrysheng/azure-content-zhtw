<properties 
   pageTitle="在資源管理員中使用範本控制路由和使用虛擬應用裝置 | Microsoft Azure"
   description="了解如何在 Azure 中使用範本控制路由和使用虛擬應用裝置"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/08/2015"
   ms.author="telmos" />

#使用範本建立使用者定義的路由 (UDR)

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋之內容包括資源管理員部署模型。您也可以[在傳統部署模型中建立 UDR](virtual-networks-udr-how-to.md)。

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

## 範本檔案中的 UDR 資源

您可以檢視和下載[範例範本](https://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR)。

下一節根據上述案例，顯示 azuredeploy-vnet-nsg-udr.json 檔案中前端 UDR 的定義。

	"apiVersion": "2015-06-15",
	"type": "Microsoft.Network/routeTables",
	"name": "[parameters('frontEndRouteTableName')]",
	"location": "[resourceGroup().location]",
	"tags": {
	  "displayName": "UDR - FrontEnd"
	},
	"properties": {
	  "routes": [
	    {
	      "name": "RouteToBackEnd",
	      "properties": {
	        "addressPrefix": "[parameters('backEndSubnetPrefix')]",
	        "nextHopType": "VirtualAppliance",
	        "nextHopIpAddress": "[parameters('vmaIpAddress')]"
	      }
	    }
	  ]


若要建立 UDR 與前端子網路的關聯，您必須變更範本中的子網路定義，並使用 UDR 的參考識別碼。

	"subnets": [
		"name": "[parameters('frontEndSubnetName')]",
		"properties": {
		  "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
		  "networkSecurityGroup": {
		    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
		  },
		  "routeTable": {
		      "id": "[resourceId('Microsoft.Network/routeTables', parameters('frontEndRouteTableName'))]"
		  }
		},
		...]

請注意，在範本中已對後端 NSG 和後端子網路完成相同作業。

您也需要確定 **FW1** VM 已在將用來接收和轉送封包的 NIC 上的 IP 轉送屬性啟用。下一節根據上述案例，顯示 azuredeploy-nsg-udr.json 檔案中 FW1 的 NIC 的定義。

	"apiVersion": "2015-06-15",
	"type": "Microsoft.Network/networkInterfaces",
	"location": "[variables('location')]",
	"tags": {
	  "displayName": "NetworkInterfaces - DMZ"
	},
	"name": "[concat(variables('fwVMSettings').nicName, copyindex(1))]",
	"dependsOn": [
	  "[concat('Microsoft.Network/publicIPAddresses/', variables('fwVMSettings').pipName, copyindex(1))]",
	  "[concat('Microsoft.Resources/deployments/', 'vnetTemplate')]"
	],
	"properties": {
	  "ipConfigurations": [
	    {
	      "name": "ipconfig1",
	      "properties": {
	        "enableIPForwarding": true,
	        "privateIPAllocationMethod": "Static",
	        "privateIPAddress": "[concat('192.168.0.',copyindex(4))]",
	        "publicIPAddress": {
	          "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('fwVMSettings').pipName, copyindex(1)))]"
	        },
	        "subnet": {
	          "id": "[variables('dmzSubnetRef')]"
	        }
	      }
	    }
	  ]
	},
	"copy": {
	  "name": "fwniccount",
	  "count": "[parameters('fwCount')]"
	}

## 使用按一下即部署來部署 ARM 範本

公用儲存機制中可用的範例範本會使用一個包含預設值的參數檔案，這些預設值可用來產生上述案例。若要使用按一下來部署此範本，請依照[此連結](https://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR)，然後按一下**部署至 Azure**，視情況取代預設參數值，並遵循入口網站中的指示。

## 使用 PowerShell 部署 ARM 範本

若要使用 PowerShell 部署您下載的 ARM 範本，請依照下列步驟執行。

1. 如果您從未用過 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)，並遵循其中的所有指示登入 Azure，然後選取您的訂用帳戶。

3. 執行 **New-AzureRMResourceGroup** Cmdlet 以使用範本建立資源群組。

		New-AzureRMResourceGroup -Name TestRG -Location westus `
		    -TemplateFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json'	

	預期的輸出：

		ResourceGroupName : TestRG
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		Permissions       : 
		                    Actions  NotActions
		                    =======  ==========
		                    *                  
		                    
		Resources         : 
		                    Name                Type                                     Location
		                    ==================  =======================================  ========
		                    ASFW                Microsoft.Compute/availabilitySets       westus  
		                    ASSQL               Microsoft.Compute/availabilitySets       westus  
		                    ASWEB               Microsoft.Compute/availabilitySets       westus  
		                    FW1                 Microsoft.Compute/virtualMachines        westus  
		                    SQL1                Microsoft.Compute/virtualMachines        westus  
		                    SQL2                Microsoft.Compute/virtualMachines        westus  
		                    WEB1                Microsoft.Compute/virtualMachines        westus  
		                    WEB2                Microsoft.Compute/virtualMachines        westus  
		                    NICFW1              Microsoft.Network/networkInterfaces      westus  
		                    NICSQL1             Microsoft.Network/networkInterfaces      westus  
		                    NICSQL2             Microsoft.Network/networkInterfaces      westus  
		                    NICWEB1             Microsoft.Network/networkInterfaces      westus  
		                    NICWEB2             Microsoft.Network/networkInterfaces      westus  
		                    NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
		                    NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
		                    PIPFW1              Microsoft.Network/publicIPAddresses      westus  
		                    PIPSQL1             Microsoft.Network/publicIPAddresses      westus  
		                    PIPSQL2             Microsoft.Network/publicIPAddresses      westus  
		                    PIPWEB1             Microsoft.Network/publicIPAddresses      westus  
		                    PIPWEB2             Microsoft.Network/publicIPAddresses      westus  
		                    UDR-BackEnd         Microsoft.Network/routeTables            westus  
		                    UDR-FrontEnd        Microsoft.Network/routeTables            westus  
		                    TestVNet            Microsoft.Network/virtualNetworks        westus  
		                    testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
		                    testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  
		                    
		ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG

## 使用 Azure CLI 部署 ARM 範本

若要使用 Azure CLI 部署 ARM 範本，請依照下列步驟執行。

1. 如果您從未使用過 Azure CLI，請參閱[安裝和設定 Azure CLI](xplat-cli.md)，並依照指示進行，直到選取您的 Azure 帳戶和訂用帳戶步驟。
2. 如下方所示，執行 **azure config mode** 命令以切換至資源管理員模式。

		azure config mode arm

	此為上述命令的預期輸出內容：

		info:    New mode is arm

3. 從您的瀏覽器，瀏覽至 ****https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json**，複製 json 檔案的內容並貼上到您電腦中的新檔案。在此案例中，您會將以下的值複製到名為 **c:\\udr\\azuredeploy.parameters.json** 的檔案。

		{
		  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "fwCount": {
		      "value": 1
		    },
		    "webCount": {
		      "value": 2
		    },
		    "sqlCount": {
		      "value": 2
		    }
		  }
		}

4. 執行 **azure group create** Cmdlet，以使用先前下載並修改的範本和參數檔案，部署新的 VNet。輸出後顯示的清單可說明所使用的參數。

		azure group create -n TestRG -l westus --template-uri 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json' -e 'c:\udr\azuredeploy.parameters.json'

	預期的輸出：

		info:    Executing command group create
		info:    Getting resource group TestRG
		info:    Updating resource group TestRG
		info:    Updated resource group TestRG
		info:    Initializing template configurations and parameters
		info:    Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:    
		info:    group create command OK

5. 執行 **azure group show** 命令來檢視於新資源群組中建立的資源。

		azure group show TestRG

	預期的結果
		
		info:    Executing command group show
		info:    Listing resource groups
		info:    Listing resources for the group
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:    Resources:
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASFW
		data:      Name    : ASFW
		data:      Type    : availabilitySets
		data:      Location: westus
		data:      Tags    : displayName=AvailabilitySet - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASSQL
		data:      Name    : ASSQL
		data:      Type    : availabilitySets
		data:      Location: westus
		data:      Tags    : displayName=AvailabilitySet - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASWEB
		data:      Name    : ASWEB
		data:      Type    : availabilitySets
		data:      Location: westus
		data:      Tags    : displayName=AvailabilitySet - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/FW1
		data:      Name    : FW1
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/SQL1
		data:      Name    : SQL1
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/SQL2
		data:      Name    : SQL2
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WEB1
		data:      Name    : WEB1
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WEB2
		data:      Name    : WEB2
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1
		data:      Name    : NICFW1
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL1
		data:      Name    : NICSQL1
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL2
		data:      Name    : NICSQL2
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1
		data:      Name    : NICWEB1
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2
		data:      Name    : NICWEB2
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd
		data:      Name    : NSG-BackEnd
		data:      Type    : networkSecurityGroups
		data:      Location: westus
		data:      Tags    : displayName=NSG - Front End
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
		data:      Name    : NSG-FrontEnd
		data:      Type    : networkSecurityGroups
		data:      Location: westus
		data:      Tags    : displayName=NSG - Remote Access
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPFW1
		data:      Name    : PIPFW1
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPSQL1
		data:      Name    : PIPSQL1
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPSQL2
		data:      Name    : PIPSQL2
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPWEB1
		data:      Name    : PIPWEB1
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPWEB2
		data:      Name    : PIPWEB2
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd
		data:      Name    : UDR-BackEnd
		data:      Type    : routeTables
		data:      Location: westus
		data:      Tags    : displayName=Route Table - Back End
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd
		data:      Name    : UDR-FrontEnd
		data:      Type    : routeTables
		data:      Location: westus
		data:      Tags    : displayName=UDR - FrontEnd
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		data:      Name    : TestVNet
		data:      Type    : virtualNetworks
		data:      Location: westus
		data:      Tags    : displayName=VNet
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/testvnetstorageprm
		data:      Name    : testvnetstorageprm
		data:      Type    : storageAccounts
		data:      Location: westus
		data:      Tags    : displayName=Storage Account - Premium
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/testvnetstoragestd
		data:      Name    : testvnetstoragestd
		data:      Type    : storageAccounts
		data:      Location: westus
		data:      Tags    : displayName=Storage Account - Simple
		data:    
		data:    Permissions:
		data:      Actions: *
		data:      NotActions: 
		data:    
		info:    group show command OK

>[AZURE.TIP]如果看不到所有資源，請執行 **azure group deployment show** 命令，以確保部署的佈建狀態為*成功*。

<!---HONumber=Oct15_HO3-->