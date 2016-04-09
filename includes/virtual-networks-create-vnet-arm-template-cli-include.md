## 使用 Azure CLI 部署 ARM 範本

若要使用 Azure CLI 部署您下載的 ARM 範本，請依照下列步驟執行。

1. 如果您從未用過 Azure CLI，請參閱[安裝和設定 Azure CLI](../articles/xplat-cli-install.md)，並依照指示進行，直到選取您的 Azure 帳戶和訂用帳戶。
2. 執行 **`azure config mode`** 命令，以切換為資源管理員模式，如下所示。

		azure config mode arm

	此為上述命令的預期輸出內容：

		info:    New mode is arm

3. 如有必要，執行 **`azure group create`** 以建立新的資源群組，如下所示。請查看命令的輸出內容。輸出後顯示的清單可說明所使用的參數。如需資源群組的詳細資訊，請造訪 [Azure 資源管理員概觀](../articles/resource-group-overview.md)。

		azure group create -n TestRG -l centralus

	此為上述命令的預期輸出內容：

		info:    Executing command group create
		+ Getting resource group TestRG
		+ Creating resource group TestRG
		info:    Created resource group TestRG
		data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            centralus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

	- **-n (或 --name)**。新資源群組的名稱。在本文案例中為 *TestRG*。
	- **-l (或 --location)**。將會在當中建立新資源群組的 Azure 區域。在本文案例中為 *centralus*。

4. 執行 **`azure group deployment create`** Cmdlet，以使用先前下載並修改的範本和參數檔案，部署新的 VNet。輸出後顯示的清單可說明所使用的參數。

		azure group deployment create -g TestRG -n TestVNetDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

	此為上述命令的預期輸出內容：

		info:    Executing command group deployment create
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "TestVNetDeployment"
		+ Registering providers
		info:    Registering provider microsoft.network
		+ Waiting for deployment to complete
		data:    DeploymentName     : TestVNetDeployment
		data:    ResourceGroupName  : TestRG
		data:    ProvisioningState  : Succeeded
		data:    Timestamp          : 2015-08-14T21:56:11.152759Z
		data:    Mode               : Incremental
		data:    Name           Type    Value
		data:    -------------  ------  --------------
		data:    location       String  Central US
		data:    vnetName       String  TestVNet
		data:    addressPrefix  String  192.168.0.0/16
		data:    subnet1Prefix  String  192.168.1.0/24
		data:    subnet1Name    String  FrontEnd
		data:    subnet2Prefix  String  192.168.2.0/24
		data:    subnet2Name    String  BackEnd
		info:    group deployment create command OK

	- **-g (或 --resource-group)**。要建立新 VNet 的資源群組名稱。
	- **-f (或 --template-file)**。ARM 範本檔案的路徑。
	- **-e (或 --parameters-file)**。ARM 參數檔案的路徑。

5. 執行 **`azure network vnet show`** 命令來檢視新 VNet 的屬性，如下所示。

		azure network vnet show -g TestRG -n TestVNet

	此為上述命令的預期輸出內容：

		info:    Executing command network vnet show
		+ Looking up virtual network "TestVNet"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : centralus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		data:    Subnets:
		data:      Name                          : FrontEnd
		data:      Address prefix                : 192.168.1.0/24
		data:
		data:      Name                          : BackEnd
		data:      Address prefix                : 192.168.2.0/24
		data:
		info:    network vnet show command OK

<!---HONumber=AcomDC_0323_2016-->