<properties 
	pageTitle="企業營運應用程式第 2 階段 | Microsoft Azure" 
	description="在 Azure 企業營運應用程式第 2 階段中，建立並設定兩個 Active Directory 複本網域控制站。" 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015" 
	ms.author="josephd"/>

# 企業營運應用程式工作負載第 2 階段：設定網域控制站

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。
 

在 Azure 基礎結構服務部署高可用性企業營運應用程式的這個階段中，您需要在 Azure 虛擬網路中設定兩個複本網域控制站，才能在 Azure 虛擬網路中本機驗證 Web 資源的用戶端 Web 要求，而不需跨越連接將驗證流量傳送至您的內部部署網路。

您必須先完成這個階段才能前往[第 3 階段](virtual-machines-workload-high-availability-LOB-application-phase3.md)。如需所有階段的相關資訊，請參閱[在 Azure 中部署高可用性的企業營運系統應用程式](virtual-machines-workload-high-availability-LOB-application-overview.md)。

## 在 Azure 中建立網域控制站虛擬機器

首先，您必須填寫資料表 M 的 [**虛擬機器名稱**] 資料行，並視需要在 [**最小大小**] 資料行中修改虛擬機器大小。

項目 | 虛擬機器名稱 | 資源庫映像 | 最小大小 
--- | --- | --- | --- 
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (第一個網域控制站，範例 DC1) | Windows Server 2012 R2 Datacenter | 標準\_D2
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (第二個網域控制站，範例 DC2) | Windows Server 2012 R2 Datacenter | 標準\_D2
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (主要資料庫伺服器，範例 SQL1) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | 	Standard\_DS4
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (次要資料庫伺服器，範例 SQL2) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | 	Standard\_DS4
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (叢集多數節點，範例 MN1) | Windows Server 2012 R2 Datacenter | 標準\_D1
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (第一部 Web 伺服器，範例 WEB1) | Windows Server 2012 R2 Datacenter | 標準\_D3
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (第二部 Web 伺服器，範例 WEB2) | Windows Server 2012 R2 Datacenter | 標準\_D3

**資料表 M – Azure 中適用於高可用性企業營運應用程式的虛擬機器**

如需虛擬機器大小的完整清單，請參閱[虛擬機器的大小](virtual-machines-size-specs.md)。

使用下列的 Azure PowerShell 命令區塊為上述兩個網域控制站建立虛擬機器。指定變數的值，並移除 < and > 字元。請注意，此 PowerShell 命令區塊使用下列的值：

- 資料表 M，適用於虛擬機器
- 資料表 V，適用於虛擬網路設定
- 資料表 S，適用於子網路
- 資料表 ST，適用於儲存體帳戶
- 資料表 A，適用於可用性設定組

回想您在[第 1 階段：設定 Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md) 中所定義的資料表 V、S、ST 和 A。

> [AZURE.NOTE]下列命令集使用 Azure PowerShell 1.0 版和更新版本。如需詳細資訊，請參閱 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)。

當您提供所有適當值後，在 Azure PowerShell 提示中執行結果區塊。

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 2 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 1 – Availability set name column>"
	
	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column>"
	$staticIP="<Table V – Item 6 - Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first domain controller." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column>"
	$staticIP="<Table V – Item 7 - Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second domain controller." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE]因為這些虛擬機器用於內部網路應用程式，所以未獲指派公用 IP 位址或 DNS 網域名稱標籤，也不會公開到網際網路。不過，這也表示您無法從 Azure 入口網站連線到它們。當您檢視虛擬機器的屬性時，[連接] 按鈕將無法使用。使用「遠端桌面連線」配件或其他遠端桌面工具，來使用虛擬機器的私人 IP 位址或內部網路 DNS 名稱連接該虛擬機器。

## 設定第一個網域控制站

使用您選擇的遠端桌面用戶端，並建立第一個網域控制站虛擬機器的遠端桌面連接。使用其內部網路 DNS 或電腦名稱，以及本機系統管理員帳戶的認證。

接下来，您需要在第一個網域控制站新增額外的資料磁碟。

### <a id="datadisk"></a>初始化空磁碟

1.	在 [伺服器管理員] 的左窗格中，按一下 [檔案和存放服務]，然後按一下 [磁碟]。
2.	在 [內容] 窗格的 [**磁碟**] 群組中，按一下 [磁碟 **2**] ([**磁碟分割**] 設為 [**未知**])。
3.	按一下 [工作]，然後按一下 [新增磁碟區]。
4.	在 [新增磁碟區精靈] 的 [在您開始前] 頁面上，按 [下一步]。
5.	在 [選取伺服器和磁碟] 頁面上，按一下 [磁碟 2]，然後按 [下一步]。出現提示時，按一下 [確定]。
6.	在 [指定磁碟區大小] 頁面上，按 [下一步]。
7.	在 [指派成磁碟機代號或資料夾] 頁面上，按 [下一步]。
8.	在 [選取檔案系統設定] 頁面上，按 [下一步]。
9.	在 [確認選取項目] 頁面上，按一下 [建立]。
10.	完成時，按一下 [關閉]。

接下來，測試第一個網域控制站對組織網路中各個位置的連線能力。

### <a id="testconn"></a>測試連線能力

1.	從桌面開啟 Windows PowerShell 提示。
2.	使用 **ping** 命令來偵測組織網路中各個資源的名稱和 IP 位址。

此程序可確保 DNS 名稱解析正常運作 (即虛擬機器已使用內部部署 DNS 伺服器正確設定)，且此封包可在跨單位虛擬網路內往來傳送。如果這個基本測試失敗，請連絡您的 IT 部門以疑難排解 DNS 名稱解析和封包傳遞問題。

接下來，從第一個網域控制站的 Windows PowerShell 命令提示字元執行下列命令：

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

系統將提示您提供網域系統管理員帳戶的認證。電腦會重新啟動。

## 設定第二個網域控制站

使用您選擇的遠端桌面用戶端，並建立第二個網域控制站虛擬機器的遠端桌面連接。使用其內部網路 DNS 或電腦名稱，以及本機系統管理員帳戶的認證。

接下来，您需要在第二個網域控制站新增額外的資料磁碟。請參閱[初始化空磁碟程序](#datadisk)。

接下來，從第二個網域控制站的 Windows PowerShell 提示執行下列命令：

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

系統將提示您提供網域系統管理員帳戶的認證。電腦會重新啟動。

接下來，您需要為您的虛擬網路更新 DNS 伺服器，讓 Azure 將兩個新網域控制站的 IP 位址指派給虛擬機器，做為他們的 DNS 伺服器。請注意，此程序會使用資料表 V (適用於虛擬網路設定) 和資料表 M (適用於虛擬機器) 的值。

1.	在 Azure 入口網站的左窗格中按一下 [**虛擬網路**]，然後按一下您虛擬網路的名稱 (資料表 V – 項目 1 – 值資料行)。
2.	在 [設定] 窗格上，按一下 [DNS 伺服器]。
3.	在 [DNS 伺服器] 窗格上，輸入下列項目：
	- 針對**主要 DNS 伺服器**：資料表 V – 項目 6 – 值資料行
	- 針對**次要 DNS 伺服器**：資料表 V – 項目 7 – 值資料行
4.	在 Azure 入口網站的左窗格中，按一下 [**虛擬機器**]。
5.	在 [虛擬機器] 窗格中，按一下第一個網域控制站 (資料表 M – 項目 1 - 虛擬機器名稱資料行) 的名稱。
6.	在虛擬機器的窗格中，按一下 [重新啟動]。
7.	重新啟動第一個網域控制站時，在 [虛擬機器] 窗格上，按一下第二個網域控制站 (資料表 M – 項目 2 - 虛擬機器名稱資料行) 的名稱。
8.	在虛擬機器的窗格中，按一下 [重新啟動]。等待第二個網域控制站啟動。

請注意，重新啟動兩個網域控制站後，他們才不會由內部部署 DNS 伺服器設定做為 DNS 伺服器。由於他們本身都是 DNS 伺服器，因此當升級為網域控制站時，它們會自動由內部部署 DNS 伺服器設定做為 DNS 轉寄站。

接下來，我們需要建立 Active Directory 複寫站台，以確保 Azure 虛擬網路中的伺服器使用本機網域控制站。使用網域系統管理員帳戶登入主要網域控制站，自系統管理員層級 Windows PowerShell 提示執行下列命令：

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet 
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

接下來，新增使用者帳戶以管理 SQL Server 虛擬機器。

	New-ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

此圖顯示已成功完成此階段設定，並以電腦名稱預留位置表示。

![](./media/virtual-machines-workload-high-availability-LOB-application-phase2/workload-lobapp-phase2.png)

## 後續步驟

- 依照[第 3 階段](virtual-machines-workload-high-availability-LOB-application-phase3.md)指示繼續此工作負載的設定。

<!---HONumber=AcomDC_1217_2015-->