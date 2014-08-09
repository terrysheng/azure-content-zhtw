<properties umbracoNaviHide="0" pageTitle="Storage Account Concepts | Azure" metaKeywords="Azure storage, storage service, service, storage account, account, create storage account, create account" description="Learn about storage account concepts." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Storage Account Concepts" authors="" />

儲存體帳戶概念
==============

-   **地理區域備援儲存體 (GRS)** 地理區域備援儲存體會將資料緊密複寫到相同地區內的次要位置，以提供最高層級的儲存體持久性。如此一來，在主要位置發生重大故障時，即可進行容錯移轉。次要位置座落在與主要位置相距甚遠的地點。GRS 是透過名為「地理區域複寫」**的功能來實作 (儲存體帳戶預設會開啟該功能)，但如果您不想使用該功能 (例如，公司規定禁止使用) ，則可將之關閉。如需詳細資訊，請參閱 [Windows Azure 的地理區域複寫簡介](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx) (英文)。

-   **本機備援儲存體 (LRS)** 本機備援儲存體在單一位置內提供高度持久、可用的儲存體。使用本機備援儲存體時，帳戶資料會在相同的資料中心內進行複寫三次。Azure 中所有儲存體的備援都在本機。如需更高的持久性，您可以開啟地理區域複寫功能。使用本機備援儲存體可享有折扣費率。如需定價資訊，請參閱 [Azure 定價一覽](http://www.windowsazure.com/en-us/pricing/details/)。

-   **同質群組** 「同質群組」**是依地理位置將您在 Azure 中的雲端服務部署與儲存體帳戶進行分組的結果。同質群組會將電腦工作負載置於相同的資料中心內或目標使用者對象附近，因而能改善服務效能。同樣地，出口流量並不會產生費用。

-   **儲存體帳戶端點** 儲存體帳戶的「端點」**代表存取 Blob、資料表或佇列時所依據的最高層級命名空間。儲存體帳戶的預設端點具有下列格式：

    -   Blob 服務：http://*mystorageaccount*.blob.core.windows.net

    -   資料表服務：http://*mystorageaccount*.table.core.windows.net

    -   佇列服務：http://*mystorageaccount*.queue.core.windows.net

-   **儲存體帳戶 URL** 用以存取儲存體帳戶中某物件的 URL，可藉由在端點後附加該物件在儲存體帳戶中的位置來建置。例如，Blob 位址的格式可能如下：http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*。

-   **儲存體存取金鑰** 當您建立儲存體帳戶時，Azure 會產生兩個 512 位元的儲存體存取金鑰，作為存取儲存體帳戶時的驗證憑藉。透過提供這兩個儲存體存取金鑰，Azure 讓您可重新產生金鑰，同時又不需中斷儲存體服務或對該服務的存取。

-   **最小度量或詳細資訊度量** 您可以在儲存體帳戶的監視設定中設定最小度量或詳細資訊度量。「最小度量」**所收集的度量是 Blob、資料表與佇列服務的彙總資料 (例如入口流量/出口流量、可用性、延遲和成功百分比)。「詳細資訊度量」**則除了收集服務層級彙總，還會為相同的度量收集作業層級詳細資料。詳細度量能夠進一步分析在應用程式運作期間發生的問題。如需可用度量的完整清單，請參閱[儲存體分析度量資料表結構描述](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh343264.aspx) (英文)。如需儲存體監視的詳細資訊，請參閱[關於儲存體分析度量](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh343258.aspx) (英文)。

-   **記錄** 記錄是儲存體帳戶中的可設定功能，能夠將讀取、寫入和刪除 Blob、資料表與佇列的要求進行記錄。您可以在 Azure 管理入口網站中設定記錄功能，但無法在管理入口網站中檢視記錄檔。記錄檔是在儲存體帳戶中受到儲存和存取 (在 \$logs 容器中)。如需詳細資訊，請參閱[儲存體分析概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh343268.aspx)。


