

![獨立雲端服務中的虛擬機器](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

如果您將虛擬機器放在虛擬網路，您可以決定要將多少雲端服務用於負載平衡和可用性集。此外，您可以利用內部部署網路的相同方式，在子網路上組織虛擬機器，並將虛擬網路連線到內部部署網路。以下是範例：

![虛擬網路中的虛擬機器](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

若要在 Azure 中連線虛擬機器，建議使用虛擬網路。最佳作法是在個別的雲端服務中設定應用程式的每一層。不過，您可能需要將不同應用程式層的部分虛擬機器結合至相同的雲端服務，以維持在每個訂用帳戶最多有 200 項雲端服務的限制內。若要檢閱本限制和其他限制，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](../articles/azure-subscription-service-limits.md)。

## 連接虛擬網路中的 VM

若要連線虛擬網路中的虛擬機器：

1.	在 [Azure 入口網站](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md)中建立虛擬網路。
2.	為部署一組建立雲端服務，以反映可用性設定組和負載平衡的設計。在 Azure 傳統入口網站中，針對每一個雲端服務，按一下 **[新增] > [計算] > [雲端服務] > [自訂建立]**。
3.	若要逐一建立新的虛擬機器，請按一下 **[新增] > [計算] > [虛擬機器] > [從資源庫]**。為 VM 選擇正確的雲端服務和虛擬網路。如果雲端服務已加入虛擬網路，系統會為您選取服務名稱。

![選取虛擬機器的雲端服務](./media/virtual-machines-common-classic-connect-vms/VMConfig1.png)

## 連接獨立雲端服務中的 VM

若要在獨立雲端服務中連接虛擬機器：

1.	在 [Azure 傳統入口網站](http://manage.windowsazure.com)中建立雲端服務。按一下 [**新增 > 計算 > 雲端服務 > 自訂建立**]。或者，當您建立第一部虛擬機器時，您可以為您的部署建立雲端服務。

2.	建立虛擬機器時，請選擇在上一個步驟中所建立雲端服務的名稱。

	![將虛擬機器加入至現有的雲端服務。](./media/virtual-machines-common-classic-connect-vms/Connect-VM-to-CS.png)

<!---HONumber=AcomDC_0330_2016-->