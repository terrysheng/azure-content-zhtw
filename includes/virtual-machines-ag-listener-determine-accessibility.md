請務必了解有兩種方式可以在 Azure 中設定可用性群組接聽程式。這些方法的差異在於您建立接聽程式時使用的 Azure 負載平衡器類型。下表描述其差異：

| 負載平衡器 | 實作 | 使用時機： |
| ------------- | -------------- | ----------- |
| **外部** | 使用主控虛擬機器之雲端服務的 [公用虛擬 IP 位址]。 | 您需要從虛擬網路外部存取接聽程式，包括從網際網路。 |
| **內部** | 使用 [內部負載平衡 (ILB)] 搭配接聽程式的私用位址。 | 您只能從相同的虛擬網路內存取接聽程式。這包括混合案例中的站對站 VPN。 |

>[AZURE.IMPORTANT]針對使用雲端服務公用 VIP (外部負載平衡器) 的接聽程式，透過接聽程式傳回的任何資料都會視為輸出，並在 Azure 中依照標準資料傳輸費率收費。即使用戶端位於與接聽程式和資料庫相同的虛擬網路與資料中心也是如此。使用 ILB 的接聽程式情況與此不同。

ILB 只可以在具有區域範圍的虛擬網路上設定。已針對同質群組設定的現有虛擬網路無法使用 ILB。如需詳細資訊，請參閱[內部負載平衡器](../articles/load-balancer/load-balancer-internal-overview.md)。

<!---HONumber=Oct15_HO3-->