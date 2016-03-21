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
	ms.date="02/22/2016"
	ms.author="raynew"/>

#  使用 Site Recovery 在 Azure 區域之間移轉 Azure IaaS 虛擬機器

Azure Site Recovery 服務可藉由協調虛擬機器與實體伺服器的複寫、容錯移轉及復原 (BCDR) 策略，為您的商務持續性與災害復原做出貢獻。機器可以複寫至 Azure，或次要的內部部署資料中心。如需快速概觀，請參閱[什麼是 Azure Site Recovery？](site-recovery-overview.md)。

## 概觀

本文說明如何使用 Site Recovery，將 AWS 中執行的 Windows 執行個體移轉或容錯移轉至 Azure。其中彙總[複寫 VMware 虛擬機器或實體伺服器至 Azure](site-recovery-vmware-to-azure-classic.md) 中所完整描述的步驟。此連結的文章是會將 VMware VM 或 Windows/Linux 實體伺服器複寫到 Azure 之案例的最新增強版本。如需部署中每個步驟的詳細指示，建議您遵循該篇連結文章。

>[AZURE.NOTE] 如需在區域之間移轉，您不應該再使用[舊版文件](site-recovery-vmware-to-azure-classic-legacy.md)中的指示。

## 開始使用

以下是您在開始之前的必要條件：

- 管理伺服器︰執行 Windows Server 2012 R2 作為管理伺服器的內部部署 VM。您可在此伺服器上安裝 Site Recovery 元件 (包括組態伺服器和處理序伺服器)。深入了解[管理伺服器的考量](site-recovery-vmware-to-azure-classic.md#management-server-considerations)和[內部部署的必要條件](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites)。
- **IaaS 虛擬機器**：您想要移轉的 VM。

## 部署步驟

1. [建立保存庫](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault)。
2. [部署管理伺服器](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server)。
3. 部署管理伺服器之後，請驗證該伺服器否能夠與您要移轉的 VM 通訊。
4. [建立保護群組](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group)。保護群組包含受保護的機器，它們共用相同的複寫設定。您可以指定群組的複寫設定，這些設定會套用至您加入該群組的所有機器。
5. [安裝行動服務](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service)。所有要保護的 VM 都需要安裝行動服務。這項服務會將資料傳送至處理序伺服器。行動服務可以手動方式安裝，或在為 VM 啟用保護時由處理序伺服器自動推入安裝。請注意，要移轉之 IaaS 虛擬機器的防火牆規則應該設定為允許推入安裝這項服務。
6. [為機器啟用保護](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine)。將您想要保護的電腦加入複寫群組中。 
7. 您可以使用虛擬機器的私人 IP 位址，探索要移轉至 Azure 的 IaaS 虛擬機器。這個位址可在 Azure 的虛擬機器儀表板上找到。
8. 在您建立保護群組的索引標籤上，按一下 [加入機器] > [實體機器]。

	![EC2 探索](./media/site-recovery-migrate-azure-to-azure/migrate-add-machines.png)

9. 指定虛擬機器的私人 IP 位址。

	![EC2 探索](./media/site-recovery-migrate-azure-to-azure/migrate-machine-ip.png)
	
將機器加入群組中之後，將啟用保護，而且初始複寫將根據保護群組設定執行。

10. [執行未規劃的容錯移轉](site-recovery-failover.md#run-an-unplanned-failover)。初始複寫完成之後，您可以執行從一個 Azure 區域到另一個區域的非計劃性容錯移轉。(選擇性) 您可以建立復原計劃並執行非計劃性容錯移轉，在區域與區域之間移轉多部虛擬機器。[深入了解](site-recovery-create-recovery-plans.md)復原計劃。
		
## 後續步驟

在 [Site Recovery 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見或問題

<!---HONumber=AcomDC_0309_2016-->