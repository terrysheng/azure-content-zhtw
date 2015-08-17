<properties 
   pageTitle="StorSimple Adapter for SharePoint | Microsoft Azure"
   description="描述如何在 SharePoint 伺服器陣列中安裝 StorSimple Adapter for SharePoint。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/30/2015"
   ms.author="v-sharos" />

# StorSimple Adapter for SharePoint

## 概觀

StorSimple Adapter for SharePoint 是可讓您提供 Microsoft Azure StorSimple 彈性儲存和資料保護給 SharePoint 伺服器陣列的元件。您可以使用配接器將二進位大型物件 (BLOB) 內容從 SQL Server 內容資料庫移至 Microsoft Azure StorSimple 混合雲端儲存體裝置。

StorSimple Adapter for SharePoint 作為遠端 BLOB 儲存 (RBS) 提供者，使用 SQL Server 遠端 BLOB 儲存功能將非結構化 SharePoint 內容 (以 BLOB 形式)，儲存在 StorSimple 裝置所支援的檔案伺服器上。

>[AZURE.NOTE]StorSimple Adapter for SharePoint 支援 SharePoint Server 2010 遠端 BLOB 儲存 (RBS)。它不支援 SharePoint Server 2010 外部 BLOB 儲存體 (EBS)。

- 若要下載 StorSimple Adapter for SharePoint，請移至 Microsoft 下載中心的 [StorSimple Adapter for SharePoint][1]。

- 如需有關規劃 RBS 和 RBS 限制的資訊，請移至[決定在 SharePoint 2013 中使用 RBS][2] 或[規劃 RBS (SharePoint Server 2010)][3]。

此概觀的其餘部分簡短說明 StorSimple Adapter for SharePoint 的角色，以及您在安裝和設定配接器之前，應該注意的 SharePoint 容量和效能限制。檢閱此資訊之後，請移至 [StorSimple Adapter for SharePoint 安裝](#storsimple-adapter-for-sharepoint-installation)開始設定配接器。

### StorSimple Adapter for SharePoint 優點

在 SharePoint 網站中，內容會在一個或多個內容資料庫中儲存為非結構化 BLOB 資料。根據預設，這些資料庫裝載於執行 SQL Server 且位於 SharePoint 伺服器陣列中的電腦上。BLOB 可能快速增大，耗用大量的內部部署儲存體。因此，您可能想要尋找另一個較便宜的儲存體解決方案。SQL Server 提供一種稱為遠端 Blob 儲存 (RBS) 的技術，可讓您將 BLOB 內容儲存在檔案系統 (在 SQL Server 資料庫之外)。運用 RBS 時，Blob 可以位於執行 SQL Server 的電腦上的檔案系統，或儲存在另一部伺服器電腦上的檔案系統。

RBS 要求您必須使用 RBS 提供者，例如 StorSimple Adapter for SharePoint，才能在 SharePoint 中啟用 RBS。StorSimple Adapter for SharePoint 可搭配 RBS，讓您將 BLOB 移至 Microsoft Azure StorSimple 系統所支援的伺服器。Microsoft Azure StorSimple 接著可根據使用方式，在本機或在雲端中儲存 BLOB 資料。非常活躍的 BLOB (通常稱為第 1 層或熱門資料) 位在本機。較不活躍的資料和封存資料則位於雲端。在內容資料庫上啟用 RBS 之後，任何在 SharePoint 中建立的新 BLOB 內容會儲存在 StorSimple 裝置上，而不是在內容資料庫中。

RBS 的 Microsoft Azure StorSimple 實作提供下列優點：

- 將 BLOB 內容移至另一個伺服器可以減輕 SQL Server 上的查詢負荷，改善 SQL Server 的回應能力。 

- Azure StorSimple 使用重複資料刪除和壓縮來減少資料大小。

- Azure StorSimple 以本機和雲端快照集的形式提供資料保護。此外，如果您將資料庫本身放在 StorSimple 裝置，您可以用當機時保持一致的方式將內容資料庫和 BLOB 備份在一起。(僅 StorSimple 8000 系列裝置支援將內容資料庫移至裝置。5000 或 7000 系列不支援這項功能。)

- Azure StorSimple 包含災害復原功能，包括容錯移轉、檔案和磁碟區復原 (包括測試復原) 及快速還原資料。

- 您可以使用資料復原軟體，例如 Kroll Ontrack PowerControls，並搭配 BLOB 資料的 StorSimple 快照集，在項目層級復原 SharePoint 內容。(此資料復原軟體需另外購買)。

- StorSimple Adapter for SharePoint 外掛到 SharePoint 管理中心入口網站，可讓您從中央位置管理整個 SharePoint 解決方案。

將 BLOB 內容移至檔案系統可以節省更多成本並提供其他優點。例如，使用 RBS 就不需要購買昂貴的第 1 層儲存體，也而因為它會縮小內容資料庫，RBS 可減少 SharePoint 伺服器陣列中所需的資料庫數目。不過，其他因素也會影響儲存需求，例如資料庫大小限制和非 RBS 內容數量。如需有關使用 RBS 的優點與成本的詳細資訊，請參閱[規劃 RBS (SharePoint Foundation 2010)][4] 和[決定在 SharePoint 2013 中使用 RBS][5]。

### 容量和效能限制

在考慮於 SharePoint 解決方案中使用 RBS 之前，您應該注意 SharePoint Server 2010 和 SharePoint Server 2013 已測試的效能和容量限制，以及這些限制與可接受的效能有何關係。如需詳細資訊，請參閱＜SharePoint 2013 的軟體界限及限制＞。

設定 RBS 之前，請檢閱下列事項：

- 請確定內容的大小總計 (內容資料庫大小，加上任何相關聯的外部化 BLOB 大小) 不超過 SharePoint 所支援的 RBS 大小限制。這項限制為 200 GB。 

    **測量內容資料庫和 BLOB 大小**

     1. 在中央管理 WFE 上執行此查詢。啟動 SharePoint 管理命令介面，然後輸入下列 Windows PowerShell 命令來取得內容資料庫的大小：

        `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`

         此步驟取得磁碟上的內容資料庫大小。

     2. SQL Management Studio 的 SQL Server 方塊中，對每個內容資料庫執行下列 SQL 查詢，然後將結果和步驟 1 取得的數字相加。

        在 SharePoint 2013 內容資料庫中，輸入：

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`

        在 SharePoint 2010 內容資料庫中，輸入：

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`

        此步驟取得已外部化的 BLOB 大小。

- 我們建議您將所有 BLOB 和資料庫內容儲存在 StorSimple 裝置本機。StorSimple 裝置是提供高可用性的雙節點叢集。將內容資料庫和 BLOB 放在 StorSimple 裝置上提供高可用性。

    使用傳統的 SQL Server 移轉最佳作法，將內容資料庫移至 StorSimple 裝置。只有在資料庫的所有 BLOB 內容，都透過 RBS 移至檔案共用之後，才移動資料庫。如果您選擇將內容資料庫移至 StorSimple 裝置，我們建議您在裝置上將內容資料庫儲存體設定為主要磁碟區。

- 在 Microsoft Azure StorSimple 中，無法保證儲存在 StorSimple 裝置本機的內容不會移至 Microsoft Azure 雲端儲存體。若要確保內容資料庫保留在 StorSimple 裝置上，而不會移至 Microsoft Azure (這會延長 SharePoint 交易回應時間)，務必了解及管理 StorSimple 裝置上的其他工作負載。如果 StorSimple 裝置已裝載 SharePoint 內容資料庫工作負載和 SharePoint 檔案共用工作負載，建議您不要設定 StorSimple 裝置來裝載資料寫入率較高的工作負載。

- 如果您不在 StorSimple 裝置上儲存的內容資料庫，請使用支援 RBS 的傳統 SQL Server 高可用性最佳作法。SQL Server 叢集支援 RBS，而 SQL Server 鏡像不支援 RBS。

>[AZURE.WARNING]如果您尚未啟用 RBS，我們不建議您將內容資料庫移至 StorSimple 裝置。這是未經過測試的設定。
 
## StorSimple Adapter for SharePoint 安裝

您必須設定 StorSimple 裝置，並確定 SharePoint 伺服器陣列和 SQL Server 具現化符合所有必要條件，才能安裝 StorSimple Adapter for SharePoint。本教學課程描述 StorSimple Adapter for SharePoint 的組態需求，以及安裝和升級程序。

## 設定必要條件

您必須確定 StorSimple 裝置、SharePoint 伺服器陣列和 SQL Server 具現化符合下列必要條件，才能安裝 StorSimple Adapter for SharePoint。

### 系統需求

StorSimple Adapter for SharePoint 適用於下列的硬體和軟體：

- 支援的作業系統 – Windows Server 2008 R2 SP1、Windows Server 2012 或 Windows Server 2012 R2 

- 支援的 SharePoint 版本 – SharePoint Server 2010 或 SharePoint Server 2013

- 支援的 SQL Server 版本 – SQL Server 2008 Enterprise Edition、SQL Server 2008 R2 Enterprise Edition 或 SQL Server 2012 Enterprise Edition

- 支援的 StorSimple 裝置 – StorSimple 8000 系列、StorSimple 7000 系列或 StorSimple 5000 系列。

### StorSimple 裝置組態必要條件

StorSimple 裝置是一種區塊裝置，因此需要可裝載資料的檔案伺服器。我們建議您使用另外的伺服器，而不是 SharePoint 伺服器陣列中現有的伺服器。此檔案伺服器必須與裝載內容資料庫的 SQL Server 電腦位於相同的區域網路 (LAN)。

>[AZURE.TIP]
>
>- 如果您為了高可用性而設定 SharePoint 伺服器陣列，則也應該為了高可用性而部署檔案伺服器。
>
>- 如果您不在 StorSimple 裝置上儲存的內容資料庫，請使用支援 RBS 的傳統高可用性最佳作法。SQL Server 叢集支援 RBS，而 SQL Server 鏡像不支援 RBS。

請確定您的 StorSimple 裝置已正確設定，也已設定適當的磁碟區來支援 SharePoint 部署，且可以從 SQL Server 電腦存取。如果您尚未部署和設定您的 StorSimple 裝置，請移至[部署內部部署 StorSimple 裝置](storsimple-deployment-walkthrough.md)。請記下 StorSimple 裝置的 IP 位址，StorSimple Adapter for SharePoint 安裝期間需要用到。

此外，請確定要用於 BLOB 外部化的磁碟區符合下列需求：

- 此磁碟區必須使用 64 KB 配置單位大小格式化。

- Web 前端 (WFE) 和應用程式伺服器必須能夠透過通用命名慣例 (UNC) 路徑來存取此磁碟區。

- SharePoint 伺服器陣列必須設定為寫入此磁碟區。

>[AZURE.NOTE]安裝和設定配接器之後，所有 BLOB 外部化都必須透過 StorSimple 裝置進行 (此裝置會向 SQL Server 呈現磁碟區並管理儲存層)。您無法使用任何其他目標進行 BLOB 外部化。
 
如果您打算使用 StorSimple Snapshot Manager 建立 BLOB 和資料庫資料的快照集，務必將 StorSimple Snapshot Manager 安裝在資料庫伺服器上，它才能使用 SQL 寫入器服務來實作 Windows 磁碟區陰影複製服務 (VSS)。

>[AZURE.IMPORTANT]StorSimple Snapshot Manager 不支援 SharePoint VSS 寫入器，無法建立 SharePoint 資料的應用程式一致快照集。在 SharePoint 案例中，StorSimple Snapshot Manager 只提供當機時保持一致的備份。
 
## SharePoint 伺服器陣列組態必要條件

請確定您的 SharePoint 伺服器陣列已正確設定，如下：

- 確認您的 SharePoint 伺服器陣列處於良好狀態，並檢查下列項目: 

- 所有在伺服陣列中註冊的 SharePoint WFE 和應用程式伺服器正在執行，而且可以從您將安裝 StorSimple Adapter for SharePoint 伺服器進行 Ping。

- SharePoint 計時器服務 (SPTimerV3 或 SPTimerV4) 正在每一部 WFE 伺服器和應用程式伺服器上執行。

- SharePoint 計時器服務和用來執行 SharePoint 管理中心網站的 IIS 應用程式集區，具有系統管理權限。

- 請確定已停用 Internet Explorer 增強式安全性內容 (IE ESC)。請遵循下列步驟來停用 IE ESC：

    1. 關閉 Internet Explorer 的所有執行個體。

    2. 啟動伺服器管理員。

    3. 在左窗格中按一下 [**本機伺服器**]。

    4. 在右窗格中，按一下 [**IE 增強式安全性設定**] 旁邊的 [**開啟**]。

    5. 在 [**系統管理員**下，按一下 [**關閉**]。

    6. 按一下 [確定]。

## 遠端 BLOB 儲存 (RBS) 必要條件

請確定您使用支援的 SQL Server 版本。僅下列版本才支援和能夠使用 RBS：

- SQL Server 2008 Enterprise Edition

- SQL Server 2008 R2 Enterprise Edition

- SQL Server 2012 Enterprise Edition

只有在 StorSimple 裝置向 SQL Server 呈現的磁碟區上，才能外部化 BLOB。不支援在其他目標上進行 BLOB 外部化。

當您完成所有必要設定步驟時，請移至[安裝 StorSimple Adapter for SharePoint](#install-the-storsimple-adapter-for-sharepoint)。

## 安裝 StorSimple Adapter for SharePoint

使用下列步驟來安裝 StorSimple Adapter for SharePoint。如果您要重新安裝軟體，請參閱[升級或重新安裝 StorSimple Adapter for SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint)。安裝所需的時間取決於您的 SharePoint 伺服器陣列中的 SharePoint 資料庫總數。

[AZURE.INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]


## 設定 RBS

安裝 StorSimple Adapter for SharePoint 之後，請依下列程序設定 RBS。

>[AZURE.TIP]StorSimple Adapter for SharePoint 外掛到 SharePoint 管理中心入口網站頁面，可讓您在 SharePoint 伺服器陣列中的每個內容資料庫上啟用或停用 RBS。不過，在內容資料庫上啟用或停用 RBS 會導致 IIS 重設，而且根據您的伺服器陣列設定，可能會短暫地中斷 SharePoint Web 前端 (WFE) 的可用性。(有一些因素可以限制或避免此中斷狀況，例如使用前端負載平衡器、目前的伺服器工作負載等等)。 為了避免使用者因為中斷而受影響，建議您只在規劃的維護期間啟用或停用 RBS。

[AZURE.INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]
 

## 設定記憶體回收

從 SharePoint 網站刪除物件時，不會自動從 RBS 存放磁碟區刪除這些物件。而是由一個非同步的背景維護程式，從檔案存放區刪除被遺棄的 BLOB。系統管理員可以排程定期執行此程序，或需要時才啟動此程序。

當您啟用 RBS 時，此維護程式 (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) 會自動安裝在所有 SharePoint WFE 伺服器和應用程式伺服器上。此程式安裝在下列位置：<boot drive>:\\Program Files\\Microsoft SQL Remote Blob Storage 10.50\\Maintainer\\

如需有關設定和使用維護程式的資訊，請參閱[在 SharePoint Server 2013 中維護 RBS][8]。

>[AZURE.IMPORTANT]RBS 維護程式會消耗大量資源。您應該將它排程在 SharePoint 伺服器陣列的活動量較少期間執行。

### 立即刪除被遺棄的 BLOB

如果您需要立即刪除被遺棄的 BLOB，您可以使用下列指示。請注意，這些指示是如何在具有下列元件的 SharePoint 2013 環境中完成此作業的範例：

- 內容資料庫名稱是 WSS\_Content。
- SQL Server 名稱是 SHRPT13-SQL12\\SHRPT13。
- Web 應用程式名稱是 SharePoint–80。

[AZURE.INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]


## 升級或重新安裝 StorSimple Adapter for SharePoint

使用下列程序升級 SharePoint 伺服器，再重新安裝 StorSimple Adapter for SharePoint，或只是在現有的 SharePoint 伺服器陣列中升級或重新安裝配接器。

>[AZURE.IMPORTANT]在嘗試升級 SharePoint 軟體和 (或) 升級或重新安裝 StorSimple Adapter for SharePoint 之前，請檢閱下列資訊：
>
>- 先前透過 RBS 移到外部儲存體的任何檔案，必須等到重新安裝完成並重新啟用 RBS 功能之後才能使用。為了限制使用者受影響的程度，請在規劃的維護期間執行任何升級或重新安裝。
>
>- 根據 SharePoint 伺服器陣列中的 SharePoint 資料庫總數而定，升級/重新安裝所需的時間可能不同。
>
>- 升級/重新安裝完成之後，您需要針對內容資料庫啟用 RBS。如需詳細資訊，請參閱[設定 RBS](#configure-rbs)。
>
>- 如果要設定 RBS 的 SharePoint 伺服器陣列有非常大量的資料庫 (超過 200 個)，**SharePoint 管理中心**頁面可能會逾時。如果發生這種情況，請重新整理頁面。這不會影響設定程序。

## 後續步驟

[深入了解 StorSimple](storsimple-overview.md)

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/zh-tw/library/ff943565.aspx

<!---HONumber=August15_HO6-->