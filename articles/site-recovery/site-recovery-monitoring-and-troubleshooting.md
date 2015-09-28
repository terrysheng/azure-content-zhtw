<properties
	pageTitle="VMM 與 Hyper-V 網站保護的「監視與疑難排解指南」" 
	description="Azure Site Recovery 可將內部部署伺服器上的虛擬機器複寫、容錯移轉及復原協調至 Azure 或次要資料中心。使用這份文件來監控和疑難排解 VMM 或 Hyper-V 網站保護。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="anbacker" 
	manager="mkjain" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="09/01/2015" 
	ms.author="anbacker"/>
	
# 監視和疑難排解 VMware、VMM、Hyper-V 和實體網站的保護

此「監視與疑難排解指南」可讓您了解追蹤複寫健康狀態和 Azure 站台復原的疑難排解技術。

## 了解元件

### 用於在內部部署和 Azure 之間複寫的 VMware/實體網站部署。
若要設定內部部署 VMware/實體機器之間的 DR，必須設定設定伺服器、主要目標和處理序伺服器。啟用來源伺服器的保護時，Azure Site Recovery 將會安裝行動服務。一旦來源伺服器容錯移轉至 Azure 以致內部部署中斷之後，客戶必須在 Azure 中設定處理序伺服器以及在內部部署環境中設定主要目標伺服器，以在重建的內部部署環境中保護來源伺服器。

![用於在內部部署和 Azure 之間複寫的 Hyper-V 網站部署。](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### 用於在內部部署網站之間複寫的 VMM 網站部署。

在兩個內部部署位置之間設定 DR 的一部分；Azure Site Recovery 提供者必須下載並安裝在 VMM 伺服器上。提供者需要網際網路連線，以確保從 Azure 入口網站觸發的所有作業都會轉譯成內部部署作業，像是啟用保護、關閉主要端虛擬機器做為容錯移轉的一部分等等。

![用於在內部部署網站之間複寫的 VMM 網站部署。](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### 用於在內部部署和 Azure 之間複寫的 VMM 網站部署。

在內部部署和 Azure 之間設定 DR 的一部分；Azure Site Recovery 提供者必須下載並安裝在 VMM 伺服器上，連同 Azure 復原服務代理程式，它必須安裝在每個 Hyper-V 主機上。如需詳細資訊，請參閱[了解網站至 Azure 保護](./site-recovery-understanding-site-to-azure-protection.md)。

![用於在內部部署和 Azure 之間複寫的 VMM 網站部署。](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### 用於在內部部署和 Azure 之間複寫的 Hyper-V 網站部署。

與 VMM 部署相同 – 不同的是提供者和代理程式都安裝在 HYPER-V 主機本身。如需詳細資訊，請參閱[了解網站至 Azure 保護](./site-recovery-understanding-site-to-azure-protection.md)。

## 監視組態、保護和復原作業

ASR 中的每個作業都會被稽核，並且在 [工作] 索引標籤底下追蹤。如果發生任何組態、保護或復原錯誤，瀏覽至 [工作] 索引標籤，查看是否有任何失敗。

![監視組態、保護和復原作業](media/site-recovery-monitoring-and-troubleshooting/image3.png)

一旦您在 [工作] 檢視底下找到失敗，選取 [工作]，然後按一下該工作的 [錯誤詳細資料]。

![監視組態、保護和復原作業](media/site-recovery-monitoring-and-troubleshooting/image4.png)

錯誤詳細資料可協助您識別可能的原因以及問題的建議事項。

![監視組態、保護和復原作業](media/site-recovery-monitoring-and-troubleshooting/image5.png)

在上述情況中，進度中似乎有另一個作業，就是該作業導致保護組態失敗。請確定您根據建議解決問題 – 然後按一下 [重新啟動] 來重新起始作業。

![監視組態、保護和復原作業](media/site-recovery-monitoring-and-troubleshooting/image6.png)

[重新啟動] 選項不適用於所有作業 – 對於那些沒有 [重新啟動] 選項的作業，瀏覽回物件，然後再一次重做作業。每個工作可以使用 [取消] 按鈕，在進行中的任何時間點取消。

![監視組態、保護和復原作業](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## 監視虛擬機器的複寫健全狀況

透過 Azure 入口網站對每個受保護實體的 ASR 提供者中央與遠端監視。在選取 [VMM 雲端] 或 [保護群組] 之後，瀏覽至 [受保護的項目]。[VMM 雲端] 索引標籤只適用於 VMM 型部署，和在[保護群組] 索引標籤底下具有受保護的實體的所有其他案例。

![監視虛擬機器的複寫健全狀況](media/site-recovery-monitoring-and-troubleshooting/image8.png)

然後在個別雲端或保護群組底下選取受保護的實體。一旦您選取受保護的實體，所有允許的作業就會顯示在下方窗格中。

![監視虛擬機器的複寫健全狀況](media/site-recovery-monitoring-and-troubleshooting/image9.png)

如上所示，如果虛擬機器健康狀態嚴重 – 您可以按一下底部的 [錯誤詳細資料] 按鈕，以查看錯誤。根據所述的「可能的原因」和「建議」解決問題。

![監視虛擬機器的複寫健全狀況](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![監視虛擬機器的複寫健全狀況](media/site-recovery-monitoring-and-troubleshooting/image11.png)

注意：如果有任何作用中的作業正在進行中或失敗，則如先前所述瀏覽至 [工作] 檢視，以檢視工作的特定錯誤。

## 疑難排解內部部署 Hyper-V 問題

連接到內部部署 HYPER-V 管理員主控台，選取虛擬機器，然後查看複寫健康狀態。

![疑難排解內部部署 Hyper-V 問題](media/site-recovery-monitoring-and-troubleshooting/image12.png)

在此案例中，[*複寫健康狀態*] 已標示為 [嚴重] – [*檢視複寫健康狀態*] 以查看詳細資料。

![疑難排解內部部署 Hyper-V 問題](media/site-recovery-monitoring-and-troubleshooting/image13.png)

在虛擬機器複寫已暫停的情況下，以滑鼠右鍵按一下並選取 [複寫] -> [繼續複寫] ![疑難排解內部部署 Hyper-V 問題](media/site-recovery-monitoring-and-troubleshooting/image19.png)

如果虛擬機器移轉透過 ASR 設定的新 Hyper-V 主機 (在叢集或獨立電腦中)，虛擬機器的複寫將不受影響。確定新的 Hyper-V 主機符合所有必要條件並已使用 ASR 進行設定。

### 事件記錄檔

| 事件來源 | 詳細資料 |
|-------------------------	|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| **Applications and Service Logs/Microsoft/VirtualMachineManager/Server/Admin** (VMM 伺服器) | 這可提供很有用的記錄以疑難排解許多不同的 VMM 問題。 |
| **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** (Hyper-V 主機) | 這可提供很有用的記錄以疑難排解許多 Microsoft Azure 復原服務代理程式問題。<br/> ![Hyper-V 主機的事件來源](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Applications and Service Logs/Microsoft/Azure Site Recovery/Provider/Operational** (Hyper-V 主機) | 這可提供很有用的記錄以疑難排解許多 Microsoft Azure Site Recovery 服務問題。<br/> ![Hyper-V 主機的事件來源](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** (Hyper-V 主機) | 這可提供很有用的記錄以疑難排解許多 Hyper-V 虛擬機器管理問題。<br/> ![Hyper-V 主機的事件來源](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |


### HYPER-V 複寫記錄選項

與 Hyper-V 複本有關的所有事件都會記錄在位於 **Applications and Services Logs\\Microsoft\\Windows** 底下的 Hyper-V-VMMS\\Admin 記錄檔。此外，可以針對 Hyper-V-VMMS 啟用分析記錄檔。若要啟用此記錄檔，先讓 [分析] 與 [偵錯] 記錄檔可在 [事件檢視器] 中檢視。開啟 [事件檢視器]，然後在 [**檢視**] 功能表中，按一下 [**顯示分析與偵錯記錄檔**]。

![疑難排解內部部署 Hyper-V 問題](media/site-recovery-monitoring-and-troubleshooting/image14.png)

[分析] 記錄檔會顯示在 Hyper-V-VMMS 底下

![疑難排解內部部署 Hyper-V 問題](media/site-recovery-monitoring-and-troubleshooting/image15.png)

然後，在 [**動作**] 窗格中，按一下 [**啟用記錄檔**]。一旦啟用之後，它會出現在 [**效能監視器**]，做為位於 [**資料收集器集合工具**] 底下的事件追蹤工作階段。

![疑難排解內部部署 Hyper-V 問題](media/site-recovery-monitoring-and-troubleshooting/image16.png)

若要檢視收集的資訊，請先藉由停用記錄檔來停止追蹤工作階段，然後儲存記錄檔並且在 [事件檢視器] 中重新開啟，或使用其他工具視需要轉換它。



## 連絡 Microsoft 支援

### 記錄檔集合

對於 VMM 網站保護，請參考[使用支援診斷平台 (SDP) 工具的 ASR 記錄檔收集](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)，以收集必要的記錄檔。

對於 Hyper-V 網站保護，請下載[工具](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)並且在 Hyper-V 主機上執行，以收集記錄檔。

對於 VMware/實體案例，請參考 [VMware 和實體網站保護的 Azure Site Recovery 記錄檔收集](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx)，以收集必要的記錄檔。

工具會在本機收集記錄檔，而這些記錄檔會位於 **%LocalAppData%\\ElevatedDiagnostics** 底下隨機命名的子資料夾中

![從 Hyper-V 網站保護顯示的範例步驟。](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### 開啟支援票證

若要針對 ASR 引發支援票證，請使用 <http://aka.ms/getazuresupport> 的 URL 連絡 Azure 支援

## 知識庫文章

-   [如何保留已容錯移轉或移轉至 Azure 的受保護虛擬機器的磁碟機代號](http://support.microsoft.com/kb/3031135)
-   [如何管理內部部署至 Azure 保護網路頻寬使用](https://support.microsoft.com/kb/3056159)
-   [ASR：當您嘗試啟用虛擬機器的保護時的「找不到叢集資源」錯誤](http://support.microsoft.com/kb/3010979)
-   [了解及疑難排解 HYPER-V 複本指南](http://www.microsoft.com/en-in/download/details.aspx?id=29016) 

## 常見的 ASR 錯誤及其解決方案

以下是您可能會遇到的常見錯誤及其解決方案。每個錯誤記錄在個別的 WIKI 頁面。

### 一般
-   <span style="color:green;">NEW</span> [作業失敗，發生錯誤「作業正在進行中。」 錯誤 505, 514, 532](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
-   <span style="color:green;">NEW</span> [作業失敗，發生錯誤「伺服器未連接到網際網路」。錯誤 25018](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### 設定
-   [無法註冊 VMM 伺服器，因為發生內部錯誤。如需錯誤的詳細資訊，請參閱 Site Recovery 入口網站中的工作檢視。再次執行安裝程式以註冊伺服器。](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
-   [無法連接到 Hyper-V 復原管理員保存庫。請確認 Proxy 設定，或稍後再試一次。](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### 組態
-   [無法建立保護群組：擷取伺服器清單時發生錯誤。](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
-   [Hyper-V 主機叢集至少包含一個靜態網路介面卡，或未設定連線的介面卡來使用 DHCP。](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
-   [VMM 沒有完成動作的權限](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
-   [設定保護時無法選取訂用帳戶內的儲存體帳戶](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### 保護
- <span style="color:green;">NEW</span> [啟用保護失敗，發生錯誤「保護無法針對虛擬機器設定」。錯誤 60007, 40003](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
- <span style="color:green;">NEW</span> [啟用保護失敗，發生錯誤「保護無法針對虛擬機器啟用。」 錯誤 70094](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
- <span style="color:green;">NEW</span> [即時移轉錯誤 23848 - 虛擬機器即將使用 Live 類型移動。這可能會中斷虛擬機器的復原保護狀態。](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx) 
- [啟用保護失敗，因為主機機器上未安裝代理程式](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
- [找不到複本虛擬機器的適當主機 - 因為計算資源不足](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
- [找不到複本虛擬機器的適當主機 - 因為未連接邏輯網路](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
- [無法連接到複本主機機器 - 連線無法建立](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)


### 復原
- VMM 無法完成主機作業 -
    -   [容錯移轉至虛擬機器的所選復原點：一般拒絕存取錯誤。](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
    -   [Hyper-V 無法容錯移轉至虛擬機器的所選復原點：作業中止。請嘗試較新的復原點。(0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
    -   無法建立與伺服器的連線。(0x00002EFD)
        -   [Hyper-V 無法啟用虛擬機器的反向複寫](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
        -   [Hyper-V 無法啟用虛擬機器的複寫](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
    -   [無法認可虛擬機器的容錯移轉](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
-   [復原計劃包含未準備好進行規劃之容錯移轉的虛擬機器](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
-   [虛擬機器未準備好進行規劃的容錯移轉](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   [虛擬機器並未執行，而且未關閉電源](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
-   [虛擬機器上發生頻外作業且認可容錯移轉失敗](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   Test Failover
    -   [無法起始容錯移轉，因為正在進行測試容錯移轉](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)

### 設定伺服器、處理序伺服器、主要目標
設定伺服器 (CS)、處理序伺服器 (PS)、主要目標 (MT) - [將 PS/CS 做為 VM 加以管理的 ESXi 主機失敗，並且出現紫色的當機畫面。](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### 容錯移轉之後的遠端桌面疑難排解
-   許多客戶面臨在 Azure 中連接到容錯移轉的 VM 的問題。[使用疑難排解文件 RDP 至 VM](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

<!---HONumber=Sept15_HO3-->