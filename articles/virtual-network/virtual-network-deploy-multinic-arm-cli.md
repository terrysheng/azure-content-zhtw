<properties 
   pageTitle="在資源管理員中使用 Azure CLI 部署多個 NIC VM | Microsoft Azure"
   description="了解如何在資源管理員中使用 Azure CLI 部署多個 NIC VM"
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
   ms.date="02/02/2016"
   ms.author="telmos" />

#使用 Azure CLI 部署多個 NIC VM

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

因為在這個時間點，您不能讓配備單一 NIC 的 VM 與配備多個 NIC 的 VM 位在同一個資源群組中，您要在與所有其他元件不同的資源群組中實作後端伺服器。下列步驟中是使用名為 *IaaSStory* 的資源群組做為主要資員群組，以及使用 *IaaSStory-BackEnd* 做為後端伺服器。

## 必要條件

您必須為此案例部署含有所有必要資源的主要資源群組，然後才可以部署後端伺服器。若要部署這些資源，請遵循下列步驟。

1. 瀏覽至[範本頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)。
2. 在範本頁面中，按一下 [父資源群組] 右邊的 [部署至 Azure]。
3. 視需要變更參數值，然後依照 Azure Preview 入口網站中的步驟部署資源群組。

> [AZURE.IMPORTANT] 請確定您的儲存體帳戶名稱是唯一的。在 Auzre 中不能有重複的儲存體帳戶名稱。

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## 部署後端 VM

後端 VM 有賴於建立下列資源。

- **資料磁碟的儲存體帳戶**。為取得更佳的效能，資料庫伺服器上的資料磁碟會使用需要進階儲存體帳戶的固態硬碟 (SSD) 技術。請確定 Azure 的部署位置，以支援進階儲存體。
- **NIC**。每部 VM 都會有兩個 NIC，一個用於資料庫存取，另一個用於管理。
- **可用性設定組**。所有的資料庫伺服器都會加入單一的可用性設定組，確保在維護期間至少有一部 VM 啟動並執行。 

### 步驟 1：啟動指令碼

[這裡](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-cli.sh)可以下載所使用的完整 Bash 指令碼。請遵循下列步驟來變更要在環境中工作的指令碼。

1. 根據上述[必要條件](#Prerequisites)中已部署的現有資源群組來變更下列變數的值。

		existingRGName="IaaSStory"
		location="westus"
		vnetName="WTestVNet"
		backendSubnetName="BackEnd"
		remoteAccessNSGName="NSG-RemoteAccess"
		
2. 根據要用於後端部署的值來變更下列變數的值。

		backendRGName="IaaSStory-Backend"
		prmStorageAccountName="wtestvnetstorageprm"
		avSetName="ASDB"
		vmSize="Standard_DS3"
		diskSize=127
		publisher="Canonical"
		offer="UbuntuServer"
		sku="14.04.2-LTS"
		version="latest"
		vmNamePrefix="DB"
		osDiskName="osdiskdb"
		dataDiskName="datadisk"
		nicNamePrefix="NICDB"
		ipAddressPrefix="192.168.2."
		username='adminuser'
		password='adminP@ssw0rd'
		numberOfVMs=2

3. 擷取將在其中建立 VM 之 `BackEnd` 子網路的識別碼。您需要這麼做，因為要和這個子網路關聯的 NIC 位於不同的資源群組中。

		subnetId="$(azure network vnet subnet show --resource-group $existingRGName \
		                --vnet-name $vnetName \
		                --name $backendSubnetName|grep Id)"
		subnetId=${subnetId#*/}

>[AZURE.TIP] 上述的第一個命令會使用 [grep](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_04_02.html) 和[字串操作](http://tldp.org/LDP/abs/html/string-manipulation.html) (更具體來說，是子字串移除)。

4. 擷取 `NSG-RemoteAccess` NSG 的識別碼。您需要這麼做，因為要和這個 NSG 關聯的 NIC 位於不同的資源群組中。

		nsgId="$(azure network nsg show --resource-group $existingRGName \
		                --name $remoteAccessNSGName|grep Id)"
		nsgId=${nsgId#*/}

### 步驟 2：為 VM 建立必要的資源

1. 為所有後端資源建立新的資源群組。請注意，資員群組名稱的 `$backendRGName` 變數，以及 Azure 區域之 `$location` 的使用方式。

		azure group create $backendRGName $location

2. 為您的 VM 要使用的作業系統和資料磁碟建立進階儲存體帳戶。

		azure storage account create $prmStorageAccountName \
		    --resource-group $backendRGName \
		    --location $location \
			--type PLRS 

3. 為 VM 建立可用性設定組。

		azure availset create --resource-group $backendRGName \
		    --location $location \
		    --name $avSetName

### 步驟 3：建立 NIC 和後端 VM

1. 根據 `numberOfVMs` 變數，啟動迴圈以建立多部 VM。

		for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
		do

2. 針對每部 VM，建立 NIC 以用於資料庫存取。

		    nic1Name=$nicNamePrefix$suffixNumber-DA
		    x=$((suffixNumber+3))
		    ipAddress1=$ipAddressPrefix$x
		    azure network nic create --name $nic1Name \
		        --resource-group $backendRGName \
		        --location $location \
		        --private-ip-address $ipAddress1 \
		        --subnet-id $subnetId

3. 針對每部 VM，建立 NIC 以用於遠端存取。請注意用於關聯 NIC 與 NSG 的 `--network-security-group` 參數。

		    nic2Name=$nicNamePrefix$suffixNumber-RA
		    x=$((suffixNumber+53))
		    ipAddress2=$ipAddressPrefix$x
		    azure network nic create --name $nic2Name \
		        --resource-group $backendRGName \
		        --location $location \
		        --private-ip-address $ipAddress2 \
		        --subnet-id $subnetId $vnetName \
		        --network-security-group-id $nsgId

4. 建立 VM。

		    azure vm create --resource-group $backendRGName \
		        --name $vmNamePrefix$suffixNumber \
		        --location $location \
		        --vm-size $vmSize \
		        --subnet-id $subnetId \
		        --availset-name $avSetName \
		        --nic-names $nic1Name,$nic2Name \
		        --os-type linux \
		        --image-urn $publisher:$offer:$sku:$version \
		        --storage-account-name $prmStorageAccountName \
		        --storage-account-container-name vhds \
		        --os-disk-vhd $osDiskName$suffixNumber.vhd \
		        --admin-username $username \
		        --admin-password $password

5. 針對每部 VM，建立兩個資料磁碟，並使用 `done` 命令結束迴圈。

		    azure vm disk attach-new --resource-group $backendRGName \
		        --vm-name $vmNamePrefix$suffixNumber \        
		        --storage-account-name $prmStorageAccountName \
		        --storage-account-container-name vhds \
		        --vhd-name $dataDiskName$suffixNumber-1.vhd \
		        --size-in-gb $diskSize \
		        --lun 0
		
		    azure vm disk attach-new --resource-group $backendRGName \
		        --vm-name $vmNamePrefix$suffixNumber \        
		        --storage-account-name $prmStorageAccountName \
		        --storage-account-container-name vhds \
		        --vhd-name $dataDiskName$suffixNumber-2.vhd \
		        --size-in-gb $diskSize \
		        --lun 1
		done


### 步驟 4：執行指令碼

現在您已根據需求下載並變更了指令碼，請執行指令碼來建立具有多個 NIC 的後端資料庫 VM。

1. 儲存您的指令碼並從 **Bash** 終端機執行。您會看到初始的輸出，如下所示。

		info:    Executing command group create
		info:    Getting resource group IaaSStory-Backend
		info:    Creating resource group IaaSStory-Backend
		info:    Created resource group IaaSStory-Backend
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
		data:    Name:                IaaSStory-Backend
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK
		info:    Executing command storage account create
		info:    Creating storage account
		info:    storage account create command OK
		info:    Executing command availset create
		info:    Looking up the availability set "ASDB"
		info:    Creating availability set "ASDB"
		info:    availset create command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB1-DA"
		info:    Creating network interface "NICDB1-DA"
		info:    Looking up the network interface "NICDB1-DA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB1-DA
		data:    Name                            : NICDB1-DA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.4
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB1-RA"
		info:    Creating network interface "NICDB1-RA"
		info:    Looking up the network interface "NICDB1-RA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB1-RA
		data:    Name                            : NICDB1-RA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkSecurityGroups/NSG-RemoteAccess
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.54
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command vm create
		info:    Looking up the VM "DB1"
		info:    Using the VM Size "Standard_DS3"
		info:    The [OS, Data] Disk or image configuration requires storage account
		info:    Looking up the storage account wtestvnetstorageprm
		info:    Looking up the availability set "ASDB"
		info:    Found an Availability set "ASDB"
		info:    Looking up the NIC "NICDB1-DA"
		info:    Looking up the NIC "NICDB1-RA"
		info:    Creating VM "DB1"

2. 幾分鐘後，執行將會結束，且您將會看到其餘的輸出，如下所示。

		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB1"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk1-1.vhd
		info:    Updating VM "DB1"
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB1"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk1-2.vhd
		info:    Updating VM "DB1"
		info:    vm disk attach-new command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB2-DA"
		info:    Creating network interface "NICDB2-DA"
		info:    Looking up the network interface "NICDB2-DA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB2-DA
		data:    Name                            : NICDB2-DA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.5
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB2-RA"
		info:    Creating network interface "NICDB2-RA"
		info:    Looking up the network interface "NICDB2-RA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB2-RA
		data:    Name                            : NICDB2-RA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkSecurityGroups/NSG-RemoteAccess
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.55
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command vm create
		info:    Looking up the VM "DB2"
		info:    Using the VM Size "Standard_DS3"
		info:    The [OS, Data] Disk or image configuration requires storage account
		info:    Looking up the storage account wtestvnetstorageprm
		info:    Looking up the availability set "ASDB"
		info:    Found an Availability set "ASDB"
		info:    Looking up the NIC "NICDB2-DA"
		info:    Looking up the NIC "NICDB2-RA"
		info:    Creating VM "DB2"
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB2"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk2-1.vhd
		info:    Updating VM "DB2"
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB2"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk2-2.vhd
		info:    Updating VM "DB2"
		info:    vm disk attach-new command OK

<!---HONumber=AcomDC_0211_2016-->