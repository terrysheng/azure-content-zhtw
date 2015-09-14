<properties 
   pageTitle="如何從同質群組移轉至區域虛擬網路 (VNet)"
	description="了解如何從同質群組移轉至區域 VNet"
	services="virtual-network"
	documentationCenter="na"
	authors="telmosampaio"
	manager="carolz"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/02/2015"
	ms.author="telmos"/>

# 如何從同質群組移轉至區域虛擬網路 (VNet)

您可以使用同質群組，以確保在相同同質群組內建立的資源由伺服器所實際主控，這些伺服器彼此鄰近，可讓這些資源進行更快速的通訊。在過去，建立虛擬網路 (VNet) 需要同質群組。當時，管理 VNet 的網路管理員服務僅能在一組實體伺服器或縮放單位內工作。架構改進則增加區域的網路管理範圍。

因為這些架構的改進，造成同質群組不再建議使用，或虛擬網路不再需要。針對 VNet 使用的同質群組現在已取代為區域。與區域相關聯的 VNet 稱為區域 VNet。

此外，我們建議您在一般情況下不要使用同質群組。除了 VNet 的需求，過去也必須使用同質群組來確保資源，例如放置在彼此鄰近位置的計算和儲存體。不過，使用目前的 Azure 網路架構時，這些放置需求不再必要。請參閱＜[同質群組和 VM](#Affinity-groups-and-VMs)＞以查看您可能要使用同質群組的少數剩餘特定情況。

## 建立和移轉至區域 VNet

從現在開始，建立新的 VNet 時，將使用*區域*。您會在管理入口網站中看到這個選項。請注意，在網路組態檔中，這會顯示為*位置*。

>[AZURE.IMPORTANT]雖然技術上來說仍然可以建立與同質群組相關聯的虛擬網路，但是這樣做沒有令人信服的理由。許多新功能，例如網路安全性群組在使用區域 VNet 時才能使用，且不適用於與同質群組相關聯的虛擬網路。

### 關於目前與同質群組相關聯的 VNet

目前與同質群組相關聯的 VNet 可移轉至區域 VNet。若要移轉至區域 VNet，請遵循下列步驟：

1. 匯出網路組態檔。您可以使用 PowerShell 或管理入口網站。如需使用管理入口網站的指示，請參閱＜[使用網路組態檔設定 VNet](../virtual-networks-using-network-configuration-file/)＞。

1. 編輯網路組態檔，並以新值取代舊值。

	> [AZURE.NOTE] **位置**是您所指定與 VNet 相關聯的同質群組區域。例如，如果 VNet 與位於美國西部的同質群組相關聯，則當您移轉時，您的位置必須指向美國西部。
	
	在網路組態檔中編輯下列幾行，並取代為您需要的值：

	**舊值：**<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG">

	**新值：**<VirtualNetworkSitename="VNetUSWest" Location="West US">

1. 儲存您的變更並網路組態[匯入](../virtual-networks-using-network-configuration-file/)至 Azure。

>[AZURE.INFO]這項移轉不會對您的服務造成任何停機時間。

## 同質群組和 VM

如先前所述，同質群組通常不再建議針對 VM 來使用。您應該僅在一組 VM 與 VM 之間必須具有絕對最低的網路延遲時使用同質群組。將 VM 放置在同質群組中，則 VM 會放置在相同的計算叢集或縮放單位。

請務必注意使用同質群組可能會有兩個負面的結果：

- VM 大小的集合會受限於計算縮放單位所提供 VM 大小的集合。

- 會有更高的可能性無法配置新的 VM。當同質群組的特定縮放單位超出容量時會發生這種情況。

### 如果您在同質群組中擁有 VM 時該怎麼辦

目前在同質群組中的 VM 不需要從同質群組中移除。

一旦部署 VM，其會部署至單一的縮放單位。同質群組可針對新的 VM 部署限制可用的 VM 大小集合，但任何已部署的現有 VM 部署，已在 VM 所部署的位置限制縮放單位中可用的 VM 大小集合。因此，從同質群組中移除 VM 不會產生任何效果。
 

<!---HONumber=September15_HO1-->