<properties 
   pageTitle="如何在傳統模式中使用 Azure CLI 建立 NSG | Microsoft Azure"
   description="了解如何傳統模式中使用 Azure CLI 建立及部署 NSG"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/15/2015"
   ms.author="telmos" />

# 如何在 Azure CLI 中建立 NSG (傳統)

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋之內容包括傳統部署模型。您也可以[在資源管理員部署模型中建立 NSG](virtual-networks-create-nsg-arm-cli.md)。

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

以下的範例 Azure CLI 命令是假設您已根據上述案例建立簡單的環境。如果您想要執行如本文件中所顯示的命令，請先建置[建立 VNet](virtual-networks-create-vnet-classic-cli) 以建置測試環境。

## 如何建立前端子網路的 NSG
若要根據上述案例建立名為 *NSG-FrontEnd* 的 NSG，請依照下列步驟執行。

1. 如果您從未使用過 Azure CLI，請參閱[安裝和設定 Azure CLI](xplat-cli.md)，並依照指示進行，直到開始選取您的 Azure 帳戶和訂用帳戶為止。

2. 執行 **azure config mode** 命令，以切換為傳統模式，如下所示。

		azure config mode asm

	預期的輸出：

		info:    New mode is asm

3. 執行 **azure network nsg create** 命令以建立 NSG。

		azure network nsg create -l uswest -n NSG-FrontEnd

	預期的輸出：

		info:    Executing command network nsg create
		info:    Creating a network security group "NSG-FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : NSG-FrontEnd
		data:    Location                        : West US
		data:    Security group rules:
		data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
		ity  Default
		data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
		---  -------
		data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
		     true   
		data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
		     true   
		data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
		     true   
		data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
		     true   
		data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
		     true   
		data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
		     true   
		info:    network nsg create command OK

	參數：

	- **-l (或 --location)**。將要建立新 NSG 的 Azure 區域。在本文案例中為 *westus*。
	- **-n (或 --name)**。新 NSG 的名稱。在本文案例中為 *NSG-FrontEnd*。

4. 執行 **azure network nsg rule create** 命令來建立允許從網際網路存取連接埠 3389 (RDP) 的規則。

		azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

	預期的輸出：

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security rule "rdp-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : rdp-rule
		data:    Source address prefix           : INTERNET
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 3389
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

	參數：

	- **-a (或 --nsg-name)**。將在其中建立規則之 NSG 的名稱。在本文案例中為 *NSG-FrontEnd*。
	- **-n (或 --name)**。新規則的名稱。在本文案例中為 *rdp-rule*。
	- **-c (或 --action)**。規則 (拒絕或允許) 的存取層級。
	- **-p (或 --protocol)**。規則的通訊協定 (TCP、UDP 或 *)。
- **-r (或 --type)**。連線 (輸入或輸出) 的方向。
	- **-y (或 --priority)**。規則的優先順序。
	- **-f (或 --source-address-prefix)**。CIDR 中的來源位址首碼或使用預設標記。
	- **-o (或 --source-port-range)**。來源連接埠，或連接埠範圍。
	- **-e (或 --destination-address-prefix)**。CIDR 中的目的地位址首碼或使用預設標記。
	- **-u (或 --destination-port-range)**。目的地連接埠，或連接埠範圍。	

5. 執行 **azure network nsg rule create** 命令來建立允許從網際網路存取連接埠 80 (HTTP) 的規則。

		azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

	預期的輸出：

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : web-rule
		data:    Source address prefix           : INTERNET
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 80
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. 執行 **azure network nsg subnet add** 命令來連結 NSG 與前端子網路。

		azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd 

	預期的輸出：

		info:    Executing command network nsg subnet add
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up network configuration
		info:    Creating a network security group "NSG-FrontEnd"
		info:    network nsg subnet add command OK

## 如何建立後端子網路的 NSG
若要根據上述案例建立名為 *NSG-BackEnd* 的 NSG，請依照下列步驟執行。

3. 執行 **azure network nsg create** 命令以建立 NSG。

		azure network nsg create -l uswest -n NSG-BackEnd

	預期的輸出：

		info:    Executing command network nsg create
		info:    Creating a network security group "NSG-BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : NSG-BackEnd
		data:    Location                        : West US
		data:    Security group rules:
		data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
		ity  Default
		data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
		---  -------
		data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
		     true   
		data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
		     true   
		data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
		     true   
		data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
		     true   
		data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
		     true   
		data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
		     true   
		info:    network nsg create command OK

	參數：

	- **-l (或 --location)**。將要建立新 NSG 的 Azure 區域。在本文案例中為 *westus*。
	- **-n (或 --name)**。新 NSG 的名稱。在本文案例中為 *NSG-FrontEnd*。

4. 執行 **azure network nsg rule create** 命令來建立允許從前端子網路存取連接埠 1433 (SQL) 的規則。

		azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

	預期的輸出：

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security rule "sql-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : sql-rule
		data:    Source address prefix           : 192.168.1.0/24
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 1433
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK


5. 執行 **azure network nsg rule create** 命令來建立拒絕存取網際網路的規則。

		azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80

	預期的輸出：

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : web-rule
		data:    Source address prefix           : *
		data:    Source Port                     : *
		data:    Destination address prefix      : INTERNET
		data:    Destination Port                : 80
		data:    Protocol                        : TCP
		data:    Type                            : Outbound
		data:    Action                          : Deny
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. 執行 **azure network nsg subnet add** 命令來連結 NSG 與後端子網路。

		azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd 

	預期的輸出：

		info:    Executing command network nsg subnet add
		info:    Looking up the network security group "NSG-BackEndX"
		info:    Looking up the subnet "BackEnd"
		info:    Looking up network configuration
		info:    Creating a network security group "NSG-BackEndX"
		info:    network nsg subnet add command OK

<!---HONumber=Sept15_HO4-->