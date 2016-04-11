<properties
	pageTitle="使用 SAN 搭配 Azure Site Recovery 將 Hyper-V VM (位於 VMM 雲端中) 複寫至次要站台 | Microsoft Azure"
	description="本文說明如何使用 SAN 複寫，搭配 Azure Site Recovery 在兩個網站之間複寫 Hyper-V 虛擬機器。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="raynew"/>

# 使用 SAN 搭配 Azure Site Recovery 將 Hyper-V VM (位於 VMM 雲端中) 複寫至次要網站

在本文中，您將學習如何部署 Site Recovery 來協調及自動化 Hyper-V 虛擬機器 (位於 System Center VMM 雲端中) 的 SAN 複寫及容錯移轉 (至次要 VMM 網站)。

在閱讀本文之後，請在本文下方或 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 張貼任何意見或問題。


## 概觀

組織需要商務持續性和災害復原 (BCDR) 策略，決定應用程式、工作負載和資料如何在規劃與未規劃停機期間維持運作，並儘速復原到正常運作的情況。BCDR 策略的解決方案重點集中在保護商務資料安全且可復原，以及當發生災害時工作負載持續可用。

Site Recovery 是一項 Azure 服務，可藉由將內部部署實體伺服器和虛擬機器的複寫協調至雲端 (Azure) 或次要資料中心，協助您的 BCDR 策略。當您的主要位置發生故障時，您容錯移轉至次要網站，讓應用程式和工作負載保持可用。當它恢復正常作業時，容錯回復至您的主要位置。Site Recovery 可以用在許多案例中，並可保護許多工作負載。深入了解[什麼是 Azure Site Recovery？](site-recovery-overview.md)

本文包含如何設定使用 SAN 複寫將 Hyper-V VM 從某個 VMM 網站複寫至另一個 VMM 網站的指示。其中包含架構概觀，以及部署必要條件和指示。您將探索 VMM 中的 SAN 存放裝置並加以分類、佈建 LUN，以及將存放裝置配置給 Hyper-V 叢集。最後它會測試容錯移轉，藉此確定一切如預期般運作。


## 為什麼使用 SAN 複寫？

以下是此案例中所提供的項目：

- 提供 Site Recovery 自動化的企業可調整複寫解決方案。
- 利用企業儲存體合作夥伴在光纖通道及 iSCSI 存放裝置之間提供的 SAN 複寫功能。請參閱我們 [SAN 儲存體合作夥伴](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)。
- 運用現有的 SAN 基礎結構保護在 Hyper-V 叢集中部署的關鍵任務應用程式。
- 為客體叢集提供支援。
- 藉由低 RTO 與 RPO 的同步複寫，以及高彈性的非同步複寫 (視存放裝置陣列功能而定) 確保不同層應用程式的複寫一致性。  
- 與 VMM 整合時，可在 VMM 主控台中提供 SAN 管理功能，且 VMM 中的 SMI-S 會探索現有的存放裝置。  

## 架構

此案例可透過使用 SAN 複寫，將 Hyper-V 虛擬機器從一個內部部署 VMM 站台備份至另一個內部部署 VMM 站台來保護您的工作負載。

![SAN 架構](./media/site-recovery-vmm-san/architecture.png)

此案例中的元件包括：

- **內部部署虛擬機器** — 在 VMM 私人雲端中管理的內部部署 Hyper-V 伺服器包含您想要保護的虛擬機器。
- **內部部署 VMM 伺服器** — 您可以擁有一個或多個在您想要保護的主要站台和次要站台上執行的 VMM 伺服器。
- **SAN 儲存體** — 主要站台和次要站台上各一個 SAN 陣列
-  **Azure Site Recovery 保存庫** — 此保存庫可協調內部部署站台之間的資料複寫、容錯移轉及復原等作業。
- **Azure Site Recovery 提供者** — 提供者安裝在每部 VMM 伺服器上。

## 開始之前

確認您已備妥這些必要條件：

**必要條件** | **詳細資料** 
--- | ---
**Azure**| 您將需要 [Microsoft Azure](https://azure.microsoft.com/) 帳戶。您可以從[免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。[深入了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 價格。 
**VMM** | 您需要至少一部部署為實體或虛擬獨立伺服器，或是部署為虛擬叢集的 VMM 伺服器。<br/><br/>VMM 伺服器應執行含有最新累積更新的 System Center 2012 R2。<br/><br/>您需要在您想要保護的主要 VMM 伺服器上至少設定一個雲端，以及在您要用於保護及復原的次要 VMM 伺服器上設定一個雲端<br/><br/>您要保護的來源雲端必須包含一或多個 VMM 主機群組。<br/><br/>所有的 VMM 雲端都必須有 Hyper-V 容量設定檔集合。<br/><br/>在 [Configuring the VMM cloud fabric (設定 VMM 雲端網狀架構)](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) 和 [Walkthrough: Creating private clouds with System Center 2012 SP1 VMM (逐步解說：使用 System Center 2012 SP1 VMM 建立私人雲端)](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) 深入了解如何設定 VMM 雲端。
**Hyper-V** | 您將需要在主要站台和次要站台中具備一或多個 Hyper-V 叢集，以及在來源 Hyper-V 叢集上具備一或多個 VM。位於主要位置和次要位置的每個 VMM 主機群組，都應具備一或多個 Hyper-V 叢集。<br/><br/>主機和目標 Hyper-V 伺服器必須執行具有 Hyper-V 角色的最新 Windows Server 2012，並安裝最新的更新。<br/><br/>任何含有您要保護之 VM 的 Hyper-V 伺服器都必須位於 VMM 雲端。<br/><br/>如果您是在叢集中執行 Hyper-V，請注意，當您擁有靜態 IP 位址叢集時，並不會自動建立叢集訊息代理程式。您必須手動設定叢集代理。在 Aidan Finn 的部落格項目中[深入了解](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters)。
**SAN 儲存體** | 您可以使用 SAN 複寫，藉由 iSCSI 或光纖通道存放裝置，或藉由使用共用虛擬硬碟 (vhdx)，複寫客體叢集化虛擬機器。<br/><br/>您將需要設定兩個 SAN 陣列，一個在主要站台中，一個在次要站台中。<br/><br/>應該在陣列之間設定網路基礎結構。應該設定對等互連和複寫。應該根據存放裝置陣列需求設定複寫授權。<br/><br/>應該在 Hyper-V 主機伺服器與存放裝置陣列之間設定網路功能，讓主機可以使用 ISCSI 或光纖通道與存放裝置 LUN 進行通訊。<br/><br/> 檢查[支援的儲存體陣列](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)清單。<br/><br/>應該安裝存放裝置陣列製造商所提供的 SMI-S 提供者，並且應該由此提供者管理 SAN 陣列。根據提供者的文件設定提供者。<br/><br/>確定陣列的 SMI-S 提供者所在的伺服器，是 VMM 伺服器可透過網路以 IP 位址或 FQDN 存取的伺服器。<br/><br/>每個 SAN 陣列都應該要有一或多個儲存集區可供在此部署中使用。主要站台上的 VMM 伺服器將需要管理主要陣列，而次要 VMM 伺服器將管理次要陣列。<br/><br/>主要站台上的 VMM 伺服器應該管理主要陣列，而次要 VMM 伺服器應該管理次要陣列。
**網路對應** | 您可以設定網路對應，以確保在容錯移轉後，會以最佳方式將已複寫的虛擬機器置於次要 Hyper-V 主機伺服器上，而且這些虛擬機器可以連線到適當的 VM 網路。如果您未設定網路對應，複本 VM 在容錯移轉後將無法連線到任何網路。<br/><br/>若要在部署期間設定網路對應，請確定來源 Hyper-V 主機伺服器上的虛擬機器已連線到 VMM VM 網路。該網路應該連結到與雲端相關聯的邏輯網路。<br/<br/>在您用於復原的次要 VMM 伺服器上的目標雲端應該設定相對應的 VM 網路，而該網路應該連結到與目標雲端相關聯的相對應邏輯網路。<br/><br/>[深入了解](site-recovery-network-mapping.md)網路對應。


## 步驟 1：準備 VMM 基礎結構

為準備您的 VMM 基礎結構，您需要：

1. 確定已設定 VMM 雲端
2. 將 VMM 中的 SAN 存放裝置整合並分類。
3. 建立 LUN 並配置存放裝置
4. 建立複寫群組
5. 設定 VM 網路

### 確定已設定 VMM 雲端

Site Recovery 可協調對 VMM 雲端中 Hyper-V 主機伺服器上之虛擬機器的保護。您必須確定在開始 Site Recovery 部署之前，已妥善設定這些雲端。在 Keith Mayer 部落格中的[逐步解說：建立私人雲端](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)中深入了解。

### 將 VMM 中的 SAN 存放裝置整合並分類。

在 VMM 主控台中新增 SAN 分類：

1. 在 [網狀架構] 工作區中，按一下 [存放裝置]。按一下 [首頁] > [新增資源] > [存放裝置] 以啟動 [新增存放裝置精靈]。
2. 在 [選取存放裝置提供者類型] 頁面上，選取 [由 SMI-S 提供者探索到及管理的 SAN 和 NAS 裝置]。

	![提供者類型](./media/site-recovery-vmm-san/provider-type.png)

3. 在 [指定儲存 SMI-S 提供者的通訊協定與位址] 頁面上，選取 [SMI-S CIMXML]，並指定用來連線到提供者的設定。
4. 在 [提供者 IP 位址或 FQDN] 和 [TCP/IP 連接埠] 中，指定用來連線到提供者的設定。您只能針對 SMI-S CIMXML 使用 SSL 連線。

	![提供者連接](./media/site-recovery-vmm-san/connect-settings.png)

5. 在 [執行身分帳戶] 中指定一個可以存取提供者的 VMM 執行身分帳戶，或建立一個新帳戶。
6. 在 [收集資訊] 頁面上，VMM 會自動嘗試探索並匯入存放裝置資訊。若要重新嘗試探索，請按一下 [掃描提供者]。如果探索程序成功，頁面上就會列出探索到的存放裝置陣列、存放集區、製造商、型號及容量。

	![探索儲存體](./media/site-recovery-vmm-san/discover.png)

7. 在 [選取要列入管理的存放集區並指派分類] 中，選取 VMM 將管理的存放集區，並為它們指派一個分類。LUN 資訊將會從存放集區匯入。請根據您需要保護的應用程式、其容量需求，以及您對於哪些項目要一起複寫的需求，建立 LUN。

	![分類儲存體](./media/site-recovery-vmm-san/classify.png)

### 建立 LUN 並配置存放裝置

1. 將 SAN 存放裝置整合到 VMM 之後，您將建立 (佈建) 邏輯單元 (LUN)：

	- [如何選取在 VMM 中建立邏輯單元的方法](https://technet.microsoft.com/library/gg610624.aspx)
	- [如何在 VMM 中佈建存放裝置邏輯單元](https://technet.microsoft.com/library/gg696973.aspx)

	>[AZURE.NOTE] 在啟用了機器的複寫之後，您不應該將它的 VHD 新增至不在 Site Recovery 複寫群組的 LUN。如果您這樣做，Site Recovery 將偵測不到它們。

2. 接著，配置儲存容量給 Hyper-V 主機叢集，讓 VMM 可以將虛擬機器資料部署到已佈建的存放裝置上：

	- 將存放裝置配置給叢集之前，您必須先將其配置給叢集所在的 VMM 主機群組。請參閱[如何將存放裝置邏輯單元配置到主機群組](https://technet.microsoft.com/library/gg610686.aspx)及[如何將存放集區配置到主機群組](https://technet.microsoft.com/library/gg610635.aspx)。</a>
	- 接著，依照[如何在 VMM 中設定 Hyper-V 主機叢集上的存放裝置](https://technet.microsoft.com/library/gg610692.aspx)中所述，將儲存容量配置到叢集。</a>。

### 建立複寫群組

建立複寫群組，其中包含所有需要一起進行複寫的 LUN。

1. 在 VMM 主控台中，開啟存放裝置陣列內容的 [複寫群組] 索引標籤，然後按一下 [新增]。
2. 接著，建立複寫群組。

	![SAN 複寫群組](./media/site-recovery-vmm-san/rep-group.png)

### 設定網路

如果您想要設定網路對應，請執行下列操作：

1. [深入了解](site-recovery-network-mapping.md)網路對應。
2. 在 VMM 中準備 VM 網路：

	- [設定邏輯網路](https://technet.microsoft.com/library/jj721568.aspx)。
	- [設定 VM 網路](https://technet.microsoft.com/library/jj721575.aspx)。

## 步驟 2：建立保存庫


1. 從您想要註冊的 VMM 伺服器登入[管理入口網站](https://portal.azure.com)。

2. 展開 [資料服務] > [復原服務]，然後按一下 [Site Recovery 保存庫]。

3. 按一下 [新建] > [快速建立]。

4. 在 [名稱] 中，輸入保存庫的易記識別名稱。

5. 在 [區域] 中，選取保存庫的地理區域。若要查看支援的區域，請參閱 [Azure Site Recovery 價格詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)中的＜各區域上市情況＞。

6. 按一下 [建立保存庫]。

	![新增保存庫](./media/site-recovery-vmm-san/create-vault.png)

檢查狀態列，以確認是否順利建立保存庫。保存庫在主要 [復原服務] 頁面上會列為 [使用中]。


### 註冊 VMM 伺服器

1. 從 [復原服務] 頁面開啟 [快速啟動] 頁面。您也可以使用圖示隨時開啟 [快速啟動]。

	![快速啟動圖示](./media/site-recovery-vmm-san/quick-start-icon.png)

2. 在下拉式清單中，選取 [在使用陣列複寫的 Hyper-V 內部部署站台之間]。

	![註冊金鑰](./media/site-recovery-vmm-san/select-san.png)


3. 在 [準備 VMM 伺服器] 中，下載最新版本的 Azure Site Recovery 提供者安裝檔案。
4. 在來源 VMM 伺服器上執行此檔案。如果 VMM 部署在叢集中，且您是第一次安裝提供者，請將其安裝在作用中節點上，並完成安裝以在保存庫中註冊 VMM 伺服器。然後在其他節點上安裝提供者。請注意，如果您要升級提供者，您必須在所有節點上升級，因為它們都應該執行相同的提供者版本。
5. 安裝程式會執行一些**先決條件檢查**，並要求停止 VMM 服務的權限，以便開始安裝提供者。當安裝完成之後，VMM 服務將會自動重新啟動。如果您安裝在 VMM 叢集上，您會收到停止叢集角色的提示。
6. 在 [Microsoft Update] 中，您可以選擇加入以取得更新。啟用這項設定時，會根據您的 Microsoft Update 原則來安裝提供者更新。

	![Microsoft Update](./media/site-recovery-vmm-san/ms-update.png)

7. 安裝位置已設為 **<SystemDrive>\\Program Files\\Microsoft System Center 2012 R2\\Virtual Machine Manager\\bin**。按一下 [安裝] 按鈕，開始安裝提供者。

	![InstallLocation](./media/site-recovery-vmm-san/install-location.png)

8. 提供者安裝之後，請按一下 [註冊] 按鈕，在保存庫中註冊伺服器。

	![InstallComplete](./media/site-recovery-vmm-san/install-complete.png)

9. 在 [網際網路連線] 中，指定 VMM 伺服器上執行的提供者連接到網際網路的方式。選取 [Use default system proxy settings]，以使用在伺服器上設定的預設網際網路連線設定。

	![網際網路設定](./media/site-recovery-vmm-san/proxy-details.png)

	- 如果您想要使用自訂 proxy，您應該在安裝提供者之前進行設定。當您進行自訂 proxy 設定時，會執行一項測試以檢查 proxy 連線。
	- 如果您使用自訂 proxy，或者您的預設 proxy 需要驗證，您必須輸入 proxy 詳細資料，包含 proxy 位址和連接埠。
	- 下列 URL 應可從 VMM 伺服器和 Hyper-V 主機存取
		- *.hypervrecoverymanager.windowsazure.com
		- *.accesscontrol.windows.net
		- *.backup.windowsazure.com
		- *.blob.core.windows.net
		- *.store.core.windows.net
	- 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)中所述的 IP 位址和 HTTPS (443) 通訊協定。您必須具有打算使用以及美國西部之 Azure 區域的白名單 IP 範圍。
	- 如果您使用的是自訂 proxy，則會使用指定的 proxy 認證自動建立 VMM RunAs 帳戶 (DRAProxyAccount)。設定 proxy 伺服器，讓此帳戶可以成功進行驗證。在 VMM 主控台中，可以修改 VMM RunAs 帳戶設定。若要這樣做，請開啟 [設定] 工作區、展開 [安全性]、按一下 [執行身分帳戶]，然後修改 DRAProxyAccount 的密碼。您必須重新啟動 VMM 服務，這項設定才會生效。

10. 在 [註冊金鑰] 中，選取您從 Azure Site Recovery 下載並複製到 VMM 伺服器的金鑰。
11. 在 [保存庫名稱] 中，確認要註冊伺服器的保存庫名稱。 

	![伺服器註冊](./media/site-recovery-vmm-san/vault-creds.png)

12. 加密設定只適用於 VMM 到 Azure 案例，如果您只是 VMM 到 VMM 的使用者，則可以忽略這個畫面。

	![伺服器註冊](./media/site-recovery-vmm-san/encrypt.png)

13. 在 [伺服器名稱] 中，指定保存庫中 VMM 伺服器的易記識別名稱。在叢集設定中，指定 VMM 叢集角色名稱。
14. 在 [初始雲端中繼資料同步] 中，指定將顯示於保存庫中的伺服器易記名稱，並選取您是否要將 VMM 伺服器上所有雲端的中繼資料與保存庫同步。這個動作只需要在每個伺服器上進行一次。如果不要同步所有雲端，您可以取消核取這項設定，再於 VMM 主控台的雲端屬性中個別同步每個雲端。

	![伺服器註冊](./media/site-recovery-vmm-san/friendly-name.png)

15. 按 [下一步]，完成此程序。註冊後，Azure Site Recovery 即可從 VMM 伺服器擷取中繼資料。此伺服器會顯示於保存庫中 [伺服器] 頁面的 [VMM 伺服器] 索引標籤上。

### 命令列安裝

您也可以使用下列命令列來安裝 Azure Site Recovery 提供者。這個方法可以用來在適用於 Windows Server 2012 R2 的伺服器核心上安裝提供者。

1. 將提供者安裝檔案和註冊金鑰下載至資料夾，例如 C:\\ASR
2. 停止 System Center Virtual Machine Manager 服務
3. 從命令提示字元，使用**系統管理員**權限執行下列命令，來解壓縮提供者安裝程式：

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. 執行下列命令來安裝提供者：

		C:\ASR> setupdr.exe /i

5. 執行下列命令來註冊提供者：

    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

其中參數為：

 - **/Credentials**：必要參數，用來指定註冊金鑰檔案所在的位置  
 - **/FriendlyName**：對於 Hyper-V 主機伺服器名稱的必要參數，該伺服器會出現在 Azure Site Recovery 入口網站中。
 - **/EncryptionEnabled**：如果您需要在 Azure 中以靜止的方式為虛擬機器加密，則必須只能在 VMM 到 Azure 案例中使用這個選用參數。請確定您提供的檔案名稱具有 **.pfx** 副檔名。
 - **/proxyAddress**：指定 Proxy 伺服器位址的選用參數。
 - **/proxyport**：指定 Proxy 伺服器連接埠的選用參數。
 - **/proxyUsername**：指定 Proxy 使用者名稱 (如果 Proxy 需要驗證) 的選用參數。
 - **/proxyPassword**：指定用以驗證 Proxy 伺服器之密碼 (如果 Proxy 需要驗證) 的選用參數。


## 步驟 3：對應存放裝置陣列和集區

對應陣列，以指定哪一個次要存放集區要從主要集區接收複寫資料。您應該在設定雲端保護之前先對應存放裝置，因為在您為複寫群組啟用保護時，會使用此對應資訊。

在開始之前，請先確定雲端有出現在保存庫中。偵測雲端的方式有兩種：在安裝提供者時選擇同步所有雲端；或在 VMM 主控台中雲端內容的 [一般] 索引標籤上，選擇同步某個特定的雲端。然後，依下列方式對應存放裝置陣列：

1. 按一下 [資源] > [伺服器存放裝置] > [對應來源和目標陣列]。![伺服器註冊](./media/site-recovery-vmm-san/storage-map.png)
2. 選取主要站台上的存放裝置陣列，然後將它們對應至次要站台上的存放裝置陣列。
3.  對應陣列內的來源和目標存放集區。若要這樣做，請在 [存放集區] 中，選取要對應的來源和目標存放集區。

	![伺服器註冊](./media/site-recovery-vmm-san/storage-map-pool.png)

## 步驟 4：設定雲端保護設定

註冊 VMM 伺服器之後，您就可以設定雲端保護設定。當您安裝提供者之後，即可啟用 [將雲端資料與保存庫同步] 選項，如此一來，VMM 伺服器上的所有雲端將會出現在保存庫的 [受保護的項目]<b></b> 索引標籤中。

![發佈的雲端](./media/site-recovery-vmm-san/clouds-list.png)

1. 在 [快速啟動] 頁面上，按一下 [設定 VMM 雲端的保護]。
2. 在 [受保護項目] 索引標籤上，選取要設定的雲端，並移至 [設定] 索引標籤。請注意：
3. 在 [目標] 中，選取 [VMM]。
4. 在 [目標位置] 中，選取網站上負責對您要用於復原之雲端進行管理的 VMM 伺服器。
5. 在 [目標雲端] 中，選取要作為來源雲端中虛擬機器容錯移轉目標的雲端。請注意：
	- 建議您，選取的目標雲端要符合所要保護之虛擬機器的復原需求。
	- 雲端只能當成單一雲端組中的主要雲端或目標雲端。
6. Azure Site Recovery 會確認雲端能夠存取具備 SAN 複寫功能的存放裝置，並確認存放裝置陣列已對等互連。將會顯示參與的陣列對等項。
7. 如果驗證成功，請在 [複寫類型] 中，選取 [SAN]。

儲存設定之後，會建立一個工作，並可在 [工作] 索引標籤上進行監視。在 [設定] 索引標籤上可修改雲端設定。如果您要修改目標位置或目標雲端，則必須移除雲端組態，然後重新設定雲端。

## 步驟 5：啟用網路對應

1. 在 [快速入門] 頁面上，按一下 [對應網路]。
2. 選取您想要從其中對應網路的來源 VMM 伺服器，然後選取對應網路的目標 VMM 伺服器。隨即會顯示來源網路的清單和與其相關聯的目標網路。目前尚未對應的網路會顯示空白值。按一下來源和目標網路名稱旁邊的資訊圖示，以檢視每個網路的子網路。
3. 在 [來源上的網路] 中選取網路，然後按一下 [對應]。服務會偵測目標伺服器上的 VM 網路並加以顯示。

	![SAN 架構](./media/site-recovery-vmm-san/network-map1.png)

4. 在顯示的對話方塊中，從目標 VMM 伺服器選取其中一個 VM 網路。

	![SAN 架構](./media/site-recovery-vmm-san/network-map2.png)

5. 當您選曲目標網路時，隨即會顯示使用來源網路的受保護雲端。同時也會顯示與用於保護之雲端相關聯的可用目標網路。建議您選取所有用於保護之雲端都可用的目標網路。
6.  按一下打勾記號完成對應程序。隨即有個工作啟動來追蹤對應程序。您可以在 [工作] 索引標籤上檢視它。


## 步驟 6：為複寫群組啟用複寫

您必須先為存放裝置複寫群組啟用複寫，才能為虛擬機器啟用保護。

1. 在 Azure Site Recovery 入口網站之主要雲端的內容頁面中，開啟 [虛擬機器] 索引標籤。按一下 [新增複寫群組]。
2. 選取一個或多個與雲端關聯的 VMM 複寫群組、確認來源和目標陣列，然後指定複寫頻率。

當此操作完成時，Azure Site Recovery 會與 VMM 和 SMI-S 提供者一起佈建目標站台存放裝置 LUN，並啟用存放裝置複寫。如果複寫群組已經被複寫，Azure Site Recovery 便會重複使用現有的複寫關係，然後更新 Azure Site Recovery 中的資訊。

## 步驟 7：對虛擬機器啟用保護

在存放裝置群組正在進行複寫之後，請在 VMM 主控台中，使用下列其中一個方法為虛擬機器啟用保護：

- **新的虛擬機器** — 在 VMM 主控台中，當您建立新的虛擬機器時，請啟用 Azure Site Recovery 保護，並讓該虛擬機器與複寫群組建立關聯。使用此選項時，VMM 會使用智慧型放置，以最佳方式將虛擬機器存放裝置放置在複寫群組的 LUN 上。Azure Site Recovery 會在次要網站上協調建立影子虛擬機器並配置容量，以便能夠在容錯移轉後啟動複本虛擬機器。
- **現有的虛擬機器** — 如果 VMM 中已經部署虛擬機器，您便可以啟用 Azure Site Recovery 保護，並將存放裝置移轉至複寫群組。完成之後，VMM 和 Azure Site Recovery 會偵測新的虛擬機器，並開始在 Azure Site Recovery 中管理它以進行保護。建立影子虛擬機器時，會在次要站台上建立並配置容量，以便能夠在容錯移轉後啟動複本虛擬機器。

	![啟用保護。](./media/site-recovery-vmm-san/enable-protect.png)

為虛擬機器啟用保護之後，它們就會出現在 Azure Site Recovery 主控台中。您可以檢視虛擬機器內容、追蹤狀態，以及讓包含多個虛擬機器的複寫群組進行容錯移轉。請注意，在 SAN 複寫中，與複寫群組關聯的所有虛擬機器必須都一起進行容錯移轉。這是因為容錯移轉會先發生在儲存層。請務必正確地組成您的複寫群組，只將相關聯的虛擬機器放在一起。

>[AZURE.NOTE] 在啟用了機器的複寫之後，您不應該將它的 VHD 新增至不在 Site Recovery 複寫群組的 LUN。如果您這樣做，Site Recovery 將偵測不到它們。

您可以在 [工作] 索引標籤中追蹤「啟用保護」動作的進度，包括初始複寫。執行「完成保護」工作之後，虛擬機器即準備好進行容錯移轉。

![虛擬機器保護工作](./media/site-recovery-vmm-san/job-props.png)

## 步驟 8：測試部署

測試您的部署，以確定虛擬機器和資料容錯移轉如預期般運作。若要這樣做，您將必須選取複寫群組來建立復原方案。然後在方案上執行測試容錯移轉。

1. 在 [復原計畫] 索引標籤上，按一下 [建立復原計畫]。
2. 指定復原方案的名稱，以及來源和目標 VMM 伺服器。來源伺服器必須有已啟用容錯移轉和復原功能的虛擬機器。選取 [SAN]，僅檢視為 SAN 複寫設定的雲端。
3.![建立復原計畫](./media/site-recovery-vmm-san/r-plan.png)

4. 在 [選取虛擬機器] 中，選取複寫群組。所有與複寫群組關聯的虛擬機器，將會被選取並新增至復原方案。這些虛擬機器會新增到復原方案預設群組 (群組 1)。您可以視需要新增更多群組。請注意，複寫之後，虛擬機器將會根據復原方案群組的順序來啟動。

	![新增虛擬機器](./media/site-recovery-vmm-san/r-plan-vm.png)
5. 建立復原計畫之後，它會出現在 [復原計畫] 索引標籤上的清單中。
6. 在 [復原計畫] 索引標籤上，選取計畫，然後按一下 [測試容錯移轉]。
7. 在 [確認測試容錯移轉] 頁面上，選取 [無]。請注意，啟用此選項時，容錯移轉複本虛擬機器將不會連線到任何網路。這將會測試虛擬機器是否依照預期執行容錯移轉，但是不會測試您的複寫網路環境。請參閱[執行測試容錯移轉](site-recovery-failover.md#run-a-test-failover)中有關如何使用不同網路選項的詳細資訊。


	![選取測試網路](./media/site-recovery-vmm-san/test-fail1.png)

8. 測試虛擬機器將建立在複本虛擬機器所在的相同主機上。它並不會新增至複本虛擬機器所在的雲端。
9. 複製之後，複本虛擬機器的 IP 位址會不同於主要虛擬機器的 IP 位址。如果您是從 DHCP 發出位址，便會自動更新位址。如果您不是使用 DHCP 而想要確定位址相同，您將需要執行數個指令碼。
10. 執行此範例指令碼以抓取 IP 位址。

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

11. 執行此範例指令碼來更新 DNS (使用上一個範例指令碼抓取到的 IP 位址)。

		[string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## 後續步驟

在您執行測試容錯移轉檢查您的環境是否如預期般運作之後，請[深入了解](site-recovery-failover.md)不同類型的容錯移轉。

<!---HONumber=AcomDC_0330_2016-->