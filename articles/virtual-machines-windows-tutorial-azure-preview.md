<properties linkid="virtual-machines-windows-tutorial-azure-preview" urlDisplayName="Create a virtual machine in the Preview Portal" pageTitle="Create a virtual machine running Windows Server in the Azure Preview Portal" metaKeywords="Azure image gallery vm" description="Learn how to create an Azure virtual machine (VM) running Windows Server, using the VM Gallery in the Azure Preview Portal" metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="danlep,kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="danlep,kathydav"></tags>

# 在 Azure 預覽入口網站中建立執行 Windows Server 的虛擬機器

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/virtual-machines-windows-tutorial/" title="Azure 入口網站">Azure 入口網站</a><a href="/zh-tw/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure 預覽入口網站" class="current">Azure 預覽入口網站</a></div>

本教學課程說明如何使用 Azure 預覽入口網站中的 VM 映像庫，輕鬆建立執行 Windows Server 的 Azure 虛擬機器 (VM)。映像庫提供多種映像，包括 Windows 作業系統、Linux 架構的作業系統和應用程式映像。

> [WACOM.NOTE] 您不需要有任何 Azure VM 的使用經驗，也能完成本教學課程。但您必須要有 Azure 帳戶。只需要幾分鐘的時間，您就可以建立免費試用帳戶。如需詳細資料，請參閱＜[建立 Azure 帳戶][建立 Azure 帳戶]＞。

本教學課程將說明：

-   [如何建立虛擬機器][如何建立虛擬機器]
-   [如何在建立虛擬機器之後登入][如何在建立虛擬機器之後登入]

若要深入了解，請參閱[虛擬機器][虛擬機器]。

## <span id="createvirtualmachine"></span> </a>如何建立虛擬機器

本節說明如何使用預覽入口網站來建立執行 Windows Server 的 VM。您可以在大部分的組態中使用 Azure 的預設設定，只需幾分鐘的時間即可建立 VM。

1.  登入 [Azure 預覽入口網站][1]。如果您還沒有訂閱，請參考[免費試用][免費試用]優惠。

2.  在 [中心] 功能表上，按一下 [新增]。

    ![Select New from the Command Bar][Select New from the Command Bar]

3.  在 [新增] 下按一下 [全部]，然後在 [映像庫] 下按一下 [虛擬機器]。按一下 [Windows Server 2012 R2 Datacenter]。按一下此處的 [建立]。

    ![Select a VM image from the Gallery][Select a VM image from the Gallery]

4.  在 [建立 VM] 分頁上，填入您想要的 VM [主機名稱]、系統管理 [使用者名稱，及強式 [密碼]。

    ![Configure host name and log on credentials][Configure host name and log on credentials]

    > [WACOM.NOTE] **使用者名稱**是指用來管理伺服器的系統管理帳戶。建立此帳戶的唯一密碼，並確定記住此密碼。**您將需要使用者名稱和密碼才能登入虛擬機器**。

5.  若要在剩下的 VM 選項中使用預設設定並開始建立 VM，請按一下 [建立]。在按下 [建立] 之前，您可以選擇瀏覽 [選用設定] 設定。例如，您可以在 VM 上設定選用診斷，以便稍後在您的 VM 上追蹤各種度量。依序按一下 [選用設定]、[診斷]，然後將 [狀態] 切換成 [開啟]。

    ![Turn on VM diagnostics][Turn on VM diagnostics]

    > [WACOM.NOTE] 如果開啟 Azure 診斷，則 Azure 會將診斷資料儲存在 Azure 儲存體帳戶中，但這會產生額外的儲存成本。

6.  當 Azure 建立 VM 時，您可以在 [中心] 功能表的 [通知] 中持續追蹤進度。在 Azure 建立 VM 後，您便可以在您的 [開始面板] 中看到此 VM。

    ![VM appears on the Startboard][VM appears on the Startboard]

## <span id="logon"></span> </a>如何在建立虛擬機器之後登入

本節為您說明如何登入 VM，以管理其設定和將在 VM 上執行的應用程式。

> [WACOM.NOTE] 如需需求和疑難排解秘訣，請參閱[透過 RDP 或 SSH 連線至 Azure 虛擬機器][透過 RDP 或 SSH 連線至 Azure 虛擬機器]。

1.  如果您未曾執行過這項操作，請登入 [Azure 預覽入口網站][1]。

2.  在 [開始面板] 上按一下您的 VM。如果您要尋找 VM，請按一下 [瀏覽]，然後按一下 [虛擬機器]。

    ![Browse to find the VM][Browse to find the VM]

3.  在 VM 分頁上，按一下頂端的 [連接]。

    ![登入虛擬機器][登入虛擬機器]

4.  按一下 [開啟]，並使用系統自動為虛擬機器建立的遠端桌面通訊協定檔案。

5.  按一下 [連接] 以繼續進行連線程序。

    ![繼續連接][繼續連接]

6.  在虛擬機器上輸入系統管理帳戶的使用者名稱和密碼，然後按一下 [確定]。

7.  按一下 [是] 以驗證虛擬機器的身分識別。

    ![驗證機器的身分識別][驗證機器的身分識別]

    您現在可以開始使用虛擬機器，就如同操作任何其他伺服器一樣。

## 後續步驟

若要深入了解在 Azure 上設定 Windows 虛擬機器，請參閱下列文章：

[如何連接至雲端服務中的虛擬機器][如何連接至雲端服務中的虛擬機器]

[如何建立和上傳包含 Windows Server 作業系統的個人虛擬硬碟][如何建立和上傳包含 Windows Server 作業系統的個人虛擬硬碟]

[將資料磁碟連接至虛擬機器][將資料磁碟連接至虛擬機器]

[管理虛擬機器的可用性][管理虛擬機器的可用性]

  [Azure 入口網站]: /zh-tw/documentation/articles/virtual-machines-windows-tutorial/ "Azure 入口網站"
  [Azure 預覽入口網站]: /zh-tw/documentation/articles/virtual-machines-windows-tutorial-azure-preview/ "Azure 預覽入口網站"
  [建立 Azure 帳戶]: http://www.windowsazure.com/zh-tw/develop/php/tutorials/create-a-windows-azure-account/
  [如何建立虛擬機器]: #createvirtualmachine
  [如何在建立虛擬機器之後登入]: #logon
  [虛擬機器]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [1]: https://portal.azure.com
  [免費試用]: http://www.windowsazure.com/zh-tw/pricing/free-trial/
  [Select New from the Command Bar]: ./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png
  [Select a VM image from the Gallery]: ./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png
  [Configure host name and log on credentials]: ./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png
  [Turn on VM diagnostics]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_diagnostics_status_preview_portal.png
  [VM appears on the Startboard]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png
  [透過 RDP 或 SSH 連線至 Azure 虛擬機器]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Browse to find the VM]: ./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png
  [登入虛擬機器]: ./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png
  [繼續連接]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [驗證機器的身分識別]: ./media/virtual-machines-log-on-windows-server/connectverify.png
  [如何連接至雲端服務中的虛擬機器]: http://www.windowsazure.com/zh-tw/documentation/articles/cloud-services-connect-virtual-machine/
  [如何建立和上傳包含 Windows Server 作業系統的個人虛擬硬碟]: http://www.windowsazure.com/zh-tw/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [將資料磁碟連接至虛擬機器]: http://www.windowsazure.com/zh-tw/documentation/articles/storage-windows-attach-disk/
  [管理虛擬機器的可用性]: http://www.windowsazure.com/zh-tw/documentation/articles/manage-availability-virtual-machines/
