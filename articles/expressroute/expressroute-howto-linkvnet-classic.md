<properties 
   pageTitle="將虛擬網路連結到 ExpressRoute 循環 |Microsoft Azure"
   description="本文件提供如何將虛擬網路 (Vnet) 連結到 ExpressRoute 循環的概觀。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc" />

# 將虛擬網路連結到 ExpressRoute 線路

> [AZURE.SELECTOR]
- [PowerShell - Classic](expressroute-howto-linkvnet-classic.md)
- [PowerShell - Resource Manager] (expressroute-howto-linkvnet-arm.md)  
- [Template - Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)

本文提供如何將虛擬網路 (Vnet) 連結到 ExpressRoute 線路的概觀。虛擬網路可以位於相同的訂用帳戶中，或屬於另一個訂用帳戶。本文適用於使用傳統部署模型部署的 VNet。如果您想要連結已使用 Azure 資源管理員部署方法進行部署的虛擬網路，您可以使用範本來執行。請參閱上述索引標籤以瀏覽至範本。

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## 組態必要條件

- 您需要最新版的 Azure PowerShell 模組。您可以從 [Azure 下載頁面](http://azure.microsoft.com/downloads)的 PowerShell 區段下載最新的 PowerShell 模組。遵循[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 頁面上的指示，來取得如何設定您的電腦以使用 Azure PowerShell 模組的逐步指引。 
- 開始設定之前，請確定您已經檢閱過[必要條件](expressroute-prerequisites.md)頁面、[路由需求](expressroute-routing.md)頁面和[工作流程](expressroute-workflows.md)頁面。
- 您必須擁有作用中的 ExpressRoute 循環。 
	- 遵循指示來[建立 ExpressRoute 線路](expressroute-howto-circuit-classic.md)，並由您的連線提供者來啟用該線路。 
	- 確定您已針對循環設定了 Azure 私用對等。請參閱[設定路由](expressroute-howto-routing-classic.md)一文，以取得路由指示。 
	- Azure 私用對等必須設定，且在您的網路與 Microsoft 之間的 BGP 對等必須為您啟用端對端連線。

您最多可以將 10 個 VNet 連結到 ExpressRoute 循環。所有的 ExpressRoute 循環都必須位於同一個地理區域。如果您已啟用 ExpressRoute 高階附加元件，則可將更大量的虛擬網路連結到您的 ExpressRoute 循環。如需高階附加元件的詳細資訊，請參閱[常見問題集](expressroute-faqs.md)。

## 將同一個 Azure 訂用帳戶中的 VNet 連結到 ExpressRoute 循環

您可以使用下列 Cmdlet，將虛擬網路連結到 ExpressRoute 循環。執行 Cmdlet 之前，請確定您已建立虛擬網路閘道，並準備好進行連結。

	C:\> New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
	Provisioned

## 將不同 Azure 訂用帳戶中的 VNet 連結到 ExpressRoute 循環

多個訂用帳戶間可共用一個 ExpressRoute 循環。此圖顯示簡單的圖解，示範如何在多個訂用帳戶間共用 ExpressRoute 循環的方式。大型雲端內的每個較小型雲端，會用來代表屬於組織內不同部門的訂用帳戶。組織內的每個部門都可以使用自己的訂用帳戶來部署它們的服務，但可共用單一 ExpressRoute 循環，以連接回內部部署網路。單一部門 (在此範例中：IT) 可以擁有 ExpressRoute 循環。組織內的其他訂用帳戶可以使用 ExpressRoute 電路。

>[AZURE.NOTE]ExpressRoute 循環擁有者需支付專用循環的連線和頻寬費用。所有虛擬網路都會共用相同的頻寬。

![跨訂用帳戶的連線能力](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### 系統管理

「線路擁有者」是訂用帳戶的管理員/共同管理員，而 ExpressRoute 線路即建立於該訂用帳戶中。循環擁有者可以授權其他訂用帳戶的管理員/共同管理員 (工作流程圖中稱為「循環使用者」) 來使用他們擁有的專用循環。一旦獲得使用組織 ExpressRoute 循環的授權，這些循環使用者就可以將其訂用帳戶中的 VNet 連結到 ExpressRoute 循環。

電路擁有者能夠隨時修改及撤銷授權。如果撤銷授權，則在存取權遭撤銷的訂用帳戶中，所有連結均會被刪除。

### 循環擁有者作業 

#### 建立授權
	
電路擁有者可授權其他訂用帳戶管理員使用指定的電路。在下列範例中，循環管理員 (Contoso IT) 藉由指定另一個訂用帳戶的管理員 (Dev-Test) 的 Microsoft 識別碼，使其最多可將 2 個 Vnet 連結到循環。此 Cmdlet 不會傳送電子郵件給指定的 Microsoft ID。電路擁有者必須明確通知其他訂用帳戶擁有者，告知授權已完成。

		PS C:\> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
		
		Description         : Dev-Test Links 
		Limit               : 2 
		LinkAuthorizationId : ********************************** 
		MicrosoftIds        : devtest@contoso.com 
		Used                : 0

#### 檢閱授權

循環擁有者可以藉由執行下列 Cmdlet，來檢閱特定循環上發出的所有授權。

	PS C:\> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : #################################### 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : Dev-Test Links 
	Limit               : 2 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	

#### 更新授權

循環擁有者可以使用下列 Cmdlet 來修改授權。

	PS C:\> set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
		
	Description         : Dev-Test Links 
	Limit               : 5 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : devtest@contoso.com 
	Used                : 0


#### 刪除授權

循環擁有者可以使用下列 Cmdlet 來撤銷/刪除使用者的授權。

	PS C:\> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### 循環使用者作業

#### 檢閱授權

循環使用者可以使用下列 Cmdlet 來檢閱授權。

	PS C:\> Get-AzureAuthorizedDedicatedCircuit
		
	Bandwidth                        : 200
	CircuitName                      : ContosoIT
	Location                         : Washington DC
	MaximumAllowedLinks              : 2
	ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled
	UsedLinks                        : 0

#### 兌換連結授權

循環使用者可以執行下列 Cmdlet 來兌換連結授權。

	PS C:\> New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1' 
		
	State VnetName 
	----- -------- 
	Provisioned SalesVNET1

## 後續步驟

如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

<!---HONumber=AcomDC_0121_2016-->