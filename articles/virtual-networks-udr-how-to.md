<properties 
   pageTitle="如何在 Azure 中建立路由並啟用 IP 轉送"
   description="如何在 Azure 中建立路由並啟用 IP 轉送"
   services="virtual-networks"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />

<tags 
   ms.service="virtual-networks"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# 如何在 Azure 中建立路由並啟用 IP 轉送
您可以使用 Azure 中的虛擬應用裝置處理 Azure 虛擬網路中的流量。不過，您需要建立可讓虛擬網路中的 VM 和雲端服務將封包傳送至虛擬應用裝置的路由，而不是傳送至封包所需的目的地的路由。您也需要在虛擬應用裝置 VM 上啟用 IP 轉送，讓它可以接收和轉送未定址到實際虛擬應用裝置 VM 的封包。

##如何管理路由
您可以在 Azure 中使用 PowerShell 新增、移除及變更路由。在您可以建立路由之前，必須先建立一個託管路由的路由表。

### 如何建立路由表
若要建立名為 *FrontEndSubnetRouteTable* 的路由表，執行下列 PowerShell 命令：

	New-AzureRouteTable -Name FrontEndSubnetRouteTable `
	-Location usnorth `
	-Label "Route table for frontend subnet"

### 如何將路由新增到路由表
若要在以上建立的路由表中新增將 *10.1.1.10* 設定為 *10.2.0.0/16* 子網路的下一個躍點的路由，執行下列 PowerShell 命令：

	Set-AzureRoute -RouteTableName FrontEndSubnetRouteTable `
	-RouteName FirewallRoute -AddressPrefix 10.2.0.0/16 `
	-NextHopType VirtualAppliance `
	-NextHopIpAddress 10.1.1.10

### 如何將路由與子網路建立關聯
路由表必須與一個或多個子網路建立關聯，才能使用。若要在虛擬網路 *ProductionVnet* 中，將 *FrontEndSubnetRouteTable* 路由表與名稱為 *FrontEndSubnet* 的子網路建立關聯，執行下列 PowerShell 命令：

	Set-AzureSubnetRouteTable -VNetName ProductionVnet `
	-SubnetName FrontEndSubnet `
	-RouteTableName FrontEndSubnetRouteTable

### 如何在 VM 中查看套用的路由
您可以查詢 Azure 以查看針對特定 VM 或角色執行個體套用的實際路由。顯示的路由包括 Azure 提供的預設路由以及 VPN 閘道通告的路由。顯示的路由限制為 800。

若要在名稱為 *FirewallAppliance1* 的 VM 上查看與主要 NIC 相關聯的路由，執行下列 PowerShell 命令：

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable

若要在名稱為 *FirewallAppliance1* 的 VM 上查看與名稱為 *backendnic* 的次要 NIC 相關聯的路由，執行下列 PowerShell 命令：

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic

若要在名稱為 *myRole* 的角色執行個體 \(屬於名稱為 *myservice* 的雲端服務的一部分\) 上查看與主要 NIC 相關聯的路由，執行下列 PowerShell 命令：

	Get-AzureEffectiveRouteTable -ServiceName myservice `
	-RoleInstanceName myRole

## 如何管理 IP 轉送
如先前所提及，您需要在當做虛擬應用裝置的任何 VM 或角色執行個體上啟用 IP 轉送。

### 如何啟用 IP 轉送
若要在名稱為 *FirewallAppliance1* 的 VM 中啟用 IP 轉送，執行下列 PowerShell 命令：

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Enable

若要在名稱為 *myService* 的雲端服務中，名稱為 *FirewallAppliance1* 的角色執行個體內啟用 IP 轉送，執行下列 PowerShell 命令：

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Enable

### 如何停用 IP 轉送
若要在名稱為 *FirewallAppliance1* 的 VM 中停用 IP 轉送，執行下列 PowerShell 命令：

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Disable

若要在名稱為 *myService* 的雲端服務中，名稱為 *FirewallAppliance1* 的角色執行個體內停用 IP 轉送，執行下列 PowerShell 命令：

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Disable

### 如何檢視 IP 轉送的狀態
若要在名稱為 *FirewallAppliance1* 的 VM 中檢視 IP 轉送的狀態，執行下列 PowerShell 命令：

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureIPForwarding

## 另請參閱

[使用者定義的路由和 IP 轉送概觀](../virtual-networks-udr-overview)

[執行個體層級公用 IP \(ILIP\)](../virtual-networks-instance-level-public-ip)

[虛擬網路概觀](https://msdn.microsoft.com/library/azure/jj156007.aspx) <!--HONumber=52-->
