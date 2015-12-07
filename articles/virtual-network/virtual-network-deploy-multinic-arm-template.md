<properties 
   pageTitle="在資源管理員中使用範本部署多個 NIC VM | Microsoft Azure"
   description="了解如何在資源管理員中使用範本部署多個 NIC VM"
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
   ms.date="11/20/2015"
   ms.author="telmos" />

# 使用範本部署多個 NIC VM

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-ps.md)。

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

因為在這個時間點，您不能讓配備單一 NIC 的 VM 與配備多個 NIC 的 VM 位在同一個資源群組中，您要在某個資源群組中實作後端伺服器，並且將所有其他的元件放置在另一個安全性群組中。下列步驟中是使用名為 *IaaSStory* 的資源群組做為主要資員群組，以及使用 *IaaSStory-BackEnd* 做為後端伺服器。

## 必要條件

您必須為此案例部署含有所有必要資源的主要資源群組，然後才可以部署後端伺服器。若要部署這些資源，請遵循下列步驟。

1. 瀏覽至[範本頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)。
2. 在範本頁面中，按一下 [父資源群組] 右邊的 [部署至 Azure]。
3. 視需要變更參數值，然後依照 Azure Preview 入口網站中的步驟部署資源群組。

> [AZURE.IMPORTANT]請確定您的儲存體帳戶名稱是唯一的。在 Auzre 中不能有重複的儲存體帳戶名稱。

## 了解部署範本

在部署此文件所提供的範本之前，請確定您了解它的功用。下列步驟為所探討的範本提供了很好的概觀。

1. 瀏覽至[範本頁面](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC)。
2. 按一下 **azuredeploy.json** 以開啟範本檔案。
3. 請注意下面列出的 *osType* 參數。此參數用於選取要用於資料庫伺服器的 VM 映像，以及多個作業系統相關設定。

	    "osType": {
	      "type": "string",
	      "defaultValue": "Windows",
	      "allowedValues": [
	        "Windows",
	        "Ubuntu"
	      ],
	      "metadata": {
	        "description": "Type of OS to use for VMs: Windows or Ubuntu."
	      }
	    },

4. 向下捲動到變數清單，然後檢查 *dbVMSetting* 變數的定義，如下所示。它會接收包含在 *dbVMSettings* 變數中的其中一個陣列元素。如果您熟悉軟體開發術語，您可以將 *dbVMSettings* 變數視為雜湊表或字典。

		"dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"

5. 假設您決定要在後端部署執行 SQL 的 Windows VM。那麼，*osType* 的值將會是 *Windows*，且 *dbVMSetting* 變數會包含下面列出的元素，其代表 *dbVMSettings* 變數中的第一個值。

	      "Windows": {
	        "vmSize": "Standard_DS3",
	        "publisher": "MicrosoftSQLServer",
	        "offer": "SQL2014SP1-WS2012R2",
	        "sku": "Standard",
	        "version": "latest",
	        "vmName": "DB",
	        "osdisk": "osdiskdb",
	        "datadisk": "datadiskdb",
	        "nicName": "NICDB",
	        "ipAddress": "192.168.2.",
	        "extensionDeployment": "",
	        "avsetName": "ASDB",
	        "remotePort": 3389,
	        "dbPort": 1433
	      },

6. 請注意 *vmSize* 包含值 *Standard\_DS3*。只有特定的 VM 大小允許使用多個 NIC。您可以瀏覽[多個 NIC 概觀](virtual-networks-multiple-nics.md)，確認哪種 VM 大小可以啟用多個 NIC。
7. 向下捲動至 **resources** 並注意第一個元素。它描述儲存體帳戶。此儲存體帳戶將用來維護每個資料庫 VM 所使用的資料磁碟。在此案例中，每部資料庫 VM 都有儲存在一般儲存體的作業系統磁碟，以及儲存在 SSD (進階) 儲存體的兩個資料磁碟。

	    {
	      "apiVersion": "2015-05-01-preview",
	      "type": "Microsoft.Storage/storageAccounts",
	      "name": "[parameters('prmStorageName')]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "Storage Account - Premium"
	      },
	      "properties": {
	        "accountType": "[parameters('prmStorageType')]"
	      }
	    },

8. 向下捲動到下一個資源，如下所示。這項資源代表每個資料庫 VM 中用於資料庫存取的 NIC。請注意此資源中 **copy** 函式的使用方式。根據 *dbCount* 參數，此範本可讓您部署任意數量的 VM。因此，您需要建立相同數量的 NIC 以用於資料庫存取 (每部 VM 一個)。

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Network/networkInterfaces",
	      "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "NetworkInterfaces - DB DA"
	      },
	      "copy": {
	        "name": "dbniccount",
	        "count": "[parameters('dbCount')]"
	      },
	      "properties": {
	        "ipConfigurations": [
	          {
	            "name": "ipconfig1",
	            "properties": {
	              "privateIPAllocationMethod": "Static",
	              "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
	              "subnet": {
	                "id": "[variables('backEndSubnetRef')]"
	              }
	            }
	          }
	        ]
	      }
	    },

9. 向下捲動到下一個資源，如下所示。這項資源代表每個資料庫 VM 中用於管理的 NIC。同樣地，每個資料庫 VM 都需要一個這種 NIC。請注意，*networkSecurityGroup* 元素只會連結允許存取 RDP/SSH 的 NSG 到此 NIC。

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Network/networkInterfaces",
	      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "NetworkInterfaces - DB RA"
	      },
	      "copy": {
	        "name": "dbniccount",
	        "count": "[parameters('dbCount')]"
	      },
	      "properties": {
	        "ipConfigurations": [
	          {
	            "name": "ipconfig1",
	            "properties": {
	              "networkSecurityGroup": {
	                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
	              },
	              "privateIPAllocationMethod": "Static",
	              "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
	              "subnet": {
	                "id": "[variables('backEndSubnetRef')]"
	              }
	            }
	          }
	        ]
	      }
	    },

10. 向下捲動到下一個資源，如下所示。這項資源代表所有資料庫 VM 要共用的可用性設定組。如此一來，您可以保證在維護期間，設定組中一定會有一部 VM 在執行。

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Compute/availabilitySets",
	      "name": "[variables('dbVMSetting').avsetName]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "AvailabilitySet - DB"
	      }
	    },

11. 向下捲動到下一個資源。這項資源代表資料庫 VM，如下面列出的前幾行所示。請再次注意 **copy** 函式的使用方式，確定是根據 *dbCount* 參數建立多部 VM。另外也請注意 *dependsOn* 集合。在 VM 部署前，它會列出兩個必須建立的 NIC，以及可用性設定組和儲存體帳戶。

		  "apiVersion": "2015-06-15",
		  "type": "Microsoft.Compute/virtualMachines",
		  "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
		  "location": "[variables('location')]",
		  "dependsOn": [
		    "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
		    "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
		    "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
		    "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
		  ],
		  "tags": {
		    "displayName": "VMs - DB"
		  },
		  "copy": {
		    "name": "dbvmcount",
		    "count": "[parameters('dbCount')]"
		  },

12. 在 VM 資源中向下捲動至 **networkProfile** 元素，如下所示。請注意，每部 VM 都有兩個參照的 NIC。當您為一部 VM 建立多個 NIC 時，您必須將其中一個 NIC 的 *primary* 屬性設為 *true*，其餘的設為 *false*。

        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
              "properties": { "primary": true }
            },
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
              "properties": { "primary": false }
            }
          ]
        }
      }

## 使用按一下即部署來部署 ARM 範本

> [AZURE.IMPORTANT]依照下列指示進行之前，請確定您有遵循[必要條件](#Pre-requisites)中的步驟。

公用儲存機制中可用的範例範本會使用一個包含預設值的參數檔案，這些預設值可用來產生上述案例。若要使用按一下即部署來部署此範本，請依照[此連結](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC)，按一下 [後端資源群組 (請參閱文件)] 右邊的 [部署至 Azure]，視需要取代預設參數值，然後遵循入口網站中的指示。

下圖顯示部署之後，新資源群組的內容。

![後端資源群組](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## 使用 PowerShell 部署範本

若要使用 PowerShell 部署您下載的範本，請依照下列步驟執行。

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. 執行 **New-AzureRmResourceGroup** Cmdlet 以使用範本建立資源群組。

		New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
		    -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'	

	預期的輸出：

		ResourceGroupName : IaaSStory-Backend
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		Permissions       : 
		                    Actions  NotActions
		                    =======  ==========
		                    *                  
		                    
		Resources         : 
		                    Name                 Type                                 Location
		                    ===================  ===================================  ========
		                    ASDB                 Microsoft.Compute/availabilitySets   westus  
		                    DB1                  Microsoft.Compute/virtualMachines    westus  
		                    DB2                  Microsoft.Compute/virtualMachines    westus  
		                    NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
		                    NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
		                    NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
		                    NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
		                    wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
		                    
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## 使用 Azure CLI 部署範本

若要使用 Azure CLI 部署範本，請依照下列步驟執行。

1. 如果您從未使用過 Azure CLI，請參閱[安裝和設定 Azure CLI](xplat-cli.md)，並依照指示進行，直到選取您的 Azure 帳戶和訂用帳戶為止。
2. 執行 **azure config mode** 命令，以切換為資源管理員模式，如下所示。

		azure config mode arm

	此為上述命令的預期輸出內容：

		info:    New mode is arm

3. 開啟[參數檔案](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json)，選取其內容，然後在您的電腦中將它儲存至檔案。在此範例中，我們將參數檔案儲存為 *parameters.json*。

4. 執行 **azure group deployment create** Cmdlet，以使用先前下載並修改的範本和參數檔案部署新的 VNet。輸出後顯示的清單可說明所使用的參數。

		azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json

	預期的輸出：

		info:    Executing command group create
		+ Getting resource group IaaSStory-Backend
		+ Creating resource group IaaSStory-Backend
		info:    Created resource group IaaSStory-Backend
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
		data:    Name:                IaaSStory-Backend
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

<!---HONumber=AcomDC_1125_2015-->