<properties urlDisplayName="Attach a disk" pageTitle="將磁碟附加至虛擬機器 | Azure" metaKeywords="Azure Windows virtual machine, Azure attach disk, Azure initialize disk" description="了解如何將資料磁碟附加至 Azure 虛擬機器，並初始化磁碟以便開始使用。" metaCanonical="" services="virtual-machines,storage" documentationCenter="" title="如何將資料磁碟附加至虛擬機器" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="7/29/2014" ms.author="kathydav" />

# 如何將資料磁碟附加至 Windows 虛擬機器

您可以附加空的磁碟和含有資料的磁碟。在這兩種情況下，磁碟實際上是位於 Azure 儲存體帳戶中的 .vhd 檔案。另外，在這兩種情況下，當您附加磁碟之後，磁碟必須完成初始化才能使用。

> [WACOM.NOTE] 最好使用一或多個不同的磁碟來儲存虛擬機器的資料。當您建立 Azure 虛擬機器時，它會有一個作業系統的磁碟對應至 C 磁碟機，還有一個暫存磁碟對應至 D 磁碟機。**請勿使用 D 磁碟機來儲存資料。**顧名思義，它只提供暫存儲存空間。它並不提供備援或備份，因為它不在 Azure 儲存體內。

-   [作法：連接空的磁碟][作法：連接空的磁碟]
-   [作法：連接現有磁碟][作法：連接現有磁碟]
-   [作法：在 Windows Server 中初始化新的資料磁碟][作法：在 Windows Server 中初始化新的資料磁碟]

[WACOM.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

## <span id="initializeinWS"></span></a>作法：在 Windows Server 中初始化新的資料磁碟

1.  使用[如何登入執行 Windows Server 的虛擬機器][如何登入執行 Windows Server 的虛擬機器]中列出的步驟，連線至虛擬機器。

2.  登入之後，開啟 [伺服器管理員]，在左窗格中展開 [存放裝置]，然後按一下 [磁碟管理]。

    ![開啟伺服器管理員][開啟伺服器管理員]

3.  以滑鼠右鍵按一下 [磁碟 2]，按一下 [初始化磁碟]，然後按一下 [確定]。

    ![初始化磁碟][初始化磁碟]

4.  以滑鼠右鍵按一下 [磁碟 2] 的空間配置區域，按一下 [新增簡單磁碟區]，然後以預設值完成精靈。

    ![初始化磁碟區][初始化磁碟區]

<!-- -->

    The disk is now online and ready to use with a new drive letter.



    ![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

  [作法：連接空的磁碟]: #attachempty
  [作法：連接現有磁碟]: #attachexisting
  [作法：在 Windows Server 中初始化新的資料磁碟]: #initializeinWS
  [如何登入執行 Windows Server 的虛擬機器]: ../virtual-machines-log-on-windows-server/
  [開啟伺服器管理員]: ./media/storage-windows-attach-disk/ServerManager.png
  [初始化磁碟]: ./media/storage-windows-attach-disk/InitializeDisk.png
  [初始化磁碟區]: ./media/storage-windows-attach-disk/InitializeDiskVolume.png
