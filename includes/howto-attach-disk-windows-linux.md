如需 Azure 虛擬機器磁碟的詳細資訊，請參閱[關於 Azure 中的虛擬機器磁碟][關於 Azure 中的虛擬機器磁碟]。

## <span id="attachempty"></span></a>作法：連接空的磁碟

新增資料磁碟的一個較為簡易方式是連接空的磁碟，因為 Azure 會為您建立 .vhd 檔案並將它儲存在儲存體帳戶中。

1.  按一下 [虛擬機器]，然後選取適當的虛擬機器。

2.  在命令列上，按一下 [連接]，然後選取 [連接空的磁碟]。

    ![連接空的磁碟][連接空的磁碟]

    [連接空的磁碟] 對話方塊隨即出現。

    ![連接新的空磁碟][連接新的空磁碟]

3.  在 [檔案名稱] 中，接受自動產生的名稱或輸入一個描述性名稱。從 .vhd 檔案建立的資料磁碟一律會使用自動產生的名稱。

4.  在 [大小] 中，輸入資料磁碟的大小。

5.  按一下核取記號連接空的資料磁碟。

    您現在會看到資料磁碟列在虛擬機器的儀表板上。

    ![成功連接空的資料磁碟][成功連接空的資料磁碟]

## <span id="attachexisting"></span></a>作法：連接現有磁碟

連接現有磁碟要求您在儲存體帳戶中需要有可用的 .vhd。請使用 [Add-AzureVhd][Add-AzureVhd] Cmdlet，將 .vhd 檔案上傳至儲存體帳戶。在建立並上傳 .vhd 檔案之後，就可將它連接至虛擬機器。

1.  按一下 [虛擬機器]，然後選取適當的虛擬機器。

2.  在命令列上，按一下 [連接]，然後選取 [連接磁碟]。

    ![連接資料磁碟][連接資料磁碟]

    [連接磁碟] 對話方塊隨即出現。

    ![輸入資料磁碟詳細資料][輸入資料磁碟詳細資料]

3.  選取要連接至虛擬機器的資料磁碟。
4.  按一下核取記號將資料磁碟連接至虛擬機器。

    您現在會看到資料磁碟列在虛擬機器的儀表板上。

    ![成功連接資料磁碟][成功連接資料磁碟]

> [WACOM.NOTE]
> 新增資料磁碟之後，您必須登入虛擬機器並初始化磁碟，這樣虛擬機器才能使用該磁碟來儲存資料。

  [關於 Azure 中的虛擬機器磁碟]: http://go.microsoft.com/fwlink/p/?LinkId=403697
  [連接空的磁碟]: ./media/howto-attach-disk-window-linux/AttachDiskWindows.png
  [連接新的空磁碟]: ./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png
  [成功連接空的資料磁碟]: ./media/howto-attach-disk-window-linux/AttachEmptySuccess.png
  [Add-AzureVhd]: http://go.microsoft.com/FWLink/p/?LinkID=391684
  [連接資料磁碟]: ./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png
  [輸入資料磁碟詳細資料]: ./media/howto-attach-disk-window-linux/AttachExistingDisk.png
  [成功連接資料磁碟]: ./media/howto-attach-disk-window-linux/AttachSuccess.png
