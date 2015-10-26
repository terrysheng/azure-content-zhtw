<properties
	pageTitle="站台復原工作負載指引 | Microsoft Azure" 
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
	ms.date="09/21/2015" 
	ms.author="pratshar"/>

# 站台復原工作負載指引

Azure Site Recovery 可讓客戶部署應用程式感知可用性解決方案。若為 Windows Server 或 Linux 架構的應用程式、Microsoft 第一方企業應用程式或其他廠商的產品，您可以使用 Azure Site Recovery 來啟用損毀復原、隨選開發/測試環境或雲端移轉。

Microsoft 有開發頂級企業應用程式的深度專業與經驗，例如 SharePoint、Exchange、Dynamics、SQL Server 和 Active Directory。Microsoft 也與 Oracle、SAP、IBM 和 Red Hat 等頂尖廠商合作，以確保其應用程式和服務適用於 Microsoft 平台，包括 Azure 和 HYPER-V。我們運用這些獨家優勢深入了解每個應用程式的可用性需求，讓您可以部署位為每個應用程式自訂的頂級災害復原及可用性解決方案。


##主要功能
Azure Site Recovery 功能在設計時有考量到應用程式層級的保護/復原：

- 使用 30 秒之 RPO 的幾乎同步複寫，符合最重要應用程式的需求。
- 適用於單一或多層式架構應用程式的應用程式一致性快照
- 和應用程式層級複寫整合。運用頂級應用程式層級產品，包括 AD 複寫、SQL Always On、Exchange 資料庫可用性群組和 Oracle 資料保護
- 彈性的復原計劃只需按一下，即可復原整個應用程式堆疊，包括執行外部指令碼或甚至是手動動作。 
- ASR 與 Azure 中的進階網路管理會自動化所有專屬於應用程式的網路組態：保留 IP 位址、設定負載平衡器或使用 Microsoft 的流量管理員進行低 RTO 網路交換。
- 提供實際執行備妥應用程式特定指令碼的豐富自動化程式庫。將其下載並整合到您的 ASR 式解決方案。


##工作負載指引摘要

ASR 複寫技術與虛擬機器中執行的任何應用程式相容。我們已經與應用程式產品團隊合作進行了其他測試，以進一步支援每個應用程式。

**工作負載** | <p>**複寫 Hyper-V 虛擬機器**</p><p>**(至次要網站)**</p> | <p>**複寫 Hyper-V 虛擬機器**</p><p>**(至 Azure)**</p> | <p>**複寫 VMware 虛擬機器**</p><p>**(至次要網站)**</p> | <p>**複寫 VMware 虛擬機器**</p><p>**(至 Azure)****</p>---|---|---|---|---Active Directory、DNS | Y | Y | Y | Y Web 應用程式 (IIS、SQL) | Y | Y | Y | Y SCOM | Y | Y | Y | Y Sharepoint | Y | Y | Y | Y <p>SAP</p><p>複寫 SAP 非叢集至 Azure 網站</p> | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) Exchange (非 DAG) | Y | 即將推出 | Y | Y 遠端桌面/VDI | Y | Y | Y | N/A <p>Linux</p><p>(作業系統和應用程式)</p> | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) Dynamics AX | Y | Y | Y | Y Dynamics CRM | Y | 即將推出 | 即將推出 Oracle | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) Windows 檔案伺服器 | Y | Y | Y | Y

##Active Directory 和 DNS

所有企業應用程式，例如 SharePoint、Dynamics AX 和 SAP 都依存於 AD 和 DNS 基礎結構才能正確運作。建立這類應用程式的災害復原 (DR) 解決方案時，務必在其他應用程式元件遭遇中斷事件之前保護和復原 AD。

使用 Azure Site Recovery 時，您可以為 AD 建立完整的自動化災害復原計劃。如果您的主要站台中有適用於多個應用程式的 AD，如 SharePoint 和 SAP，而您決定容錯移轉整個站台，您可以先使用 AD 復原計劃容錯移轉 AD，再使用應用程式特定復原計劃容錯移轉其他應用程式。

參考連結的文件以了解[為 AD 部署 Azure Site Recovery](http://aka.ms/asr-ad) 的詳細指引

##SQL Server
Microsoft SQL Server 是許多企業級第一方、協力廠商，以及在客戶內部部署資料中心內執行之自訂企業營運應用程式的基礎。SharePoint、Dynamics 和 SAP 之類的應用程式會使用 SQL Server 提供資料服務。Azure Site Recovery 和 SQL Server HA/DR 技術是互補的，並可用來提供多層企業應用程式端對端保護。Azure Site Recovery 提供下列 SQL Server 環境的優點：

- 輕鬆地保護 Azure 或次要站台的獨立或叢集 SQL 伺服器。為任何版本的 SQL Server 擴充簡單、符合成本效益的 DR 解決方案。
- 與頂級的災害復原解決方案 SQL Always On 可用性群組整合，並利用 ASR 復原計劃管理容錯移轉/容錯回復作業。
- 為包括 SQL 資料庫的整個應用程式堆疊建立端對端復原計劃。
- 透過在 Azure 中將負載「暴增」為較大的 IaaS VM 大小，在尖峰負載期間使用 Azure Site Recovery 相應增加 SQL Server。
- 在 Azure 中或在次要站台使用 Azure Site Recovery 建立 SQL Server 的測試複本。將這個複本用於分析和資料相容性檢查，而不影響生產環境。

參考連結的文件以了解[為 SQL 部署 Azure Site Recovery](http://aka.ms/asr-sql) 的詳細指引

##SharePoint
SharePoint 是受歡迎的應用程式，可讓組織藉由提供內部網路入口網站、文件與檔案管理、社交網路、網站和企業搜尋功能進行共同作業。它也是可以輕鬆部署自訂應用程式和工作流程的應用程式平台。藉由使用 SharePoint 的 Azure Site Recovery，您可以：

- 排除對災害復原之待命伺服器陣列的需要和相關聯的基礎結構成本。使用 ASR，您可以啟用整個伺服器陣列 (Web、應用程式和資料庫層) 至 Azure 或次要站台的保護。
- 簡化應用程式部署和管理能力。部署至主要站台的更新會自動複寫，並且在伺服器陣列於次要站台上復原時可供使用。如此能排除讓待命伺服器陣列保持最新狀態的管理複雜度並降低 TCO。
- 藉由建立類似生產的複本隨選進行測試和偵錯，以簡化 SharePoint 應用程式開發和測試。
- 藉由使用 ASR 將 SharePoint 部署移轉至 Azure，以簡化到雲端的路徑。

參考連結的文件以了解[為 Sharepoint 部署 Azure Site Recovery](http://aka.ms/asr-sharepoint) 的詳細指引


##Dynamics AX
Microsoft Dynamics AX 是首屈一指的企業資源規劃 (ERP) 解決方案，非常容易學習和使用，所以您可以更快速地傳遞價值、把握商機並且在組織內促進使用者參與和創新。

- 使用 ASR，您可以啟用整個 Dynamics Ax (Web 及 AOS 層、資料庫層、SharePoint) 至 Azure 或次要站台的保護。
- 藉由使用 ASR 將 Dynamics Ax 部署移轉至 Azure，以簡化到雲端的路徑。
- 藉由建立類似生產的複本隨選進行測試和偵錯，以簡化 Dynamics 應用程式開發和測試。

參考連結的文件以了解[為 Dynamics Ax 部署 Azure Site Recovery](http://aka.ms/asr-dynamics) 的詳細指引

## RDS 
Windows Server 遠端桌面服務 (RDS) 會加速並延伸桌面及應用程式部署到任何裝置、改善遠端背景工作效率，同時協助保護重要智慧財產安全，並簡化法規相符性。遠端桌面服務可啟用虛擬桌面基礎結構 (VDI)、以工作階段為基礎的桌面和應用程式，讓使用者可在任何地方工作。

使用 ASR，您可以啟用保護受管理或未受管理集區虛擬桌面至次要站台，以及啟用保護遠端應用程式以及工作階段至次要站台或 Azure。

參考連結的文件以了解[為 RDS/VDI 部署 Azure Site Recovery](http://aka.ms/asr-rds) 的詳細指引


## Exchange
Microsoft Exchange 是企業用來裝載其訊息和電子郵件服務的慣用軟體。Exchange 會確保存取 PC、電話或瀏覽器之間的通訊，同時提供前所未有的可靠性、可管理性和資料保護。

Microsoft Exchange 原本就支援企業級的高可用性和災害復原解決方案。Exchange 資料庫可用性群組和 Azure Site Recovery 技術是互補的。

- Exchange DAG 是啟用 Exchange 部署之頂級災害復原的建議部署選項。ASR 復原計劃可以包含 DAG 以協調跨站台的 DAG 容錯移轉。
- 若是小型部署，例如單一伺服器或非叢集伺服器，Azure Site Recovery 可以保護個別伺服器並將其容錯移轉到 Azure 或次要站台。

參考連結的文件以了解[為 Exchange 部署 Azure Site Recovery](http://aka.ms/asr-exchange) 的詳細指引

## SAP

SAP 是全世界許多組織所使用首屈一指的企業資源規劃 (ERP) 軟體。SAP 常被視為企業內最常見的關鍵任務應用程式。這些應用程式的架構和作業大部分都是非常複雜的，而且務必要確保您符合 BCDR 的需求。

- 使用 ASR，您可以啟用整個具有不同層的 SAP 部署至 Azure 或次要站台的保護。
- 藉由使用 ASR 將 SAP 部署移轉至 Azure，以簡化到雲端的路徑。
- 藉由建立類似生產的複本隨選進行測試和偵錯，以簡化 SAP 應用程式開發和測試。

參考連結的文件以了解[為 SAP 部署 Azure Site Recovery](http://aka.ms/asr-sap) 的詳細指引

<!---HONumber=Oct15_HO3-->