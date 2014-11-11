<properties linkid="storage-introduction" urlDisplayName="Introduction to Azure Storage" pageTitle="Introduction to Storage | Microsoft Azure" metaKeywords="Get started  Azure storage introduction  Azure storage overview  Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage  Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage  Azure file storage  Azure file  Azure file share  Azure " description="An overview of Microsoft Azure Storage." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="Introduction to Microsoft Azure Storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Microsoft Azure 儲存體簡介

本文針對開發人員、IT 專業人員和業務決策者，提供 Microsoft Azure 儲存體簡介。在閱讀本文後，您將學到：

-   何謂 Azure 儲存體，以及可以如何在雲端、行動、伺服器和桌面應用程式中加以充分運用
-   可以在 Azure 儲存體服務中儲存的資料種類：Blob、資料表、佇列和檔案儲存體
-   如何管理 Azure 儲存體的資料存取
-   如何透過備援和複寫來保護 Azure 儲存體資料
-   若要打造第一個 Azure 儲存體應用程式下一步該怎麼做

## 何謂 Azure 儲存體？

雲端運算針對資料需要可擴充、可持久和高可用性儲存體的應用程式，提供了全新案例 - 這也是 Microsoft 開發 Azure 儲存體的真正原因。除了可讓開發人員打造支援全新案例的大規模應用程式之外，Azure 儲存體還針對 Microsoft 虛擬機器提供儲存基礎，並更進一步證明了其穩固性。

Azure 儲存體可大幅擴充，因此您可以儲存並處理數百 TB 的資料，藉此支援科學、財務分析和媒體應用程式等所需的巨量資料案例。或者您可以儲存小型企業網站所需的少量資料。無論您的需求屬於何者，只需要為您儲存的資料付費。Azure 儲存體目前儲存了數十兆的獨特客戶物件，且平均每秒處理上百萬個要求。

Azure 儲存體非常有彈性，因此您可以為大量的全球使用者設計應用程式，然後視需要擴充這些應用程式 - 都是根據儲存的資料量和要求資料的次數。您只需在使用時，針對所使用資料進行付費即可。

Azure 儲存體使用自動分割系統，可自動根據流量負載平衡您的資料。這代表隨著應用程式需求不斷成長，Azure 儲存體會自動配置適當資源以符合需求。

您可從世界各地、任何應用程式類型 (無論是在雲端、桌面、內部部署伺服器、行動或平板裝置上執行) 存取 Azure 儲存體。您可以在行動案例中使用 Azure 儲存體，案例中的應用程式會在裝置上儲存資料子集，然後將它與儲存在雲端中的整組資料同步處理。

為了方便開發，Azure 儲存體支援使用各種作業系統 (包括 Windows 和 Linux) 和多種程式設計語言 (包括 .NET、Java 和 C++) 的用戶端。Azure 儲存體還可透過簡單 REST API 公開資料資源，REST API 適用於任何能夠透過 HTTP/HTTPS 傳送與接收資料的用戶端。

## Azure 儲存體服務簡介

Azure 儲存體服務包括 Blob 儲存體、資料表儲存體、佇列儲存體和檔案儲存檔：

-   **Blob 儲存體**可儲存檔案資料。Blob 可以是任何類型的文字或二進位資料，例如文件、媒體檔案或應用程式安裝程式。
-   **資料表儲存體**可儲存結構化的資料集。資料表儲存體屬於 NoSQL 索引鍵屬性資料儲存，可允許快速開發和迅速存取大量資料。
-   **佇列儲存體**針對工作流程處理及雲端服務元件間的通訊，提供可靠的訊息服務。
-   **檔案儲存體**為使用標準 SMB 2.1 通訊協定的舊版應用程式提供共用儲存體。Azure 虛擬機器和雲端服務可以透過掛接的共用，在應用程式元件之間共用檔案資料，而內部部署應用程式可以透過檔案服務 REST API，存取共用中的檔案資料。

Blob、資料表和佇列儲存體包含在每一個儲存體帳戶中，而檔案儲存體可依要求透過 [Azure 預覽頁面][Azure 預覽頁面]取得。

儲存體帳戶是指可讓您存取 Azure 儲存體的唯一命名空間。每個儲存體帳戶都可包含多達 500 TB 的 Blob、佇列、資料表和檔案資料的組合。如需 Azure 儲存體帳戶容量的詳細資訊，請參閱＜[Azure 儲存體延展性和效能目標][Azure 儲存體延展性和效能目標]＞。

下圖顯示 Azure 儲存體資源之間的關係：

![Azure 儲存體資源][Azure 儲存體資源]

在建立儲存體帳戶之前，您必須擁有 Azure 訂用帳戶，訂用帳戶是指可讓您存取各種 Azure 服務的方案。單一訂用帳戶可讓您建立最多 20 個唯一命名的儲存體帳戶。

您可以利用[免費試用版][免費試用版]來開始使用 Azure。在決定購買方案之後，您便可以選擇各種[購買選項][購買選項]。如果您是 [MSDN 訂閱者][MSDN 訂閱者]，您將取得可在 Azure 服務 (包括 Azure 儲存體) 中使用的每月免費額度。

## Blob 儲存體

若是擁有大量非結構化資料要儲存於雲端的使用者，Blob 儲存體提供具有成本效益且可擴充的解決方案。您可以使用 Blob 儲存體來儲存如下所示的內容：

-   文件
-   社交資料 (例如照片、視訊、音樂和部落格)
-   檔案、電腦、資料庫和裝置的備份
-   Web 應用程式的影像和文字
-   雲端應用程式的組態資料
-   巨量資料 (例如記錄和其他大型資料集)

每個 Blob 會組織成一個容器。容器也提供對物件群組指派安全原則的實用方式。儲存體帳戶可包含任意數目的容器，而容器可包含任意數目的 Blob，儲存體帳戶的容量限制高達 500 TB。

Blob 儲存體提供兩種 Blob 類型：區塊 Blob 和頁面 Blob (磁碟)。區塊 Blob 已針對串流和儲存雲端物件進行最佳化，是儲存文件、媒體檔案、備份等的不錯選擇。區塊 Blob 的大小可高達 200 GB。頁面 Blob 已針對顯示 IaaS 磁碟與支援隨機寫入進行最佳化，且大小可能可以高達 1 TB。Azure 虛擬機器網路附加 IaaS 磁碟是以頁面 Blob 方式儲存的 VHD。

若是超大型資料集，網路限制會使得透過線路上傳或下載資料至 Blob 儲存體變得不切實際，您可以將硬碟送至 Microsoft，以便使用 [Azure 匯入/匯出服務][Azure 匯入/匯出服務]直接從資料中心匯入或匯出資料。您也可以複製儲存體帳戶或跨不同儲存體帳戶內的 Blob 資料。

## 資料表儲存體

新式應用程式通常會要求以超越前幾代軟體需求的延伸性和彈性儲存資料。資料表儲存體提供高可用性且可大幅擴充的儲存體，方便您的應用程式自動擴充以滿足使用者需求。資料表儲存體屬於 Microsoft 的 NoSQL 索引鍵屬性儲存，它的無結構描述設計讓它有別於傳統的關聯式資料庫。透過無結構描述資料儲存，便可輕易隨著應用程式發展需求改寫資料。資料表儲存體非常容易使用，方便開發人員可以快速建立應用程式。所有類型的應用程式都可以用快速且具成本效益的方式存取資料。相較於類似資料量的傳統 SQL，資料表儲存體通常可大幅降低成本。

資料表儲存體屬於索引鍵屬性儲存，代表資料表中的每個值會與具型別的屬性名稱一起儲存。屬性名稱可用來篩選與指定選取條件。一組屬性及其值就構成一個實體。因為資料表儲存體沒有結構描述，因此相同資料表中的兩個實體可包含不同屬性集合，且這些屬性可以是不同類型。

您可以使用資料表儲存體來儲存具彈性的資料集，例如 Web 應用程式的使用者資料、通訊錄、裝置資訊，以及服務所需的任何其他中繼資料類型。您可以在資料表中儲存任意數目的實體，且儲存體帳戶可能包含任意數目的資料表，儲存體帳戶的容量限制高達 200 TB。

如同 Blob 和佇列，開發人員可以使用標準 REST 通訊協定來管理與存取資料表儲存體，不過，資料表儲存體也支援 OData 通訊協定的子集、簡化進階查詢功能，及啟用 JSON 和 AtomPub (以 XML 為基礎) 格式。

在現今的網際網路架構應用程式中，NoSQL 資料庫 (如資料表儲存體) 提供傳統關聯式資料庫的熱門替代方式。

## 佇列儲存體

設計擴充性的應用程式時，會經常分離應用程式元件，以便進行個別擴充。佇列儲存體針對應用程式元件間的非同步通訊，提供可靠的訊息服務解決方案，無論應用程式元件是在雲端、桌面、內部部署伺服器或行動裝置上執行。佇列儲存體也支援管理非同步工作並建置處理工作流程。

儲存體帳戶可包含任意數目的佇列。佇列可包含任意數目的訊息，儲存體帳戶的容量限制高達 200 TB。個別訊息的大小可能高達 64 KB。

## 檔案儲存體

許多舊版應用程式都仰賴檔案共用，形成複雜的相依性而難以將這些應用程式移轉至雲端。檔案儲存體提供雲端架構的檔案共用，可讓您快速地將舊式應用程式移轉至 Azure，而不必浪費成本來重新撰寫程式。

在 Azure 虛擬機器或雲端服務中執行的應用程式，可掛接檔案儲存體共用來存取檔案資枓，就像桌面應用程式掛接一般 SMB 共用一樣。可同時掛接和存取檔案儲存體共用的應用程式元件數量沒有限制。

因為檔案儲存體是標準 SMB 2.1 檔案共用，所以 Azure 中執行的應用程式可透過檔案系統 I/O API 來存取共用中的資料。因此，開發人員可利用現有的程式碼和技能來移轉現有的應用程式。IT 專業人員在管理 Azure 應用程式時，可以使用 PowerShell Cmdlet 來建立、掛接和管理檔案儲存體共用。

就像其他 Azure 儲存體服務一樣，檔案儲存體也公開 REST API 來存取共用中的資料。內部部署應用程式可以呼叫檔案儲存體 REST API 來存取檔案共用中的資料。因此，企業可以選擇將某些舊式應用程式移轉至 Azure，並繼續在其自己的組織內執行其他舊式應用程式。請注意，只有 Azure 中執行的應用程式才能掛接檔案共用。內部部署應用程式只能透過 REST API 來存取檔案共用。

分散式應用程式也可以利用檔案儲存體，以儲存並共用實用的應用程式資料及開發和測試工具。例如，應用程式可以在檔案儲存體共用中儲存組態檔和診斷資料，像是記錄、度量和損毀傾印，供多個虛擬機器或角色使用。開發人員和管理員可以在所有元件可用的檔案儲存體共用中，儲存他們建置或管理應用程式所需的公用程式，而不需要將公用程式安裝在每個虛擬機器或角色執行個體上。

## 存取 Blob、資料表、佇列和檔案資源

依預設，只有儲存體帳戶擁有者可以存取儲存體帳戶內的資源。為了資料的安全性，針對帳戶內資源所做的每個要求都必須經過驗證。驗證會仰賴共用金鑰模型。Blob 也可設定為支援匿名驗證。

建立儲存體帳戶時所指派的兩組存取金鑰可用於驗證。當您定期重新產生金鑰作為一般安全性金鑰管理作法時，擁有兩組金鑰可確保您的應用程式保持為可用狀態。

如果您確實需要允許使用者對儲存體資源進行控管存取，則您可以建立[共用存取簽章][共用存取簽章]。共用存取簽章是指可附加在 URL 後面的權杖，可提供容器、Blob、資料表或佇列的委派存取。在權杖有效的期限內，擁有權杖的任何人都可以存取它在指定權限中所指向的資源。請注意，Azure 檔案儲存體目前不支援共用存取簽章。

最後，您可以指定容器及其 Blob 或特定的 Blob 是否可供公用存取。當您將容器或 Blob 指定為公用時，任何人都可以進行匿名讀取；不需要驗證。公用容器和 Blob 對於公開資源 (例如網站上所託管的媒體和文件) 而言非常有用。若要縮短全球使用者的網路延遲，您可以使用 Azure CDN 來快取網站所用的 Blob 資料。

## 持久性和高可用性的複寫

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

## 定價

客戶會根據四個因素來支付 Azure 儲存體的費用：使用的儲存體容量、選取的複寫選項、針對服務所做的要求次數，和出口流量。

儲存體容量是指您用於儲存資料的儲存體帳戶配額。若只是儲存資料，則成本是由您所儲存的資料量和複寫資料的方式來決定。對 Azure 儲存體進行的每個讀取和寫入操作也會對服務提出要求。出口流量是指傳出 Windows Azure 地區的資料。當您儲存體帳戶中的資料受不同地區中執行的應用程式存取時，不論該應用程式是雲端服務還是其他某類應用程式，您都要負擔出口流量的費用。(若為 Windows Azure 服務，您可以採取步驟，將資料和服務群組在相同的資料中心，以減少或消除處理和出口流量費用。)

[儲存體定價詳細資料][儲存體定價詳細資料]頁面提供了儲存體容量、複寫和交易的詳細定價資料。[資料傳輸定價詳細資料][資料傳輸定價詳細資料]則提供了出口流量的詳細定價資訊。您可以使用 [Azure 儲存體定價計算機][Azure 儲存體定價計算機]，以協助消除成本。

## 針對儲存體進行開發

Azure 儲存體可透過 [REST API][REST API] 公開儲存體資源，任何可提出 HTTP/HTTPS 要求的語言都可呼叫 REST API。另外，Azure 儲存體還提供了幾種熱門語言的程式設計程式庫。這些程式庫可透過處理詳細資料 (例如同步和非同步叫用、進行批次操作、例外狀況管理、自動重試、運作方式等等) 來簡化使用 Azure 儲存體的許多項目。程式庫目前適用於下列語言和平台，以及正在研發的其他語言和平台：

-   [.NET][.NET]
-   [機器碼][機器碼]
-   [Java/Android][Java/Android]
-   [Node.js][Node.js]
-   [PHP][PHP]
-   [Ruby][Ruby]
-   [Python][Python]
-   [PowerShell][PowerShell]

## 後續步驟

若要開始使用 Azure 儲存體，請探索以下資源：

-   [Azure 儲存體文件][Azure 儲存體文件]
-   [Azure 儲存體延展性和效能目標][Azure 儲存體延展性和效能目標]

### 針對 .NET 開發人員

-   [如何使用 .NET 的 Blob 儲存體][如何使用 .NET 的 Blob 儲存體]
-   [如何使用 .NET 的資料表儲存體][如何使用 .NET 的資料表儲存體]
-   [如何使用 .NET 的佇列儲存體][如何使用 .NET 的佇列儲存體]

### 針對 Java/Android 開發人員

-   [如何從 Java/Android 使用 Blob 儲存體][如何從 Java/Android 使用 Blob 儲存體]
-   [如何從 Java/Android 使用資料表儲存體][如何從 Java/Android 使用資料表儲存體]
-   [如何從 Java/Android 使用查詢儲存體][如何從 Java/Android 使用查詢儲存體]

### 針對 Node.js 開發人員

-   [如何使用 Node.js 的 Blob 儲存體][如何使用 Node.js 的 Blob 儲存體]
-   [如何使用 Node.js 的資料表儲存體][如何使用 Node.js 的資料表儲存體]
-   [如何使用 Node.js 的佇列儲存體][如何使用 Node.js 的佇列儲存體]

### 針對 PHP 開發人員

-   [如何使用 PHP 的 Blob 儲存體][如何使用 PHP 的 Blob 儲存體]
-   [如何使用 PHP 的資料表儲存體][如何使用 PHP 的資料表儲存體]
-   [如何使用 PHP 的佇列儲存體][如何使用 PHP 的佇列儲存體]

### 針對 Ruby 開發人員

-   [如何使用 Ruby 的 Blob 儲存體][如何使用 Ruby 的 Blob 儲存體]
-   [如何使用 Ruby 的資料表儲存體][如何使用 Ruby 的資料表儲存體]
-   [如何使用 Ruby 的佇列儲存體][如何使用 Ruby 的佇列儲存體]

### 針對 Python 開發人員

-   [如何使用 Python 的 Blob 儲存體][如何使用 Python 的 Blob 儲存體]
-   [如何使用 Python 的資料表儲存體][如何使用 Python 的資料表儲存體]
-   [如何使用 Python 的佇列儲存體][如何使用 Python 的佇列儲存體]

  [Azure 預覽頁面]: /zh-tw/services/preview/
  [Azure 儲存體延展性和效能目標]: http://msdn.microsoft.com/library/windowsazure/dn249410.aspx
  [Azure 儲存體資源]: ./media/storage-introduction/storage-concepts.png
  [免費試用版]: /zh-tw/pricing/free-trial/
  [購買選項]: /zh-tw/pricing/purchase-options/
  [MSDN 訂閱者]: /zh-tw/pricing/member-offers/msdn-benefits-details/
  [Azure 匯入/匯出服務]: http://azure.microsoft.com/zh-tw/documentation/articles/storage-import-export-service/
  [共用存取簽章]: ../storage-dotnet-shared-access-signature-part-1/
  [storage-replication-options]: ../includes/storage-replication-options.md
  [儲存體定價詳細資料]: /zh-tw/pricing/details/storage/
  [資料傳輸定價詳細資料]: /zh-tw/pricing/details/data-transfers/
  [Azure 儲存體定價計算機]: /zh-tw/pricing/calculator/?scenario=data-management
  [REST API]: http://msdn.microsoft.com/library/windowsazure/dd179355.aspx
  [.NET]: http://msdn.microsoft.com/library/dn495001.aspx
  [機器碼]: http://msdn.microsoft.com/library/dn495438.aspx
  [Java/Android]: /zh-tw/develop/java/
  [Node.js]: /zh-tw/develop/nodejs/
  [PHP]: /zh-tw/develop/php/
  [Ruby]: /zh-tw/develop/ruby/
  [Python]: /zh-tw/develop/python/
  [PowerShell]: http://msdn.microsoft.com/library/dn495240.aspx
  [Azure 儲存體文件]: /zh-tw/documentation/services/storage/
  [如何使用 .NET 的 Blob 儲存體]: ../storage-dotnet-how-to-use-blobs/
  [如何使用 .NET 的資料表儲存體]: ../storage-dotnet-how-to-use-tables/
  [如何使用 .NET 的佇列儲存體]: ../storage-dotnet-how-to-use-queues/
  [如何從 Java/Android 使用 Blob 儲存體]: ../storage-java-how-to-use-blob-storage/
  [如何從 Java/Android 使用資料表儲存體]: ../storage-java-how-to-use-table-storage/
  [如何從 Java/Android 使用查詢儲存體]: ../storage-java-how-to-use-queue-storage/
  [如何使用 Node.js 的 Blob 儲存體]: ../storage-nodejs-how-to-use-blob-storage/
  [如何使用 Node.js 的資料表儲存體]: ../storage-nodejs-how-to-use-table-storage/
  [如何使用 Node.js 的佇列儲存體]: ../storage-nodejs-how-to-use-queues/
  [如何使用 PHP 的 Blob 儲存體]: ../storage-php-how-to-use-blobs/
  [如何使用 PHP 的資料表儲存體]: ../storage-php-how-to-use-table-storage/
  [如何使用 PHP 的佇列儲存體]: ../storage-php-how-to-use-queues/
  [如何使用 Ruby 的 Blob 儲存體]: ../storage-ruby-how-to-use-blob-storage/
  [如何使用 Ruby 的資料表儲存體]: ../storage-ruby-how-to-use-table-storage/
  [如何使用 Ruby 的佇列儲存體]: ../storage-ruby-how-to-use-queue-storage/
  [如何使用 Python 的 Blob 儲存體]: ../storage-python-how-to-use-blob-storage/
  [如何使用 Python 的資料表儲存體]: ../storage-python-how-to-use-table-storage/
  [如何使用 Python 的佇列儲存體]: ../storage-python-how-to-use-queue-storage/
