<properties
	pageTitle="何謂 Azure 備份中心？ | Microsoft Azure"
	description="您可以使用 Azure 備份和復原服務，從 Windows 伺服器、 Windows 用戶端電腦、 SCDPM 伺服器或 Azure 虛擬機器備份和還原資料與應用程式。"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="aashishr"; "trinadhk"; "jimpark"/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/17/2015" ms.author="aashishr"; "trinadhk"; "jimpark"/>

# 何謂 Azure 備份？
Azure 備份是一個多租用戶的 Azure 服務，可讓您備份與還原內部部署或 Azure 中的資料。它將以一個可靠、安全及具成本競爭力的雲端備份解決方案，取代您現有的內部部署或異地備份解決方案。同時也提供保護雲端執行資產的彈性。Azure 備份是以可調整、持久和高可用性的世界級基礎結構為建置基礎。您可以使用此解決方案，從 System Center Data Protection Manager (SCDPM) 伺服器、Windows 伺服器、Windows 用戶端機器或 Azure Iaas 虛擬機器，備份資料與應用程式。Azure 備份和 SCDPM 是構成 Microsoft 雲端整合式備份解決方案的基礎技術。

> [AZURE.VIDEO what-is-azure-backup]

## 雲端設計點
傳統的備份解決方案已演變成將雲端視為類似於磁碟或磁帶的端點。雖然這種方法簡單、容易部署並能提供一致的使用經驗，但除使用上受限，亦無法充分利用基礎平台。這對一般客戶而言即是效率不佳又昂貴的解決方案。若把 Azure 當作「只是一個儲存體端點」，備份解決方案將無法挖掘豐富和強大的公用雲端平台。相反地，Azure 備份提供真正的服務，也就是使用雲端來提供功能強大及價格合理的備份解決方案。其整合了內部部署備份解決方案 (SCDPM)，以提供端對端混合式解決方案。

這種方法的優點如下：

- 有效率的雲端儲存體架構，可以提供低成本及彈性的資料儲存體
- 不會造成干擾且會自動調整的服務，保證有高可用性
- 使用一致的方式在內部部署、混合式和 IaaS 部署中進行備份

此解決方案的主要功能如下：

1. **可靠的服務**：採用 Azure 備份，您將取得高可用性的備份服務。服務為多租用戶，所以您不必擔心基礎計算或儲存體的管理。

2. **可靠的儲存體**：Azure 備份是以可靠的雲端儲存體為基礎，並受高度 SLA 支援。您不必擔心維護儲存體的高資本或作業費用。此外，您可以選擇備份至 LRS (本地備援儲存體) 或 GRS (異地複寫儲存體) 儲存體。LRS 可在相同地區中提供 3 份資料複本以預防本機電腦的硬體故障；GRS 則在已配對的資料中心再額外提供 3 份複本 (總計 6 份複本)。這將確保備份資料具高可用性。即使發生 Azure 網站層級損毀，備份資料仍非常安全。

3. **安全**：Azure 備份的資料在來源中和傳送時皆為加密，並加密儲存在 Azure 中。加密金鑰會儲存在來源，並且永遠不會傳送至或儲存在 Azure 中。還原任何資料皆需要加密金鑰，而只有客戶在服務中擁有資料的完整存取權。

4. **異地保護**：與其為異地磁帶備份解決方案付費，客戶可以備份至提供令人信服且與磁帶相似語意解決方案的 Azure，而且所需成本非常低。

5. **簡化**：Azure 備份提供熟悉的介面，並可調整以保護任何大小的部署。隨著服務的演進，功能如中央管理將可讓您從單一位置管理您的備份基礎結構。

6. **符合成本效益**：Azure 備份定價包含每次執行個體備份的管理費用，以及在 Azure 上耗用的儲存體 (區塊 Blob 價格) 成本。不同於其他雲端架構備份優惠，Azure 備份並不向客戶收取任何還原作業費用。此外，亦不會向客戶收取還原作業期間的資料傳送出口流量 (輸出) 費用。

7. **在雲端中備份**：Azure 備份能對執行的 Azure IaaS 虛擬機器提供 VSS 型應用程式一致備份，而不需要關閉虛擬機器。它也可以使用檔案系統一致性備份 Azure 中的 Linux 虛擬機器。


## 部署案例
| 元件 | 可以在 Azure 中部署嗎？ | 可以在內部部署嗎？ | 支援的目標儲存體|
| --- | --- | --- | --- |
| Azure 備份代理程式 | **是** <br><br>您可以在 Azure 中執行的任何 Windows Server VM 上部署Azure 備份代理程式。 | **是** <br><br>您可以在 Windows Server VM 或實體機器上部署Azure 備份代理程式。 | Azure 備份保存庫 |
| System Center Data Protection Manager (SCDPM) | **是** <br><br>深入了解[使用 SCDPM 保護 Azure 中的工作負載](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx) (英文)。 | **是** <br><br>深入了解[保護工作負載和資料中心內的 VM](https://technet.microsoft.com/zh-TW/library/hh758173.aspx) (英文)。 | 本機連接的磁碟、<br>Azure 備份保存庫、<br>磁帶 (僅限內部部署) |
| Azure 備份 (VM 延伸模組) | **是** <br><br>適用於 [Azure IaaS 虛擬機器備份](backup-azure-vms-introduction.md)的特殊元件。 | **否** <br><br>請使用 SCDPM 備份資料中心內的虛擬機器。 | Azure 備份保存庫 |


## 應用程式和工作負載

| 工作負載 | 來源機器 | Azure 備份方案 |
| --- | --- |---|
| 檔案與資料夾 | Windows Server | [Azure 備份代理程式](backup-configure-vault.md)、<br> [System Center DPM](backup-azure-dpm-introduction.md) |
| 檔案與資料夾 | Windows 用戶端 | [Azure 備份代理程式](backup-configure-vault.md)、<br> [System Center DPM](backup-azure-dpm-introduction.md) |
| Hyper-V 虛擬機器 (Windows) | Windows Server | System Center DPM |
| Hyper-V 虛擬機器 (Linux) | Windows Server | System Center DPM |
| Microsoft SQL Server | Windows Server | [System Center DPM](backup-azure-backup-sql.md) |
| Microsoft SharePoint | Windows Server | [System Center DPM](backup-azure-backup-sharepoint.md) |
| Microsoft Exchange | Windows Server | System Center DPM |
| Azure IaaS VM (Windows)| - | [Azure 備份 (VM 延伸模組)](backup-azure-vms-introduction.md) |
| Azure IaaS VM (Linux) | - | [Azure 備份 (VM 延伸模組)](backup-azure-vms-introduction.md) |


## 後續步驟
- [試用 Azure 備份](backup-try-azure-backup-in-10-mins.md)
- Azure 備份服務的常見問題集在[這裡](backup-azure-backup-faq.md)列出。
- 造訪 [Azure 備份論壇](http://go.microsoft.com/fwlink/p/?LinkId=290933) (英文)。

<!---HONumber=Nov15_HO4-->