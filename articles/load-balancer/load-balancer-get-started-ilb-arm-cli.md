<properties 
   pageTitle="使用資源管理員中的 Azure CLI 建立內部負載平衡器| Microsoft Azure"
   description="了解如何使用資源管理員的 Azure CLI 建立內部負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />

# 開始使用 Azure CLI 建立內部負載平衡器

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]<BR>[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](load-balancer-get-started-ilb-classic-cli.md)。

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## 若要建立內部負載平衡器，需要哪些項目？

您需要建立和設定下列物件以部署負載平衡器：

- 前端 IP 設定 - 設定供連入網路流量使用的私人 IP 位址 

- 後端位址集區 - 包含從負載平衡器接收流量的網路介面 (NIC)。

- 負載平衡規則 - 包含可以將連入網路流量連接埠對應到後端集區中，用以接收網路流量之連接埠的規則。

- 輸入 NAT 規則 - 包含將負載平衡器上的公用連接埠對應至後端位址集區中特定虛擬機器之連接埠的規則。

- 探查 - 包含可用於檢查後端位址集區中，虛擬機器執行個體之可用性的健康情況探查。

您可以在 [Azure 資源管理員的負載平衡器支援](load-balancer-arm.md)中取得有關負載平衡器元件與 Azure 資源管理員的詳細資訊。

## 設定 CLI 以使用資源管理員

1. 若您從未使用過 Azure CLI，請參閱[安裝及設定 Azure CLI](xplat-cli.md)，並依照指示執行到選取您的 Azure 帳戶和訂用帳戶為止。

2. 執行 **azure config mode** 命令，以切換為 Azure 資源管理員模式，如下所示。

		azure config mode arm

	預期的輸出：

		info:    New mode is arm

## 逐步建立內部負載平衡器 

下列步驟會依據上述案例建立內部負載平衡器：

### 步驟 1 

若還未下載最新版的 [Azure 命令列介面](https://azure.microsoft.com/downloads/)，請先下載。

### 步驟 2 

完成安裝之後，請驗證您的帳戶。

	azure login

此驗證程序會要求您使用 Azure 訂用帳戶的名稱及密碼。

### 步驟 3

將命令工具變更為 Azure 資源管理員模式。

	azure config mode arm

## 建立資源群組

Azure 資源管理員中的所有資源都會關聯到資源群組。若還未建立資源群組，請先建立。

	azure group create <resource group name> <location>


## 建立內部負載平衡器集 


### 步驟 1 

使用 `azure network lb create` 命令建立內部負載平衡器。在下列案例中，有一個名為 nrprg 的資源群組建立於美國東部區域。
 	
	azure network lb create -n nrprg -l westus

>[AZURE.NOTE] 內部負載平衡器的所有資源 (例如虛擬網路和虛擬網路子網路) 必須位於同一個資源群組及區域。


### 步驟 2 

建立內部負載平衡器的前端 IP 位址。使用的 IP 位址必須位於虛擬網路的子網路範圍內。

	
	azure network lb frontend-ip create -g nrprg -l ilbset -n feilb -a 10.0.0.7 -e nrpvnetsubnet -m nrpvnet

所使用的參數：

**-g** - 資源群組 **-l** - 內部負載平衡器集的名稱 **-n** - 前端 IP 的名稱 **-a** - 子網路範圍內的私用 IP 位址 **-e** - 子網路名稱 **-m** - 虛擬網路名稱

### 步驟 3 

建立後端位址集區。

	azure network lb address-pool create -g nrprg -l ilbset -n beilb

所使用的參數：

**-g** - 資源群組 **-l** - 內部負載平衡器集的名稱 **-n** - 後端位址集區的名稱

定義前端 IP 位址和後端位址集區之後，您可以建立負載平衡器規則、連入 NAT 規則，以及自訂健康情況探查。

### 步驟 4


建立內部負載平衡器的負載平衡器規則。緊接在上述案例之後，此命令會建立接聽前端集區中之連接埠 1433，以及將負載平衡的網路流量傳送到後端位址集區時，也使用連接埠 1433 的負載平衡器規則。

	azure network lb rule create -g nrprg -l ilbset -n ilbrule -p tcp -f 1433 -b 1433 -t feilb -o beilb

所使用的參數：

**-g** - 資源群組 **-l** - 內部負載平衡器集的名稱 **-n** - 負載平衡器規則的名稱 **-p** - 規則所使用的通訊協定 **-f** - 負載平衡器前端中，負責接聽連入網路流量的連接埠 **-b** - 後端位址集區中，負責接收網路流量的連接埠

### 步驟 5

建立連入 NAT 規則。連入 NAT 規則會用在負載平衡器中建立要分配給特定虛擬機器執行個體的端點。緊接在上述範例之後，則會建立 2 項有關於遠端桌面存取的 NAT 規則。

	azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule1 -p TCP -f 5432 -b 3389
	
	azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule2 -p TCP -f 5433 -b 3389

使用的參數：

**-g** - 資源群組 **-l** - 內部負載平衡器集的名稱 **-n** - 連入 NAT 規則的名稱 **-p** - 規則所使用的通訊協定 **-f** - 負載平衡器前端中，負責接聽連入網路流量的連接埠 **-b** - 後端位址集區中，負責接收網路流量的連接埠

### 步驟 5 

為負載平衡器建立健康情況探查。健康情況探查會檢查所有虛擬機器執行個體，確認其可以傳送網路流量。探查檢查失敗的虛擬機器執行個體會從負載平衡器上移除，直到其恢復正常運作並通過健康情況探查為止。

	azure network lb probe create -g nrprg -l ilbset -n ilbprobe -p tcp -i 300 -c 4

**-g** - 資源群組 **-l** - 內部負載平衡器集的名稱 **-n** - 健康情況探查的名稱 **-p** - 健康情況探查所使用的通訊協定 **-i** - 探查間隔 (秒) **-c** - 檢查數

>[AZURE.NOTE] Microsoft Azure 平台會對各種管理案例使用靜態、可公開路由傳送的 IPv4 位址。IP 位址是 168.63.129.16。此 IP 位址不應該遭到任何防火牆封鎖，因為可能會造成非預期的行為。採用 Azure 內部負載平衡，來自負載平衡器的監視探查會使用此 IP 位址，藉此判斷虛擬機器在負載平衡集的健全狀態。如果網路安全性群組已用來限制傳輸至內部負載平衡集中 Azure 虛擬機器的流量，或已套用至虛擬網路子網路，請確定您已新增網路安全性規則，允許來自 168.63.129.16 的流量。

## 建立 NIC

您需要建立 NIC (或修改現有的) 並將它們關聯至 NAT 規則、負載平衡器規則和探查。

### 步驟 1 

建立名為 *lb-nic1-be* 的 NIC，並將其關聯到 *rdp1* NAT 規則及 *beilb* 後端位址集區。
	
	azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

參數：

- **-g** - 資源群組名稱
- **-n** - NIC 資源的名稱
- **--subnet-name** - 子網路的名稱 
- **--subnet-vnet-name** - 虛擬網路的名稱
- **-d** - 後端集區資源的 ID - 開頭為 /subscription/{subscriptionID/resourcegroups/<resourcegroup-name>/providers/Microsoft.Network/loadbalancers/<load-balancer-name>/backendaddresspools/<name-of-the-backend-pool> 
- **-e** - 將與 NIC 資源相關聯的 NAT 規則 ID - 開頭為 /subscriptions/####################################/resourceGroups/<resourcegroup-name>/providers/Microsoft.Network/loadBalancers/<load-balancer-name>/inboundNatRules/<nat-rule-name>


預期的輸出：

	info:    Executing command network nic create
	+ Looking up the network interface "lb-nic1-be"
	+ Looking up the subnet "nrpvnetsubnet"
	+ Creating network interface "lb-nic1-be"
	+ Looking up the network interface "lb-nic1-be"
	data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	data:    Name                            : lb-nic1-be
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : eastus
	data:    Provisioning state              : Succeeded
	data:    Enable IP forwarding            : false
	data:    IP configurations:
	data:      Name                          : NIC-config
	data:      Provisioning state            : Succeeded
	data:      Private IP address            : 10.0.0.4
	data:      Private IP Allocation Method  : Dynamic
	data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
	data:      Load balancer backend address pools
	data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
	data:      Load balancer inbound NAT rules:
	data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
	data:
	info:    network nic create command OK

### 步驟 2

建立名為 *lb-nic2-be* 的 NIC，並將其關聯到 *rdp2* NAT 規則及 *beilb* 後端位址集區。

 	azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

### 步驟 3 

建立名為 *DB1* 的虛擬機器 (VM)，並將其關聯到名為 *lb-nic1-be* 的 NIC。系統先建立了名為 *web1nrp* 的儲存體帳戶，再執行下方命令。

	azure vm create --resource-group nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

>[AZURE.IMPORTANT] 負載平衡器中的 VM 必須在相同的可用性設定組中。使用 `azure availset create` 建立可用性設定組。

### 步驟 4

建立名為 *DB2* 的虛擬機器 (VM)，並將其關聯到名為 *lb-nic2-be* 的 NIC。在執行下列命令之前，會先建立名為 *web1nrp* 的儲存體帳戶。

	azure vm create --resource-group nrprg --name DB2 --location eastus --vnet-	name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## 刪除負載平衡器 


若要移除負載平衡器，請使用下列命令

	azure network lb delete -g nrprg -n ilbset 

其中 **nrprg** 是資源群組，而 **ilbset** 則是內部負載平衡器名稱。


## 後續步驟

[使用來源 IP 同質性設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0218_2016-->