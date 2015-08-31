<properties 
   pageTitle="StorSimple 元件有哪些？ | Microsoft Azure" 
   description="描述 StorSimple 裝置、服務與管理技術。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="08/13/2015"
   ms.author="v-sharos"/>


# StorSimple 元件有哪些？ 

歡迎使用 Microsoft Azure StorSimple，StorSimple 是一個整合式儲存體解決方案，可管理內部部署裝置與 Microsoft Azure 雲端儲存體之間的儲存體工作。StorSimple 的設計旨在降低儲存體成本、簡化儲存體管理、改善災害復原能力和效率，以及提供資料行動力。

下列章節將描述 Microsoft Azure StorSimple 元件，並說明解決方案如何排列資料、配置儲存體、加快儲存體管理速度以及資料保護。最後一節提供一些與 StorSimple 元件和其管理相關之重要術語的定義。

> [AZURE.NOTE]發佈於 Microsoft Azure 網站上的 StorSimple 部署資訊僅適用於 StorSimple 8000 系列裝置。如需 7000 序列裝置的相關資訊，請移至：[StorSimple 說明](http://onlinehelp.storsimple.com/)。

## StorSimple 裝置

Microsoft Azure StorSimple 裝置是內部部署混合式儲存體陣列，可透過主要儲存體和 iSCSI 存取其中儲存的資料。它會管理雲端儲存體之間的通訊，並協助確保所有儲存在 Microsoft Azure StorSimple 方案的資料安全性和機密性。

StorSimple 裝置包括固態硬碟 (SSD) 和硬碟 (HDD)，並支援叢集和自動容錯移轉。它包含共用處理器、共用儲存體，以及兩個鏡像控制站。每個控制站都可提供下列功能：

- 連接到主機電腦
- 最多六個網路連接埠可連接到區域網路 (LAN)
- 硬體監控
- 即使電源中斷仍會保留資訊的靜態隨機存取記憶體 (NVRAM)
- 叢集感知更新可管理容錯移轉叢集中伺服器上的軟體更新，因此更新對服務可用性的影響會降到最低或不會有影響
- 叢集服務 (如後端叢集般運作) 可提供高可用性，並將 HDD 或 SSD 故障或離線時可能會發生的任何負面影響降到最低

不論在任何時間點，只會有一個作用中的控制器。如果作用中的控制器發生故障，則第二個控制站便會自動啟動。

如需詳細資訊，請參閱 [StorSimple 硬體元件和狀態](storsimple-monitor-hardware-status.md)。

## StorSimple 虛擬裝置

您可以使用 StorSimple 來建立可複寫實際混合式存放裝置之架構與功能的虛擬裝置。

StorSimple 虛擬裝置 (也稱為 StorSimple 虛擬應用裝置) 會在 Azure 虛擬機器中的單一節點上執行。(虛擬裝置只能建立在 Azure 虛擬機器上。您無法在 StorSimple 裝置或在內部部署伺服器上建立虛擬裝置)。 StorSimple 虛擬裝置與實體 StorSimple 裝置之間的差異如下：

- 虛擬裝置只有一個介面，而實體裝置則會有六個網路介面。 
- 您會在裝置設定期間 (而不是以個別工作的方式) 註冊虛擬裝置。
- 您無法在虛擬裝置中重新產生服務資料加密金鑰。金鑰變換期間，您會在實體裝置上重新產生金鑰，然後以新的金鑰更新虛擬裝置。
- 如果需要將更新套用到虛擬裝置，您將會遇到出現停機時間的問題。實體 StorSimple 裝置不會發生這個問題。

建議您在實體裝置無法使用的災害復原案例中使用 StorSimple 虛擬裝置，例如雲端開發測試案例。

如需詳細資訊，請參閱 [StorSimple 虛擬裝置](storsimple-virtual-device.md)。


## 儲存體管理技術
 
除了專用的 StorSimple 裝置和虛擬裝置以外，Microsoft Azure StorSimple 還會使用下列軟體技術來提供資料的快速存取，並減少儲存體使用：

- 自動儲存體分層 
- 精簡佈建 
- 重複資料刪除和壓縮 

### 自動儲存體分層

Microsoft Azure StorSimple 會依據目前使用量、使用年限及與其他資料的關聯性，自動排列邏輯層中的資料。最常使用的資料會儲存在本機，而不常使用和非使用中資料會自動移轉至雲端。圖 1 說明此儲存方法。
 
![StorSimple 儲存層](./media/storsimple-components/hcs-data-services-storsimple-components-tiers.png)

**圖 1：StorSimple 儲存體**

若要提供快速存取，StorSimple 會將經常使用的資料 (熱資料) 儲存在 StorSimple 裝置的 SSD 上。它會將偶爾使用的資料 (暖資料) 儲存在裝置的 HDD 上或儲存在資料中心的伺服器上。它會將非使用中的資料、備份資料，以及為封存或遵循法規而保留的資料移動至雲端。

StorSimple 會隨著使用模式變更而調整並重新排列資料和儲存體指派。例如，某些資訊在經過一段時間之後可能會變成不常使用。在資訊變成極少使用的情況下，系統會將它從 SSD 移轉至 HDD，然後移轉至雲端。如果相同資料再次變成使用中，系統會將它移轉回存放裝置。

儲存體分層程序會如下進行：

1. 系統管理員設定 Microsoft Azure 雲端儲存體帳戶。
2. 系統管理員使用序列主控台和 StorSimple Manager 服務 (在 Azure 管理入口網站中執行)，來設定裝置與檔案伺服器，建立磁碟區和資料保護原則。內部部署檔案伺服器使用 Internet Small Computer System Interface (iSCSI) 來存取 StorSimple 裝置。
3. 一開始，StorSimple 將資料儲存在裝置的快速 SSD 層上。
4. 當 SSD 層接近容量上限時，StorSimple deduplicates 會刪除重複資料並壓縮最舊的資料區塊，並將它們移至 HDD 層。
5. 當 HDD 層接近容量上限時，StorSimple 會加密最舊的資料區塊，並透過 HTTPS 將它們安全地傳送到 Microsoft Azure 儲存體帳戶。
6. Microsoft Azure 會在其資料中心和遠端資料中心建立多個資料複本，確保災害發生時可以復原資料。 
7. 當檔案伺服器要求雲端中儲存的資料時，StorSimple 會順暢地傳回它，並將複本儲存在 StorSimple 裝置的 SSD 層上。

### 精簡佈建

精簡佈建是一種虛擬化技術，精簡佈建中的可用儲存體會顯示超過實體資源。與其預先保留足夠的儲存空間，StorSimple 會使用精簡佈建來配置剛好符合目前需求的足夠空間。雲端儲存體的彈性本質正好支援這種方法，因為 StorSimple 可以增加或減少雲端儲存體以符合不斷變更的需求。

### 重複資料刪除和壓縮

Microsoft Azure StorSimple 會使用重複資料刪除和資料壓縮，來進一步降低儲存體需求。

重複資料刪除減少整體儲存資料量的方式是刪除儲存資料集中的重複資料。當資訊變更時，StorSimple 會忽略未變更的資料，而只擷取變更。此外，StorSimple 會透過識別並移除不必要資訊來減少儲存資料量。

## Windows PowerShell for StorSimple

Windows PowerShell for StorSimple 提供了可用來建立和管理 Microsoft Azure StorSimple 服務以及設定和監視 StorSimple 裝置的命令列介面。它會是 Windows PowerShell 型的命令列介面，其中包含可管理 StorSimple 裝置的專用 Cmdlet。Windows PowerShell for StorSimple 的功能可讓您：

- 註冊裝置。
- 在裝置上設定網路介面。
- 安裝特定類型的更新。
- 透過存取支援工作階段來疑難排解您的裝置。
- 變更裝置狀態。

您可以從序列主控台 (在與裝置直接連線的主機電腦上) 或使用 Windows PowerShell 遠端處理來遠端存取 Windows PowerShell for StorSimple。請注意，部分 Windows PowerShell for StorSimple 工作 (例如初始裝置註冊) 只能在序列主控台上執行。

如需詳細資訊，請參閱[使用 Windows PowerShell for StorSimple 管理您的裝置](storsimple-windows-powershell-administration.md)。

## Azure PowerShell StorSimple Cmdlet

Azure PowerShell StorSimple Cmdlet 是一組 Windows PowerShell Cmdlet 集合，可讓您從命令列將服務層級和移轉工作自動化。如需適用於 StorSimple 的 Azure PowerShell Cmdlet 的詳細資訊，請移至 [Cmdlet 參考](https://msdn.microsoft.com/library/dn920427.aspx)。

## StorSimple Manager 服務

Microsoft Azure StorSimple 提供可讓您集中管理資料中心和雲端儲存體的 Web 架構使用者介面 (StorSimple Manager 服務)。您可以使用 StorSimple Manager 服務來執行下列工作：

- 進行 StorSimple 裝置的系統設定。
- 進行和管理 StorSimple 裝置的安全性設定。
- 設定雲端認證和屬性。
- 設定和管理伺服器上的磁碟區。
- 設定磁碟區群組。
- 備份和還原資料。
- 監視效能。
- 檢閱系統設定，並識別可能發生的問題。

您可以使用 StorSimple Manager 服務來執行所有管理工作，除了需要系統停機的工作 (例如初始安裝和更新安裝) 以外。

如需詳細資訊，請參閱[使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

## StorSimple Snapshot Manager

StorSimple Snapshot Manager 是 Microsoft Management Console (MMC) 嵌入式管理單元，可用來建立本機和雲端資料的一致時間點備份複本。嵌入式管理單元會在 Windows Server 型的主機上執行。您可以使用 StorSimple Snapshot Manager 進行下列作業：

- 設定、備份及刪除磁碟區。
- 設定磁碟區群組，以確保是應用程式一致的備份資料。
- 管理備份原則，方便在預先決定的排程上備份資料並儲存在指定位置 (在本機或在雲端中)。
- 還原磁碟區和個別檔案。

系統會以快照集的方式擷取備份，快照集僅會記錄建立上次快照集之後所做的變更，而且需要的儲存空間遠少於完整備份。您可以建立備份排程或視需要進行立即備份。此外，您還可以使用 StorSimple Snapshot Manager 建立可控制要儲存多少快照集的保留原則。如果您之後需要將資料從備份還原，StorSimple Snapshot Manager 可讓您從本機或雲端快照集的類別中進行選取。

如果發生損毀，或如果因為其他原因而必須還原資料，StorSimple Snapshot Manager 會在需要時將它以累加方式還原。當您還原檔案、更換設備或將作業移到另一個站台時，資料還原不需要您將整個系統關閉。

如需詳細資訊，請參閱[什麼是 StorSimple Snapshot Manager？](storsimple-what-is-snapshot-manager.md)

## StorSimple Adapter for SharePoint

Microsoft Azure StorSimple 包括 StorSimple Adapter for SharePoint，其為可將 StorSimple 儲存體和資料保護功能明確延伸至 SharePoint 伺服器陣列的選擇性元件。將配接器與遠端 Blob 儲存體 (RBS) 提供者和 SQL Server RBS 功能搭配使用，可讓您將 Blob 移至由 Microsoft Azure StorSimple 系統備份的伺服器。Microsoft Azure StorSimple 接著可根據使用方式，在本機或在雲端中儲存 BLOB 資料。

您可以在 SharePoint 管理中心入口網站內管理 StorSimple Adapter for SharePoint。因此，SharePoint 管理仍然是集中式管理，而所有的儲存體會顯示在 SharePoint 伺服陣列中。

如需詳細資訊，請參閱 [StorSimple Adapter for SharePoint](storsimple-adapter-for-sharepoint.md)。


## StorSimple 術語 

在部署 Microsoft Azure StorSimple 解決方案，我們建議您檢閱下列詞彙和定義。

### 重要詞彙和定義

| 術語 (首字母縮寫或縮寫) | 說明 |
| ------------------------------ | ---------------- |
| 存取控制記錄 (ACR) | 與 Microsoft Azure StorSimple 裝置上的磁碟區相關聯的記錄，決定哪些主機可連接到其中。此決定根據連接到 StorSimple 裝置之主機 (包含在 ACR 中) 的 iSCSI 合格名稱 (IQN)。|
| AES 256 | 256 位元進階加密標準 (AES) 演算法，用於加密移入和移出雲端的資料。 |
| 配置單位大小 (AUS) | 最小數量的磁碟空間，可配置來保留 Windows 檔案系統中的檔案。如果檔案大小不是叢集大小的偶數倍數，則必須使用額外的空間來保留檔案 (最多為叢集大小的下一個倍數)，這會導致喪失空間和硬碟的片段。<br>Azure StorSimple 磁碟區建議的 AUS 是 64 KB，因為它適用於重複資料刪除演算法。|
| 自動儲存體分層 | 自動將較不活躍的資料移到雲端中的層，然後可讓您從中央使用者介面管理所有儲存體。|
| 備份類別目錄 | 備份的集合，通常與所使用的應用程式類型相關。這個集合會顯示在 StorSimple Manager 服務 UI 的 [備份類別目錄] 頁面中。|
| 備份類別目錄檔案 | 包含清單的檔案，此清單會列出 StorSimple Snapshot Manager 的備份資料庫中目前儲存的可用快照。 |
| 備份原則 | 選取磁碟區、備份類型，以及可讓您根據預先定義的排程建立備份的時間資料表。|
| 二進位大型物件 (BLOB) | 二進位資料的集合，而這些二進位資料會在資料庫管理系統中儲存為單一實體。BLOB 通常是影像、音訊或其他多媒體物件，但是有時二進位可執行程式碼會儲存為 BLOB。|
| Challenge Handshake 驗證通訊協定 (CHAP) | 一種通訊協定，用來根據共用密碼的對等節點驗證連接的對等節點。CHAP 可以是單向或雙向。使用單向 CHAP 時，目標會驗證啟動器。雙向 CHAP 會要求目標與啟動器相互驗證。 | 
| 複製 | 磁碟區的重複複本。 |
|雲端即層次 (CaaT) | 雲端儲存體會整合成儲存體架構內的一層，以便所有儲存體形同企業儲存體網路的一部分。|
| 雲端服務提供者 (CSP) | 雲端運算服務的提供者。|
| 雲端快照 | 雲端中儲存之磁碟區資料的時間點複本。雲端快照相當於在不同的異地儲存體系統上複寫的快照。雲端快照在災害復原案例中特別有用。|
| 雲端儲存體加密金鑰 | StorSimple 裝置所使用的密碼或金鑰，用來存取您的裝置傳送至雲端的加密資料。|
| 叢集感知更新 | 管理容錯移轉叢集中伺服器上的軟體更新，以便更新對服務可用性的影響會降到最低或不會有影響。|
| 資料路徑 | 功能單位的集合，而這些功能單位會執行互連的資料處理作業。|
| 停用 | 一種永久動作，用於中斷 StorSimple 裝置與相關聯雲端服務之間的連接。裝置的雲端快照在此程序之後仍會保留，並可以複製或用於災害復原。|
| 磁碟鏡像 | 即時複寫個別硬碟上的邏輯磁碟區以確保連續可用性。|
| 動態磁碟鏡像 | 複寫動態磁碟上的邏輯磁碟區。|
| 動態磁碟 | 一種磁碟區格式，其會使用邏輯磁碟管理員 (LDM)，跨多個實體磁碟儲存和管理資料。動態磁碟可以擴大來提供更多的可用空間。|
| 擴充的磁碟群 (EBOD) 機箱 | Microsoft Azure StorSimple 裝置的次要機箱，其中包含額外的硬碟來提供額外的儲存體。|
| 豐富佈建 | 一種傳統的儲存體佈建，其中的儲存體空間是根據預期需求 (通常超過目前需求) 來配置。另請參閱*精簡佈建*。|
| 硬碟 (HDD) | 使用旋轉盤來儲存資料的磁碟機。|
| 混合式雲端儲存體 | 使用本機和異地資源 (包括雲端儲存體) 的儲存體架構。|
| Internet Small Computer System Interface (iSCSI) | 用於連結資料儲存設備或設施的網際網路通訊協定 (IP) 型儲存體網路標準。|
| iSCSI 啟動器 | 一種軟體元件，可讓執行 Windows 的主機電腦連接到外部 iSCSI 型儲存體網路。|
| iSCSI 合格名稱 (IQN) | 識別 iSCSI 目標或啟動器的唯一名稱。|
| iSCSI 目標 | 一種軟體元件，可在存放區域網路中提供集中式 iSCSI 磁碟子系統。|
| 即時封存 | 隨時都可存取封存資料 (例如，它不是儲存在異地磁帶上) 的儲存方法。Microsoft Azure StorSimple 使用即時封存。|
| 本機快照 | 儲存在 Microsoft Azure StorSimple 裝置之磁碟區資料的時間點複本。|
| Microsoft Azure StorSimple | 一種功能強大的解決方案，其中包含資料中心儲存體應用裝置和軟體，可讓 IT 組織運用雲端儲存體，就好像它是資料中心儲存體，並簡化資料保護和資料管理，同時降低成本。這個解決方案會透過與雲端的完美整合，將主要儲存體、封存、備份和災害復原 (DR) 合而為一。藉由結合企業級平台上的 SAN 儲存體和雲端資料管理，StorSimple 裝置啟用了所有儲存體相關需求的速度、簡化及可靠性。|
| 電源和冷卻模組 (PCM) | StorSimple 裝置的硬體元件，其中包含電源供應器和冷卻風扇，因此命名為電源和冷卻模組。裝置的主要機箱有兩個 764W PCM，而 EBOD 機箱有兩個 580W PCM。|
| 主要機箱 | StorSimple 裝置的主要機箱，其中包含應用程式平台控制器。|
| 復原時間目標 (RTO) | 發生災害之後，商務程序或系統完全還原之前應該延長的時間量上限。| 
|序列連接 SCSI (SAS) | 硬碟 (HDD) 的類型。|
| 服務資料加密金鑰 | 向 StorSimple Manager 服務註冊的金鑰，讓任何新的 StorSimple 裝置可供使用。StorSimple Manager 服務與裝置之間傳輸的組態資料會使用公開金鑰加密，然後只能使用私密金鑰在裝置上進行解密。服務資料加密金鑰可讓服務取得此私密金鑰進行解密。|
| 服務註冊金鑰 | 協助向 StorSimple Manager 服務註冊 StorSimple 裝置的金鑰，以便該裝置可以出現在管理入口網站中，讓您能夠採取進一步的管理動作。|
| Small Computer System Interface (SCSI) | 一組實際連接電腦並在它們之間傳送資料的標準。|
| 固態硬碟 (SSD) | 沒有包含任何移動組件的磁碟；例如，快閃磁碟機。|
| storage account | 一組針對給定的雲端服務提供者連結至您的儲存體帳戶的存取認證。| 
| StorSimple Adapter for SharePoint| 一種 Microsoft Azure StorSimple 元件，其可將 StorSimple 儲存體和資料保護明確延伸至 SharePoint 伺服器陣列。|
| StorSimple Manager 服務 | Azure 管理入口網站的延伸模組，可讓您管理您的 Azure StorSimple 內部部署和虛擬裝置。|
| StorSimple Snapshot Manager | 一種 Microsoft Management Console (MMC) 嵌入式管理單元，用於管理 Microsoft Azure StorSimple 中的備份和還原作業。|
| 進行備份 | 一種可讓使用者進行磁碟區互動式備份的功能。相對於透過定義的原則進行自動備份，它是進行磁碟區手動備份的替代方式。|
| 精簡佈建 | 一種可在儲存體系統中以最佳效率使用可用之儲存空間的方法。在精簡佈建中，會根據每個使用者在任何給定時間所需的最小空間，在多個使用者之間配置儲存體。另請參閱*豐富佈建*。|
| 磁碟區 | 以磁碟機形式呈現的邏輯存放區域。StorSimple 磁碟區會對應至主機所掛接的磁碟區，包括透過使用 iSCSI 及 StorSimple 裝置探索到的磁碟區。|
 | 磁碟區容器 | 磁碟區和套用至其中之設定的群組。StorSimple 裝置中分組為磁碟區容器的所有磁碟區。磁碟區容器設定包括儲存體帳戶、使用相關聯的加密金鑰將資料傳送至雲端的加密設定，以及對牽涉到雲端之作業所耗用的頻寬。|
| 磁碟區群組 | 在 StorSimple Snapshot Manager 中，磁碟區群組是為了協助處理備份而設定的磁碟區集合。|
| 磁碟區陰影複製服務 (VSS)| 一種 Windows Server 作業系統服務，可藉由與 VSS 感知應用程式通訊，來協調建立增量快照，以促進應用程式一致性。VSS 確保取得快照時應用程式會暫時停用。|
| Windows PowerShell for StorSimple | 用來操作和管理 StorSimple 裝置的 Windows PowerShell 型命令列介面。這個介面具備 Windows PowerShell 的一些基本功能，同時又具有額外的專用 Cmdlet，相互搭配來管理 StorSimple 裝置。|


## 後續步驟

深入了解 [StorSimple 安全性](storsimple-security.md)。




 

<!---HONumber=August15_HO8-->