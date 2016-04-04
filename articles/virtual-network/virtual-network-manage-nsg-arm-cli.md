<properties 
   pageTitle="在資源管理員中使用 Azure CLI 管理 NSG | Microsoft Azure"
   description="了解如何在資源管理員中使用 Azure CLI 管理現有的 NSG"
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

# 使用 Azure CLI 管理 NSG

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## 擷取資訊

您可以檢視您現有的 NSG、擷取現有 NSG 的規則，並找出與 NSG 相關聯的資源。

### 檢視現有的 NSG

若要檢視特定資源群組中的 NSG 清單，請執行 `azure network nsg list` 命令，如下所示。

	azure network nsg list --resource-group RG-NSG

預期的輸出：

	info:    Executing command network nsg list
	+ Getting the network security groups
	data:    Name          Location
	data:    ------------  --------
	data:    NSG-BackEnd   westus
	data:    NSG-FrontEnd  westus
	info:    network nsg list command OK
		 
### 列出 NSG 的所有規則

若要檢視名為 **NSG-FrontEnd** 的 NSG 的規則，請執行 `azure network nsg show` 命令，如下所示。

	azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd

預期的輸出：
	
	info:    Executing command network nsg show
	+ Looking up the network security group "NSG-FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	data:    Name                            : NSG-FrontEnd
	data:    Type                            : Microsoft.Network/networkSecurityGroups
	data:    Location                        : westus
	data:    Provisioning state              : Succeeded
	data:    Tags                            : displayName=NSG - Front End
	data:    Security group rules:
	data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
	data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
	data:    rdp-rule                       Internet           *            *               3389              Tcp       Inbound    Allow   100
	data:    web-rule                       Internet           *            *               80                Tcp       Inbound    Allow   101
	data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
	data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
	data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
	data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
	data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
	data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
	info:    network nsg show command OK

>[AZURE.NOTE] 您也可以使用 `azure network nsg rule list --resource-group RG-NSG --nsg-name NSG-FrontEnd` 從 **NSG-FrontEnd** NSG 列出規則。

### 檢視 NSG 關聯

若要檢視與 **NSG-FrontEnd** NSG 相關聯的資源，請執行 `azure network nsg show` 命令，如下所示。請注意，唯一的差別是使用 **--json** 參數。

	azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json

尋找 **NetworkInterfaces** 和 **Subnets** 屬性，如下所示︰

	"networkInterfaces": [],
	...
	"subnets": [
		{
			"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
		}
	],
	...

在上述範例中，NSG 沒有與任何網路介面 (NIC) 相關聯，而是與名稱為 **FrontEnd** 的子網路相關聯。

## 管理規則

您可以將規則新增至現有的 NSG、編輯現有的規則，以及移除規則。

### 新增規則

若要新增規則，允許來自任何電腦的連接埠 **443** 的**輸入**流量流向 **NSG-FrontEnd** NSG，請執行 `azure network nsg rule create` 命令，如下所示。

	azure network nsg rule create --resource-group RG-NSG \
		--nsg-name NSG-FrontEnd \
		--name allow-https \
		--description "Allow access to port 443 for HTTPS" \
		--protocol Tcp \
		--source-address-prefix * \
		--source-port-range * \
		--destination-address-prefix * \
		--destination-port-range 443 \
		--access Allow \
		--priority 102 \
		--direction Inbound		

預期的輸出：

	info:    Executing command network nsg rule create
	+ Looking up the network security rule "allow-https"
	+ Creating a network security rule "allow-https"
	+ Looking up the network security group "NSG-FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
	data:    Name                            : allow-https
	data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
	data:    Provisioning state              : Succeeded
	data:    Description                     : Allow access to port 443 for HTTPS
	data:    Source IP                       : *
	data:    Source Port                     : *
	data:    Destination IP                  : *
	data:    Destination Port                : 443
	data:    Protocol                        : Tcp
	data:    Direction                       : Inbound
	data:    Access                          : Allow
	data:    Priority                        : 102
	info:    network nsg rule create command OK

### 變更規則

若要變更以上所建立的規則，僅允許來自**網際網路**的輸入流量，請執行 `azure network nsg rule set` 命令，如下所示。

	azure network nsg rule set --resource-group RG-NSG \
		--nsg-name NSG-FrontEnd \
		--name allow-https \
		--source-address-prefix Internet

預期的輸出：

	info:    Executing command network nsg rule set
	+ Looking up the network security group "NSG-FrontEnd"
	+ Setting a network security rule "allow-https"
	+ Looking up the network security group "NSG-FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
	data:    Name                            : allow-https
	data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
	data:    Provisioning state              : Succeeded
	data:    Description                     : Allow access to port 443 for HTTPS
	data:    Source IP                       : Internet
	data:    Source Port                     : *
	data:    Destination IP                  : *
	data:    Destination Port                : 443
	data:    Protocol                        : Tcp
	data:    Direction                       : Inbound
	data:    Access                          : Allow
	data:    Priority                        : 102
	info:    network nsg rule set command OK

### 刪除規則

若要刪除在前文中建立的規則，請執行 `azure network nsg rule delete` 命令，如下所示。

	azure network nsg rule delete --resource-group RG-NSG \
		--nsg-name NSG-FrontEnd \
		--name allow-https \
		--quiet

>[AZURE.NOTE] **--quiet** 參數可確保您不需要確認刪除。

預期的輸出：

	info:    Executing command network nsg rule delete
	+ Looking up the network security group "NSG-FrontEnd"
	+ Deleting network security rule "allow-https"
	info:    network nsg rule delete command OK

## 管理關聯

您可以建立 NSG 與子網路和 NIC 的關聯。您也可以中斷 NSG 與其相關聯的任何資源的關聯。

### 建立 NSG 與 NIC 的關聯

若要建立 **NSG-FrontEnd** NSG 與 **TestNICWeb1** NIC 的關聯，請執行 `azure network nic set` 命令，如下所示。

	azure network nic set --resource-group RG-NSG \
		--name TestNICWeb1 \
		--network-security-group-name NSG-FrontEnd

預期的輸出：

	info:    Executing command network nic set
	+ Looking up the network interface "TestNICWeb1"
	+ Looking up the network security group "NSG-FrontEnd"
	+ Updating network interface "TestNICWeb1"
	+ Looking up the network interface "TestNICWeb1"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
	data:    Name                            : TestNICWeb1
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : westus
	data:    Provisioning state              : Succeeded
	data:    MAC address                     : 00-0D-3A-30-A1-F8
	data:    Enable IP forwarding            : false
	data:    Tags                            : displayName=NetworkInterfaces - Web
	data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
	data:    IP configurations:
	data:      Name                          : ipconfig1
	data:      Provisioning state            : Succeeded
	data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
	data:      Private IP address            : 192.168.1.5
	data:      Private IP Allocation Method  : Dynamic
	data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:
	info:    network nic set command OK

### 中斷 NSG 與 NIC 的關聯

若要中斷 **NSG-FrontEnd** NSG 與 **TestNICWeb1** NIC 的關聯，請執行 `azure network nic set` 命令，如下所示。

	azure network nic set --resource-group RG-NSG --name TestNICWeb1 --network-security-group-id ""

>[AZURE.NOTE] 請注意 **network-security-group-id** 參數的 "" (空白) 值。這是移除與 NSG 之關聯的方法。您不能對 **network-security-group-name** 參數執行同樣的操作。

預期的結果

	info:    Executing command network nic set
	+ Looking up the network interface "TestNICWeb1"
	+ Updating network interface "TestNICWeb1"
	+ Looking up the network interface "TestNICWeb1"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
	data:    Name                            : TestNICWeb1
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : westus
	data:    Provisioning state              : Succeeded
	data:    MAC address                     : 00-0D-3A-30-A1-F8
	data:    Enable IP forwarding            : false
	data:    Tags                            : displayName=NetworkInterfaces - Web
	data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
	data:    IP configurations:
	data:      Name                          : ipconfig1
	data:      Provisioning state            : Succeeded
	data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
	data:      Private IP address            : 192.168.1.5
	data:      Private IP Allocation Method  : Dynamic
	data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:
	info:    network nic set command OK

### 中斷 NSG 與子網路的關聯

若要中斷 **NSG-FrontEnd** NSG 與 **FrontEnd** 子網路的關聯，請執行 `azure network vnet subnet set` 命令，如下所示。

	azure network vnet subnet set --resource-group RG-NSG \
		--vnet-name TestVNet \
		--name FrontEnd \
		--network-security-group-id ""

預期的輸出：

	info:    Executing command network vnet subnet set
	+ Looking up the subnet "FrontEnd"
	+ Setting subnet "FrontEnd"
	+ Looking up the subnet "FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:    Type                            : Microsoft.Network/virtualNetworks/subnets
	data:    ProvisioningState               : Succeeded
	data:    Name                            : FrontEnd
	data:    Address prefix                  : 192.168.1.0/24
	data:    IP configurations:
	data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
	data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
	data:
	info:    network vnet subnet set command OK

### 建立 NSG 至子網路的關聯

若要再次建立 **NSG-FrontEnd** NSG 與 **FronEnd** 子網路的關聯，請執行 `azure network vnet subnet set` 命令，如下所示。

	azure network vnet subnet set --resource-group RG-NSG \
		--vnet-name TestVNet \
		--name FrontEnd \
		--network-security-group-name NSG-FronEnd

>[AZURE.NOTE] 上述命令能生效的原因，在於 **NSG-FrontEnd** NSG 與虛擬網路 **TestVNet** 位於相同的資源群組中。如果 NSG 位於不同資源群組，您需要改用 **--network-security-group-id** 參數並提供 NSG 的完整識別碼。您可以藉由執行 **azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json** 來擷取識別碼，並尋找 **id** 屬性。

預期的輸出：

		info:    Executing command network vnet subnet set
		+ Looking up the subnet "FrontEnd"
		+ Looking up the network security group "NSG-FrontEnd"
		+ Setting subnet "FrontEnd"
		+ Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:    Network security group          : [object Object]
		data:    IP configurations:
		data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
		data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
		data:
		info:    network vnet subnet set command OK

## 刪除 NSG

您只能刪除與任何資源沒有關聯的 NSG。若要刪除 NSG，請依照下列步驟進行。

1. 若要檢查與 NSG 相關聯的資源，請執行 `azure network nsg show`，如[檢視 NSG 關聯](#View-NSGs-associations)中所示。
2. 如果 NSG 與任何 NIC 相關聯，為每個 NIC 執行 `azure network nic set`，如[中斷 NSG 與 NIC 的關聯](#Dissociate-an-NSG-from-a-NIC)中所示。 
3. 如果 NSG 與任何子網路相關聯，為每個子網路執行 `azure network vnet subnet set`，如[中斷 NSG 與子網路的關聯](#Dissociate-an-NSG-from-a-subnet)中所示。
4. 若要刪除 NSG，請執行 `azure network nsg delete` 命令，如下所示。

		azure network nsg delete --resource-group RG-NSG --name NSG-FrontEnd --quiet

	預期的輸出：

		info:    Executing command network nsg delete
		+ Looking up the network security group "NSG-FrontEnd"
		+ Deleting network security group "NSG-FrontEnd"
		info:    network nsg delete command OK

## 後續步驟

- [啟用 NSG 的記錄](virtual-network-nsg-manage-log.md)。

<!---HONumber=AcomDC_0323_2016-->