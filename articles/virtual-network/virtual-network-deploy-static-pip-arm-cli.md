<properties 
   pageTitle="在資源管理員中使用 Azure CLI 部署使用靜態公用 IP 的 VM | Microsoft Azure"
   description="了解如何在資源管理員中使用 Azure CLI 部署使用靜態公用 IP 的 VM"
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
   ms.date="03/15/2016"
   ms.author="telmos" />

# 使用 Azure CLI 部署使用靜態公用 IP 的 VM

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## 步驟 1：啟動指令碼

[這裡](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-cli.sh)可以下載所使用的完整 Bash 指令碼。請遵循下列步驟來變更要在環境中工作的指令碼。

1. 根據要用於部署的值來變更下列變數的值。下列值對應本文件使用的案例。

		# Set variables for the new resource group
		rgName="IaaSStory"
		location="westus"
		
		# Set variables for VNet
		vnetName="TestVNet"
		vnetPrefix="192.168.0.0/16"
		subnetName="FrontEnd"
		subnetPrefix="192.168.1.0/24"
		
		# Set variables for storage
		stdStorageAccountName="iaasstorystorage"
		
		# Set variables for VM
		vmSize="Standard_A1"
		diskSize=127
		publisher="Canonical"
		offer="UbuntuServer"
		sku="14.04.2-LTS"
		version="latest"
		vmName="WEB1"
		osDiskName="osdisk"
		nicName="NICWEB1"
		privateIPAddress="192.168.1.101"
		username='adminuser'
		password='adminP@ssw0rd'
		pipName="PIPWEB1"
		dnsName="iaasstoryws1"

## 步驟 2：為 VM 建立必要的資源

建立 VM 之前，您需要供 VM 使用的資源群組、VNet、公用 IP 及 NIC。

1. 建立新的資源群組。

		azure group create $rgName $location
		
2. 建立 VNet 和子網路。
		
		azure network vnet create --resource-group $rgName \
		    --name $vnetName \
		    --address-prefixes $vnetPrefix \
		    --location $location
		azure network vnet subnet create --resource-group $rgName \
		    --vnet-name $vnetName \
		    --name $subnetName \
		    --address-prefix $subnetPrefix

3. 建立公用 IP 資源。

		azure network public-ip create --resource-group $rgName \
		    --name $pipName \
		    --location $location \
		    --allocation-method Static \
		    --domain-name-label $dnsName 

4. 使用公用 IP 為上述建立之子網路中的 VM 建立網路介面 (NIC)。請注意第一組命令是用來擷取上述建立的子網路的「識別碼」 。

		subnetId="$(azure network vnet subnet show --resource-group $rgName \
		                --vnet-name $vnetName \
		                --name $subnetName|grep Id)"

		subnetId=${subnetId#*/}
		
		azure network nic create --name $nicName \
		    --resource-group $rgName \
		    --location $location \
		    --private-ip-address $privateIPAddress \
		    --subnet-id $subnetId \
		    --public-ip-name $pipName

>[AZURE.TIP] 上述的第一個命令會使用 [grep](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_04_02.html) 和[字串操作](http://tldp.org/LDP/abs/html/string-manipulation.html) (更具體來說，是子字串移除)。

5. 建立裝載 VM 作業系統磁碟機的儲存體帳戶。

		azure storage account create $stdStorageAccountName \
		    --resource-group $rgName \
		    --location $location --type LRS 

## 步驟 3：建立 VM 

現在所有必要的資源已準備就緒，您可以建立新的 VM。

1. 建立 VM。

		azure vm create --resource-group $rgName \
		    --name $vmName \
		    --location $location \
		    --vm-size $vmSize \
		    --subnet-id $subnetId \
		    --nic-names $nicName \
		    --os-type linux \
		    --image-urn $publisher:$offer:$sku:$version \
		    --storage-account-name $stdStorageAccountName \
		    --storage-account-container-name vhds \
		    --os-disk-vhd $osDiskName.vhd \
		    --admin-username $username \
		    --admin-password $password

2. 儲存指令碼檔案。

## 步驟 4：執行指令碼

在進行任何必要的變更並了解上述指令碼後，請執行指令碼。

1. 從 bash 主控台執行上述指令碼。

		sh myscript.sh

2. 幾分鐘後，應該會顯示以下輸出。

		info:    Executing command group create
		info:    Getting resource group IaaSStory
		info:    Creating resource group IaaSStory
		info:    Created resource group IaaSStory
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory
		data:    Name:                IaaSStory
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK
		info:    Executing command network vnet create
		info:    Looking up virtual network "TestVNet"
		info:    Creating virtual network "TestVNet"
		info:    Loading virtual network state
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/TestVNet
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : westus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		info:    network vnet create command OK
		info:    Executing command network vnet subnet create
		info:    Looking up the subnet "FrontEnd"
		info:    Creating subnet "FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:
		info:    network vnet subnet create command OK
		info:    Executing command network public-ip create
		info:    Looking up the public ip "PIPWEB1"
		info:    Creating public ip address "PIPWEB1"
		info:    Looking up the public ip "PIPWEB1"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/publicIPAddresses/PIPWEB1
		data:    Name                            : PIPWEB1
		data:    Type                            : Microsoft.Network/publicIPAddresses
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Allocation method               : Static
		data:    Idle timeout                    : 4
		data:    IP Address                      : 40.78.63.253
		data:    Domain name label               : iaasstoryws1
		data:    FQDN                            : iaasstoryws1.westus.cloudapp.azure.com
		info:    network public-ip create command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICWEB1"
		info:    Looking up the public ip "PIPWEB1"
		info:    Creating network interface "NICWEB1"
		info:    Looking up the network interface "NICWEB1"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkInterfaces/NICWEB1
		data:    Name                            : NICWEB1
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/publicIPAddresses/PIPWEB1
		data:      Private IP address            : 192.168.1.101
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory2/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:
		info:    network nic create command OK
		info:    Executing command storage account create
		info:    Creating storage account
		info:    storage account create command OK
		info:    Executing command vm create
		info:    Looking up the VM "WEB1"
		info:    Using the VM Size "Standard_A1"
		info:    The [OS, Data] Disk or image configuration requires storage account
		info:    Looking up the storage account iaasstorystorage
		info:    Looking up the NIC "NICWEB1"
		info:    Creating VM "WEB1"
		info:    vm create command OK

<!---HONumber=AcomDC_0323_2016-->