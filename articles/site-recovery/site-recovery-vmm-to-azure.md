<properties
	pageTitle="將 Hyper-V 虛擬機器 (位於 VMM 雲端中) 複寫至 Azure | Microsoft Azure"
	description="本文說明如何將位於 System Center VMM 雲端中 Hyper-V 主機上的 Hyper-V 虛擬機器複寫至 Azure。"
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
	ms.topic="hero-article"
	ms.date="03/15/2016"
	ms.author="raynew"/>

#  將 Hyper-V 虛擬機器 (位於 VMM 雲端中) 複寫至 Azure

Azure Site Recovery 服務可藉由協調虛擬機器與實體伺服器的複寫、容錯移轉及復原 (BCDR) 策略，為您的商務持續性與災害復原做出貢獻。機器可以複寫至 Azure，或次要的內部部署資料中心。如需快速概觀，請參閱[什麼是 Azure Site Recovery？](site-recovery-overview.md)。

## 概觀

本文說明如何部署 Site Recovery，將位於 VMM 私人雲端中的 Hyper-V 主機伺服器上的 Hyper-V 虛擬機器複寫至 Azure。

本文包含案例的必要條件，並示範如何設定 Site Recovery 保存庫、取得安裝於來源 VMM 伺服器上的「Azure Site Recovery 提供者」、在保存庫註冊伺服器、加入 Azure 儲存體帳戶、在 Hyper-V 主機伺服器上安裝 Azure Site Recovery 代理程式、設定將套用到所有受保護虛擬機器之 VMM 雲端的保護設定、然後啟用那些虛擬機器的保護。測試容錯移轉，確認一切如預期般運作以完成動作。

在這篇文章下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。

## 架構

![架構](./media/site-recovery-vmm-to-azure/topology.png)

- Azure Site Recovery 提供者是於 Site Recovery 部署期間安裝在 VMM，且在 Site Recovery 保存庫中註冊 VMM 伺服器。提供者會與 Site Recovery 通訊以處理複寫協調流程。
- Azure Recovery Services 代理程式是於 Site Recovery 部署期間安裝在 Hyper-V 主機伺服器上。它會將資料複寫處理至 Azure 儲存體。


## Azure 必要條件

以下是您在 Azure 中需要的內容。

必要條件 | 詳細資料
--- | ---
Azure 帳戶| 您將需要 [Microsoft Azure](https://azure.microsoft.com/) 帳戶。您可以從[免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。[深入了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 價格。 
Azure 儲存體 | 您需要 Azure 儲存體帳戶來儲存複寫的資料。複寫的資料會儲存在 Azure 儲存體，容錯移轉時會啟動 Azure VM。<br/><br/>您需要[標準異地備援儲存體帳戶](../storage/storage-redundancy.md#geo-redundant-storage)。此帳戶應與 Site Recovery 服務位於相同的區域，且與相同的訂用帳戶相關聯。請注意，複寫到進階儲存體帳戶目前不受支援，因此請勿使用。我們不支援使用[新的 Azure 入口網站](../storage/storage-create-storage-account.md)來跨資源群組移動所建立的儲存體帳戶。<br/><br/>[深入了解](../storage/storage-introduction.md) Azure 儲存體。
Azure 網路 | 容錯移轉發生時，您需要 Azure VM 會連接的 Azure 虛擬網路。Azure 虛擬網路必須位於與 Site Recovery 保存庫相同的區域中。 

## 內部部署必要條件

以下是您在內部部署中需要的內容。

必要條件 | 詳細資料
--- | ---
VMM | 您需要至少一部部署為實體或虛擬獨立伺服器，或是部署為虛擬叢集的 VMM 伺服器。<br/><br/>VMM 伺服器應執行含有最新累積更新的 System Center 2012 R2。<br/><br/>您需要在 VMM 伺服器上至少設定一個雲端。<br/><br/>您要保護的來源雲端必須包含一或多個 VMM 主機群組。<br/><br/>在 Keith Mayer 的部落格上的[逐步說明：使用 System Center 2012 SP1 VMM 建立私人雲端](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)中，深入了解設定 VMM 雲端。
Hyper-V | 您在 VMM 雲端中需要一或多個 Hyper-V 主機伺服器或叢集。主機伺服器應該有一或多個 VM。<br/><br/>Hyper-V 伺服器必須執行具有 Hyper-V 角色的最新 Windows Server 2012 R2，並安裝最新的更新。<br/><br/>任何含有您要保護之 VM 的 Hyper-V 伺服器都必須位於 VMM 雲端。<br/><br/>如果您是在叢集中執行 Hyper-V，請注意，當您擁有靜態 IP 位址叢集時，並不會自動建立叢集訊息代理程式。您必須手動設定叢集代理。在 Aidan Finn 的部落格項目中[深入了解](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters)。
受保護的機器 | 您想要保護的 VM 應該符合 [Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)。


## 網路對應的必要條件
當您在 Azure 網路對應中保護虛擬機器，請對應來源 VMM 伺服器上的 VM 網路和目標 Azure 網路，以啟用下列項目：

- 在相同網路上容錯移轉的所有機器都可以彼此連接，無論它們隸屬於哪個復原計畫都一樣。
- 如果目標 Azure 網路上已設定網路閘道，則虛擬機器可以連接到其他內部部署虛擬機器。
- 如果您未設定網路對應，則只有在相同復原計畫中容錯移轉的虛擬機器才能在容錯移轉到 Azure 之後彼此連接。

如果您想要部署網路對應，您需要下列項目：

- 您想要在來源 VMM 伺服器上保護的虛擬機器應該連線到 VM 網路。該網路應該連結到與雲端相關聯的邏輯網路。
- 複寫的虛擬機器可在容錯移轉之後連線的 Azure 網路。您將在容錯移轉時選取此網路。此網路應該和您的 Azure Site Recovery 訂用帳戶在相同的區域中。

準備網路對應，如下所示：

1. [閱讀](site-recovery-network-mapping.md)網路對應需求。
2. 在 VMM 中準備 VM 網路：

	- [設定邏輯網路](https://technet.microsoft.com/library/jj721568.aspx)。
	- [設定 VM 網路](https://technet.microsoft.com/library/jj721575.aspx)。


## 步驟 1：建立 Site Recovery 保存庫

1. 從您想要註冊的 VMM 伺服器登入[管理入口網站](https://portal.azure.com)。
2. 按一下 [資料服務] > [復原服務]，然後按一下 [Site Recovery 保存庫]。
3. 按一下 [新建] > [快速建立]。
4. 在 [名稱] 中，輸入保存庫的易記識別名稱。
5. 在 [地區] 中，選取保存庫的地理區域。若要查看支援的區域，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)中的＜各地區上市情況＞。
6. 按一下 [建立保存庫]。

	![新增保存庫](./media/site-recovery-vmm-to-azure/create-vault.png)

檢查狀態列，以確認是否順利建立保存庫。保存庫在主要復原服務頁面上會列為 [使用中]。

## 步驟 2：產生保存庫註冊金鑰

在保存庫中產生註冊金鑰。下載 Azure Site Recovery 提供者並將其安裝在 VMM 伺服器之後，您將使用此金鑰在保存庫中註冊 VMM 伺服器。

1. 在 [復原服務] 頁面中，按一下保存庫以開啟 [快速啟動] 頁面。您也可以使用圖示隨時開啟 [快速啟動]。

	![快速啟動圖示](./media/site-recovery-vmm-to-azure/qs-icon.png)

2. 在下拉式清單中，選取 [在內部部署 Hyper-V 站台與 Microsoft Azure 之間]。
3. 在 [準備 VMM 伺服器] 中，按一下 [產生註冊金鑰檔案]。該金鑰檔案會自動產生，並在產生後會維持 5 天有效。如果您沒有從 VMM 伺服器存取 Azure 入口網站，您必須將這個檔案複製到伺服器。

	![註冊金鑰](./media/site-recovery-vmm-to-azure/register-key.png)

## 步驟 3：安裝 Azure Site Recovery 提供者

1. 在 [快速啟動] 中，按一下 [準備 VMM 伺服器] 中的 [下載要在 VMM 伺服器上安裝的 Microsoft Azure Site Recovery 提供者]，以取得最新版的提供者安裝檔案。
2. 在來源 VMM 伺服器上執行此檔案。如果 VMM 部署在叢集中，且您是第一次安裝提供者，請將其安裝在作用中節點上，並完成安裝以在保存庫中註冊 VMM 伺服器。然後在其他節點上安裝提供者。請注意，如果您要升級提供者，您必須在所有節點上升級，因為它們都應該執行相同的提供者版本。
3. 安裝程式會執行一些先決條件檢查，並要求停止 VMM 服務的權限，以便開始安裝提供者。當安裝完成之後，VMM 服務將會自動重新啟動。如果您安裝在 VMM 叢集上，您會收到停止叢集角色的提示。

	![必要條件](./media/site-recovery-vmm-to-azure/prereq.png)

4. 在 [Microsoft Update] 中，您可以選擇加入以取得更新。啟用這項設定時，會根據您的 Microsoft Update 原則來安裝提供者更新。

	![Microsoft Update](./media/site-recovery-vmm-to-azure/updates.png)


5.  提供者的安裝位置已設為 **<SystemDrive>\\Program Files\\Microsoft System Center 2012 R2\\Virtual Machine Manager\\bin**。按一下 [Install]。

	![InstallLocation](./media/site-recovery-vmm-to-azure/install-location.png)

6. 安裝提供者之後，請按一下 [註冊]，在保存庫中註冊伺服器。

	![InstallComplete](./media/site-recovery-vmm-to-azure/install-complete.png)

7. 在 [網際網路連線] 中，指定 VMM 伺服器上執行的提供者連接到網際網路的方式。選取 [Use default system proxy settings]，以使用在伺服器上設定的預設網際網路連線設定。

	![網際網路設定](./media/site-recovery-vmm-to-azure/proxy.png)

	- 如果您想要使用自訂 proxy，您應該在安裝提供者之前進行設定。當您進行自訂 proxy 設定時，會執行一項測試以檢查 proxy 連線。
	- 如果您使用自訂 proxy，或者您的預設 proxy 需要驗證，您必須輸入 proxy 詳細資料，包含 proxy 位址和連接埠。
	- 下列 URL 應可從 VMM 伺服器和 Hyper-V 主機存取
		- *.hypervrecoverymanager.windowsazure.com
		- *.accesscontrol.windows.net
		- *.backup.windowsazure.com
		- *.blob.core.windows.net
		- *.store.core.windows.net
	- 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)中所述的 IP 位址和 HTTPS (443) 通訊協定。您也應該將打算使用以及美國西部之 Azure 區域的 IP 範圍設為白名單。

	- 如果您使用的是自訂 proxy，則會使用指定的 proxy 認證自動建立 VMM RunAs 帳戶 (DRAProxyAccount)。設定 proxy 伺服器，讓此帳戶可以成功進行驗證。在 VMM 主控台中，可以修改 VMM RunAs 帳戶設定。若要這樣做，請開啟 [設定] 工作區、展開 [安全性]、按一下 [執行身分帳戶]，然後修改 DRAProxyAccount 的密碼。您必須重新啟動 VMM 服務，這項設定才會生效。

8. 在 [註冊金鑰] 中，選取您從 Azure Site Recovery 下載並複製到 VMM 伺服器的金鑰。
9. 在 [保存庫名稱] 中，確認要註冊伺服器的保存庫名稱。 

	![伺服器註冊](./media/site-recovery-vmm-to-azure/credentials.png)

10. 您可以指定一個位置來儲存自動為資料加密產生的 SSL 憑證。如果您在 Site Recovery 部署期間針對 VMM 雲端啟用資料加密，則會使用此憑證。請保護此憑證的安全。當您執行至 Azure 的容錯移轉時，您將選取它來將加密的資料解密。

	![伺服器註冊](./media/site-recovery-vmm-to-azure/encryption.png)

11. 在 [伺服器名稱] 中，指定保存庫中 VMM 伺服器的易記識別名稱。在叢集設定中，指定 VMM 叢集角色名稱。

12. 在 [初始雲端中繼資料同步] 中，選取您是否要將 VMM 伺服器上所有雲端的中繼資料與保存庫同步。這個動作只需要在每個伺服器上進行一次。如果不要同步所有雲端，您可以取消核取這項設定，再於 VMM 主控台的雲端屬性中個別同步每個雲端。

	![伺服器註冊](./media/site-recovery-vmm-to-azure/friendly.png)

13. 按 [下一步]，完成此程序。註冊後，Azure Site Recovery 即可從 VMM 伺服器擷取中繼資料。此伺服器會顯示於保存庫中 [伺服器] 頁面的 [VMM 伺服器] 索引標籤上。

### 命令列安裝

您也可以使用下列命令列來安裝 Azure Site Recovery 提供者。這個方法可以用來在適用於 Windows Server 2012 R2 的伺服器核心上安裝提供者。

1. 將提供者安裝檔案和註冊金鑰下載至資料夾。例如：C:\\ASR。
2. 停止 System Center Virtual Machine Manager 服務
3. 從提高權限的命令提示字元中，使用下列命令擷取提供者安裝程式：

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. 安裝提供者，如下所示：

		C:\ASR> setupdr.exe /i

5. 註冊提供者，如下所示：

    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

參數如下所示：

 - **/Credentials**：必要參數，用來指定註冊金鑰檔案所在的位置  
 - **/FriendlyName**：對於 Hyper-V 主機伺服器名稱的必要參數，該伺服器會出現在 Azure Site Recovery 入口網站中。
 - **/EncryptionEnabled**：選擇性參數，指定您是否要在 Azure 中加密您的虛擬機器 (靜態加密)。檔名應該有 **.pfx** 副檔名。
 - **/proxyAddress**：指定 Proxy 伺服器位址的選用參數。
 - **/proxyport**：指定 Proxy 伺服器連接埠的選用參數。
 - **/proxyUsername**：選擇性參數，指定 Proxy 使用者名稱。
 - **/proxyPassword**：選擇性參數，指定 Proxy 密碼。  


## 步驟 4：建立 Azure 儲存體帳戶

1. 如果您沒有 Azure 儲存體帳戶，請按一下 [新增 Azure 儲存體帳戶] 以建立帳戶。
2. 建立帳戶並啟用異地複寫。此帳戶應與 Azure 站台復原服務位於相同的區域，且與相同的訂閱相關聯。

	![儲存體帳戶](./media/site-recovery-vmm-to-azure/storage.png)

>[AZURE.NOTE] 我們不支援使用[新的 Azure 入口網站](../storage/storage-create-storage-account.md)來跨資源群組移動所建立的儲存體帳戶。

## 步驟 5：安裝 Azure 復原服務代理程式

在 VMM 雲端中的每一個 Hyper-V 主機伺服器上，安裝 Azure 復原服務代理程式。

1. 按一下 [快速啟動] > [下載 Azure Site Recovery 服務代理程式並安裝在主機上]，以取得最新版的代理程式安裝檔案。

	![Install Recovery Services Agent](./media/site-recovery-vmm-to-azure/install-agent.png)

2. 在每一個 Hyper-V 主機伺服器上執行安裝檔案。
3. 在 [檢查先決條件] 頁面上，按 [下一步]。將自動安裝任何缺少的必要元件。

	![Prerequisites Recovery Services Agent](./media/site-recovery-vmm-to-azure/agent-prereqs.png)

4. 在 [安裝設定] 頁面上，指定您要安裝代理程式的位置，並選取將在其中安裝備份中繼資料的快取位置。然後按一下 [安裝]。
5. 安裝完成之後，按一下 [關閉] 來完成精靈。
	
	![註冊 MARS 代理程式](./media/site-recovery-vmm-to-azure/agent-register.png)

### 命令列安裝 

您也可以使用下列命令，從命令列安裝 Microsoft Azure 復原服務代理程式：

    marsagentinstaller.exe /q /nu
	
## 步驟 6：設定雲端保護設定

註冊 VMM 伺服器之後，您就可以設定雲端保護設定。當您安裝提供者之後，即可啟用 [將雲端資料與保存庫同步] 選項，如此一來，VMM 伺服器上的所有雲端將會出現在保存庫的 [受保護的項目]<b></b> 索引標籤中。

![發佈的雲端](./media/site-recovery-vmm-to-azure/clouds-list.png)

1. 在 [快速啟動] 頁面上，按一下 [設定 VMM 雲端的保護]。
2. 在 [受保護項目] 索引標籤上，按一下要設定的雲端，並移至 [設定] 索引標籤。
3. 在 [目標] 中，選取 [Azure]。
4. 在 [儲存體帳戶] 中，選取您想要用來複寫的 Azure 儲存體帳戶。 

	>[AZURE.NOTE] 我們不支援使用[新的 Azure 入口網站](../storage/storage-create-storage-account.md)來跨資源群組移動所建立的儲存體帳戶。

5. 將 [加密儲存的資料] 設為 [關閉]。此設定指定應該將內部部署與 Azure 之間複寫的資料加密。
6. 在 [複製頻率] 中保留預設設定。這個值指定應在來源與目標位置之間同步處理資料的頻率。
7. 在 [保留復原點的時間] 中保留預設設定。使用預設值 0 時，只會在複本主機伺服器上儲存主要虛擬機器的最新復原點。
8. 在 [應用程式一致快照的頻率] 中保留預設設定。這個值指定建立快照的頻率。快照會使用「磁碟區陰影複製服務」(VSS) 來確保建立快照時，應用程式是處於一致狀態。如果您設定一個值，請確定此值小於您設定的其他復原點數目。
9. 在 [複寫開始時間] 中，指定初次將資料複寫至 Azure 的開始時間。將會使用 Hyper-V 主機伺服器的時區。建議您將初次複寫排定在離峰時段進行。

	![Cloud replication settings](./media/site-recovery-vmm-to-azure/cloud-settings.png)

儲存設定之後，會建立一個工作，並可在 [工作] 索引標籤上進行監視。將會對 VMM 雲端中的所有 Hyper-V 主機伺服器設定複寫。

儲存之後，可在 [設定] 索引標籤上修改雲端設定。若要修改目標位置或目標儲存體帳戶，您需要移除雲端組態，然後重新設定雲端。請注意，如果您變更儲存體帳戶，則只會對修改儲存體帳戶之後才啟用保護的虛擬機器套用變更。現有的虛擬機器不會移轉至新的儲存體帳戶。

## 步驟 7：設定網路對應
開始網路對應之前，請確認來源 VMM 伺服器上的虛擬機器已連線到 VM 網路。此外，請建立一或多個 Azure 虛擬網路。請注意，多個 VM 網路可對應至單一 Azure 網路。

1. 在 [快速入門] 頁面上，按一下 [對應網路]。
2. 在 [網路] 索引標籤的 [來源位置] 中，選取來源 VMM 伺服器。在 [目標位置] 中選取 [Azure]。
3. 在 [來源] 網路中，會顯示與 VMM 伺服器相關聯的 VM 網路清單。在 [目標] 網路中，會顯示與訂用帳戶相關聯的 Azure 網路。
4. 選取來源 VM 網路，然後按一下 [對應]。
5. 在 [選取目標網路] 頁面上，選取您要使用的目標 Azure 網路。
6. 按一下打勾記號完成對應程序。

	![Cloud replication settings](./media/site-recovery-vmm-to-azure/map-networks.png)

儲存設定之後，工作會開始追蹤對應進度，可在 [工作] 索引標籤上進行監視。對應來源 VM 網路的任何現有複本虛擬機器都會連線到目標 Azure 網路。連線到來源 VM 網路的新虛擬機器都會在複寫之後連線到對應的 Azure 網路。如果您修改與新網路的現有對應，複本虛擬機器將使用新設定進行連線。

請注意，如果目標網路具有多個子網路，且其中一個子網路的名稱和來源虛擬機器所在之子網路名稱相同，複本虛擬機器將會在容錯移轉之後連線到該目標子網路。如果沒有目標子網路具有相符的名稱，虛擬機器將會連線到網路中的第一個子網路。

## 步驟 8：對虛擬機器啟用保護

正確設定伺服器、雲端和網路後，您就可以對雲端中的虛擬機器啟用保護。請注意：

- 虛擬機器必須符合 [Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)。
- 若要啟用保護功能，您必須為虛擬機器設定作業系統和作業系統磁碟屬性。當您使用虛擬機器範本在 VMM 中建立虛擬機器時，您可以設定屬性。您也可以在虛擬機器屬性的 [一般] 及 [硬體設定] 索引標籤上，為現有的虛擬機器設定這些屬性。若未在 VMM 中設定這些屬性，您將可在 Azure 站台復原入口網站中加以設定。

	![Create virtual machine](./media/site-recovery-vmm-to-azure/enable-new.png)

	![Modify virtual machine properties](./media/site-recovery-vmm-to-azure/enable-existing.png)


1. 若要啟用保護，請在虛擬機器所在雲端中的 [虛擬機器] 索引標籤上，按一下 [啟用保護] > [加入虛擬機器]。
2. 從雲端中所有虛擬機器的清單，選取您要保護的虛擬機器。

	![啟用虛擬機器保護](./media/site-recovery-vmm-to-azure/select-vm.png)

	您可以在 [作業] 索引標籤中追蹤 [啟用保護] 動作的進度，包括初始複寫。執行 [完成保護] 作業之後，虛擬機器即準備好進行容錯移轉。啟用保護並複寫虛擬機器之後，您將能夠在 Azure 中檢視這些虛擬機器。


	![虛擬機器保護工作](./media/site-recovery-vmm-to-azure/vm-jobs.png)

3. 驗證虛擬機器屬性，並視需要加以修改。

	![Verify virtual machines](./media/site-recovery-vmm-to-azure/vm-properties.png)

4. 在虛擬機器屬性的 [設定] 索引標籤上可以修改下列網路屬性。



- 目標虛擬機器的網路介面卡數目 - 網路介面卡的數目取決於您針對目標虛擬機器所指定的大小。查看[虛擬機器大小規格](../virtual-machines/virtual-machines-size-specs.md#size-tables)，了解虛擬機器大小所支援的介面卡數目。在修改虛擬機器的大小並儲存設定之後，當您下次開啟 [設定] 頁面時，網路介面卡的數量將會改變。目標虛擬機器的網路介面卡數目，是來源虛擬機器上的網路介面卡數目下限，以及所選虛擬機器大小支援的網路介面卡數目上限，如下所示：

	- 如果來源電腦上的網路介面卡數目小於或等於針對目標機器大小所允許的介面卡數目，則目標將具備與來源相同的介面卡數目。
	- 如果來源虛擬機器的介面卡數目超過針對目標大小所允許的數目，則將使用目標大小的最大值。
	- 例如，如果來源機器具有兩張網路介面卡，而目標機器大小支援四張，則目標機器將會有兩張介面卡。如果來源機器具有兩張介面卡，但支援的目標大小僅支援一張，則目標機器將只會有一張介面卡。 	

- 目標虛擬機器的網路 - 虛擬機器連接的網路取決於來源虛擬機器網路的網路對應。如果來源虛擬機器有一個以上的網路介面卡，且來源網路對應至目標上的不同網路，則您必須選擇其中一個目標網路。
- 每個網路介面卡的子網路 - 針對每個網路介面卡，您可以選取容錯移轉的虛擬機器會連接的子網路。
- 目標 IP 位址 - 如果來源虛擬機器的網路介面卡是設定為使用靜態 IP 位址，則您可以提供目標虛擬機器的 IP 位址。使用此功能，在容錯移轉之後保留來源虛擬機器的 IP 位址。如果未提供任何 IP 位址，在容錯移轉時會將任何可用的 IP 位址提供給網路介面卡。如果已指定目標 IP 位址，但是已由在 Azure 中執行的另一個虛擬機器使用，則容錯移轉將會失敗。  

	![修改網路屬性](./media/site-recovery-vmm-to-azure/multi-nic.png)

>[AZURE.NOTE] 不支援具有靜態 IP 位址的 Linux 虛擬機器。

## 測試部署

若要測試部署，您可以對單一虛擬機器執行測試容錯移轉，或者建立包含多部虛擬機器的復原方案，再對這個方案執行測試容錯移轉。

測試容錯移轉會在隔離的網路中模擬您的容錯移轉與復原機制。請注意：

- 如果您在容錯移轉之後要使用遠端桌面連接到 Azure 中的虛擬機器，請先在虛擬機器上啟用遠端桌面連線，再執行測試容錯移轉。
- 容錯移轉之後，您將透過遠端桌面使用公用 IP 位址連接到 Azure 中的虛擬機器。如果想要這樣做，請確定沒有任何網域原則禁止您使用公用位址連接到虛擬機器。

### 建立復原計畫

1. 在 [復原方案] 索引標籤上，增加一個新方案。指定一個名稱、在 [來源類型] 中指定 [VMM]、在 [來源] 中指定來源 VMM 伺服器，則目標將會是 Azure。

	![建立復原計畫](./media/site-recovery-vmm-to-azure/recovery-plan1.png)

2. 在 [選取虛擬機器] 頁面上，選取要新增到復原方案的虛擬機器。這些虛擬機器將新增到復原計畫的預設群組—群組 1。最多 100 部虛擬機器已在單一復原計畫中經過測試。

	- 如果您將虛擬機器屬性加入計畫之前，想要確認這些屬性，請按一下屬性所在之雲端的屬性頁面上的虛擬機器。您也可以在 VMM 主控台中設定虛擬機器屬性。
	- 顯示的所有虛擬機器已啟用保護。此清單包含啟用保護並已完成初始複寫的虛擬機器，以及利用初始複寫擱置啟用保護的虛擬機器。只有已完成初始複寫的虛擬機器可做為復原計畫的一部分進行容錯移轉。 


	![建立復原計畫](./media/site-recovery-vmm-to-azure/select-rp.png)

建立復原方案之後，它會出現在 [復原方案] 索引標籤中。您也可以將 [Azure 自動化 Runbook](site-recovery-runbook-automation.md) 加入至復原計劃，以自動化容錯移轉期間的動作。

### 執行測試容錯移轉

有兩種方式可以測試容錯移轉至 Azure。

- 在沒有 Azure 網路的情況下測試容錯移轉—這種測試容錯移轉可以檢查虛擬機器是否正確地出現在 Azure 中。在容錯移轉之後，虛擬機器不會連線到任何 Azure 網路。
- 利用 Azure 網路測試容錯移轉 - 這種測試容錯移轉可以檢查整個復寫環境是否如預期般出現並進行容錯移轉，虛擬機器會連線到只訂的目標 Azure 網路。針對子網路處理的測試容錯移轉，可根據複本虛擬機器的子網路得知測試虛擬機器的子網路。這和一般的複寫不同，一般複寫的複本虛擬機器子網路是根據來源虛擬機器的子網路得知。

如果您想要針對啟用保護的虛擬機器執行測試容錯轉移至 Azure ，卻不想指定 Azure 目標網路，您不需要作任何準備。若要以目標 Azure 網路執行測試容錯移轉，您必須建立與您的 Azure 正式作業網路 (當您在 Azure 中建立新網路時的預設行為) 分隔的新的 Azure 網路。如需詳細資訊，請參考如何[執行測試容錯移轉](site-recovery-failover.md#run-a-test-failover)。


您也必須針對複寫虛擬機器設定基礎結構，以如預期般運作。例如，具有網域控制站和 DNS 的虛擬機器可以使用 Azure Site Recovery 複寫到 Azure，而且可以使用測試容錯移轉，在測試網路中加以建立。如需詳細資訊，請參閱 [Active Directory 測試容錯移轉考量](site-recovery-active-directory.md#considerations-for-test-failover)一節。

如果要執行測試容錯移轉，請執行下列動作：

1. 在 [復原計畫] 索引標籤上，選取計畫，然後按一下 [測試容錯移轉]。
2. 在 [確認測試容錯移轉] 頁面上，選取 [無] 或特定的 Azure 網路。請注意，如果您選取 [無]，測試容錯移轉將會檢查虛擬機器是否正確地複寫至 Azure，但並不會檢查您的複寫網路設定。

	![沒有網路](./media/site-recovery-vmm-to-azure/test-no-network.png)

3. 如果已啟用雲端的資料加密，當您開啟選項以啟用雲端的資料加密時，請在 [加密金鑰] 中選取在 VMM 伺服器上安裝提供者時發出的憑證。
4. 在 [工作] 索引標籤上，您可以追蹤容錯移轉進度。您應該可以在 Azure 入口網站中看到該虛擬機器測試複本。如果您設定從內部部署網路存取虛擬機器，您可以初始化虛擬機器的「遠端桌面」連線。
5. 當容錯移轉到達 [完成測試] 階段時，請按一下 [完成測試] 以完成測試容錯移轉。您可以向下切入到 [工作] 索引標籤，來追蹤容錯移轉進度和狀態，並執行任何所需的動作。
6. 在容錯移轉之後，您將可以在 Azure 入口網站中看到該虛擬機器測試複本。如果您設定從內部部署網路存取虛擬機器，您可以初始化虛擬機器的「遠端桌面」連線。執行下列動作：

    1. 確認虛擬機器成功啟動。
    2. 如果您在容錯移轉之後要使用遠端桌面連接到 Azure 中的虛擬機器，請先在虛擬機器上啟用遠端桌面連線，再執行測試容錯移轉。您也必須在虛擬機器上新增 RDP 端點。您可以利用 [Azure 自動化 Runbook](site-recovery-runbook-automation.md) 來執行這個動作。
    3. 容錯移轉之後，如果您使用公用 IP 位址，利用 [遠端桌面] 連接到 Azure 中的虛擬機器，請確定您沒有任何網域原則會阻止您使用公用位址連接到虛擬機器。

7.  測試完成之後，請執行下列動作：
	- 按一下 [測試容錯移轉完成]。清除測試環境，以自動關閉電源及刪除測試虛擬機器。
	- 按一下 [記事] 記錄並儲存關於測試容錯移轉的任何觀察。

## 後續步驟

深入了解[設定復原計劃](site-recovery-create-recovery-plans.md)和[容錯移轉](site-recovery-failover.md)。

<!---HONumber=AcomDC_0316_2016-->
