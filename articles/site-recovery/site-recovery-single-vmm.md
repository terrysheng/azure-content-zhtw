
<properties
	pageTitle="Azure Site Recovery：複寫 Hyper-V 虛擬機器 (單一 VMM 伺服器)"
	description="Azure Site Recovery 可協調將位於內部部署 VMM 雲端中的虛擬機器複寫、容錯移轉及復原至 Azure 或至次要 VMM 雲端的作業。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="12/01/2015"
	ms.author="raynew"/>

#  Azure Site Recovery：複寫 Hyper-V 虛擬機器 (單一 VMM 伺服器)

Azure Site Recovery 服務提供健全的商務持續性和災害復原 (BCDR) 解決方案，可協調並自動化複寫內部部署實體伺服器及虛擬機器至 Azure，或是次要的內部部署資料中心。本文說明如何在單一 VMM 伺服器部署中，部署 Site Recovery 來保護位於 VMM 雲端中的 Hyper-V 虛擬機器。如果您在閱讀本文後有任何問題，請將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## 概觀

您可以利用下列幾種方式複寫 Hyper-V VM：

- 如果 Hyper-V 主機上的 Hyper-V VM 不在 VMM 雲端中，請將其複寫至 Azure。
- 如果 Hyper-V 主機上的 Hyper-V VM 位於 VMM 雲端中，請將其複寫至 Azure。
- 如果 Hyper-V 主機上的 Hyper-V VM 位於 VMM 雲端中，請將其複寫至 Azure。

不過，如果您想要使用 VMM，但是基礎結構中只有單一 VMM 伺服器，該怎麼辦？

在這種情況下，您有下列幾個選項：

- 將 VMM 雲端中的 Hyper-V VM 複寫至 Azure。[深入了解](site-recovery-vmm-to-azure.md)此案例。
- 在單一 VMM 伺服器上的雲端之間進行複寫。

我們建議第一個選項，因為第二個選項的容錯移轉和復原需要一些手動步驟，較不順暢。


### 在單一的獨立 VMM 伺服器與站台間複寫

在此案例中，您要將單一的獨立 VMM 伺服器部署為主要站台中的虛擬機器，並使用 Site Recovery 和 Hyper-V 複本，將此虛擬機器複寫至次要站台。作法：

1. 在 Hyper-V VM 上設定 VMM。請考慮將 VMM 使用的 SQL Server 執行個體共置在相同的 VM 上。因為只有一個 VM 必須具現化，這樣可以節省時間。如果您想要使用遠端執行個體卻發生中斷，則必須先復原該執行個體後再復原 VMM。
2. VMM 伺服器必須設定至少兩個雲端。其中一個雲端包含您想要複寫的 VM，另一個雲端做為次要位置。包含受保護 VM 的雲端應具備：一或多個 VMM 主機群組，每個主機群組中含有一或多個 Hyper-V 主機伺服器，且每部主機伺服器上至少有一部 Hyper-V 虛擬機器。
2. 建立 Site Recovery 保存庫，產生並下載保存庫註冊金鑰，然後在保存庫中註冊 VMM 伺服器。
2. 在 VMM 的虛擬機器中設定一或多個雲端，然後將含有受保護 VM 的 Hyper-V 主機加入這些雲端。
3. 在 Azure Site Recovery 中，設定這些雲端的保護設定。在來源和目標位置中，指定單一 VMM 伺服器的名稱。如果您設定網路對應，則會針對下列兩處的 VM 網路進行對應：含有受保護 VM 的雲端與您要複寫的雲端。
4. 因為兩個雲端位於相同的伺服器上，您可使用 [透過網路] 做為複寫方法，以啟用受保護 VM 的複寫功能。
4. 使用 Hyper-V 管理員主控台，在包含 VMM VM 的 Hyper-V 主機上啟用 Hyper-V 複本，並啟用 VM 的複寫功能。請不要將 VMM 虛擬機器加入受 Site Recovery 保護的雲端，以確保 Site Recovery 不會覆寫 Hyper-V 複本設定。
5. 如果您想要建立復原方案，請針對來源和目標指定相同的 VMM 伺服器。 

發生中斷時，您可如下所示復原 Hyper-V VM 上的工作負載：

1. 搭配使用 Hyper-V 管理員與規劃的容錯移轉，手動將複本 VMM VM 容錯移轉至次要站台。
2. 復原 VMM VM 之後，您可以從次要站台登入 Hyper-V 復原管理員，並執行從主要站台至次要站台的非計劃性 VM 容錯移轉。
3. 在非計劃性的容錯移轉完成之後，使用者可在主要站台上存取所有資源。

請注意，VMM VM 將必須手動容錯移轉至次要站台，然後工作負載才能進行容錯移轉。

![獨立虛擬 VMM 伺服器](./media/site-recovery-single-vmm/single-vmm-standalone.png)

### 在延伸叢集中的單一 VMM 伺服器與站台間複寫

您不需要將獨立的 VMM 伺服器部署為複寫至次要站台的虛擬機器，而可將其部署為 Windows 容錯移轉叢集中的 VM，以提供工作負載彈性及硬體故障的防護，讓 VMM 發揮高可用性。若要使用 Site Recovery 來部署，應將 VMM 部署至跨地理不同位置之站台的延伸叢集中。作法：

1. 在 Windows 容錯移轉叢集中的虛擬機器上安裝 VMM，然後選取在安裝期間以高度可用性執行伺服器的選項。
2. VMM 所使用的 SQL Server 執行個體應以 SQL Server AlwaysOn 可用性群組來複寫，這樣一來次要站台就會有資料庫的複本。 

如果發生中斷，VMM 伺服器及其對應的 SQL Server 資料庫會容錯移轉並可從次要站台存取。

![叢集虛擬 VMM 伺服器](./media/site-recovery-single-vmm/single-vmm-cluster.png)




 

<!---HONumber=AcomDC_1203_2015-->