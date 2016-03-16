## 如何透過 Azure CLI 建立 VNet

您可以透過 Azure CLI 管理來自任何 Windows、Linux 或 OSX 電腦的命令提示字元中的 Azure 資源。若要透過 Azure CLI 建立 VNet，請依照下列步驟執行。

1. 如果您從未使用過 Azure CLI，請參閱[安裝和設定 Azure CLI](xplat-cli-install.md)，並依照指示進行，直到開始選取您的 Azure 帳戶和訂用帳戶為止。
2. 執行 **azure config mode** 命令，以切換為 Azure 資源管理員模式，如下所示。

		azure config mode arm

	此為上述命令的預期輸出內容：

		info:    New mode is arm

3. 若有必要，請執行 **azure group create** 以建立新的資源群組，如下所示。請查看命令的輸出內容。輸出之後所顯示的清單說明需使用的參數。如需資源群組的詳細資訊，請瀏覽 [Azure 資源管理員概觀](resource-group-overview.md/#resource-groups)。

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

4. 執行 **azure network vnet create** 命令以建立 VNet 和子網路，如下所示。

		azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

	此為上述命令的預期輸出內容：

		info:    Executing command network vnet create
		+ Looking up virtual network "TestVNet"
		+ Creating virtual network "TestVNet"
		+ Loading virtual network state
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : centralus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		info:    network vnet create command OK

	- **-g (或 --resource-group)**。將會在當中建立 VNet 之資源群組的名稱。在本文案例中為 *TestRG*。
	- **-n (或 --name)**。即將建立的 VNet 的名稱。在本文案例中為 *TestVNet*
	- **-a (或 --address-prefixes)**。用於 VNet 位址空間的 CIDR 區塊清單。在本文案例中為 *192.168.0.0/16*。
	- **-l (或 --location)**。將會在當中建立 VNet 的 Azure 區域。在本文案例中為 *centralus*。

5. 執行 **azure network vnet subnet create** 命令以建立子網路，如下方所示。請查看命令的輸出內容。輸出後顯示的清單可說明所使用的參數。

		azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

	此為上述命令的預期輸出內容：

		info:    Executing command network vnet subnet create
		+ Looking up the subnet "FrontEnd"
		+ Creating subnet "FrontEnd"
		+ Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:
		info:    network vnet subnet create command OK

	- **-e (或 --vnet-name**。將會在當中建立子網路的 VNet 的名稱。在本文案例中為 *TestVNet*。
	- **-n (或 --name)**。新的子網路的名稱。在本文案例中為 *FrontEnd*。
	- **-a (或 --address-prefix)**。子網路的 CIDR 區塊。在本文案例中為 *192.168.1.0/24*。

6. 如有必要，請重複上述的步驟 5，以建立其他子網路。在本文案例中，執行以下命令，以建立 *BackEnd* 子網路。

		azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. 執行 **azure network vnet show** 命令以檢視新 Vnet 的屬性，如下所示。

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

<!---HONumber=Oct15_HO3-->