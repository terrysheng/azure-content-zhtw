


## 尋找虛擬機器

1. 登入 Azure 入口網站。

2. 在 [中樞] 功能表上，按一下 [虛擬機器]。

3.	然後從清單中選取虛擬機器。

4. 在右側的 [Essentials] 底下，按一下 [所有設定]，然後按一下 [磁碟]。

	![開啟磁碟設定](./media/virtual-machines-common-attach-disk-portal/find-disk-settings.png)

接著依照指示來連接新的磁碟或現有的磁碟。

## 選項 1：連接新的磁碟

1.	在 [磁碟] 刀鋒視窗上，按一下 [連接新項目]。

2.	檢閱預設設定，視需要進行更新，然後按一下 [確定]。

 	![檢閱磁碟設定](./media/virtual-machines-common-attach-disk-portal/attach-new.png)

3.	在 Azure 建立磁碟並將其連接至虛擬機器之後，該新磁碟就會列在虛擬機器之磁碟設定中的 [資料磁碟] 底下。

## 選項 2：連接現有磁碟

1.	在 [磁碟] 刀鋒視窗上，按一下 [連接現有項目]。

2.	在 [連接現有磁碟] 底下，按一下 [VHD 檔案]。

	![連接現有磁碟](./media/virtual-machines-common-attach-disk-portal/attach-existing.png)

3.	在 [儲存體帳戶] 底下，選取持有該 .vhd 檔案的帳戶和容器。

	![尋找 VHD 位置](./media/virtual-machines-common-attach-disk-portal/find-storage-container.png)

4.	選取 .vhd 檔案。

5.	在 [連接現有磁碟] 底下，您剛才選取的檔案會列在 [VHD 檔案] 底下。按一下 [確定]。

6.	Azure 將磁碟連接至虛擬機器之後，該磁碟會列在虛擬機器磁碟設定中的 [資料磁碟] 下面。

<!---HONumber=AcomDC_0330_2016-->