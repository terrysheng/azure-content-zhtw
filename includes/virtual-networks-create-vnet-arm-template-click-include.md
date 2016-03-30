## 使用按一下即部署來部署 ARM 範本

您可以重複使用已上傳至由 Microsoft 維護且開放社群使用之 Github 存放庫中的預先定義 ARM 範本。這些範本可以直接從 Github 部署，或下載並修改以符合您的需求。若要部署範本，建立具有兩個子網路的 VNet，請依照下列步驟執行。

1. 請透過瀏覽器瀏覽 [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)。
2. 向下捲動範本清單，按一下 [101-vnet-two-subnets]。查看 **README.md** 檔案，如下所示。

	![Github 中的 READEME.md 檔案](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. 按一下 [**部署至 Azure**]。如有必要，請輸入您的 Azure 登入認證。
4. 在 [**參數**] 刀鋒視窗中，輸入您要用以建立新 VNet 的值，然後按一下 [**確定**]。下圖顯示我們案例中的值。

	![ARM 範本參數](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

4. 按一下 [**資源群組**]，選取要加入 VNet 的目的地資源群組，或按一下 [**新建**]，將 VNet 加入至新的資源群組。下圖顯示新資源群組 **TestRG** 的資源群組設定。

	![資源群組](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

5. 如有必要，請變更您 VNet 的 [**訂用帳戶**] 和 [**位置**] 設定。
6. 如果您不想在「**開始面板**」上看到 VNet 磚，請停用 [**釘選到「開始面板」**]。
5. 按一下 [**法律條款**] 並閱讀條款，然後按一下 [**購買**] 表示同意條款。 
6. 按一下 [**建立**] 來建立 VNet。

	![在 Preview 入口網站中提交部署磚](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

7. 完成部署後，依序按一下 [**TestVNet**] > [**所有設定**] > [**子網路**]，以查看子網路屬性，如下所示。

	![在 Preview 入口網站中建立 VNet](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.gif)

<!---HONumber=AcomDC_0323_2016-->