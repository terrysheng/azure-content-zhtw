<properties urlDisplayName="Create a virtual machine in the Preview Portal" pageTitle="在 Azure Preview 入口網站中建立執行 Windows 的虛擬機器" metaKeywords="Azure 映像庫 vm" description="了解如何使用 Azure 預覽入口網站中的 VM 映像庫，來建立執行 Windows 的 Azure 虛擬機器 (VM)" metaCanonical="" services="virtual-machines" documentationCenter="" title="Create a Virtual Machine Running Windows in the Azure Preview Portal" authors="danlep,kathydav,rasquill" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="danlep,kathydav,rasquill" />

<!---Workflow can be confusing...have to select the correct size with **Browse all pricing tiers** then click Select on that pane and the **Recommended pricing tiers** pane to apply. But even after that, it didn't seem to pick the Premium storage account...showed up as 'standard GRS' when I inspected what type of storage account would be used for the VM. 
-->

# 在 Azure Preview 入口網站中建立虛擬機器#

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal">Azure 入口網站</a><a href="/zh-tw/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal" class="current">Azure Preview 入口網站</a></div>

本教學課程說明如何使用 Azure 預覽入口網站中的映像庫 (以 Windows Server 映像為例)，輕鬆建立執行 Windows 的 Azure 虛擬機器 (VM)。映像庫提供多種映像，包括 Windows 作業系統、Linux 架構的作業系統和應用程式映像。 

> [WACOM.NOTE] 您不需要有任何 Azure VM 的使用經驗，也能完成本教學課程。但您必須要有 Azure 帳戶。只需要幾分鐘的時間，您就可以建立免費試用帳戶。如需詳細資訊，請參閱[建立 Azure 帳戶](http://www.windowsazure.com/zh-tw/develop/php/tutorials/create-a-windows-azure-account/)。 

本教學課程將說明：

- [如何建立虛擬機器](#createvirtualmachine)
- [如何在建立虛擬機器之後登入](#logon)

若要深入了解，請參閱[虛擬機器](http://go.microsoft.com/fwlink/p/?LinkID=271224)。


##<a id="createvirtualmachine"> </a>如何建立虛擬機器##

本節說明如何使用 Preview 入口網站來建立 VM (以 Windows Server 2012 R2 Datacenter 為例)。您可以在大部分的組態中使用 Azure 的預設設定，只需幾分鐘的時間即可建立 VM。

> [WACOM.NOTE] 您可以使用的映像取決於您的訂用帳戶。本教學課程使用 Windows Server 映像，但 MSDN 訂閱可能會提供其他映像，包括桌面映像。 
 

1. 登入 [Azure Preview 入口網站](https://portal.azure.com)。如果您還沒有訂用帳戶，請參考[免費試用](http://www.windowsazure.com/zh-tw/pricing/free-trial/)優惠。

2. 在 [中心] 功能表上，按一下 [新增]****。

	![Select New from the Command Bar](./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png)

3. 在 [新增]**** 刀鋒視窗中，按一下 [所有項目]****，按一下 [虛擬機器]****，按一下 [Windows Server 2012 R2 Datacenter]****，然後按一下 [建立]****。

	![Select a VM image from the Gallery](./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png)
	
4. 在 [建立 VM]**** 刀鋒視窗上，填入您想要的 VM [主機名稱]****、系統管理 [使用者名稱]****，及強式 [密碼]****。  

	>[WACOM.NOTE] **使用者名稱**是指用來管理伺服器的系統管理帳戶。建立此帳戶的唯一密碼，並確定記住此密碼。**您將需要使用者名稱和密碼才能登入虛擬機器**。
	

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_name_pwd_preview_portal.png)
	
	
5. 檢閱預設設定，例如 [定價層]****、[選擇性組態]**** 及 [位置]****。這些選項會影響 VM 的大小，以及如網域成員資格等的網路功能選項。例如，若要在虛擬機器上試用進階儲存體，您將必須挑選區域和支援它的大小。 

	>[WACOM.NOTE] 進階儲存體位於 Preview 中，可供某些區域的 DS 系列虛擬機器使用。如需詳細資訊，請參閱[進階儲存體：適合 Azure 虛擬機器工作負載的高效能儲存體](http://azure.microsoft.com/zh-tw/documentation/articles/storage-premium-storage-preview-portal/)。

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png)
	
6. 當您完成檢閱或更新設定時，請按一下 [建立]****。	

7. 當 Azure 建立 VM 時，您可以在 [中心] 功能表的 [通知]**** 中持續追蹤進度。在 Azure 建立 VM 後，您便可以在您的 [開始面板] 中看到此 VM。

	![VM appears on the Startboard](./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png)

## <a id="logon"> </a>如何在建立虛擬機器之後登入##

本節為您說明如何登入 VM，以管理其設定和將在 VM 上執行的應用程式。

>[WACOM.NOTE] 如需需求與疑難排解提示，請參閱[透過 RDP 或 SSH 連線至 Azure 虛擬機器](http://go.microsoft.com/fwlink/p/?LinkId=398294)。

1. 如果您未曾執行過這項操作，請登入 [Azure Preview 入口網站](https://portal.azure.com)。

2. 在 [開始面板] 上按一下您的 VM。如果您要尋找 VM，請按一下 [瀏覽]****，然後按一下 [虛擬機器]****。 

	![Browse to find the VM](./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png)

3. 在 VM 刀鋒視窗上，按一下頂端的 [連線]****。

	![Log on to the virtual machine](./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png)

4. 按一下 [開啟]**** 以使用系統自動為虛擬機器建立的遠端桌面通訊協定檔案。
	
5. 按一下 [連線]**** 繼續進行連線程序。

	![Continue with connecting](./media/virtual-machines-log-on-windows-server/connectpublisher.png)

6. 在虛擬機器上輸入系統管理帳戶的使用者名稱與密碼，然後按一下 [確定]****。
	
7. 按一下 [是]**** 以驗證虛擬機器的身分識別。

	![Verify the identity of the machine](./media/virtual-machines-log-on-windows-server/connectverify.png)

	您現在可以開始使用虛擬機器，就如同操作任何其他伺服器一樣。

##後續步驟 

若要深入了解在 Azure 上設定 Windows 虛擬機器，請參閱下列文章：

[如何連線雲端服務中的虛擬機器](http://www.windowsazure.com/zh-tw/documentation/articles/cloud-services-connect-virtual-machine/)

[如何建立和上傳包含 Windows Server 作業系統的個人虛擬硬碟](http://www.windowsazure.com/zh-tw/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[將資料磁碟連接至虛擬機器](http://www.windowsazure.com/zh-tw/documentation/articles/storage-windows-attach-disk/)

[管理虛擬機器的可用性](http://www.windowsazure.com/zh-tw/documentation/articles/manage-availability-virtual-machines/)

[關於 Azure VM 組態設定](http://msdn.microsoft.com/library/azure/dn763935.aspx)

[如何建立虛擬機器]: #custommachine
[如何在建立虛擬機器之後登入]: #logon

<!--HONumber=35.2-->
