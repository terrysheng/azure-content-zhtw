<properties 
	pageTitle="管理 DocumentDB 容量 | Microsoft Azure" 
	description="了解如何調整 DocumentDB，以符合應用程式的容量需求。" 
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
	ms.date="10/28/2015" 
	ms.author="mimig"/>

# 管理 DocumentDB 容量需求
DocumentDB 是受到完整管理、可調整的文件導向 NoSQL 資料庫服務。有了 DocumentDB，您就不需要租用虛擬機器、部署軟體、監視資料庫或擔心災害復原。Microsoft 工程師會負責操作並持續監視 DocumentDB，提供世界級的可用性、效能和資料保護能力。

若要開始使用 DocumentDB，請先透過 [Azure Preview 入口網站](https://portal.azure.com/)[建立資料庫帳戶](documentdb-create-account.md)。DocumentDB 是以固態硬碟 (SSD) 型儲存體和輸送量的單位來提供，這些單位是透過在您的資料庫帳戶內建立資料庫集合來佈建的。每個集合包含具有保留的輸送量的 10GB 資料庫儲存體。如果您的應用程式輸送量需求變更，您可以藉由為每個集合設定[效能層級](documentdb-performance-levels.md)來動態變更。

當您的應用程式超過一個或多個集合的效能等級時，要求將會依每個集合為基礎降低。這表示某些應用程式的要求可能會成功，而其他的可能受到節流控制。

## 資料庫帳戶和管理資源
身為 Azure 訂閱者，您可以佈建一個或多個 DocumentDB 資料庫帳戶。每個資料庫帳戶都附有系統管理資源配額，包括資料庫、使用者和權限。這些資源受限於[限制和配額](documentdb-limits.md)。如果您需要其他管理資源，請[連絡支援服務](documentdb-increase-limits.md)。

## 文件儲存體數量沒有限制的資料庫
單一 DocumentDB 資料庫可以包含幾乎不限數量的文件儲存體 (依集合分割)。集合構成其內含文件的交易網域。DocumentDB 資料庫大小具有彈性 – 範圍從幾 GB 到可能數 TB 的 SSD 型文件儲存體和佈建的輸送量。不同於傳統 RDBMS 資料庫，DocumentDB 中的資料庫不侷限在單一機器內。

利用 DocumentDB，您可以隨著需求調整應用程式，建立更多的集合及/或資料庫。Microsoft 中的各種第一方應用程式都是依消費者規模來使用 DocumentDB，建立極大的 DocumentDB 資料庫，每個資料庫各包含數千個具有好幾 TB 文件儲存體的集合。您可以新增或移除集合來擴充或縮減資料庫，以配合您的應用程式規模需求。

## 資料庫集合
每個 DocumentDB 資料庫都可以包含一個或多個集合。集合做為文件儲存和處理之高度可用的資料分割。每個集合可以儲存具有異質結構描述的文件。DocumentDB 的自動索引和查詢功能可讓您輕鬆地篩選並擷取文件。集合可為文件儲存體和查詢執行提供適用範圍。集合也是其內含之所有文件的交易網域。集合的輸送量配置是根據其指定的效能層級。可以透過 Azure Preview 入口網站或其中一個 SDK 來動態調整。

您可以建立任何數目的集合，以符合應用程式的儲存體和輸送量規模需求。可以透過 [Azure Preview 入口網站](https://portal.azure.com/)或其中任何一個 [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 來建立集合。

>[AZURE.NOTE]每個集合支援最多 10 GB 文件資料的儲存體。
 
## 要求單位和資料庫作業
DocumentDB 可供進行許多的資料庫作業，包括使用 UDF、預存程序和觸發程序進行關聯式和階層式查詢，而這些作業全都是對資料庫集合內的文件來進行。與上述各項作業相關聯的處理成本，會因為完成作業所需的 CPU、IO 和記憶體而不同。您不需要考慮和管理硬體資源，您可以將要求單位 (RU) 想成是執行各種資料庫作業以及服務應用程式要求時所需的資源數量。

要求單位是根據針對集合設定的效能層級進行佈建和指派。每個集合都會在建立時指定效能層級。此效能層級會決定集合對於要求單位的每秒處理容量。效能層級可以在集合存留期期間進行調整，以適應多變的處理需求和應用程式的存取模式。如需詳細資訊，請參閱 [DocumentDB 效能層級](documentdb-performance-levels.md)。

>[AZURE.IMPORTANT]集合是可計費的實體。成本取決於指派給集合的效能層級。

要求單位消耗量是以每秒的速率來計算。對於超過集合上佈建的要求單位速率的應用程式，對於該集合的要求會受到節流控制，直到該速率降到預留層級以下。如果您的應用程式需要較高等級的輸送量，您可以增加現有集合的效能層級，或者將應用程式要求散佈到新的集合。

要求單位是要求處理成本的標準化量值。單一要求單位代表讀取 (透過自我連結) 由 10 個唯一屬性值所組成的單一 1KB JSON 文件所需的處理容量。要求單位費用會假設一致性層級設定為預設「工作階段」，並且自動編製所有文件的索引。插入、取代或刪除相同文件的要求會耗用服務的更多處理，因此耗用更多的要求單位。服務的每個回應都會包括自訂標頭 (x-ms-request-charge)，以測量要求所耗用的要求單位。此標頭也可以透過 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 進行存取。在 .NET SDK 中，RequestCharge 是 ResourceResponse 物件的屬性。

>[AZURE.NOTE]1KB 文件 1 個要求單位的基準，對應至文件自我連結的簡單 GET。

有數個因素會影響針對 DocumentDB 資料庫帳戶作業所耗用的要求單位。這些因素包括：

- 文件大小。文件大小增加時，也會增加讀取或寫入資料所耗用的單位。
- 屬性計數。假設預設會編製所有屬性的索引，撰寫文件所耗用的單位將會隨著屬性計數增加而增加。
- 資料一致性。當使用強式或繫結失效的資料一致性層級時，將會耗用額外單位來讀取文件。
- 索引的屬性。每個集合上的索引原則會判定預設要編製哪些索引的屬性。您可以限制索引的屬性數目，來減少要求單位耗用量。 
- 編製文件索引。依預設會自動編製每份文件的索引，如果您選擇不要編製一些文件的索引，則會耗用較少的要求單位。

查詢、預存程序和觸發程序會根據所執行作業的複雜度來耗用單位。開發應用程式時，請檢查要求費用標頭，進一步了解每項作業如何耗用要求單位容量。

## 一致性層級和輸送量的選擇
預設一致性層級的選擇會影響輸送量和延遲。您可以用程式設計方式或透過 Azure Preview 入口網站來設定預設的一致性層級。您也可以覆寫個別要求的一致性層級。依預設，一致性層級是一種工作階段，可提供單純的讀取/寫入以及讀取您的寫入的保證。工作階段一致性對於以使用者為中心的應用程式非常有用，並且可以讓您在取捨一致性與效能時找到最佳平衡點。

如需在 Azure Preview 入口網站上變更一致性層級的指示，請參閱[如何管理 DocumentDB 帳戶](documentdb-manage-account.md#consistency)。如需一致性層級的詳細資訊，請參閱[使用一致性層級](documentdb-consistency-levels.md)。

## 佈建的文件儲存體和索引額外負荷
每建立一個集合，您就會佈建 10GB 的 SSD 型文件儲存體。10GB 的文件儲存體包含文件加上用於索引的儲存體。預設會將 DocumentDB 集合設定為自動將所有文件編製索引，而不明確要求任何次要索引或結構描述。根據使用 DocumentDB 的應用程式，一般索引額外負荷介於 2-20% 之間。DocumentDB 所使用的索引技術可確保無論屬性值為何，索引額外負荷都不會超過使用預設值之文件大小的 80% 以上。

依預設，DocumentDB 會自動將所有文件編製索引。不過，如果您想要微調索引額外負荷，您可以選擇排除特定文件，不要在插入或取代文件時對這些文件編製索引，如 [DocumentDB 編製索引原則](documentdb-indexing-policies.md)所述。您可以設定 DocumentDB 集合，排除對集合中的所有文件編製索引。您也可以設定 DocumentDB 集合，選擇只將 JSON 文件中具有萬用字元的特定屬性或路徑編製索引，如[設定集合的編製索引原則](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection)所述。排除屬性或文件也可以改善寫入輸送量，也就是說，您會耗用較少的要求單位。
 
## 後續步驟
如需在 Azure Preview 入口網站上監視效能層級的指示，請參閱[監視 DocumentDB 帳戶](documentdb-monitor-accounts.md)。

如需為集合選擇效能層級的詳細資訊，請參閱 [DocumentDB 中的效能層級](documentdb-performance-levels)。
 

<!---HONumber=Nov15_HO1-->