<properties
	pageTitle="設定 AlwaysOn 可用性群組的外部接聽程式 | Microsoft Azure"
	description="本教學課程將逐步引導您完成建立 Azure 中 AlwaysOn 可用性群組接聽程式的步驟，並且可使用相關聯雲端服務的公用虛擬 IP 位址從外部存取。"
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth" />

# 設定 Azure 中 AlwaysOn 可用性群組的外部接聽程式

> [AZURE.SELECTOR]
- [Internal Listener](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [External Listener](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)

本主題說明如何設定 AlwaysOn 可用性群組的接聽程式，並且能夠在網際網路上從外部存取。如此可將雲端服務的**公用虛擬 IP (VIP)** 位址與接聽程式建立關聯。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。


您的可用性群組可包含的複本為僅限內部部署、僅限 Azure，或同時跨內部部署和 Azure 的混合式組態。Azure 複本可位於相同區域內，或使用多個虛擬網路 (VNet) 跨多個區域。下列步驟假設您已[設定可用性群組](virtual-machines-sql-server-alwayson-availability-groups-gui.md)，但尚未設定接聽程式。

## 外部接聽程式的指導方針和限制

請注意下面在使用雲端服務公用 VIP 位址部署時，有關 Azure 中可用性群組接聽程式的指導方針：

- 可用性群組接聽程式支援 Windows Server 2008 R2、Windows Server 2012 和 Windows Server 2012 R2。

- 用戶端應用程式必須與包含可用性群組 VM 的雲端服務位於不同雲端服務上。Azure 在相同的雲端服務中不支援伺服器直接回傳搭配用戶端和伺服器使用。

- 根據預設，本文中的步驟示範了如何設定一個接聽程式使用雲端服務的虛擬 IP (VIP) 位址；不過，您可以為雲端服務保留並建立多個 VIP 位址。這可讓您利用本文的步驟建立多個個別與不同 VIP 相關聯的接聽程式。如需有關如何建立多個 VIP 位址的資訊，請參閱[每一雲端服務可有多個 VIP](load-balancer-multivip.md)。

- 如果您要建立混合式環境的接聽程式，內部部署網路必須能夠連線到公用網際網路，以及使用 Azure 虛擬網路的站對站 VPN。位於 Azure 子網路時，僅能透過相應雲端服務的公用 IP 位址才能連線至可用性群組接聽程式。

- 有外部接聽程式也使用內部負載平衡器 (ILB) 時，無法在同一個雲端服務中建立外部接聽程式。

## 判斷接聽程式的存取性

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

本文著重於建立使用**外部負載平衡**的接聽程式。如果您想要虛擬網路專屬的接聽程式，請參閱本文提供設定[包含 ILB 之接聽程式](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)步驟的版本。

## 使用伺服器直接回傳建立負載平衡 VM 端點

外部負載平衡會使用主控 VM 之雲端服務的虛擬和公用虛擬 IP 位址。因此在此情況下，您不需要建立或設定負載平衡器。

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. 將下方的 PowerShell 指令碼複製到文字編輯器，並設定變數值以符合您的環境 (某些參數已提供預設值)。請注意，如果您的可用性群組跨越 Azure 區域，您必須針對雲端服務和位於該資料中心的節點，在每個資料中心執行一次指令碼。

		# Define variables
		$ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
		$AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas

		# Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
		ForEach ($node in $AGNodes)
		{
		    Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
		}

1. 一旦您已設定變數，請從文字編輯器將指令碼複製到您的 Azure PowerShell 工作階段來執行它。如果提示依然顯示「>>」，請再次按 ENTER 鍵以確定指令碼開始執行。

## 必要時，請確認已安裝 KB2854082

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## 在可用性群組節點中開啟防火牆連接埠

[AZURE.INCLUDE [防火牆](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## 建立可用性群組接聽程式

[AZURE.INCLUDE [防火牆](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. 對於外部負載平衡，您必須取得包含複本之雲端服務的公用虛擬 IP 位址。登入 Azure 傳統入口網站。巡覽至包含可用性群組 VM 的雲端服務。開啟**儀表板**檢視。

3. 記下 [**公用虛擬 IP (VIP) 位址**] 下方顯示的位址。如果您的解決方案跨越多個 VNet，請針對包含主控複本之 VM 的每個雲端服務重複此步驟。

4. 在其中一個 VM 上，將下方的 PowerShell 指令碼複製到文字編輯器，並將變數設定為之前記下的值。

		# Define variables
		$ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name
		$CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service

		Import-Module FailoverClusters

		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. 設定變數之後，開啟提升權限的 Windows PowerShell 視窗中，然後從文字編輯器將指令碼複製並貼到您的 Azure PowerShell 工作階段來執行它。如果提示依然顯示「>>」，請再次按 ENTER 鍵以確定指令碼開始執行。

1. 在每個 VM 上重複此步驟。此指令碼會使用雲端服務的 IP 位址來設定 IP 位址資源，並設定類似探查連接埠的其他參數。當 IP 位址資源處於線上時，它會從本教學課程中稍早所建立的負載平衡端點，接著回應探查連接埠上的輪詢。

## 使接聽程式上線

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## 待處理項目

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## 測試可用性群組接聽程式 (位於相同的 VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## 測試可用性群組接聽程式 (位於網際網路)

若要從虛擬網路外部存取接聽程式，您必須使用外部/公用負載平衡 (如本主題中所述) 而非 ILB，因為 ILB 僅能在相同的 VNet 中進行存取。在連接字串中，您將指定雲端服務名稱。例如，如果您雲端服務的名稱為 *mycloudservice*，sqlcmd 陳述式便如下所示：

	sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

不同於先前範例，必須使用 SQL 驗證，因為呼叫端無法透過網際網路使用 Windows 驗證。如需詳細資訊，請參閱 [Azure VM 中的 AlwaysOn 可用性群組：用戶端連線能力情況](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx)。使用 SQL 驗證時，請確定您在兩個複本上建立相同的登入。如需有關使用可用性群組疑難排解登入的詳細資訊，請參閱[如何對應登入或使用包含的 SQL Database 使用者以連線到其他複本並對應到可用性資料庫](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx)。

如果 AlwaysOn 複本位於其他子網路中，用戶端必須在連接字串中指定 **MultisubnetFailover=True**。這會導致對於不同子網路中的複本進行平行連接嘗試。請注意，此情況包含跨區域的 AlwaysOn 可用性群組部署。

## 後續步驟

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!---HONumber=AcomDC_0204_2016-->