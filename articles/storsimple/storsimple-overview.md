<properties 
   pageTitle="何謂 StorSimple？| Microsoft Azure" 
   description="描述 StorSimple 資料管理和保護程序、優點及架構，並介紹 StorSimple 元件。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="09/25/2015"
   ms.author="v-sharos@microsoft.com"/>

# StorSimple 8000 系列：混合式雲端存放解決方案 

## 概觀

Microsoft Azure StorSimple 是一個有效率、符合成本效益且易於管理的 SAN 解決方案，可減少許多與企業儲存體和資料保護相關聯的問題和支出。它使用專屬裝置 (Microsoft Azure StorSimple 裝置)、與雲端服務整合以及提供一組管理工具，提供所有企業儲存體 (包括雲端儲存體) 的整體檢視。

StorSimple 使用儲存體分層管理各種儲存媒體中儲存的資料。目前的工作集是以內部部署的方式儲存在固態硬碟 (SSD) 中，較不常使用的資料會儲存於硬碟機 (HDD)，而封存資料則會推送到雲端。此外，StorSimple 使用重複資料刪除和壓縮來減少資料使用的儲存體數量。儲存體分層程序會如下進行：

1. 系統管理員設定 Microsoft Azure 雲端儲存體帳戶。
2. 系統管理員使用序列主控台和 StorSimple Manager 服務 (在 Azure 管理入口網站中執行)，來設定裝置以及建立磁碟區和資料保護原則。內部部署機器 (如檔案伺服器) 使用 Internet Small Computer System Interface (iSCSI) 來存取 StorSimple 裝置。
3. 一開始，StorSimple 將資料儲存在裝置的快速 SSD 層上。
4. 當 SSD 層接近容量上限時，StorSimple deduplicates 會刪除重複資料並壓縮最舊的資料區塊，並將它們移至 HDD 層。
5. 當 HDD 層接近容量上限時，StorSimple 會加密最舊的資料區塊，並透過 HTTPS 將它們安全地傳送到 Microsoft Azure 儲存體帳戶。
6. Microsoft Azure 會在其資料中心和遠端資料中心建立多個資料複本，確保災害發生時可以復原資料。 
7. 當檔案伺服器要求雲端中儲存的資料時，StorSimple 會順暢地傳回它，並將複本儲存在 StorSimple 裝置的 SSD 層上。

除了儲存體管理外，StorSimple 資料保護功能可讓您建立隨選和排程備份，並將其儲存在本機或雲端中。備份採用累加快照的形式，這表示建立及還原備份更快速。雲端快照在災害復原案例中至關重要，因為這些快照會取代次要儲存體系統 (例如磁帶備份)，並讓您將資料還原到資料中心或在必要時還原至其他網站。

>[AZURE.NOTE]包含軟體更新 1 或更新版本的 StorSimple 8000 Series 支援使用 RRS 的 Amazon S3、HP 和 OpenStack 雲端服務，以及 Microsoft Azure。(您仍然需要 Microsoft Azure 儲存體帳戶進行裝置管理。) 如需詳細資訊，請參閱[為服務設定新的儲存體帳戶](storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service)。

## 為何要使用 StorSimple？

Microsoft Azure StorSimple 提供下列優點：

- **透明整合** – Microsoft Azure StorSimple 使用 iSCSI 通訊協定，以無形的方式連結資料儲存設備。這可確保儲存在雲端、在資料中心或在遠端伺服器上的資料會看似儲存在單一位置。
- **降低儲存體成本** – Microsoft Azure StorSimple 會配置足夠符合目前需求的本機或雲端儲存體，且只在必要時才擴充雲端儲存體。它可以進一步地降低儲存體需求和支出，方法是消除相同資料的備援版本 (重複資料刪除)，並使用壓縮。
- **簡化儲存體管理** – Microsoft Azure StorSimple 提供系統管理工具，可用來設定和管理在內部部署、在遠端伺服器上和在雲端中儲存的資料。此外，您可以從 Microsoft Management Console (MMC) 嵌入式管理單元來管理備份和還原功能。StorSimple 提供一個單獨的選用介面，可用來將 StorSimple 管理和資料保護服務延伸到儲存在 SharePoint 伺服器上的內容。 
- **改進災害復原和提高合規性** – Microsoft Azure StorSimple 不需要延長的回復時間。相反地，它會在需要時還原資料。這表示正常作業可以在最少干擾的情況下繼續執行。此外，您還可以設定原則以指定備份排程和資料保留。
- **資料行動性** – 您可以從其他站台存取上傳至 Microsoft Azure 雲端服務的資料，以供復原和移轉使用。此外，您可以使用 StorSimple，在 Microsoft Azure 中執行的虛擬機器 (VM) 上設定 StorSimple 虛擬裝置。VM 然後可以使用虛擬裝置來存取儲存的資料，以供測試或復原使用。 

下圖提供 Microsoft Azure StorSimple 解決方案的高階檢視。

![StorSimple 架構](./media/storsimple-overview/hcs-data-services-storsimple-system-architecture.png)

**Microsoft Azure StorSimple 架構**

## StorSimple 元件

Microsoft Azure StorSimple 解決方案包括下列元件：

- **Microsoft Azure StorSimple 裝置** - 包含固態硬碟 (SSD) 和硬碟 (HDD) 的內部部署混合式存放裝置陣列，提供備援控制器和自動容錯移轉功能。控制器可管理儲存體分層、將目前使用的 (或熱) 資料放在本機儲存體 (在裝置或在內部部署伺服器中)，而將不常使用的資料移到雲端。
- **StorSimple 虛擬裝置** – 也就是 StorSimple 虛擬應用裝置，這是 StorSimple 裝置的軟體版本，可複寫實體混合式存放裝置的架構和大部分功能。StorSimple 虛擬裝置會在 Azure 虛擬機器中的單一節點上執行。虛擬裝置適用於測試和小型試驗案例。您無法在 StorSimple 裝置或在內部部署伺服器上建立 StorSimple 虛擬裝置。
- **Windows PowerShell for StorSimple** – 可讓您管理 StorSimple 裝置的命令列介面。Windows PowerShell for StorSimple 的功能包括：可讓您註冊您的 StorSimple 裝置、在您的裝置上設定網路介面、安裝特定類型的更新，以及透過存取支援工作階段及變更裝置狀態來疑難排解您的裝置。藉由連接至序列主控台或使用 Windows PowerShell 遠端處理，您可以存取 Windows PowerShell for StorSimple。
- **Azure PowerShell StorSimple Cmdlet** – 一組 Windows PowerShell Cmdlet，可讓您從命令列將服務層級和移轉工作自動化。如需適用於 StorSimple 的 Azure PowerShell Cmdlet 的詳細資訊，請移至 [Cmdlet 參考](https://msdn.microsoft.com/library/dn920427.aspx)。
- **StorSimple Manager 服務** – Azure 管理入口網站的延伸模組，可讓您從單一 Web 介面管理 StorSimple 裝置或 StorSimple 虛擬裝置。您可以使用 StorSimple Manager 服務來建立和管理服務、檢視和管理裝置、檢視警示、管理磁碟區，以及檢視和管理備份原則與備份類別。
- **StorSimple Snapshot Manager** – MMC 嵌入式管理單元，可使用磁碟區群組和 Windows 磁碟區陰影複製服務產生應用程式一致備份。此外，您可以使用 StorSimple Snapshot Manager 建立備份排程及複製或還原磁碟區。 
- **StorSimple Adapter for SharePoint** – 此工具可將 Microsoft Azure StorSimple 儲存體和資料保護明確延伸至 SharePoint 伺服器陣列，並可讓您從 SharePoint 管理入口網站檢視和管理 StorSimple 儲存體。

## 後續步驟

深入了解 [StorSimple](https://azure.microsoft.com/documentation/services/storsimple/)。

深入了解 [StorSimple 元件和術語](storsimple-components.md)。


 

<!---HONumber=Oct15_HO1-->