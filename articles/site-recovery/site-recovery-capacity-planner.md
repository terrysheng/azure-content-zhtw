<properties
	pageTitle="Site Recovery Capacity Planner | Microsoft Azure" 
	description="Azure Site Recovery 可將內部部署上虛擬機器和實體伺服器的複寫、容錯移轉及復原協調至 Azure 或次要內部部署站台。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="11/27/2015" 
	ms.author="pratshar"/>

# Site Recovery Capacity Planner

本文件說明如何使用 Microsoft ASR 容量規劃工具，其中提供與為了順暢執行 Site Recovery 作業而需佈建的資源相關的指引。容量規劃工具可用來分析來源環境 (工作負載)、頻寬需求、目標上的資源需求 (VM、儲存體)，以及來源端所需的任何額外伺服器資源 (SC VMM、組態伺服器、處理序伺服器)。下載 [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) 工具
 
容量規劃有兩種使用模式：
 
- **Quick planning** (快速規劃)：根據 VM、磁碟、儲存體及變更率的平均數取得網路與伺服器的預測。 
- **Detailed planning** (詳細規劃)：提供每個工作負載的 VM 層級詳細資料。分析 VM 層級的相容性，同時也取得網路與伺服器的預測。
     
本文假設使用者已熟悉 Azure Site Recovery。請參閱 [Azure Site Recovery 概觀](site-recovery-overview.md)

## 開始使用
###必要條件
繼續進行所需的詳細資料會依您想要使用的規劃模式而有所不同。除了 VM 數、 每一 VM 的磁碟數、每一磁碟的儲存體大小等基礎結構詳細資料之外，還需要一些其他詳細資料。關鍵的詳細資料是每日變更率或變換率。如果來源環境是 Hyper-V，請使用 [Hyper-V 容量規劃工具](https://www.microsoft.com/en-in/download/details.aspx?id=39057)來取得變換率。請閱讀指示以使用 [Hyper-V 容量規劃工具](site-recovery-capacity-planning-for-hyper-v-replication.md)。如果是 VMWare，請使用 [VMware 容量規劃設備工具](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)

##快速規劃
1.	開啟 **ASR Capacity Planner.xlsm** 檔案。這需要執行巨集。因此，出現提示時，請 [啟用編輯] 和 [啟用內容] 
1.	從清單方塊中選取 [Quick Planner] (快速規劃)。這會開啟標題為 **Capacity Planner** (容量規劃) 的另一個工作表

	![開始使用](./media/site-recovery-capacity-planner/getting-started.png)

1.	在 “Capacity Planner” (容量規劃) 工作表中，視需要輸入輸入值。所有圈選的欄位都是必須輸入的欄位。
	1.	[Select your scenario] (選取您的案例) 清單方塊是一個可供您在 [Hyper-V to Azure] (Hyper-V 到 Azure) 與 [VMware/Physical to Azure] (VMware/實體到 Azure) 之間變更來源環境的清單方塊。
	1. 	[Average Daily data change rate] (平均每日資料變更率) 需要測量。在 Hyper-V 環境中，可以使用 Hyper-V 容量規劃工具。如果是 VMWare，則可以使用 VMWare 容量規劃工具。通常建議您至少讓工具執行一週，以便可以擷取任何尖峰值並算出平均值。 
	1. 	Compression (壓縮) – 這是 ASR 在 VMWare/Physical to Azure (VMWare/實體到 Azure) 案例中提供的壓縮。在 Hyper-V to Azure (Hyper-V 到 Azure) 中，這可以透過協力廠商設備 (例如 Riverbed) 來達成。 
	1. 在 VMWare/Physical to Azure (VMWare/實體到 Azure) 案例中，應該以天為單位輸入 [Number of retention] (保留數)。在 Hyper-V 案例中，將會以小時為單位輸入此欄位。 
	1. 最後兩個輸入值是用來計算初始複寫 (IR)。部署 ASR 安裝程式時，應該上傳整個初始資料集。[Number of hours in which initial replication for the batch of virtual machines should complete] (應該完成虛擬機器批次初始複寫的時數) 與 [Number of virtual machines per initial replication batch] (每一初始複寫批次的虛擬機器數) - 這是預期要做為輸入值的欄位。另一方面，也可以調整這些數字來調整現有的頻寬。 

	![輸入](./media/site-recovery-capacity-planner/inputs.png)

1. 輸入來源環境的詳細資料之後，所顯示的輸出將會提供指引，其中包括
	1.	網路頻寬需求
		1. [Bandwidth required for delta replication (in Megabits/sec)] (差異複寫所需的頻寬 (Mb/秒))。這會根據平均每日資料變更率計算得出。 
		1. 也會顯示 [Bandwidth required for initial replication (In Megabits/sec)] (初始複寫所需的頻寬 (Mb/秒))。這會根據輸入中顯示的初始複寫輸入值 (最後兩列) 計算得出。 
	1.	Azure 需求
		1. 	本節詳述 Azure 中所需的「儲存體」、IOPS、「儲存體帳戶」及「磁碟」。 
	1. 	其他基礎結構需求 
		1. VMware/Physical to Azure (VMware/實體到 Azure) 案例中的任何額外需求，例如「組態伺服器」與「處理序伺服器」需求。 
		1. 	Hyper-V to Azure (Hyper-V 到 Azure) 案例中的任何額外需求，例如來源上所需的額外儲存體。
			
	![輸出](./media/site-recovery-capacity-planner/output.png)
 
##詳細規劃

1.	開啟 **ASR Capacity Planner.xlsm** 檔案。這需要執行巨集。因此，出現提示時，請 [啟用編輯] 和 [啟用內容] 
1.	從清單方塊中選取 [Detailed Planner] (詳細規劃)。這會開啟標題為 **Workload Qualification** (工作負載限定性條件) 的另一個工作表

	![開始使用](./media/site-recovery-capacity-planner/getting-started-2.png)


1.	在 Workload Qualification (工作負載限定性條件) 工作表中，視需要輸入輸入值。所有以紅色標示的資料行都是必要欄位。其他資料行則是選擇性欄位。
	1.	Processor Cores (處理器核心數)：提供來源伺服器的核心總數。
	1. Memory Allocation in MB (記憶體配置 (MB))：提供來源伺服器的 RAM 大小。
	1.	Number of NICs (NIC 數)：提供來源伺服器的 NIC 數。
	1. Total Storage (in GBs) (總儲存體 (GB))：提供 VM 儲存體的總大小。例如，如果來源伺服器有 3 個磁碟，每一個有 500 GB，則總儲存體大小為 1500 GB。
	1. Number of disks attached (連接的磁碟數)：提供來源伺服器的磁碟總數。
	1. Disk capacity utilization (磁碟容量使用率)：提供平均使用率 
	1. Daily Change rate(%) (每日變更率 (%))：提供來源伺服器的每日資料變更率。
	1. Mapping Azure size (對應的 Azure 大小)：您可以輸入您想要對應的 Azure VM 大小，或使用 [Compute IaaS VMs] (計算 IaaS VM) 按鈕來計算最可能的相符項目。 

	![工作負載限定性條件](./media/site-recovery-capacity-planner/workload-qualification.png)
 

1. 按一下 [Compute IaaS VMs] (計算 IaaS VM) 將會驗證上述輸入值，並建議最可能的 Azure VM 相符項目。如果找不到來源伺服器的適當 Azure VM 大小，就會針對該伺服器產生錯誤。例如，當來源 VM 的 [Number of disks attached] (連接的磁碟數) 是 65 時，它就會產生錯誤，因為最大 Azure VM 可連接的磁碟數上限是 64。


[Compute IaaS VMs] (計算 IaaS VM) 也會計算 VM 需要的是 Azure 標準儲存體帳戶，還是 Azure 進階儲存體帳戶。它還會提供工作負載需要多少標準儲存體帳戶與進階儲存體帳戶的建議。在右邊向下捲動以檢視可用於來源伺服器的 Azure 儲存體類型與儲存體帳戶
 
**範例**：針對具有下列值的 5 個 VM，此工具已計算並指派最佳的 Azure 大小 VM 相符項目，以及建議 VM 需要的是標準儲存體還是進階儲存體

![工作負載限定性條件](./media/site-recovery-capacity-planner/workload-qualification-2.png)

在此範例中，5 個 VM 需要兩個標準儲存體帳戶和一個進階儲存體帳戶。VM1、VM2 可以使用第一個標準儲存體帳戶，而 VM3 可以使用第二個標準儲存體帳戶。VM4 與 VM5 需要進階儲存體帳戶，並且可以納入單一進階儲存體帳戶中。

![工作負載限定性條件](./media/site-recovery-capacity-planner/workload-qualification-3.png)


>[AZURE.NOTE]IOPS 是在 VM 層級進行計算，不是在磁碟層級。如果來源 VM 其中一個磁碟的 IOPS 大於 500，但 VM 的 IOPS 總計是在支援的標準 Azure VM 內，而所有其他值 (磁碟數、NIC 數、CPU 核心數、記憶體大小) 都在標準 VM 限制內，則此工具會挑選標準 VM，而不是進階儲存體。使用者必須以適當的 DS 或 GS 系列 VM 手動更新 [Mapping Azure Size](對應的 Azure 大小) 儲存格。


1.	第一個資料行是 VM、磁碟及變換的驗證資料行。 
1.	所有詳細資料就定位之後，請按頂端的 [Submit data to the planner tool] (提交資料給規劃工具) 按鈕。這會開啟 **Capacity Planner** (容量規劃) 工作表，其中會自動填入平均值，如下圖所示。 
1.	此動作也會醒目提示哪些工作負載可供保護，哪些無法保護。


###容量規劃

1.	在 **Capacity Planner** (容量規劃) 工作表中，第一個資料列 [Infra Inputs source] (基礎結構輸入來源) 中的 [Workload] (工作負載) 暗示輸入資訊是從 **Workload Qualification** (工作負載限定性條件) 工作表填入。  
1.	需要進行變更時，請在 **Workload Qualification** (工作負載限定性條件) 工作表中進行必要的變更，然後按 [Submit Data To the planner tool] (提交資料給規劃工具) 按鈕。 

	![容量規劃](./media/site-recovery-capacity-planner/capacity-planner.png)

<!---HONumber=AcomDC_1203_2015-->