<properties 
	pageTitle="將磁碟附加至虛擬機器 | Azure" 
	description="了解如何將資料磁碟附加至 Azure 虛擬機器，並初始化磁碟以便開始使用。" 
	services="virtual-machines, storage" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2015" 
	ms.author="kathydav"/>

#如何將資料磁碟附加至 Windows 虛擬機器

您可以附加空的磁碟和含有資料的磁碟。在這兩種情況下，磁碟實際上是位於 Azure 儲存體帳戶中的 .vhd 檔案。另外，在這兩種情況下，當您附加磁碟之後，磁碟必須完成初始化才能使用。 

> [AZURE.NOTE] 最好使用一或多個不同的磁碟來儲存虛擬機器的資料。當您建立 Azure 虛擬機器時，它會有一個作業系統的磁碟對應至 C 磁碟機，還有一個暫存磁碟對應至 D 磁碟機。**請勿使用 D 磁碟機來儲存資料。**顧名思義，它只提供暫存儲存空間。它並不提供備援或備份，因為它不在 Azure 儲存體內。

- [做法：連接空的磁碟](#attachempty)
- [做法：連接現有磁碟](#attachexisting)
- [做法：在 Windows Server 中初始化新的資料磁碟](#initializeinWS)


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

##<a id="initializeinWS"></a>做法：在 Windows Server 中初始化新的資料磁碟

1. 連線到虛擬機器。如需詳細資訊，請參閱[如何登入執行 Windows Server 的虛擬機器][logon]。

2. 登入之後，開啟 **[伺服器管理員]**，在左窗格中展開 **[存放裝置]**，然後按一下 **[磁碟管理]**。



	![Open Server Manager](./media/storage-windows-attach-disk/ServerManager.png)



3. 以滑鼠右鍵按一下 **[磁碟 2]**，按一下 **[初始化磁碟]**，然後按一下 **[確定]**。



	![Initialize the disk](./media/storage-windows-attach-disk/InitializeDisk.png)


4. 以滑鼠右鍵按一下 [磁碟 2] 的空間配置區域，按一下 **[新增簡單磁碟區]**，然後以預設值完成精靈。
 

	![Initialize the volume](./media/storage-windows-attach-disk/InitializeDiskVolume.png)


[logon]: ../virtual-machines-log-on-windows-server/



	磁碟現在為上線狀態，可以搭配新的磁碟機代號來使用。



	![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

> [AZURE.NOTE] 可連接至虛擬機器的磁碟數目上限會依據虛擬機器的大小而改變。例如，您只能連接 4 個磁碟至標準 A2，但是您連接 32 個磁碟至標準 D14，以及連接 64 個磁碟至標準 G5。您可以在[這裡](https://msdn.microsoft.com/zh-tw/library/azure/dn197896.aspx)找到依據虛擬機器大小決定可以連接多少磁碟的詳細資訊。



<!--HONumber=42-->
