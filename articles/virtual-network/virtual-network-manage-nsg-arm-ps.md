<properties 
   pageTitle="在資源管理員中使用 PowerShell 管理 NSG | Microsoft Azure"
   description="了解如何在資源管理員中使用 PowerShell 管理現有的 NSG"
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
   ms.date="03/14/2016"
   ms.author="telmos" />

# 使用 PowerShell 管理 NSG

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## 擷取資訊

您可以檢視您現有的 NSG、擷取現有 NSG 的規則，並找出與 NSG 相關聯的資源。

### 檢視現有的 NSG
若要檢視訂用帳戶中所有現有的 NSG，請執行 `Get-AzureRmNetworkSecurityGroup` Cmdlet，如下所示。

	Get-AzureRmNetworkSecurityGroup

預期的結果：

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	                     	
	Name                 : WEB1
	ResourceGroupName    : RG101
	Location             : eastus2
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
	                       icrosoft.Network/networkSecurityGroups/WEB1
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]


若要檢視特定資源群組中的 NSG 清單，請執行 `Get-AzureRmNetworkSecurityGroup` Cmdlet，如下所示。

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

預期的輸出：

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
		 
### 列出 NSG 的所有規則

若要檢視名為 **NSG-FrontEnd** 的 NSG 的規則，請執行 `Get-AzureRmNetworkSecurityGroup` Cmdlet，如下所示。

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

預期的輸出：
	
	Name                     : rdp-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow RDP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 3389
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 100
	Direction                : Inbound
	
	Name                     : web-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow HTTP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 80
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 101
	Direction                : Inbound

>[AZURE.NOTE] 您也可以使用 `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` 從 **NSG-FrontEnd** NSG 列出預設的規則。

### 檢視 NSG 關聯

若要檢視與 **NSG-FrontEnd** NSG 相關聯的資源，請執行 `Get-AzureRmNetworkSecurityGroup` Cmdlet，如下所示。

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

尋找 **NetworkInterfaces** 和 **Subnets** 屬性，如下所示︰

	NetworkInterfaces    : []
	Subnets              : [
	                         {
	                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
	                           "IpConfigurations": []
	                         }
	                       ]

在上述範例中，NSG 沒有與任何網路介面 (NIC) 相關聯，而是與名稱為 **FrontEnd** 的子網路相關聯。

## 管理規則

您可以將規則新增至現有的 NSG、編輯現有的規則，以及移除規則。

### 新增規則

若要新增規則，允許來自任何電腦的連接埠 **443** 的**輸入**流量流向 **NSG-FrontEnd** NSG，請依照下列步驟進行。

1. 執行 `Get-AzureRmNetworkSecurityGroup` Cmdlet 來擷取現有的 NSG，並將其儲存在變數中，如下所示。

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. 執行 `Add-AzureRmNetworkSecurityRuleConfig` Cmdlet，如下所示。

		Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange * `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. 若要儲存對 NSG 所進行的變更，請執行 `Set-AzureRmNetworkSecurityGroup` Cmdlet，如下所示。

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	僅顯示安全性規則的預期輸出：

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "*",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### 變更規則

若要變更以上所建立的規則，僅允許來自**網際網路**的輸入流量，請依照下列步驟進行。

1. 執行 `Get-AzureRmNetworkSecurityGroup` Cmdlet 來擷取現有的 NSG，並將其儲存在變數中，如下所示。

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. 執行 `Set-AzureRmNetworkSecurityRuleConfig` Cmdlet，如下所示。

		Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange Internet `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. 若要儲存對 NSG 所進行的變更，請執行 `Set-AzureRmNetworkSecurityGroup` Cmdlet，如下所示。

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	僅顯示安全性規則的預期輸出：

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### 刪除規則

1. 執行 `Get-AzureRmNetworkSecurityGroup` Cmdlet 來擷取現有的 NSG，並將其儲存在變數中，如下所示。

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. 執行 `Remove-AzureRmNetworkSecurityRuleConfig` Cmdlet，如下所示。

		Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule

3. 若要儲存對 NSG 所進行的變更，請執行 `Set-AzureRmNetworkSecurityGroup` Cmdlet，如下所示。

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	僅顯示安全性規則的預期輸出，請注意，不再列出 **https-rule**︰

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         }
		                       ]

## 管理關聯

您可以建立 NSG 與子網路和 NIC 的關聯。您也可以中斷 NSG 與其相關聯的任何資源的關聯。

### 建立 NSG 與 NIC 的關聯

若要建立 **NSG-FrontEnd** NSG 與 **TestNICWeb1** NIC 的關聯，請依照下列步驟進行。

1. 執行 `Get-AzureRmNetworkSecurityGroup` Cmdlet 來擷取現有的 NSG，並將其儲存在變數中，如下所示。

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. 執行 `Get-AzureRmNetworkInterface` Cmdlet 來擷取現有的 NIC，並將其儲存在變數中，如下所示。

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. 將 **NIC** 變數的 **NetworkSecurityGroup** 屬性設定為 **NSG** 變數的值，如下所示。

		$nic.NetworkSecurityGroup = $nsg

4. 若要儲存對 NIC 所進行的變更，請執行 `Set-AzureRmNetworkInterface` Cmdlet，如下所示。

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	僅顯示 **NetworkSecurityGroup** 屬性的預期輸出︰

		NetworkSecurityGroup : {
		                         "SecurityRules": [],
		                         "DefaultSecurityRules": [],
		                         "NetworkInterfaces": [],
		                         "Subnets": [],
		                         "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                       }

### 中斷 NSG 與 NIC 的關聯

若要中斷 **NSG-FrontEnd** NSG 與 **TestNICWeb1** NIC 的關聯，請依照下列步驟進行。

1. 執行 `Get-AzureRmNetworkSecurityGroup` Cmdlet 來擷取現有的 NSG，並將其儲存在變數中，如下所示。

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. 執行 `Get-AzureRmNetworkInterface` Cmdlet 來擷取現有的 NIC，並將其儲存在變數中，如下所示。

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. 將 **NIC** 變數的 **NetworkSecurityGroup** 屬性設定為 **$null**，如下所示。

		$nic.NetworkSecurityGroup = $null

4. 若要儲存對 NIC 所進行的變更，請執行 `Set-AzureRmNetworkInterface` Cmdlet，如下所示。

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	僅顯示 **NetworkSecurityGroup** 屬性的預期輸出︰

		NetworkSecurityGroup : null

### 中斷 NSG 與子網路的關聯

若要中斷 **NSG-FrontEnd** NSG 與 **FrontEnd** 子網路的關聯，請依照下列步驟進行。

1. 執行 `Get-AzureRmVirtualNetwork` Cmdlet 來擷取現有的 VNet，並將其儲存在變數中，如下所示。

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. 執行 `Get-AzureRmVirtualNetworkSubnetConfig` Cmdlet 來擷取 **FrontEnd** 子網路，並將其儲存在變數中，如下所示。

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

3. 將 **subnet** 變數的 **NetworkSecurityGroup** 屬性設定為 **$null**，如下所示。

		$subnet.NetworkSecurityGroup = $null

4. 若要儲存對子網路所進行的變更，請執行 `Set-AzureRmVirtualNetwork` Cmdlet，如下所示。

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	僅顯示 **FrontEnd** 子網路屬性的預期輸出。請注意，沒有 **NetworkSecurityGroup** 的屬性：

			...
			Subnets           : [
			                      {
			                        "Name": "FrontEnd",
			                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
			                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
			                        "AddressPrefix": "192.168.1.0/24",
			                        "IpConfigurations": [
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
			                          },
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
			                          }
			                        ],
			                        "ProvisioningState": "Succeeded"
			                      },
									...
			                    ]

### 建立 NSG 至子網路的關聯

若要再次建立 **NSG-FrontEnd** NSG 與 **FronEnd** 子網路的關聯，請依照下列步驟進行。

1. 執行 `Get-AzureRmVirtualNetwork` Cmdlet 來擷取現有的 VNet，並將其儲存在變數中，如下所示。

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. 執行 `Get-AzureRmVirtualNetworkSubnetConfig` Cmdlet 來擷取 **FrontEnd** 子網路，並將其儲存在變數中，如下所示。

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

1. 執行 `Get-AzureRmNetworkSecurityGroup` Cmdlet 來擷取現有的 NSG，並將其儲存在變數中，如下所示。

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

3. 將 **subnet** 變數的 **NetworkSecurityGroup** 屬性設定為 **$null**，如下所示。

		$subnet.NetworkSecurityGroup = $nsg

4. 若要儲存對子網路所進行的變更，請執行 `Set-AzureRmVirtualNetwork` Cmdlet，如下所示。

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	僅顯示 **FrontEnd** 子網路的 **NetworkSecurityGroup** 屬性的預期輸出︰

		...
		"NetworkSecurityGroup": {
		                          "SecurityRules": [],
		                          "DefaultSecurityRules": [],
		                          "NetworkInterfaces": [],
		                          "Subnets": [],
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        }
		...

## 刪除 NSG

您只能刪除與任何資源沒有關聯的 NSG。若要刪除 NSG，請依照下列步驟進行。

1. 若要檢查與 NSG 相關聯的資源，請執行 `azure network nsg show`，如[檢視 NSG 關聯](#View-NSGs-associations)中所示。
2. 如果 NSG 與任何 NIC 相關聯，為每個 NIC 執行 `azure network nic set`，如[中斷 NSG 與 NIC 的關聯](#Dissociate-an-NSG-from-a-NIC)中所示。 
3. 如果 NSG 與任何子網路相關聯，為每個子網路執行 `azure network vnet subnet set`，如[中斷 NSG 與子網路的關聯](#Dissociate-an-NSG-from-a-subnet)中所示。
4. 若要刪除 NSG，請執行 `Remove-AzureRmNetworkSecurityGroup` Cmdlet，如下所示。

		Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

	>[AZURE.NOTE] **-Force** 參數可確保您不需要確認刪除。

## 後續步驟

- [啟用 NSG 的記錄](virtual-network-nsg-manage-log.md)。

<!---HONumber=AcomDC_0323_2016-->