<properties 
   pageTitle="如何在 Azure 將傳統 VNet 連接到 ARM Vnet - 解決方案指南"
   description="了解如何在傳統 VNet 和新的 VNet 之間建立 VPN 連線"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# 將傳統 VNet 連接到新的 VNet

Azure 目前有兩種管理模式：Azure 服務管理員 (稱為傳統) 和 Azure 資源管理員 (ARM)。如果您已使用 Azure 一段時間，則可能具有傳統 VNet 上執行的 Azure VM 和執行個體角色。此外，較新的 VM 和角色執行個體可能在 ARM 中建立的 VNet 上執行。

在這種情況下，您將想要確保新的基礎結構能夠與您的傳統資源進行通訊。作法為您可以在兩個 VNet 之間建立 VPN 連線。下圖說明具有兩個 VNet (傳統和 ARM) 的範例環境，以及 VNet 之間的安全通道連線能力。

![](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure01.png)

>[AZURE.NOTE]這份文件會引導您完成用於測試用途的端對端解決方案。如果您已設定 VNet，而且熟悉 Azure 中的 VPN 閘道與站台對站台連線，請造訪[在 ARM VNet 與傳統 VNet 之間設定 S2S VPN](../virtual-networks-arm-asm-s2s-howto.md)。

若要測試此案例，您將：

1. [建立傳統 VNet 環境](#Create-a-classic-VNet-environment)。
2. [建立新的 VNet 環境](#Create-a-new-VNet-environment)。
3. [連接兩個 VNet](#Connect-the-two-VNets)。

您將執行上述步驟，方法是首先使用傳統 Azure 管理工具，包括傳統入口網站、網路組態檔，以及 Azure 服務管理員 PowerShell Cmdlet；而且稍後會移至新的管理工具，包括新的 Azure 入口網站、ARM 範本和 ARM PowerShell Cmdlet。

>[AZURE.IMPORTANT]若要使 VNet 彼此連接，它們不能有相衝突的 CIDR 區塊。確定每個 VNet 具有唯一的 CIDR 區塊！

## 建立傳統 VNet 環境

您可以使用現有的傳統 VNet 連接到新的 ARM VNet。在此範例中，您將了解如何建立新的傳統 VNet，而且基於測試目的，其會具有兩個子網路、一個閘道和一個 VM。

### 步驟 1： 建立傳統 VNet

若要建立新的 VNet 對應至上述圖 1，請遵循下列指示。

1. 從 PowerShell 主控台中，執行下列命令以新增您的 Azure 帳戶。

		Add-AzureAccount

2. 遵循登入對話方塊的指示，來使用您的 Azure 帳戶登入。

3. 執行下列命令，來確定您是使用 Azure 服務管理 PowerShell Cmdlet。

		Switch-AzureMode AzureServiceManagement

4. 執行下列命令來下載 Azure 網路組態檔。

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

		XMLConfiguration                                OperationDescription                            OperationId                                     OperationStatus                                
		----------------                                --------------------                            -----------                                     ---------------                                
		<?xml version="1.0" encoding="utf-8"?>...       Get-AzureVNetConfig                             04ab3224-7e1c-cc1a-8b75-96c6c300ddb8            Succeeded       

5. 開啟您剛下載的檔案，然後新增一個名為 *vnet02* 的區域網路網站，其會使用 *10.2.0.0/16* 做為位址首碼，以及使用任何有效的公用 IP 位址做為 VPN 閘道位址。作法為將 **LocalNetworkSite** 元素新增至組態檔中的 **LocalNetworkSites** 元素。底下的檔案片段顯示 **LocalNetworksSites** 元素範例。

	    <LocalNetworkSites>
	      <LocalNetworkSite name="vnet02">
	        <AddressSpace>
	          <AddressPrefix>10.2.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>2.0.0.2</VPNGatewayAddress>
	      </LocalNetworkSite>
	    </LocalNetworkSites>		

6. 在 **VirtualNetworkSites** 元素中，新增一個位址首碼為 *10.1.0.0/16* 的虛擬網路，以及兩個根據上述案例圖的子網路。底下的檔案片段顯示 **Netcfg** 元素範例。
	
	    <VirtualNetworkSites>
	      <VirtualNetworkSite name="vnet01" Location="East US">
	        <AddressSpace>
	          <AddressPrefix>10.1.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <Subnets>
	          <Subnet name="Subnet1">
	            <AddressPrefix>10.1.0.0/24</AddressPrefix>
	          </Subnet>
	          <Subnet name="GatewaySubnet">
	            <AddressPrefix>10.1.200.0/29</AddressPrefix>
	          </Subnet>
	        </Subnets>
	      </VirtualNetworkSite>
	    </VirtualNetworkSites>

7. 儲存檔案，然後執行下列命令，將它上傳至 Azure。確定您會視需要變更環境的檔案路徑。

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

		OperationDescription                                            OperationId                                                     OperationStatus                                                
		--------------------                                            -----------                                                     ---------------                                                
		Set-AzureVNetConfig                                             e0ee6e66-9167-cfa7-a746-7cab93c22013                            Succeeded 

### 步驟 2：在傳統 VNet 中建立 VM

若要使用 Azure 服務管理員 PowerShell Cmdlet，在傳統 VNet 中建立 VM，請遵循下列指示。

1. 從 Azure 擷取 VM 映像。下列 PowerShell 命令會擷取最新可用的 Windows Server 2012 R2 映像。

		$WinImage = (Get-AzureVMImage `
		    | ?{$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"} `
		    | sort PublishedDate -Descending)[0]		

2. 執行下列命令，建立儲存體帳戶來儲存 VM 的虛擬硬碟 (VHD)。

		$storage1 = New-AzureStorageAccount `
			-StorageAccountName v1v2teststorage1 `
		    -Location "East US"	

3.  執行下列命令來建立 VM。確定取代使用者名稱和密碼值。

		$vm1 = New-AzureVMConfig -Name "VM01" -InstanceSize "ExtraLarge" `
		    -Image $WinImage.ImageName –AvailabilitySetName "MyAVSet1" `
		    -MediaLocation "https://v1v2teststorage1.blob.core.windows.net/vhd/vm01.vhd"
		Add-AzureProvisioningConfig –VM $vm1 -Windows `
		    -AdminUserName "user" -Password "P@ssw0rd" 

4.  執行下列命令，將 VM 連接至 *Subnet1*。

		Set-AzureSubnet -SubnetNames "Subnet1" -VM $vm1
		Set-AzureStaticVNetIP  -IPAddress "10.1.0.101" -VM $vm1

5. 執行下列命令，建立新的雲端服務來裝載 VM。

		New-AzureService -ServiceName "v1v2svc01" -Location "East US"
 		New-AzureVM -ServiceName "v1v2svc01" –VNetName "vnet01" –VMs $vm1

### 步驟 3：建立傳統 VNet 的 VPN 閘道 

若要使用傳統的 Azure 入口網站來建立 vnet01 的 VPN 閘道，請遵循下列指示。

1. 開啟傳統的 Azure 入口網站，網址是 https://manage.windowsazure.com。如有必要，請指定您的認證。
2. 向下捲動 [**所有項目**] 的清單，然後按一下 [**網路**]。
3. 在虛擬網路的清單上，按一下 [**vnet01**]，然後按一下 [**設定**]。
4. 在 [**台對站連線能力**] 下，啟用 [**連接到區域網路**] 核取方塊。
5. 在 [**區域網路**] 清單上，選取 [**vnet02**]，然後按一下 [**儲存**]，再按一下 [**是**]。
6. 按一下 [**儀表板**]，並注意指出閘道尚未建立的訊息，如下列圖 2 所示。

	![VNet 儀表板](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure02.png)

7. 按一下 [**建立閘道**] (如下列圖 3 所示) 來建立 vnet01 的 VPN 閘道。

	![VNet 儀表板](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure03.png)

8. 在閘道類型的清單中，按一下 [**動態**]，然後按一下 [**是**]。請注意，當建立閘道時，儀表板影像會變更，以黃色顯示閘道。

	![VNet 儀表板](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure04.png)

	>[AZURE.NOTE]這項作業可能需要幾分鐘的時間。

9. 一旦建立了閘道，請寫下閘道的公用 IP 位址，如下所見。稍後您將需要此位址，建立 ARM VNet 的區域網路。

	![VNet 儀表板](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure05.png)

## 建立新的 VNet 環境

既然傳統 VNet 已啟動，並搭配 VM 和閘道執行，是時候建立 ARM VNet。

### 步驟 1：在 ARM 中建立新的 VNet

若要使用 ARM 範本建立 ARM VNet，搭配兩個子網路，以及傳統 VNet 的一個區域網路，請遵循下列指示。

1. 從 [git 中心](https://github.com/Azure/azure-quickstart-templates/tree/master/arm-asm-s2s)下載 azuredeploy.json 和 azuredeploy-parameters.json 檔案。
2. 在 Visual Studio 中開啟 azuredeploy.json 檔案，並注意此範本會建立四個資源： 

	- **本機閘道**：此資源代表針對您想要連接到的 VNet 所建立的閘道。在此案例中，指的是 vnet01 的閘道。
	- **虛擬網路**：此資源代表要建立的 ARM VNet。在此案例中，指的是 vnet02。
	- **閘道公用 IP**：此資源代表要針對 ARM VNet 所建立之閘道的公用 IP 位址。 
	- **閘道**：此資源代表要針對 ARM VNet (vnet02) 所建立的閘道。

3. 請注意此檔案中所使用的參數。其中大部分都有預設值。您可以根據您的需求變更這些值。

4. 在 Visual Studio 中開啟 azuredeploy-parameters.json 檔案。此檔案包含要對範本檔案中的參數使用的值。如有必要，請編輯下列值。

	- **subscriptionId**：編輯並貼上您的訂用帳戶識別碼。如果不知道您的訂用帳戶識別碼，請執行 **Get-azuresubscription** PowerShell Cmdlet 來擷取您的識別碼。
	- **位置**：指定將建立 VNet 的 Azure 位置。在此案例中，它將是 [**美國中部**]。
	- **virtualNetworkName**：這是要建立的 ARM VNet 名稱。在此案例中，指的是 **vnet02**。
	- **localGatewayName**：這是您想要從新的 ARM VNet 連接到的區域網路。在此案例中，指的是 **vnet01**。
	- **localGatewayIpAddress**：這是針對您想要連接到的網路所建立之閘道的公用 IP 位址。在此案例中，這是當建立 **vnet01** 的 VPN 閘道時，您在上述步驟 9 中所寫下的 IP 位址。
	- **localGatewayAddressPrefix**：這是 VNet 將連接到之區域網路的 CIDR 區塊。在此案例中，您將連接到的 VNet 是 **vnet01**，而且其 CIDR 區塊是 **10.1.0.0/16**。
	- **gatewayPublicIPName**：這是要對閘道之公用 IP 建立的 IP 物件名稱，而此閘道將針對 ARM VNet 而建立。
	- **gatewayName**：這是將對 ARM VNet 建立之閘道物件的名稱。
	- **addressPrefix**：這是 ARM VNet 的 CIDR 區塊。在此案例中，指的是 **10.2.0.0/16**。
	- **subnet1Prefix**：這是 ARM VNet 中子網路的 CIDR 區塊。在此案例中，指的是 **10.2.0.0/24**。
	- **gatewaySubnetPrefix**：這是 ARM VNet 中閘道子網路的 CIDR 區塊。在此案例中，指的是 **10.2.200.0/29**。
	- **connectionName**：這是要建立之連接物件的名稱。
	- **sharedKey**：這是連接的 IPSec 共用金鑰。在此案例中，指的是 **abc123**。

5. 若要建立 ARM VNet 和其相關物件，請在名為 **RG1** 的新資源群組中，執行下列 PowerShell 命令。確定您變更範本檔案和參數檔案的路徑。

		Switch-AzureMode AzureResourceManager
		New-AzureResourceGroup -Name RG1 -Location "Central US" `
		    -TemplateFile C:\Azure\azuredeploy.json `
		    -TemplateParameterFile C:\Azure\azuredeploy-parameters.json		

	>[AZURE.NOTE]這項作業可能需要幾分鐘的時間。

7. 從瀏覽器中，巡覽至 https://portal.azure.com/，並在需要時輸入您的認證。
8. 按一下 Azure 入口網站中的 **RG1** 資源群組磚 ，如下所示。

	![VNet 儀表板](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure06.png)

9. 請注意使用 ARM 範本新增至群組的資源。

### 步驟 2：在 ARM 中建立新的 VM

若要在新的 VNet 中建立 VM，請從 Azure 入口網站中，遵循下列指示。

1. 從入口網站中，依序按一下 [**新增**] 按鈕、[**計算**] 和 [**Windows Server 2012 R2 Datacenter**]。
2. 在右窗格底部的 [**選取計算堆疊**] 中，選取 [**使用資源管理員堆疊**]，在 ARM 中建立 VM (如下所示)，然後按一下 [**建立**]。

	![VNet 儀表板](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure07.png)

3. 在 [**基本**] 刀鋒視窗中，輸入 VM 名稱、使用者名稱、密碼、訂用帳戶、資源群組，以及 VM 的位置，然後按一下 [**確定**]。下圖顯示這個案例的設定。

	![VNet 儀表板](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure08.png)

4. 在 [**選擇大小**] 刀鋒視窗中，選取大小，然後按一下 [**選取**]。對於此案例，請選取 [**D2**]。
5. 在 [**設定**] 刀鋒視窗中，按一下 [**虛擬網路**]，然後按一下 [**vnet02**]。
6. 依序按一下 [**子網路**]、[**Subnet1**] 和 [**確定**]。[**摘要**] 刀鋒視窗看起來應該類似下圖。

	![VNet 儀表板](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure09.png)

7. 按兩次 [**確定**]，然後按一下 [**建立**] 來建立 VM。您會在入口網站中看到新的磚，顯示正在佈建的 VM，如下所示。

	![VNet 儀表板](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure10.png)

	>[AZURE.NOTE]這項作業可能需要幾分鐘的時間。您可以移至本文件的下一個部分。

## 連接兩個 VNet

既然您有兩個 VNet 且 VM 已連接到其中，是時候透過先前建立的閘道連接 VNet，然後測試連線。

### 步驟 1：設定傳統 VNet 的閘道

您需要設定傳統 VNet，才能使用針對 ARM VNet (vnet02) 建立之閘道的 IP 位址，然後從每個 VNet 建立連線。若要這樣做，請遵循下列指示。

1. 若要擷取用於 ARM VNet 中閘道的 IP 位址，請執行下列命令，並注意輸出。寫下位址，稍後您將需要它，來修改傳統 VNet 的區域網路設定更。

		Get-AzurePublicIpAddress | ?{$_.Name -eq "ArmAsmS2sGatewayIp"}

		Name                     : ArmAsmS2sGatewayIp
		ResourceGroupName        : RG1
		Location                 : centralus
		Id                       : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/publicIPAddresses/ArmAsmS2sGatewayIp
		Etag                     : W/"1ee6c1bd-8be1-488e-a571-77b05b49e33a"
		ProvisioningState        : Succeeded
		Tags                     : 
		PublicIpAllocationMethod : Dynamic
		IpAddress                : 23.99.213.28
		IdleTimeoutInMinutes     : 4
		IpConfiguration          : {
		                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworkGateways/ArmAsmGateway/ipConfigurations/vn
		                           etGatewayConfig"
		                           }
		DnsSettings              : null

2. 執行下列命令，確定您是對 PowerShell 命令使用 Azure 服務管理 API。

		Switch-AzureMode AzureServiceManagement

3. 執行下列命令來下載 Azure 網路組態檔。

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

4. 開啟您剛下載的檔案，然後編輯 **vnet02** 的 **LocalNetworkSite** 元素，為上述步驟 1 中取得的新 VNet 新增閘道的 IP 位址。此元素看起來應該類似下列範例。

	      <LocalNetworkSite name="vnet02">
	        <AddressSpace>
	          <AddressPrefix>10.2.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>23.99.213.28</VPNGatewayAddress>
	      </LocalNetworkSite>

5. 儲存檔案，然後執行下列命令來設定傳統 vnet。確定您變更路徑，以指向您在上述步驟 4 中儲存的檔案。

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

6. 執行下列命令來設定傳統 VNet 閘道的 IPSec 共用金鑰。您應該會看到類似下面所公佈的輸出。確定您變更 VNet 名稱，如果您是使用您自己預先存在的 Vnet 的話。

		Set-AzureVNetGatewayKey -VNetName vnet01 -LocalNetworkSiteName vnet02 -SharedKey abc123 

		Error          : 
		HttpStatusCode : OK
		Id             : b2154475-bf00-480e-ad1e-aed893014979
		Status         : Successful
		RequestId      : 08257a09d723cb8982c47b85edb0e08a
		StatusCode     : OK

### 步驟 2：設定 ARM VNet 的閘道

既然您已設定傳統的 VNet 閘道，是時候建立連線。若要這樣做，請遵循下列指示。

1. 從 PowerShell 主控台中，執行下列命令以切換到 ARM 模式。 

		Switch-AzureMode AzureResourceManager

2. 執行下列命令，在閘道之間建立連線。

		$vnet01gateway = Get-AzureLocalNetworkGateway -Name vnet01 -ResourceGroupName RG1
		$vnet02gateway = Get-AzureVirtualNetworkGateway -Name ArmAsmGateway -ResourceGroupName RG1
		
		New-AzureVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "Central US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

3. 開啟 Azure 入口網站(網址是 https://manage.windowsazure.com )，並在需要時輸入您的認證。
4. 在 [**所有項目**] 下，向下捲動並依序按一下 [**網路**]、[**vnet01**] 和 [**儀表板**]。請注意，現在已建立 **vnet01** 與 **vnet02** 之間的連線，如下所見。

	![VNet 儀表板](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure11.png)

5. 雖然您可以從傳統的入口網站來管理傳統的 VNet 和其連線，但是建議使用新的 Azure 入口網站。若要開啟新的入口網站，請巡覽至 https://portal.azure.com。
6. 在新的入口網站中，依序按一下 [**全部瀏覽**]、[**虛擬網路 (傳統)**] 和 [**vnet01**]。請注意下面顯示的 [**VPN 連線**] 窗格。

	![VNet 儀表板](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure12.png)

### 步驟 3：測試連線

既然您已連接兩個 VNet，是時候從某個 VM ping 另一個 VM 來測試連線能力。您將需要變更其中一個 VM 中的防火牆設定來允許 ICMP，然後從另一個 VM 偵測該 VM。若要這樣做，請遵循下列指示。

1. 從 Azure 入口網站中，依序按一下 [**全部瀏覽**]、[**虛擬機器**] 和 [**VM02**]。
2. 從 [**VM02**] 刀鋒視窗中，按一下 [**連接**]。如有需要，請在瀏覽器安全性橫幅上按一下 [**開啟**]，來開啟 RDP 檔案。
3. 在 [**遠端桌面連線**] 對話方塊中，按一下 [**連接**]。
4. 在 [**Windows 安全性**] 對話方塊中，輸入您的 VM 使用者名稱和密碼。 
5. 在 [**遠端桌面連線**] 對話方塊中，按一下 [**是**]。
6. 一旦連接到 VM，即從 [**伺服器管理員**]，依序按一下 [**工具**] 和 [**具有進階安全性的 Windows 防火牆**]。
7. 在 [**具有進階安全性的 Windows 防火牆**] 視窗中，按一下 [**輸入規則**]。
8. 在 [**輸入規則**] 清單中，以滑鼠右鍵按一下 [**檔案及印表機共用 (回應要求 - ICMPv4-In)**]，然後按一下 [**啟用規則**]。
9. 在工作列上，按一下 [**Windows PowerShell**] 按鈕，然後執行下列命令。

		ipconfig

		Connection-specific DNS Suffix  . : 4oxp4ce0c5rulb1iey4cdqhasg.gx.internal.cloudapp.net
		Link-local IPv6 Address . . . . . : fe80::8cea:a98a:5c48:4c58%12
		IPv4 Address. . . . . . . . . . . : 10.2.0.101
		Subnet Mask . . . . . . . . . . . : 255.255.255.0
		Default Gateway . . . . . . . . . : 10.2.0.101

10. 寫下 VM 的 IP 位址。在此案例中，指的是 **10.2.0.101**。您將從其他 VM ping 該位址來測試 VNet 之間的連線能力。
11. 從 Azure 入口網站中，在左窗格上，依序按一下 [**虛擬機器 (傳統)**]、[**VM01**] 和 [**連接**]。如有需要，請在瀏覽器安全性橫幅上按一下 [**開啟**]，來開啟 RDP 檔案。
12. 在 [**遠端桌面連線**] 對話方塊中，按一下 [**連接**]。
13. 在 [**Windows 安全性**] 對話方塊中，輸入您的 VM 使用者名稱和密碼。 
14. 在 [**遠端桌面連線**] 對話方塊中，按一下 [**是**]。
15. 在遠端 VM 的工作列中，按一下 [**Windows PowerShell**] 按鈕，然後執行下列命令。

		ping 10.2.0.101

		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126

## 後續步驟

- 深入了解 [ARM 的網路資源提供者 (NRP)](../resource-groups-networking.md)。
- 檢視有關如何[在傳統 VNet 和 ARM VNet 之間建立 S2S VPN 連線](../virtual-networks-arm-asm-s2s-howto.md)的一般指導方針。

<!---HONumber=AcomDC_1217_2015-->