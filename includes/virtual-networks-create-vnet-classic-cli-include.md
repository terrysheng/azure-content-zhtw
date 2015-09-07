## 如何使用 Azure CLI 建立傳統 VNet

您可以使用 Azure CLI 透過任何 Windows、Linux 或 OSX 電腦命令提示字元管理 Azure 資源。若要使用 Azure CLI 建立 VNet，請遵循下列步驟。

1. 如果您從未使用過 Azure CLI，請參閱[安裝和設定 Azure CLI](xplat-cli.md)，並依照指示進行，直到選取您的 Azure 帳戶和訂用帳戶。
2. 執行 **azure network vnet create** 命令來建立 VNet 和子網路，如下所示。請注意 CLI 命令的輸出。輸出後顯示的清單可說明所使用的參數。

			azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
	
			info:    Executing command network vnet create
			+ Looking up network configuration
			+ Looking up locations
			+ Setting network configuration
			info:    network vnet create command OK

	- **--vnet**。即將建立的 VNet 名稱。在我們的案例中為 *TestVNet*
	- **-e (或 --address-space)**。VNet 位址空間。在我們的案例中為 *192.168.0.0*
	- **-i (或 -cidr)**。CIDR 格式的網路遮罩。在我們的案例中為 *16*。
	- **-n (或 --subnet-name**)。第一個子網路的名稱。在我們的案例中為 *FrontEnd*。
	- **-p (或 --subnet-start-ip)**。子網路的起始 IP 位址或子網路位址空間。在我們的案例中為 *192.168.1.0*。
	- **-r (或 --subnet-cidr)**。CIDR 格式的子網路網路遮罩。在我們的案例中為 *24*。
	- **-l (或 --location)**。將要建立 VNet 的 Azure 區域。在我們的案例中為*美國中部*。

3. 執行 **azure network vnet subnet create** 命令來建立子網路，如下所示。請注意命令的輸出。輸出後顯示的清單可說明所使用的參數。

			azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
	
			info:    Executing command network vnet subnet create
			+ Looking up network configuration
			+ Creating subnet "BackEnd"
			+ Setting network configuration
			+ Looking up the subnet "BackEnd"
			+ Looking up network configuration
			data:    Name                            : BackEnd
			data:    Address prefix                  : 192.168.2.0/24
			info:    network vnet subnet create command OK

	- **-t (或 --vnet-name**。將會建立子網路的 VNet 名稱。在我們的案例中為 *TestVNet*。
	- **-n (或 --name)**。新子網路的名稱。在我們的案例中為 *BackEnd*。
	- **-a (或 --address-prefix)**。子網路 CIDR 區塊。在我們的案例中為 *192.168.2.0/24*。

4. 執行 **azure network vnet show** 命令來檢視新 vnet 的屬性，如下所示。

			azure network vnet show

			info:    Executing command network vnet show
			Virtual network name: TestVNet
			+ Looking up the virtual network sites
			data:    Name                            : TestVNet
			data:    Location                        : Central US
			data:    State                           : Created
			data:    Address space                   : 192.168.0.0/16
			data:    Subnets:
			data:      Name                          : FrontEnd
			data:      Address prefix                : 192.168.1.0/24
			data:
			data:      Name                          : BackEnd
			data:      Address prefix                : 192.168.2.0/24
			data:
			info:    network vnet show command OK

<!---HONumber=August15_HO9-->