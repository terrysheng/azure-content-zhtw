<properties
	pageTitle="使用 Site Recovery 將 Windows 虛擬機器從 Amazon Web Services 移轉至 Azure | Microsoft Azure"
	description="本文說明如何使用 Azure Site Recovery 將 Amazon Web Services (AWA) 中執行的 Windows 虛擬機器移轉至 Azure。"
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
	ms.date="03/16/2016"
	ms.author="raynew"/>

#  使用 Azure Site Recovery 將 Amazon Web Services (AWS) 中的 Windows 虛擬機器移轉至 Azure

## 概觀

本文說明如何使用 Site Recovery，將 AWS 中執行的 Windows 執行個體移轉至 Azure。開始之前，請注意：

- 您只能在這個階段移轉。這表示您可以從 AWS 容錯移轉至 Azure，但是無法將它們容錯移轉回來。
- 本文摘要說明並使用[複寫 VMware 虛擬機器或實體伺服器至 Azure](site-recovery-vmware-to-azure-classic.md) 一文中完整描述的許多步驟，該文提供設定複寫的最新增強指示。我們建議您遵循這份文件，以取得移轉時的詳細指示。
- **您不應該再使用**[舊版文件](site-recovery-vmware-to-azure-classic-legacy.md)中的指示。

在這篇文章下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題


## 必要條件

以下是您在開始之前的必要條件：

- **管理伺服器**︰執行 Windows Server 2012 R2 作為管理伺服器的內部部署 VM。您可在此伺服器上安裝 Site Recovery 元件 (包括組態伺服器和處理序伺服器)。深入了解[管理伺服器考量](site-recovery-vmware-to-azure-classic.md#management-server-considerations)和[內部部署必要條件](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites)。
- **EC2 VM 執行個體**：您要移轉後加以保護的執行個體。

## 部署步驟

1. [建立保存庫](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault)
2. [部署管理伺服器](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server)。 
3. 部署管理伺服器之後，請驗證該伺服器否能夠與您要移轉的 EC2 執行個體通訊。
4. [建立保護群組](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group)。保護群組包含受保護的機器，它們共用相同的複寫設定。您可以指定群組的複寫設定，這些設定會套用至您加入該群組的所有機器。 
5. [安裝行動服務](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service)。所有要保護的 VM 都需要安裝行動服務。這項服務會將資料傳送至處理序伺服器。行動服務可以手動方式安裝，或在為 VM 啟用保護時由處理序伺服器自動推入安裝。您要移轉的 EC2 執行個體上的防火牆規則應該設定為允許推入安裝這項服務。EC2 執行個體的安全性群組應該具有下列規則：

	![防火牆規則](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [為機器啟用保護](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine)。將您想要保護的電腦加入複寫群組中。

	![啟用保護](./media/site-recovery-migrate-aws-to-azure/migrate-add-machines.png)

7. 您可以使用可從 EC2 主控台取得之 EC2 執行個體的私人 IP 位址，探索要移轉至 Azure 的 EC2 執行個體。在保護群組中，您可以加入每個執行個體的私人 IP 位址。

	![啟用保護](./media/site-recovery-migrate-aws-to-azure/migrate-machine-ip.png)

	將機器加入群組中之後，將啟用保護，而且初始複寫將根據保護群組設定執行。

8. [執行未規劃的容錯移轉](site-recovery-failover.md#run-an-unplanned-failover)。初始複寫完成之後，您可以為每部 VM 執行從 AWS 到 Azure 之未規劃的容錯移轉。(選擇性) 您可以建立復原計劃並執行未規劃的容錯移轉，將多部虛擬機器從 AWS 移轉至 Azure。[深入了解](site-recovery-create-recovery-plans.md)復原計劃。
		
## 後續步驟

在[什麼是 Azure Site Recovery？](site-recovery-overview.md)深入了解其他複寫案例

<!---HONumber=AcomDC_0323_2016-->