<properties linkid="manage-services-what-is-a-storage-account" urlDisplayName="What is a Storage Account" pageTitle="What is a storage account? | Microsoft Azure" metaKeywords="" description="Learn about the different types of storage accounts available in Azure, and get definitions for key storage terms." metaCanonical="" services="storage" documentationCenter="" title="What is a Storage Account?" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

什麼是儲存體帳戶？
==================

Azure 儲存體包括三項服務：Blob 儲存體、資料表儲存體和佇列儲存體。每個儲存體帳戶中都已包含這三項服務。儲存體帳戶可提供處理 Blob、佇列和資料表所需的唯一命名空間。

在 2012 年 6 月 8 日 (含) 以後建立的儲存體帳戶可包含最多 200TB 的 Blob、佇列和資料表資料；如果儲存體帳戶是在該日期之前建立，則總容量為 100TB。您可以在單一訂閱之下建立最多 20 個唯一命名的儲存體帳戶。如需儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](http://msdn.microsoft.com/en-us/library/dn249410.aspx) (英文)。

所有關於您儲存體帳戶的資訊 (包括建立時間) 都可在管理入口網站中 **[儲存體]**的 **[儀表板]** 頁面取得。

儲存體的成本是根據四項因素來計算：儲存體容量、複寫配置、儲存體交易以及出口流量。儲存體容量是指您用於儲存資料的儲存體帳戶配額。若只是儲存資料，則成本是由您所儲存的資料量和複寫資料的方式來決定。交易是指對 Azure 儲存體進行的所有讀取和寫入作業。出口流量是指傳出 Azure 地區的資料。當您儲存體帳戶中的資料受不同地區中執行的應用程式存取時，不論該應用程式是雲端服務還是其他某類應用程式，您都要負擔出口流量的費用。(若為 Azure 服務，您可以採取步驟，將資料和服務群組在相同的資料中心，以減少或消除出口流量費用。)

[儲存體定價詳細資料](http://www.windowsazure.com/en-us/pricing/details/#storage)頁面提供了儲存體容量、複寫和交易的詳細定價資料。[資料傳輸定價詳細資料](http://www.windowsazure.com/en-us/pricing/details/data-transfers/)則提供了出口流量的詳細定價資訊。

概念
----

-   **地理區域備援儲存體 (GRS)** 地理區域備援儲存體會將資料緊密複寫到相同地區內的次要位置，以提供最高層級的儲存體持久性。如此一來，在主要位置發生重大故障時，即可進行容錯移轉。次要位置座落在與主要位置相距甚遠的地點。GRS 是透過名為<em>「地理區域複寫」</em>的功能來實作 (儲存體帳戶預設會開啟該功能)，但如果您不想使用該功能 (例如，公司規定禁止使用) ，則可將之關閉。如需詳細資訊，請參閱 [Azure 儲存體的地理區域複寫簡介](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx) (英文)。

-   **本機備援儲存體 (LRS)** 本機備援儲存體在單一位置內提供高度持久、可用的儲存體。使用本機備援儲存體時，帳戶資料會在相同的資料中心內進行複寫三次。Azure 中所有儲存體的備援都在本機。如需更高的持久性，您可以開啟地理區域複寫功能。使用本機備援儲存體可享有折扣費率。如需定價資訊，請參閱[定價詳細資料](http://www.windowsazure.com/en-us/pricing/details/#storage)。

-   **同質群組** <em>「同質群組」</em>是依地理位置將您在 Azure 中的雲端服務部署與儲存體帳戶進行分組的結果。同質群組會將電腦工作負載置於相同的資料中心內或目標使用者對象附近，因而能改善服務效能。此外，就出口流量而言，當您儲存體帳戶中的資料是由同一同質群組中的服務存取時，並不會產生任何費用。

-   **儲存體帳戶端點** 儲存體帳戶的<em>「端點」</em>代表存取 Blob、資料表或佇列時所依據的最高層級命名空間。儲存體帳戶的預設端點具有下列格式：

    -   Blob 服務：http://*mystorageaccount*.blob.core.windows.net

    -   資料表服務：http://*mystorageaccount*.table.core.windows.net

    -   佇列服務：http://*mystorageaccount*.queue.core.windows.net

-   **儲存體帳戶 URL** 若要存取儲存體帳戶中的物件，在端點後加上該物件在儲存體帳戶中的位置，就是可用於存取該物件的 URL。例如，Blob 位址的格式可能如下：http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*。

-   **儲存體存取金鑰** 當您建立儲存體帳戶時，Azure 會產生兩個 512 位元的儲存體存取金鑰，作為存取儲存體帳戶時的驗證憑藉。透過提供這兩個儲存體存取金鑰，Azure 讓您可重新產生金鑰，同時又不需中斷儲存體服務或對該服務的存取。

-   **最小度量或詳細資訊度量** 您可以在儲存體帳戶的監視設定中設定最小度量或詳細資訊度量。「最小度量」**所收集的度量是 Blob、資料表與佇列服務的彙總資料 (例如入口流量/出口流量、可用性、延遲和成功百分比)。「詳細資訊度量」**則除了收集服務層級彙總，還會為相同的度量收集作業層級詳細資料。詳細資訊度量可供進一步分析在應用程式運作期間發生的問題。如需可用度量的完整清單，請參閱[儲存體分析度量資料表結構描述](http://msdn.microsoft.com/en-us/library/windowsazure/hh343264.aspx) (英文)。如需儲存體監視的詳細資訊，請參閱[關於儲存體分析度量](http://msdn.microsoft.com/en-us/library/windowsazure/hh343258.aspx) (英文)。

-   **記錄** 記錄是儲存體帳戶中的可設定功能，能夠將讀取、寫入和刪除 Blob、資料表與佇列的要求進行記錄。您可以在 Azure 管理入口網站中設定記錄功能，但無法在管理入口網站中檢視記錄檔。記錄檔是在儲存體帳戶中受到儲存和存取 (在 $logs 容器中)。如需詳細資訊，請參閱[儲存體分析概觀](http://msdn.microsoft.com/en-us/library/windowsazure/hh343268.aspx)。


