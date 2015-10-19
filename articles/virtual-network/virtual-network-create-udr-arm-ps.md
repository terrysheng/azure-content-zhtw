<properties 
   pageTitle="在資源管理員中使用 PowerShell 控制路由和使用虛擬應用裝置 | Microsoft Azure"
   description="了解如何在 Azure PowerShell 控制路由和使用虛擬應用裝置"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/30/2015"
   ms.author="telmos" />

#在 PowerShell 中建立使用者定義的路由 (UDR)

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋之內容包括資源管理員部署模型。您也可以[在傳統部署模型中建立 UDR](virtual-network-create-udr-classic-ps.md)。

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

以下的範例 PowerShell 命令會預期已根據上述案例建立簡單的環境。如果您想要以本文件顯示的方式執行命令，請先依照下列方式建置測試環境：部署[此範本](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)，按一下 [部署至 Azure]，視情況取代預設參數值，然後遵循入口網站中的指示。

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## 建立前端子網路的 UDR
若要根據上述案例建立前端子網路所需的路由表和路徑，請依照下列步驟執行。

2. 在 Azure PowerShell 提示字元中執行 **`Switch-AzureMode`** Cmdlet，以切換至資源管理員模式，如下所示。

		Switch-AzureMode AzureResourceManager
	
	預期的輸出：

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]Switch-AzureMode Cmdlet 即將被汰除。屆時將重新命名所有的資源管理員 Cmdlet。

3. 建立用來傳送到以後端子網路 (192.168.2.0/24) 為目標的路由，以路由傳送所有流量到 **FW1** 虛擬應用裝置 (192.168.0.4)。

		$route = New-AzureRouteConfig -Name RouteToBackEnd `
		    -AddressPrefix 192.168.2.0/24 -NextHopType VirtualAppliance `
		    -NextHopIpAddress 192.168.0.4

4. 在包含上面建立的路由 **westus**區域中建立名為 **UDR-FrontEnd** 的路由表。

		$routeTable = New-AzureRouteTable -ResourceGroupName TestRG -Location westus `
		    -Name UDR-FrontEnd -Route $route

5. 建立包含子網路所在之 VNet 的變數。在我們的案例中，VNet 名為 **TestVNet**。

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet

6. 將上方建立的路由表與 **FrontEnd** 子網路建立關聯。
		
		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
			-AddressPrefix 192.168.1.0/24 -RouteTable $routeTable

	預期的輸出：

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : westus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mic
		                    rosoft.Network/virtualNetworks/TestVNet
		Etag              : W/"7df26c0e-652f-4754-bc4e-733fef7d5b2b"
		ProvisioningState : Succeeded
		Tags              : 
		                    Name         Value
		                    ===========  =====
		                    displayName  VNet 
		                    
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : [
								...,
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"7df26c0e-652f-4754-bc4e-733fef7d5b2b"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/pr
		                    oviders/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestR
		                    G/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestR
		                    G/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/
		                    providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
		                        },
		                        "RouteTable": {
		                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/
		                    providers/Microsoft.Network/routeTables/UDR-FrontEnd"
		                        },
		                        "ProvisioningState": "Succeeded"
		                      },
								...
		                    ]

## 建立後端子網路的 UDR
若要根據上述案例建立後端子網路所需的路由表和路徑，請依照下列步驟執行。

1. 建立用來傳送到以前端子網路 (192.168.1.0/24) 為目標的路由，以路由傳送所有流量到 **FW1** 虛擬應用裝置 (192.168.0.4)。

		$route = New-AzureRouteConfig -Name RouteToFrontEnd `
		    -AddressPrefix 192.168.1.0/24 -NextHopType VirtualAppliance `
		    -NextHopIpAddress 192.168.0.4

4. 在包含上面建立的路由 **uswest**區域中建立名為 **UDR-BackEnd** 的路由表。

		$routeTable = New-AzureRouteTable -ResourceGroupName TestRG -Location westus `
		    -Name UDR-BackEnd -Route $route

5. 將上方建立的路由表與 **BackEnd** 子網路建立關聯。

		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
			-AddressPrefix 192.168.2.0/24 -RouteTable $routeTable

	預期的輸出：

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : westus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mic
		                    rosoft.Network/virtualNetworks/TestVNet
		Etag              : W/"7df26c0e-652f-4754-bc4e-733fef7d5b2b"
		ProvisioningState : Succeeded
		Tags              : 
		                    Name         Value
		                    ===========  =====
		                    displayName  VNet 
		                    
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      ...,
		                      {
		                        "Name": "BackEnd",
		                        "Etag": "W/"7df26c0e-652f-4754-bc4e-733fef7d5b2b"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/pr
		                    oviders/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestR
		                    G/providers/Microsoft.Network/networkInterfaces/NICSQL2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestR
		                    G/providers/Microsoft.Network/networkInterfaces/NICSQL1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/
		                    providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        },
		                        "RouteTable": {
		                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/
		                    providers/Microsoft.Network/routeTables/UDR-BackEnd"
		                        },
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

## 啟用 FW1 上的 IP 轉送
若要啟用 **FW1** 所使用的 NIC 中的 IP 轉送，請依照下列步驟執行。

1. 建立包含 FW1 所使用 NIC 的設定的變數。在我們的案例中，NIC 名為 **NICFW1**。

		$nicfw1 = Get-AzureNetworkInterface -ResourceGroupName TestRG -Name NICFW1

2. 啟用 IP 轉送並儲存 NIC 設定。

		$nicfw1.EnableIPForwarding = 1
		Set-AzureNetworkInterface -NetworkInterface $nicfw1

	預期的輸出：

		Name                 : NICFW1
		ResourceGroupName    : TestRG
		Location             : westus
		Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/
		                       Microsoft.Network/networkInterfaces/NICFW1
		Etag                 : W/"e0f9adc0-d8bf-4def-beab-5ed3e1305c9a"
		ProvisioningState    : Succeeded
		Tags                 : 
		                       Name         Value                  
		                       ===========  =======================
		                       displayName  NetworkInterfaces - DMZ
		                       
		VirtualMachine       : {
		                         "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/p
		                       roviders/Microsoft.Compute/virtualMachines/FW1"
		                       }
		IpConfigurations     : [
		                         {
		                           "Name": "ipconfig1",
		                           "Etag": "W/"e0f9adc0-d8bf-4def-beab-5ed3e1305c9a"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
		                       /providers/Microsoft.Network/networkInterfaces/NICFW1/ipConfigurations/ipconfig1",
		                           "PrivateIpAddress": "192.168.0.4",
		                           "PrivateIpAllocationMethod": "Static",
		                           "Subnet": {
		                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/Test
		                       RG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/DMZ"
		                           },
		                           "PublicIpAddress": {
		                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/Test
		                       RG/providers/Microsoft.Network/publicIPAddresses/PIPFW1"
		                           },
		                           "LoadBalancerBackendAddressPools": [],
		                           "LoadBalancerInboundNatRules": [],
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]
		DnsSettings          : {
		                         "DnsServers": [],
		                         "AppliedDnsServers": [],
		                         "InternalDnsNameLabel": null,
		                         "InternalFqdn": null
		                       }
		EnableIPForwarding   : True
		NetworkSecurityGroup : null
		Primary              : True

<!---HONumber=Oct15_HO2-->