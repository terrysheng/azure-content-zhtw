<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="開始使用 Azure Site Recovery：內部部署對內部部署保護" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure 站台復原可協調將位於內部部署 VMM 雲端中的 Hyper-V 虛擬機器複寫、容錯移轉及復原至另一個內部部署站台的作業。" metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="開始使用 Azure Site Recovery：內部部署對內部部署保護" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# 開始使用 Azure Site Recovery：內部部署對內部部署保護

<div class="dev-callout">

使用 Azure Site Recovery 可協調對 VMM 雲端中內部部署 Hyper-V 主機伺服器上之虛擬機器的保護。您可以設定：

-   **內部部署對內部部署保護**：將內部部署虛擬機器複寫至另一個內部部署網站。您可以在 Azure 站台復原保存庫中設定和啟用保護設定。虛擬機器資料會從一部內部部署 Hyper-V 伺服器複寫至另一部伺服器。 如需這個案例的詳細資訊，請參閱[開始使用 Azure Site Recovery：內部部署對內部部署保護][開始使用 Azure Site Recovery：內部部署對內部部署保護]。
-   **內部部署對 Azure 保護**：將內部部署虛擬機器複寫至 Azure。您可以在 Azure 站台復原保存庫中設定和啟用保護設定。虛擬機器資料會從一部內部部署 Hyper-V 伺服器複寫至 Azure 儲存體。如需這個案例的詳細資訊，請參閱[開始使用 Azure Site Recovery：內部部署對 Azure 保護][開始使用 Azure Site Recovery：內部部署對 Azure 保護]。

## <span id="about"></span></a>關於本教學課程

您可以使用這個教學課程，在內部部署 Azure 部署中設定 Azure Site Recovery 的快速概念證明。該教學課程盡可能使用最快的路徑和預設設定。您將建立 Azure Site Recovery 保存庫、在來源 VMM 伺服器中安裝 Azure Site Recovery 提供者、設定雲端保護設定、啟用虛擬機器的保護，以及測試您的部署。

如果您需要完整部署的相關資訊，請參閱：

-   [規劃 Azure 站台復原部署][規劃 Azure 站台復原部署] - 描述在開始完整部署之前應該完成的規劃步驟。
-   [部署 Azure Site Recovery：內部部署至內部部署保護][部署 Azure Site Recovery：內部部署至內部部署保護] - 提供完整部署的逐步指示。

如果您在進行本教學課程期間發生問題，請檢閱 Wiki 文章 [Azure Site Recovery：常見錯誤案例和解決方法][Azure Site Recovery：常見錯誤案例和解決方法] (英文)，或在 [Azure 復原服務論壇][Azure 復原服務論壇]提出您的問題。

</div>

## <span id="before"></span></a>開始之前

<div class="dev-callout">

開始本教學課程之前，請確認已達到先決條件。

### <span id="HVRMPrereq"></span></a>必要條件

-   **Azure 帳戶**：您將需要一個 Azure 帳戶。如果您沒有帳戶，請參閱 [Azure 免費試用][Azure 免費試用]。您可以從 [Azure Site Recovery 管理員定價詳細資料][Azure Site Recovery 管理員定價詳細資料] (英文) 取得定價資訊。
-   **VMM 伺服器**：您至少需要一部在 System Center 2012 SP1 或 System Center 2012 R2 上執行的 VMM 伺服器。
-   **VMM 雲端**：您至少應該在要保護的來源 VMM 伺服器上具有一個雲端，並在目標 VMM 伺服器上具有一個雲端。如果您執行一部 VMM 伺服器，則需要兩個雲端。要保護的主要雲端必須包含下列項目：
    -   一或多個 VMM 主機群組
    -   每個主機群組中的一或多個 Hyper-V 主機伺服器或叢集。
    -   位於雲端中來源 Hyper-V 伺服器上的一或多部虛擬機器。

## <span id="tutorial"></span></a>教學課程步驟

確認已達成先決條件之後，請執行下列動作：

-   [步驟 1：建立保存庫][步驟 1：建立保存庫] - 建立 Azure 站台復原保存庫。
-   [步驟 2：安裝提供者應用程式][步驟 2：安裝提供者應用程式]—產生註冊金鑰，然後在 VMM 伺服器上執行 Microsoft Azure 站台復原提供者應用程式。這會安裝提供者並在保存庫中註冊 VMM 伺服器。
-   [步驟 3：設定雲端保護][步驟 3：設定雲端保護] - 為 VMM 雲端進行保護設定。
-   <a href="#networkmapping">步驟 5：設定網路對應：您可以選擇性地設定網路對應，將來源 VM 網路對應至目標 VM 網路。
-   [步驟 6：設定儲存體對應][步驟 6：設定儲存體對應] - 您可以選擇性地設定儲存體對應，以將來源 VMM 伺服器上的儲存體分類對應至目標伺服器上的儲存體分類。
-   [步驟 7：對虛擬機器啟用保護][步驟 7：對虛擬機器啟用保護] - 對受保護 VMM 雲端中的虛擬機器啟用保護
-   [步驟 8：設定並執行復原計畫][步驟 8：設定並執行復原計畫] - 建立復原計畫並執行計劃的測試容錯移轉。

<a name="vault"></a>

## 步驟 1：建立保存庫

</p>
1.  登入[管理入口網站][管理入口網站]。

2.  依序展開 [資料服務]**** 和 [復原服務]****，然後按一下 [Site Recovery 保存庫]****。

3.  按一下 [建立新項目]****，然後按一下 [快速建立]****。

4.  在 [名稱]**** 中，輸入保存庫的易記識別名稱。

5.  在 [地區]**** 中，選取保存庫的地理區域。若要查看支援的地區，請參閱 [Azure Site Recovery 定價詳細資料][Azure Site Recovery 定價詳細資料] (英文) 中的＜各地區上市情況＞。

6.  按一下 [建立保存庫]****。

    ![新增保存庫][新增保存庫]

檢查狀態列，以確認是否順利建立保存庫。保存庫在主要復原服務頁面上會列為 [使用中]****。

<a name="upload"></a>

## 步驟 2：設定保存庫

</p>
1.  在 [復原服務]**** 頁面中，按一下保存庫以開啟 [快速啟動] 頁面。您也可以使用圖示隨時開啟 [快速啟動]。

    ![快速啟動圖示][快速啟動圖示]

2.  在下拉式清單中，選取 [在兩個內部部署 Hyper-V 網站之間]。
3.  在 [準備 VMM 伺服器] 中，按一下 [產生註冊金鑰檔案]。該金鑰在產生後會維持 5 天有效。將檔案複製到 VMM 伺服器。設定提供者時需要這個檔案。

    ![註冊金鑰][註冊金鑰]

<a name="download"></a>

## 步驟 3：安裝 Azure Site Recovery 提供者

</p>
1.  在 [快速啟動]**** 頁面上，按一下 [準備 VMM 伺服器] 中的 [下載要在 VMM 伺服器上安裝的 Microsoft Azure Site Recovery 提供者]****，以取得最新版的提供者安裝檔案。

2.  在來源和目標 VMM 伺服器上執行這個檔案。

3.  在 [必要條件檢查] 中，選取停止 VMM 服務以開始執行提供者安裝程式。服務隨即停止，並將在安裝程式完成時自動重新啟動。

    ![必要條件][必要條件]

4.  在 [Microsoft Update] 中，您可以選擇加入以取得更新。啟用這項設定時，會根據您的 Microsoft Update 原則來安裝提供者更新。

    ![Microsoft Update][Microsoft Update]

安裝提供者之後，請繼續安裝以在保存庫中註冊伺服器。

1.  在 [網際網路連線] 頁面上，指定在 VMM 伺服器上執行的提供者連線至網際網路的方式。請選取 [使用預設系統 Proxy 設定]**** 使用在伺服器上設定的預設網際網路連線設定。

    ![網際網路設定][網際網路設定]

2.  在 [註冊金鑰] 中，選取您從 Azure Site Recovery 下載並複製到 VMM 伺服器的金鑰。
3.  在 [保存庫名稱] 中，確認要註冊伺服器的保存庫名稱。
4.  在 [伺服器名稱] 中，指定保存庫中 VMM 伺服器的易記識別名稱。

    ![伺服器註冊][伺服器註冊]

5.  在 [初始雲端中繼資料同步] 中，選取您是否要將 VMM 伺服器上所有雲端的中繼資料與保存庫同步。這個動作只需要在每個伺服器上進行一次。如果不要同步所有雲端，您可以取消核取這項設定，再於 VMM 主控台的雲端屬性中個別同步每個雲端。

6.  在 [資料加密] 中，您可以產生用於加密受保護 Azure 資料的憑證。
    如果從一個內部部署網站複寫至另一個內部部署網站，則與這個選項無關。

    ![伺服器註冊][1]

7.  按一下 [註冊]**** 完成程序。註冊後，Azure Site Recovery 即可從 VMM 伺服器擷取中繼資料。伺服器會顯示在保存庫中 [伺服器] 頁面上 [資源]**** 索引標籤的結尾。

## <span id="clouds"></span></a>步驟 4：設定雲端保護設定

註冊 VMM 伺服器之後，您就可以設定雲端保護設定。當您安裝提供者之後，即可啟用 [將雲端資料與保存庫同步] 選項，如此一來，VMM 伺服器上的所有雲端將會出現在保存庫中的 [受保護的項目]**** 索引標籤中。

![發佈的雲端][發佈的雲端]

1.  在 [快速啟動] 頁面上，按一下 [設定 VMM 雲端的保護]。
2.  在 [受保護項目] 索引標籤上，選取要設定的雲端，並移至 [設定] 索引標籤。請注意：
3.  在 [目標]**** 中，選取 [VMM]****。
4.  在 [目標位置]**** 中，選取網站上負責對您要用於復原之雲端進行管理的 VMM 伺服器。
5.  在 [目標雲端]**** 中，選取要作為來源雲端中虛擬機器容錯移轉目標的雲端。請注意：

    -   建議您，選取的目標雲端要符合所要保護之虛擬機器的復原需求。
    -   雲端只能當成單一雲端組中的主要雲端或目標雲端。

6.  在 [複製頻率]**** 中保留預設設定。這個值指定應在來源與目標位置之間同步處理資料的頻率。這只有在 Hyper-V 主機是執行 Windows Server 2012 R2 時才有重要性。對於其他伺服器，使用預設設定的 5 分鐘即可。
7.  在 [額外的復原點]**** 中保留預設設定。這個值指定是否要建立其他復原點。預設值 0 指定只會在複本主機伺服器上儲存主要虛擬機器的最新復原點。
8.  在 [應用程式一致快照的頻率]**** 中保留預設設定。這個值指定建立快照的頻率。快照會使用「磁碟區陰影複製服務」(VSS) 來確保建立快照時，應用程式是處於一致狀態。若要針對本教學課程的逐步解說設定這個值，請務必將它設定為小於您設定之額外復原點的數目。
    ![進行保護設定][進行保護設定]
9.  在 [資料傳輸壓縮]**** 中，指定是否應該將傳輸的複寫資料壓縮。
10. 在 [驗證]**** 中，指定如何驗證主要與復原 Hyper-V 主機伺服器之間的流量。除非您已設定可用的 Kerberos 環境，否則建議您針對這個逐步解說選取 HTTPS。Azure Site Recovery 會自動設定用於 HTTPS 驗證的憑證。不需要進行任何手動設定。請注意，這項設定只有在 Hyper-V 主機伺服器是執行於 Windows Server 2012 R2 時才有重要性。
11. 在 [連接埠]**** 中保留預設設定。這個值會設定來源與目標 Hyper-V 主機電腦接聽複寫流量的連接埠號碼。
12. 在 [複寫方法]**** 中，指定在正常複寫開始前，初次將資料從來源複寫到目標位置時的處理方式。

    -   **透過網路**：透過網路複製資料可能既費時又耗資源。如果雲端中的虛擬機器使用相當小的虛擬硬碟，而且主要 VMM 伺服器透過寬頻連線來連接次要 VMM 伺服器，則建議您使用這個選項。您可以指定立即開始複製，或是選取開始複製的時間。如果您使用網路複寫，建議您排在離峰時段進行。
    -   **離線**：這個方法指定要使用外部媒體執行初次複寫。如果您想要避免影響網路效能，或者複寫目的地位於很遙遠的地方，就適合使用這個方法。若要使用這個方法，您需指定來源雲端上的匯出位置，以及目標雲端上的匯入位置。對虛擬機器啟用保護時，虛擬硬碟會複製到指定的匯出位置。您需將它送到目標站台，再將它複製到匯入位置。系統會將匯入的資訊複製到複本虛擬機器。如需離線複寫先決條件的完整清單，請參閱部署指南的[步驟 3：設定 VMM 雲端的保護設定][步驟 3：設定 VMM 雲端的保護設定]。

13. 選取 [刪除複本虛擬機器] 指定在選取雲端屬性之 [虛擬機器] 索引標籤上的 [刪除虛擬機器的保護] 選項，以停止保護虛擬機器時，應該刪除複本虛擬機器。如果啟用這項設定，當您停用保護時，即會從 Azure Site Recovery 中移除虛擬機器、在 VMM 主控台中移除虛擬機器的 Site Recovery 設定，並刪除複本。
    ![進行保護設定][2]

儲存設定之後，會建立一個工作，並可在 [工作]**** 索引標籤上進行監視。VMM 來源雲端中的所有 Hyper-V 主機伺服器會設定進行複寫。在 [設定]**** 索引標籤上可修改雲端設定。如果您要修改目標位置或目標雲端，則必須移除雲端組態，然後重新設定雲端。

## <span id="networkmapping"></span></a>步驟 5：設定網路對應

本教學課程說明在測試環境中部署 Azure Site Recovery 的最簡單路徑。如果您不想在進行本教學課程時設定網路對應，請閱讀《規劃指南》中的[準備網路對應][準備網路對應]。若要設定對應，請遵循《部署指南》之[設定網路對應][設定網路對應]中的步驟進行。

## <span id="storagemapping"></span></a>步驟 6：設定儲存體對應

本教學課程說明在測試環境中部署 Azure Site Recovery 的最簡單路徑。如果您不想在進行本教學課程時設定儲存體對應，請遵循《部署指南》之[設定儲存體對應][設定儲存體對應]中的步驟進行。

## <span id="enablevirtual"></span></a>步驟 7：啟用虛擬機器保護

正確設定伺服器、雲端和網路後，您就可以對雲端中的虛擬機器啟用保護。

1.  在虛擬機器所在的雲端中，按一下 [虛擬機器]**** 索引標籤上的 [啟用保護]****，然後選取 [加入虛擬機器]****。
2.  從雲端中所有虛擬機器的清單，選取您要保護的虛擬機器。

![啟用虛擬機器保護][啟用虛擬機器保護]

您可以在 [工作] 索引標籤中追蹤「啟用保護」動作的進度，包括初始複寫。執行「完成保護」工作之後，虛擬機器即準備好進行容錯移轉。啟用保護並複寫虛擬機器之後，您將能夠在 Azure 中檢視這些虛擬機器。

![虛擬機器保護工作][虛擬機器保護工作]

## <span id="recoveryplans"></span></a>步驟 8：測試部署

若要測試部署，您可以對單一虛擬機器執行測試容錯移轉，或者建立包含多部虛擬機器的復原方案，再對這個方案執行測試容錯移轉。測試容錯移轉會在隔離的網路中模擬您的容錯移轉與復原機制。

-   如需建立復原方案的指示，請參閱[建立及自訂復原計劃：內部部署至 Azure][建立及自訂復原計劃：內部部署至 Azure]。
-   如需執行測試容錯移轉的指示，請參閱[測試內部部署對內部部署的部署][測試內部部署對內部部署的部署]。

### <span id="runtest"></span></a>監視活動

您可以使用 [工作]**** 索引標籤和 [儀表板]**** 檢視及監視 Azure Site Recovery 保存庫執行的主要工作，包括設定雲端的保護、啟用和停用虛擬機器的保護、執行容錯移轉 (計劃性、非計劃性或測試)，以及認可非計劃性容錯移轉。

您可以從 [工作]**** 索引標籤檢視工作、向下鑽研工作詳細資料和錯誤、執行工作查詢以擷取符合特定條件的工作、將工作匯出至 Excel，以及重新啟動失敗的工作。

您可以從 [儀表板]**** 下載最新版的提供者和代理程式安裝檔案、取得保存庫的組態資訊、查看由保存庫管理其保護的虛擬機器數目、查看最近的工作、管理保存庫憑證，以及重新同步虛擬機器。

如需與工作和儀表板互動的詳細資訊，請參閱[作業和監視指南][作業和監視指南]。

## <span id="next"></span></a>後續步驟

-   若要在完全生產環境中規劃及部署 Azure Site Recovery，請參閱 [Azure Site Recovery 的規劃指南][規劃 Azure 站台復原部署]及 [Azure Site Recovery 的部署指南][部署 Azure Site Recovery：內部部署至內部部署保護]。
-   若有任何問題，請造訪 [Azure 復原服務論壇][Azure 復原服務論壇] (英文)。

</div>

  [開始使用 Azure Site Recovery：內部部署對內部部署保護]: http://go.microsoft.com/fwlink/?LinkId=398765
  [開始使用 Azure Site Recovery：內部部署對 Azure 保護]: http://go.microsoft.com/fwlink/?LinkId=398764
  [規劃 Azure 站台復原部署]: http://go.microsoft.com/fwlink/?LinkId=321294
  [部署 Azure Site Recovery：內部部署至內部部署保護]: http://go.microsoft.com/fwlink/?LinkId=321295
  [Azure Site Recovery：常見錯誤案例和解決方法]: http://go.microsoft.com/fwlink/?LinkId=389879
  [Azure 復原服務論壇]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Azure 免費試用]: http://aka.ms/try-azure
  [Azure Site Recovery 管理員定價詳細資料]: http://go.microsoft.com/fwlink/?LinkId=378268
  [步驟 1：建立保存庫]: #vault
  [步驟 2：安裝提供者應用程式]: #download
  [步驟 3：設定雲端保護]: #clouds
  [步驟 6：設定儲存體對應]: #storagemapping
  [步驟 7：對虛擬機器啟用保護]: #enablevirtual
  [步驟 8：設定並執行復原計畫]: #recovery%20plans
  [管理入口網站]: https://manage.windowsazure.com
  [Azure Site Recovery 定價詳細資料]: http://go.microsoft.com/fwlink/?LinkId=389880
  [新增保存庫]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [快速啟動圖示]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [註冊金鑰]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png
  [必要條件]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Microsoft Update]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [網際網路設定]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [伺服器註冊]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [發佈的雲端]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [進行保護設定]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png
  [步驟 3：設定 VMM 雲端的保護設定]: http://go.microsoft.com/fwlink/?LinkId=323469
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png
  [準備網路對應]: http://go.microsoft.com/fwlink/?LinkId=324817
  [設定網路對應]: http://go.microsoft.com/fwlink/?LinkId=402534
  [設定儲存體對應]: http://go.microsoft.com/fwlink/?LinkId=402535
  [啟用虛擬機器保護]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [虛擬機器保護工作]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [建立及自訂復原計劃：內部部署至 Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [測試內部部署對內部部署的部署]: http://go.microsoft.com/fwlink/?LinkId=511493
  [作業和監視指南]: http://go.microsoft.com/fwlink/?LinkId=398534
