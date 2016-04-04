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
	ms.date="03/16/2016"
	ms.author="raynew"/>

#  使用 Azure Site Recovery 在 Azure 區域之間移轉 Azure IaaS 虛擬機器

## 概觀

本文說明如何使用 Site Recovery，在 Azure 區域之間移轉 Azure VM。開始之前，請注意：

- 您只能在這個階段移轉。這表示您可以將 VM 從一個 Azure 區域容錯移轉至另一個，但是無法將它們容錯移轉回來。
- 本文摘要說明並使用[複寫 VMware 虛擬機器或實體伺服器至 Azure](site-recovery-vmware-to-azure-classic.md) 一文中完整描述的許多步驟，該文提供設定複寫的最新增強指示。我們建議您遵循這份文件，以取得移轉時的詳細指示。
- **您不應該再使用**[舊版文件](site-recovery-vmware-to-azure-classic-legacy.md)中的指示。

在這篇文章下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。


## 必要條件

以下是您針對此部署所需要的項目︰

- **管理伺服器**︰執行 Windows Server 2012 R2 作為管理伺服器的內部部署 VM。您可在此伺服器上安裝 Site Recovery 元件 (包括組態伺服器和處理序伺服器)。深入了解[管理伺服器考量](site-recovery-vmware-to-azure-classic.md#management-server-considerations)和[內部部署必要條件](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites)。
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

10. [執行非計劃性容錯移轉](site-recovery-failover.md#run-an-unplanned-failover)。初始複寫完成之後，您可以執行從一個 Azure 區域到另一個區域的非計劃性容錯移轉。(選擇性) 您可以建立復原計劃並執行非計劃性容錯移轉，在區域與區域之間移轉多部虛擬機器。[深入了解](site-recovery-create-recovery-plans.md)復原計劃。
		
## 後續步驟

在[什麼是 Azure Site Recovery？](site-recovery-overview.md)深入了解其他複寫案例

<!---HONumber=AcomDC_0323_2016-->