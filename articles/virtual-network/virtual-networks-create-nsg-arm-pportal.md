<properties 
   pageTitle="如何在 ARM 模式中使用 Preview 入口網站建立 NSG | Microsoft Azure"
   description="了解如何在 ARM 中使用 Preview 入口網站建立和部署 NSG"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2015"
   ms.author="telmos" />

# 如何使用 Preview 入口網站管理 NSG

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋之內容包括資源管理員部署模型。您也可以[在傳統部署模型中建立 NSG](virtual-networks-create-nsg-classic-ps.md)。

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## 使用按一下即部署來部署 ARM 範本

在此階段中，您無法從預覽建立 NSG。但是可以管理現有的 NSG。您必須先使用公用儲存機制中可用的範例範本來建立上述案例中描述的資源，才能管理 NSG。部署[此範本](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG)，按一下 [部署至 Azure]，視情況取代預設參數值，再依循入口網站中的指示。

## 建立現有 NSG 中的規則

若要從 Preview 入口網站建立現有 NSG 中的規則，依照下列步驟執行。

1. 透過瀏覽器瀏覽至 http://portal.azure.com，並視需要使用您的 Azure 帳戶登入。
2. 按一下 [瀏覽] > [網路安全性群組]。

![Preview 入口網站 - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure1.png)

3. 在 NSG 清單中按一下 [NSG-FrontEnd] > [輸入安全性規則]

![Preview 入口網站 - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. 在 [輸入安全性規則] 清單中，按一下 [新增]。

![Preview 入口網站 - 加入規則](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. 在 [新增輸入安全性規則] 刀鋒視窗中，建立一個名為 [web-rule] 的規則，優先順序設為 [200]，以允許從任何來源經由 [TCP] 與連接埠 [80] 存取任何 VM，然後按一下 [確定]。請注意，這些設定大部分已是預設值。

![Preview 入口網站 - 規則設定](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. 數秒後，您會看到 NSG 中的新規則。

![Preview 入口網站 - 新增規則](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

<!---HONumber=Sept15_HO4-->