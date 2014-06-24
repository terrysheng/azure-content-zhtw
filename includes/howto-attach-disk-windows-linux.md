# 如何將資料磁碟附加至虛擬機器

* [概念](#concepts)
* [作法：連接現有磁碟](#attachexisting)
* [作法：連接空的磁碟](#attachempty)
* [作法：在 Windows Server 2008 R2 中初始化新的資料磁碟](#initializeinWS)
* [作法：在 Linux 中初始化新的資料磁碟](#initializeinlinux)
## <a  id="concepts" ></a> 概念

您可以將資料磁碟連接至虛擬機器，以儲存應用程式資料。資料磁碟是以您自己的電腦在本機或以 Azure 在雲端中建立的虛擬硬碟
(VHD)。在虛擬機器中管理資料磁碟的方式，就像在公司的伺服器上管理一樣。

透過管理入口網站，您可以將包含資料的資料磁碟上傳並連接至虛擬機器，也可以從虛擬機器所使用的相同儲存體帳戶來新增空的磁碟。本文說明這些程序。若要連接位於不同儲存體帳戶中的空磁碟，請使用
Azure PowerShell 模組中提供的 [Add-AzureDataDisk][1]
Cmdlet。若要下載此模組，請參閱[下載][2]頁面。

虛擬機器中可不斷地新增磁碟。可連接至虛擬機器的磁碟數目以虛擬機器的大小為限。如需關於虛擬機器和磁碟大小的詳細資訊，請參閱＜[Azure
的虛擬機器大小][3]＞。

> [WACOM.NOTE] Azure 儲存體支援的 BLOB 最大為 1 TB，可容納最大虛擬大小 999 GB 的
> VHD。不過，如果使用 Hyper-V 來建立新的 VHD，則您指定的大小就代表虛擬大小。若要在 Azure 中使用
> VHD，則指定的大小不能大於 999 GB。

**資料磁碟與資源磁碟的比較**  
資料磁碟位於 Azure 儲存體，可永久儲存檔案和應用程式資料。

每個虛擬機器也都連接一個暫時性本機*資源磁碟*。因為資源磁碟上的資料在重新開機之後就會消失，通常供虛擬機器中執行的應用程式和處理程序暫時儲存資料。也用來儲存作業系統的分頁檔或交換檔。

在 Windows 上，資源磁碟標示為 **D:**磁碟機。在 Linux 上，資源磁碟通常由 Azure Linux
代理程式管理，並自動掛接到 **/mnt/resource** (或 Ubuntu 映像中的
**/mnt**)。如需詳細資訊，請參閱[Azure Linux 代理程式使用者指南][4](英文)。

如需關於使用資料磁碟的詳細資訊，請參閱[管理磁碟及映像][5]。
## <a  id="attachexisting" ></a>作法：連接現有磁碟

1.  如果您尚未登入，請登入 [Azure 管理入口網站][6]。

2.  按一下 **虛擬機器**，然後選取要連接磁碟的虛擬機器。

3.  在命令列上，按一下 **連接**，然後選取 **連接磁碟**。
    
    ![連接資料磁碟](./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png)
    
    **連接磁碟** 對話方塊隨即出現。
    
    ![輸入資料磁碟詳細資料](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

4.  選取要連接至虛擬機器的資料磁碟。
5.  按一下核取記號將資料磁碟連接至虛擬機器。
    
    您現在會看到資料磁碟列在虛擬機器的儀表板上。
    
    ![成功連接資料磁碟](./media/howto-attach-disk-window-linux/AttachSuccess.png)
## <a  id="attachempty" ></a>作法：連接空的磁碟

在建立並上傳 .vhd 檔案做為空的磁碟之後，就可將它連接至虛擬機器。請使用 [Add-AzureVhd][7] Cmdlet，將 .vhd
檔案上傳至儲存體帳戶。

1.  按一下 **虛擬機器**，然後選取要連接資料磁碟的虛擬機器。

2.  在命令列上，按一下 **連接**，然後選取 **連接空的磁碟**。
    
    ![連接空的磁碟](./media/howto-attach-disk-window-linux/AttachDiskWindows.png)
    
    **連接空的磁碟** 對話方塊隨即出現。
    
    ![連接新的空磁碟](./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png)

3.  在 **檔案名稱** 中，接受自動產生的名稱，或輸入想要用於已儲存之 VHD 檔案的名稱。從 VHD
    檔案建立的資料磁碟都是使用自動產生的名稱。

4.  在 **大小** 中，輸入資料磁碟的大小。

5.  按一下核取記號連接空的資料磁碟。
    
    您現在會看到資料磁碟列在虛擬機器的儀表板上。
    
    ![成功連接空的資料磁碟](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)

> [WACOM.NOTE] 新增資料磁碟之後，您必須登入虛擬機器並初始化磁碟，這樣虛擬機器才能使用磁碟來儲存資料。
## <a  id="initializeinWS" ></a>作法：在 Windows Server 中初始化新的資料磁碟

1.  使用[如何登入執行 Windows Server
    的虛擬機器](../virtual-machines-log-on-windows-server/)中列出的步驟，連線至虛擬機器。

2.  登入之後，開啟 **伺服器管理員**，在左窗格中展開 **存放裝置**，然後按一下 **磁碟管理**。
    
    ![開啟伺服器管理員](./media/howto-attach-disk-window-linux/ServerManager.png)

3.  以滑鼠右鍵按一下 **磁碟 2**，然後按一下 **初始化磁碟**。
    
    ![初始化磁碟](./media/howto-attach-disk-window-linux/InitializeDisk.png)

4.  按一下 **確定** 開始初始化程序。

5.  以滑鼠右鍵按一下 **磁碟 2] 的空間配置區域，按一下 [新增簡單磁碟區**，然後以預設值完成精靈。
    
    ![初始化磁碟區](./media/howto-attach-disk-window-linux/InitializeDiskVolume.png)
    
    磁碟現在為上線狀態，可以搭配新的磁碟機代號來使用。
    
    ![成功初始化磁碟區](./media/howto-attach-disk-window-linux/InitializeSuccess.png)
## <a  id="initializeinlinux" ></a>作法：在 Linux 中初始化新的資料磁碟

1.  使用[如何登入執行 Linux
    的虛擬機器](../virtual-machines-linux-how-to-log-on/)中列出的步驟，連線至虛擬機器。

2.  在 SSH 視窗中，輸入下列命令，然後輸入您為了管理虛擬機器而建立之帳戶的密碼：
    
    `sudo grep SCSI /var/log/messages`
    
    在出現的訊息中，您可以找到最後一個新增之資料磁碟的識別碼。
    
    ![取得磁碟訊息](./media/howto-attach-disk-window-linux/DiskMessages.png)

3.  在 SSH 視窗中，輸入下列命令來建立新的裝置，然後輸入帳戶密碼：
    
    `sudo fdisk /dev/sdc`
    
    > [WACOM.NOTE] 在此範例中，如果 /sbin 或 /usr/sbin 不在您的 `$PATH`
    > 中，您可能需要在部份散發套件上使用 `sudo -i`。

4.  輸入 **n** 建立新的磁碟分割。
    
    ![建立新的裝置](./media/howto-attach-disk-window-linux/DiskPartition.png)

5.  輸入 **p** 將磁碟分割設為主要磁碟分割，輸入 **1** 設為第一個磁碟分割，然後按 Enter 鍵接受預設的磁柱值。
    
    ![建立磁碟分割](./media/howto-attach-disk-window-linux/DiskCylinder.png)

6.  輸入 **p** 查看目前分割之磁碟的詳細資料。
    
    ![列出磁碟資訊](./media/howto-attach-disk-window-linux/DiskInfo.png)

7.  輸入 **w** 寫入磁碟的設定。
    
    ![寫入磁碟變更](./media/howto-attach-disk-window-linux/DiskWrite.png)

8.  您必須在新的磁碟分割上建立檔案系統。做為範例，請輸入下列命令來建立檔案系統，然後輸入帳戶密碼：
    
    `sudo mkfs -t ext4 /dev/sdc1`
    
    ![建立檔案系統](./media/howto-attach-disk-window-linux/DiskFileSystem.png)
    
    > [WACOM.NOTE] 請注意，SUSE Linux Enterprise 11 系統上僅對 ext4
    > 檔案系統提供唯讀存取。針對這些系統，建議您將新檔案系統格式化為 ext3，而非 ext4。

9.  接下來您必須有可供掛接新檔案系統的目錄。做為範例，請輸入下列命令建立新目錄來掛接磁碟機，然後輸入帳戶密碼：
    
    `sudo mkdir /datadrive`

10. 輸入下列命令來掛接磁碟機：
    
    `sudo mount /dev/sdc1 /datadrive`
    
    資料磁碟現在可以當做 **/datadrive** 來使用。

11. 將新的磁碟機新增至 /etc/fstab：
    
    為了確保重新開機之後自動重新掛接磁碟機，必須將磁碟機新增至 /etc/fstab 檔案。此外，強烈建議在 /et/fstab
    中使用全域唯一識別碼 (Universally Unique IDentifier, UUID) 來參考磁碟機，而不只是裝置名稱
    (例如，/dev/sdc1)。若要尋找新磁碟機的 UUID，您可以使用 **blkid** 公用程式：
    
        `sudo -i blkid`
    
    輸出類似如下範例：
    
        `/dev/sda1:UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
        `/dev/sdb1:UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
        `/dev/sdc1:UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`
    
    > [WACOM.NOTE] blkid 並不要求一定要使用 sudo 存取權，只是在某些散發套件上，如果 /sbin 或
    > /usr/sbin 不在 `$PATH` 中，則使用 `sudo -i` 來執行會比較簡單。
    
    **警告：**不當編輯 /etc/fstab
    檔案會導致系統無法開機。如果不確定，請參閱散發套件的文件，以取得如何適當編輯此檔案的相關資訊。在編輯之前，也建議先備份
    /etc/fstab 檔案。
    
    請使用文字編輯器，在 /etc/fstab 檔案的結尾輸入新檔案系統的相關資訊。在此範例中，我們使用先前步驟所建立之新的
    **/dev/sdc1** 裝置的 UUID 值，並使用掛接點 **/datadrive**：
    
        `UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`
    
    或者，在基於 SUSE Linux 的系統上，您可能需要使用稍微不同的格式：
    
        `/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`
    
    如果還有建立其他資料磁碟機或磁碟分割，同樣也需要分別在 /etc/fstab 中輸入。
    
    現在，您可以測試檔案系統是否適當掛接，方法是取消掛接檔案系統，再重新掛接，例如使用先前步驟中建立的範例掛接點 `/datadrive`：
    
        `sudo umount /datadrive`
        `sudo mount /datadrive`
    
    如果第二個命令發生錯誤，請檢查 /etc/fstab 檔案的語法是否正確。
    
    > [WACOM.NOTE] 後續移除資料磁碟而不編輯 fstab，可能會造成 VM
    > 無法開機。如果這是常見情況，那麼多數散發套件會提供 `nofail` 和/或 `nobootwait` fstab
    > 選項，使得即使沒有磁碟，也能讓系統開機。請查閱散發套件的文件，以取得這些參數的相關資訊。



[1]: http://go.microsoft.com/fwlink/p/?LinkId=391661
[2]: http://www.windowsazure.com/en-us/downloads/
[3]: http://go.microsoft.com/FWLink/p/?LinkID=294683
[4]: http://www.windowsazure.com/en-us/manage/linux/how-to-guides/linux-agent-guide/
[5]: http://msdn.microsoft.com/en-us/library/windowsazure/jj672979.aspx
[6]: http://manage.windowsazure.com
[7]: http://go.microsoft.com/FWLink/p/?LinkID=391684
