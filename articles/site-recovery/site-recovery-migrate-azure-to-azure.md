<properties
	pageTitle="使用 Site Recovery，將 Azure IaaS 虛擬機器從一個 Azure 區域移轉到另一個區域 | Microsoft Azure"
	description="使用 Azure Site Recovery，將 Azure IaaS 虛擬機器從一個 Azure 區域移轉到另一個區域。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="raynew"/>

#  使用 Site Recovery 在 Azure 區域之間移轉 Azure IaaS 虛擬機器


## 概觀

Azure Site Recovery 可在一些部署案例中協調虛擬機器的複寫、容錯移轉及復原，為您的商務持續性與災害復原 (BCDR) 做出貢獻。如需完整的部署案例清單，請參閱 [Azure Site Recovery 概觀](site-recovery-overview.md)。

本文說明如何使用 Site Recovery，將 Azure IaaS 虛擬機器從一個 Azure 區域移轉到另一個區域。本文使用[設定在內部部署 VMware 虛擬機器或實體伺服器與 Azure 之間的保護](site-recovery-vmware-to-azure.md)中所述的大部分步驟。如需部署中每個步驟的詳細指示，建議您閱讀該篇文章。

## 開始使用

以下是您在開始之前的必要條件：

- **組態伺服器**：做為組態伺服器的 Azure 虛擬機器。組態伺服器會協調內部部署機器與 Azure 伺服器之間的通訊。
- **主要目標伺服器**：做為主要目標伺服器的 Azure 虛擬機器。這部伺服器會接收並保留受保護機器中的複寫資料。
- **處理序伺服器**：執行 Windows Server 2012 R2 的虛擬機器。受保護的虛擬機器會將複寫資料傳送至這部伺服器。
- **IaaS 虛擬機器**：您想要移轉的 VM。

- 如需這些元件的詳細資訊，請參閱[我需要什麼？](site-recovery-vmware-to-azure.md#what-do-i-need)
- 您也應該閱讀[容量規劃](site-recovery-vmware-to-azure.md#capacity-planning)的方針，並確定具有所有[部署必要條件](site-recovery-vmware-to-azure.md#before-you-start)，再開始進行。

## 部署步驟

1. [建立保存庫](site-recovery-vmware-to-azure.md/#step-1-create-a-vault)
2. [部署組態伺服器](site-recovery-vmware-to-azure.md#step-2-deploy-a-configuration-server)做為 Azure VM。
3. [部署主要目標伺服器](site-recovery-vmware-to-azure.md#step-2-deploy-a-configuration-server)做為 Azure VM。
4. [部署處理序伺服器](site-recovery-vmware-to-azure.md#step-4-deploy-the-on-premises-process-server)。請注意：

	- 處理序伺服器應該部署在相同的虛擬網路/子網路，當做要移轉的 IaaS VM。![IaaS VM](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure1.png)

	- 部署處理序伺服器之後，請驗證該伺服器能否與要移轉的虛擬機器通訊。
	- 所有要保護的 VM 都需要安裝行動服務。這項服務會將資料傳送至處理序伺服器。行動服務可以手動方式安裝，或在為 VM 啟用保護時由處理序伺服器自動推入安裝。要移轉的 IaaS 虛擬機器的防火牆規則應該設定為允許推入安裝這項服務。 

	- 部署處理序伺服器並使用 Site Recovery 保存庫中的組態伺服器註冊之後，該伺服器應該會顯示在 Site Recovery 主控台的 [組態伺服器] 索引標籤下。請注意，這項作業最多需要 15 分鐘。
	
		![處理序伺服器](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure2.png)

5. [安裝最新的更新](site-recovery-vmware-to-azure.md#step-5-install-latest-updates)。確定您安裝的所有元件伺服器都處於最新狀態。
6. [建立保護群組](site-recovery-vmware-to-azure.md#step-7-create-a-protection-group)。您需要設定保護群組，才能開始使用 Site Recovery 保護移轉的虛擬機器。您可以指定群組的複寫設定，這些設定會套用至您加入該群組的所有機器。 
7. [設定虛擬機器](site-recovery-vmware-to-azure.md#step-8-set-up-machines-you-want-to-protect)。您需要在每部 VM 上 (自動或手動) 安裝行動服務。
8. [步驟 8：對虛擬機器啟用保護](site-recovery-vmware-to-azure.md#step-9-enable-protection)。您可以將 VM 加入保護群組，為執行個體啟用保護。請注意：

	- 您可以使用虛擬機器的私人 IP 位址，探索要移轉至 Azure 的 IaaS 虛擬機器。這個位址可在 Azure 的虛擬機器儀表板上找到。
	-  在您建立保護群組的索引標籤上，按一下 [加入機器] > [實體機器] ![EC2 探索](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure3.png)
	- 指定虛擬機器的私人 IP 位址。
		- ![EC2 探索](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure4.png)
	- 系統會啟用保護，並根據保護群組的初始複寫設定來執行初始複寫。
9. [步驟 9：執行非計劃性容錯移轉](site-recovery-failover.md#run-an-unplanned-failover)。初始複寫完成之後，您可以執行從一個 Azure 區域到另一個區域的非計劃性容錯移轉。(選擇性) 您可以建立復原計劃並執行非計劃性容錯移轉，在區域與區域之間移轉多部虛擬機器。[深入了解](site-recovery-create-recovery-plans.md)復原計劃。
		
## 後續步驟

在 [Site Recovery 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見或問題

<!---HONumber=AcomDC_1217_2015-->