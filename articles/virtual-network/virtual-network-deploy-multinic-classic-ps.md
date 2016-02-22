<properties 
   pageTitle="在傳統部署模型中使用 PowerShell 部署多部 NIC VM |Microsoft Azure"
   description="了解如何在傳統部署模型中使用 PowerShell 部署多部 NIC VM"
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

#使用 PowerShell 部署多部 NIC VM (傳統)

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [資源管理員模型](virtual-network-deploy-multinic-arm-ps.md)。

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

因為在這個時間點，在同一個雲端服務中不能有配備單一 NIC 的 VM 和配備多個 NIC 的 VM，所以在案例中，您要在和所有其他元件都不同的雲端服務中實作後端伺服器。下列步驟中，主要資源使用名為 *IaaSStory* 的雲端服務，後端伺服器使用 *IaaSStory-BackEnd* 。

## 必要條件

在這個案例中，主要雲端服務必須先部署所有必要的資源，然後才可以部署後端伺服器。至少，您需要以後端的子網路建立虛擬網路。請瀏覽[使用 PowerShell 建立虛擬網路](virtual-networks-create-vnet-classic-netcfg-ps.md)，以了解如何部署虛擬網路。

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## 部署後端 VM

後端 VM 有賴於建立下列資源。

- **後端子網路**。資料庫伺服器會是另外的子網路的一部分，以隔離流量。下面的指令碼需要這個子網路位在名為 *WTestVnet* 的 vnet 中。
- **資料磁碟的儲存體帳戶**。為取得更佳的效能，資料庫伺服器上的資料磁碟會使用需要進階儲存體帳戶的固態硬碟 (SSD) 技術。請確定 Azure 的部署位置，以支援進階儲存體。
- **可用性設定組**。所有的資料庫伺服器都會加入單一的可用性設定組，確保在維護期間至少有一部 VM 啟動並執行。 

### 步驟 1：啟動指令碼

[這裡](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1)可以下載所使用之完整的 PowerShell 指令碼。請遵循下列步驟來變更指令碼來讓指令碼在環境中運作。

1. 根據上述[必要條件](#Prerequisites)中已部署的現有資源群組來變更下列變數的值。

		$location              = "West US"
		$vnetName              = "WTestVNet"
		$backendSubnetName     = "BackEnd"

2. 根據後端部署要使用的值，變更下列變數值。

		$backendCSName         = "IaaSStory-Backend"
		$prmStorageAccountName = "iaasstoryprmstorage"
		$avSetName             = "ASDB"
		$vmSize                = "Standard_DS3"
		$diskSize              = 127
		$vmNamePrefix          = "DB"
		$dataDiskSuffix        = "datadisk"
		$ipAddressPrefix       = "192.168.2."
		$numberOfVMs           = 2

### 步驟 2：為 VM 建立必要的資源

您需要為所有的 VM 建立新的雲端服務和資料磁碟儲存體帳戶。您也需要指定影像及 VM 的本機系統管理員帳戶。若要建立這些資源，請執行下列步驟。

1. 建立新的雲端服務。

		New-AzureService -ServiceName $backendCSName -Location $location

2. 建立新的進階儲存體帳戶。

		New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
		    -Location $location `
		    -Type Premium_LRS

3. 設定前文中建立的儲存體帳戶，做為訂用帳戶的目前儲存體帳戶。

		$subscription = Get-AzureSubscription `
		    | where {$_.IsCurrent -eq $true}  
		Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
		    -CurrentStorageAccountName $prmStorageAccountName

4. 選取 VM 影像。

		$image = Get-AzureVMImage `
		    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
		    | sort PublishedDate -Descending `
		    | select -ExpandProperty ImageName -First 1

5. 設定本機系統管理員帳戶認證。

		$cred = Get-Credential -Message "Enter username and password for local admin account"

### 步驟 3：建立 VM

您需要使用迴圈建立所需數量的 VM，並在迴圈中建立必要的 NIC 和 VM。若要建立 NIC 和 VM，請執行下列步驟。

1. 啟動 `for` 迴圈，根據 `$numberOfVMs` 變數值，視需要的次數重複命令來建立一部 VM 和兩個 NIC。

		for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. 建立指定 VM 影像、大小和可用性設定組的 `VMConfig` 物件。

		    $vmName = $vmNamePrefix + $suffixNumber
		    $vmConfig = New-AzureVMConfig -Name $vmName `
		                    -ImageName $image `
		                    -InstanceSize $vmSize `
		                    -AvailabilitySetName $avSetName  

3. 將 VM 佈建為 Windows VM。

		    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
		        -AdminUsername $cred.UserName `
		        -Password $cred.Password

4. 設定預設 NIC，並指派它一個靜態 IP 位址。

		    Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
		    Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. 為每部 VM 加入第二個 NIC。

		    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
		        -SubnetName $backendSubnetName `
		        -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
		        -VM $vmConfig 

6. 為每部 VM 建立資料磁碟。

		    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk1Name `
		        -LUN 0       
		
		    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk2Name `
		        -LUN 1

7. 建立每部 VM 並結束迴圈。

		    New-AzureVM -VM $vmConfig `
		        -ServiceName $backendCSName `
		        -Location $location `
		        -VNetName $vnetName
		}

### 步驟 4：執行指令碼

既然您已根據需求下載並變更指令碼，請執行指令碼來建立有多個 NIC 的後端資料庫 VM。

1. 儲存您的指令碼，並從 **PowerShell** 命令提示字元或 **PowerShell ISE** 執行。您會看到初始的輸出，如下所示。

		OperationDescription    OperationId                          OperationStatus
		--------------------    -----------                          ---------------
		New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
		New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
		                                                                            
		WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. 填寫認證提示中所需的資訊，並按一下 [確定]。即會顯示下列輸出。

		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded 

<!---HONumber=AcomDC_0211_2016-->