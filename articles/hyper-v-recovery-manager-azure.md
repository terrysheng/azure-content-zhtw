<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="開始使用 Azure Site Recovery：內部部署至 Azure 保護" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure 站台復原可協調將位於內部部署 VMM 雲端中的 Hyper-V 虛擬機器複寫、容錯移轉及復原至 Azure 的作業。" metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="開始使用 Azure Site Recovery：內部部署至 Azure 保護" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# 開始使用 Azure Site Recovery：內部部署至 Azure 保護

<div class="dev-callout">

使用 Azure Site Recovery 可協調對 VMM 雲端中內部部署 Hyper-V 主機伺服器上之虛擬機器的保護。您可以設定：

-   **內部部署對 Azure 保護**：將內部部署虛擬機器複寫至 Azure。您可以在 Azure 站台復原保存庫中設定和啟用保護設定。虛擬機器資料會從一部內部部署 Hyper-V 伺服器複寫至 Azure 儲存體。
-   **內部部署對內部部署保護**：將內部部署虛擬機器複寫至另一個內部部署網站。您可以在 Azure 站台復原保存庫中設定和啟用保護設定。虛擬機器資料會從一部內部部署 Hyper-V 伺服器複寫至另一部伺服器。 如需這個案例的詳細資訊，請參閱[開始使用 Azure Site Recovery：內部部署對內部部署保護][開始使用 Azure Site Recovery：內部部署對內部部署保護]。

## <span id="about"></span></a>關於本教學課程

您可以使用這個教學課程，在內部部署 Azure 部署中設定 Azure Site Recovery 的快速概念證明。該教學課程盡可能使用最快的路徑和預設設定。您將會建立 Azure 站台復原保存庫、在來源 VMM 伺服器中安裝 Azure 站台復原提供者、在位於 VMM 雲端中的 Hyper-V 主機伺服器上安裝 Azure 復原服務代理程式、設定雲端保護設定、啟用虛擬機器的保護功能，以及測試您的部署。

如果您需要完整部署的相關資訊，請參閱：

-   [規劃 Azure 站台復原部署][規劃 Azure 站台復原部署] - 說明在開始完整部署之前所需完成的規劃步驟。
-   [部署 Azure Site Recovery：內部部署至 Azure 保護][部署 Azure Site Recovery：內部部署至 Azure 保護] - 提供完整部署的逐步指示。

如果您在進行本教學課程期間發生問題，請檢閱 Wiki 文章 [Azure Site Recovery：常見錯誤案例和解決方法][Azure Site Recovery：常見錯誤案例和解決方法] (英文)，或在 [Azure 復原服務論壇][Azure 復原服務論壇]提出您的問題。

</div>

## <span id="before"></span></a>開始之前

<div class="dev-callout">

開始本教學課程之前，請確認已達到先決條件。

### <span id="HVRMPrereq"></span></a>必要條件

-   **Azure 帳戶**：您將需要一個 Azure 帳戶。如果您沒有帳戶，請參閱 [Azure 免費試用][Azure 免費試用]。您可以從 [Azure Site Recovery 管理員定價詳細資料][Azure Site Recovery 管理員定價詳細資料] (英文) 取得定價資訊。
-   **Azure 儲存體帳戶** - 您需要 Azure 儲存體帳戶才能將複寫的資料儲存至 Azure。此帳戶必須啟用異地複寫。應該與 Azure 站台復原服務位於相同的區或，且與相同的訂閱相關聯。若要深入了解設定 Azure 儲存體，請參閱 [Microsoft Azure 儲存體簡介][Microsoft Azure 儲存體簡介]。
-   **VMM 伺服器** - 在 System Center 2012 R2 上執行 VMM 伺服器。
-   **VMM 雲端** - VMM 伺服器上至少一個雲端，此雲端應該包含：
    -   一或多個 VMM 主機群組
    -   每個主機群組中的一或多個 Hyper-V 主機伺服器或叢集。
    -   位於雲端中來源 Hyper-V 伺服器上的一或多部虛擬機器。虛擬機器應該為第 1 代。
-   **虛擬機器**—您將需要符合 Azure 需求的虛擬機器。請參閱《規劃指南》中的[必要條件和支援][必要條件和支援]。
-   如需虛擬機器容錯移轉至 Azure 的支援需求完整清單，請閱讀

## <span id="tutorial"></span></a>教學課程步驟

確認已達成先決條件之後，請執行下列動作：

-   [步驟 1：建立保存庫][步驟 1：建立保存庫] - 建立 Azure 站台復原保存庫。
-   [步驟 2：安裝提供者應用程式][步驟 2：安裝提供者應用程式]—產生註冊金鑰，然後在 VMM 伺服器上執行 Microsoft Azure 站台復原提供者應用程式。這會安裝提供者並在保存庫中註冊 VMM 伺服器。
-   [步驟 3：新增 Azure 儲存體帳戶][步驟 3：新增 Azure 儲存體帳戶]—如果您沒有帳戶，請建立一個。
-   [步驟 4：安裝代理程式應用程式][步驟 4：安裝代理程式應用程式] - 在每一個 Hyper-V 主機上安裝 Microsoft Azure 復原服務代理程式。
-   [步驟 5：設定雲端保護][步驟 5：設定雲端保護] - 為 VMM 雲端進行保護設定。
-   [步驟 6：設定網路對應][步驟 6：設定網路對應] - 您可以選擇性地設定網路對應，將來源 VM 網路對應到目標 Azure 網路。
-   [步驟 7：對虛擬機器啟用保護][步驟 7：對虛擬機器啟用保護] - 對受保護 VMM 雲端中的虛擬機器啟用保護。
-   [步驟 8：測試部署][步驟 8：測試部署]—您可以執行單一虛擬機器的測試容錯移轉，或是建立復原計劃，然後執行該計劃的測試容錯移轉。

<a name="vault"></a>

## 步驟 1：建立保存庫

</p>
1.  登入[管理入口網站][管理入口網站]。

2.  依序展開 [資料服務]**** 和 [復原服務]****，然後按一下 [Site Recovery 保存庫]****。

3.  按一下 [建立新項目]****，然後按一下 [快速建立]****。

4.  在 [名稱]**** 中，輸入保存庫的易記識別名稱。

5.  在 [地區]**** 中，選取保存庫的地理區域。可用的地理區域包括 [東亞]、[西歐]、[美國西部]、[美國東部]、[北歐] 和 [東南亞]。
6.  按一下 [建立保存庫]****。

    ![新增保存庫][新增保存庫]

檢查狀態列，以確認是否順利建立保存庫。保存庫在主要復原服務頁面上會列為 [使用中]****。

<a name="download"></a>

## 步驟 2：產生註冊金鑰並安裝 Azure 站台復原提供者

</p>
1.  在 [復原服務]**** 頁面中，按一下保存庫以開啟 [快速啟動] 頁面。您也可以使用圖示隨時開啟 [快速啟動]。

    ![快速啟動圖示][快速啟動圖示]

2.  在下拉式清單中，選取 [Between an on-premises Hyper-V site and Microsoft Azure]。
3.  在 [準備 VMM 伺服器] 中，按一下 [產生註冊金鑰檔案]。該金鑰在產生後會維持 5 天有效。將檔案複製到 VMM 伺服器。設定提供者時需要這個檔案。

    ![註冊金鑰][註冊金鑰]

4.  在 [快速啟動]**** 頁面上，按一下 [準備 VMM 伺服器] 中的 [下載要在 VMM 伺服器上安裝的 Microsoft Azure Site Recovery 提供者]****，以取得最新版的提供者安裝檔案。

5.  在來源 VMM 伺服器上執行此檔案。

6.  在 [必要條件檢查] 中，選取停止 VMM 服務以開始執行提供者安裝程式。服務隨即停止，並將在安裝程式完成時自動重新啟動。

    ![必要條件][必要條件]

7.  在 [Microsoft Update] 中，您可以選擇加入以取得更新。啟用這項設定時，會根據您的 Microsoft Update 原則來安裝提供者更新。

    ![Microsoft Update][Microsoft Update]

安裝提供者之後，請繼續安裝以在保存庫中註冊伺服器。

1.  在 [網際網路連線] 中，指定 VMM 伺服器上執行的提供者連接到網際網路的方式。選取 [Use default system proxy settings]****，以使用在伺服器上設定的預設網際網路連線設定。

    ![網際網路設定][網際網路設定]

2.  在 [註冊金鑰] 中，選取您從 Azure Site Recovery 下載並複製到 VMM 伺服器的金鑰。
3.  在 [保存庫名稱] 中，確認要註冊伺服器的保存庫名稱。
4.  在 [伺服器名稱] 中，指定保存庫中 VMM 伺服器的易記識別名稱。

    ![伺服器註冊][伺服器註冊]

5.  在 [初始雲端中繼資料同步] 中，選取您是否要將 VMM 伺服器上所有雲端的中繼資料與保存庫同步。這個動作只需要在每個伺服器上進行一次。如果不要同步所有雲端，您可以取消核取這項設定，再於 VMM 主控台的雲端屬性中個別同步每個雲端。

6.  在 [資料加密] 中，您可以指定一個位置來儲存自動為資料加密產生的 SSL 憑證。如果您在 Azure 站台復原入口網站中為 Azure 所保護的雲端啟用資料加密，則會使用此憑證。請保護此憑證的安全。當您執行容錯移轉至 Azure 的作業時，您將會選取此憑證以將加密的資料解密。
    如果您是在不同的內部部署站台之間進行複寫，則不適用此選項。

    ![伺服器註冊][1]

7.  按一下 [註冊]**** 完成程序。註冊後，Azure Site Recovery 即可從 VMM 伺服器擷取中繼資料。伺服器會顯示在保存庫中 [伺服器] 頁面上 [資源]**** 索引標籤的結尾。

## <span id="storage"></span></a>步驟 3：建立 Azure 儲存體帳戶

如果您沒有 Azure 儲存體帳戶，請按一下 [新增 Azure 儲存體帳戶]。此帳戶應啟用異地複寫。此帳戶應與 Azure 站台復原服務位於相同的區或，且與相同的訂閱相關聯。

您可以使用這個教學課程，在內部部署 Azure 部署中設定 Azure Site Recovery 的快速概念證明。該教學課程盡可能使用最快的路徑和預設設定。您將會建立 Azure 站台復原保存庫、在來源 VMM 伺服器中安裝 Azure 站台復原提供者、在位於 VMM 雲端中的 Hyper-V 主機伺服器上安裝 Azure 復原服務代理程式、設定雲端保護設定、啟用虛擬機器的保護功能，以及測試您的部署。

![儲存體帳戶][儲存體帳戶]

## <span id="agent"></span></a>步驟 4：在 Hyper-V 主機上安裝 Azure 復原服務代理程式

在 VMM 雲端中您要保護的每一個 Hyper-V 主機伺服器上，安裝 Azure 復原服務代理程式。

1.  在 [快速入門] 頁面上，按一下 [Download Azure Site Recovery Services Agent and install on hosts]****，取得最新版的代理程式安裝檔案。

    ![Install Recovery Services Agent][Install Recovery Services Agent]

2.  在 VMM 雲端中您要保護的每一個 Hyper-V 主機伺服器上執行安裝檔案。
3.  在 [檢查先決條件] 頁面上，按 [下一步]****。將自動安裝任何缺少的必要元件。

    ![Prerequisites Recovery Services Agent][Prerequisites Recovery Services Agent]

4.  在 [安裝設定] 頁面上，指定您要安裝代理程式的位置，並選取將在其中安裝備份中繼資料的快取位置。然後按一下 [安裝]****。

## <span id="clouds"></span></a>步驟 5：設定雲端保護設定

註冊 VMM 伺服器之後，您就可以設定雲端保護設定。當您安裝提供者之後，即可啟用 [將雲端資料與保存庫同步] 選項，如此一來，VMM 伺服器上的所有雲端將會出現在保存庫中的 [受保護的項目]**** 索引標籤中。

![發佈的雲端][發佈的雲端]

1.  在 [快速啟動] 頁面上，按一下 [設定 VMM 雲端的保護]。

2.  在 [受保護項目] 索引標籤上，按一下要設定的雲端，並移至 [設定] 索引標籤。
3.  在 [目標]**** 中，選取 [Microsoft Azure]****。
4.  在 [儲存體帳戶]**** 中，選取要用來儲存 Azure 虛擬機器的 Azure 儲存體。
5.  將 [Encrypt stored data]**** 設為 [關閉]****。此設定指定應該將內部部署與 Azure 之間複寫的資料加密。
6.  在 [複製頻率]**** 中保留預設設定。這個值指定應在來源與目標位置之間同步處理資料的頻率。
7.  在 [Retain recovery points for]**** 中保留預設設定。使用預設值 0 時，只會在複本主機伺服器上儲存主要虛擬機器的最新復原點。
8.  在 [應用程式一致快照的頻率]**** 中保留預設設定。這個值指定建立快照的頻率。快照會使用「磁碟區陰影複製服務」(VSS) 來確保建立快照時，應用程式是處於一致狀態。如果您設定一個值，請確定此值小於您設定的其他復原點數目。
9.  在 [Replication start time]**** 中，指定初次將資料複寫至 Azure 的開始時間。將會使用 Hyper-V 主機伺服器的時區。建議您將初次複寫排定在離峰時段進行。

    ![Cloud replication settings][Cloud replication settings]

儲存設定之後，會建立一個工作，並可在 [工作]**** 索引標籤上進行監視。VMM 來源雲端中的所有 Hyper-V 主機伺服器會設定進行複寫。 儲存之後，可在 [設定]**** 索引標籤上修改雲端設定。若要修改目標位置或目標儲存體，您需要移除雲端組態，然後重新設定雲端。請注意，如果您變更儲存體帳戶，則只會對修改儲存體帳戶之後才啟用保護的虛擬機器套用變更。現有的虛擬機器不會移轉至新的儲存體帳戶。

## <span id="networkmapping"></span></a>步驟 6：設定網路對應

本教學課程說明在測試環境中部署 Azure Site Recovery 的最簡單路徑。如果您不想在進行本教學課程時設定網路對應，請閱讀《規劃指南》中的[準備網路對應][準備網路對應]。若要設定對應，請遵循《部署指南》之[設定網路對應][設定網路對應]中的步驟進行。

## <span id="virtualmachines"></span></a>步驟 7：對虛擬機器啟用保護

正確設定伺服器、雲端和網路後，您就可以對雲端中的虛擬機器啟用保護。請注意：

-   虛擬機器必須符合 Azure 需求。請在《規劃指南》的[必要條件和支援][必要條件和支援]中查看這些需求。
-   若要啟用保護功能，您必須為虛擬機器設定作業系統和作業系統磁碟屬性。當您使用虛擬機器範本在 VMM 中建立虛擬機器時，您可以設定屬性。您也可以在虛擬機器屬性的 [一般] 和 [硬體組態] 索引標籤上，為現有的虛擬機器設定這些屬性。若未在 VMM 中設定這些屬性，您將可在 Azure 站台復原入口網站中加以設定。

![Create virtual machine][Create virtual machine]

![Modify virtual machine properties][Modify virtual machine properties]

1.  若要啟用保護，請在虛擬機器所在雲端中的 [虛擬機器]**** 索引標籤上，按一下 [啟用保護]****，然後選取 [Add virtual machines]****。
2.  從雲端中所有虛擬機器的清單，選取您要保護的虛擬機器。

    ![啟用虛擬機器保護][啟用虛擬機器保護]

3.  驗證虛擬機器屬性，並視需要加以修改。

    ![Verify virtual machines][Verify virtual machines]

您可以在 [工作] 索引標籤中追蹤「啟用保護」動作的進度，包括初始複寫。執行「完成保護」工作之後，虛擬機器即準備好進行容錯移轉。啟用保護並複寫虛擬機器之後，您將能夠在 Azure 中檢視這些虛擬機器。

![虛擬機器保護工作][虛擬機器保護工作]

## <span id="test"></span></a>步驟 8：測試部署

若要測試部署，您可以對單一虛擬機器執行測試容錯移轉，或者建立包含多部虛擬機器的復原方案，再對這個方案執行測試容錯移轉。測試容錯移轉會在隔離的網路中模擬您的容錯移轉與復原機制。請注意：

-   如果您在容錯移轉之後要使用遠端桌面連接到 Azure 中的虛擬機器，請先在虛擬機器上啟用遠端桌面連線，再執行測試容錯移轉。
-   容錯移轉之後，您將透過遠端桌面使用公用 IP 位址連接到 Azure 中的虛擬機器。如果想要這樣做，請確定沒有任何網域原則禁止您使用公用位址連接到虛擬機器。

-   如需建立復原方案的指示，請參閱[建立及自訂復原計劃：內部部署至 Azure][建立及自訂復原計劃：內部部署至 Azure]。
-   如需執行測試容錯移轉的指示，請參閱[測試從內部部署到 Azure 的部署][測試從內部部署到 Azure 的部署]。

</p>
### <span id="runtest"></span></a>監視活動

您可以使用 [工作]**** 索引標籤和 [儀表板]**** 檢視及監視 Azure Site Recovery 保存庫執行的主要工作，包括設定雲端的保護、啟用和停用虛擬機器的保護、執行容錯移轉 (計劃性、非計劃性或測試)，以及認可非計劃性容錯移轉。

您可以從 [工作]**** 索引標籤檢視工作、向下鑽研工作詳細資料和錯誤、執行工作查詢以擷取符合特定條件的工作、將工作匯出至 Excel，以及重新啟動失敗的工作。

您可以從 [儀表板]**** 下載最新版的提供者和代理程式安裝檔案、取得保存庫的組態資訊、查看由保存庫管理其保護的虛擬機器數目、查看最近的工作、管理保存庫憑證，以及重新同步虛擬機器。

如需與工作和儀表板互動的詳細資訊，請參閱[作業和監視指南][作業和監視指南]。

## <span id="next"></span></a>後續步驟

-   若要在完全生產環境中規劃及部署 Azure Site Recovery，請參閱 [Azure Site Recovery 的規劃指南][規劃 Azure 站台復原部署]及 [Azure Site Recovery 的部署指南][Azure Site Recovery 的部署指南]。
-   若有任何問題，請造訪 [Azure 復原服務論壇][Azure 復原服務論壇] (英文)。

</div>

  [開始使用 Azure Site Recovery：內部部署對內部部署保護]: http://go.microsoft.com/fwlink/?LinkId=398765
  [規劃 Azure 站台復原部署]: http://go.microsoft.com/fwlink/?LinkId=321294
  [部署 Azure Site Recovery：內部部署至 Azure 保護]: http://go.microsoft.com/fwlink/?LinkId=402679
  [Azure Site Recovery：常見錯誤案例和解決方法]: http://go.microsoft.com/fwlink/?LinkId=389879
  [Azure 復原服務論壇]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Azure 免費試用]: http://aka.ms/try-azure
  [Azure Site Recovery 管理員定價詳細資料]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Microsoft Azure 儲存體簡介]: http://go.microsoft.com/fwlink/?LinkId=398704
  [必要條件和支援]: http://go.microsoft.com/fwlink/?LinkId=402602
  [步驟 1：建立保存庫]: #vault
  [步驟 2：安裝提供者應用程式]: #download
  [步驟 3：新增 Azure 儲存體帳戶]: #storage
  [步驟 4：安裝代理程式應用程式]: #agent
  [步驟 5：設定雲端保護]: #clouds
  [步驟 6：設定網路對應]: #NetworkMapping
  [步驟 7：對虛擬機器啟用保護]: #virtualmachines
  [步驟 8：測試部署]: #test
  [管理入口網站]: https://manage.windowsazure.com
  [新增保存庫]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [快速啟動圖示]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [註冊金鑰]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png
  [必要條件]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Microsoft Update]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [網際網路設定]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [伺服器註冊]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [儲存體帳戶]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png
  [Install Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png
  [Prerequisites Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
  [發佈的雲端]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Cloud replication settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png
  [準備網路對應]: http://go.microsoft.com/fwlink/?LinkId=324817
  [設定網路對應]: http://go.microsoft.com/fwlink/?LinkId=402533
  [Create virtual machine]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png
  [Modify virtual machine properties]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png
  [啟用虛擬機器保護]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png
  [Verify virtual machines]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png
  [虛擬機器保護工作]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [建立及自訂復原計劃：內部部署至 Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [測試從內部部署到 Azure 的部署]: http://go.microsoft.com/fwlink/?LinkId=511494
  [作業和監視指南]: http://go.microsoft.com/fwlink/?LinkId=398534
  [Azure Site Recovery 的部署指南]: http://go.microsoft.com/fwlink/?LinkId=321295
