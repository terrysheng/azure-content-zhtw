<properties 
   pageTitle="如何在傳統模式中使用 Azure 入口網站設定靜態私人 IP| Microsoft Azure"
   description="了解靜態私人 IP 以及如何在傳統模式中使用 Azure 入口網站進行管理"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="telmos" />

# 如何在 Azure 入口網站中設定靜態私人 IP 位址 (傳統)

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋之內容包括傳統部署模型。您也可以[管理資源管理員部署模型中的靜態私人 IP 位址](virtual-networks-static-private-ip-arm-pportal.md)。

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

以下的範例步驟會預期已經建立簡單的環境。如果您想要執行如本文件中所顯示的步驟，請先建置[建立 vnet](virtual-networks-create-vnet-classic-pportal.md) 中所說明的測試環境。

## 建立 VM 時如何指定靜態私人 IP 位址
若要在名為 *TestVNet* 之 VNet 的*前端*子網路中建立名為 *DNS01* 的 VM，且靜態私人 IP 為 *192.168.1.101*，請遵循下列步驟：

1. 透過瀏覽器瀏覽至 http://portal.azure.com，並視需要使用您的 Azure 帳戶登入。
2. 按一下 [新增] > [計算] > [Windows Server 2012 R2 Datacenter]，注意 [選取部署模型] 清單已經顯示 [傳統]，然後按一下 [建立]。

	![在 Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure01.png)

3. 在 [建立 VM] 刀鋒視窗中，輸入要建立之 VM 的名稱 (我們的案例中為 *DNS01*)、本機系統管理員帳戶和密碼。

	![在 Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure02.png)

4. 按一下 [選擇性組態] > [網路] > [虛擬網路]，然後按一下 [TestVNet]。如果不能使用 **TestVNet**，請確定您使用*美國中部*位置，並已建立本文開頭所描述的測試環境。

	![在 Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure03.png)

5. 在 [網路] 刀鋒視窗中，請確定目前選取的子網路是*前端*，然後按一下 [IP 位址]，在 [IP 位址指派] 底下，按一下 [靜態] 然後 [IP 位址] 輸入 *192.168.1.101*，如下所示。

	![在 Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure04.png)

6. 按一下 [IP 位址] 刀鋒視窗中的 [確定]，然後按一下 [網路] 刀鋒視窗中的 [確定]，再按一下 [選擇性組態] 刀鋒視窗中的 [確定]。
7. 在 [建立 VM] 刀鋒視窗中，按一下 [建立]。請注意您儀表板中下方顯示的磚。

	![在 Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## 如何擷取 VM 的靜態私人 IP 位址資訊

若要檢視使用上述步驟建立之 VM 的靜態私人 IP 位址資訊，請執行下列步驟。

1. 從 Azure 入口網站按一下 [瀏覽全部] > [虛擬機器 (傳統)] > [DNS01] > [所有設定] > [IP 位址]，並注意 IP 位址指派和 IP 位址，如下所示。

	![在 Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## 如何移除 VM 的靜態私人 IP 位址
若要移除上方建立之 VM 的靜態私人 IP 位址，請遵循下列步驟。
	
1. 從上方顯示的 [IP 位址] 刀鋒視窗按一下 [IP 位址指派] 右側的 [動態]，然後按一下 [儲存]，再按一下 [是]。

	![在 Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## 如何將靜態私人 IP 位址新增至現有的 VM
若要將靜態私人 IP 位址新增至使用上述步驟建立之 VM，請遵循下列步驟：

1. 從上方顯示的 [IP 位址] 刀鋒視窗按一下 [IP 位址指派] 右側的 [靜態]。
2. [IP 位址] 輸入 *192.168.1.101*，然後按一下 [儲存]，再按一下 [是]。

## 後續步驟

- 深入了解[保留的公用 IP](../virtual-networks-reserved-public-ip) 位址。
- 深入了解[執行個體層級公用 IP (ILPIP)](../virtual-networks-instance-level-public-ip) 位址。
- 請參閱[保留 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)。

<!---HONumber=AcomDC_0211_2016-->