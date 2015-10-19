<properties 
   pageTitle="在資源管理員中使用 Azure CLI 控制路由和使用虛擬應用裝置 | Microsoft Azure"
   description="了解如何使用 Azure CLI 控制路由和使用虛擬應用裝置"
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
   ms.date="09/30/2015"
   ms.author="telmos" />

#在 Azure CLI 中建立使用者定義的路由 (UDR)

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋之內容包括資源管理員部署模型。您也可以[在傳統部署模型中建立 UDR](virtual-network-create-udr-classic-cli.md)。

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

以下的範例 Azure CLI 命令是假設您已根據上述案例建立簡單的環境。如果您想要以本文件顯示的方式執行命令，請先依照下列方式建置測試環境：部署[此範本](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)，按一下 [部署至 Azure]，視情況取代預設參數值，然後遵循入口網站中的指示。

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## 建立前端子網路的 UDR
若要根據上述案例建立前端子網路所需的路由表和路徑，請依照下列步驟執行。

3. 執行 **`azure network route-table create`** 命令，建立前端子網路的路由表。

		azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest

	Output:

		info:    Executing command network route-table create
		info:    Looking up route table "UDR-FrontEnd"
		info:    Creating route table "UDR-FrontEnd"
		info:    Looking up route table "UDR-FrontEnd"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/
		routeTables/UDR-FrontEnd
		data:    Name                            : UDR-FrontEnd
		data:    Type                            : Microsoft.Network/routeTables
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		info:    network route-table create command OK

	參數：- **-g (或 --resource-group)**。將會在當中建立 NSG 之資源群組的名稱。在本文案例中為 *TestRG*。- **-l (或 --location)**。將要建立新 NSG 的 Azure 區域。在本文案例中為 *westus*。- **-n (或 --name)**。新 NSG 的名稱。在本文案例中為 *NSG-FrontEnd*。

4. 執行 **`azure network route-table route create`** 命令，在上方建立的路由表中建立路由，將目的地為後端子網路 (192.168.2.0/24) 的所有流量傳送到 **FW1** VM (192.168.0.4)。

		azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4

	Output:

		info:    Executing command network route-table route create
		info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
		info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
		info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		routeTables/UDR-FrontEnd/routes/RouteToBackEnd
		data:    Name                            : RouteToBackEnd
		data:    Provisioning state              : Succeeded
		data:    Next hop type                   : VirtualAppliance
		data:    Next hop IP address             : 192.168.0.4
		data:    Address prefix                  : 192.168.2.0/24
		info:    network route-table route create command OK

	參數：- **-r (或 --route-table-name)**。將會加入路由的路由表的名稱。在本文案例中為 *UDR-FrontEnd*。- **-a (或 --address-prefix)**。封包所指向位置的子網路的位址首碼。在本文案例中為 *192.168.2.0/24*。- **-y (或 --next-hop-type)**。將傳送流量的目標物件類型。可能的值為 *VirtualAppliance*、*VirtualNetworkGateway*、*VNETLocal*、*Internet* 或 *None*。- **-p (或 --next-hop-ip-address**))。下個躍點的 IP 位址。在本文案例中為 *192.168.0.4*。

5. 執行 **`azure network vnet subnet set`** 命令，將上方建立的路由表關聯至 **FrontEnd** 子網路。

		azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd

	Output:

		info:    Executing command network vnet subnet set
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up route table "UDR-FrontEnd"
		info:    Setting subnet "FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:    Network security group          : [object Object]
		data:    Route Table                     : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		routeTables/UDR-FrontEnd
		data:    IP configurations:
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
		igurations/ipconfig1
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
		igurations/ipconfig1
		data:    
		info:    network vnet subnet set command OK

	參數：- **-e (或 --vnet-name)**。子網路所在的 VNet 名稱。在本文案例中為 *TestVNet*。
 
## 建立後端子網路的 UDR
若要根據上述案例建立後端子網路所需的路由表和路徑，請依照下列步驟執行。

1. 執行 **`azure network route-table create`** 命令，建立後端子網路的路由表。

		azure network route-table create -g TestRG -n UDR-BackEnd -l westus

4. 執行 **`azure network route-table route create`** 命令，在上方建立的路由表中建立路由，將目的地為前端子網路 (192.168.1.0/24) 的所有流量傳送到 **FW1** VM (192.168.0.4)。

		azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4

5. 執行 **`azure network vnet subnet set`** 命令，將上方建立的路由表關聯至 **BackEnd** 子網路。

		azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd

## 啟用 FW1 上的 IP 轉送
若要啟用 **FW1** 所使用的 NIC 中的 IP 轉送，請依照下列步驟執行。

1. 執行 **`azure network nic show`** 命令，並注意**啟用 IP 轉送**的值。應該設定為 *false*。

		azure network nic show -g TestRG -n NICFW1

	Output:
		
		info:    Executing command network nic show
		info:    Looking up the network interface "NICFW1"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkInterfaces/NICFW1
		data:    Name                            : NICFW1
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    MAC address                     : 00-0D-3A-30-95-B3
		data:    Enable IP forwarding            : false
		data:    Tags                            : displayName=NetworkInterfaces - DMZ
		data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
		virtualMachines/FW1
		data:    IP configurations:
		data:      Name                          : ipconfig1
		data:      Provisioning state            : Succeeded
		data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		publicIPAddresses/PIPFW1
		data:      Private IP address            : 192.168.0.4
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/DMZ
		data:    
		info:    network nic show command OK

2. 執行 **`azure network nic set`** 命令以啟用 IP 轉送。

		azure network nic set -g TestRG -n NICFW1 -f true

	Output:

		info:    Executing command network nic set
		info:    Looking up the network interface "NICFW1"
		info:    Updating network interface "NICFW1"
		info:    Looking up the network interface "NICFW1"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkInterfaces/NICFW1
		data:    Name                            : NICFW1
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    MAC address                     : 00-0D-3A-30-95-B3
		data:    Enable IP forwarding            : true
		data:    Tags                            : displayName=NetworkInterfaces - DMZ
		data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
		virtualMachines/FW1
		data:    IP configurations:
		data:      Name                          : ipconfig1
		data:      Provisioning state            : Succeeded
		data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		publicIPAddresses/PIPFW1
		data:      Private IP address            : 192.168.0.4
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/DMZ
		data:    
		info:    network nic set command OK

	參數：

	- **-f (或 --enable-ip-forwarding)**。*true* 或 *false*。

<!---HONumber=Oct15_HO2-->