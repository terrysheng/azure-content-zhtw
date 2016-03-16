<properties 
	pageTitle="企業營運應用程式第 4 階段 | Microsoft Azure" 
	description="在 Azure 企業營運應用程式第 4 階段中，建立網頁伺服器，並在此載入您的企業營運應用程式。" 
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
	ms.date="01/21/2016" 
	ms.author="josephd"/>

# 企業營運應用程式工作負載第 4 階段：設定 Web 伺服器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

在 Azure 基礎結構服務部署高可用性企業營運應用程式的這個階段中，您將建置 Web 伺服器，並在其中載入企業營運應用程式。

您必須先完成這個階段才能前往[第 5 階段](virtual-machines-workload-high-availability-LOB-application-phase5.md)。如需所有階段的相關資訊，請參閱[在 Azure 中部署高可用性的企業營運應用程式](virtual-machines-workload-high-availability-LOB-application-overview.md)。

## 在 Azure 中建立 Web 伺服器虛擬機器

Web 伺服器虛擬機器共有兩部，您可以在其中部署 ASP.NET 應用程式或可透過 Windows Server 2012 R2 中 Internet Information Services (IIS) 8 裝載舊版應用程式。

首先，您將設定內部負載平衡，讓 Azure 在兩部 Web 伺服器之間將用戶端流量平均分散到企業營運應用程式。這需要您指定內部負載平衡執行個體，其中包含名稱和從子網路位址空間 (指派至 Azure 虛擬網路) 所指派的專屬 IP 位址。

> [AZURE.NOTE] 下列命令集使用 Azure PowerShell 1.0 版和更新版本。如需詳細資訊，請參閱 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)。

指定變數的值，並移除 < and > 字元。請注意，下列 Azure PowerShell 命令集使用下表中的值：

- 資料表 M，適用於虛擬機器
- 資料表 V，適用於虛擬網路設定
- 資料表 S，適用於子網路
- 資料表 ST，適用於儲存體帳戶
- 資料表 A，適用於可用性設定組

回想您在[第 2 階段](virtual-machines-workload-high-availability-LOB-application-phase2.md)中所定義的資料表 M，以及在[第 1 階段](virtual-machines-workload-high-availability-LOB-application-phase1.md)中所定義的資料表 V、S、ST 和 A。

當您提供所有適當值後，在 Azure PowerShell 命令提示字元中執行結果區塊。

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureRMLoadBalancerFrontendIpConfig -Name WebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureRMLoadBalancerBackendAddressPoolConfig -Name WebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureRMLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureRMLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

接下來，新增一個內部 DNS 位址記錄至您組織的 DNS 基礎結構，此基礎結構可將企業營運應用程式完整網域名稱 (例如 lobapp.corp.contoso.com) 解析給指派至內部負載平衡器的 IP 位址 (先前 Azure PowerShell 命令區塊中 $privIP 的值)。

接下來，使用下列方塊中的 PowerShell 命令為上述兩個 Web 伺服器建立虛擬機器。

當您提供所有適當值後，在 Azure PowerShell 提示中執行結果區塊。

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$webLB=Get-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure"	
	
	# Use the standard storage account
	$saName="<Table ST – Item 2 – Storage account name column>"

	$vnetName="<Table V – Item 1 – Value column>"
	$beSubnetName="<Table S - Item 2 - Name column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureRMVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first web server virtual machine
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureRMAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second web server virtual machine
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] 因為這些虛擬機器用於內部網路應用程式，所以未獲指派公用 IP 位址或 DNS 網域名稱標籤，也不會公開到網際網路。不過，這也表示您無法從 Azure 入口網站連線到它們。當您檢視虛擬機器的屬性時，[連線] 按鈕將無法使用。

使用您選擇的遠端桌面用戶端，並建立每個 Web 伺服器虛擬機器的遠端桌面連接。使用其內部網路 DNS 或電腦名稱，以及本機系統管理員帳戶的認證。

接下來，對於每部 Web 伺服器虛擬機器，請在 Windows PowerShell 提示中使用下列命令，將其加入適當的 Active Directory 網域。

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

請注意，您必須在輸入 **Add-Computer** 命令後提供網域帳戶認證。

重新啟動之後，使用具有本機系統管理員權限的帳戶重新連接到網域。

接下來，為每個 Web 伺服器安裝並設定 IIS。

1. 執行伺服器管理員，然後按一下 [新增角色及功能]。
2. 在 [開始之前] 頁面中按 [下一步]。
3. 在 [選取安裝類型] 頁面上，按 [下一步]。
4. 在 [選取目的地伺服器] 頁面上，按 [下一步]。
5. 在 [伺服器角色] 頁面上，按一下 [角色] 清單中的 [網頁伺服器 (IIS)]。
6. 出現提示時，按一下 [新增功能]，然後按 [下一步]。
7. 在 [選取功能] 頁面上，按 [下一步]。
8. 在 [網頁伺服器 (IIS)] 頁面上，按 [下一步]。
9. 在 [選取角色服務] 頁面上，選取或清除 LOB 應用程式所需服務的核取方塊，然後按 [下一步]。10. 在 [確認安裝選項] 頁面上，按一下 [安裝]。

## 將您的企業營運應用程式部署於 Web 伺服器虛擬機器。

從內部部署網路上的電腦：

1.	將企業營運應用程式的檔案加入至兩部 Web 伺服器。
2.	在 SQL Server 叢集上建立企業營運應用程式的資料庫。
3.	測試對於企業營運應用程式及其功能的存取。

此圖表是成功完成此階段所產生的組態。

![](./media/virtual-machines-workload-high-availability-LOB-application-phase4/workload-lobapp-phase4.png)

## 下一步

- 依照[第 5 階段](virtual-machines-workload-high-availability-LOB-application-phase5.md)指示完成此工作負載的設定。

<!---HONumber=AcomDC_0128_2016-->