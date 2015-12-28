<properties
	pageTitle="以 Azure Site Recovery 保護 Active Directory 和 DNS | Microsoft Azure" 
	description="本文說明如何使用 Azure Site Recovery 實作 Active directory 的災害復原解決方案。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="12/14/2015" 
	ms.author="pratshar"/>

# 以 Azure Site Recovery 保護 Active Directory 和 DNS

企業應用程式，例如 SharePoint、Dynamics AX 和 SAP 都依存於 Active Directory 和 DNS 基礎結構才能正確運作。當您建立應用程式的災害復原解決方案時，請務必記住，您必須先保護及復原 Active Directory 和 DNS，再保護及復原其他應用程式元件，以確保災害發生時每樣東西能正常運作。

Site Recovery 是一項 Azure 服務，藉由協調虛擬機器的複寫、容錯移轉及復原，提供災害復原功能。Site Recovery 支援多種複寫案例，可持續保護及順暢地容錯移轉虛擬機器和應用程式至私密/公用或主機服務提供者的雲端。

使用 Site Recovery，您可以針對 Active Directory 建立一個完整的自動化災害復原計畫。當中斷發生時，您可以在幾秒鐘內從任何地方起始容錯移轉，並在數分鐘內啟動並執行 Active Directory。如果您已在主要網站針對多個應用程式 (例如 SharePoint 和 SAP) 部署 Active Directory，且您想要執行整個網站的容錯移轉，您可以先使用 Site Recovery 容錯移轉 Active Directory，再使用應用程式專用的復原計劃容錯移轉其他應用程式。

本文說明如何建立 Active Directory 的災害復原解決方案、如何使用單鍵復原計劃執行計劃/未計劃/測試容錯移轉，以及執行支援的組態和必要條件。開始之前，您應該先熟悉 Active Directory 與 Azure Site Recovery。

根據環境的複雜度，有兩個建議的選項。

### 選項 1

如果您有少數的應用程式和單一網域控制站，而且您想要容錯移轉整個網站，我們建議您使用 Site Recovery 將網域控制站複寫至次要網站 (無論您要容錯移轉至 Azure 或次要網站)。相同的複寫的虛擬機器也可以用於測試容錯移轉。

### 選項 2

如果您有大量的應用程式且在環境中有一個以上的網域控制站，或您打算一次容錯移轉幾個應用程式，我們建議您除了使用 Site Recovery 複寫網域控制站虛擬機器外，也在目標網站 (Azure 或內部部署資料中心上的次要網站) 上設定其他網域控制站。

>[AZURE.NOTE]即使您實作第 2 個選項來執行測試容錯移轉，您仍然需要使用 Site Recovery 複寫網域控制站。如需詳細資訊，請詳閱[測試容錯移轉考量](#considerations-for-test-failover)。


下列各節說明如何在 Site Recovery 中的網域控制站上啟用保護，以及如何在 Azure 中設定網域控制站。


## 必要條件

- 內部部署 Active Directory 和 DNS 伺服器。
- 在 Microsoft Azure 訂用帳戶中有 Azure Site Recovery 服務保存庫。 
- 如果您複寫至 Azure，請在 VM 上執行 Azure 虛擬機器整備評估工具，以確保它們與 Azure VM 和 Azure Site Recovery 服務相容。


## 使用 Site Recovery 啟用保護


### 保護虛擬機器

在 Site Recovery 中啟用網域控制站/DNS 虛擬機器的保護。根據虛擬機器類型 (Hyper-V 或 VMware) 設定 Site Recovery。我們建議 15 分鐘的當機一致複寫頻率。

###設定虛擬機器的網路設定

對於網域控制站/DNS 虛擬機器，在 Site Recovery 中設定網路設定，讓 VM 在容錯移轉之後附加至正確的網路。例如，如果您將 Hyper-V VM 複寫至 Azure，您可以選取 VMM 雲端或保護群組中的 VM，並設定網路設定如下

![VM 網路設定](./media/site-recovery-active-directory/VM-Network-Settings.png)

## 以 Active Directory 複寫保護 Active Directory 

### 站對站保護

將伺服器提升至網域控制站角色時，請在次要網站上建立網域控制站，並指定主要網站使用的同一網域的名稱給它。您可以使用 **Active Directory 網站和服務**嵌入式管理單元來設定網站要新增至的網站連結物件上的設定。藉由設定網站連結，您可以控制兩個以上的網站之間的複寫發生的時間和頻率。請參閱[排程網站之間的複寫](https://technet.microsoft.com/library/cc731862.aspx)以取得詳細資訊。

###站對 Azure 保護

遵循指示以[在 Azure 虛擬網路中建立網域控制站](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md)。當您將伺服器提升至網域控制站角色時，指定在主要網站中使用的同一網域的名稱。

然後[重新設定虛擬網路的 DNS 伺服器](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)以在 Azure 中使用此 DNS 伺服器。
  
![Azure 網路](./media/site-recovery-active-directory/azure-network.png)

## 測試容錯移轉考量

測試容錯移轉是在與生產網路隔離的網路中發生，因此不會影響生產工作負載。

大部分應用程式也需要網域控制站和 DNS 伺服器的存在，因此在應用程式容錯移轉之前，必須在隔離的網路中建立網域控制站以用於測試容錯移轉。若要這麼做，最簡單的方式是在執行應用程式復原計劃的測試容錯移轉之前，先使用 Site Recovery 在網域控制站/DNS 虛擬機器上啟用保護，並執行虛擬機器的測試容錯移轉。以下是做法：

1. 在 Site Recovery 中啟用網域控制站/DNS 虛擬機器的保護。
2. 建立隔離的網路。在 Azure 中建立的任何虛擬網路預設是與其他網路隔離。建議此網路的 IP 範圍使用與您的生產網路相同的 IP 範圍。請勿在此網路上啟用網站對網站連線能力。
3. 提供建立之網站的 DNS IP 位址，做為您預期 DNS 虛擬機器取得的 IP 位址。如果您是複寫到 Azure，則提供 VM 的 IP 位址，該 VM 將會用於 VM 屬性中 [目標 IP] 設定的容錯移轉。如果您是複寫到其他內部部署網站，且您是使用 DHCP，請遵循指示來[針對測試容錯移轉設定 DNS 和 DHCP](site-recovery-failover.md#prepare-dhcp)。 

>[AZURE.NOTE]如果 IP 位址可用於測試容錯移轉網路，在測試容錯移轉期間配置給虛擬機器的 IP 位址，與它在執行計劃或未計劃的容錯移轉時得到的 IP 位址相同。如果不可用，則虛擬機器收到的 IP 位址與測試容錯移轉網路中所用的不同。

4. 在網域控制站虛擬機器上，在隔離網路中執行它的測試容錯移轉。 
5. 執行應用程式復原計畫的測試容錯移轉。
6. 測試完成之後，在 Site Recovery 入口網站的 [作業] 索引標籤中，將網域控制站虛擬機器和復原計劃的作業的測試容錯移轉標示為｢完成」。 

### 不同電腦上的 DNS 和網域控制站
 
如果 DNS 與網域控制站不在相同的虛擬機器上，您必須為測試容錯移轉建立 DNS VM。如果是在相同 VM 上，您可以略過本節。

您可以使用全新的 DNS 伺服器，並建立所有的必要區域。例如，如果 Active Directory 網域是 contoso.com，您可以使用 contoso.com 的名稱來建立 DNS 區域。DNS 中對應至 Active Directory 的項目必須更新，如下：

1. 確定復原方案中的任何其他虛擬機器出現之前，這些設定均已完成：

	- 區域必須在樹系根名稱之後命名。
	- 區域必須是檔案備份的。
	- 區域必須能夠進行安全和非安全更新。
	- 網域控制站虛擬機器的解析程式應該指向 DNS 虛擬機器的 IP 位址。

2. 在網域控制站虛擬機器目錄中執行下列命令：

	`nltest /dsregdns`

3. 在 DNS 伺服器上新增區域、允許不安全的更新，以及將適用於它的項目新增到 DNS：

	    dnscmd /zoneadd contoso.com  /Primary 
	    dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1 
	    dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM> 
	    dnscmd /config contoso.com /allowupdate 1


## 後續步驟

閱讀[我可以保護哪些工作負載?](../site-recovery/site-recovery-workload.md) 深入了解如何以 Azure Site Recovery 保護企業工作負載。

<!---HONumber=AcomDC_1217_2015-->