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
	ms.date="12/01/2015" 
	ms.author="raynew"/>

# Azure Site Recovery 可以保護哪些工作負載？

Azure Site Recovery 可讓客戶部署應用程式感知可用性方案，以協助您的組織制訂商務持續性和災害復原 (BCDR) 策略。無論是 Windows 伺服器或 Linux 應用程式、Microsoft 企業應用程式或其他廠商的產品，您可以使用 Azure Site Recovery 來啟用 災害復原、隨選開發和測試環境，以及雲端移轉。

Site Recovery 整合了 Microsoft 應用程式，包括 SharePoint、Exchange、Dynamics、SQL Server 和 Active Directory。Microsoft 也與 Oracle、SAP、IBM 和 Red Hat 等頂尖廠商合作，以確保其應用程式和服務適用於 Microsoft 平台，例如 Azure 和 Hyper-V。您可以針對各個特定應用程式，自訂災害復原解決方案。


##主要功能
可協助您實作應用程式層級保護及復原策略的 Azure Site Recovery 功能包括：

- 僅需 30 秒即可複寫 RPO 之接近同步的複寫功能，以符合最重要的應用程式需求。
- 適用於單一或多層式架構應用程式的應用程式一致性快照。
- 整合 SQL Server AlwaysOn，並與其他應用程式層級的複寫技術合作，包括 AD 複寫、SQL AlwaysOn、Exchange 資料庫可用性群組 (DAG) 和 Oracle 資料保護。
- 彈性修復計劃，只要按一下就能讓您復原整個應用程式堆疊，並包含外部指令碼或手動動作。 
- Site Recovery 和 Azure 中的進階網路管理可簡化應用程式的網路需求，包括保留 IP 位址，設定負載平衡器或低 RTO 網路轉換的 Azure 流量管理員整合。
-  豐富的自動化程式庫，提供已可用於生產環境，且可下載並已經與 Site Recovery 整合的應用程式特定指令碼。



##工作負載摘要

Site Recovery 複寫技術與虛擬機器中執行的任何應用程式相容。此外，我們已經與其他應用程式產品小組合作完成其他測試，以進一步支援每個應用程式。

**工作負載** | <p>**複寫 Hyper-V VM**</p><p>**(至次要網站)**</p> | <p>**複寫 Hyper-V VM**</p><p>**(至 Azure)**</p> | <p>**複寫 VMware VM**</p><p>**(至次要網站)**</p> | <p>**複寫 VMware VM**</p><p>**(至 Azure)****</p>---|---|---|---|---Active Directory、DNS | Y | Y | Y | Y Web 應用程式 (IIS、SQL) | Y | Y | Y | Y SCOM | Y | Y | Y | Y Sharepoint | Y | Y | Y | Y <p>SAP</p><p>複寫 SAP 非叢集至 Azure 網站</p> | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) Exchange (非 DAG) | Y | 即將推出 | Y | Y 遠端桌面/VDI | Y | Y | Y | N/A <p>Linux</p><p>(作業系統和應用程式)</p> | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) Dynamics AX | Y | Y | Y | Y Dynamics CRM | Y | 即將推出 | 即將推出 Oracle | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) Windows 檔案伺服器 | Y | Y | Y | Y

##保護 Active Directory 和 DNS

所有的企業應用程式 (例如 SharePoint、Dynamics AX 和 SAP) 取決於 Active Directory 和 DNS 基礎結構。做為 BCDR 解決方案的一部分，您必須在復原工作負載和應用程式之前，先保護與復原這些基礎結構元件。

使用 Site Recovery，您可以針對 Active Directory 和 DNS 建立一個完整的自動化災害復原計畫。例如，如果您在您的主要網站針對多個應用程式 (例如 SharePoint 和 SAP) 使用 Active Directory，且您想要執行完整網站的容錯移轉，您可以先使用復原計畫執行 Active Directory 的容錯移轉，再使用應用程式特定復原計劃來執行依賴 Active Directory 之應用程式的容錯移轉。

[詳細資訊](http://aka.ms/asr-ad)

##保護 SQL Server

SQL Server 針對內部部署資料中心內許多商務應用程式，提供資料服務的基礎。Site Recovery 和 SQL Server HA/DR 技術是互補的，並可一起用來提供多層企業應用程式端對端保護。Site Recovery 提供下列 SQL Server 環境的優點：

- 簡易保護以及 Azure 或次要網站的獨立或叢集式 SQL Server 複寫。針對許多 SQL Server 版本提供了簡單且符合成本效益的災害復原方案。
- 整合 SQL AlwaysOn 可用性群組，利用 Azure Site Recovery 中的復原方案管理容錯移轉和容錯回復處理序。
- 整個應用程式堆疊 (包括 SQL Server 資料庫) 的端對端復原計劃。
- 藉由將 SQL Server「暴增」到 Azure 中大小較大的 IaaS 虛擬機器中，來使用 Azure Site Recovery 擴充 SQL Server 以因應尖峰時間載入。
- 使用 Azure Site Recovery 在 Azure 或次要網站中測試 SQL Server。測試容錯移轉可用於分析資料相容性檢查，而不會影響實際執行環境。

[深入了解](http://aka.ms/asr-sql)

##保護 SharePoint

Azure Site Recovery 可協助您保護您的 SharePoint 部署。使用 Site Recovery，您可以：

- 排除對災害復原之待命伺服器陣列的需要和相關聯的基礎結構成本。使用 Site Recovery，您可以啟用保護整個伺服器陣列 (Web、應用程式和資料庫層) 以保護 Azure 或次要網站。
- 簡化應用程式部署和管理能力。部署至主要網站的更新會自動複寫，並且在伺服器陣列於次要網站上復原時可供使用。如此能排除讓待命伺服器陣列保持最新狀態的管理複雜度並降低成本。
- 藉由建立類似生產的複本隨選複本環境進行測試和偵錯，以簡化 SharePoint 應用程式開發和測試。
- 藉由使用 Site Recovery 將 SharePoint 部署移轉至 Azure，來簡化雲端轉換。

[深入了解](http://aka.ms/asr-sharepoint)


## 保護 Dynamics AX

Azure Site Recovery 可協助您保護您的 Dynamics AX ERP 方案：

- 使用 Site Recovery 將您整個 Dynamics AX 環境 (Web 和 AOS 層、資料庫層、SharePoint) 的保護擴充到 Azure 或次要網站。
- 使用 Site Recovery 將 Dynamics AX 部署移轉至 Azure，來簡化雲端移轉。
- 藉由建立類似生產的複本隨選進行測試和偵錯，以簡化 Dynamics AX 應用程式開發和測試。

[閱讀更多資訊](http://aka.ms/asr-dynamics)

## 保護 RDS 
遠端桌面服務可啟用虛擬桌面基礎結構 (VDI)、以工作階段為基礎的桌面和應用程式，讓使用者可在任何地方工作。使用 Site Recovery，您可以將受管理或未受管理集區虛擬桌面的保護擴充至次要網站，以及將遠端應用程式與工作階段的保護擴充至次要網站或 Azure。

[深入了解](http://aka.ms/asr-rds)


## 保護 Exchange

Microsoft Exchange 包含高可用性和災害復原的內建支援。Exchange DAG 與 Azure Site Recovery 可搭配使用。

- Exchange DAG 是企業中 Exchange 災害復原的建議解決方案。Site Recovery 中的復原計劃可以包含 DAG，以協調網站之間的 DAG 容錯移轉。
- 針對小型部署 (例如單一或非叢集式伺服器)， Site Recovery 可以保護和容錯移轉至 Azure 或次要網站。

[深入了解](http://aka.ms/asr-exchange)

## 保護 SAP

使用 Site Recovery 保護您的 SAP 部署：

- 啟用保護整個具有不同層的 SAP 部署至 Azure 或次要網站。
- 使用 Site Recovery 將您的 SAP 部署移轉至 Azure，來簡化雲端移轉。
- 藉由建立類似生產的複本隨選來測試和偵錯應用程式，以簡化 SAP 開發和測試。

[深入了解](http://aka.ms/asr-sap)

<!---HONumber=AcomDC_1203_2015-->