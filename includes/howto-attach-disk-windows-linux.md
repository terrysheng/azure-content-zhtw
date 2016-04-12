
如需有關磁碟的詳細資訊，請參閱[關於虛擬機器的磁碟和 VHD](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md)。

##<a id="attachempty"></a>做法：連接空的磁碟

連結空磁碟是一個新增資料磁碟的簡易方式，因為 Azure 會為您建立 .vhd 檔案並將它儲存在儲存體帳戶中。

1. 按一下 [虛擬機器]，然後選取適當的 VM。

2. 在命令列上，按一下 [連接]，然後按一下 [連接空的磁碟]。


	![連接空的磁碟](./media/howto-attach-disk-windows-linux/AttachEmptyDisk.png)

3.	[**連接空的磁碟**] 對話方塊隨即出現。


	![連接新的空磁碟](./media/howto-attach-disk-windows-linux/AttachEmptyDetail.png)


	執行下列動作：

	- 在 [檔案名稱] 中，接受預設名稱，或是為 .vhd 檔案輸入另一個名稱。資料磁碟會使用自動產生的名稱，即使您為 .vhd 檔案輸入另一個名稱亦然。

	- 輸入資料磁碟的 [**大小 (GB)**]。

	- 按一下核取記號完成工作。

4.	建立並連結資料磁碟之後，它就會列在 VM 的儀表板中。

	![成功連接空的資料磁碟](./media/howto-attach-disk-windows-linux/AttachEmptySuccess.png)

> [AZURE.NOTE] 新增新的資料磁碟之後，您將必須登入 VM 並將磁碟初始化，才能使用該磁碟。


##<a id="attachexisting"></a>做法：連接現有磁碟

連接現有磁碟要求您在儲存體帳戶中需要有可用的 .vhd。請使用 [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) Cmdlet，將 .vhd 檔案上傳至儲存體帳戶。在建立並上傳 .vhd 檔案之後，您就可將它連結至 VM。

1. 按一下 [虛擬機器]，然後選取適當的虛擬機器。

2. 在命令列上，按一下 [連接]，然後選取 [連接磁碟]。


	![連接資料磁碟](./media/howto-attach-disk-windows-linux/AttachExistingDisk.png)


3. 選取資料磁碟，然後按一下核取記號來連結該資料值磁碟。

	![輸入資料磁碟詳細資料](./media/howto-attach-disk-windows-linux/AttachExistingDetail.png)

4.	連結資料磁碟之後，它就會列在 VM 的儀表板中。


	![成功連接資料磁碟](./media/howto-attach-disk-windows-linux/AttachExistingSuccess.png)

<!---HONumber=AcomDC_0330_2016-->