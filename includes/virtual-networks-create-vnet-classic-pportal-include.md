## 如何在 Azure Preview 入口網站中建立傳統 VNet

若要根據上述案例建立傳統 VNet，請依照下列步驟執行。

1. 透過瀏覽器瀏覽 http://portal.azure.com，並視需要使用您的 Azure 帳戶登入。
2. 依序按一下 [**新增**] > [**網路**] > [**虛擬網路**]，注意 [**選取部署模型**] 清單是否已經顯示為 [**傳統**]，然後再按一下 [**建立**]，如下圖所示。

	![在 Preview 入口網站中建立 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)

3. 在 [**虛擬網路**] 刀鋒視窗上，鍵入 VNet 的**名稱**，然後再按一下 [**位址空間**]。設定 VNet 和其第一個子網路的位址空間設定，然後按一下 [**確定**]。下圖顯示我們案例的 CIDR 區塊設定。

	![位址空間刀鋒視窗](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)

4. 按一下 [**資源群組**]，選取要新增 VNet 的資源群組，或按一下 [**建立新的資源群組**]，將 VNet 新增到新的資源群組。下圖顯示新資源群組 **TestRG** 的資源群組設定。如需資源群組的詳細資訊，請造訪 [Azure 資源管理員概觀](resource-group-overview.md/#resource-groups)。

	![建立資源群組刀鋒視窗](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)

5. 如有必要，請變更您 VNet 的 [**訂用帳戶**] 和 [**位置**] 設定。

6. 如果您不想在「**開始面板**」上看到 VNet 磚，請停用 [**釘選到「開始面板」**]。

7. 按一下 [**建立**]，並注意名為 [**建立虛擬網路**] 的磚，如下圖所示。

	![在入口網站中建立 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)

8. 等候建立 VNet，然後在看到下方的磚之後，按一下以新增更多子網路。

	![在入口網站中建立 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)

9. 您應該會看到 VNet 的 [**組態**]，如下所示。

	![在入口網站中建立 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)

10. 依序按一下 [**子網路**] > [**新增**]，然後鍵入 [**名稱**]，並指定您子網路的 [**位址範圍 (CIDR 區塊)**]，然後按一下 [**確定**]。下圖顯示我們目前案例的設定。

	![在 Preview 入口網站中建立 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

<!---HONumber=Oct15_HO3-->