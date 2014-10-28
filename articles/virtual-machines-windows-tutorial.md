<properties linkid="manage-windows-tutorial-virtual-machine-gallery" urlDisplayName="Create a virtual machine" pageTitle="Create a virtual machine running Windows Server in Azure" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server. " metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# 建立執行 Windows Server 的虛擬機器

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/virtual-machines-windows-tutorial/" title="Azure 入口網站" class="current">Azure 入口網站</a><a href="/zh-tw/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure 預覽入口網站">Azure 預覽入口網站</a></div>

本教學課程說明如何使用 Azure 管理入口網站中的映像庫輕鬆建立執行 Windows Server 的 Azure 虛擬機器 (VM)。映像庫提供多種映像，包括 Windows 作業系統、Linux 架構的作業系統和應用程式映像。

> [WACOM.NOTE] 您不需要有任何 Azure VM 的使用經驗，也能完成本教學課程。但您必須要有 Azure 帳戶。只需要幾分鐘的時間，您就可以建立免費試用帳戶。如需詳細資料，請參閱＜[建立 Azure 帳戶][建立 Azure 帳戶]＞。

本教學課程將說明：

-   [如何建立虛擬機器][如何建立虛擬機器]
-   [如何在建立虛擬機器之後登入][如何在建立虛擬機器之後登入]
-   [如何將資料磁碟連接至新的虛擬機器][如何將資料磁碟連接至新的虛擬機器]

若要深入了解，請參閱[虛擬機器][虛擬機器]。

## <span id="createvirtualmachine"></span> </a>如何建立虛擬機器

本節說明如何在管理入口網站中使用 [從組件庫] 選項建立虛擬機器。此選項提供的組態選擇比 [快速建立] 選項還多。例如，如果您要將虛擬機器加入虛擬網路中，您必須使用 [從組件庫] 選項。

> [WACOM.NOTE] 您也可以使用更豐富而可自訂的 [Azure 預覽入口網站][1]來建立虛擬機器、將多機器應用程式範本的部署自動化、使用增強的 VM 監控和診斷功能，和執行其他作業。兩個入口網站中的可用 VM 組態選項有許多重疊之處，但並不完全相同。

[WACOM.INCLUDE [virtual-machines-create-WindowsVM][virtual-machines-create-WindowsVM]]

## <span id="logon"></span> </a>如何在建立虛擬機器之後登入

本節為您說明如何登入虛擬機器，以管理其設定和將在虛擬機器上執行的應用程式。

[WACOM.INCLUDE [virtual-machines-log-on-win-server][virtual-machines-log-on-win-server]]

## <span id="attachdisk"></span> </a>如何將資料磁碟連接至新的虛擬機器

本節說明如何將空的資料磁碟連接至虛擬機器。請參閱[連接資料磁碟教學課程][連接資料磁碟教學課程]，以取得連接空磁碟和如何連接現有磁碟的詳細資訊。

1.  登入 Azure [管理入口網站][管理入口網站]。

2.  按一下 \[虛擬機器\]，然後選取 \[MyTestVM\] 虛擬機器。

    ![Select MyTestVM][Select MyTestVM]

3.  您可能會先進入 [快速入門] 頁面。若是如此，請選取頂端的 [儀表板]。

    ![Select Dashboard][Select Dashboard]

4.  在命令列上按一下 [連接]，然後在 \[連接空的磁碟\] 蹦現時加以點選。

    ![Select Attach from the command bar][Select Attach from the command bar]

5.  系統已為您定義 [虛擬機器名稱]、[儲存位置]、[檔案名稱] 和 [主機快取喜好設定]。您只需要輸入想要的磁碟大小。在 [大小] 欄位中輸入 **5**。接著，按一下核取記號將空磁碟連接至虛擬機器。

    ![Specify the size of the empty disk][Specify the size of the empty disk]

    > [WACOM.NOTE] 所有磁碟都是從 Windows Azure 儲存體中的 VHD 檔案建立的。在 [檔案名稱] 下，您可以為新增至儲存體的 VHD 檔案提供名稱，但是 Azure 會自動產生磁碟的名稱。

6.  返回儀表板，驗證空的資料磁碟已成功連接至虛擬機器。它會與 OS 磁碟並列為 [磁碟] 清單中的第二個磁碟。

    ![連接空的磁碟][連接空的磁碟]

    將資料磁碟連接至虛擬機器之後，磁碟會處於離線狀態且未初始化。您必須登入虛擬機器並初始化磁碟，才能使用磁碟來儲存資料。

7.  使用上一節[如何在建立虛擬機器之後登入][如何在建立虛擬機器之後登入] (\#logon) 中的步驟，連接到虛擬機器。

8.  登入虛擬機器之後，開啟 [伺服器管理員]。在左窗格中，選取 [File and Storage Services]。

    ![Expand File and Storage Services in Server Manager][Expand File and Storage Services in Server Manager]

9.  從展開的功能表中選取 [磁碟]。

    ![Expand File and Storage Services in Server Manager][2]

10. 在 [磁碟] 區段中，清單中會有三個磁碟：磁碟 0、磁碟 1 和磁碟 2。磁碟 0 是 OS 磁碟、磁碟 1 是暫存資源磁碟 (不應用於資料儲存體)，磁碟 2 是您已連接至虛擬機器的資料磁碟。請注意，依據先前的指定，資料磁碟的容量為 5 GB。請以滑鼠右鍵按一下磁碟 2，然後選取 [初始化]。

    ![開始初始化][開始初始化]

11. 按一下 [是] 開始初始化程序。

    ![Continue initialization][Continue initialization]

12. 再次以滑鼠右鍵按一下磁碟 2，然後選取 [新增磁碟區]。

    ![建立磁碟區][建立磁碟區]

13. 使用提供的預設值完成精靈。精靈完成時，新磁碟區會列示在 [磁碟區] 區段中。

    ![建立磁碟區][3]

    磁碟現在為上線狀態，可以搭配新的磁碟機代號來使用。

## 後續步驟

若要深入了解如何在 Azure 上設定 Windows 虛擬機器，請參閱下列文章：

[如何連接至雲端服務中的虛擬機器][如何連接至雲端服務中的虛擬機器]

[如何建立和上傳包含 Windows Server 作業系統的個人虛擬硬碟][如何建立和上傳包含 Windows Server 作業系統的個人虛擬硬碟]

[將資料磁碟連接至虛擬機器][連接資料磁碟教學課程]

[管理虛擬機器的可用性][管理虛擬機器的可用性]

  [Azure 入口網站]: /zh-tw/documentation/articles/virtual-machines-windows-tutorial/ "Azure 入口網站"
  [Azure 預覽入口網站]: /zh-tw/documentation/articles/virtual-machines-windows-tutorial-azure-preview/ "Azure 預覽入口網站"
  [建立 Azure 帳戶]: http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/
  [如何建立虛擬機器]: #createvirtualmachine
  [如何在建立虛擬機器之後登入]: #logon
  [如何將資料磁碟連接至新的虛擬機器]: #attachdisk
  [虛擬機器]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [1]: https://portal.azure.com
  [virtual-machines-create-WindowsVM]: ../includes/virtual-machines-create-WindowsVM.md
  [virtual-machines-log-on-win-server]: ../includes/virtual-machines-log-on-win-server.md
  [連接資料磁碟教學課程]: http://www.windowsazure.com/zh-tw/documentation/articles/storage-windows-attach-disk/
  [管理入口網站]: http://manage.windowsazure.com
  [Select MyTestVM]: ./media/virtual-machines-windows-tutorial/selectvm.png
  [Select Dashboard]: ./media/virtual-machines-windows-tutorial/dashboard.png
  [Select Attach from the command bar]: ./media/virtual-machines-windows-tutorial/commandbarattach.png
  [Specify the size of the empty disk]: ./media/virtual-machines-windows-tutorial/emptydisksize.png
  [連接空的磁碟]: ./media/virtual-machines-windows-tutorial/disklistwithdatadisk.png
  [Expand File and Storage Services in Server Manager]: ./media/virtual-machines-windows-tutorial/fileandstorageservices.png
  [2]: ./media/virtual-machines-windows-tutorial/selectdisks.png
  [開始初始化]: ./media/virtual-machines-windows-tutorial/initializedisk.png
  [Continue initialization]: ./media/virtual-machines-windows-tutorial/yesinitialize.png
  [建立磁碟區]: ./media/virtual-machines-windows-tutorial/initializediskvolume.png
  [3]: ./media/virtual-machines-windows-tutorial/newvolumecreated.png
  [如何連接至雲端服務中的虛擬機器]: http://www.windowsazure.com/zh-tw/documentation/articles/cloud-services-connect-virtual-machine/
  [如何建立和上傳包含 Windows Server 作業系統的個人虛擬硬碟]: http://www.windowsazure.com/zh-tw/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [管理虛擬機器的可用性]: http://www.windowsazure.com/zh-tw/documentation/articles/manage-availability-virtual-machines/
