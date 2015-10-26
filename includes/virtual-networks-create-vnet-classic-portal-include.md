## 如何在 Azure 入口網站中建立 VNet

若要根據上述案例建立 VNet，請依照下列步驟執行。

1. 透過瀏覽器瀏覽至 http://manage.windowsazure.com，並視需要使用您的 Azure 帳戶登入。
2. 依序按一下 [**新增**] > [**網路服務**] > [**虛擬網路**] > [**自訂建立**]，如下圖所示。

	![在入口網站中建立 VNet](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)

3. 在 [**虛擬網路詳細資料**] 頁面上，鍵入 VNet 的**名稱**，選取其**位置**，然後按一下頁面右下角的箭頭，進行步驟 2。下圖顯示本文案例的設定。

	![虛擬網路詳細資料頁面](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)

4. 在 [**DNS 伺服器和 VPN 連線能力**] 頁面上，指定最多 9 組要使用的 DNS 伺服器名稱和 IP 位址。如果您不指定 DNS 伺服器，VNet 會使用 Azure 提供的內部命名解決方案。在我們的案例中不會設定 DNS 伺服器。
5. 如果您要提供 VNet 的點對站 VPN 存取，請啟用 [**設定點對站 VPN**] 核取方塊。如果您並未設定點對站 VPN，可在建立點對站 VPN 後隨時新增至 VNet。在我們的案例中不會設定點對站 VPN。
6. 如果您要提供 VNet 與另一個 VNet 或內部部署網路之間的站對站 VPN 連線能力，請啟用 [**設定站對站 VPN**] 核取方塊，並指定是否要使用 **ExpressRoute**，以及指定要連接的網路名稱。如果您並未設定站對站 VPN，建立之後隨時可以將站對站 VPN 新增至 VNet。在我們的案例中不會設定站對站 VPN。
7. 按一下頁面右下角的箭頭，繼續進行步驟 3。下圖顯示我們案例的設定。

	![DNS 伺服器和 VPN 連線能力頁面](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)

8. 在 [**虛擬網路位址空間**] 頁面的 [**起始 IP**]下，按一下 [*10.0.0.0*] 變更 VNet 位址空間，然後鍵入您要使用的起始位址空間。在我們的案例中是鍵入 *192.168.0.0*。
9. 在 [**CIDR (位址計數)**] 下，選取子網路遮罩的位元數。在我們的案例中是選取 [*16 (65536)*]。
10. 在 [**子網路**] 下，按一下 [*Subnet-1*]，並視需要重新命名子網路。在我們的案例中會將子網路重新命名為 *FrontEnd*。

	>[AZURE.NOTE]如果按一下子網路名稱文字方塊以外的區域，您將無法再次編輯子網路的名稱。若要修正此問題，您需要按一下子網路右邊的 X 按鈕移除子網路，然後如以下步驟 13 所述新增子網路。

11. 在第一個子網路的 [**起始 IP**] 下，指定子網路的起始 IP 位址。在我們的案例中是鍵入 *192.168.1.0*。
12. 在 [**CIDR (位址計數)**] 下，選取第一個子網路的子網路遮罩位元數。在我們的案例中是選取 [*24 (256)*]。
13. 按一下 [**新增子網路**]，視需要新增子網路。在我們的案例中會新增子網路，然後重複步驟 10 到 12 設定 VNet，如下圖所示。

	![虛擬網路位址空間頁面](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)

14. 按一下頁面右下角的核取記號按鈕建立 VNet。幾秒鐘後，VNet 會顯示在可用 VNet 清單中，如下圖所示。

	![新的虛擬網路](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)

<!---HONumber=Oct15_HO3-->