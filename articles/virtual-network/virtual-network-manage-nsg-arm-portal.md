<properties 
   pageTitle="在資源管理員中使用 Preview 入口網站管理 NSG | Microsoft Azure"
   description="了解如何在資源管理員中使用 Preview 入口網站管理管理現有的 NSG"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="telmos" />

# 使用 Preview 入口網站管理 NSG

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## 擷取資訊

您可以檢視您現有的 NSG、擷取現有 NSG 的規則，並找出與 NSG 相關聯的資源。

### 檢視現有的 NSG
若要檢視訂用帳戶中所有現有的 NSG，請遵循下列步驟。

1. 透過瀏覽器瀏覽至 http://portal.azure.com，並視需要使用您的 Azure 帳戶登入。
2. 按一下 [瀏覽] > [網路安全性群組]。

![Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. 查看 [網路安全性群組] 刀鋒視窗中的 NSG 清單。

![Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

若要檢視 **RG-NSG** 資源群組中的 NSG 清單，請遵循下列步驟。

1. 按一下[資源群組] > [RG-NSG] > [...]。

![Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. 在資源清單中，尋找顯示 NSG 圖示的項目，如下圖 [資源] 刀鋒視窗所示。

![Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure4.png)
		 
### 列出 NSG 的所有規則

若要檢視名為 **NSG-FrontEnd** 的 NSG 的規則，請遵循下列步驟。

1. 在 [網路安全性群組] 刀鋒視窗或前文提及的 [資源] 刀鋒視窗中，按一下 [NSG-FrontEnd]。
2. 在 [設定] 索引標籤中，按一下 [輸入安全性規則]。

![Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. [輸入安全性規則] 刀鋒視窗隨即會出現，如下圖所示。

![Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. 在 [設定] 索引標籤中，按一下 [輸出安全性規則] 以查看輸出規則。

>[AZURE.NOTE] 若要檢視預設規則，請按一下顯示規則之刀鋒視窗頂端的 [預設規則] 圖示。

### 檢視 NSG 關聯

若要檢視與 **NSG-FrontEnd** NSG 相關聯的資源，請遵循下列步驟。

1. 在 [網路安全性群組] 刀鋒視窗或前文提及的 [資源] 刀鋒視窗中，按一下 [NSG-FrontEnd]。
2. 在 [設定] 索引標籤中，按一下 [子網路] 以檢視與 NSG 相關聯的子網路。

![Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. 在 [設定] 索引標籤中，按一下 [網路介面] 以檢視與 NSG 相關聯的 NIC。

## 管理規則

您可以將規則新增至現有的 NSG、編輯現有的規則，以及移除規則。

### 新增規則

若要新增規則，允許來自任何電腦的連接埠 **443** 的**輸入**流量流向 **NSG-FrontEnd** NSG，請依照下列步驟進行。

1. 在 [網路安全性群組] 刀鋒視窗或前文提及的 [資源] 刀鋒視窗中，按一下 [NSG-FrontEnd]。
2. 在 [設定] 索引標籤中，按一下 [輸入安全性規則]。
3. 在 [輸入安全性規則] 刀鋒視窗中，按一下 [新增]。接著，在 [新增輸入安全性規則] 刀鋒視窗中，依照下圖所示填入值，然後按一下 [確定]。

![Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

4. 幾秒鐘後，請注意 [輸入安全性規則] 刀鋒視窗中的新規則。

![Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### 變更規則

若要變更以上所建立的規則，僅允許來自**網際網路**的輸入流量，請依照下列步驟進行。

1. 在 [網路安全性群組] 刀鋒視窗或前文提及的 [資源] 刀鋒視窗中，按一下 [NSG-FrontEnd]。
2. 在 [設定] 索引標籤中，按一下在前文中建立的規則。
3. 在 [allow-https] 刀鋒視窗中，依照下圖所示變更 [來源] 屬性，然後按一下 [儲存]。

![Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### 刪除規則

若要刪除在前文中建立的規則，請遵循下列步驟。

1. 在 [網路安全性群組] 刀鋒視窗或前文提及的 [資源] 刀鋒視窗中，按一下 [NSG-FrontEnd]。
2. 在 [設定] 索引標籤中，按一下在前文中建立的規則。
3. 在 [allow-https] 刀鋒視窗中，依序按一下 [刪除] 和 [是]。

![Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## 管理關聯

您可以建立 NSG 與子網路和 NIC 的關聯。您也可以中斷 NSG 與其相關聯的任何資源的關聯。

### 建立 NSG 與 NIC 的關聯

若要建立 **NSG-FrontEnd** NSG 與 **TestNICWeb1** NIC 的關聯，請依照下列步驟進行。

1. 在 [網路安全性群組] 刀鋒視窗或前文提及的 [資源] 刀鋒視窗中，按一下 [NSG-FrontEnd]。
2. 在 [設定] 索引標籤中，按一下 [網路介面] > [關聯] > [TestNICWeb1]。

![Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### 中斷 NSG 與 NIC 的關聯

若要中斷 **NSG-FrontEnd** NSG 與 **TestNICWeb1** NIC 的關聯，請依照下列步驟進行。

1. 從 Azure 入口網站中，按一下 [資源群組] > [RG-NSG] > [...] > [TestNICWeb1]。
2. 在 [TestNICWeb1] 刀鋒視窗中，按一下 [變更安全性...] > [無]。

![Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

>[AZURE.NOTE] 您也可以使用此刀鋒視窗來建立 NIC 與任何現有 NSG 之間的關聯。

### 中斷 NSG 與子網路的關聯

若要中斷 **NSG-FrontEnd** NSG 與 **FrontEnd** 子網路的關聯，請依照下列步驟進行。

1. 從 Azure 入口網站中，按一下 [資源群組] > [RG-NSG] > [...] > [TestVNet]。
2. 在 [設定] 刀鋒視窗中，按一下 [子網路] > [FrontEnd] > [網路安全性群組] > [無]。

![Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. 在 [FrontEnd] 刀鋒視窗中，按一下 [儲存]。

![Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### 建立 NSG 至子網路的關聯

若要再次建立 **NSG-FrontEnd** NSG 與 **FronEnd** 子網路的關聯，請依照下列步驟進行。

1. 從 Azure 入口網站中，按一下 [資源群組] > [RG-NSG] > [...] > [TestVNet]。
2. 在 [設定] 刀鋒視窗中，按一下 [子網路] > [FrontEnd] > [網路安全性群組] > [NSG-FrontEnd]。
3. 在 [FrontEnd] 刀鋒視窗中，按一下 [儲存]。

>[AZURE.NOTE] 您也可以從 NSG 的 [設定] 刀鋒視窗建立 NSG 與子網路之間的關聯。

## 刪除 NSG

您只能刪除與任何資源沒有關聯的 NSG。若要刪除 NSG，請依照下列步驟進行。

1. 從 Azure 入口網站中，按一下 [資源群組] > [RG-NSG] > [...] > [NSG-FrontEnd]。
2. 在 [設定] 刀鋒視窗中，按一下 [網路介面]。
3. 如果有列出任何 NIC，請按一下 NIC，然後遵循[中斷 NSG 與 NIC 的關聯](#Dissociate-an-NSG-from-a-NIC)中的步驟 2。
4. 針對每個 NIC 重複步驟 3。
5. 在 [設定] 刀鋒視窗中，按一下 [子網路]。
6. 如果有列出任何子網路，請按一下子網路，然後遵循[中斷 NSG 與子網路的關聯](#Dissociate-an-NSG-from-a-subnet)中的步驟 2 和 3。
7. 向左捲動到 [NSG-FrontEnd] 刀鋒視窗，然後按一下 [刪除] > [是]。

[Azure 入口網站 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## 後續步驟

- [啟用 NSG 的記錄](virtual-network-nsg-manage-log.md)。

<!---HONumber=AcomDC_0323_2016-->