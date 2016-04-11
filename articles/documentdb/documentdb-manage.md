<properties
	pageTitle="DocumentDB 儲存體和效能 | Microsoft Azure" 
	description="了解 DocumentDB 中的資料儲存體和文件儲存體，以及如何調整 DocumentDB 以符合應用程式的容量需求。" 
	keywords="文件儲存體"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="mimig"/>

# 了解儲存體和 DocumentDB 中佈建的可預測效能
DocumentDB 是適用於 JSON 文件且受到完整管理、可調整的文件導向 NoSQL 資料庫服務。有了 DocumentDB，您就不需要租用虛擬機器、部署軟體、監視資料庫或擔心災害復原。Microsoft 工程師會負責操作並持續監視 DocumentDB，提供世界級的可用性、效能和資料保護能力。

若要開始使用 DocumentDB，請先透過 [Azure 入口網站](https://portal.azure.com/)[建立資料庫帳戶](documentdb-create-account.md)。DocumentDB 是以固態硬碟 (SSD) 型儲存體和輸送量的單位來提供，這些單位是透過在您的資料庫帳戶內建立資料庫集合來佈建的。每個集合具有保留的輸送量。如果您的應用程式輸送量需求變更，您可以藉由為每個集合設定[效能層級](documentdb-performance-levels.md)來動態變更。

當您的應用程式超過一個或多個集合的效能等級時，要求將會依每個集合為基礎降低。這表示某些應用程式的要求可能會成功，而其他的可能受到節流控制。

本文概述可供用來管理容量和規劃資料儲存體的資源和度量。

## 資料庫帳戶和管理資源
身為 Azure 訂閱者，您可以佈建一個或多個 DocumentDB 資料庫帳戶。每個資料庫帳戶都附有系統管理資源配額，包括資料庫、使用者和權限。這些資源受限於[限制和配額](documentdb-limits.md)。如果您需要其他管理資源，請[連絡支援服務](documentdb-increase-limits.md)。

Azure 入口網站提供了用來監視資料庫帳戶、資料庫和集合的使用量度量。如需詳細資訊，請參閱[監視 DocumentDB 帳戶](documentdb-monitor-accounts.md)。

## 資料庫
單一 DocumentDB 資料庫可以包含幾乎不限數量的文件儲存體 (依集合群組)。集合提供效能隔離 - 每個集合可以使用不與相同資料庫或帳戶中的其他集合共用的輸送量進行佈建。DocumentDB 資料庫大小具有彈性，範圍從 GB 到 TB 的 SSD 型文件儲存體和佈建的輸送量。不同於傳統 RDBMS 資料庫，DocumentDB 中的資料庫不侷限在單一機器內，可以橫跨多個機器或叢集。

利用 DocumentDB，您可以隨著需求調整應用程式，建立更多的集合及/或資料庫。

## 資料庫集合
每個 DocumentDB 資料庫都可以包含一個或多個集合。集合做為文件儲存和處理之高度可用的資料分割。每個集合可以儲存具有異質結構描述的文件。DocumentDB 的自動索引和查詢功能可讓您輕鬆地篩選並擷取文件。集合可為文件儲存體和查詢執行提供適用範圍。集合也是其內含之所有文件的交易網域。集合的輸送量配置是根據其指定的效能層級。可以透過 Azure 入口網站或其中一個 SDK 來動態調整。

集合會由 DocumentDB 自動分割成一或多個實體伺服器。當您建立集合時，您可以指定以每秒的要求單位和分割索引鍵屬性佈建的輸送量。DocumentDB 將會使用此屬性的值將文件散佈到分割並路由要求 (例如查詢)。分割索引鍵值也做為預存程序和觸發程序的交易界限。每個集合都有該集合特定的保留輸送量，且不會與相同帳戶中的其他集合共用。因此，您可以相應放大您的應用程式的儲存體和輸送量。

可以透過 [Azure 入口網站](https://portal.azure.com/)或其中任何一個 [DocumentDB SDK](documentdb-sdk-dotnet.md) 來建立集合。
 
## 要求單位和資料庫作業

當您建立集合時，您可以每秒要求單位 (RU) 保留輸送量。您不需要考慮和管理硬體資源，您可以將**要求單位**當做執行各種資料庫作業以及服務應用程式要求所需的資源的單一計量。讀取 1KB 文件使用相同的 1 RU，無論集合中儲存的項目數，或同時執行的並行要求數目。針對 DocumentDB 的所有要求，包括複雜的作業 (例如 SQL 查詢)，具有可以在開發期間決定的可預測 RU 值。如果您知道您的文件的大小和針對您的應用程式支援的每個作業 (讀取、寫入和查詢) 的頻率，您可以佈建準確數量的輸送量，以符合您的應用程式需求，並且在您的效能需求變更時相應增加或減少。

每個集合可以保留每秒 100 個要求單位區塊的輸送量，從每秒數百到數百萬個要求。佈建的輸送量可以在集合存留期期間進行調整，以適應多變的處理需求和應用程式的存取模式。如需詳細資訊，請參閱 [DocumentDB 效能層級](documentdb-performance-levels.md)。

>[AZURE.IMPORTANT] 集合是可計費的實體。成本取決於以每秒要求單位計量的集合的輸送量，以及已使用儲存體總計 (以 gb 為單位)。

特定的作業，例如插入、刪除、查詢或預存程序執行會耗用的要求單位是多少？ 要求單位是要求處理成本的標準化量值。讀取 1KB 的文件是 1 RU，但是插入、取代或刪除相同文件的要求會耗用服務的更多處理，因此耗用更多的要求單位。服務的每個回應都會包括自訂標頭 (`x-ms-request-charge`)，報告測量要求所耗用的要求單位。此標頭也可以透過 [SDK](documentdb-sdk-dotnet.md) 進行存取。在 .NET SDK 中，RequestCharge 是 ResourceResponse 物件的屬性。

>[AZURE.NOTE] 1KB 文件 1 個要求單位的基準，對應至具有工作階段一致性的文件的簡單 GET。

有數個因素會影響針對 DocumentDB 資料庫帳戶作業所耗用的要求單位。這些因素包括：

- 文件大小。文件大小增加時，也會增加讀取或寫入資料所耗用的單位。
- 屬性計數。假設預設會編製所有屬性的索引，撰寫文件所耗用的單位將會隨著屬性計數增加而增加。
- 資料一致性。當使用強式或繫結失效的資料一致性層級時，將會耗用額外單位來讀取文件。
- 索引的屬性。每個集合上的索引原則會判定預設要編製哪些索引的屬性。您可以限制索引的屬性數目，來減少要求單位耗用量。 
- 編製文件索引。依預設會自動編製每份文件的索引，如果您選擇不要編製一些文件的索引，則會耗用較少的要求單位。

如需詳細資訊，請參閱 [DocumentDB 要求單位](documentdb-request-units.md)。

例如，下表顯示要在三個不同的文件大小 (1KB、4KB 和 64KB)，以及兩個不同效能等級 (500 讀取/秒 + 100 寫入/秒和 500 讀取/秒 + 500 寫入/秒) 中佈建多少要求單位。在工作階段中設定資料一致性，且索引編製原則已設定為 [無]。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>文件大小</strong></p></td>
            <td valign="top"><p><strong>讀取/秒</strong></p></td>
            <td valign="top"><p><strong>寫入/秒</strong></p></td>
            <td valign="top"><p><strong>要求單位</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1,000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 5) + (100 * 5) = 3,000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1.3) + (100 * 7) = 1,350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1.3) + (500 * 7) = 4,150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9,800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29,000 RU/s</p></td>
        </tr>
    </tbody>
</table>

查詢、預存程序和觸發程序會根據所執行作業的複雜度來耗用單位。開發應用程式時，請檢查要求費用標頭，進一步了解每項作業如何耗用要求單位容量。


## 一致性層級和輸送量的選擇
預設一致性層級的選擇會影響輸送量和延遲。您可以用程式設計方式或透過 Azure 入口網站來設定預設的一致性層級。您也可以覆寫個別要求的一致性層級。依預設，一致性層級是一種工作階段，可提供單純的讀取/寫入以及讀取您的寫入的保證。工作階段一致性對於以使用者為中心的應用程式非常有用，並且可以讓您在取捨一致性與效能時找到最佳平衡點。

如需在 Azure 入口網站上變更一致性層級的指示，請參閱[如何管理 DocumentDB 帳戶](documentdb-manage-account.md#consistency)。如需一致性層級的詳細資訊，請參閱[使用一致性層級](documentdb-consistency-levels.md)。

## 佈建的文件儲存體和索引額外負荷
DocumentDB 支援建立單一分割和分割的集合。DocumentDB 中的每個分割支援最多 10 GB 的 SSD 型儲存。10GB 的文件儲存體包含文件加上用於索引的儲存體。預設會將 DocumentDB 集合設定為自動將所有文件編製索引，而不明確要求任何次要索引或結構描述。根據使用 DocumentDB 的應用程式，一般索引額外負荷介於 2-20% 之間。DocumentDB 所使用的索引技術可確保無論屬性值為何，索引額外負荷都不會超過使用預設值之文件大小的 80% 以上。

依預設，DocumentDB 會自動將所有文件編製索引。不過，如果您想要微調索引額外負荷，您可以選擇排除特定文件，不要在插入或取代文件時對這些文件編製索引，如 [DocumentDB 編製索引原則](documentdb-indexing-policies.md)所述。您可以設定 DocumentDB 集合，排除對集合中的所有文件編製索引。您也可以設定 DocumentDB 集合，選擇只將 JSON 文件中具有萬用字元的特定屬性或路徑編製索引，如[設定集合的編製索引原則](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection)所述。排除屬性或文件也可以改善寫入輸送量，也就是說，您會耗用較少的要求單位。
## 後續步驟
如需在 Azure 入口網站上監視效能層級的指示，請參閱[監視 DocumentDB 帳戶](documentdb-monitor-accounts.md)。

如需為集合選擇效能層級的詳細資訊，請參閱 [DocumentDB 中的效能層級](documentdb-performance-levels.md)。
 

<!---HONumber=AcomDC_0330_2016-->