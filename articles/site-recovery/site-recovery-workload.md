<properties
	pageTitle="Azure Site Recovery 可以保護哪些工作負載？" 
	description="Azure Site Recovery 會透過協調將內部部署虛擬機器和實體伺服器複寫、容錯移轉及復原至 Azure 或次要內部部署網站，來保護您的工作負載和應用程式" 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="03/27/2016" 
	ms.author="raynew"/>

# Azure Site Recovery 可以保護哪些工作負載？


本文說明您可以使用 Azure Site Recovery 保護哪些工作負載和應用程式。

在這篇文章下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。

## 概觀

組織需要商務持續性和災害復原 (BCDR) 策略，決定應用程式、工作負載和資料如何在規劃與未規劃停機期間保持可用，並儘速復原到正常運作的情況。

Site Recovery 是一項 Azure 服務，藉由將內部部署實體伺服器和虛擬機器的複寫協調至雲端 (Azure) 或次要資料中心，讓您能夠部署應用程式感知恢復應用程式，進而協助制定您的 BCDR 策略。不論您的應用程式是 Windows 或 Linux 型、在實體伺服器、VMware 或 Hyper-V VM 上執行，您都可以使用 Site Recovery 來協調複寫、執行災害復原測試，以及執行容錯移轉和容錯回復。


Site Recovery 整合了 Microsoft 應用程式，包括 SharePoint、Exchange、Dynamics、SQL Server 和 Active Directory。Microsoft 也與 Oracle、SAP、IBM 和 Red Hat 等頂尖廠商合作，以確保其應用程式和服務適用於 Microsoft 平台，包括 Azure。您可以根據各應用程式自訂解決方案。

## 為何要使用 Site Recovery 進行應用程式保護？

Site Recovery 可協助您實作應用程式層級的保護和復原，如下所示：

- 僅需 30 秒即可完成 PRO 近同步複寫，能滿足大多數重要商務應用程式的需求。
- 適用於單一或多層式架構應用程式的應用程式一致性快照。
- 整合 SQL Server AlwaysOn，並與其他應用程式層級的複寫技術合作，包括 AD 複寫、SQL AlwaysOn、Exchange 資料庫可用性群組 (DAG) 和 Oracle 資料保護。
- 彈性修復計劃，讓您只要按一下就能復原整個應用程式堆疊，並在方案中納入外部指令碼和手動動作。 
- Site Recovery 和 Azure 中的進階網路管理可簡化應用程式網路需求，包括保留 IP 位址、設定負載平衡，以及與 Azure 流量管理員整合而達到低 RTO 網路轉換。
-  豐富的自動化程式庫，提供已可用於生產環境的應用程式特定指令碼，這些指令碼可供下載並與復原方案整合。



##工作負載摘要

Site Recovery 可複寫在支援的虛擬機器上執行的任何應用程式。此外，我們已經與產品團隊合作，合力執行額外的特定應用程式測試。

**工作負載** | **將 Hyper-V VM 複寫至次要網站** | **將 Hyper-V VM 複寫至 Azure** | **將 VMware VM 複寫到次要網站** | **將 VMware VM 複寫到 Azure**
---|---|---|---|---
Active Directory、DNS | Y | Y | Y | Y 
Web 應用程式 (IIS、SQL) | Y | Y | Y | Y
SCOM | Y | Y | Y | Y
Sharepoint | Y | Y | Y | Y
SAP<br/><br/>將 SAP 網站複寫至非叢集的 Azure | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試)
Exchange (非 DAG) | Y | 敬請期待 | Y | Y
遠端桌面/VDI | Y | Y | Y | N/A 
Linux (作業系統和應用程式) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) 
Dynamics AX | Y | Y | Y | Y
Dynamics CRM | Y | 敬請期待 | Y | 敬請期待
Oracle | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試)
Windows 檔案伺服器 | Y | Y | Y | Y


## 複寫 Active Directory 和 DNS

Active Directory 和 DNS 基礎結構是大多數企業應用程式的必要項目。在災害復原期間，您必須在復原工作負載和應用程式之前，先保護與復原這些基礎結構元件。

您可以使用 Site Recovery，對 Active Directory 和 DNS 建立一個完整的自動化災害復原方案。例如，如果您想要將 SharePoint 和 SAP 從主要網站容錯移轉至次要網站，您可以先設定可容錯移轉 Active Directory 的復原方案，然後再設定額外的應用程式專屬方案，以便容錯移轉依賴 Active Directory 的其他應用程式。

[深入了解](site-recovery-active-directory.md)如何保護 Active Directory 和 DNS。

## 保護 SQL Server

SQL Server 針對內部部署資料中心內許多商務應用程式，提供資料服務的資料服務基礎。Site Recovery 可與 SQL Server HA/DR 技術一起用來保護採用 SQL Server 的多層式企業應用程式。Site Recovery 提供：

- 針對 SQL Server 提供簡單且符合成本效益的災害復原解決方案。將多個版本的 SQL Server 獨立伺服器和叢集複寫至 Azure 或次要網站。  
- 整合 SQL AlwaysOn 可用性群組，利用 Azure Site Recovery 復原方案管理容錯移轉和容錯回復。
- 應用程式中各層 (包括 SQL Server 資料庫) 的端對端復原方案。
- 藉由將 SQL Server「暴增」到 Azure 中較大的 IaaS 虛擬機器中，利用 Azure Site Recovery 調整 SQL Server 以因應尖峰負載。
- 簡單的 SQL Server 災害復原測試。您可以測試容錯移轉，以分析資料並執行相容性檢查，但不會影響您的生產環境。

[深入了解](site-recovery-sql.md)如何保護 SQL Server。

##保護 SharePoint

Azure Site Recovery 可協助保護 SharePoint 部署，如下所示：

- 排除對災害復原之待命伺服器陣列的需要和相關聯的基礎結構成本。使用 Site Recovery，將整個伺服器陣列 (Web、應用程式和資料庫層) 複寫至 Azure 或次要網站。
- 簡化應用程式部署和管理作業。部署至主要網站的更新會自動複寫，因此可在伺服器陣列於次要網站中容錯移轉和復原之後使用。此外，還可降低讓待命伺服器陣列保持最新狀態的管理複雜度和相關成本。
- 藉由建立類似生產的複本隨選複本環境進行測試和偵錯，以簡化 SharePoint 應用程式開發和測試。
- 藉由使用 Site Recovery 將 SharePoint 部署移轉至 Azure，來簡化雲端轉換。

[深入了解](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae)如何保護 SharePoint。


## 保護 Dynamics AX

Azure Site Recovery 可協助您保護您的 Dynamics AX ERP 解決方案，方法如下：

- 協調將整個 Dynamics AX 環境 (Web 和 AOS 層、資料庫層、SharePoint) 複寫到 Azure 或次要網站。
- 簡化 Dynamics AX 部署到雲端 (Azure) 的移轉。
- 藉由建立類似生產的複本隨選進行測試和偵錯，以簡化 Dynamics AX 應用程式開發和測試。

[深入了解](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281)如何保護動態 Dynamic AX。

## 保護 RDS 

遠端桌面服務 (RDS) 可啟用虛擬桌面基礎結構 (VDI)、以工作階段為基礎的桌面和應用程式，讓使用者可在任何地方工作。使用 Azure Site Recovery，您可以：

- 將受管理或未受管理的集區化虛擬桌面複寫至次要網站，以及將遠端應用程式和工作階段複寫至次要網站或 Azure。
- 以下是您可以複寫的項目︰

**RDS** | **將 Hyper-V VM 複寫至次要網站** | **將 Hyper-V VM 複寫至 Azure** | **將 VMware VM 複寫到次要網站** | **將 VMware VM 複寫到 Azure** | **將實體伺服器複寫到次要網站** | **將實體伺服器複寫到 Azure**
---|---|---|---|---|---|---
**集區化虛擬桌面 (未受管理)** | 是 | 否 | 是 | 否 | 是 | 否
**集區化虛擬桌面 (受管理但不含 UPD)** | 是 | 否 | 是 | 否 | 是 | 否
**遠端應用程式和桌面工作階段 (不含 UPD)** | 是 | 是 | 是 | 是 | 是 | 是


[深入了解](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb)如何保護 RDS。


## 保護 Exchange

Site Recovery 協助保護 Exchange 的方式如下所示：

- 對於小型 Exchange 部署 (例如單一或非叢集式伺服器)，Site Recovery 可以複寫並容錯移轉至 Azure 或次要網站。
- 對於大型部署，Site Recovery 會與 Exchange DAG 整合。
- Exchange DAG 是企業中 Exchange 災害復原的建議解決方案。Site Recovery 復原方案可以包含 DAG，以協調網站之間的 DAG 容錯移轉。


[深入了解](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6)如何保護 Exchange。

## 保護 SAP

使用 Site Recovery 保護您的 SAP 部署，如下所示：

- 將不同的部署層複寫至 Azure 或次要網站，進行整個 SAP 部署的保護。
- 使用 Site Recovery 將您的 SAP 部署移轉至 Azure，來簡化雲端移轉。
- 藉由建立類似生產的複本隨選來測試和偵錯應用程式，以簡化 SAP 開發和測試。

[深入了解](http://aka.ms/asr-sap)如何保護 SAP。

## 後續步驟

[準備](site-recovery-best-practices.md) Site Recovery 部署

<!---HONumber=AcomDC_0330_2016-->