<properties 
   pageTitle="在傳統部署模型中使用 Azure CLI 部署多部 NIC VM | Microsoft Azure"
   description="了解如何在傳統部署模型中使用 Azure CLI 部署多部 NIC VM"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

#使用 Azure CLI 部署多個 NIC VM (傳統)

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-network-deploy-multinic-arm-cli.md)。

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

因為在這個時間點，在同一個雲端服務中不能有配備單一 NIC 的 VM 和配備多個 NIC 的 VM，所以在案例中，您要在和所有其他元件都不同的雲端服務中實作後端伺服器。下列步驟中，主要資源使用名為 *IaaSStory* 的雲端服務，後端伺服器使用 *IaaSStory-BackEnd*。

## 必要條件

在這個案例中，主要雲端服務必須先部署所有必要的資源，然後才可以部署後端伺服器。至少，您需要以後端的子網路建立虛擬網路。請瀏覽[使用 Azure CLI 建立虛擬網路](virtual-networks-create-vnet-classic-cli.md)，以了解如何部署虛擬網路。

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## 部署後端 VM

後端 VM 有賴於建立下列資源。

- **資料磁碟的儲存體帳戶**。為取得更佳的效能，資料庫伺服器上的資料磁碟會使用需要進階儲存體帳戶的固態硬碟 (SSD) 技術。請確定 Azure 的部署位置，以支援進階儲存體。
- **NIC**。每部 VM 都會有兩個 NIC，一個用於資料庫存取，另一個用於管理。
- **可用性設定組**。所有的資料庫伺服器都會加入單一的可用性設定組，確保在維護期間至少有一部 VM 啟動並執行。 

### 步驟 1：啟動指令碼

[這裡](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh)可以下載所使用的完整 Bash 指令碼。請遵循下列步驟來變更要在環境中工作的指令碼。

1. 根據上述[必要條件](#Prerequisites)中已部署的現有資源群組來變更下列變數的值。

		location="useast2"
		vnetName="WTestVNet"
		backendSubnetName="BackEnd"

2. 根據後端部署要使用的值，變更下列變數值。

		backendCSName="IaaSStory-Backend"
		prmStorageAccountName="iaasstoryprmstorage"
		image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
		avSetName="ASDB"
		vmSize="Standard_DS3"
		diskSize=127
		vmNamePrefix="DB"
		osDiskName="osdiskdb"
		dataDiskPrefix="db"
		dataDiskName="datadisk"
		ipAddressPrefix="192.168.2."
		username='adminuser'
		password='adminP@ssw0rd'
		numberOfVMs=2

### 步驟 2：為 VM 建立必要的資源

1. 為所有後端 VM 建立新的雲端服務。請注意，資源群組名稱的 `$backendCSName` 變數，以及 Azure 區域之 `$location` 的使用方式。

		azure service create --serviceName $backendCSName \
		    --location $location

2. 為您的 VM 要使用的作業系統和資料磁碟建立進階儲存體帳戶。

		azure storage account create $prmStorageAccountName \
		    --location $location \
		    --type PLRS 

### 步驟 3：建立具有多個 NIC 的 VM

1. 根據 `numberOfVMs` 變數，啟動迴圈以建立多部 VM。

		for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
		do

2. 對於每個 VM，請分別為這兩個 NIC 的個別指定名稱和 IP 位址。

		    nic1Name=$vmNamePrefix$suffixNumber-DA
		    x=$((suffixNumber+3))
		    ipAddress1=$ipAddressPrefix$x
		
		    nic2Name=$vmNamePrefix$suffixNumber-RA
		    x=$((suffixNumber+53))
		    ipAddress2=$ipAddressPrefix$x

4. 建立 VM。請注意使用 `--nic-config` 參數，其中包含具有名稱、子網路和 IP 位址的所有 NIC 清單。

		    azure vm create $backendCSName $image $username $password \
		        --connect $backendCSName \
		        --vm-name $vmNamePrefix$suffixNumber \
		        --vm-size $vmSize \
		        --availability-set $avSetName \
		        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
		        --virtual-network-name $vnetName \
		        --subnet-names $backendSubnetName \
		        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. 針對每個 VM，請建立兩個資料磁碟。

		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd
		
		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
		done

### 步驟 4：執行指令碼

現在您已根據需求下載並變更了指令碼，請執行指令碼來建立具有多個 NIC 的後端資料庫 VM。

1. 儲存您的指令碼並從 **Bash** 終端機執行。您會看到初始的輸出，如下所示。

		info:    Executing command service create
		info:    Creating cloud service
		data:    Cloud service name IaaSStory-Backend
		info:    service create command OK
		info:    Executing command storage account create
		info:    Creating storage account
		info:    storage account create command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM

2. 幾分鐘後，執行將會結束，且您將會看到其餘的輸出，如下所示。

		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM
		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK

<!---HONumber=AcomDC_0211_2016-->