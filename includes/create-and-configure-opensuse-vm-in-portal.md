1. 登入 [Azure 入口網站](http://manage.windowsazure.com)。如果您還沒有訂用帳戶，請參考[免費試用](http://azure.microsoft.com/pricing/free-trial/)優惠。

2. 在視窗底部的命令列上，按一下 [新增]。

3. 在 [計算] 下，依序按一下 [虛擬機器] 及 [從映像庫]。

	![建立新的虛擬機器][Image1]

4. 在 **SUSE** 群組之下，選取 OpenSUSE 虛擬機器映像，然後按一下箭號以繼續作業。

5. 在第一個 [虛擬機器組態] 頁面上：

	- 輸入 [**虛擬機器名稱**] (如 "testlinuxvm")。
	- 驗證 [**階層**] 並挑選 [**大小**]。階層可決定您可以選擇的大小。大小會影響其使用成本以及組態選項 (例如您可連接的資料磁碟數目)。如需詳細資訊，請參閱〈[虛擬機器的大小](../articles/virtual-machines-size-specs.md)〉。
	- 輸入**新的使用者名稱**，或接受預設值 **azureuser**。這個名稱會新增至 Sudoers 清單檔案。
	- 決定要使用的 [**驗證**] 類型。如需一般密碼指導方針，請參閱[字串密碼](http://msdn.microsoft.com/library/ms161962.aspx)。

6. 在下一個 [虛擬機器組態] 頁面中：

	- 使用預設 [**建立新的雲端服務**]。
	- 在 [**DNS 名稱**] 方塊中，輸入有效的 DNS 名稱以作為位址的一部分 (如 "testlinuxvm")。
	- 在 [區域/同質群組/虛擬網路] 方塊中，選取這個虛擬映像將託管於的區域。
	- 在**端點**下，保留 SSH 端點。您可以立即新增其他內容，或在建立虛擬機器之後新增、變更或刪除它們。

	>[AZURE.NOTE]如果您想要讓虛擬機器使用虛擬網路，就「必須」在建立虛擬機器時指定虛擬網路。在建立虛擬機器之後，您就無法將虛擬機器加入至虛擬網路。如需詳細資訊，請參閱[虛擬網路概觀](virtual-networks-overview.md)。

7.	在最後一個 [虛擬機器組態] 頁面上，保留預設設定，然後按一下核取記號以完成作業。

入口網站會在 [虛擬機器] 底下列出新的虛擬機器。雖然狀態報告為 **(佈建)**，仍會設定虛擬機器。當狀態報告為**執行**，您可以移至下一個步驟。

##連線至虛擬機器

視您將連結之電腦上的作業系統而定，您將使用 SSH 或 PuTTY 連線至虛擬機器：

- 請從執行 Linux 的電腦使用 SSH。在命令提示字元中，輸入：

	`$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`

	輸入使用者的密碼。

- 請從執行 Windows 的電腦使用 PuTTY。如果您尚未安裝，請從 [PuTTY 下載頁面][PuTTYDownload]將其下載。

	將 **putty.exe** 儲存至您電腦上的目錄中。開啟命令提示字元，瀏覽至該資料夾，然後執行 **putty.exe**。

	輸入主機名稱 (例如 "testlinuxvm.cloudapp.net")，然後針對 [**連接埠**] 輸入 "22"。

	![PuTTY 畫面][Image6]

##更新虛擬機器 (選用)

1. 連線至虛擬機器之後，您可以選擇性地安裝系統更新和修補程式。若要執行更新，請輸入：

	`$ sudo zypper update`

2. 選取 [**軟體**]，然後選取 [**線上更新**] 以列出可用的更新。選取 [**接受**] 即可開始安裝並套用所有可用的新修補程式 (選用修補程式除外)。

3. 安裝完成之後，選取 [**完成**]。您的系統現在已是最新版本。

[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png

<!---HONumber=September15_HO1-->