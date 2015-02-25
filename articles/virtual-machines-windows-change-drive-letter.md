<properties pageTitle="如何變更 Windows 暫存磁碟的磁碟機代號" description="說明如何在 Azure 的 Windows VM 上重新對應暫存磁碟" services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/15/2015" ms.author="kathydav"/>

# 如何變更 Windows 暫存磁碟的磁碟機代號

如果您需要使用 D 磁碟機來儲存資料，請遵循下列指示，使用不同的磁碟機做為暫存磁碟。請勿使用暫存磁碟機來儲存您需要保留的資料。

在開始之前，您需要將資料磁碟連接至虛擬機器，這樣您可以在此程序期間儲存 Windows 分頁檔 (pagefile.sys)。如果您還沒有這麼做，請參閱[如何將資料磁碟連接至 Windows 虛擬機器]。如需如何找出哪些磁碟已連接的相關指示，請參閱[關於 Azure 中的虛擬機器磁碟] 中的＜管理您的磁碟＞。

如果您要使用 D 磁碟機上現有的資料磁碟，請確定您已將 VHD 上傳至儲存體帳戶。如需指示，請參閱[建立 Windows Server VHD 並上傳至 Azure] 中的步驟 3 和 4。

> [AZURE.WARNING] 如果您調整虛擬機器大小，且這麼做會將虛擬機器移至不同的主機，暫存磁碟機會變更回 D 磁碟機。

## 變更磁碟機代號

1. 登入虛擬機器。 

2. 將 pagefile.sys 從 D 磁碟機移至另一個磁碟機。

3. 重新啟動虛擬機器。

4. 	重新登入，並將磁碟機代號從 D 變更為 E。

5.	從 [Azure 管理入口網站](http://manage.windowsazure.com)，連接現有的資料磁碟或空的資料磁碟。

6.	重新登入虛擬機器、初始化磁碟，並指定 D 作為剛連接磁碟的磁碟機代號。

7.	驗證 E 是否已對應至暫存磁碟。

8.	將 pagefile.sys 從其他磁碟機移至 E 磁碟機。

## 其他資源
[如何登入執行 Windows Server 的虛擬機器]

[如何從虛擬機器中斷連結資料磁碟]

[關於 Azure 儲存體帳戶]

<!--Link references-->
[如何將資料磁碟連接至 Windows 虛擬機器]: ../storage-windows-attach-disk
[關於 Azure 中的虛擬機器磁碟]: ../http://msdn.microsoft.com/zh-tw/library/azure/dn790303.aspx
[建立並上傳 Windows Server VHD 到 Azure]: ../virtual-machines-create-upload-vhd-windows-server/
[如何登入執行 Windows Server 的虛擬機器]: ../virtual-machines-log-on-windows-server/
[如何從虛擬機器中斷連結資料磁碟]: ../storage-windows-detach-disk/
[關於 Azure 儲存體帳戶]: ../storage-whatis-account/




<!--HONumber=42-->
