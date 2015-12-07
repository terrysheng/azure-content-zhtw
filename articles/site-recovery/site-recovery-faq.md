<properties 
	pageTitle="Azure Site Recovery：常見問題集" 
	description="本文討論關於使用 Azure Site Recovery 的熱門問題。" 
	services="site-recovery" 
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags 
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="08/26/2015" 
	ms.author="lauraa"/>


# Azure Site Recovery：常見問題集
## 本文內容

本文包含有關 Azure Site Recovery 的常見問題集。如需 Site Recovery 的簡介和相關部署案例，請參閱 [Site Recovery 概觀](site-recovery-overview.md)。

如果您在閱讀本文後有其他問題，可以將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)。


## 一般
### ASR 安全嗎？ 您要將哪些資料傳送到 Azure？

是的，ASR 很安全。它不會攔截您的應用程式資料，也不會包含虛擬機器內正在執行之項目的任何相關資訊。不論是從 Hyper-V 主機或虛擬機器，都不需要網際網路連線。

由於複寫是在您自己的企業和服務提供者站台之間進行，因此不會將任何應用程式資料傳送到 Azure。只有中繼資料 (例如 VM 或雲端名稱，協調容錯移轉所需的資料) 會傳送到 Azure。ASR 不具攔截應用程式資料的功能，而該資料一律會保持內部部署狀態。

ASR 已經過 ISO 27001:2005 認證，目前正處於完成其 HIPAA、DPA 及 FedRAMP JAB 評定的程序。

### 法規遵循需求要求即使是來自內部部署環境的中繼資料，也必須保留在相同的地理區域內。ASR 可以確保我們符合該需求嗎？

是。當您在選擇的區域中建立 Site Recovery 保存庫時，我們可確保我們啟用並協調您的災害復原安裝程式所需的所有中繼資料都會保留於該區域的地理界限內。

### 是否提供可用來將 ASR 工作流程自動化的 SDK？

是。您可以使用 Rest API、PowerShell 或 Azure SDK，將 ASR 工作流程自動化。您可以在標題為 [PowerShell 對於 Azure Site Recovery 的支援簡介](http://azure.microsoft.com/blog/2014/11/05/introducing-powershell-support-for-azure-site-recovery/)的部落格文章中找到更多詳細資料。

### ASR 是否將複寫加密 
內部部署與 Azure 間及內部部署間複寫支援 Hyper-V 和 VMM 保護案例的傳輸中加密。Azure 的 Hyper-V 和 VMM 保護也支援靜止加密。如需詳細資訊，請參閱[這篇文章](https://azure.microsoft.com/blog/2014/09/02/azure-site-recovery-privacy-security-part1/)。

### 可以將複寫/複製頻率提高到超過 15 分鐘嗎？
* **Hyper-V & VMM 案例**︰不可以，使用主機型複寫的 Hyper-V 虛擬機器複寫只可以設定為 30 秒、5 分鐘和 15 分鐘
* **VMware/實體案例**︰這不適用於來賓型複寫，因為技術使用連續資料保護。

### 可以從使用 ASR 的複寫中排除特定的磁碟嗎？
不支援此做法。透過 [Azure Site Recovery 意見反應論壇 - 從複寫中排除磁碟](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication)傳送您的意見反應。

### 可以複寫以動態磁碟為基礎的虛擬機器嗎？
HYPER-V 和 VMM 案例支援動態磁碟。VMware 虛擬機器或實體機器案例不支援動態磁碟。透過 [Azure Site Recovery 意見反應論壇](http://feedback.azure.com/forums/256299-site-recovery)傳送您的意見反應。

### 支援哪種儲存體帳戶類型？
支援[標準異地備援儲存體](../storage/storage-redundancy.md#geo-redundant-storage)。只有 [VMware 虛擬機器或實體機器案例](site-recovery-vmware-to-azure.md)支援 [進階儲存體帳戶]((../storage/storage-premium-storage-preview-portal/)。標準本地備援儲存體支援處於待處理狀態，請透過[本地備援儲存體支援](http://feedback.azure.com/forums/256299-site-recovery/suggestions/7204469-local-redundant-type-azure-storage-support)傳送您的意見反應。

### 可以將現有復原網站複寫擴充到第三個網站嗎？
不支援此做法。透過 [Azure Site Recovery 意見反應論壇 - 支援擴充複寫](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication)傳送您的意見反應。

### 可以使用離線機制將初始磁碟植入到 Azure 嗎？
不支援此做法。透過 [Azure Site Recovery 意見反應論壇 - 支援離線複寫](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)傳送您的意見反應。

### 可以在以 Hyper-v 做為來源時，調節分配給複寫流量的頻寬嗎？
- 如果您在兩個內部部署網站間進行複寫，您便可以使用 Windows 服務品質來進行此動作。以下為範例指令碼： 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- 如果您正在複寫至 Azure，您可以使用下列範例 Powershell Cmdlet 來設定指令碼：

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)


## 版本支援

### 支援哪些版本的 Windows Server 主機和叢集？
當您選擇 Hyper-V 複本，在兩個內部部署 Hyper-V 站台之間啟用複寫和保護時，可以使用 Windows Server 2012 和 Windows Server 2012 R2。當您要從 Hyper-v 伺服器設定複寫到Azure 時，需要 Windows Server 2012 R2。


### 支援哪些版本的客體作業系統？
若為內部部署之間的保護，您可以在標題為《[關於虛擬機器和客體作業系統](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)》的主題中，找到支援客體作業系統的最新清單。針對 Hyper-v 到 Azure 的案例，Azure Site Recovery 支援所有 Azure 支援的客體作業系統。針對 VMware 到 Azure 的案例，請參閱[這篇文章](site-recovery-vmware-to-azure.md#before-you-start)

### 當 Hyper-V 正在用戶端作業系統上執行時是否可以設定虛擬機器保護？

您無法設定正在用戶端作業系統上執行的 Hyper-V 來將虛擬機器複寫至 Azure。只有伺服器作業系統才支援 Hyper-V 複寫。您可以依照[這篇文章](site-recovery-vmware-to-azure.md)中的說明將實體用戶端電腦複寫至 Azure。

### ASR 是否支援第 2 代機器？

是，ASR 支援將 Hyper-V 上的第 2 代虛擬機器複寫到 Azure。ASR 會在容錯移轉期間從第 2 代轉換成第 1 代。在容錯回復時，機器會轉換回第 1 代。如需進一步資訊，請[深入閱讀](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)。


## 在服務提供者網站之間部署 

### 這個解決方案適用於專用或共用的基礎結構模型嗎？
是，ASR 支援專用和共用的基礎結構模型。

### 我租用戶的身分識別是否會與 Azure 共用？
否。事實上，您的租用戶不需要存取 ASR 入口網站。只有服務提供者管理員會在 Azure 的 ASR 入口網站上執行動作。

### 我的租用戶將會收來自 Azure 的帳單嗎？
否。Microsoft Azure 的計費關係會與服務提供者直接相關。服務提供者負責為其租用戶產生特定的帳單。

### 我的租用戶應用程式資料一律會傳送到 Azure 嗎？
針對 DR 使用服務提供者所擁有的站台時，絕對不會將應用程式資料傳送到 Azure。資料已經過加密，並且會在服務提供者站台之間直接進行複寫。使用 Azure 做為 DR 站台時，會將應用程式資料傳送到 Azure - 靜態和傳輸中的資料仍會經過加密。您也可以使用 VPN、ExpressRoute 或公用的網際網路，來建立與 Azure 的連線。

### 使用 Azure 做為 DR 站台時，我們需要隨時在 Azure 中執行虛擬機器嗎？
否，ASR 是設計來做為第一級的公用雲端 DRaaS 解決方案。資料會複寫到您自己的訂用帳戶內，異地備援的 Azure 儲存體帳戶中。當您執行 DR 練習或實際容錯移轉時，ASR 會在您的訂用帳戶中自動建立虛擬機器。

### 當我選擇使用 Azure 做為 DR 站台時，您可以確保會有租用戶層級的隔離嗎？
是。

### 目前支援哪些平台？
我們支援 Azure Pack、雲端平台系統，以及 System Center 架構的 Hyper 2012 及較高版本的部署。若要深入了解 ASR 和 Azure Pack 整合的詳細資訊，請參閱[設定虛擬機器的保護](https://technet.microsoft.com/library/dn850370.aspx)。

### 您也支援單一 Azure 套件與單一 VMM 伺服器部署嗎？
是，單一 SCVMM 部署支援下列任一個案例 - 在服務提供者站台之間的 DR，以及在服務提供者站台與 Azure 之間的 DR。

當您在服務提供者站台之間啟用 DR 時也支援單一的 Azure 套件部署，不過，ASR Runbook 整合不適用於此拓撲。

## 在 Hyper-V 網站與 Azure (不含 VMM) 之間部署

### ASR 會要求您啟用站台對站台 VPN 嗎？
否，這並非強制動作；ASR 也可透過公用網際網路來運作。不過，如果您已設定站台對站台 VPN，就能使用和以前一樣的方式來存取已容錯移轉的虛擬機器。如需啟用災害復原到 Azure 時的網路考量詳細資訊，請參閱[適用於 Microsoft Azure 做為災害復原站台的網路基礎結構安裝部落格](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/)。

### 除了 ASR 保護費用，我還需要支付任何費用嗎？
在穩定狀態下，我們會將變更複寫到異地備援的 Azure 儲存體，而您不需支付任何 Azure IaaS 虛擬機器的費用 (值得注意的優點)。當您執行容錯移轉到 Azure 時，ASR 會自動建立 Azure IaaS 虛擬機器，之後您就需要支付在 Azure 中取用計算資源的費用。

### 我有一家分公司尚未安裝 VMM。我可以在該分公司中啟用對 Azure 之工作負載的保護嗎？ 使用 ASR 執行此動作有哪些主要優點？
是，您可以使用 ASR 來保護在 Hyper-V 主機上執行且 SCVMM 不會管理的虛擬機器。

當您使用 ASR 來管理所有分公司的災害復原需求時，可在單一中央位置取得所有分公司工作負載的整合檢視。您也可以從總公司實現所有分公司的單鍵容錯移轉與災害復原管理，甚至不需到分公司就能完成。目前不支援使用網路對應和靜態加密來進行分公司的災害復原。

### 針對分公司，我可以保護的虛擬機器數目有任何限制嗎？
否。支援內容與我們的企業案例中所支援的一樣，維持不變。

### 是否需要在我想保護的虛擬機器上安裝代理程式？

您不需要在虛擬機器上安裝代理程式。您需要在每部執行要保護之虛擬機器的 Hyper-V 伺服器上安裝 Site Recovery Provider 和復原服務代理程式。這兩個元件會在 Site Recovery 部署期間下載，並以單一 Provider 檔案的方式安裝。

### 在 Heper-V 伺服器上執行的 Provider 是否需要任何特殊防火牆設定？

不需要特定設定。伺服器上的 Provider 元件會透過 HTTPS 連線與 Azure 通訊，並使用 Hyper-V 主機伺服器上的預設內部網路設定，除非有另外指定。

### Hyper-V 伺服器是否需要是網域成員？

不需要，伺服器不需要位於網域中。

## 在兩個 VMM 資料中心之間部署

### 是否可以將已複寫至第二個網站的虛擬機器複寫至 Azure？ 

不行，不支援此類型鏈結複寫。

### 需要憑證才能設定兩個 VMM 資料中心之間的保護嗎？

否。在 ASR 中設定 VMM 雲端之間的保護時，請指定驗證類型。除非您已設定可用的 Kerberos 環境，否則請選取 HTTPS。Azure Site Recovery 會自動設定用於 HTTPS 驗證的憑證。不需要進行任何手動設定。如果您確實已選取 Kerberos，Kerberos 票證將會用於主機伺服器的相互驗證。根據預設，連接埠 8083 (用於 Kerberos) 和 8084 (用於憑證) 將在 Hyper-V 主機伺服器上的 Windows 防火牆中開啟。請注意，這項設定只有在 Hyper-V 主機伺服器是執行於 Windows Server 2012 R2 時才有重要性。



## 使用 SAN 在兩個 VMM 資料中心之間部署

### 如果我已經設定以 SAN 為基礎的複寫且不想進行變更，該怎麼辦？
沒問題。ASR 支援複寫可能已經設定的案例，在此情況下，您可以利用 ASR 進行以虛擬機器為中心的災害復原協調、災害復原網路的設定、應用程式感知的復原方案、災害復原練習，以及您的法規遵循與稽核報告需求。


### 我需要 VMM 嗎？ 我需要在這兩端使用 VMM 嗎？
是。我們需要使用陣列特定的 SMI-S 提供者，在 VMM 的管理下帶出 SAN 陣列。

我們支援以陣列類型為依據的單一 VMM HA 部署，不過，建議的設定是使用不同的 VMM 伺服器來管理站台。


### 支援的儲存體陣列有哪些？

NetApp、EMC 和 HP 已啟用 Azure Site Recovery SAN 複寫支援 (包含其 SMI-S 提供者的更新)。如需詳細資訊，請參閱下列連結。

- [NetApp Clustered Data ONTAP 8.2](http://community.netapp.com/t5/Technology/NetApp-Unveils-Support-for-Microsoft-Azure-SAN-Replication-with-SMI-S-and/ba-p/94483)
- [EMC VMAX 系列](https://thecoreblog.emc.com/high-end-storage/microsoft-azure-site-recovery-now-generally-available-vmax-srdf-integration-pack-ready-for-public-review/)    
- [HP 3PAR](http://h20195.www2.hp.com/V2/GetDocument.aspx?docname=4AA5-7068ENW&cc=us&lc=en)


### 如果我不確定有關儲存體管理的資訊，該怎麼辦？
我們會使用您儲存體管理員所設定的現有複寫，這表示儲存體管理員不需要在其陣列上進行任何變更。但是，若組織想要透過 SCVMM 將其儲存體管理自動化，也可以使用 ASR 和 VMM 來佈建儲存體。

### 我可以支援同步處理複寫嗎？ 客體叢集？ 共用儲存體？
是、是、是！

### 純網路：我在安裝內部部署時需要什麼，才能使其運作？
使用以 (SAN) 陣列為基礎的複寫來啟用 Hyper-V 站台之間的複寫和保護時，您只需要在 SCVMM 伺服器上安裝 ASR DR 提供者。請記住，這也是*唯一*需要連線到網際網路的主機。


您也需要使用更新的 SMI-S 提供者，透過 SCVMM 來探索陣列，該提供者是由您的個別儲存體廠商所提供。

## 在 VMware 與 Azure 之間部署

### 我有一個複製的 VMware VM。我可以將複製的 VM 保護到 Azure 嗎？
您無法複製受保護的 VM。只要複製的 VM 中沒有已安裝的行動服務，您就可以將複製的 VMware VM 保護到 Azure。您可以在安裝行動服務前複製 VM，以避免重複項目，因為兩部 VM 會使用相同的 GUID 報告而影響複寫。

### 我可以複製處理序伺服器 VM 嗎？
否，您不應該複製處理序伺服器。當部署處理序伺服器時，它會建立自己的唯一識別碼。如果複製，則兩部處理序伺服器將會有相同的 GUID 而影響現有的複寫。


## 在實體伺服器與 Azure 之間部署

### 是否可以使用 Azure 來保護我的實體內部部署伺服器？

可以，您可以使用 ASR 複寫實體伺服器，並於內部部署伺服器關閉時，在 Azure 中以 VM 的方式執行伺服器。目前不支援容錯回復至內部部署實體伺服器。您可以容錯回復至在 Hyper-V 或 VMware 上執行的虛擬機器。


## 容錯移轉

### 如果 Azure 已關閉，該怎麼辦？ 我可以從 VMM 觸發容錯移轉嗎？

儘管 Azure 是針對服務恢復能力所設計，但我們還是想要為最壞的狀況做打算。ASR 已經過設計，可容錯移轉到符合 Azure SLA 的次要 Azure 資料中心。我們也會確保即使發生這種情況，您的中繼資料和 ASR 保存庫都會保留在您針對保存庫所選擇的相同地理區域中。


### 如果我用來做為主要資料中心的 ISP 在災害發生期間也發生了中斷現象，該怎麼辦？ 如果我用來做為次要資料中心的 ISP 也發生了中斷現象，該怎麼辦？

您可以使用 ASR 入口網站，只需按一下就能執行非計劃性容錯移轉。您的主要資料中心不需具備連線能力，就能執行容錯移轉。

在容錯移轉之後，需要在次要資料中心執行的應用程式很可能需要某種形式的網際網路連線。ASR 假設，即使主要站台與 ISP 可能會在災害發生期間受到影響，但是次要站台的 SCVMM 伺服器仍會連接到 ASR。

### ASR 支援容錯回復嗎？
是。從 Azure 容錯回復到內部部署，只需透過 ASR 入口網站使用單鍵程序。

### 可以將容錯移轉自動化嗎？

沒有自動化容錯移轉選項。您可以在入口網站按下單一按鈕來觸發容錯移轉，或者您可以使用 [Site Recovery PowerShell](https://msdn.microsoft.com/library/dn850420.aspx) 觸發容錯移轉。您可以使用內部部署 Orchestrator 或 Operations Manager 來見證虛擬機器失敗，然後決定使用 SDK 觸發容錯移轉。就可以讓此程序變成自動化程序。




## 監控

### 您可以提供哪些解決方案，讓我可用來監視安裝程式和進行中的複寫？
您可以直接向 ASR 服務訂閱電子郵件警示，以收到任何需要注意之問題的相關通知。您也可以使用 SCOM 來監視進行中之複寫的健康情況。

## 後續步驟

若要開始部署 ASR：

- [設定內部部署 VMM 站台和 Azure 之間的保護](site-recovery-vmm-to-azure.md) 
- [設定內部部署 Hyper-V 站台和 Azure 之間的保護](site-recovery-hyper-v-site-to-azure.md) 
- [設定兩個內部部署 VMM 站台之間的保護](site-recovery-vmm-to-vmm.md) 
- [利用 SAN 設定兩個內部部署 VMM 站台之間的保護](site-recovery-vmm-san.md) 
- [利用單一 VMM 伺服器設定保護](site-recovery-single-vmm.md) 

 

<!---HONumber=AcomDC_1125_2015-->