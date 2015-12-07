<properties
   pageTitle="在 Azure 虛擬機器 (VM) 上使用 SAP | Microsoft Azure"
   description="在 Azure 虛擬機器 (VM) 上使用 SAP"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="juergent"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="na"
   ms.date="11/18/2015"
   ms.author="sedusch"/>
   
# 在 Azure 虛擬機器 (VM) 上使用 SAP

雲端運算這個廣泛使用的名詞已日益受到 IT 產業的重視，不論是小型公司、大型公司還是跨國企業都是如此。Microsoft Azure 是 Microsoft 所推出的雲端服務平台，可提供各式各樣的新契機。客戶現在既能將應用程式快速佈建為雲端服務，也能快速取消佈建，因此不會再受到技術或預算所限制。與其在硬體基礎結構投入時間和預算，公司寧可專注於應用程式、商業流程及其帶給客戶和使用者的優點。

透過 Microsoft Azure 虛擬機器服務，Microsoft 提供完整的基礎結構即服務 (IaaS) 平台。Azure 虛擬機器 (IaaS) 支援以 SAP NetWeaver 為基礎的應用程式。下列白皮書將說明如何在做為所選平台的 Microsoft Azure 中，規劃和實作以 SAP NetWeaver 為基礎的應用程式。

## 規劃和實作

標題：Azure 虛擬機器上的 SAP NetWeaver – 規劃和實作指南

摘要：如果您考慮在 Azure 虛擬機器中執行 SAP NetWeaver，那麼請先閱讀此白皮書。此規劃和實作指南將協助您評估是否可以將現有或已規劃的 SAP NetWeaver 架構系統部署到 Azure 虛擬機器環境。其內容涵蓋多個 SAP NetWeaver 部署案例，並包含 Azure 特有的 SAP 組態。本白皮書列出並描述若要執行混合式 SAP 環境，SAP/Azure 端所需的所有必要設定資訊。另外也會說明為確保 IaaS 上的 SAP NetWeaver 架構系統擁有高可用性，您所能採取的措施。

更新時間：2015 年 8 月

[立即下載此指南](http://go.microsoft.com/fwlink/?LinkId=397963)
## Deployment
標題：Azure 虛擬機器上的 SAP NetWeaver – 部署指南

摘要：本文件提供用來將 SAP NetWeaver 軟體部署到 Azure 虛擬機器的程序指引。本白皮書將重點放在三個特定的部署案例，強調要啟用 Azure Monitoring Extensions for SAP，並包括 Azure Monitoring Extensions for SAP 的疑難排解建議。本白皮書假設您已閱讀過規劃和實作指南。

更新時間：2015 年 9 月

[立即下載此指南](http://go.microsoft.com/fwlink/?LinkId=397964)

## Azure 上的 SAP DBMS
標題：Azure 中的 SAP DBMS 部署指南

摘要：本白皮書涵蓋應該與 SAP 搭配執行之 DBMS 系統的規劃和實作考量。第一個部分會列出和提出一般考量。本白皮書的後續部分與在 Azure 中部署 SAP 所支援的不同 DBMS 有關。所提出的不同 DBMS 分別是 SQL Server、SAP ASE 和 Oracle。這幾個特定部分會探討當您在 Azure 上搭配這些 DBMS 來執行 SAP 系統時，所必須納入的考量。本白皮書也會提出在搭配 SAP 應用程式使用時，Azure 上不同 DBMS 所支援的備份和高可用性方法等主題。

更新時間：2015 年 8 月

[立即下載此指南](http://go.microsoft.com/fwlink/?LinkId=397965)

## Azure 上的 SAP NetWeaver

標題：SAP NetWeaver - 建置 Azure 災害復原解決方案

摘要：本文件提供的逐步指引可用來建置適用於 SAP NetWeaver 的 Azure 災害復原解決方案。所描述的解決方案假設 SAP 環境正在執行以 Hyper-V 為基礎的虛擬化內部部署。本文件的第一個部分會在 Azure Site Recovery (ASR) 元件中導入其服務。本文件的第二個部分描述 SAP NetWeaver 架構環境的細節，另外也會探討並描述搭配使用 ASR 與 SAP NetWeaver 應用程式執行個體及 SAP Central Services 的可能性。第二個部分的重點是將 ASR 用於受 Windows Server 容錯移轉叢集組態所保護的 SAP Central Services。

更新時間：2015 年 9 月

[立即下載此指南](http://go.microsoft.com/fwlink/?LinkID=521971)

## Azure 上的 SAP NetWeaver - HA

標題：Azure 上的 SAP NetWeaver - 使用 SIOS DataKeeper 在 Azure 上利用 Windows Server 容錯移轉叢集將 SAP ASCS/SCS 執行個體叢集在一起

摘要：本文件說明如何使用 SIOS DataKeeper 在 Azure 上設定高可用性的 SAP ASCS/SCS 組態。SAP 可透過需要共用磁碟的 Windows Server 容錯移轉叢集組態，保護其單一失敗點元件，例如 SAP ASCS/SCS 或 Enqueue Replication Services。要讓 SAP 系統運作，就不可缺少這些 SAP 元件。因此必須備有高可用性功能，才能確保這些元件可以和裸機與 Hyper-V 環境的 Windows 叢集組態所做的一樣，承受伺服器或 VM 的故障。自 2015 年 8 月起，Azure 本身就無法提供 Windows 型高可用性組態所需的共用磁碟，而這些重要的 SAP 元件正需要這些組態。不過在有了 SIOS DataKeeper 產品的協助後，就能在 Azure IaaS 平台上建置 SAP ASCS/SCS 所需要的 Windows Server 容錯移轉叢集組態。本白皮書將說明如何在 Azure 中使用 SIOS Datakeeper 所提供的共用磁碟，來安裝 Windows Server 容錯移轉叢集組態的逐步方法。本白皮書將詳細說明 Azure、Windows 和 SAP 端的組態，讓高可用性組態能以最佳的方式運作。本白皮書會對「SAP 安裝文件」和「SAP 附註」進行補充說明，指出用來在指定平台上安裝和部署 SAP 軟體的主要資源。

更新時間：2015 年 8 月

[立即下載此指南](http://go.microsoft.com/fwlink/?LinkId=613056)

<!---HONumber=AcomDC_1125_2015-->