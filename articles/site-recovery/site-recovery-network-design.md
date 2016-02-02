<properties
	pageTitle="站台復原的網路基礎結構考量 | Microsoft Azure" 
	description="這篇文章討論利用站台復原進行容錯移轉的實用網路設計考量" 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="12/14/2015" 
	ms.author="raynew"/>

#  站台復原的網路基礎結構考量

Azure Site Recovery 服務提供健全的商務持續性和災害復原 (BCDR) 解決方案，可協調及自動化複寫並容錯移轉至 Azure，或是次要的內部部署資料中心，藉以保護您的內部部署實體伺服器和虛擬機器。

這篇文章的對象是負責架構設計、實作，以及支援 BCDR 方案和基礎結構 (包含 System Center VMM 和 Azure Site Recovery ) 的虛擬化小組。

## 概觀

BCDR 策略的目的是讓您的商務應用程式繼續執行，並還原失敗的工作負載與服務，以便組織可以快速地恢復正常運作。開發災害復原策略有相當的挑戰性，因為難以預測無法預料的事件，以及實作足夠的保護以避免發生大規模的失敗，需要很高的成本。Azure Site Recovery 可協助您保護，並從您的主要資料中心容錯移轉至次要資料中心 (或 Azure)，方法是一開始複製 (複寫) 主要資料，然後定期更新複本。

作為 BCDR 規劃的重要部分，您需要定義您的復原時間目標 (RTO) 和復原點目標 (RPO)，讓您可以使您的組織資料盡快恢復上線 (具有低 RTO) 與最少的資料遺失 (低 RPO)。您組織中設計的網路是 RTO 和 RPO 目標的潛在瓶頸，因此規劃穩定的設計可協助避免發生此瓶頸。


如果您已經決定要使用 Azure Site Recovery 服務來協助實作保護和容錯移轉，則在 BCDR 的網路設計中有幾個重要問題：

- **VMM 設計**：我使用 System Center VMM。我應該如何處理網路設計，來整合 VMM 和站台復原？
- **容錯移轉之後的連線能力**：我使用站台復原來執行容錯移轉。我要如何確保我的應用程式和工作負載可用，而且可在容錯移轉完成之後連接？

## VMM 設計

無論設計是使用單一還是多部 VMM 伺服器，您都可以在您現有的 VMM 設計上方堆放一層站台復原。

### 獨立 VMM 伺服器

在此拓撲中，您將在主要站台中的虛擬機器上部署 VMM 伺服器，然後使用站台復原和 Hyper-V 複本，將此虛擬機器複寫至次要站台。您可能考量在相同的虛擬機器上安裝 VMM 伺服器和其支援的 SQL Server，可以縮短停機時間，因為只有一個 VM 必須具現化。當 VMM 服務使用的是遠端 SQL Server，您必須先復原 SQL Server 執行個體，然後再復原 VMM 伺服器。

若要使用 HYPER-V 複本，在 VM 上部署單一 VMM：

1. 在已安裝 SQL Server 的 VM 上設定 VMM。
2. 將要管理的主機新增到此 VMM 伺服器上的雲端。
3. 登入 Azure 入口網站，然後設定雲端進行保護。
4. 對需要受到 VMM 伺服器保護的所有 VM 啟用複寫。
5. 移至 [HYPER-V 管理員] 主控台，選擇 HYPER-V 複本，然後啟用 VMM VM 上的複寫。
6. 確定 VMM VM 不會新增至受到 ASR 服務保護的雲端，以便 ASR 不會覆寫 HYPER-V 複寫設定。

發生災害時，可依下列方式復原工作負載：

1. 使用 HYPER-V 管理員，將複本 VMM VM 容錯移轉至復原站台。
2. 在復原了 VMM VM 之後，使用者可以從次要站台登入 HYPER-V 復原管理員。
3. 在非計劃性的容錯移轉完成之後，使用者可在主要站台上存取所有資源。
4. 請注意，VMM VM 將必須手動容錯移轉至次要站台，然後工作負載才能進行容錯移轉。 


### 叢集化的 VMM 伺服器


[在叢集中部署 VMM](https://technet.microsoft.com/library/gg610675.aspx) 提供高可用性和保護，以防範硬體容錯移轉。如果您使用站台復原，部署您的 VMM 叢集，請注意：

應該在延伸的叢集中跨地理上不同位置的站台部署 VMM 伺服器。VMM 所使用的 SQL Server 資料庫應該使用複本位於次要站台的 SQL Server AlwaysOn 可用性群組保護。如果發生災害，VMM 伺服器和其對應的 SQL Server 將自動容錯移轉至復原站台。然後，您可以使用站台復原，容錯移轉工作負載。

![延伸的 VMM 叢集](./media/site-recovery-network-design/ASR_NetworkDesign1.png)

## 容錯移轉之後的連線能力

現代應用程式幾乎一律相依於網路至某種程度，因此實際將某個站台中的服務移至另一個站台服務代表一種站台挑戰，以確定應用程式和工作負載可在容錯移轉之後連接。這個問題有兩個解決方案：

- **固定 IP 位址**：第一種方法是要維持固定 IP 位址。儘管移動服務和主控伺服器開始於不同的實體位置，但是應用程式會將 IP 位址組態連同它們帶至新位置。
- **修改的 IP 位址**：第二種方法需要在轉換為復原的站台期間變更 IP 位址。 

### 選項 1：固定 IP 位址

從災害復原的觀點來看，使用固定 IP 位址似乎是最簡單的實作方法。不過，有許多潛在挑戰，實際上讓它成為最不常使用的方法。Azure Site Recovery 可讓您保留所有案例中的 IP 位址。在您決定採取這種方法之前，請讓我們看看主要案例的考量 (延伸的子網路和子網路容錯移轉)。

#### 延伸的子網路

在延伸的子網路中，可在主要位置和目標位置同時使用子網路。簡單地說，這表示您可以將伺服器和其 IP (第 3 層) 組態移至次要網台，而且網路會自動將流量路由傳送至新位置。從伺服器的觀點來看，這是微不足道的處理，但它有幾項挑戰：



- 從第 2 層 (資料連結層) 觀點來看，它需要可以管理延伸的 VLAN 的網路設備，不過，這種類型的設備現在已普遍使用。
- 第二個和更困難的問題是，透過延伸 VLAN，潛在的容錯網域會延伸到兩個站台，基本上成為單一失敗點。然而，不太可能發生廣播風暴啟動，而且無法隔離。我們已看到關於此問題的混合意見，範圍從成功實作到「從未」。
- 如果您是容錯移轉至 Azure 做為您的 DR 站台，則延伸的子網路一可行。

#### 子網路容錯移轉

可以實作子網路容錯移轉，以便取得延伸的子網路的優點，而無需實際延伸它。在此組態中，任何給定的子網路會呈現在站台 1 或站台 2 中，但永遠不會同時呈現在這兩個站台中。若要在發生容錯移轉時維持 IP 位址空間，您可以程式設計方式排列路由器基礎結構，將子網路從某個站台移到另一個站台。在容錯移轉案例中，子網路會與其相關聯的受保護 VM 一起移動。這種方法的主要缺點是，如果失敗，您必須移動整個子網路，這可能是合理的解決方案，但是可能會影響容錯移轉的細微性考量。

讓我們看看虛構企業 (Contoso) 如何能夠將其 VM 複寫到復原位置，同時又能夠容錯移轉整個子網路。我們將看看 Contoso 如何能夠管理它的子網路，同時又在兩個內部部署位置之間複寫 VM，以及討論當 Azure 做為災害復原站台時，子網路容錯移轉如何運作。

##### 範例 - 企業中的子網路容錯移轉
 
- 主要站台有在子網路 192.168.1.0/24 中執行的應用程式。
- 整個子網路將容錯移轉，而且子網路中的所有虛擬機器將會容錯移轉至復原站台，並保留其 IP 位址
- 如下圖所示，主要站台與復原站台、第三個站台與主要站台，以及第三個站台與復原站台之間的路由將必須適當地修改，以反映所有屬於子網路 192.168.1.0/24 的虛擬機器都已都移至復原站台的事實。
- 此圖假設：
	-  每個資料中心是由自己的 VMM 執行個體服務。資料中心之間將沒有 System Center VMM 資料庫的複寫。
	-  每個資料中心會使用虛擬機器的靜態 IP 位址。
	-  資料中心之間的連線是透過專用電路，而不是透過經由網際網路的 VPN 連線。

**容錯移轉之前**

![容錯移轉之前](./media/site-recovery-network-design/ASR_NetworkDesign2.png)

**容錯移轉之後**

![容錯移轉之後](./media/site-recovery-network-design/ASR_NetworkDesign3.png)


當啟用特定虛擬機器的保護時，站台復原會配置網路資源，如下所示：

1. 站台復原會從相關網路上針對每個 VMM 執行個體定義的靜態 IP 位址集區配置虛擬機器上每個網路介面的 IP 位址。
2. 如果系統管理員在復原站台上定義相同的 IP 位址集區，用於網路的主要站台，則站台復原會將配置給主要虛擬機器的相同 IP 位址配置給複本虛擬機器。IP 位址會保留在 VMM 中，但不會設定為容錯移轉 IP 位址。容錯移轉 IP 位址只會在容錯移轉之前設定。這個螢幕擷取畫面顯示複本虛擬機器的容錯移轉 TCP/IP 設定 (在 HYPER-V 主控台上)。這些設定只會在虛擬機器的容錯移轉啟動之間複寫。

	![設定 IP 位址](./media/site-recovery-network-design/ASR_NetworkDesign4.png)

3. 如果相同的 IP 位址無法使用，則站台復原將從集區配置不同的位址。
4. 在啟用虛擬機器進行保護之後，您可以使用下列範例指令碼，驗證已配置給虛擬機器的 IP 位址。相同的 IP 位址會設定為容錯移轉 IP 位址，並在容錯移轉時指派給 VM。

    $vm = Get-SCVirtualMachine -Name $na = $vm[0].VirtualNetworkAdapters $ip = Get-SCIPAddress -GrantToObjectID $na[0].id $ip.address

請注意，如果虛擬機器使用 DHCP，則 IP 位址管理不是由站台復原處理。您必須確保復原站台上配置 IP 位址的 DHCP 伺服器，可以從與主要站台相同的範圍中配置位址。

##### 範例 - 子網路容錯移轉至 Azure

如果您是容錯移轉至 Azure，有幾個條件約束。讓我們看看具有內部部署基礎結構，裝載企業營運系統應用程式的虛構公司 (Woodgrove Bank)，以及裝載其行動裝置應用程式的 Azure。


- Azure 中的 Woodgrove Bank VM 與內部部署伺服器之間的連線能力是透過站對站 VPN，其會將 Azure asn 中的虛擬網路顯示為內部部署網路上 Woodgrove Bank 的延伸。 
- Woodgrove 想要使用站台復原，將其內部部署工作負載複寫到 Azure。 
- Woodgrove 必須處理相依於硬式編碼的 IP 位址的應用程式和組態，因此在容錯移轉至 Azure 之後，它們需要保留其應用程式的 IP 位址。
- Woodgrove 的內部部署基礎結構是由 VMM 2012 R2 伺服器管理。
- 有 VLAN 架構邏輯網路 (應用程式網路) 已建立在 VMM 伺服器上。![邏輯網路](./media/site-recovery-network-design/ASR_NetworkDesign5.png)
- VM 網路 (應用程式 VM 網路) 是使用邏輯網路建立的。![VM 網路](./media/site-recovery-network-design/ASR_NetworkDesign6.png)
- 應用程式中的所有虛擬機器都會使用靜態 IP 位址，因此也會針對邏輯網路定義靜態 IP 集區。 
- Woodgrove 會將來自 IP 位址範圍 (172.16.1.0/24、172.16.2.0/24) 的 IP 位址指派給其在 Azure 中執行的資源。


Woodgrove 若要能夠部署複寫和維護 IP 位址，需要以下項目：

- 內部部署網路延伸的 Azure 虛擬機器，讓應用程式可以順暢地容錯移轉。
- 請注意，當您在 Azure 中設定站對站連線時，只在 IP 位址範圍不同於內部部署 IP 位址範圍時，Azure 網路才可讓您將流量路由傳送到內部部署位置 (Azure 將它稱為本機網路)，因為 Azure 不支援延伸子網路。這表示，如果您有子網路 192.168.1.0/24 內部部署，則您無法在 Azure 網路中加入本機網路 192.168.1.0/24。這是預期行為，因為 Azure 不知道子網路中沒有任何作用中的 VM，也不知道子網路是針對 DR 用途而建立的。若要能夠從 Azure 網路正確地路由傳送網路流量，網路和本機網路中的子網路不得發生衝突。 
- 我們將需要在 Azure (復原網路) 中建立額外的網路，將在這裡建立容錯移轉的 VM。

	![Azure 網路](./media/site-recovery-network-design/ASR_NetworkDesign7.png)

- 若要確保 VM 的 IP 位址保留下來，我們將在站台復原中的 VM 屬性指定應該使用相同的 IP 位址。然後，在容錯移轉之後，站台復原就會將指定的 IP 位址指派給 VM。![Azure 網路](./media/site-recovery-network-design/ASR_NetworkDesign8.png)


- 當觸發容錯移轉，並在復原網路中以必要的 IP 位址建立 VM 時，可以使用下列項目建立 VM 的連線能力：這個動作可以編寫指令碼。如同我們在關於子網路容錯移轉的前一節所討論，如果容錯移轉至 Azure，路由也須適當地修改，以反映該 192.168.1.0/24 現在已移至 Azure。![Azure 網路](./media/site-recovery-network-design/ASR_NetworkDesign9.png)

### 選項 2：修改的 IP 位址

這種方法似乎是最常見，而且表示將容錯移轉的每個 VM 的 IP 位址會變更。這個方法的主要缺點是，您的網路基礎結構需要知道 IP 位址已變更，而且在整個網路中，DNS 項目通常必須變更或排清，如同網路資料表中的快取項目一般。這會造成停機時間，取決於 DNS 基礎結構的設定方式而定。在內部網路應用程式的情況下使用低 TTL 值，以及對網際網路架構應用程式使用 [Azure 流量管理員與站台復原](https://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)，即可緩和這些問題。

#### 範例：修改的 IP 位址

讓我們看看這個案例，其範例具有第三個站台，可以從中存取主要或復原站台上裝載的應用程式。

![Azure 網路](./media/site-recovery-network-design/ASR_NetworkDesign10.png)

- 有些應用程式裝載在主要站台上的子網路 192.168.1.0/24 中，而且它們已設定為在容錯移轉後出現在子網路 172.16.1.0/24 的復原站台上。
- 已適當地設定 VPN 連線/網路路由，讓所有的三個站台可以互相存取。 
- 在容錯移轉一或多個應用程式之後，它們會還原在復原子網路中。在此情況下，我們不需要同時容錯移轉整個子網路，而且不需任何變更，即可重新設定 VPN 或網路路由。
- 容錯移轉和某些 DNS 更新會保留應用程式可供存取。如果 DNS 伺服器設定為允許動態更新，則虛擬機器一旦在容錯移轉之後啟動，就會使用新的 IP 位址會自行註冊。

	![Azure 網路](./media/site-recovery-network-design/ASR_NetworkDesign11.png)

- 在容錯移轉之後，複本虛擬機器具有的 IP 位址可能與主要虛擬機器的 IP 位址不同。
- 虛擬機器在啟動後將更新他們正在使用的 DNS 伺服器。在整個網路中，DNS 項目通常需要變更或排清，而且網路資料表中的快取項目必須更新或排清，因此這些狀態變更發生時面臨停機時間，不是罕見的情況。緩和方式如下：

	- 對內部網路應用程式使用低 TTL 值。
	- 使用 [Azure 流量管理員與站台復原] (https://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/，適用於網際網路架構應用程式/)。
	- 在您的復原計劃內使用下列指令碼來更新 DNS 伺服器，以確保及時更新 (如果已設定動態 DNS 登錄，不需要指令碼)

    [string]$Zone, [string]$name, [string]$IP ) $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name $newrecord = $record.clone() $newrecord.RecordData[0].IPv4Address = $IP Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

#### 範例 - 容錯移轉至 Azure

Azure 做為災害復原站台的網路基礎結構設定[部落格文章](https://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/)說明如何在保留 IP 位址不是必要時，設定必要的 Azure 網路基礎結構。一開始描述應用程式，接著探討如何在內部部署及 Azure 設定網路。最後指示如何執行測試容錯移轉和計劃的容錯移轉。

## 後續步驟

[了解](site-recovery-network-mapping.md)站台復原如何對應來源及目標網路。

<!---HONumber=AcomDC_0128_2016-->