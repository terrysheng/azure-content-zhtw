## 如何使用 PowerShell 建立 VNet

若要使用 PowerShell 建立 VNet，請遵循下列步驟。

1. 如果您從未用過 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)，並遵循其中的所有指示登入 Azure，然後選取您的訂用帳戶。
2. 在 Azure PowerShell 提示字元中執行 **Switch-AzureMode** Cmdlet，以切換至資源管理員模式，如下所示。

		Switch-AzureMode AzureResourceManager
	
		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]Switch-AzureMode Cmdlet 即將被汰除。屆時將重新命名所有的資源管理員 Cmdlet。
	
3. 如有必要，請執行 **New-AzureResourceGroup** Cmdlet，以建立新的資源群組，如下所示。在我們的案例中，會建立名為 *TestRG* 的資源群組。如需資源群組的詳細資訊，請造訪 [Azure 資源管理員概觀](resource-group-overview.md/#resource-groups)。

		New-AzureResourceGroup -Name TestRG -Location centralus
	
		ResourceGroupName : TestRG
		Location          : centralus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		                    Actions  NotActions
		                    =======  ==========
		                    *
		
		ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG	

4. 執行 **New-AzureVirtualNetwork** Cmdlet 以建立新的 VNet，如下所示。

		New-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
			-AddressPrefix 192.168.0.0/16 -Location centralus	
		
		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"5b89894f-db7f-4634-9870-f9b97e209510"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : []

5. 執行 **Get-AzureVirtualNetwork** Cmdlet，將虛擬網路物件儲存在變數中，如下所示。

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
	
	>[AZURE.TIP]您可以藉由執行 **$vnet = New-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus** 結合步驟 4 和 5。

6. 執行 **Add-AzureVirtualNetworkSubnetConfig** Cmdlet，將子網路加入至新的 VNet，如下所示。

		Add-AzureVirtualNetworkSubnetConfig -Name FrontEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
		
		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"5b89894f-db7f-4634-9870-f9b97e209510"
		ProvisioningState : Succeeded
		Tags              :
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
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": null,
		                        "Id": null,
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": null,
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": null
		                      }
		                    ]

7. 重複上述步驟 6，建立您想要的每個子網路。以下命令會為我們的案例建立 *BackEnd* 子網路。

		Add-AzureVirtualNetworkSubnetConfig -Name BackEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

8. 雖然建立了子網路，但是它們目前只以本機變數的形式存在，並用來擷取您在前述步驟 4 中建立的 VNet。若要將變更儲存至 Azure，請執行 **Set-AzureVirtualNetwork** Cmdlet，如下所示。

		Set-AzureVirtualNetwork -VirtualNetwork $vnet	
		
		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": []
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      },
		                      {
		                        "Name": "BackEnd",
		                        "Etag": "W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

<!---HONumber=August15_HO9-->