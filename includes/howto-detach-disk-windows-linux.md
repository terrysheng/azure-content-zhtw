<properties writer="kathydav" editor="tysonn" manager="timlt" />



#如何從虛擬機器卸離資料磁碟 

- [步驟 1：尋找磁碟](#finddisks)
- [步驟 2：卸離資料磁碟](#detachdisk)

當不再需要某個連接至虛擬機器的資料磁碟時，卸離此資料磁碟很簡單。這會將磁碟從虛擬機器中卸離，但這不會將它從儲存體中移除。如果您想要再次使用磁碟上現有的資料，您可以將磁碟重新連接至相同或其他虛擬機器。  

> [WACOM.NOTE] Azure 中的虛擬機器使用不同類型的磁碟，例如作業系統磁碟、本機暫存磁碟和選擇性資料磁碟。建議您使用資料磁碟來儲存虛擬機器的資料。如需磁碟的詳細資訊，請參閱[關於磁碟和映像] []。如需指示，請參閱[如何將資料磁碟連接至虛擬機器] [attachdisk]。

## <a id="finddisks"> </a>步驟 1：尋找磁碟##


如果您不知道或想要在卸離磁碟之前驗證磁碟名稱，請遵循下列步驟。 

> [WACOM.NOTE] Azure 會在您連接磁碟時自動指派磁碟的名稱。此名稱是由雲端服務名稱、虛擬機器名稱和數值所組成。

1. 如果您尚未登入 Azure [管理入口網站]，請先登入。(http://manage.windowsazure.com)。 

2. 按一下 [**虛擬機器**]，然後選取適當的虛擬機器。VM 的儀表板隨即開啟。

3. 在 [**磁碟**] 下，資料表會列出所有連接磁碟的名稱和類型。例如，此畫面會顯示虛擬機器及一個作業系統 (OS) 磁碟和一個資料磁碟：
		
	![Find data disk](./media/howto-detach-disk-windows-linux/FindDataDisks.png)	


## <a id="detachdisk"> </a>步驟 2：卸離磁碟##

在您找到磁碟名稱後，您可以開始卸離磁碟：

1. 按一下 [**虛擬機器**]，選取您要卸離資料磁碟的虛擬機器。
2. 在命令列中，按一下 [**卸離磁碟**]。

2. 選取資料磁碟，然後按一下核取記號即可卸離。


	![Detach disk details](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

磁碟仍留在儲存中，但不再連接至虛擬機器。



[attachdisk]:/zh-tw/manage/windows/how-to-guides/attach-a-disk/

[關於磁碟和映像]：http://go.microsoft.com/fwlink/p/?LinkId=263439


<!--HONumber=35_1-->
