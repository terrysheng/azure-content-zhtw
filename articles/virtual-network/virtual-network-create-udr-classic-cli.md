<properties 
   pageTitle="在傳統部署模型中使用 Azure CLI 控制路由和使用虛擬應用裝置 | Microsoft Azure"
   description="了解如何在傳統部署模型中使用 Azure CLI 來控制 VNet 中的路由"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2015"
   ms.author="telmos" />

#使用 Azure CLI 控制路由和使用虛擬應用裝置 (傳統)

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋之內容包括傳統部署模型。您也可以[在資源管理員部署模型中控制路由和使用虛擬應用裝置](virtual-network-create-udr-arm-cli.md)。

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

以下的範例 Azure CLI 命令是假設您已根據上述案例建立簡單的環境。如果您想要執行如本文件中所示的命令，請建立[使用 Azure CLI 建立 VNet (傳統)](virtual-networks-create-vnet-classic-cli.md) 中所示的環境。

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## 建立前端子網路的 UDR
若要根據上述案例建立前端子網路所需的路由表和路徑，請依照下列步驟執行。

1. 執行 **`azure config mode`** 切換到傳統模式。

		azure config mode asm

	Output:

		info:    New mode is asm

3. 執行 **`azure network route-table create`** 命令，建立前端子網路的路由表。

		azure network route-table create -n UDR-FrontEnd -l uswest

	Output:

		info:    Executing command network route-table create
		info:    Creating route table "UDR-FrontEnd"
		info:    Getting route table "UDR-FrontEnd"
		data:    Name                            : UDR-FrontEnd
		data:    Location                        : West US
		info:    network route-table create command OK

	參數：- **-l (或 --location)**。將要建立新 NSG 的 Azure 區域。在本文案例中為 *westus*。- **-n (或 --name)**。新 NSG 的名稱。在本文案例中為 *NSG-FrontEnd*。

4. 執行 **`azure network route-table route set`** 命令，在上方建立的路由表中建立路由，將目的地為後端子網路 (192.168.2.0/24) 的所有流量傳送到 **FW1** VM (192.168.0.4)。

		azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4

	Output:

		info:    Executing command network route-table route set
		info:    Getting route table "UDR-FrontEnd"
		info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
		info:    network route-table route set command OK

	參數：- **-r (或 --route-table-name)**。將會加入路由的路由表的名稱。在本文案例中為 *UDR-FrontEnd*。- **-a (或 --address-prefix)**。封包所指向位置的子網路的位址首碼。在本文案例中為 *192.168.2.0/24*。- **-t (或 --next-hop-type)**。將傳送流量的目標物件類型。可能的值為 *VirtualAppliance*、*VirtualNetworkGateway*、*VNETLocal*、*Internet* 或 *None*。- **-p (或 --next-hop-ip-address**))。下個躍點的 IP 位址。在本文案例中為 *192.168.0.4*。

5. 執行 **`azure network vnet subnet route-table add`** 命令，將上方建立的路由表關聯至 **FrontEnd** 子網路。

		azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd

	Output:

		info:    Executing command network vnet subnet route-table add
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up network configuration
		info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
		info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
		info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
		data:    Route table name                : UDR-FrontEnd
		data:      Location                      : West US
		data:      Routes:
		info:    network vnet subnet route-table add command OK	

	參數：- **-t (或 --vnet-name)**。子網路所在的 VNet 名稱。在本文案例中為 *TestVNet*。- **-n (或 --subnet-name**。路由表將加入的子網路的名稱。在本文案例中為 *FrontEnd*。
 
## 建立後端子網路的 UDR
若要根據上述案例建立後端子網路所需的路由表和路徑，請依照下列步驟執行。

3. 執行 **`azure network route-table create`** 命令，建立後端子網路的路由表。

		azure network route-table create -n UDR-BackEnd -l uswest

4. 執行 **`azure network route-table route set`** 命令，在上方建立的路由表中建立路由，將目的地為前端子網路 (192.168.1.0/24) 的所有流量傳送到 **FW1** VM (192.168.0.4)。

		azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4

5. 執行 **`azure network vnet subnet route-table add`** 命令，將上方建立的路由表關聯至 **BackEnd** 子網路。

		azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd

<!---HONumber=Oct15_HO2-->