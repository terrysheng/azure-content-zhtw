1. 登入 Azure [管理入口網站](http://manage.windowsazure.com)。如果您還沒有訂用帳戶，請參考[免費試用](http://azure.microsoft.com/pricing/free-trial/)優惠。

2. 在視窗底部的命令列，按一下 [**新增**]。

3. 在 [**計算**] 下，按一下 [**虛擬機器**]，然後按一下 [**從組件庫**]。

	![Navigate to From Gallery in the Command Bar](./media/virtual-machines-create-WindowsVM/fromgallery.png)
	
4. 第一個畫面可讓您從映像庫的其中一份清單中，為您的虛擬機器 [**選擇映像**]。(視您使用的訂閱而定，可用的映像可能有所不同)。按一下箭頭以繼續。

	![Choose an image](./media/virtual-machines-create-WindowsVM/chooseimage.png)

5. 第二個畫面可讓您挑選電腦名稱、大小，及系統管理使用者名稱和密碼。如果您只想試用 Azure虛擬機器，請填寫欄位，如下圖所示。您也可以選擇執行應用程式或工作負載所需的層次和大小。以下是可協助您填寫欄位的部分詳細資料： 
	
	- **新使用者名稱**是指用來管理伺服器的系統管理帳戶。建立此帳戶的唯一密碼，並確定記住此密碼。**您將需要使用者名稱和密碼才能登入虛擬機器**。 

	- 虛擬機器的大小會影響其使用成本以及組態選項 (例如您可連接的資料磁碟數目)。如需詳細資料，請參閱 [Azure 的虛擬機器和雲端服務大小](http://go.microsoft.com/fwlink/p/?LinkId=466520)。

	![Configure the properties of the virtual machine](./media/virtual-machines-create-WindowsVM/vmconfiguration.png)

6. 第三個畫面可讓您設定網路、儲存體和可用性的資源。以下是可協助您填寫欄位的部分詳細資料： 

	- [**雲端服務 DNS 名稱**] 是全域 DNS 名稱，它會成為用來連絡虛擬機器的 URI 一部分。您將必須想出自己的雲端服務名稱，因為它在 Azure 中必須是唯一的。雲端服務對於使用[多個虛擬機器](http://azure.microsoft.com/documentation/articles/cloud-services-connect-virtual-machine/) 的案例十分重要。
 
	- 在 [**區域/同質群組/虛擬網路**] 中，使用適合您位置的區域。您也可以改選指定虛擬網路。
 
  >[AZURE.NOTE] 如果您想要讓虛擬機器使用虛擬網路，就**必須**在建立虛擬機器時指定虛擬網路。在建立 VM 後，您無法將虛擬機器加入虛擬網路。如需詳細資訊，請參閱 [Azure 虛擬網路概觀](http://go.microsoft.com/fwlink/p/?LinkID=294063)。

	- 如需有關設定端點的詳細資料，請參閱[如何設定虛擬機器的端點](http://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/)。

	![Configure the connected resources of the virtual machine](./media/virtual-machines-create-WindowsVM/resourceconfiguration.png)

7. 第四個組態畫面可讓您設定 VM 代理程式及部分可用延伸模組。按一下核取記號以建立虛擬機器。

	![Configure VM Agent and extensions for the virtual machine](./media/virtual-machines-create-WindowsVM/agent-and-extensions.png)

	>[AZURE.NOTE] VM 代理程式提供環境讓您安裝延伸模組，以協助您與虛擬機器互動或管理虛擬機器。如需詳細資訊，請參閱[使用延伸模組](http://go.microsoft.com/FWLink/p/?LinkID=390493)。  
    
8. 建立虛擬機器後，管理入口網站會在 [**虛擬機器**] 底下列出新的虛擬機器。並建立對應的雲端服務和儲存體帳戶，且列於這些區段中。虛擬機器和雲端服務會自動啟動，管理入口網站會將他們的狀態顯示為 [**執行中**]。 

	![Configure VM Agent and the endpoints of the virtual machine](./media/virtual-machines-create-WindowsVM/vmcreated.png)
<!--HONumber=45--> 
