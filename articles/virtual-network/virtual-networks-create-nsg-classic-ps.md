<properties 
   pageTitle="如何使用 PowerShell 以傳統模式建立 NSG | Microsoft Azure"
   description="了解如何使用 PowerShell 以傳統模式建立和部署 NSG"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2015"
   ms.author="telmos" />

# 如何在 PowerShell 中建立 NSG (傳統)

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋之內容包括傳統部署模型。您也可以[在資源管理員部署模型中建立 NSG](virtual-networks-create-nsg-arm-ps.md)。

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

以下的範例 PowerShell 命令會預期已根據上述案例建立簡單的環境。如果您想要執行如本文件中所顯示的命令，請先[建立 VNet](virtual-networks-create-vnet-classic-netcfg-ps) 以建置測試環境。

## 如何建立前端子網路的 NSG
若要根據上述案例建立名為 *NSG-FrontEnd* 的 NSG，請依照下列步驟執行。

1. 如果您從未用過 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)，並遵循其中的所有指示登入 Azure，然後選取您的訂用帳戶。

3. 建立名為 **NSG-FrontEnd** 的網路安全性群組。

		New-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" -Location uswest `
		    -Label "Front end subnet NSG"

	預期的輸出：

		Name         Location   Label               
		----         --------   -----               
		NSG-FrontEnd West US 	Front end subnet NSG


4. 建立允許從網際網路存取連接埠 3389 的安全性規則。

		Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
		| Set-AzureNetworkSecurityRule -Name rdp-rule `
		    -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
		    -SourceAddressPrefix Internet  -SourcePortRange '*' `
		    -DestinationAddressPrefix '*' -DestinationPortRange '3389' 

	預期的輸出：

		Name     : NSG-FrontEnd
		Location : Central US
		Label    : Front end subnet NSG
		Rules    : 
		           
		              Type: Inbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           rdp-rule             100       Allow    INTERNET        *             *                3389           TCP     
		           ALLOW VNET INBOUND   65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW AZURE LOAD     65001     Allow    AZURE_LOADBALAN *             *                *              *       
		           BALANCER INBOUND                        CER                                                                   
		           DENY ALL INBOUND     65500     Deny     *               *             *                *              *       
		           
		           
		              Type: Outbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           ALLOW VNET OUTBOUND  65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW INTERNET       65001     Allow    *               *             INTERNET         *              *       
		           OUTBOUND                                                                                                      
		           DENY ALL OUTBOUND    65500     Deny     *               *             *                *              *

4. 建立允許從網際網路存取連接埠 80 的安全性規則。

		Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
		| Set-AzureNetworkSecurityRule -Name web-rule `
		    -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
		    -SourceAddressPrefix Internet  -SourcePortRange '*' `
		    -DestinationAddressPrefix '*' -DestinationPortRange '80' 

	預期的輸出：
		

		Name     : NSG-FrontEnd
		Location : Central US
		Label    : Front end subnet NSG
		Rules    : 
		           
		              Type: Inbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           rdp-rule             100       Allow    INTERNET        *             *                3389           TCP     
		           web-rule             200       Allow    INTERNET        *             *                80             TCP     
		           ALLOW VNET INBOUND   65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW AZURE LOAD     65001     Allow    AZURE_LOADBALAN *             *                *              *       
		           BALANCER INBOUND                        CER                                                                   
		           DENY ALL INBOUND     65500     Deny     *               *             *                *              *       
		           
		           
		              Type: Outbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           ALLOW VNET OUTBOUND  65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW INTERNET       65001     Allow    *               *             INTERNET         *              *       
		           OUTBOUND                                                                                                      
		           DENY ALL OUTBOUND    65500     Deny     *               *             *                *              *   

## 如何建立後端子網路的 NSG
3. 建立名為 **NSG-BackEnd** 的網路安全性群組。

		New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location uswest `
		    -Label "Back end subnet NSG"

	預期的輸出：

		Name        Location   Label              
		----        --------   -----              
		NSG-BackEnd West US    Back end subnet NSG


4. 建立允許從前端子網路存取連接埠 1433 (SQL Server 所使用的預設連接埠) 的安全性規則。

		Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
		| Set-AzureNetworkSecurityRule -Name rdp-rule `
		    -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
		    -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
		    -DestinationAddressPrefix '*' -DestinationPortRange '3389' 

	預期的輸出：

		Name     : NSG-BackEnd
		Location : Central US
		Label    : Back end subnet NSG
		Rules    : 
		           
		              Type: Inbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           fe-rule              100       Allow    192.168.1.0/24  *             *                1433           TCP     
		           ALLOW VNET INBOUND   65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW AZURE LOAD     65001     Allow    AZURE_LOADBALAN *             *                *              *       
		           BALANCER INBOUND                        CER                                                                   
		           DENY ALL INBOUND     65500     Deny     *               *             *                *              *       
		           
		           
		              Type: Outbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           ALLOW VNET OUTBOUND  65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW INTERNET       65001     Allow    *               *             INTERNET         *              *       
		           OUTBOUND                                                                                                      
		           DENY ALL OUTBOUND    65500     Deny     *               *             *                *              *      

4. 建立封鎖從子網路存取網際網路的安全性規則。

		Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
		| Set-AzureNetworkSecurityRule -Name block-internet `
		    -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
		    -SourceAddressPrefix '*'  -SourcePortRange '*' `
		    -DestinationAddressPrefix Internet -DestinationPortRange '*' 

	預期的輸出：

		Name     : NSG-BackEnd
		Location : Central US
		Label    : Back end subnet NSG
		Rules    : 
		           
		              Type: Inbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           fe-rule              100       Allow    192.168.1.0/24  *             *                1433           TCP     
		           ALLOW VNET INBOUND   65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW AZURE LOAD     65001     Allow    AZURE_LOADBALAN *             *                *              *       
		           BALANCER INBOUND                        CER                                                                   
		           DENY ALL INBOUND     65500     Deny     *               *             *                *              *       
		           
		           
		              Type: Outbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           block-internet       200       Deny     *               *             INTERNET         *              *       
		           ALLOW VNET OUTBOUND  65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW INTERNET       65001     Allow    *               *             INTERNET         *              *       
		           OUTBOUND                                                                                                      
		           DENY ALL OUTBOUND    65500     Deny     *               *             *                *              *   

<!---HONumber=Oct15_HO3-->