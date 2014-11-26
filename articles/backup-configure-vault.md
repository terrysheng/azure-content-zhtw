<properties linkid="manage-services-recovery-configure-backup-vault" urlDisplayName="Configure a Backup Vault" pageTitle="Configure Azure Recovery Services to quickly and easily back-up Windows Server" metaKeywords="disaster recovery" description="Use this tutorial to learn how to use the Backup service in Microsoft's Azure cloud offering to back up Windows Server to the cloud." metaCanonical="" services="recovery-services" documentationCenter="" title="Configure Azure Backup to quickly and easily back-up Windows Server" authors="raynew" solutions="" manager="johndaw" editor="tysonn" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# <span id="configure-a-backup-vault-tutorial"></span></a>設定 Azure 備份以輕鬆迅速地備份 Windows Server

<div class="dev-callout"> 
<strong>注意</strong>
 
<p>若要完成此教學課程，您需要 Azure 帳戶。本教學課程將引導您啟用 Azure 備份功能。您之前需要建立或取得 X.509 第 3 版憑證，才能註冊備份伺服器。這些憑證仍受到支援，但現在為了簡化向伺服器註冊 Azure 保存庫的作業，您可以直接從 [快速啟動] 頁面產生保存庫認證。 </p>
<ul> 
<li>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="/zh-tw/pricing/free-trial/">Azure 免費試用</a>。</li> 
 

</ul>
 

</div>

若要將 Windows Server 中的檔案與資料備份至 Azure，您必須在要將這些資料儲存於的區域建立一個備份保存庫。本教學課程將逐步引導您建立要用來儲存備份的保存庫、下載保存庫認證、安裝備份代理程式，並大致介紹可透過管理入口網站使用的備份管理工作。

## <span id="create"></span></a>建立備份保存庫

1.  登入[管理入口網站][管理入口網站]。

2.  按一下 [新增]，依序指向 [資料服務] 和 [復原服務]，然後依序按一下 [備份保存庫] 和 [快速建立]。

3.  在 [名稱] 中，輸入易記名稱來識別備份保存庫。

4.  在 [區域] 中，選取備份保存庫的區域。
    ![新增備份保存庫][新增備份保存庫]

5.  按一下 [建立保存庫]。

    要等備份保存庫建立好，可能需要一些時間。若要檢查狀態，可以監控位於入口網站底部的通知。建立好備份保存庫之後，將有一則訊息告訴您已順立建立保存庫，而且該保存庫將以 [作用中] 狀態列在復原服務的資源中。
    ![備份保存庫建立][備份保存庫建立]

6.  如果您有多個與組織帳戶相關聯的訂用帳戶，請選擇與備份保存庫相關聯的正確帳戶。

## <span id="upload"></span></a>下載保存庫認證

保存庫認證透過取代憑證向伺服器註冊您的 Azure 服務。您仍可使用憑證，但保存庫認證在使用上更容易，因為您可以使用 Azure 入口網站產生及下載保存庫認證。

1.  登入[管理入口網站][管理入口網站]。

2.  按一下 [復原服務]，然後選取您要向伺服器註冊的備份保存庫。備份保存庫的 [快速啟動] 頁面隨即出現。

3.  在 [快速啟動] 頁面上，按一下 [下載保存庫認證]，以提示入口網站產生並下載您要用來註冊伺服器之備份保存庫的保存庫認證。

4.  入口網站會使用保存庫名稱和目前日期的組合來產生保存庫認證。按一下 [儲存] 將保存庫認證下載至本機帳戶的下載資料夾，或從 [儲存] 功能表中選取 [另存新檔]，以指定保存庫認證的位置。您無法編輯保存庫認證，因此不需要按 [開啟]。下載認證之後，系統會提示您開啟資料夾。按一下 **x** 關閉這個功能表。

## <span id="download"></span></a>下載並安裝備份代理程式

1.  在[管理入口網站][管理入口網站]中。

2.  按一下 [復原服務]，然後選取備份保存庫以檢視其 [快速啟動] 頁面。

3.  在 [快速啟動] 頁面上，選取您要下載的代理程式類型。您可以選擇下載 **Azure Backup Agent**、**Windows Server 和 System Center Data Protection Manager** 或 **Windows Server Essentials**。如需詳細資訊，請參閱：

    -   [安裝 Azure Backup Agent for Windows Server 2012 and System Center 2012 SP1 - Data Protection Manager][安裝 Azure Backup Agent for Windows Server 2012 and System Center 2012 SP1 - Data Protection Manager]
    -   [安裝 Azure Backup Agent for Windows Server 2012 Essentials][安裝 Azure Backup Agent for Windows Server 2012 Essentials]

安裝好代理程式後，您就可以使用適當的本機管理介面 (例如 Microsoft Management Console 嵌入式管理單元、System Center Data Protection Manager 主控台，或 Windows Server Essentials 儀表板)，來設定伺服器的備份原則。

## <span id="manage"></span></a>管理備份保存庫與伺服器

1.  登入[管理入口網站][管理入口網站]。

2.  按一下 [復原服務]，然後按一下備份保存庫的名稱以檢視 [快速啟動] 頁面。

3.  按一下 [儀表板] 以查看伺服器的使用量概觀。您可以在儀表板下方執行下列工作：

    -   **管理憑證**。如果已使用憑證註冊伺服器，這個選項可用來更新憑證。如果使用保存庫認證，則請勿使用 [管理憑證]。
    -   **刪除**。刪除目前的備份保存庫。如果您有不再使用的備份保存庫，可將之刪除來釋出儲存空間。只有在從保存庫中刪除了所有已註冊的伺服器之後，[刪除] 才會啟用。
    -   **保存庫認證**。這個 [快速概覽] 功能表項目可用來設定您的保存庫認證。

4.  按一下 [受保護項目]，以檢視已從伺服器備份的項目。這份清單僅做提供資訊之用。
    ![受保護項目][受保護項目]

5.  按一下 [伺服器]，以檢視已註冊至此保存庫的伺服器名稱。您可以在其中執行下列工作：

    -   **允許重新註冊**。為伺服器選取此選項時，您可以使用代理程式中的註冊精靈，再次向備份保存庫註冊伺服器。如果憑證中有錯誤，或必須重建伺服器，您可能需要重新註冊。每個伺服器名稱僅能重新註冊一次。
    -   **刪除**。從備份保存庫中刪除伺服器。所有與該伺服器相關聯的已儲存資料都將立即刪除。

        ![已刪除的伺服器][已刪除的伺服器]

## <span id="next"></span></a>後續步驟

-   若要深入了解 Azure 備份，請參閱 [Azure 備份概觀][Azure 備份概觀] (英文)。

-   造訪 [Azure 備份論壇][Azure 備份論壇] (英文)。

  [管理入口網站]: https://manage.windowsazure.com
  [新增備份保存庫]: http://i.imgur.com/506c7ch.png
  [備份保存庫建立]: http://i.imgur.com/grtLcKM.png
  [安裝 Azure Backup Agent for Windows Server 2012 and System Center 2012 SP1 - Data Protection Manager]: http://technet.microsoft.com/zh-tw/library/hh831761.aspx#BKMK_installagent
  [安裝 Azure Backup Agent for Windows Server 2012 Essentials]: http://technet.microsoft.com/zh-tw/library/jj884318.aspx
  [受保護項目]: ./media/backup-configure-vault/RS_protecteditems.png
  [已刪除的伺服器]: ./media/backup-configure-vault/RS_deletedserver.png
  [Azure 備份概觀]: http://go.microsoft.com/fwlink/p/?LinkId=222425
  [Azure 備份論壇]: http://go.microsoft.com/fwlink/p/?LinkId=290933
