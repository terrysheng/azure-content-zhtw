<properties
	pageTitle="Active Directory 的 ASR 指引 | Microsoft Azure" 
	description="本文詳細說明有關如何使用 Azure Site Recovery 為您的 AD 建立災害復原解決方案、使用單鍵復原計劃執行計劃/未計劃/測試容錯移轉、支援的組態和必要條件。" 
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
	ms.date="10/12/2015" 
	ms.author="pratshar"/>

#使用 ASR 對 Active Directory 和 DNS 自動化 DR 解決方案


所有企業應用程式，例如 SharePoint、Dynamics AX 和 SAP 都依存於 AD 和 DNS 基礎結構才能正確運作。建立這類應用程式的災害復原 (DR) 解決方案時，務必在其他應用程式元件遭遇中斷事件之前保護和復原 AD。

Azure Site Recovery 是以 Azure 為基礎的服務，藉由協調虛擬機器的複寫、容錯移轉及復原，提供災害復原功能。Azure Site Recovery 支援多種複寫技術來持續複寫、保護及順暢地容錯移轉虛擬機器和應用程式至私密/公用或主機服務提供者的雲端。

使用 Azure Site Recovery 時，您可以為 AD 建立完整的自動化災害復原計劃。您可以在發生中斷時，從任何地方數秒鐘內起始容錯移轉，並且在數分鐘內啟動 AD 並執行。如果您的主要站台中有適用於多個應用程式的 AD，如 SharePoint 和 SAP，而您決定容錯移轉整個站台，您可以先使用 ASR 復原計劃容錯移轉 AD，再使用應用程式特定復原計劃容錯移轉其他應用程式。

本文詳細說明有關如何為您的 AD 建立災害復原解決方案、使用單鍵復原計劃執行計劃/未計劃/測試容錯移轉、支援的組態和必要條件。讀者應該要熟悉 AD 和 Azure Site Recovery。根據客戶用來保護 AD 的內部部署環境的複雜性，有兩個建議的選項。

####選項 1

如果環境中有少數的應用程式和單一網域控制站，且您將容錯移轉整個網站，則我們建議使用 ASR 複寫將網域控制站電腦複寫至次要網站 (適用於網站對網站和網站對 Azure)。相同的複寫的虛擬機器也可以用於測試容錯移轉。

####選項 2
如果有大量的應用程式且在環境中有一個以上的網域控制站，或您打算一次容錯移轉幾個應用程式，則我們建議您除了在網域控制站虛擬機器上啟用 ASR 複寫 (用於測試容錯移轉) 之外，您也在 DR 網站上設定其他網域控制站 (次要內部部署網站或在 Azure 中)。

>[AZURE.NOTE]即使您使用選項 2，若要進行測試容錯移轉，系統仍然會要求您設定網域控制站的 ASR 複寫。如需詳細資訊，請詳閱[測試容錯移轉考量](#considerations-for-test-failover)一節。


下列各節說明如何在 ASR 中的網域控制站上啟用保護，以及如何在 Azure 中設定網域控制站。


##必要條件

使用 Azure Site Recovery 針對 AD 實作災害復原需要完成下列必要條件。

- 內部部署 Active Directory 和 DNS 伺服器
- 已在 Microsoft Azure 訂用帳戶中建立 Azure Site Recovery 服務保存庫 
- 如果 Azure 是您的復原網站，請在 VM 上執行 Azure 虛擬機器整備評估工具，以確保相容於 Azure VM 與 Azure Site Recovery 服務


##使用 ASR 啟用網域控制站/DNS 的保護


###保護 VM
在 ASR 中啟用網域控制站/DNS VM 的保護。根據是將 VM 部署在 Hyper-V 或 VMware 上，執行相關的 Azure Site Recovery 設定。建議的損毀一致頻率設定為 15 分鐘。

###設定 VM 網路設定
- 對於網域控制站/DNS 虛擬機器，在 ASR 中設定網路設定，讓 VM 在容錯移轉之後附加至正確的網路。 
- 例如，當您使用 Azure 做為 DR 網站時，您可以在 [VMM 雲端] 或 [保護群組] 中選取 VM 以設定網路設定，如下方的快照所示。
- 
![VM 網路設定](./media/site-recovery-active-directory/VM-Network-Settings.png)

##使用 AD 複寫啟用 AD 的保護
###網站對網站案例

在 secondary(DR) 網站上建立網域控制站，並且同時將伺服器提升至網域控制站角色，給予已在主要網站中使用的相同網域的名稱。您可以使用 Active Directory 網站及服務嵌入式管理單元在要加入網站的網站連結物件上設定。藉由設定網站連結，您可以控制兩個以上的網站之間的複寫發生的時間和頻率。您可以參閱[排程網站之間的複寫](https://technet.microsoft.com/zh-TW/library/cc731862.aspx)以取得詳細資訊。

###網站對 Azure 案例
遵循這些指示以建立[在 Azure 虛擬網路中的網域控制站](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md)同時將伺服器提升至網域控制站角色，給予已在主要網站中使用的相同網域的名稱。

在此之後，您應該[重新設定虛擬網路的 dns 伺服器](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)，以在 Azure 中使用 DNS 伺服器
  
![Azure 網路](./media/site-recovery-active-directory/azure-network.png)

##測試容錯移轉的考量
測試容錯移轉是在與生產網路隔離的網路中完成，因此不會影響到生產工作負載。大部分的應用程式也需要網域控制站和 DNS 伺服器才能運作。因此，應用程式容錯移轉之前，必須在隔離的網路中建立網域控制站以用於測試容錯移轉。若要這麼做，最簡單的方式是先使用 ASR 在網域控制站/DNS 虛擬機器上啟用保護，以及在觸發應用程式的復原計劃的測試容錯移轉之前，觸發網域控制站虛擬機器的測試容錯移轉。以下是執行測試容錯移轉的逐步指引：

1. 如同您對任何其他虛擬機器所執行的動作一樣，在網域控制站/DNS 虛擬機器上啟用保護。
2. 建立隔離的網路。在 Azure 中建立的任何虛擬網路預設是與其他網路隔離。建議此網路的 IP 範圍與您的生產網路的 IP 範圍相同。請勿在此網路上啟用網站對網站連線能力。
3. 提供在上述步驟中建立的網站中的 DNS IP，做為您預期 DNS VM 取得的 IP。如果您使用 Azure 做為 DR 網站，則您可以提供 VM 的 IP，該 VM 將會用於 VM 屬性中 [目標 IP] 設定的容錯移轉。如果您的 DR 網站是在內部部署且您是使用 DHCP，則遵循這裡的指示來[針對測試容錯移轉設定 DNS 和 DHCP](site-recovery-failover.md#prepare-dhcp) 

>[AZURE.NOTE]給予測試容錯移轉上的虛擬機器的 IP，與它在執行計劃或未計劃的容錯移轉所得到的 IP 相同，讓此 IP 可用於測試容錯移轉網路。如果相同 IP 不適用於測試容錯移轉網路，虛擬機器會取得可用於測試容錯移轉網路中的一些其他 IP。

4. 請移至網域控制站虛擬機器，並且在隔離網路中執行它的測試容錯移轉。 
5. 執行應用程式的復原計劃的測試容錯移轉。
6. 測試完成之後，在 ASR 的 [工作] 索引標籤中，將網域控制站虛擬機器和復原計劃的工作的測試容錯移轉標示為 [完成]。 

###DNS 不在與網域控制站相同的虛擬機器上： 
萬一 DNS 不在與網域控制站相同的虛擬機器上，您必須針對測試容錯移轉建立 DNS。如果是在相同的 VM 上，您可以略過本節。您可以使用全新的 DNS 伺服器，並建立所有的必要區域。例如，如果 Active Directory 網域是 contoso.com，您可以使用 contoso.com 的名稱來建立區域。對應至 Active Directory 的項目必須在 DNS 中更新。以下列方式來執行此動作：

- 確定復原方案中的任何其他虛擬機器出現之前，下列設定均已完成：
	- 區域必須在樹系根名稱之後命名。
	- 區域必須是檔案備份的。
	- 區域必須能夠進行安全和非安全更新。
	- 網域控制站虛擬機器的解析程式應該指向 DNS 虛擬機器的 IP 位址。
- 在網域控制站虛擬機器目錄中執行下列命令：nltest /dsregdns

- **加入區域** - 使用下列範例指令碼，在 DNS 伺服器上新增區域、允許不安全的更新，以及將適用於其本身的項目新增到 DNS：

	    dnscmd /zoneadd contoso.com  /Primary 
	    dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1 
	    dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM> 
	    dnscmd /config contoso.com /allowupdate 1


##摘要
使用 Azure Site Recovery 時，您可以為 AD 建立完整的自動化災害復原計劃。您可以在發生中斷時，從任何地方數秒鐘內起始容錯移轉，並且在數分鐘內啟動 AD 並執行。如果您的主要網站中有適用於多個應用程式的 AD，如 SharePoint 和 SAP，而您決定容錯移轉整個網站，您可以先使用 ASR 容錯移轉 AD，再使用應用程式特定復原計劃容錯移轉其他應用程式。


請參閱[網站復原工作負載指引](../site-recovery/site-recovery-workload.md)以取得使用 ASR 保護各種工作負載的詳細資訊。

<!---HONumber=Oct15_HO3-->