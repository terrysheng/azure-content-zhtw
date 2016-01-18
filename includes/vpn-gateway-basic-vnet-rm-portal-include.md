若要透過 Azure 入口網站依據上述案例建立 VNet，請依照下列步驟執行。

1. 透過瀏覽器瀏覽至 http://portal.azure.com，並視需要使用您的 Azure 帳戶登入。

2. 依序按一下 [新增] > [網路] > [虛擬網路]，然後按一下 [選取部署模型] 清單中的 [資源管理員]，再按一下 [建立]。

3. 如下圖所示，在 [**建立虛擬網路**] 刀鋒視窗中進行 VNet 設定。

	![建立虛擬網路的刀鋒視窗](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. 按一下 [**資源群組**]，選取要加入 VNet 的目的地資源群組，或按一下 [**新建**]，將 VNet 加入至新的資源群組。下圖顯示新資源群組 **TestRG** 的資源群組設定。如需資源群組的詳細資訊，請造訪 [Azure 資源管理員概觀](resource-group-overview.md/#resource-groups)。

	![資源群組](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. 如有必要，請變更您 VNet 的 [**訂用帳戶**] 和 [**位置**] 設定。

6. 按一下 [**建立**]，並注意名為 [**建立虛擬網路**] 的磚，如下圖所示。

	![建立虛擬網路磚](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

7. 等候 VNet 建立完成，然後在 [虛擬網路] 刀鋒視窗中，依序按一下 [所有設定] > [子網路] > [新增]。

8. 如下方所示，指定 *BackEnd* 子網路的子網路設定，然後按一下 [**確定**]。

	![子網路設定](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

9. 檢視子網路清單。

	![VNet 中的子網路清單](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)

<!---HONumber=AcomDC_0107_2016-->