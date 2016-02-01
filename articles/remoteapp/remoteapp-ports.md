
<properties
    pageTitle="加入客戶虛擬網路中部署的 Azure RemoteApp 白名單的連接埠和 URL 清單 | Microsoft Azure"
    description="了解您必須設定哪些連接埠及 URL，才能透過 Azure RemoteApp 進行通訊。"
    services="remoteapp"
	documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/19/2016"
    ms.author="elizapo" />



# 允許存取客戶虛擬網路中部署的 Azure RemoteApp 的連接埠和 URL 清單 

以下適用於雲端或混合式集合 Azure RemoteApp (如果您要在虛擬網路 (VNET) 中部署它)。如需虛擬網路的詳細資訊，請閱讀[虛擬網路概觀](virtual-networks-overview.md)。如果您已經建立網路安全性群組 (NSG) 來限制流向您針對 Azure RemoteApp 選擇的虛擬網路資源的流量，請確定可透過虛擬網路上的安全性原則存取並允許以下項目。如需網路安全性群組的詳細資訊，請參閱[什麼是網路安全性群組 (NSG)？](virtual-networks-nsg.md)。

##  Azure RemoteApp 子網路必須存取以下端點和 URL： 
*	**.servicebus.windows.net
*	 **.servicebus.net
*	 https://*.remoteapp.windwsazure.com  
*	 https://www.remoteapp.windowsazure.com 
*	 https://*remoteapp.windowsazure.com  
*	 https://*.core.windows.net  
*	 輸出：TCP：443、TCP：10101-10175 
*	 選用 – UDP：10201-10275 
 
## Azure RemoteApp 用戶端必須存取以下端點和 URL： 

這裡的用戶端指的是使用者用來連線到 Azure RemoteApp 集合中部署應用程式的桌上型電腦、裝置等等。

-  https://telemetry.remoteapp.windowsazure.com  
-  https://**.remoteapp.windowsazure.com (選用的 UDP 連接埠是供此位址使用) 
-  https://login.windows.net  
-  https://login.microsoftonline.com  
-  https://www.remoteapp.windowsazure.com 
-  https://**.remoteapp.windowsazure.com  
-  https://*.core.windows.net  
-  輸出：TCP：443  
-  選用 - UDP：10201-10275

<!---HONumber=AcomDC_0121_2016-->