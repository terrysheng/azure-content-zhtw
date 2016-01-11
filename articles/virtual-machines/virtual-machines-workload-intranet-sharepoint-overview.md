<properties
	pageTitle="部署 SharePoint Server 2013 伺服器陣列 | Microsoft Azure"
	description="透過五個步驟，在 Azure 中使用 SQL Server AlwaysOn 可用性群組，來部署高可用性 SharePoint Server 2013 伺服器陣列。"
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="josephd"/>

# 在 Azure 中使用 SQL Server AlwaysOn 可用性群組部署 SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

本主題包含的逐步指示連結，可用於部署包含 SQL Server AlwaysOn 可用性群組的內部網路限定 SharePoint 2013 伺服器陣列。此伺服器陣列包含下列電腦：

- 兩部 SharePoint Web 伺服器
- 兩部 SharePoint 應用程式伺服器
- 兩部資料庫伺服器
- 一部叢集多數節點伺服器
- 兩個網域控制站

以下呈現的就是這個設定，其中會為每部伺服器提供預留位置名稱。

![](./media/virtual-machines-workload-intranet-sharepoint-overview/workload-spsqlao_05.png)

兩部適用於每個角色的機器可確保高可用性。所有的虛擬機器都位於單一區域中。適用於特定角色的每個虛擬機器群組都位於它自己的可用性集合中。

## 用料表

基準組態需要下列 Azure 服務和元件的設定組：

- 九部虛擬機器。
- 四個額外資料磁碟，適用於網域控制站和 SQL 伺服器。
- 四個可用性集合。
- 一個跨單位虛擬網路。
- 一個儲存體帳戶。
- 一個 Azure 訂用帳戶。

以下是適用於此組態的虛擬機器及其預設大小。

項目 | 虛擬機器描述 | 資源庫映像 | 預設大小
--- | --- | --- | ---
1\. | 第一網域控制站 | Windows Server 2012 R2 Datacenter | A2
2\. | 第二網域控制站 | Windows Server 2012 R2 Datacenter | A2
3\. | 第一資料庫伺服器 | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
4\. | 第二資料庫伺服器 | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
5\. | 叢集的多數節點 | Windows Server 2012 R2 Datacenter | A1
6\. | 第一 SharePoint 應用程式伺服器 | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | A4
7\. | 第二 SharePoint 應用程式伺服器 | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | A4
8\. | 第一 SharePoint Web 伺服器 | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | A4
9\. | 第二 SharePoint Web 伺服器 | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | A4

若要計算此組態的預估成本，請參閱 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)。

1. 在 [模組] 中，按一下 [計算]，然後按一下 [虛擬機器] 數次，直到足夠建立含有九個虛擬機器的清單為止。
2. 針對每一個虛擬機器，選取：
	- 您想要的區域
	- [Windows] 類型
	- [標準] 定價層
	- 上表中的預設大小或您想要的大小來做為 [執行個體大小]

> [AZURE.NOTE]「Azure 價格計算機」並未納入兩個執行 SQL Server 2014 Enterprise 之虛擬機器的額外 SQL Server 授權費用。如需詳細資訊，請參閱[虛擬機器價格-SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)。

## 部署的階段

您可以在下列階段中部署這個設定：

- [第 1 階段：設定 Azure](virtual-machines-workload-intranet-sharepoint-phase1.md)。建立儲存體帳戶、可用性設定組及跨單位虛擬網路。
- [第 2 階段：設定網域控制站](virtual-machines-workload-intranet-sharepoint-phase2.md)。建立和設定複本 Active Directory 網域服務 (AD DS) 網域控制站。
- [第 3 階段：設定 SQL Server 基礎結構](virtual-machines-workload-intranet-sharepoint-phase3.md)。建立和設定 SQL Server 虛擬機器、準備將它們與 SharePoint 搭配使用，然後建立叢集。
- [第 4 階段：設定 SharePoint 伺服器](virtual-machines-workload-intranet-sharepoint-phase4.md)。建立和設定四部 SharePoint 虛擬機器。
- [第 5 階段：建立可用性群組並新增 SharePoint 資料庫](virtual-machines-workload-intranet-sharepoint-phase5.md)。準備資料庫並建立 SQL Server AlwaysOn 可用性群組。

這個包含 SQL Server AlwaysOn 的 SharePoint 部署是設計來搭配[包含 SQL Server AlwaysOn 的 SharePoint 資訊圖](http://go.microsoft.com/fwlink/?LinkId=394788)使用，其中並包含最新建議。

這個設定是針對每個階段進行引導的指南，適用於在 Azure 基礎結構服務中建立功能完整且高可用性之內部網路 SharePoint 伺服器陣列的預先定義架構。如需在 Azure 中實作 SharePoint 2013 的其他架構指導方針，請參閱[適用於 SharePoint 2013 的 Microsoft Azure 架構](https://technet.microsoft.com/library/dn635309.aspx)。

請記住下列要點：

- 如果您是經驗豐富的 SharePoint 實作者，請自行決定是否要調整第 3 到 5 階段中的指示，以建置最適合您需求的伺服器陣列。
- 如果您已經具備現有的 Azure 混合式雲端部署，則可自行決定要調整或略過第 1 和 2 階段中的指示，在適當的子網路上裝載新的 SharePoint 伺服器陣列。
- 所有伺服器都位於 Azure 虛擬網路中的單一子網路上。如果您想要提供其他相當於隔離子網路的安全性，可以使用[網路安全性群組](../virtual-network/virtual-networks-nsg.md)。

若要建置開發/測試環境或此設定的概念證明，請參閱[在混合式雲端中設定用於測試的 SharePoint 內部網路伺服器陣列](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)。

如需包含 SQL Server AlwaysOn 可用性群組的 SharePoint 的其他資訊，請參閱[為 SharePoint 2013 設定 SQL Server 2012 AlwaysOn 可用性群組 ](https://technet.microsoft.com/library/jj715261.aspx)。

> [AZURE.NOTE]Microsoft 已發行 SharePoint Server 2016 IT 預覽版。若要輕鬆安裝和測試此預覽版，您可以搭配 SharePoint Server 2016 IT 預覽版和其預先安裝的必要元件來使用 Azure 虛擬機器資源庫映像。如需詳細資訊，請參閱[在 Azure 中測試 SharePoint Server 2016 IT Preview](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/)。

## 後續步驟

- 依照[第 1 階段](virtual-machines-workload-intranet-sharepoint-phase1.md)指示開始設定此工作負載。

<!---HONumber=AcomDC_1223_2015-->