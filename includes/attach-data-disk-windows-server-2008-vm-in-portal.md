請依照以下步驟來連接資料磁碟：

1.  在 [Azure 管理入口網站][Azure 管理入口網站]中，按一下 [虛擬機器]，然後選取剛剛建立的虛擬機器 (**testwinvm**)。

2.  在命令列上，按一下 [連接]，然後按一下 [連接空的磁碟]。

    [將空的磁碟連接至虛擬機器] 對話方塊隨即出現。

3.  系統已為您定義 [虛擬機器名稱]、[儲存位置] 和 [檔案名稱]。您只需要輸入想要的磁碟大小。在 [大小] 欄位中輸入 **5**。

    ![連接空的磁碟][連接空的磁碟]

    **注意：**所有磁碟都是從 Azure 儲存體中的 VHD 檔案建立。您可以為新增至儲存體的 VHD 檔案提供名稱，但是 Azure 會自動產生磁碟的名稱。

4.  按一下核取記號將資料磁碟連接至虛擬機器。

5.  按一下虛擬機器名稱來顯示儀表板；這可讓您確認資料磁碟已順利連接至虛擬機器。

    虛擬機器的磁碟數目現在是 2。您連接的磁碟會列在 [磁碟] 表格中。

    ![連接空的磁碟][1]

    將資料磁碟連接至虛擬機器之後，磁碟會處於離線狀態且未初始化。您必須先登入虛擬機器並將磁碟初始化，才能使用該磁碟來儲存資料。

## 使用遠端桌面連線至虛擬機器並完整安裝

1.  佈建好虛擬機器之後，按一下 [管理入口網站] 上的 [虛擬機器]，然後按一下您的新虛擬機器。畫面上即會顯示您虛擬機器的相關資訊。

2.  按一下頁面底部的 [連接]。使用 Windows 遠端桌面程式 (*%windir%\\system32\\mstsc.exe*) 來開啟 .rpd 檔案。

3.  在 [Windows 安全性] 對話方塊中，提供 **Administrator** 帳戶的密碼。(系統可能會要求您確認虛擬機器的認證。) 當您第一次登入這個虛擬機器時，可能需要完成數個程序，包括設定桌面、Windows Update，以及完成 Windows 初始設定工作。使用 Windows 遠端桌面連接到虛擬機器之後，虛擬機器的運作方式與任何其他電腦很像。

4.  登入虛擬機器之後，開啟 [伺服器管理員]。在左窗格中，展開 [儲存體]，然後按一下 [磁碟管理]。

    ![伺服器管理員][伺服器管理員]

5.  隨即顯示 [初始化磁碟] 視窗。按一下 [確定]。

    ![初始化磁碟][初始化磁碟]

6.  以滑鼠右鍵按一下 [磁碟 2] 的空間配置區域，按一下 [新增簡單磁碟區]，然後以預設值完成精靈。

    ![新增簡單磁碟區][新增簡單磁碟區]

    磁碟現在為上線狀態，可以搭配新的磁碟機代號來使用。

    ![初始化成功][初始化成功]

  [Azure 管理入口網站]: http://manage.windowsazure.com
  [連接空的磁碟]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/AttachDataDiskWinVM2.png
  [1]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/AttachDataDiskWinVM3.png
  [伺服器管理員]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/servermanager.png
  [初始化磁碟]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/initializedisk0.png
  [新增簡單磁碟區]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/initializediskvolume.png
  [初始化成功]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/initializesuccess.png
