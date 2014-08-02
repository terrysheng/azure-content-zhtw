<properties linkid="storage-introduction" urlDisplayName="Introduction to Azure Storage" pageTitle="Introduction to Storage | Microsoft Azure" metaKeywords="Get started  Azure storage introduction  Azure storage overview  Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage  Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure " description="An overview of Microsoft Azure Storage." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="Introduction to Microsoft Azure Storage" authors="tamram" />

Microsoft Azure 儲存體簡介
==========================

本文針對開發人員、IT 專業人員和業務決策者，提供 Microsoft Azure 儲存體簡介。在閱讀本文後，您將學到：

-   何謂 Azure 儲存體，以及可以如何在雲端、行動、伺服器和桌面應用程式中加以充分運用
-   可以在 Azure 儲存體服務中儲存的資料種類：Blob、資料表及佇列儲存體
-   如何管理 Azure 儲存體的資料存取
-   如何透過備援和複寫來保護 Azure 儲存體資料
-   若要打造第一個 Azure 儲存體應用程式下一步該怎麼做

何謂 Azure 儲存體？
-------------------

雲端運算針對資料需要可擴充、可持久和高可用性儲存體的應用程式，提供了全新案例；這也是 Microsoft 開發 Azure 儲存體的真正原因。除了可讓開發人員打造支援全新案例的大規模應用程式之外，Azure 儲存體還針對 Microsoft 的「基礎結構即服務」功能提供儲存基礎，並更進一步證明了其穩固性。

Azure 儲存體可大幅擴充，因此您可以儲存並處理數百 TB 的資料，藉此支援科學、財務分析和媒體應用程式等所需的巨量資料案例。或者您可以儲存小型企業網站所需的少量資料。無論您的需求屬於何者，您只需支付儲存的資料。Azure 儲存體目前儲存了數十兆的獨特客戶物件，且平均每秒處理上百萬個要求。

Azure 儲存體非常有彈性，因此您可以為大量的全球使用者設計應用程式，然後視需要擴充這些應用程式 - 需要同時擴充儲存體數量和交易數目。您只需在使用時，針對所使用資料進行付費即可。

Azure 儲存體使用自動分割系統，可自動根據流量負載平衡您的資料。這代表隨著應用程式需求不斷成長，Azure 儲存體會自動配置適當資源以符合需求。

您可從世界各地、任何應用程式類型 (無論是在雲端、桌面、內部部署伺服器、行動或平板裝置上執行) 存取 Azure 儲存體。您可以在行動案例中使用 Azure 儲存體，案例中的應用程式會在裝置上儲存資料子集，然後將它與儲存在雲端中的整組資料同步處理。

為了方便開發，Azure 儲存體支援使用各種作業系統 (包括 Windows 和 Linux) 和多種程式設計語言 (包括 .NET、Java 和 C++) 的用戶端。Azure 儲存體還可透過簡單 REST API 公開資料資源，REST API 適用於任何能夠透過 HTTP/HTTPS 傳送與接收資料的用戶端。

Azure 儲存體服務簡介
--------------------

Azure 儲存體服務包括 Blob 儲存體、資料表儲存體和佇列儲存體：

-   **Blob 儲存體**可儲存檔案資料。Blob 可以是任何類型的文字或二進位資料，例如文件、媒體檔案或應用程式安裝程式。
-   **資料表儲存體**可儲存結構化的資料集。資料表儲存體屬於 NoSQL 索引鍵屬性資料儲存，可允許快速開發和迅速存取大量資料。
-   **佇列儲存體**針對工作流程處理及雲端服務元件間的通訊，提供可靠的訊息服務。

每個儲存體帳戶中都已包含這三項服務。儲存體帳戶是指可讓您存取 Azure 儲存體的唯一命名空間。每個儲存體帳戶都可包含包含多達 200 TB 的 Blob、佇列和資料表組合資料。

在建立儲存體帳戶之前，您必須擁有 Azure 訂閱，訂閱是指可讓您存取各種 Azure 服務的方案。單一訂閱可讓您建立最多 20 個唯一命名的儲存體帳戶。

您可以利用[免費試用版](/en-us/pricing/free-trial/)來開始使用 Azure。在決定購買方案之後，您便可以選擇各種[購買選項](/en-us/pricing/purchase-options/)。如果您是 [MSDN 訂閱者](/en-us/pricing/member-offers/msdn-benefits-details/)，您將取得可在 Azure 服務 (包括 Azure 儲存體) 中使用的每月免費額度。

Blob 儲存體
-----------

若是擁有大量非結構化資料要儲存於雲端的使用者，Blob 儲存體提供具有成本效益且可擴充的解決方案。您可以使用 Blob 儲存體來儲存如下所示的內容：

-   文件
-   社交資料 (例如照片、視訊、音樂和部落格)
-   檔案、電腦、資料庫和裝置的備份
-   Web 應用程式的影像和文字
-   雲端應用程式的組態資料
-   巨量資料 (例如記錄和其他大型資料集)

每個 Blob 會組織成一個容器。容器也提供對物件群組指派安全原則的實用方式。儲存體帳戶可包含任意數目的容器，而容器可包含任意數目的 Blob，儲存體帳戶的容量限制高達 200 TB。

Blob 儲存體提供兩種 Blob 類型：區塊 Blob 和頁面 Blob (磁碟)。區塊 Blob 已針對串流和儲存雲端物件進行最佳化，是儲存文件、媒體檔案、備份等的不錯選擇。區塊 Blob 的大小可高達 200 GB。頁面 Blob 已針對顯示 IaaS 磁碟與支援隨機寫入進行最佳化，且大小可能可以高達 1 TB。Azure 虛擬機器網路附加 IaaS 磁碟是以頁面 Blob 方式儲存的 VHD。

若是超大型資料集，網路限制會使得透過線路上傳或下載資料至 Blob 儲存體變得不切實際，您可以將硬碟送至 Microsoft，以便使用 [Azure 匯入/匯出服務](http://www.windowsazure.com/documentation/articles/storage-import-export-service/)直接從資料中心匯入或匯出資料。您也可以複製儲存體帳戶或跨不同儲存體帳戶內的 Blob 資料。

資料表儲存體
------------

新式應用程式通常會要求以超越前幾代軟體需求的延伸性和彈性儲存資料。資料表儲存體提供高可用性且可大幅擴充的儲存體，方便您的應用程式自動擴充以滿足使用者需求。資料表儲存體屬於 Microsoft? 的 NoSQL 索引鍵屬性儲存，它的無結構描述設計讓它有別於傳統的關聯式資料庫。透過無結構描述資料儲存，便可輕易隨著應用程式發展需求改寫資料。資料表儲存體非常容易使用，方便開發人員可以快速建立應用程式。所有類型的應用程式都可以用快速且具成本效益的方式存取資料。相較於類似資料量的傳統 SQL，資料表儲存體通常可大幅降低成本。

資料表儲存體屬於索引鍵屬性儲存，代表資料表中的每個實體會與屬性名稱一起儲存。此屬性名稱 (索引鍵) 可用來篩選與指定選取條件。索引鍵連同屬性集合及其值可組成實體。因為資料表儲存體沒有結構描述，因此相同資料表中的實體可包含不同屬性集合，且這些屬性可以是不同類型。

您可以使用資料表儲存體來儲存具彈性的資料集，例如 Web 應用程式的使用者資料、通訊錄、裝置資訊，以及服務所需的任何其他中繼資料類型。您可以在資料表中儲存任意數目的實體，且儲存體帳戶可能包含任意數目的資料表，儲存體帳戶的容量限制高達 200 TB。

如同 Blob 和佇列，開發人員可以使用標準 REST 通訊協定來管理與存取資料表儲存體，不過，資料表儲存體也支援 OData 通訊協定的子集、簡化進階查詢功能，及啟用 JSON 和 AtomPub (以 XML 為基礎) 格式。

在現今的網際網路架構應用程式中，NoSQL 資料庫 (如資料表儲存體) 提供傳統關聯式資料庫的熱門替代方式。

佇列儲存體
----------

設計擴充性的應用程式時，會經常分離應用程式元件，以便進行個別擴充。佇列儲存體針對應用程式元件間的非同步通訊，提供可靠的訊息服務解決方案，無論應用程式元件是在雲端、桌面、內部部署伺服器或行動裝置上執行。佇列儲存體也支援管理非同步工作並建置處理工作流程。

儲存體帳戶可包含任意數目的佇列。佇列可包含任意數目的訊息，儲存體帳戶的容量限制高達 200 TB。個別訊息的大小可能高達 64 KB。

存取 Blob、資料表和佇列資源
---------------------------

依預設，只有儲存體帳戶擁有者可以存取儲存體帳戶內的資源。為了資料的安全性，針對帳戶內資源所做的每個要求都必須經過驗證。驗證會仰賴共用金鑰模型。Blob 也可設定為支援匿名驗證。

建立儲存體帳戶時所指派的兩組存取金鑰可用於驗證。當您定期重新產生金鑰作為一般安全性金鑰管理作法時，擁有兩組金鑰可確保您的應用程式保持為可用狀態。

如果您確實需要允許使用者對儲存體資源進行控管存取，則您可以建立[共用存取簽章](../storage-dotnet-shared-access-signature-part-1/)。共用存取簽章是指可附加在 URL 後面的權杖，可提供容器、Blob、資料表或佇列的委派存取。在權杖有效的期限內，擁有權杖的任何人都可以存取它在指定權限中所指向的資源。

最後，您可以指定容器及其 Blob 或特定的 Blob 是否可供公用存取。當您將容器或 Blob 指定為公用時，任何人都可以進行匿名讀取；不需要驗證。公用容器和 Blob 對於公開資源 (例如網站上所託管的媒體和文件) 而言非常有用。若要縮短全球使用者的網路延遲，您可以使用 Azure CDN 來快取網站所用的 Blob 資料。

持久性和高可用性的複寫
----------------------

儲存體帳戶中的資料會進行複寫以確保持久性及高可用性，即使是面對暫時性的硬體故障，也可滿足 [Azure 儲存體 SLA](/en-us/support/legal/sla/)。您有三個選項來複寫自己的儲存體帳戶資料：

-   *本機備援儲存體 (LRS)* 會在單一資料中心內複寫三次。當您將資料寫入 Blob、佇列或資料表時，所有三個複本會同時執行寫入操作。LRS 會保護您的資料免於一般硬體故障。
-   *地理區域備援儲存體 (GRS)* 會在單一地區內複寫三次，也會非同步複寫至與主要區域相距甚遠的次要區域。GRS 會保留相當於 6 個複本的資料 (每個區域 3 個複本)。如果因為發生重大服務中斷或災害而無法復原主要區域，GRS 可讓 Microsoft 容錯移轉至次要區域。相較於本機備援儲存體，我們建議使用 GRS。
-   *讀取存取地理區域備援儲存體 (RA-GRS)* 提供地理區域備援儲存體的上述所有好處，並在發生無法使用主要區域的情況下，允許次要區域的資料讀取存取。除了持久性之外若還需最大可用性，我們建議使用讀取存取地理區域備援儲存體。

您可以在[儲存體定價詳細資料](/en-us/pricing/details/storage/)頁面上找到 LRS、GRS 和 RA-GRS 之間的價格差異。

定價
----

客戶會根據四個因素來支付 Azure 儲存體的費用：使用的儲存體容量、選取的複寫選項、針對服務所做的要求次數，和出口流量。

儲存體容量是指您用於儲存資料的儲存體帳戶配額。若只是儲存資料，則成本是由您所儲存的資料量和複寫資料的方式來決定。對 Azure 儲存體進行的每個讀取和寫入操作都是針對服務提出要求。出口流量是指傳出 Windows Azure 區域的資料。當您儲存體帳戶中的資料受不同地區中執行的應用程式存取時，不論該應用程式是雲端服務還是其他某類應用程式，您都要負擔出口流量的費用。(若為 Windows Azure 服務，您可以採取步驟，將資料和服務群組在相同的資料中心，以減少或消除處理和出口流量費用。)

[儲存體定價詳細資料](/en-us/pricing/details/storage/)頁面提供了儲存體容量、複寫和交易的詳細定價資料。[資料傳輸定價詳細資料](/en-us/pricing/details/data-transfers/)則提供了出口流量的詳細定價資訊。您可以使用 [Azure 儲存體定價計算機](/en-us/pricing/calculator/?scenario=data-management)，以協助消除成本。

針對儲存體進行開發
------------------

Azure 儲存體可透過 [REST API](http://msdn.microsoft.com/library/windowsazure/dd179355.aspx) 公開儲存體資源，任何可提出 HTTP/HTTPS 要求的語言都可呼叫 REST API。另外，Azure 儲存體還提供了幾種熱門語言的程式設計程式庫。這些程式庫可透過處理詳細資料 (例如同步和非同步叫用、進行批次操作、例外狀況管理、自動重試、運作方式等等) 來簡化使用 Azure 儲存體的許多項目。程式庫目前適用於下列語言和平台，以及正在研發的其他語言和平台：

-   [.NET](http://msdn.microsoft.com/library/dn495001(v=azure.10).aspx)
-   [機器碼](http://msdn.microsoft.com/library/dn495438.aspx)
-   [Java](/en-us/develop/java/)
-   [Node.js](../storage/#node)
-   [PHP](../storage/#php)
-   [Ruby](../storage/#ruby)
-   [Python](../storage/#python)
-   [PowerShell](http://msdn.microsoft.com/library/dn495240.aspx)

後續步驟
--------

若要開始使用 Azure 儲存體，請探索以下資源：

-   [Azure 儲存體文件](/en-us/documentation/services/storage/)
-   [Azure 儲存體延展性和效能目標](http://msdn.microsoft.com/library/windowsazure/dn249410.aspx)

### 針對 .NET 開發人員

-   [如何使用 .NET 的 Blob 儲存體](../storage-dotnet-how-to-use-blobs-20/)
-   [如何使用 .NET 的資料表儲存體](../storage-dotnet-how-to-use-tables-20/)
-   [如何使用 .NET 的佇列儲存體](../storage-dotnet-how-to-use-queues-20/)

### 針對 Java 開發人員

-   [如何使用 Java 的 Blob 儲存體](../storage-java-how-to-use-blob-storage/)
-   [如何使用 Java 的資料表儲存體](..storage-java-how-to-use-table-storage/)
-   [如何使用 Java 的佇列儲存體](..storage-java-how-to-use-queue-storage/)

### 針對 Node.js 開發人員

-   [如何使用 Node.js 的 Blob 儲存體](../storage-nodejs-how-to-use-blob-storage/)
-   [如何使用 Node.js 的資料表儲存體](../storage-nodejs-how-to-use-table-storage/)
-   [如何使用 Node.js 的佇列儲存體](../storage-nodejs-how-to-use-queue-storage/)

### 針對 PHP 開發人員

-   [如何使用 PHP 的 Blob 儲存體](../storage-php-how-to-use-blob-storage/)
-   [如何使用 PHP 的資料表儲存體](..storage-php-how-to-use-table-storage/)
-   [如何使用 PHP 的佇列儲存體](..storage-php-how-to-use-queue-storage/)

### 針對 Ruby 開發人員

-   [如何使用 Ruby 的 Blob 儲存體](../storage-ruby-how-to-use-blob-storage/)
-   [如何使用 Ruby 的資料表儲存體](..storage-ruby-how-to-use-table-storage/)
-   [如何使用 Ruby 的佇列儲存體](..storage-ruby-how-to-use-queue-storage/)

### 針對 Python 開發人員

-   [如何使用 Python 的 Blob 儲存體](../storage-python-how-to-use-blob-storage/)
-   [如何使用 Python 的資料表儲存體](..storage-python-how-to-use-table-storage/)
-   [如何使用 Python 的佇列儲存體](..storage-python-how-to-use-queue-storage/)

