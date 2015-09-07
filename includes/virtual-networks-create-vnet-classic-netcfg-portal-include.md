## 如何在 Azure 入口網站中使用網路組態檔建立 VNet

Azure 使用 xml 檔案定義訂用帳戶可用的所有 VNet。您可以下載這個檔案並加以編輯以進行修改，或刪除現有的 VNet，建立新的虛擬網路。本文件將說明如何下載這個檔案 (下稱網路組態，或 Netcfg 檔案)，以及如何編輯該檔案，以建立新的 VNet。請參閱 [Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)，以了解網路組態檔的詳細資訊。

若要透過 Azure 入口網站，使用 Netcfg 檔案建立 VNet，請依照下列步驟執行。

1. 透過瀏覽器瀏覽至 http://manage.windowsazure.com，並視需要使用您的 Azure 帳戶登入。
2. 如下方所示，向下捲動服務清單，按一下 [**網路**]。

	![Azure 虛擬網路](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. 如下方所示，在頁面底部按一下 [**匯出**] 按鈕。

	![匯出按鈕](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. 在 [**匯出網路組態**] 頁面上，選取您要匯出虛擬網路組態的來源訂用帳戶，然後按一下頁面左下角的核取記號按鈕。
5. 依照瀏覽器中的指示，儲存 **NetworkConfig.xml** 檔案。請務必記下儲存檔案的位置。
6. 使用任何 XML 或文字編輯器應用程式，開啟您在上述步驟 5 中儲存的檔案，並尋找 **<VirtualNetworkSites>** 元素。如果您已建立網路，每個網路都將顯示為其自身的 **<VirtualNetworkSite>** 元素。
7. 若要建立此案例所述的虛擬網路，請在 **<VirtualNetworkSites>** 元素正下方，新增下列 XML：

		<VirtualNetworkSite name="TestVNet" Location="Central US">
		  <AddressSpace>
		    <AddressPrefix>192.168.0.0/16</AddressPrefix>
		  </AddressSpace>
		  <Subnets>
		    <Subnet name="FrontEnd">
		      <AddressPrefix>192.168.1.0/24</AddressPrefix>
		    </Subnet>
		    <Subnet name="BackEnd">
		      <AddressPrefix>192.168.2.0/24</AddressPrefix>
		    </Subnet>
		  </Subnets>
		</VirtualNetworkSite>

8.  儲存網路組態檔。
9.  如下圖所示，在 Azure 入口網站頁面的左下角，依序按一下 [**新增**]、[**網路服務**]、[**虛擬網路**]，和 [**匯入組態**]。

	![匯入組態](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  在 [**匯入網路組態檔**] 頁面上，按一下 [**瀏覽檔案...**]，然後瀏覽至您在上述步驟 8 中儲存檔案的目的地資料夾，選取該檔案，按一下 [**開啟**]。網頁外觀將類似於下圖。在頁面右下角，按一下箭頭按鈕，以移至下一個步驟。

	![匯入網路組態檔頁面](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   如下圖所示，查看 [**建置您的網路**] 頁面中，新 VNet 的項目。

	![建置您的網路頁面](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   按一下頁面右下角的核取記號按鈕建立 VNet。幾秒鐘後，VNet 會顯示在可用 VNet 清單中，如下圖所示。

	![新的虛擬網路](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)

<!---HONumber=August15_HO9-->