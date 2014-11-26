<properties title="How To Change the Drive Letter of the Windows Temporary Disk" pageTitle="How To Change the Drive Letter of the Windows Temporary Disk" description="Describes how to remap the temporary disk on a Windows VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# 如何變更 Windows 暫存磁碟的磁碟機代號

如果您要將 D 磁碟機用於其他目的，您可以變更暫存磁碟的磁碟機代號。如此做可能是為了支援使用 D 磁碟機作為永久儲存位置的應用程式或服務。

開始執行之前，請確定您已具備下列項目：

-   在此程序期間，您可用來儲存 Windows 頁面檔案 (pagefile.sys) 的連接資料磁碟。如需指示，請參閱[如何將資料磁碟連接至 Windows 虛擬機器][如何將資料磁碟連接至 Windows 虛擬機器]。
-   儲存體帳戶中已上傳的 VHD (如果您想要在 D 磁碟機上使用現有資料磁碟的 VHD)。如需指示，請參閱[建立 Windows Server VHD 並上傳至 Azure][建立 Windows Server VHD 並上傳至 Azure] 中的步驟 3 和 4。

## 變更磁碟機代號

1.  登入虛擬機器。

2.  將 pagefile.sys 從 D 磁碟機移至另一個磁碟機。

3.  重新啟動虛擬機器。

4.  重新登入，並將磁碟機代號從 D 變更為 E。

5.  在 [Azure 管理入口網站][Azure 管理入口網站]中，連接現有的資料磁碟或空的資料磁碟。

6.  重新登入虛擬機器、初始化磁碟，並指定 D 作為剛連接磁碟的磁碟機代號。

7.  驗證 E 是否已對應至暫存磁碟。

8.  將 pagefile.sys 從其他磁碟機移至 E 磁碟機。

## 其他資源

[如何登入執行 Windows Server 的虛擬機器][如何登入執行 Windows Server 的虛擬機器]

[如何從虛擬機器卸離資料磁碟][如何從虛擬機器卸離資料磁碟]

[什麼是儲存體帳戶？][什麼是儲存體帳戶？]

<!--Link references-->

  [如何將資料磁碟連接至 Windows 虛擬機器]: ../storage-windows-attach-disk
  [建立 Windows Server VHD 並上傳至 Azure]: ../virtual-machines-create-upload-vhd-windows-server/
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [如何登入執行 Windows Server 的虛擬機器]: ../virtual-machines-log-on-windows-server/
  [如何從虛擬機器卸離資料磁碟]: ../storage-windows-detach-disk/
  [什麼是儲存體帳戶？]: ../storage-whatis-account/
