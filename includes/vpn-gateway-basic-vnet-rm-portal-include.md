若要使用 Azure 入口網站建立 VNet，請遵循下列步驟。請注意，已提供螢幕擷取畫面做為範例。請務必將值取代為您自己的值。

如需使用虛擬網路的詳細資訊，請參閱[虛擬網路概觀](../articles/virtual-network/virtual-networks-overview.md)。

1. 透過瀏覽器瀏覽至 [Azure 入口網站](http://portal.azure.com)，並視需要使用您的 Azure 帳戶登入。

2. 按一下 [新增] **>** [網路] **>** [虛擬網路]。

	![VNetBlade](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal650.png)

3. 從接近 [虛擬網路] 刀鋒視窗底部的 [選取部署模型] 清單中，選取 [資源管理員]，然後按一下 [建立]。


	![選取資源管理員](./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png)

4. 在 [建立虛擬網路] 刀鋒視窗中進行 VNet 設定。在此刀鋒視窗中，您將新增您的第一個位址空間和單一子網路位址範圍。完成 VNet 建立之後，您可以返回並新增其他子網路和位址空間。這是入口網站目前的一項限制。在入口網站中編輯 VNet 屬性，或使用 PowerShell，即可隨時返回來更新這些值。您使用的值將取決於您要建立的設定。請務必參考您計劃的組態值。

	![建立虛擬網路的刀鋒視窗](./media/vpn-gateway-basic-vnet-rm-portal-include/createavnet250.png)

5. 確認 [訂用帳戶] 正確無誤。您可以使用下拉式清單變更訂用帳戶。

6. 按一下 [資源群組] 並選取現有的資源群組，或輸入新的資源群組名稱以建立新的資源群組。如果您要建立新的群組，請根據您計劃的組態值來命名資源群組。如需資源群組的詳細資訊，請造訪 [Azure 資源管理員概觀](resource-group-overview.md/#resource-groups)。

7. 接著，選取 VNet 的 [位置] 設定。請注意，此位置會決定您將部署到此 VNet 的資源所在的位置。若未重新部署您的資源，之後便無法變更此位置。

8. 如果您想要能夠在儀表板上輕鬆地尋找您的 VNet，請選取 [釘選到儀表板]，然後按一下 [建立]。
	
	![釘選到儀表板](./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png)


9. 按一下 [建立] 之後，您會看到儀表板上有一個圖格會反映 VNet 的進度。建立 VNet 時，此圖格將會變更。

	![建立虛擬網路磚](./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png)

<!---HONumber=AcomDC_0406_2016-->