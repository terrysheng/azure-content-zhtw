<properties 
   pageTitle="如何在 ARM 模式中使用預覽入口網站設定靜態私人 IP| Microsoft Azure"
   description="了解靜態 IP (DIP) 如何在 ARM 模式中使用預覽入口網站管理它們"
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
   ms.date="09/08/2015"
   ms.author="telmos" />

# 如何在預覽入口網站中設定靜態私人 IP 位址

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋之內容包括資源管理員部署模型。您也可以[管理傳統部署模型中的靜態私人 IP 位址](virtual-networks-static-private-ip-classic-pportal.md)。

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

以下的範例步驟會預期已經建立簡單的環境。如果您想要執行如本文件中所顯示的步驟，請先建置[建立 vnet](virtual-networks-create-vnet-arm-pportal.md) 中所說明的測試環境。

## 如何建立用來測試靜態私人 IP 位址的 VM

您無法在資源管理員部署模式中建立 VM 期間，使用預覽入口網站設定靜態私人 IP 位址。您必須先建立 VM，才能將其私人 IP 設定為靜態。

若要在名為 *TestVNet* 之 VNet 的*前端*子網路中建立名為 *DNS01* 的 VM，請遵循下列步驟。

1. 透過瀏覽器瀏覽至 http://portal.azure.com，並視需要使用您的 Azure 帳戶登入。
2. 按一下 [新增] > [計算] > [Windows Server 2012 R2 Datacenter]，注意 [選取部署模型] 清單已經顯示 [資源管理員]，然後按一下 [建立]，如下圖所示。

	![在預覽入口網站中建立 VM](./media/virtual-networks-static-ip-arm-pportal/figure01.png)

3. 在 [基本概念] 刀鋒視窗中，輸入要建立之 VM 的名稱 (我們的案例中為 *DNS01*)、本機系統管理員帳戶和密碼，如下圖所示。

	![基本概念刀鋒視窗](./media/virtual-networks-static-ip-arm-pportal/figure02.png)

4. 請確定選取的 [位置] 為*美國中部*，按一下 [資源群組] 底下的 [選取現有項目]，然後再按一次 [資源群組]，按一下 [TestRG]，再按一下 [確定]。

	![基本概念刀鋒視窗](./media/virtual-networks-static-ip-arm-pportal/figure03.png)

5. 在 [大小選擇] 刀鋒視窗中，選取 [A1 標準]，再按一下 [選取]。

	![選擇尺寸刀鋒視窗](./media/virtual-networks-static-ip-arm-pportal/figure04.png)

6. 在 [設定] 刀鋒視窗中，確定下列屬性都已設定為下列的值，然後按一下 [確定]。

	-**儲存體帳戶**: *vnetstorage*
	- **網路**: *TestVNet*
	- **子網路**: *前端*

	![選擇尺寸刀鋒視窗](./media/virtual-networks-static-ip-arm-pportal/figure05.png)

7. 在 [摘要] 刀鋒視窗中，按一下 [確定]。請注意您儀表板中下方顯示的磚。

	![在預覽入口網站中建立 VM](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## 如何擷取 VM 的靜態私人 IP 位址資訊

若要檢視使用上述步驟建立之 VM 的靜態私人 IP 位址資訊，請執行下列步驟。

1. 從 Azure Preview 入口網站按一下 [瀏覽全部] > [虛擬機器] > [DNS01] > [所有設定] > [網路介面]，然後按一下所列出的唯一網路介面。

	![部署 VM 磚](./media/virtual-networks-static-ip-arm-pportal/figure07.png)

2. 在 [網路介面] 刀鋒視窗中，按一下 [所有設定] > [IP 位址] 並注意 [指派] 與 [IP 位址] 的值。

	![部署 VM 磚](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## 如何將靜態私人 IP 位址新增至現有的 VM
若要將靜態私人 IP 位址新增至使用上述步驟建立之 VM，請遵循下列步驟：

1. 從上方顯示的 [IP 位址] 刀鋒視窗中，按一下 [指派] 底下的[靜態]。
2. [IP 位址] 輸入 *192.168.1.101*，然後按一下 [儲存]。

	![在預覽入口網站中建立 VM](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

>[AZURE.NOTE]如果按一下 [儲存] 之後您注意到指派仍然設定為 [動態]，這表示您輸入的 IP 位址已在使用。請嘗試不同的 IP 位址。

## 如何移除 VM 的靜態私人 IP 位址
若要移除上方建立之 VM 的靜態私人 IP 位址，請遵循下列步驟。
	
1. 從上方顯示的 [IP 位址] 刀鋒視窗中，按一下 [指派] 底下的[動態]，然後按一下 [儲存]。

## 後續步驟

- 深入了解[保留的公用 IP](../virtual-networks-reserved-public-ip) 位址。
- 深入了解[執行個體層級公用 IP (ILPIP)](../virtual-networks-instance-level-public-ip) 位址。
- 請參閱[保留 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)。

<!---HONumber=Oct15_HO3-->