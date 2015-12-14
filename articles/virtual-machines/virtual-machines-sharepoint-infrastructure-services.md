<properties
	pageTitle="Azure 中的 SharePoint Server 2013 伺服器陣列 | Microsoft Azure"
	description="找說明如何在 Microsoft Azure 中設定開發/測試環境或生產的 SharePoint Server 2013 伺服器陣列的文章。"
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# 在 Azure 基礎結構服務中架設的 SharePoint 伺服器陣列

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../../includes/learn-about-deployment-models-both-include.md)]

在 Microsoft Azure 基礎結構服務中，設定第一個或下一個開發/測試或實際執行 SharePoint 伺服器陣列，這樣您就可以利用簡化設定的優點，還能夠快速擴大伺服器陣列，使其增加新的容量或最佳化關鍵功能。

> [AZURE.NOTE]Microsoft 已發行 SharePoint Server 2016 IT 預覽版。若要輕鬆安裝和測試此預覽版，您可以搭配 SharePoint Server 2016 IT 預覽版和其預先安裝的必要元件來使用 Azure 虛擬機器資源庫映像。如需詳細資訊，請參閱[在 Azure 中測試 SharePoint Server 2016 IT Preview](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/)。

## 基本的 SharePoint 開發/測試伺服器陣列

這個自動建立的環境包含純雲端 Azure 虛擬網路中的三部伺服器：網域控制站、SQL Server 和 SharePoint 伺服器。

請參閱 Azure 入口網站的 Azure Marketplace 中的 [SharePoint 2013 非 HA 伺服器陣列](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)項目。這會為連結網際網路的 SharePoint 網站建立基本開發/測試伺服器陣列。如需其他詳細資訊，請參閱[建立 SharePoint 伺服器陣列](virtual-machines-sharepoint-farm-azure-preview.md)。

您也可以使用 Azure 資源管理員範本。請參閱[部署一個包含三部伺服器的 SharePoint 伺服器陣列](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm)。

> [AZURE.NOTE]Azure 入口網站的 Azure Marketplace 中的 **SharePoint 伺服器陣列**項目已移除。

## 高可用性 SharePoint 開發/測試伺服器陣列

這個自動建立的環境包含純雲端 Azure 虛擬網路中的九部伺服器：兩部用於網域控制站、三部用於 SQL Server 叢集、兩部應用程式層的 SharePoint 伺服器，以及兩部 Web 層的 SharePoint 伺服器。

請參閱 Azure 入口網站的 Azure Marketplace 中的 [SharePoint 2013 HA 伺服器陣列](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)項目。這會為網際網路對向的 SharePoint 網站建立高可用性的開發/測試伺服器陣列。如需其他詳細資訊，請參閱[建立 SharePoint 伺服器陣列](virtual-machines-sharepoint-farm-azure-preview.md)。

您也可以使用 Azure 資源管理員範本。請參閱[部署一個包含九部伺服器的 SharePoint 伺服器陣列](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm)。

> [AZURE.NOTE]Azure 入口網站的 Azure Marketplace 中的 **SharePoint 伺服器陣列**項目已移除。

## 混合式雲端開發/測試伺服器陣列

利用 [混合式雲端開發/測試環境中的 SharePoint 內部網路伺服器陣列](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), ，您可以建立模擬的混合式雲端設定 (就可以架設簡易雙層式 SharePoint 伺服器陣列)，用來從您在網際網路上的位置，測試 Azure 中架設的內部網路 SharePoint 伺服陣列。

這個組態會使用傳統部署模型。

## 高可用性、內部網路 SharePoint 實際執行伺服器陣列

部署 [具有 Azure 中 SQL Server AlwaysOn 可用性群組的 SharePoint 2013](virtual-machines-workload-intranet-sharepoint-overview.md) 後，即表示您在 Azure 中建置了可立即實際執行、高可用性的內部網路 SharePoint Server 2013 伺服器陣列。

這個組態會使用傳統部署模型。

## 其他資源

[適用於 SharePoint 2013 的 Microsoft Azure 架構](https://technet.microsoft.com/library/dn635309.aspx)

[使用 SharePoint Server 2013 之 Microsoft Azure 中的網際網路網站](https://technet.microsoft.com/library/dn635307.aspx)

[Microsoft Azure 中的 SharePoint Server 2013 災害復原](https://technet.microsoft.com/library/dn635313.aspx)

[使用 Microsoft Azure Active Directory 進行 SharePoint 2013 驗證](https://technet.microsoft.com/library/dn635311.aspx)

[在 Microsoft Azure 中部署 Office 365 目錄同步作業 (DirSync)](https://technet.microsoft.com/library/dn635310.aspx)

<!---HONumber=AcomDC_1203_2015-->