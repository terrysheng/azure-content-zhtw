<properties
	pageTitle="針對 Site Recovery 中的虛擬機器和實體伺服器保護規劃容量 | Microsoft Azure"
	description="Azure Site Recovery 可將內部部署上虛擬機器和實體伺服器的複寫、容錯移轉及復原協調至 Azure 或次要內部部署站台。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="12/07/2015" 
	ms.author="raynew"/>

# 針對 Azure Site Recovery 中的虛擬機器和實體伺服器保護規劃容量

容量規劃工具可協助您釐清使用 Azure Site Recovery 保護 Hyper-V VM、VMware VM 及 Windows/Linux 實體伺服器的容量需求。


## 概觀

使用工具來分析您的來源環境和工作負載，並且釐清您在來源位置所需的頻寬需求、伺服器資源，以及在目標位置所需的資源 (虛擬機器和儲存體等等)。

有幾種模式可讓您執行工具：

- **快速規劃**：在此模式中執行工具，根據 VM、磁碟、儲存體及變更率的平均數，取得網路與伺服器的預測。
- **詳細規劃**：在此模式中執行工具，提供每個工作負載的 VM 層級詳細資料。分析 VM 相容性，並取得網路和伺服器預測。

## 開始之前

在您執行工具之前：

1. 收集您的環境的資訊，包括 VM、每個 VM 的磁碟、每個磁碟的儲存體。
2. 識別複寫資料的每日變更 (流失) 率。作法：

	- 如果您是複寫 Hyper-V VM，請下載 [Hyper-V 容量規劃工具](https://www.microsoft.com/download/details.aspx?id=39057)來取得變更率。[深入了解](site-recovery-capacity-planning-for-hyper-v-replication.md)此工具。我們建議您執行此工具一週以上的時間來擷取平均值。
	- 如果您是複寫 VMware 虛擬機器，請使用 [vSphere 容量規劃應用裝置](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)來釐清流失率。
	- 如果您是複寫實體伺服器，則您必須以手動方式進行預估。

## 執行快速規劃
1.	下載 [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) 工具並開啟。您必須執行選取的巨集以在出現提示時，啟用編輯和啟用內容。 
2.	在 [選取規劃類型] 中，從清單方塊中選取 [快速規劃]。

	![開始使用](./media/site-recovery-capacity-planner/getting-started.png)

3.	在 [容量規劃] 工作表中，輸入必要資訊。您必須填妥以下螢幕擷取畫面中以紅色圈出來的所有欄位。

	- 在 [選取您的案例] 中，選擇 [Hyper-V 到 Azure] 或 [VMware/實體到 Azure]。
	- 在 [平均每日資料變更率] 中，放入您使用 [Hyper-V 容量規劃工具](site-recovery-capacity-planning-for-hyper-v-replication.md)或 [vSphere 容量規劃應用裝置](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)收集的資訊。  
	- [壓縮] 設定只適用於將 VMware VM 或實體伺服器複寫到 Azure 時所提供的壓縮。我們預估為 30% 以上。視需要修改設定。對於將 Hyper-V VM 複寫到 Azure 的壓縮，您可以使用協力廠商應用裝置，例如 Riverbed。 
	-  在 [保留期] 中，指定保留複本的時間長度。如果您是複寫 VMware 或實體伺服器，以天為單位輸入值。如果您是複寫 Hyper-V，以小時為單位指定時間。
	-  在 [應該完成虛擬機器批次初始複寫的時數] 與 [每一初始複寫批次的虛擬機器數] 中，您輸入的設定是用於計算初始複寫需求。部署 Site Recovery 時，應該上傳整個初始資料集。 

	![輸入](./media/site-recovery-capacity-planner/inputs.png)

2.	您填入來源環境的值之後，顯示的輸出會包含：

	- 網路頻寬需求：
		- **差異複寫所需的頻寬** (MB/秒)。這會根據平均每日資料變更率計算得出。
		- **初始複寫所需的頻寬** (MB/秒)。這是以您填入的初始複寫值計算得出。 
	- Azure 需求：提供 Azure 中所需的儲存體、IOPS、儲存體帳戶及磁碟的詳細資料。
		- **所需的儲存體 (以 GB 為單位)** 是所需的 Azure 儲存體總計。
		- **標準儲存體帳戶上的 IOPS 總數**是根據標準儲存體帳戶總計上的 8K IOPS 單位大小計算。針對快速規劃，數目是根據來源 VM 磁碟和每日資料變更率計算。針對詳細規劃，數目是根據對應到標準 Azure VM 的 VM 數，以及那些 VM 上的資料變更率計算。 
		- **標準儲存體帳戶數目**提供保護 VM 所需的標準儲存體帳戶總數。請注意，標準儲存體帳戶跨標準儲存體的所有 VM 可以保存多達 20000 個 IOPS，每個磁碟最多支援 500 個 IOPS。 
		- **所需的 Blob 磁碟數目**提供將在 Azure 儲存體上建立的磁碟數目。
		- **進階儲存體帳戶數目**提供保護 VM 所需的進階儲存體帳戶總數。請注意，具有高 IOPS (超過 20000) 的來源 VM 需要進階儲存體帳戶。進階儲存體帳戶可以保存多達 80000 個 IOPS。
		- **進階儲存體帳戶上的 IOPS 總數**是根據進階儲存體帳戶總計上的 256K IOPS 單位大小計算。針對快速規劃，數目是根據來源 VM 磁碟和每日資料變更率計算。針對詳細規劃，數目是根據對應到進階 Azure VM (DS 和 GS 系列) 的 VM 數，以及那些 VM 上的資料變更率計算。 

	- 其他基礎結構需求：
		- 包括 VMware/實體複寫到 Azure 的額外需求，包含組態和處理序伺服器。
		- 所需的額外處理序伺服器數目：顯示是否需要額外的處理序伺服器。也就是在組態伺服器預設設定以外的處理序伺服器。N
		- 包括 Hyper-V 到 Azure 複寫中的額外需求，例如來源是否需要額外儲存體。
			
	![輸出](./media/site-recovery-capacity-planner/output.png)
 
## 執行詳細規劃


1.	下載 [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) 工具並開啟。您必須執行選取的巨集以在出現提示時，啟用編輯和啟用內容。 
2.	在 [選取規劃類型] 中，從清單方塊中選取 [詳細規劃]。

	![開始使用](./media/site-recovery-capacity-planner/getting-started-2.png)

3.	在 [工作負載限定性條件] 工作表中，輸入必要資訊。您必須填寫所有標示的欄位。

	- 在 [處理器核心] 中，提供來源伺服器的核心總數。
	- 在 [記憶體配置 (MB)] 中，指定來源伺服器的 RAM 大小。 
	- 在 [NIC 數] 中，指定來源伺服器上的網路介面卡數目。 
	-  在 [總儲存體 (GB)] 中，指定 VM 儲存體的總大小。例如，如果來源伺服器有 3 個磁碟，每一個磁碟有 500 GB，則總儲存體大小為 1500 GB。
	-  在 [連接的磁碟數] 中，指定來源伺服器的磁碟總數。
	-  在 [磁碟容量使用率] 中，指定平均使用率。
	-  在 [每日變更率 (%)] 中，指定來源伺服器的每日資料變更率。
	-  在 [對應 Azure 大小] 中，輸入您想要對應的 Azure VM 大小。如果您不想要手動執行此動作，則按一下 **[計算 IaaS VM]**。

4.	如果您按一下 [計算 IaaS VM]，它會執行動作如下：

	- 驗證您的輸入，並且取得符合適用於複寫到 Azure 的每個 VM 的最佳 Azure VM。如果無法偵測適當的 Azure VM 大小，則會發出錯誤。例如，如果連接的磁碟數為 65 則會發出錯誤，因為 Azure VM 的最大大小為 64。
	- 建議可用於 Azure VM 的儲存體帳戶。
	- 計算工作負載所需的標準儲存體帳戶與進階儲存體帳戶總數。在右邊向下捲動以檢視可用於來源伺服器的 Azure 儲存體類型與儲存體帳戶
	- 根據為 VM 指派的必要儲存體類型 (標準或進階)，以及連接的磁碟數，完成並排序資料表的其餘部分。對於符合備份至 Azure 的需求的所有 VM，資料行 A (VM 是否合格？) 會顯示 [是]。如果 VM 無法備份到 Azure，則會顯示錯誤。

	![工作負載限定性條件](./media/site-recovery-capacity-planner/workload-qualification.png)


- **範例**：對於具有資料表中顯示的值的六個 VM，此工具會計算並指派最佳的 Azure VM 相符項目，以及 Azure 儲存體需求。

	![工作負載限定性條件](./media/site-recovery-capacity-planner/workload-qualification-2.png)

- 在此範例輸出中，請注意下列項目：
	- 五個 VM 需要兩個標準儲存體帳戶和一個進階儲存體帳戶。 
	-  VM3 不適合保護，因為一或多個磁碟超過 1 TB。
	-  VM1 和 VM2 可以使用第一個標準儲存體帳戶。
	-  VM4 可以使用第二個標準儲存體帳戶。
	-  VM5 和 VM6 需要進階儲存體帳戶，兩者都可以使用單一帳戶。

	![工作負載限定性條件](./media/site-recovery-capacity-planner/workload-qualification-3.png)

	>[AZURE.NOTE]儲存體的 IOPS 是在 VM 層級進行計算，不是在磁碟層級。如果來源 VM 的其中一個磁碟的 IOPS 大於 500，但是 VM 的 IOPS 總計是在支援的標準 Azure VM 範圍內，(所有其他值，例如磁碟數、NIC 數、CPU 核心數、記憶體大小都在標準 VM 限制內)，則此工具會挑選標準 VM，而不是進階儲存體。您必須以適當的 DS 或 GS 系列 VM 手動更新 [對應的 Azure 大小] 儲存格。

	>[AZURE.NOTE]第一個資料行是 VM、磁碟及變換的驗證資料行。儲存體的 IOPS 是在 VM 層級進行計算，不是在磁碟層級。如果來源 VM 的其中一個磁碟的 IOPS 大於 500，但是 VM 的 IOPS 總計是在支援的標準 Azure VM 範圍內，(所有其他值，例如磁碟數、NIC 數、CPU 核心數、記憶體大小都在標準 VM 限制內)，則此工具會挑選標準 VM，而不是進階儲存體。您必須以適當的 DS 或 GS 系列 VM 手動更新 [對應的 Azure 大小] 儲存格。

5. 所有詳細資料已就緒之後，請按一下 [將資料提交至規劃工具] 以開啟 [容量規劃工具]。工作負載會反白顯示以顯示是否適合保護。


### 執行容量規劃

1.	填寫 [容量規劃] 工作表。「工作負載」這個字出現在基礎輸入來源資料格，以顯示輸入 [工作負載限定性條件] 工作表。  
2.	修改您想要變更的任何設定，然後按一下 [將資料提交至規劃工具]。 

	![容量規劃](./media/site-recovery-capacity-planner/capacity-planner.png)

<!---HONumber=AcomDC_1210_2015-->