<properties 
	pageTitle="部署企業營運應用程式 | Microsoft Azure"
	description="在 Azure 中透過五個階段，部署包含 SQL Server AlwaysOn 可用性群組的 Web 型、高可用性企業營運應用程式。"
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="josephd"/>

# 在 Azure 中部署高可用性的企業營運應用程式

本文包含在 Azure 基礎結構服務中，部署包含 SQL Server AlwaysOn 可用性群組之高可用性、內部網路專屬 Web 型企業營運應用程式的逐步指示連結。應用程式會裝載於這些電腦上：

- 兩部 Web 伺服器
- 兩部資料庫伺服器
- 一部叢集多數節點伺服器
- 兩個網域控制站

以下呈現的就是這個設定，其中會為每部伺服器提供預留位置名稱。

![](./media/virtual-machines-workload-high-availability-LOB-application-overview/workload-lobapp-phase4.png)
 
每個角色至少兩部機器可確保高可用性。所有虛擬機器皆位於單一 Azure 位置 (也稱為區域)。適用於特定角色的每個虛擬機器群組都位於它們自己的可用性設定組中。

您可以在下列階段中部署這個設定：

- [第 1 階段：設定 Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md)。建立儲存體帳戶、可用性集合及跨單位虛擬網路。
- [第 2 階段：設定網域控制站](virtual-machines-workload-high-availability-LOB-application-phase2.md)。建立和設定複本 Active Directory 網域服務 (AD DS) 網域控制站。
- [第 3 階段：設定 SQL Server 基礎結構](virtual-machines-workload-high-availability-LOB-application-phase3.md)。建立和設定執行 SQL Server 的虛擬機器、建立叢集，以及啟用 SQL Server AlwaysOn 可用性群組。
- [第 4 階段：設定 Web 伺服器](virtual-machines-workload-high-availability-LOB-application-phase4.md)。建立和設定兩部 Web 伺服器虛擬機器。
- [第 5 階段：將應用程式資料庫加入至 SQL Server AlwaysOn 可用性群組](virtual-machines-workload-high-availability-LOB-application-phase5.md)。準備企業營運應用程式資料庫並將其加入至 SQL Server AlwaysOn 可用性群組。

此部署是設計來搭配[企業營運應用程式架構藍圖](http://msdn.microsoft.com/dn630664)並納入最新的建議。

這是規範性且預先定義的架構。請記住下列要點：

- 如果您是經驗豐富的 Web 型企業營運應用程式實作者，請自行決定是否要調整第 3 到 5 階段中的指示，以建置最適合您需求的應用程式基礎結構。 
- 如果您已經具備現有的 Azure 混合式雲端實作，可自行決定是否要調整或略過第 1 和 2 階段中的指示，在適當的子網路上裝載適用於新應用程式的虛擬機器。
- 所有伺服器都位於 Azure 虛擬網路中的單一子網路上。如果您想要提供其他相當於隔離子網路的安全性，可以使用 [網路安全性群組](../virtual-networks/virtual-networks-nsg.md)。

若要建置開發/測試環境或此設定的概念證明，請參閱〈[在混合式雲端中設定用於測試的 Web 型 LOB 應用程式](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)〉。

如需有關針對 Azure 設計 IT 工作負載的詳細資訊，請參閱〈[Azure 基礎結構服務實作方針](virtual-machines-infrastructure-services-implementation-guidelines.md)〉。

## 後續步驟

若要開始設定這個工作負載，請前往[第 1 階段：設定 Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md)。

## 其他資源

[企業營運應用程式架構藍圖](http://msdn.microsoft.com/dn630664)

[在混合式雲端中設定 Web 型 LOB 應用程式進行測試](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Azure 基礎結構服務實作指導方針](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure 基礎結構服務工作負載：SharePoint Server 2013 陣列](virtual-machines-workload-intranet-sharepoint-farm.md)

<!----HONumber=August15_HO9-->