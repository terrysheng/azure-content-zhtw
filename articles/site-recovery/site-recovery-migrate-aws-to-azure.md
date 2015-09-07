<properties
	pageTitle="將 Windows 虛擬機器從 Amazon Web Services 移轉至 Microsoft Azure"
	description="使用 Azure Site Recovery 將 Amazon Web Services (AWA) 中執行的 Windows 虛擬機器移轉至 Azure。"
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
	ms.workload="backup-recovery"
	ms.date="08/26/2015"
	ms.author="raynew"/>

#  將 Amazon Web Services (AWS) 中的 Windows 虛擬機器移轉至 Azure


## 概觀

Azure Site Recovery 可在許多部署中協調虛擬機器的複寫、容錯移轉及復原，為您的商務持續性與災害復原 (BCDR) 做出貢獻。如需完整的部署案例清單，請參閱 [Azure Site Recovery 概觀](site-recovery-overview.md)。

本文說明如何使用 Site Recovery，將 AWS 中執行的 Windows 執行個體移轉或容錯移轉至 Azure。本文使用[設定在內部部署 VMware 虛擬機器或實體伺服器與 Azure 之間的保護](site-recovery-vmware-to-azure.md)中所述的大部分步驟。如需部署中每個步驟的詳細指示，建議您閱讀該篇文章。

## 開始使用

以下是您在開始之前的必要條件：

- **組態伺服器**：做為組態伺服器的 Azure 虛擬機器。組態伺服器會協調內部部署機器與 Azure 伺服器之間的通訊。
- **主要目標伺服器**：做為主要目標伺服器的 Azure 虛擬機器。這部伺服器會接收並保留受保護機器中的複寫資料。
- **處理序伺服器**：執行 Windows Server 2012 R2 的虛擬機器。受保護的虛擬機器會將複寫資料傳送至這部伺服器。
- **EC2 VM 執行個體**：您要移轉後加以保護的執行個體。

- 如需這些元件的詳細資訊，請參閱[我需要什麼？](site-recovery-vmware-to-azure.md#what-do-i-need)
- 您也應該閱讀[容量規劃](site-recovery-vmware-to-azure.md#capacity-planning)的方針，並確定具有所有[部署必要條件](site-recovery-vmware-to-azure.md#before-you-start)，再開始進行。

## 部署步驟

1. [建立保存庫](site-recovery-vmware-to-azure.md#step-1-create-a-vault)
2. [部署組態伺服器](site-recovery-vmware-to-azure.md#step-2-deploy-a-configuration-server)做為 Azure VM。
3. [部署主要目標伺服器](site-recovery-vmware-to-azure.md#step-2-deploy-a-configuration-server)做為 Azure VM。
4. [部署處理序伺服器](site-recovery-vmware-to-azure.md#step-4-deploy-the-on-premises-process-server)。請注意：

	- 您應該在與 EC2 執行個體相同的子網路/Amazon 虛擬私人雲端上部署處理序伺服器。![EC2 執行個體](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration1.png)

	- 部署處理序伺服器之後，請驗證該伺服器否能夠與您要移轉的 EC2 執行個體通訊。
	- 所有要保護的 VM 都需要安裝行動服務。這項服務會將資料傳送至處理序伺服器。行動服務可以手動方式安裝，或在為 VM 啟用保護時由處理序伺服器自動推入安裝。您要移轉的 EC2 執行個體上的防火牆規則應該設定為允許推入安裝這項服務。EC2 執行個體的安全性群組應該具有下列規則：

		![防火牆規則](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration2.png)

	- 部署處理序伺服器並使用 Site Recovery 保存庫中的組態伺服器註冊之後，該伺服器應該會顯示在 Site Recovery 主控台的 [組態伺服器] 索引標籤下。這項作業最多需要 15 分鐘。
	
		![處理序伺服器](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration3.png)

5. [安裝最新的更新](site-recovery-vmware-to-azure.md#step-5-install-latest-updates)。確定您安裝的所有元件伺服器都處於最新狀態。
6. [建立保護群組](site-recovery-vmware-to-azure.md#step-7-create-a-protection-group)。您需要設定保護群組，才能開始使用 Site Recovery 保護移轉的執行個體。您可以指定群組的複寫設定，這些設定會套用至您加入該群組的所有執行個體。 
7. [設定虛擬機器](site-recovery-vmware-to-azure.md#step-8-set-up-machines-you-want-to-protect)。您需要在每個執行個體上 (自動或手動) 安裝行動服務。
8. [為虛擬機器啟用保護](site-recovery-vmware-to-azure.md#step-9-enable-protection)。您可以將執行個體加入保護群組，為執行個體啟用保護。請注意：

	- 您可以使用可從 EC2 主控台取得之 EC2 執行個體的私人 IP 位址，探索要移轉至 Azure 的 EC2 執行個體。
	-  在您建立保護群組的索引標籤上，按一下 [加入機器] > [實體機器] ![EC2 探索](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration4.png)
	- 指定執行個體的私人 IP 位址。
		- ![EC2 探索](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration5.png)
	- 系統會啟用保護，並根據保護群組的初始複寫設定來執行初始複寫。
9. [執行未規劃的容錯移轉](site-recovery-failover.md#run-an-unplanned-failover)。初始複寫完成之後，您可以執行從 AWS 到 Azure 之未規劃的容錯移轉。(選擇性) 您可以建立復原計劃並執行未規劃的容錯移轉，將多部虛擬機器從 AWS 移轉至 Azure。[深入了解](site-recovery-create-recovery-plans.md)復原計劃。
		
## 後續步驟

在 [Site Recovery 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見或問題

<!---HONumber=August15_HO9-->