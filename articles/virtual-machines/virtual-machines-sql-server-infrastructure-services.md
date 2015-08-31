<properties 
	pageTitle="Azure 虛擬機器上的 SQL Server 概觀" 
	description="本文章簡介 Azure IaaS 虛擬機器上架設的 SQL Server。本文章也提供深度內容的連結。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="08/18/2015"
	ms.author="jroth"/>

# Azure 虛擬機器上的 SQL Server 概觀

## 概觀
您可以利用各種設定來架設 [Azure 虛擬機器上的 SQL Server](http://azure.microsoft.com/services/virtual-machines/sql-server/)，範圍包括單一資料庫伺服器到使用 AlwaysOn 可用性群組和 Azure 虛擬網路的多電腦設定。本主題會嘗試指向部分最佳資源，以開始在 Azure 虛擬機器中執行 SQL Server。

>[AZURE.NOTE]在 Azure VM 上執行 SQL Server，是用來將關聯式資料儲存到 Azure 的選項之一。您也可以使用 Azure SQL Database 服務。如需詳細資訊，請參閱[了解 Azure SQL Database 和 Azure VM 中的 SQL Server](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)。
 
## 在單一 VM 上部署 SQL Server 執行個體

要在 Azure 中部署 SQL Server 虛擬機器，最簡單的方式就是[在 Azure 管理入口網站中佈建 SQL Server 機器資源庫映像](virtual-machines-provision-sql-server.md)。這些映像包含的 SQL Server 授權，是以 VM 為計價單位。

不過，您也可以[建立 Azure 虛擬機器](virtual-machines-windows-tutorial.md)，而不需要預先安裝的 SQL Server。您可以安裝任何您擁有授權的 SQL Server 執行個體。

在這些佈建及設定的早期階段，常見工作包括：

- [檢閱 Azure VM 中的 SQL Server 效能最佳作法](https://msdn.microsoft.com/library/azure/dn133149.aspx)
- [檢閱 Azure VM 中的 SQL Server 安全性最佳作法](https://msdn.microsoft.com/library/azure/dn133147.aspx)
- [設定連線](virtual-machines-sql-server-connectivity.md)

## 部署具有多個 VM 的高可用性設定

您可以使用 SQL Server AlwaysOn 可用性群組，就可以實現 SQL Server 的高可用性。這牽涉到虛擬網路中多個 Azure VM。Azure Preview 入口網站有一個範本，己經有您需要的設定。如需詳細資訊，請參閱 [Microsoft Azure 入口網站組件庫提供的 SQL Server AlwaysOn](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)。

如果您想要手動設定可用性群組和相關聯的接聽程式，請參閱下列文章：

- [在 Azure (GUI) 中設定 AlwaysOn 可用性群組](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [設定 Azure 中 AlwaysOn 可用性群組的 ILB 接聽程式](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [將內部部署 AlwaysOn 可用性群組延伸至 Azure](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)

如需其他高可用性注意事項，請參閱 [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)。

## 在 Azure 中執行商業智慧、資料倉儲和 OLTP 工作負載   
您可以在 Azure 虛擬機器執行常見的 SQL Server 工作負載。SQL Server 在資源庫中有數個可用的最佳化虛擬機器映像。這包括適用於以下項目的映像：

- [商業智慧](https://msdn.microsoft.com/library/azure/jj992719.aspx)
- [資料倉儲](https://msdn.microsoft.com/library/azure/dn387396.aspx)
- [OLTP](https://msdn.microsoft.com/library/azure/dn387396.aspx)

## 遷移資料

在 SQL Server 虛擬機器啟動並執行之後，您可能想要將現有的資料庫移轉至機器。有數種技巧，但 SQL Server Management Studio 中的部署精靈最適合大部分的案例。如需這些案例的討論和精靈的教學課程，請參閱[將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-migrate-onpremises-database.md)。

## 備份與還原
至於內部部署資料庫，Azure 可以充當次要資料中心，負責儲存 SQL Server 備份檔案。如需備份與還原選項的概觀，請參閱[Azure 虛擬機器中的 SQL Server 備份和還原](virtual-machines-sql-server-backup-and-restore.md)。

[SQL Server 備份至 URL](https://msdn.microsoft.com/library/dn435916.aspx) 會將 Azure 的備份檔案儲存至 Azure Blob 儲存體。[SQL Server 託管備份](https://msdn.microsoft.com/library/dn449496.aspx)可以讓您在 Azure 中安排和保留備份。這些服務可以搭配內部部署 SQL Server 執行個體或 Azure VM 上執行的 SQL Server。Azure VM 也可以利用 SQL Server 專用的[自動備份](virtual-machines-sql-server-automated-backup.md)和[自動化修補](virtual-machines-sql-server-automated-patching.md)功能。

<!---HONumber=August15_HO8-->