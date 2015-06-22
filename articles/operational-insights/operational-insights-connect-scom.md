<properties 
   pageTitle="從 System Center Operations Manager 連接至 Operational Insights" 
   description="深入了解如何透過 Operations Manager 連接到 Operational Insights." 
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# 從 System Center Operations Manager 連接至 Operational Insights 

您可以將 Operational Insights 連線至現有的 System Center Operations Manager 環境。這可讓您將現有的 Operations Manager 代理程式做為 Operational Insights 代理程式。

 >[AZURE.NOTE] 使用 Operations Manager 2012 SP1 UR6 及 Operations Manager 2012 R2 UR2 支援 Operational Insights。SCOM 2012 SP1 UR7 和 SCOM 2012 R2 UR3 中已加入 Proxy 支援。

## 將 SCOM 連接到 Operational Insights 並加入代理程式

1. 在 Operations Manager 主控台中，按一下 [**管理**]。

2. 展開 [**Operational Insights**] 節點，然後按一下 [**Operational Insights 連線**]。

3. 按一下 [**註冊至 Operational Insights**] 連結，並遵循畫面上的指示執行。 

4. 完成註冊精靈之後，按一下 [**新增電腦/群組**]。

5. 在 [**電腦搜尋**] 對話方塊中，您可以搜尋 Operations Manager 監視的電腦或群組。選取電腦或群組以連線至 Operational Insights，按一下 [**新增**]，然後按一下 [**確定**]。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

[設定 Proxy 和防火牆設定 (選擇性)](https://msdn.microsoft.com/library/azure/dn884643.aspx)


<!--HONumber=52--> 