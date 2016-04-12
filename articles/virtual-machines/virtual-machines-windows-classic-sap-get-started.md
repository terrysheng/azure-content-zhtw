<properties
   pageTitle="在 Windows 虛擬機器上使用 SAP | Microsoft Azure"
   description="了解如何在 Microsoft Azure 中的 Windows 虛擬機器 (VM) 上使用 SAP"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="juergent"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="sedusch"/>

# 在 Azure 中的 Windows 虛擬機器上使用 SAP

雲端運算這個廣泛使用的名詞已日益受到 IT 產業的重視，不論是小型公司、大型公司還是跨國企業都是如此。Microsoft Azure 是 Microsoft 所推出的雲端服務平台，可提供各式各樣的新契機。客戶現在既能將應用程式快速佈建為雲端服務，也能快速取消佈建，因此不會再受到技術或預算所限制。與其在硬體基礎結構投入時間和預算，公司寧可專注於應用程式、商業流程及其帶給客戶和使用者的優點。

Microsoft 透過 Microsoft Azure 虛擬機器，提供完整的基礎結構即服務 (IaaS) 平台。Azure 虛擬機器 (IaaS) 支援以 SAP NetWeaver 為基礎的應用程式。下列白皮書說明如何在 Azure 中的 Windows 虛擬機器上，規劃及實作 SAP NetWeaver 的應用程式。您也可以在 [Linux 虛擬機器](virtual-machines-linux-classic-sap-get-started.md)上，實作 SAP NetWeaver 應用程式。

[AZURE.INCLUDE [virtual-machines-common-classic-sap-get-started](../../includes/virtual-machines-common-classic-sap-get-started.md)]

## Azure 上的 SAP NetWeaver - HA

標題：Azure 上的 SAP NetWeaver - 使用 SIOS DataKeeper 在 Azure 上利用 Windows Server 容錯移轉叢集將 SAP ASCS/SCS 執行個體叢集在一起

摘要：本文件說明如何使用 SIOS DataKeeper 在 Azure 上設定高可用性的 SAP ASCS/SCS 組態。SAP 可透過需要共用磁碟的 Windows Server 容錯移轉叢集組態，保護其單一失敗點元件，例如 SAP ASCS/SCS 或 Enqueue Replication Services。要讓 SAP 系統運作，就不可缺少這些 SAP 元件。因此必須備有高可用性功能，才能確保這些元件可以和裸機與 Hyper-V 環境的 Windows 叢集組態所做的一樣，承受伺服器或 VM 的故障。自 2015 年 8 月起，Azure 本身就無法提供 Windows 型高可用性組態所需的共用磁碟，而這些重要的 SAP 元件正需要這些組態。不過在有了 SIOS DataKeeper 產品的協助後，就能在 Azure IaaS 平台上建置 SAP ASCS/SCS 所需要的 Windows Server 容錯移轉叢集組態。本白皮書將說明如何在 Azure 中使用 SIOS Datakeeper 所提供的共用磁碟，來安裝 Windows Server 容錯移轉叢集組態的逐步方法。本白皮書將詳細說明 Azure、Windows 和 SAP 端的組態，讓高可用性組態能以最佳的方式運作。本白皮書會對「SAP 安裝文件」和「SAP 附註」進行補充說明，指出用來在指定平台上安裝和部署 SAP 軟體的主要資源。

更新時間：2015 年 8 月

[立即下載此指南](http://go.microsoft.com/fwlink/?LinkId=613056)

<!---HONumber=AcomDC_0330_2016-->