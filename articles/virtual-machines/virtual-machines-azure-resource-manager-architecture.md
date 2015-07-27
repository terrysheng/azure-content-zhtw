<properties
   pageTitle="Azure 資源管理員架構"
   description="了解資源管理員的架構，以及計算、網路和儲存體資源提供者之間的關聯性。"
   services="virtual-machines"
   documentationCenter=""
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
	ms.date="07/07/2015" 
	ms.author="josephd"/>

# Azure 資源管理員架構

本文提供服務管理與資源管理員架構的概觀，可用來建立以基礎結構為基礎的應用程式和工作負載。

## 服務管理的架構

在討論 Azure 資源管理員的架構以及各種資源提供者之前，讓我們來檢視目前存在的 Azure 服務管理架構。在 Azure 服務管理中，用以裝載虛擬機器的計算、儲存體或網路資源是透過下列方式來提供：

- 用來做為裝載虛擬機器 (計算) 之容器所需的雲端服務。虛擬機器是利用網路介面卡 (NIC) 和 Azure 所指派的 IP 位址自動提供。此外，雲端服務包含外部負載平衡器執行個體、共用 IP 位址及預設端點，以允許 Windows 架構虛擬機器的遠端桌面與遠端 PowerShell 流量，以及 Linux 架構虛擬機器的安全殼層 (SSH) 流量。
- 儲存虛擬機器之 VHD 所需的儲存體帳戶，包括作業系統、暫存磁碟及其他資料磁碟 (儲存體)。
- 可用來做為額外容器的選擇性虛擬網路，您可以在其中建立子網路的結構，並指定虛擬機器所在的子網路 (網路)。

以下是 Azure 服務管理的元件及其關聯性。

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)
 
## 資源管理員的架構 

針對 Azure 資源管理員，資源提供者支援可用來在您所需的設定中建立可運作虛擬機器的個別資源。針對虛擬機器，有三個主要的資源提供者：

- 計算資源提供者 (CRP)：支援虛擬機器和選擇性可用性設定組的執行個體。
- 儲存體資源提供者 (SRP)：支援儲存虛擬機器之 VHD 所需的儲存體帳戶，包括它們的作業系統和其他資料磁碟。
- 網路資源提供者 (NRP)：支援必要的 NIC、虛擬機器 IP 位址及虛擬網路內的子網路，以及選擇性的負載平衡器、負載平衡器 IP 位址與網路安全性群組。

此外，位於資源提供者內的資源之間具有關聯性：

- 虛擬機器取決於定義於 SRP 的特定儲存體帳戶，以將其磁碟儲存於 Blob 儲存體中。
- 虛擬機器會參考定義於 NRP 的特定 NIC (必要)，以及定義於 CRP 的可用性設定組 (選擇性)。
- NIC 會參考虛擬機器指派的 IP 位址 (必要)、虛擬機器之虛擬網路的子網路 (必要)，以及網路安全性群組 (選擇性)。
- 虛擬網路內的子網路會參考網路安全性群組 (選擇性)。
- 負載平衡器執行個體會參考 IP 位址的後端集區，其中包含虛擬機器的 NIC (選擇性)，以及參考負載平衡器的公用或私人 IP 位址 (選擇性)。

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch2.png)
 
設定 Azure 中裝載之 IT 工作負載的基礎結構時，將資源元件化可具備更多彈性。Azure 資源管理員範本會善用此彈性，來建立特殊設定所需的相依資源組。執行範本時，資源管理員可確保系統會以正確的順序建立設定的資源，以保留相依性和參考。例如，資源管理員將不會在建立含有子網路與 IP 位址的虛擬網路之前建立虛擬機器的 NIC (網路安全性群組是選擇性的)。

資源群組是一個邏輯容器，可保留應用程式的相關資源，其中可包含多個虛擬機器、NIC、IP 位址、負載平衡器、子網路和網路安全性群組。例如，您可以將應用程式的所有資源當成單一管理單位來管理。您可以一併建立、更新和刪除它們全部。以下是部署於單一資源群組中的應用程式範例。

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)
 
這個應用程式包含下列各項：

- 兩部虛擬機器，它們使用相同的儲存體帳戶、位於相同的可用性設定組中，且位於同一個虛擬網路的子網路上。
- 適用於每部虛擬機器的單一 NIC 和 VM IP 位址。
- 一個外部負載平衡器，可將網際網路流量分散至兩部虛擬機器的 NIC。

此應用程式的所有這些資源都是透過包含它們的單一資源群組來管理。

您也可以在使用 Azure PowerShell 或 Azure CLI 建立資源管理員架構的虛擬機器時，查看資源之間的元件化與相依關聯性。在您能夠執行命令來建立虛擬機器之前，必須先建立資源群組、儲存體帳戶、含有子網路的虛擬網路，以及具有 IP 位址的 NIC。如需詳細資訊，請參閱[利用資源管理員和 Azure PowerShell 建立及預先設定 Windows 虛擬機器](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)。

## 後續步驟

[使用 Azure Resource Manager 範本和 Azure CLI 部署和管理虛擬機器](virtual-machines-deploy-rmtemplates-azure-cli.md)

[使用資源管理員範本和 PowerShell 部署以及管理 Azure 虛擬機器](virtual-machines-deploy-rmtemplates-powershell.md)

## 其他資源

[Azure Resource Manager 提供的 Azure 運算、網路和儲存提供者](virtual-machines-azurerm-versus-azuresm.md)

[Azure 資源管理員概觀](resource-group-overview.md)

<!---HONumber=July15_HO2-->