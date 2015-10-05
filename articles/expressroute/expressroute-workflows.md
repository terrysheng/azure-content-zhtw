<properties
   pageTitle="設定 ExpressRoute 線路的工作流程 | Microsoft Azure"
   description="此頁面會引導您完成設定 ExpressRoute 線路的工作流程"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2015"
   ms.author="cherylmc"/>

# ExpressRoute 線路佈建工作流程和線路狀態
這個頁面以高階觀點引導您完成服務佈建和路由設定的工作流程。

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

上圖和以下對應的步驟顯示佈建端對端 ExpressRoute 線路所必須執行的工作。

1. 使用 PowerShell 來設定 ExpressRoute 線路。如需更多詳細資料，請依照[建立 ExpressRoute 線路](expressroute-howto-circuit-classic.md)一文中的指示進行。

2. 向服務提供者訂購連線能力。此過程視情況而異。如需有關如何訂購連線能力的詳細資訊，請連絡連線提供者。

3. 請透過 PowerShell 驗證 ExpressRoute 線路佈建狀態，以確定線路已佈建成功。

4. 設定路由網域。如果連線提供者為您管理第 3 層，他們會為您的線路設定路由。如果連線提供者只提供第 2 層服務，您必須根據[路由需求](expressroute-routing.md)和[路由組態](expressroute-howto-routing-classic.md)頁面所述的指導方針來設定路由。

	1. 啟用 Azure 私用對等 - 您必須啟用此對等，才能連接到部署在虛擬網路內的 VM / 雲端服務。 
	2. 啟用 Azure 公用對等 - 如果您想要連接到裝載於公用 IP 位址的 Azure 服務，則必須啟用 Azure 公用對等。如果您已選擇啟用 Azure 私用對等的預設路由，則這是存取 Azure 資源的條件。
	3. 啟用 Microsoft 對等 - 您必須啟用此對等，才能存取 Office 365 和 CRM 線上服務。 
	
	>[AZURE.IMPORTANT]如果您要啟用 Microsoft 對等，請確定也啟用 Azure 公用對等來存取 Azure AD。必須確定您使用個別的 Proxy / 邊緣來連接到 Microsoft，而不是您用於網際網路的 Proxy / 邊緣。ExpressRoute 和網際網路使用相同的邊緣會導致路由不對稱，並造成網路連線中斷。


	![](./media/expressroute-workflows/expressroute-routing-workflow.png)

5. 將虛擬網路連結到 ExpressRoute 線路 - 您可以將虛擬網路連結到 ExpressRoute 線路。請依照指示[連結 VNet](expressroute-howto-linkvnets-classic.md) 到您的線路。這些 VNet 可以與 ExpressRoute 線路位於相同的 Azure 訂用帳戶中，也可以在不同的訂用帳戶中。


## ExpressRoute 線路佈建狀態

每個 ExpressRoute 線路有兩種狀態：

- 服務提供者佈建狀態
- Status

Status 代表 Microsoft 的佈建狀態。這個屬性可能是下列其中一種狀態：*Enabled*、*Enabling* 或 *Disabling*。ExpressRoute 線路必須處於 Enabled 狀態，才可供您使用。

連線提供者佈建狀態代表連線提供者那端的狀態。可能是 *NotProvisioned*、*Provisioning* 或 *Provisioned*。ExpressRoute 線路必須處於 Provisioned 狀態，才可供您使用。

### ExpressRoute 線路的可能狀態

本節列出 ExpressRoute 線路的可能狀態。

#### 在建立時

執行 PowerShell Cmdlet 建立 ExpressRoute 線路後，您很快就會看到 ExpressRoute 線路處於以下所述的狀態。

	ServiceProviderProvisioningState : NotProvisioned
	Status                           : Enabled


#### 當連線提供者正在佈建線路時

當您將服務金鑰傳遞給連線提供者且他們也啟動佈建程序時，您快很就會看到 ExpressRoute 線路處於以下所述的狀態。

	ServiceProviderProvisioningState : Provisioning
	Status                           : Enabled


#### 當連線提供者完成佈建程序時

當連線提供者完成佈建程序後，您快很就會看到 ExpressRoute 線路處於以下所述的狀態。

	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled

線路只能處於 Provisioned 和 Enabled 狀態下，才可供您使用。如果您使用第 2 層提供者，則只有當線路處於此狀態下，您才能設定路由。

#### 如果先在 Microsoft 端起始取消佈建

執行 PowerShell Cmdlet 刪除 ExpressRoute 線路後，您很快就會看到 ExpressRoute 線路處於以下所述的狀態。

	ServiceProviderProvisioningState : Provisioning
	Status                           : Disabling

您必須連線連線提供者來取消佈建 ExpressRoute 線路。**重要事項：**Microsoft 會持續收取線路費用，直到您執行 PowerShell Cmdlet 來取消佈建線路為止。

#### 如果在服務提供者那端起始取消佈建

如果您要求服務提供者先取消佈建 ExpressRoute 線路，當服務提供者完成取消佈建程序後，您會看到線路設定為以下所述的狀態。


	ServiceProviderProvisioningState : NotProvisioned
	Status                           : Enabled

如有需要，您可以選擇重新啟用線路，或執行 PowerShell Cmdlet 刪除線路。**重要事項：**Microsoft 會持續收取線路費用，直到您執行 PowerShell Cmdlet 來取消佈建線路為止。


## 路由工作階段組態狀態

BGP 佈建狀態可讓您知道 Microsoft 邊緣是否已啟用 BGP 工作階段。必須是已啟用的狀態，您才能使用對等。

務必檢查 BGP 工作階段狀態，特別是 Microsoft 對等。除了 BGP 佈建狀態，還有另一個狀態稱為*已公告公用首碼狀態*。已公告公用首碼狀態必須是*已設定*狀態，BGP 工作階段才能啟動，路由也才能端對端運作。

如果已公告公用首碼狀態設為*需要驗證*狀態，則不會啟用 BGP 工作階段，因為已公告的首碼不符合任何路由登錄中的 AS 編號。

>[AZURE.IMPORTANT]如果公告的公用首碼狀態是*手動驗證*狀態，您必須向 [Microsoft 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)開啟支援票證，並提供您擁有公告的 IP 位址的證明，以及相關聯的自發系統編號。


## 後續步驟

- 設定 ExpressRoute 連線。
	- [建立 ExpressRoute 線路](expressroute-howto-circuit-classic.md)
	- [設定路由](expressroute-howto-routing-classic.md)
	- [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-classic.md)

<!---HONumber=Sept15_HO4-->