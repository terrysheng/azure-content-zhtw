<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#如何連接虛擬機器與虛擬網路或雲端服務

虛擬機器必須在雲端服務中做為容器，並提供唯一的公用 DNS 名稱、公用 IP 位址，以及一組透過網際網路存取虛擬機器的端點。雲端服務可以選擇性地位於虛擬網路。 

如果雲端服務不在虛擬網路中，該雲端服務中的虛擬機器只能與透過其他虛擬機器的公用 DNS 名稱之其他虛擬機器通訊，該流量會透過網際網路傳送。如果雲端服務是在虛擬網路中，該雲端服務中的虛擬機器可與虛擬網路中的所有其他虛擬機器通訊，而不需要透過網際網路傳送任何流量。

如果您將虛擬機器放在相同的獨立雲端服務，您可以利用負載平衡和可用性集。如需詳細資訊，請參閱[負載平衡虛擬機器](../../articles/load-balance-virtual-machines/)和[管理虛擬機器的可用性](../../articles/manage-availability-virtual-machines/)。不過，您無法組織子網路上的虛擬機器，或將獨立雲端服務連線到內部部署網路。下列是一個範例。

![Virtual machines in a standalone cloud service](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)
 
如果您將虛擬機器放在虛擬網路，您可以決定要使用多少雲端服務來利用負載平衡和可用性集。此外，您可以利用內部部署網路的相同方式，在子網路上組織虛擬機器，並將虛擬網路連線到內部部署網路。下列是一個範例。

![Virtual machines in a virtual network](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

若要在 Azure 中連線虛擬機器，建議使用虛擬網路。最佳作法是在個別的雲端服務中設定應用程式的每一層。這可透過角色型存取控制 (RBAC) 委派進階使用者權限。如需詳細資訊，請參閱 [Azure 預覽入口網站中的角色型存取控制](../../articles//role-based-access-control-configure/)。不過，您可能需要將不同應用程式層的部分虛擬機器結合至相同的雲端服務，以維持在每個訂閱最多有 200 項雲端服務的限制內。

若要連線虛擬網路中的虛擬機器：

1.	在 Azure 管理入口網站中建立虛擬網路。如需詳細資訊，請參閱[虛擬網路組態工作](https://msdn.microsoft.com/library/azure/jj156206.aspx)。
2.	使用 [**新增 > 計算 > 雲端服務 > 自訂建立**]，在 Azure 管理入口網站中為您的部署建立雲端服務集，以反映您的可用性集和負載平衡的設計。
3.	當您建立每個新的虛擬機器時，請指定正確的雲端服務和虛擬網路。如果雲端服務已與虛擬網路相關聯，則已經會為您選取其名稱。

以下是使用 Azure 管理入口網站的範例。

![Selecting a cloud service for a virtual machine](./media/howto-connect-vm-cloud-service/VMConfig1.png)
 
若要在獨立雲端服務中連接虛擬機器：

1.	使用 [**新增 > 計算 > 雲端服務 > 自訂建立**]，在 Azure 管理入口網站中為您的部署建立雲端服務。
2.	當您建立虛擬機器時，指定步驟 1 中建立的雲端服務名稱。 
或者，當您建立第一部虛擬機器時，您可以為您的部署建立雲端服務，。

下列範例為名為 EndpointTest 的現有雲端服務使用 Azure 管理入口網站。
 
![Add a virtual machine to an existing cloud service](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##資源
[虛擬機器負載平衡](../../articles/load-balance-virtual-machines/)

[管理虛擬機器的可用性](../../articles/manage-availability-virtual-machines/)

[虛擬網路組態工作](https://msdn.microsoft.com/library/azure/jj156206.aspx)

建立虛擬機器後，最好要新增資料磁碟，您的服務和工作負載才有地方可存放資料。執行下列其中一項：

[如何將資料磁碟連接至 Linux 虛擬機器](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[如何將資料磁碟連接至 Windows 虛擬機器](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/)


<!--HONumber=45--> 
