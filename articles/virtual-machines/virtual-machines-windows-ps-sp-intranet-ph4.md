<properties
	pageTitle="SharePoint Server 2013 伺服器陣列第 4 階段 | Microsoft Azure"
	description="在 Azure SharePoint Server 2013 伺服器陣列第 4 階段中，建立 SharePoint 伺服器虛擬機器和新 SharePoint 伺服器陣列。"
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="josephd"/>

# SharePoint 內部網路伺服器陣列工作負載第 4 階段：設定 SharePoint Server。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

在 Azure 基礎結構服務內，使用 SQL Server AlwaysOn 可用性群組部署內部網路專用的 SharePoint 2013 伺服器陣列的這個階段中，您需要建立 SharePoint 伺服器陣列的應用程式和 Web 階層，並使用 SharePoint 設定精靈建立伺服器陣列。

您必須先完成這個階段才能前往[第 5 階段](virtual-machines-windows-ps-sp-intranet-ph5.md)。如需所有階段的詳細資訊，請參閱[在 Azure 中使用 SQL Server AlwaysOn 可用性群組部署 SharePoint](virtual-machines-windows-sp-intranet-overview.md)。

## 在 Azure 中建立 SharePoint 伺服器虛擬機器

SharePoint Server 虛擬機器共有四部。其中兩個 SharePoint Server 虛擬機器用於前端 Web 伺服器，另外兩個用於管理和主控 SharePoint 應用程式。每一層中有兩部 SharePoint Server，可提供高可用性。

首先，您將設定內部負載平衡，讓 Azure 平均分散兩部前端 Web 伺服器的用戶端流量。這需要您指定內部負載平衡執行個體，其中包含名稱和從子網路位址空間 (指派至 Azure 虛擬網路) 取得的專屬 IP 位址。

> [AZURE.NOTE] 下列命令集使用 Azure PowerShell 1.0 版和較新版本。如需詳細資訊，請參閱 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)。

指定變數的值，並移除 < and > 字元。請注意，下列 Azure PowerShell 命令集使用下表中的值：

- 資料表 M，適用於虛擬機器
- 資料表 V，適用於虛擬網路設定
- 資料表 S，適用於子網路
- 資料表 ST，適用於儲存體帳戶
- 資料表 A，適用於可用性設定組

回想您在[第 2 階段：設定網域控制站](virtual-machines-windows-ps-sp-intranet-ph2.md)中定義的資料表 M，以及在[第 1 階段：設定 Azure ](virtual-machines-windows-ps-sp-intranet-ph1.md)中定義的資料表 V、S、ST 和 A。

當您提供所有適當值後，在 Azure PowerShell 命令提示字元中執行結果區塊。

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureRMLoadBalancerFrontendIpConfig -Name SharePointWebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureRMLoadBalancerBackendAddressPoolConfig -Name SharePointWebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureRMLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureRMLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

接下來，新增一個 DNS 位址記錄至您組織的內部 DNS 基礎結構，此基礎結構可將 SharePoint 伺服器陣列完整網域名稱 (例如 spfarm.corp.contoso.com) 解析給指派給內部負載平衡器的 IP 位址 (先前 Azure PowerShell 命令區塊中 $privIP 的值)。

使用下列的 Azure PowerShell 命令區塊為上述四個 SharePoint Server 建立虛擬機器。當您提供所有適當值後，在 Azure PowerShell 命令提示字元中執行結果區塊。

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 2 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	
	# Create the first application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Create the second application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Change the availability set
	$avName="<Table A – Item 4 – Availability set name column>"

	# Set up key variables
	$beSubnetName="<Table S - Item 2 - Name column>"
	$webLB=Get-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure"	
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureRMVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first front end web server virtual machine
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureRMAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second front end web server virtual machine
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] 因為這些虛擬機器用於內部網路應用程式，所以未獲指派公用 IP 位址或 DNS 網域名稱標籤，也不會公開到網際網路。不過，這也表示您無法從 Azure 入口網站連線到它們。當您檢視虛擬機器的屬性時，[連接] 按鈕將無法使用。

使用您選擇的遠端桌面用戶端，並建立每個虛擬機器的遠端桌面連接。使用其內部網路 DNS 或電腦名稱，以及本機系統管理員帳戶的認證。

接下來，對於每部虛擬機器，請在 Windows PowerShell 提示中使用下列命令，將其加入適當的 Active Directory 網域。

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

請注意，您必須在輸入 **Add-Computer** 命令後提供網域帳戶認證。

它們啟動之後，使用[使用遠端桌面連線程序登入虛擬機器](virtual-machines-windows-ps-sp-intranet-ph2.md#logon)四次 (每一部 SharePoint Server 一次)，以使用 [網域]\\sp\_farm\_db 帳戶認證登入。您已在[第 2 階段：設定網域控制站](virtual-machines-windows-ps-sp-intranet-ph2.md)中建立了這些認證。

使用[測試連線能力程序](virtual-machines-windows-ps-sp-intranet-ph2.md#testconn)四次，分別為四部 SharePoint Server 測試組織網路中各個位置的連線能力。

> [AZURE.NOTE] 從 SharePoint Server 2013 試用版映像建立 SharePoint Server。您需要將安裝轉換為使用適用於 SharePoint Server 2013 Standard 或 Enterprise 版本的零售或大量授權金鑰。

## 設定 SharePoint 伺服器陣列

使用下列步驟來設定伺服器陣列中的第一部 SharePoint Server：

1.	在第一部 SharePoint 應用程式伺服器桌面上，按兩下 **SharePoint 2013 產品設定精靈**。當系統詢問您是否允許程式變更電腦時，按一下 [是]。
2.	在 [歡迎使用 SharePoint 產品] 頁面中，按 [下一步]。
3.	**SharePoint 2013 產品設定精靈**對話方塊隨即出現，警告將重新啟動或重設服務 (例如 IIS)。按一下 [是]。
4.	在 [連線至伺服器陣列] 頁面中，選取 [建立新的伺服器陣列]，然後按 [下一步]。
5.	在 [指定設定資料庫設定] 頁面中：
 - 在 [資料庫伺服器] 中，輸入主要資料庫伺服器的名稱。
 - 在 [使用者名稱] 中，輸入 [網域]**\\sp\_farm\_db** (已在[第 2 階段：設定網域控制站](virtual-machines-windows-ps-sp-intranet-ph2.md)中建立)。還記得 sp\_farm\_db 帳戶具有資料庫伺服器的系統管理員權限。
 - 在 [**密碼**] 中輸入 sp\_farm\_db 帳戶密碼。
6.	按 [下一步]。
7.	在 [指定伺服器陣列安全性設定] 頁面中，輸入複雜密碼兩次。記下複雜密碼並儲存在安全的位置，以供日後參考。按 [下一步]。
8.	在 [設定 SharePoint 管理中心 Web 應用程式] 頁面中，按 [下一步]。
9.	[完成 SharePoint 產品設定精靈] 頁面隨即出現。按 [下一步]。
10.	[設定 SharePoint 產品] 頁面隨即出現。等候組態程序完成，約 8 分鐘。
11.	成功設定伺服器陣列後，按一下 [**完成**] 按鈕。 新的管理網站隨即啟動。
12.	若要開始設定 SharePoint 伺服器陣列，請按一下 [**啟動精靈**]。

在第二部 SharePoint 應用程式伺服器和兩部前端 Web 伺服器上執行下列程序：

1.	從桌面按兩下 [**SharePoint 2013 產品組態精靈**]。當系統詢問您是否允許程式變更電腦時，按一下 [是]。
2.	在 [歡迎使用 SharePoint 產品] 頁面中，按 [下一步]。
3.	**SharePoint 2013 產品設定精靈**對話方塊隨即出現，警告將重新啟動或重設服務 (例如 IIS)。按一下 [是]。
4.	在 [連線至伺服器陣列] 頁面中，按一下 [連線至現有的伺服器陣列]，然後按 [下一步]。
5.	在 [指定設定資料庫設定] 頁面的 [資料庫伺服器] 中，輸入主要資料庫伺服器的名稱，然後按一下 [擷取資料庫名稱]。
6.	在資料庫名稱清單中按一下 [SharePoint\_Config]，然後按 [下一步]。
7.	在 [指定伺服器陣列安全性設定] 頁面中，輸入先前程序所設定的複雜密碼。按 [下一步]。
8.	[完成 SharePoint 產品設定精靈] 頁面隨即出現。按 [下一步]。
9.	在 [設定成功] 頁面中，按一下 [完成]。

SharePoint 建立伺服器陣列後，會在主要 SQL Server 虛擬機器中設定一組伺服器登入。SQL Server 2012 引入了自主資料庫使用者密碼的概念。自主資料庫本身會儲存所有資料庫中繼資料和使用者資訊，因此此資料庫中定義的使用者不需具有對應的登入。可用性群組會複製此資料庫的資訊，容錯移轉之後仍可使用。如需詳細資訊，請參閱[自主資料庫](http://go.microsoft.com/fwlink/p/?LinkId=262794)。

不過根據預設，SharePoint 資料庫並非自主資料庫。因此您必須手動設定次要資料庫伺服器，使其與主要資料庫伺服器具備同樣的 SharePoint 伺服器陣列登入設定。您可以同時與兩部伺服器連線，從 SQL Server Management Studio 執行此同步設定。

完成此初始設定後，便可使用更多 SharePoint 伺服器陣列功能的組態選項。如需詳細資訊，請參閱[計劃在 Azure 基礎結構服務上部署 SharePoint 2013](http://msdn.microsoft.com/library/dn275958.aspx)。

這是成功完成此階段所產生的組態。

![](./media/virtual-machines-windows-ps-sp-intranet-ph4/workload-spsqlao_04.png)

## 後續步驟

- 依照[第 5 階段](virtual-machines-windows-ps-sp-intranet-ph5.md)指示繼續此工作負載的設定。

<!---HONumber=AcomDC_0323_2016-->