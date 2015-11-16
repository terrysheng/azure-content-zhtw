<properties
    pageTitle="如何針對 Azure RemoteApp 集合規劃您的虛擬網路 | Microsoft Azure"
    description="了解如何針對 Azure RemoteApp 集合規劃您的虛擬網路。"
    services="remoteapp"
    documentationCenter="" 
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/11/2015"
    ms.author="elizapo" />

# 如何針對 Azure RemoteApp 規劃您的虛擬網路

本文件說明如何針對 Azure RemoteApp 設定 Azure 虛擬網路 (VNET) 和子網路。如果您不熟悉 Azure 虛擬網路，這可協助您將網路基礎結構虛擬化至雲端，以及利用 Azure 和內部部署資源建立混合式解決方案。您可以在[此處](virtual-networks-overview.md)閱讀相關資訊。

如果您要在您部署 Azure RemoteApp 的虛擬網路中定義流量 (內送和外寄) 的安全性原則，我們強烈建議您在 Azure 虛擬網路中為 Azure RemoteApp 建立與您其餘的部署不同的子網路。如需有關如何在 Azure 虛擬網路子網路上定義安全性原則的詳細資訊，請參閱 [什麼是網路安全性群組 (NSG)？](virtual-networks-nsg.md)。

## 具有 Azure 虛擬網路的 Azure RemoteApp 集合類型

下圖顯示您要使用虛擬網路時的兩個不同集合選項。

### 具有 VNET 的 Azure RemoteApp 雲端集合

 ![Azure RemoteApp - 具有 VNET 的雲端集合](./media/remoteapp-planvpn/ra-cloudvpn.png)

這表示 Azure RemoteApp 集合，其中 RemoteApp 工作階段主機需要存取的所有資源都已部署在 Azure 中。也可以在 Azure 中與 RemoteApp VNET 相同的 VNET 或不同的 VNET 中。

### 具有 VNET 的 RemoteApp 混合式集合

![Azure RemoteApp - 具有 VNET 的混合式集合](./media/remoteapp-planvpn/ra-hybridvpn.png)

這表示 Azure RemoteApp 集合，其中 RemoteApp 工作階段主機需要存取的某些資源已在內部部署。RemoteApp VNET 會使用 Azure 混合式技術 (像是站對站 VPN 或 Express Route) 連結到內部部署網路。


## 系統的運作方式

實際上，Azure RemoteApp 會將 Azure 虛擬機器 (與您上傳的映像) 部署到您在佈建期間所選擇的虛擬網路子網路。如果您選擇了混合式集合，我們會嘗試解析您在佈建工作流程中輸入的網域控制站的 FQDN，以及虛擬網路中提供的 DNS 伺服器。如果您要連接到現有的虛擬網路，請務必公開 Azure RemoteApp 子網路的網路安全性群組中的必要連接埠。

我們建議您改用 [Azure RemoteApp 夠大的子網路](remoteapp-vnetsizing.md)。Azure 虛擬網路最大支援 /8 (使用 CIDR 子網路定義)。當更多使用者存取應用程式時，您的子網路在相應增加期間應足以容納所有的 Azure RemoteApp VM。

以下是您必須在虛擬網路子網路上啟用的事項：

2.	連接埠範圍 10101-10175 應允許來自子網路的輸出流量，才能與其中一個內部 Azure RemoteApp 服務通訊。
3.	輸出流量應可來自您的子網路以連接到連接埠 443 上的 Azure 儲存體
4.	如果您有 Azure 中裝載的 Active Directory，請確定 Azure RemoteApp 的虛擬網路子網路內的任何 VM 都能夠連接到該網域控制站。虛擬網路中的 DNS 應該能夠解析此網域控制站的 FQDN。


## 具有強制通道的虛擬網路

現在所有新的 Azure RemoteApp 集合都支援[強制通道](vpn-gateway-about-forced-tunneling.md)。我們目前不支援現有集合的移轉，而支援強制通道。您必須使用您要連結至 Azure RemoteApp 的 VNET 來刪除所有現有的集合，並建立新的集合以在集合上啟用強制通道。

<!---HONumber=Nov15_HO2-->