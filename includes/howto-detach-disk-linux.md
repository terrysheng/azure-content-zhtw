
當不再需要某個連接至虛擬機器的資料磁碟時，卸離此資料磁碟很簡單。這會將磁碟從虛擬機器中卸離，但這不會將它從儲存體中移除。如果您想要再次使用磁碟上現有的資料，您可以將磁碟重新連接至相同或其他虛擬機器。

> [AZURE.NOTE]Azure 中的虛擬機器使用不同類型的磁碟，例如作業系統磁碟、本機暫存磁碟和選擇性資料磁碟。建議您使用資料磁碟來儲存虛擬機器的資料。如需詳細資訊，請參閱[有關虛擬機器的磁碟和 VHD](../../virtual-machines-disks-vhds.md)。您無法卸離作業系統磁碟，除非您同時刪除虛擬機器。

## 尋找磁碟

可以從虛擬機器卸離磁碟之前，您需要找出 LUN 編號，這是要卸離之磁碟的識別碼。若要這樣做，請遵循下列步驟：

1. 	開啟 Azure CLI for Mac, Linux, and Windows，並連接至您的 Azure 訂用帳戶。如需詳細資料，請參閱 [從 Azure CLI 連接至 Azure](../articles/xplat-cli-connect.md)。

2.  請確定您是在 Azure 服務管理模式中，這是輸入 `azure config
 	mode asm` 時的預設值。

3. 	使用 `azure vm disk list
	<virtual-machine-name>`，找出哪些磁碟已連接至您的虛擬機器，如下所示：

		$azure vm disk list ubuntuVMasm
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK

4. 	請記下您要卸離之磁碟的 LUN 或**邏輯單元編號**。


## 卸離磁碟

在您找到磁碟的 LUN 編號之後，就可以開始卸離磁碟：

1. 	執行命令 `azure vm disk detach
 	<virtual-machine-name> <LUN>`，從虛擬機器卸離選取的磁碟，如下所示：

		$azure vm disk detach ubuntuVMasm 0
		info:    Executing command vm disk detach
		+ Getting virtual machines
		+ Removing Data-Disk
		info:    vm disk detach command OK

2. 	您可以執行此命令，檢查是否已卸離磁碟：

		$azure vm disk list ubuntuVMasm
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		info:    vm disk list command OK

卸離的磁碟仍留在儲存體中，但不再連接至虛擬機器。

<!---HONumber=August15_HO7-->