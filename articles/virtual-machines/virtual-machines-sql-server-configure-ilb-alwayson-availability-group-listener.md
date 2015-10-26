<properties 
	pageTitle="設定 AlwaysOn 可用性群組的 ILB 接聽程式 | Microsoft Azure"
	description="本教學課程使用以傳統部署模型建立的資源，並使用內部負載平衡器 (ILB) 在 Azure 中建立 AlwaysOn 可用性群組接聽程式。"
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" 
	tags="azure-service-management"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="09/16/2015"
	ms.author="jroth" />

# 設定 Azure 中 AlwaysOn 可用性群組的 ILB 接聽程式

> [AZURE.SELECTOR]
- [Internal Listener](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [External Listener](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)

## 概觀

本主題說明如何使用**內部負載平衡器 (ILB)** 來設定 AlwaysOn 可用性群組的接聽程式。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。
 

您的可用性群組可包含的複本為僅限內部部署、僅限 Azure，或同時跨內部部署和 Azure 的混合式組態。Azure 複本可位於相同區域內，或使用多個虛擬網路 (VNet) 跨多個區域。下列步驟假設您已[設定可用性群組](virtual-machines-sql-server-alwayson-availability-groups-gui.md)，但尚未設定接聽程式。

請注意，在使用 ILB 的 Azure 中，可用性群組接聽程式具有下列限制：

- 可用性群組接聽程式支援 Windows Server 2008 R2、Windows Server 2012 和 Windows Server 2012 R2。

- 用戶端應用程式必須與包含可用性群組 VM 的雲端服務位於不同雲端服務上。Azure 在相同的雲端服務中不支援伺服器直接回傳搭配用戶端和伺服器使用。

- 每個雲端服務僅支援一個可用性群組接聽程式，因為接聽程式設定為使用雲端服務 VIP 位址或內部負載平衡器的 VIP 位址。請注意，雖然 Azure 現在支援在指定的雲端服務中建立多個 VIP 位址中，這項限制仍為有效。

## 判斷接聽程式的協助工具

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

本文著重於建立使用**內部負載平衡器 (ILB)** 的接聽程式。如果您需要公用/外部接聽程式，請參閱本文中的版本，其中提供設定[外部接聽程式](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)的步驟。

## 使用伺服器直接回傳建立負載平衡 VM 端點

對於 ILB，您必須先建立內部負載平衡器。此動作可使用下方的指令碼來完成。

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. 對於 **ILB**，您應該指派靜態 IP 位址。首先，執行下列命令來檢查目前的 VNet 組態：

		(Get-AzureVNetConfig).XMLConfiguration

1. 請記下子網路 (其中包含主控複本的 VM) 的 **Subnet** 名稱。這將用於指令碼中的 **$SubnetName** 參數。

1. 然後記下子網路 (其中包含主控複本的 VM) 的 **VirtualNetworkSite** 名稱和起始的 **AddressPrefix**。將這兩個值傳遞至 **Test-AzureStaticVNetIP** 命令並檢查 **AvailableAddresses** 以尋找可用的 IP 位址。例如，如果 VNet 經命名為 *MyVNet* 且具有起始於 *172.16.0.128* 的子網路位址範圍，則下列命令會列出可用的位址：

		(Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. 選擇其中一個可用的位址，並將其用於下列指令碼中的 **$ILBStaticIP** 參數。

3. 將下方的 PowerShell 指令碼複製到文字編輯器，並設定變數值以符合您的環境 (請注意，某些參數已提供預設值)。請注意，使用同質群組的現有部署無法新增 ILB。如需 ILB 需求的詳細資訊，請參閱[內部負載平衡器](../load-balancer/load-balancer-internal-overview.md)。此外，如果您的可用性群組跨越 Azure 區域，您必須針對雲端服務和位於該資料中心的節點，在每個資料中心執行一次指令碼。

		# Define variables
		$ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
		$AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
		$SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
		$ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
		$ILBName = "AGListenerLB" # customize the ILB name or use this default value
		
		# Create the ILB
		Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP
		
		# Configure a load balanced endpoint for each node in $AGNodes using ILB
		ForEach ($node in $AGNodes)
		{
			Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM 
		}

1. 一旦您已設定變數，請從文字編輯器將指令碼複製到您的 Azure PowerShell 工作階段來執行它。如果提示依然顯示「>>」，請再次按 ENTER 鍵以確定指令碼開始執行。注意

>[AZURE.NOTE]Azure 管理入口網站目前不支援內部負載平衡器，因此您不會看到 ILB 或入口網站中的端點。不過，如果負載平衡器正在其中執行，則 **Get-AzureEndpoint** 會傳回內部 IP 位址。否則，它會傳回 null。

## 必要時，請確認已安裝 KB2854082

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## 在可用性群組節點中開啟防火牆連接埠

[AZURE.INCLUDE [防火牆](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## 建立可用性群組接聽程式

[AZURE.INCLUDE [防火牆](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. 對於 ILB，您必須使用之前所建立內部負載平衡器 (ILB) 的 IP 位址。使用下列指令碼來取得 PowerShell 中的這個 IP 位址。

		# Define variables
		$ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
		(Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. 在其中一個 VM 上，將下方的 PowerShell 指令碼複製到文字編輯器，並將變數設定為之前記下的值。

		# Define variables
		$ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name 
		$ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)
		
		Import-Module FailoverClusters
		
		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code. 
		
		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

1. 設定變數之後，開啟提升權限的 Windows PowerShell 視窗中，然後從文字編輯器將指令碼複製並貼到您的 Azure PowerShell 工作階段來執行它。如果提示依然顯示「>>」，請再次按 ENTER 鍵以確定指令碼開始執行。

2. 在每個 VM 上重複此步驟。此指令碼會使用雲端服務的 IP 位址來設定 IP 位址資源，並設定類似探查連接埠的其他參數。當 IP 位址資源處於線上時，它會從本教學課程中稍早所建立的負載平衡端點，接著回應探查連接埠上的輪詢。

## 使接聽程式上線

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## 待處理項目

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## 測試可用性群組接聽程式 (位於相同的 VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## 後續步驟

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!---HONumber=Oct15_HO3-->