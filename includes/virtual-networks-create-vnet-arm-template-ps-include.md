## 使用 PowerShell 部署 ARM 範本

若要使用 PowerShell 部署您下載的 ARM 範本，請依照下列步驟執行。

1. 如果您從未用過 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)，並遵循其中的所有指示登入 Azure，然後選取您的訂用帳戶。
2. 執行 **Switch-AzureMode** Cmdlet，以切換至資源管理員模式，如下所示。

		Switch-AzureMode AzureResourceManager

	此為上述命令的預期輸出內容：

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]Switch-AzureMode Cmdlet 即將被汰除。屆時將重新命名所有的資源管理員 Cmdlet。

3. 如有必要，請執行 **New-AzureResourceGroup** Cmdlet，以建立新的資源群組。下列命令會在「美國中部」Azure 區域中，建立名為 *TestRG* 的資源群組。如需資源群組的詳細資訊，請造訪 [Azure 資源管理員概觀](resource-group-overview.md)。

		New-AzureResourceGroup -Name TestRG -Location centralus
		
	此為上述命令的預期輸出內容：

		ResourceGroupName : TestRG
		Location          : centralus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		                    Actions  NotActions
		                    =======  ==========
		                    *
		ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG

4. 執行 **New-AzureResourceGroupDeployment** Cmdlet，以使用先前下載並修改的範本和參數檔案部署新的 VNet。

		New-AzureResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
			-TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
			
	此為上述命令的預期輸出內容：
		
		DeploymentName    : TestVNetDeployment
		ResourceGroupName : TestRG
		ProvisioningState : Succeeded
		Timestamp         : 8/14/2015 9:40:00 PM
		Mode              : Incremental
		TemplateLink      :
		Parameters        :
		                    Name             Type                       Value
		                    ===============  =========================  ==========
		                    location         String                     Central US
		                    vnetName         String                     TestVNet
		                    addressPrefix    String                     192.168.0.0/16
		                    subnet1Prefix    String                     192.168.1.0/24
		                    subnet1Name      String                     FrontEnd
		                    subnet2Prefix    String                     192.168.2.0/24
		                    subnet2Name      String                     BackEnd
		
		Outputs           :

5. 執行 **Get-AzureVirtualNetwork** Cmdlet 來檢視新 VNet 的屬性，如下所示。


		Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
		
	此為上述命令的預期輸出內容：
		
		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"2ed52eec-8c92-471f-b43b-2914d69f3f04"
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
		                        "Etag": "W/"2ed52eec-8c92-471f-b43b-2914d69f3f04"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      },
		                      {
		                        "Name": "BackEnd",
		                        "Etag": "W/"2ed52eec-8c92-471f-b43b-2914d69f3f04"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

<!---HONumber=Sept15_HO4-->