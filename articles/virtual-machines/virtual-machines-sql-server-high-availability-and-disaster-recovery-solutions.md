<properties 
   pageTitle="SQL Server 高可用性和災害復原 | Microsoft Azure"
   description="本教學課程使用隨傳統部署模型建立的資源，並討論在 Azure 虛擬機器中執行的 SQL Server 適用的各種 HADR 策略。"
   services="virtual-machines"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar" 
   tags="azure-service-management"/>
<tags 
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="08/17/2015"
   ms.author="jroth" />

# Azure 虛擬機器中的 SQL Server 高可用性和災害復原

## 概觀

內含 SQL Server 的 Microsoft Azure 虛擬機器 (VM) 可協助降低高可用性和嚴重損壞修復 (HADR) 資料庫解決方案的成本。Azure 虛擬機器中支援大部分的 SQL Server HADR 解決方案 (僅限 Azure 以及混合式解決方案兩種)。在僅限 Azure 解決方案中，整個 HADR 系統會在 Azure 中執行。在混合式組態中，解決方案的一部分會在 Azure 中執行，而其他部分會在組織中的內部部署執行。Azure 環境的彈性可讓您將解決方案部分或完全移動至 Azure，以滿足 SQL Server 資料庫系統的預算與 HADR 需求。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]本文涵蓋的內容包括以傳統部署模型建立資源。

## 了解 HADR 解決方案的需求

您可以決定是否要讓資料庫系統擁有服務等級協定 (SLA) 需要的 HADR 功能。即使 Azure 提供高可用性機制 (例如雲端服務的服務修復，以及虛擬機器的失敗修復偵測)，也無法保證您能夠符合需要的 SLA。這些機制能保護 VM 的高可用性，但無法保護在 VM 中執行之 SQL Server 的高可用性。因此，即使 VM 保持連線且運作正常，SQL Server 執行個體仍可能會失敗。此外，Azure 提供的高可用性機制甚至會允許因為事件 (例如，從軟體或硬體失敗修復，以及作業系統升級) 導致的 VM 停機。

再者，Azure 中的異地備援儲存體 (GRS) (使用名為異地複寫功能進行實作) 對您的資料庫而言，可能不足以當作嚴重損壞修復解決方案使用。由於異地複寫會以非同步方式傳送資料，因此最近的更新可能會在發生嚴重損壞事件時遺失。有關地理複寫限制的詳細資訊，皆記載於[異地複寫不支援不同磁碟上的資料和記錄檔](#geo-replication-support)一節。

## HADR 部署架構

在 Azure 中支援的 SQL Server HADR 技術包括：

- [AlwaysOn 可用性群組](https://technet.microsoft.com/library/hh510230.aspx)
- [資料庫鏡像](https://technet.microsoft.com/library/ms189852.aspx)
- [記錄傳送](https://technet.microsoft.com/library/ms187103.aspx)
- [備份及還原與 Azure Blob 儲存體服務](https://msdn.microsoft.com/library/jj919148.aspx)

您可以將這些技術合併在一起，以實作同時具有高可用性及嚴重損壞修復功能的 SQL Server 解決方案。根據您使用的技術而定，混合式部署可能會需要使用 Azure 虛擬網路的 VPN 通道。下列各節會說明部分範例部署架構。

## 僅限 Azure：高可用性解決方案

您可以使用 AlwaysOn 可用性群組或資料庫鏡像，為 Azure 中的 SQL Server 資料庫提供高可用性解決方案。

|Technology|範例架構|
|---|---|
|**AlwaysOn 可用性群組**|為了相同區域內的高可用性，所有可用性複本都會在 Azure VM 中執行。由於 Windows Server 容錯移轉叢集 (WSFC) 需使用 Active Directory 網域，因此除了 SQL Server 虛擬機器以外，您還需要設定網域控制站。<br/> ![AlwaysOn 可用性群組](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_ha_always_on.gif)<br/>如需詳細資訊，請參閱[在 Azure (GUI) 中設定 AlwaysOn 可用性群組](virtual-machines-sql-server-alwayson-availability-groups-gui.md)。|
|**資料庫鏡像**|為了高可用性，所有主體、鏡像和見證伺服器都會在相同的 Azure 資料中心執行。您可以使用網域控制站進行部署。<br/>![資料庫鏡像](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_ha_dbmirroring1.gif)<br/>您也可以改用伺服器憑證，不需網域控制站即可部署相同的資料庫鏡像組態。<br/>![資料庫鏡像](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_ha_dbmirroring2.gif)|

## 僅限 Azure：嚴重損壞修復解決方案

您可以使用 AlwaysOn 可用性群組和資料庫鏡像，為 Azure 中的 SQL Server 資料庫提供嚴重損壞修復解決方案，或者使用儲存體 Blob 進行備份和還原。

|Technology|範例架構|
|---|---|
|**AlwaysOn 可用性群組**|為了進行嚴重損壞修復，可用性複本會在 Azure VM 的多個資料中心執行。這種跨區域解決方案可防止網站完全中斷。<br/> ![AlwaysOn 可用性群組](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_dr_alwayson.png)<br/>在區域內，所有複本都應位於相同的雲端服務與 VNet 中。由於每個區域會有不同的 VNet，因此這些解決方案會需要 VNet 對 VNet 連線。如需詳細資訊，請參閱[在管理入口網站中設定網站間 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)。|
|**資料庫鏡像**|為了進行嚴重損壞修復，主體、鏡像和伺服器會在不同的資料中心內執行。由於 Active Directory 網域無法跨多個資料中心，因此您必須使用伺服器憑證進行部署。<br/>![資料庫鏡像](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_dr_dbmirroring.gif)|
|**備份及還原與 Azure Blob 儲存體服務**|為了進行嚴重損害修復，生產資料庫會直接備份至不同資料中心內的 Blob 儲存體。<br/>![備份與還原](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_dr_backup_restore.gif)<br/>如需詳細資訊，請參閱[Azure 虛擬機器中的 SQL Server 備份和還原](virtual-machines-sql-server-backup-and-restore.md)。|

## 混合式 IT：嚴重損壞修復解決方案

您可以使用 AlwaysOn 可用性群組、資料庫鏡像及記錄傳送，為混合式 IT 環境中的 SQL Server 資料庫提供嚴重損壞修復解決方案，以及使用 Azure Blob 儲存體進行備份和還原。

|Technology|範例架構|
|---|---|
|**AlwaysOn 可用性群組**|為了進行跨網站的嚴重損壞修復，部分可用性複本會在 Azure VM 中執行，而其他複本會在內部部署執行。生產網站可以在內部部署或 Azure 資料中心內運作。<br/>![AlwaysOn 可用性群組](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_alwayson.gif)<br/>由於所有可用性複本都必須位於相同的 WSFC 叢集中，因此該叢集必須同時跨這兩個網路 (多重子網路 WSFC 叢集)。此組態需要 Azure 及內部部署網路之間的 VPN 連線。<br/><br/>為了成功進行資料庫的嚴重損壞修復，您也應該在嚴重損壞修復站台中安裝複本網域控制站。<br/><br/>您可以使用 SSMS 中的 [加入複本精靈]，將 Azure 複本加入至現有的 AlwaysOn 可用性群組。如需詳細資訊，請參閱教學課程：將您的 AlwaysOn 可用性群組延伸至 Azure。|
|**資料庫鏡像**|為了使用伺服器憑證進行跨網站嚴重損壞修復，一個合作夥伴會在 Azure VM 中執行，而其他合作夥伴會在內部部署中執行。合作夥伴不需要位於相同的 Active Directory 網域，且不需要 VPN 連線。<br/>![資料庫鏡像](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_dbmirroring.gif)<br/>另一個資料庫鏡像案例，則是為了進行跨網站嚴重損壞修復，讓一個合作夥伴在 Azure VM 中執行，並讓其他合作夥伴在相同 Active Directory 網域的內部部署中執行。需要 [Azure 虛擬網路及內部部署網路之間的 VPN 連線](../vpn-gateway/vpn-gateway-site-to-site-create.md)。<br/><br/>為了成功進行資料庫的嚴重損壞修復，您也應該在嚴重損壞修復站台中安裝複本控制站。|
|**記錄傳送**|為了進行跨網站嚴重損壞修復，一個合作夥伴會在 Azure VM 中執行，而其他合作夥伴會在內部部署中執行。由於記錄傳送須仰賴 Windows 檔案共用，因此需要 Azure 虛擬網路及內部部署網路之間的 VPN 連線。<br/>![記錄傳送](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_log_shipping.gif)<br/>為了成功進行資料庫的嚴重損壞修復，您也應該在嚴重損壞修復站台中安裝複本網域控制站。|
|**備份及還原與 Azure Blob 儲存體服務**|為了進行嚴重損害修復，內部部署生產資料庫會直接備份至 Blob 儲存體。<br/>![備份與還原](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_backup_restore.gif)<br/>如需詳細資訊，請參閱[Azure 虛擬機器中的 SQL Server 備份和還原](virtual-machines-sql-server-backup-and-restore.md)。|

## Azure 中 SQL Server HADR 的重要考量

與內部部署、非虛擬化的 IT 基礎結構相較之下，Azure VM、儲存體和網路各有不同的作業特性。若要在 Azure 中成功實作 HADR SQL Server 解決方案，您必須了解這些差異，並配合這些差異設計您的解決方案。

### 可用性設定組中的高可用性節點

Azure 中的可用性設定組可讓您將高可用性節點分別放入容錯網域 (FD) 和更新網域 (UD)。若要將 Azure VM 放入相同的可用性設定組中，您必須將其部署至相同的雲端服務中。只有相同雲端服務內的節點可以參與相同的可用性設定組。如需詳細資訊，請參閱[管理虛擬機器的可用性](virtual-machines-manage-availability.md)。

### Azure 網路中的 WSFC 叢集行為

由於您將重複的 IP 位址指派為叢集網路名稱 (例如，將相同的 IP 位址指派為其中一個叢集節點)，因此 Azure 中的非 RFC 相容 DHCP 服務可能會導致特定 WSFC 叢集組態的建立作業失敗。這是您實作 AlwaysOn 可用性群組 (仰賴 WSFC 功能) 時會發生的問題。

當雙節點叢集建立且上線時，請考慮以下案例：

1. 當叢集上線後，NODE1 會要求一個叢集網路名稱的動態指派 IP 位址。

1. 由於 DHCP 服務會辨識來自 NODE1 本身的要求，因此除了 NODE1 本身的 IP 位址外，DHCP 服務不會指定任何 IP 位址。

1. 如果 Windows 偵測到同時指派至 NODE1 和叢集網路名稱的重複位址，則預設的叢集群組就會無法上線。

1. 此時預設的叢集群組就會移動至 NODE2，而該群組會將 NODE1 的 IP 位址視為叢集 IP 位址，並讓預設叢集群組上線。

1. 當 NODE2 嘗試與 NODE1 建立連線時，於 NODE1 導向的封包不會離開 NODE2，因為它將 NODE1 的 IP 位址解析至其本身。NODE2 無法與 NODE1 建立連線，因此會失去仲裁並關閉叢集。

1. 在此同時，NODE1 可以傳送封包至 NODE2，但是 NODE2 無法回覆。NODE1 會失去仲裁並關閉叢集。

若要避免這種情況，可以將未使用的靜態 IP 位址 (例如，連結本機的 IP 位址為 169.254.1.1) 指派至叢集網路名稱，使叢集網路名稱上線。若要簡化此程序，請參閱[在 Azure 中為 AlwaysOn 可用性群組設定 Windows 容錯移轉叢集](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx)。

如需詳細資訊，請參閱[在 Azure (GUI) 中設定 AlwaysOn 可用性群組](virtual-machines-sql-server-alwayson-availability-groups-gui.md)。

### 可用性群組接聽程式支援

可用性群組接聽程式支援執行 Windows Server 2008 R2、Windows Server 2012 和 Windows Server 2012 R2 的 Azure VM。透過使用負載平衡端點，以及在屬於可用性群組節點的 Azure VM 中啟用伺服器直接回傳 (DSR)，即可提供支援。您必須遵循特殊組態步驟，以便接聽程式在 Azure 中執行的用戶端應用程式，以及在內部部署中執行的用戶端應用程式運作。

用戶端必須從不在相同雲端服務 (做為 AlwaysOn 可用性群組節點) 中的機器連接至接聽程式。如果可用性群組跨越多個 Azure 子網路 (例如跨越多個 Azure 區域的部署)，則用戶端連接字串必須包含 "MultisubnetFailover = True"。這會導致對於不同子網路中的複本進行平行連接嘗試。如需設定接聽程式的指示，請參閱[在 Azure 中設定 AlwaysOn 可用性群組的 ILB 接聽程式](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)。

您仍可以透過直接連接至服務執行個體，分別連接至各個可用性複本。此外，由於 AlwaysOn 可用性群組能與資料庫鏡像用戶端回溯相容，因此只要將可用性複本設定成類似資料庫鏡像，即可連接至該複本 (例如資料庫鏡像合作夥伴)：

- 一個主要複本與一個次要複本

- 次要複本會設定為不可讀取 ([可讀取次要] 選項設為 [否])

使用 ADO.NET 或 SQL Server Native Client 對應至類似此資料庫鏡像組態的範例用戶端連接字串如下：

	Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

如需用戶端連線的詳細資訊，請參閱：

- [搭配 SQL Server Native Client 使用連接字串關鍵字](https://msdn.microsoft.com/library/ms130822.aspx)
- [將用戶端連接至資料庫鏡像工作階段 (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
- [連線至混合式 IT 中的可用性群組接聽程式](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx) (英文)
- [可用性群組接聽程式、用戶端連接及應用程式容錯移轉 (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
- [搭配可用性群組使用資料庫鏡像連接字串](https://technet.microsoft.com/library/hh213417.aspx)

### 混合式 IT 中的網路延遲

假設內部部署網路與 Azure 之間可能會有一段時間發生嚴重網路延遲，您應該部署 HADR 解決方案。當您將複本部署至 Azure 時，應該使用非同步認可，而不是同步處理模式的同步認可。當您將資料庫鏡像伺服器同時部署至內部部署與 Azure 時，請使用高效能模式，而不是高安全性模式。

### 異地複寫支援

Azure 磁碟中的異地複寫不支援將相同資料庫的資料檔與記錄檔儲存在不同的磁碟上。GRS 會在每個磁碟上進行獨立與非同步變更複寫。此機制能保證異地複寫複本之單一磁碟內的寫入順序，但無法為多個磁碟的跨異地複寫複本保證。如果您設定資料庫將其資料檔與記錄檔儲存在不同磁碟中，嚴重損壞後修復的磁碟可能會包含更新的資料檔 (而非記錄檔) 複本，該情形會中斷 SQL Server 與 ACID 屬性交易的預寫記錄。如果您沒有可停用儲存體帳戶中異地複寫的選項，則應該將指定資料庫的所有資料和記錄檔保留在相同磁碟上。如果因為資料庫大小的緣故，而必須使用一個以上的磁碟，則您需要部署以上列出的其中一個嚴重損壞修復解決方案，以確保能進行資料備援。

## 後續步驟

如果您需要使用 SQL Server 建立 Azure 虛擬機器，請參閱[在 Azure 上佈建 SQL Server 虛擬機器](virtual-machines-provision-sql-server.md)。

若要從 Azure VM 上執行的 SQL Server 取得最佳效能，請參閱 [Azure 虛擬機器中 SQL Server 的效能最佳做法](virtual-machines-sql-server-performance-best-practices.md)中的指引。

如需在 Azure VM 中執行 SQL Server 的其他相關主題，請參閱 [Azure 虛擬機器上的 SQL Server](virtual-machines-sql-server-infrastructure-services.md)。

### 其他資源：

- [在 Azure 中安裝新的 Active Directory 樹系](../active-directory/active-directory-new-forest-virtual-machine.md)
- [在 Azure VM 中建立 AlwaysOn 可用性群組的 WSFC 叢集](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

<!---HONumber=Sept15_HO4-->