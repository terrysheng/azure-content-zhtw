<properties 
   pageTitle="設定負載平衡器使用 SQL 一律開啟 | Microsoft Azure"
   description="設定負載平衡器使用 SQL 一律開啟，以及如何運用 powershell 來建立 SQL 實作的負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/12/2015"
   ms.author="joaoma" />

# 設定負載平衡器使用 SQL 一律開啟

SQL Server AlwaysOn 可用性群組現在可以與 ILB 搭配執行。可用性群組是 SQL Server 針對高可用性和災難復原的旗艦解決方案。不論組態中的複本數目為何，可用性群組接聽程式可讓用戶端應用程式順暢地連接到主要複本。

接聽程式 (DNS) 名稱會對應至負載平衡的 IP 位址，而 Azure 的負載平衡器會將連入流量只導向複本集中的主要伺服器。


您可以使用 ILB 的 SQL Server AlwaysOn (接聽程式) 端點支援。您現在可以控制接聽程式的存取設定，並且可以從虛擬網路 (VNet) 的特定子網路中選擇負載平衡的 IP 位址。

在接聽程式上使用 ILB 之後，只有下列項目可以存取 SQL Server 端點 (例如 Server=tcp:ListenerName,1433;Database=DatabaseName)：

服務和位於相同虛擬網路服務的 VM、連接內部部署網路服務的 VM，和內部連接 VNet 的 VM

![ILB\_SQLAO\_NewPic](./media/load-balancer-configure-sqlao/sqlao1.jpg)


內部負載平衡器只能透過 PowerShell 進行設定。


## 將內部負載平衡器新增至服務 

### 步驟 1.

在下列範例中，我們將針對包含子網路 (名為 '子網路-1') 的虛擬網路進行設定：

	Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc

步驟 2.

## 為每個 VM 上的 ILB 新增負載平衡端點

	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –
	DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

 	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

上述範例中，您有 2 個分別稱為 "sqlsvc1" 和 "sqlsvc2" 的 VM 正在雲端服務 "Sqlsvc" 中執行。在使用 "DirectServerReturn" 參數建立 ILB 之後，您就可以將負載平衡端點加入 ILB，讓 SQL 可以設定可用性群組的接聽程式。

您可以在[教學課程： Azure 的 AlwaysOn 可用性群組](https://msdn.microsoft.com/library/dn249504.aspx)或[使用入口網站圖庫](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)，找到更多建立 SQL Alwayson 的詳細資料。


## 另請參閱

[開始設定網際網路面向的負載平衡器](load-balancer-internet-getstarted.md)

[開始設定內部負載平衡器](load-balancer-internal-getstarted.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=Oct15_HO3-->