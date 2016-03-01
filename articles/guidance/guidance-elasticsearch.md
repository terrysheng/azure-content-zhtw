<properties
   pageTitle="在 Azure 上執行 Elasticsearch | Microsoft Azure"
   description="如何在 Azure 上安裝、 設定和執行 Elasticsearch。"
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="mabsimms"/>

# 在 Azure 上執行 Elasticsearch

本文章是[系列文章的一部分](guidance-elasticsearch-introduction.md)。

## 概觀

Elasticsearch 是可高度調整的開放原始碼搜尋引擎和資料庫。它很適合於需要快速分析和探索大型資料集所保存資訊的情況。常見案例包括：

- 大規模的任意文字搜尋，可以快速尋找和擷取符合搜尋詞彙組合的文件。
- 事件記錄，其中的資訊可以來自各種來源。資料可能需要加以分析，以查明一連串的事件如何導致特定結論。
- 儲存從遠端裝置和其他來源擷取的資料。資料可以包含各種資訊，但常見的需求是以一系列的儀表板來呈現此資訊，讓操作員能夠了解整體系統狀態。應用程式也可以使用此資訊，快速做出有關資料流程以及因而需要執行之商務作業的決策。
- 庫存控制，以在商品售出時記錄存貨的變更。商務系統可以使用此資訊向使用者報告庫存水準，而且在產品存量不足時重新訂購庫存。分析師可以檢查資料的趨勢，協助判斷在何種情況下哪些是暢銷產品。
- 財務分析，其中的市場資訊幾乎即時送達。可以產生儀表板，以指示各種財務工具的最新效能，而後可用來協助進行買賣決策。

本文件提供 Elasticsearch 的一般結構簡介，接著說明如何使用 Azure 實作 Elasticsearch 叢集。本文件著重於部署 Elasticsearch 叢集，專注於各種功能的效能和管理需求的系統，並考量您的需求如何應該磁碟機的組態和您選取的拓樸的最佳作法。

> [AZURE.NOTE] 本指導方針假設您基本上有點熟悉 [Elasticsearch][]。

## Elasticsearch 的結構

Elasticsearch 是為了做為搜尋引擎而高度最佳化的文件資料庫。文件會以 JSON 格式序列化。資料會保存在索引中 (使用 [Apache Lucene][] 來實作)，然而詳細資料是從檢視抽取而來，不需要完全了解 Lucene 即可使用 Elasticsearch。

### 叢集、節點、索引和分區

Elasticsearch 會實作一個叢集化架構，該架構使用分區化將資料分散於多個節點，並進行複寫以提供高可用性。

文件會儲存在索引中。使用者可以指定文件中的哪些欄位用於唯一識別索引中的各文件，或系統可以自動產生索引鍵欄位和值。索引用於實際組織文件，並做為尋找文件的主要方法。此外，Elasticsearch 會自動建立一組額外的結構，做為其餘欄位的反向索引，以便能夠在集合中快速查閱和執行分析。

索引包含一組分區。使用以索引鍵值和索引中的分區數目為基礎的雜湊機制，將文件平均散佈於分區；一旦將文件配置到分區，除非其索引鍵已變更，否則它不會從該分區移動。Elasticsearch 會將分區分散於叢集中的所有可用資料節點；單一節點一開始可保存屬於相同索引的一或多個分區，但新節點新增到叢集時，Elasticsearch 會重新放置分區，以確保系統的負載平衡。移除節點時，適用相同的重新平衡作業。

可以複寫索引。在此情況下，會複製索引中的每個分區。Elasticsearch 會確保索引的每個原始分區 (稱為「主要分區」) 與其複本永遠位於不同的節點上。

> [AZURE.NOTE] 建立索引之後，就無法輕易變更索引中的分區數目，但可以新增複本。

新增或修改文件時，所有寫入作業會先在主要分區上執行，然後於每個複本執行。預設會同時執行此程序，以確保一致性。Elasticsearch 會在寫入資料時，使用開放式並行存取搭配版本控制。使用主要分區或其任何複本可以履行讀取作業。

圖 1 顯示 Elasticsearch 叢集的重要層面，其中包含三個節點。已建立包含兩個主要分區的索引，其中每個分區具有兩個複本 (總共有六個分區)。

![](media/guidance-elasticsearch-general-cluster1.png)

**圖 1：**
包含兩個主要節點和兩組複本的簡易 Elasticsearch 叢集

在此叢集中，主要分區 1 和主要分區 2 位於不同的節點，以便平衡它們之間的負載。複本會以同樣的方式分散。如果單一節點失敗，其餘節點會有足夠的資訊讓系統繼續運作。如果無法使用對應的主要分區，Elasticsearch 會在必要時將複本分區升級成為主要分區。
節點在開始執行時，可以起始新的叢集 (如果是叢集中的第一個節點)，或加入現有的叢集。elasticsearch.yml 檔案中的 *cluster.name* 設定會決定節點所屬的叢集。

### 節點角色

Elasticsearch 叢集中的節點可以執行下列角色：

- 資料節點，此種節點可保存包含索引資料的一或多個分區。
- 用戶端節點，此種節點不會保存索引資料，但可處理用戶端應用程式對適當的資料節點所提出的傳入要求。
- 主要節點，此種節點不會保存索引資料，但可執行叢集管理作業，例如維護和發佈叢集路由資訊 (節點包含哪一個分區的清單)，判斷哪些節點可供使用，因為節點會出現，並且會消失，重新放置分區和協調節點失敗後復原。您可將多個節點設定為主要節點，但實際上只會選擇一個節點來執行主要功能。如果此節點失敗，則會進行另一次選擇，而其他合格的主要節點之一將會獲選並接替之。

根據預設，Elasticsearch 節點會執行上述三個角色 (讓您能在單一電腦上建立完整的工作中叢集，以便進行開發和概念證明)，但是您可以透過 *elasticsearch.yml* 檔案中的 *node.data* 和 *node.master* 設定來變更其作業，如下所示：

```yaml
node.data: true
node.master: false
```

**資料節點的組態**

```yaml
node.data: false
node.master: false
```

**用戶端節點的組態**

```yaml
node.data: false
node.master: true
```

**主要節點的組態**

> [AZURE.NOTE] 選定的主要節點與叢集息息相關。其他節點會定期 Ping 該節點，以確保它仍然可用。如果選定的主要節點也做為資料節點，此節點就有機會變成忙碌中而無法回應這些 Ping。在此情況下，主要節點會被視為失敗，而選擇其他主要節點之一予以取代。如果原始主要節點實際上仍然可用，結果可能是已選定兩個主要節點的叢集，而造成「核心分裂」問題，進而導致資料損毀和其他問題。「設定、測試和分析 Elasticsearch 恢復和復原」文件會說明如何設定叢集，以減少發生這種情況的機會。不過，歸根結底，在中大型叢集中使用不負責管理資料的專用主要節點是個不錯的策略。

叢集中的節點會共用叢集中其他節點的相關資訊 (由 [Gossiping][]) 及其包含的分區。儲存和擷取資料的用戶端應用程式可以連接到叢集中的任何節點，並以透明方式將要求路由傳送到正確的節點。當用戶端應用程式要求叢集中的資料時，先收到要求的節點負責導向作業、與每個相關節點通訊以擷取資料，然後在將結果傳回給用戶端應用程式前加以彙總。使用用戶端節點來處理要求，可讓資料節點免於執行此散佈/收集工作，使其能夠將時間用於提供資料。您可以停用資料節點的 HTTP 傳輸，避免用戶端應用程式不小心與資料節點通訊 (以致成為用戶端節點)：

```yaml
http.enabled: false
```

資料節點仍可使用 Elasticsearch 傳輸模組 (其使用 TCP 通訊端在節點間直接連接)，與相同網路上的其他資料節點、用戶端節點和專用主要節點通訊，但用戶端應用程式只能透過 HTTP 連接到用戶端節點。圖 2 顯示的拓撲包含 Elasticsearch 叢集中混合的專用主要節點、用戶端節點和資料節點。

![](media/guidance-elasticsearch-general-cluster2.png)

**圖 2.**
Elasticsearch 叢集會顯示不同類型的節點

### 使用用戶端節點的成本與優勢

當應用程式將查詢提交至 Elasticsearch 叢集時，應用程式所連接的節點會負責導向查詢程序。此節點會將要求轉寄至每個資料節點並收集結果，並將累積的資訊傳回到應用程式。如果查詢涉及彙總和其他計算，應用程式所連接的節點會在從其他每個節點擷取資料後，執行必要的作業。此散佈/收集程序可能相當耗用處理和記憶體資源。

使用專用的用戶端節點來執行這些工作，可讓資料節點專注於管理和儲存資料。結果是，許多涉及複雜查詢和彙總的案例都可受惠於使用專用的用戶端節點。不過，使用專用用戶端節點的影響可能會隨著您的案例、工作負載和叢集大小而有所不同。例如，使用用戶端節點可能會使資料擷取工作負載的效率降低，因為在儲存資料時需要額外的網路「躍點」。在具有 3 個節點 (6 個分區) 的叢集中，如果系統未設定專用的用戶端節點，使所有環境因素與節點負載相等，則儲存或修改資料的應用程式有 1/3 機率會直接連接到最適當的分區，而不需要執行額外的網路跳接 ( 1/3 機率)。相反地，執行複雜彙總的工作負載可能受惠於使用專用的用戶端，因為單一節點將負責這些作業所執行的每組散佈/收集作業。在混合環境中，您應該已準備好執行效能測試，以評估在您特定的工作負載上使用用戶端節點的影響。

> [AZURE.NOTE] 「在 Azure 上使用 Elasticsearch 最大化資料彙總和查詢效能」文件摘要說明 Microsoft 模式和作法開發小組所執行的一組效能評定 (部分是基於此目的)。

### 連接到叢集

Elasticsearch 會公開一系列的 REST API，以便建置用戶端應用程式並將要求傳送至叢集。如果您使用 .NET Framework 開發應用程式，則有兩個更高層級的 API 可供使用 – [Elasticsearch.Net 和 NEST][]。

如果您使用 Java 建置用戶端應用程式，您可以使用[節點用戶端 API][] 來動態建立用戶端節點，並將其新增到叢集。如果您的系統使用相對較少的長時間執行的連線，動態建立用戶端節點就很方便。主要節點將叢集路由對應 (哪些節點包含哪些分區的詳細資料) 提供給使用節點 API 所建立的用戶端節點；此資訊可讓 Java 應用程式在檢索或查詢資料時直接連接到適當的節點，進而減少在使用其他 API 時可能需要的躍點數目。這種方法的成本就是在叢集中註冊用戶端節點的額外負荷。如果大量用戶端節點快速地出現又消失，則維護與散發叢集路由對應的影響可能會變得很重要。

**連接負載平衡**

Elasticsearch 提供數個可實作連接負載平衡的機制。下列清單是一些常見的方法：

**用戶端型負載平衡**：如果您使用 Elasticsearch.Net 或 NEST API 建置用戶端應用程式，您可以使用連接集區將連接要求循環配置到節點，協助平衡要求的負載，而不需使用外部負載平衡器。以下程式碼片段示範如何建立以三個節點的位址所設定的 *ElasticsearchClient* 物件。來自用戶端應用程式的要求將會分配於這些節點：

```csharp
// C#
var node1 = new Uri("http://node1.example.com:9200");
var node2 = new Uri("http://node2.example.com:9200");
var node3 = new Uri("http://node3.example.com:9200");

var connectionPool = new SniffingConnectionPool(new[] {node1, node2, node3});
var config = new ConnectionConfiguration(connectionPool);
var client = new ElasticsearchClient(config);
```

> [AZURE.NOTE] Java 應用程式可透過[傳輸用戶端 API][] 使用類似的功能。

**伺服器型負載平衡**：您可以使用不同的負載平衡器將要求分配給節點。這種方法提供地址透明化的好處；不必以每個節點的詳細資料設定用戶端應用程式，讓您更輕鬆地新增、移除或重新配置節點，而不需修改任何用戶端程式碼。圖 3 顯示的組態使用負載平衡器將要求路由傳送至一組用戶端節點，而如果未使用用戶端節點，則可使用相同的策略來直接連接到資料節點。

> [AZURE.NOTE] 您可以使用 [Azure 負載平衡器][] 向公用網際網路公開叢集，或者如果用戶端應用程式和叢集完全包含於相同的私人虛擬網路 (VNET) 中，您可以使用[內部負載平衡器][]。

![](media/guidance-elasticsearch-general-clientappinstances.png)

**圖 3.**
透過 Azure 負載平衡器連接到 Elasticsearch 叢集的用戶端應用程式執行個體

**自訂負載平衡**：您可以使用 [nginx][] 做為反向 Proxy 伺服器，而不是 Azure 負載平衡器。Nginx 提供數種負載平衡方法，包括循環配置資源，最少連線 (以最少的目前連線將要求路由傳送到目的地)，以及根據用戶端的 IP 位址雜湊。

> [AZURE.NOTE] 您可以將 nginx 伺服器部署為 Azure VM。若要維持可用性，您應該在相同的 Azure 可用性設定組中建立至少兩部 nginx 伺服器。

在決定是否要使用負載平衡時，以及要使用哪一個實作時，應該考慮下列幾點：

- 連接至相同的節點，以處理應用程式的所有執行個體的所有要求，可能會導致該節點變成瓶頸。隨著節點中可用的執行緒數目耗盡，要求將會排入佇列，而如果佇列長度變得過長則可能會遭到拒絕 (請勿在可能部署至多個使用者的應用程式程式碼中，將單一節點的連接詳細資料硬式編碼)。
- Elasticsearch.Net、NEST 和傳輸用戶端 API 的循環配置資源機制會對連接集區中的下一個可用節點重試連接，以處理失敗的連接要求。對集區中無回應節點的連接可能會暫時標示為「失效」；它可能會稍後恢復，而集區可以 Ping 此節點，以判斷該節點是否再次作用中。
- Azure 負載平衡器可以根據許多因素 (用戶端 IP 位址、用戶端連接埠、目的地 IP 位址、目的地連接埠、通訊協定類型)，明確地將要求重新導向至各節點。依照這個策略，在指定的電腦上執行的用戶端應用程式執行個體最有可能會被導向至相同的 Elasticsearch 節點。視負載平衡器的探查組態而定，如果 Elasticsearch 服務在這個節點發生失敗，但 VM 本身會繼續執行，則對此節點的所有連接都會逾時，而其他用戶端執行個體對其他節點的連接可能會繼續進行。
- 您可以設定 Azure 負載平衡器，在節點無法適當地回應負載平衡器所執行的健康狀態探查要求時，讓該節點退出循環。

### 節點探索

Elasticsearch 是以對等通訊為基礎，因此探索叢集中的其他節點是節點生命週期中很重要的部分。節點探索可讓新的資料節點以動態方式加入至叢集，接著讓叢集以透明方式相應放大。此外，如果資料節點無法回應來自其他節點的通訊要求，主要節點可以決定該資料節點失敗並採取必要的步驟，將其保存的分區重新配置給其他作業資料節點。

使用探索模組節可處理 Elasticsearch 節點探索。此探索模組是一個外掛程式，可以切換為使用不同的探索機制。預設探索模組 ([Zen][]) 會致使節點發出 ping 要求，以尋找相同網路上的其他節點。如果其他節點回應，它們會透過 Gossip 來交換資訊。然後主要節點即可將分區分配至新節點 (若是資料節點) 及重新平衡叢集。

Zen 探索模組也會處理主要節點選擇程序以及用於偵測節點失敗的通訊協定。

在 Elasticsearch 2.0 版以前，Zen 探索模組使用了多點傳送通訊讓節點彼此連絡。這可讓您輕鬆地將新節點引進叢集中，但如果相同網路上的另一份 Elasticsearch 安裝恰巧使用相同的叢集名稱，也可能導致安全性問題；新的安裝會被視為相同叢集的一部分，而且分區可能會被導向至此安裝中的節點。此外，如果您執行 Elasticsearch 節點作為 Azure 虛擬機器 (VM)，則不支援多點傳送訊息。基於這些理由，您應該將 Zen 探索設定為使用單點傳送訊息，並在 elasticsearch.yml 組態檔中提供有效的連絡節點清單。

> [AZURE.NOTE] 在 Elasticsearch 2.0 和更新版本中，多點傳送不再是預設的探索機制。

如果您在 Azure VNET 中裝載 Azure vnet Elasticsearch 叢集，您可指定提供給叢集中各 VM 之 DHCP 指派的私人 IP 位址應保持已配置 (靜態) 狀態。您可以使用這些靜態 IP 位址設定 Zen 探索單點傳送訊息。如果您使用具有動態 IP 位址的 VM，請記住，如果 VM 停止並重新啟動，則會被配置新的 IP 位址，讓探索變得更困難。若要處理這種情況，您可以 Zen 探索模組交換 [Azure 雲端外掛程式][]。此外掛程式會使用 Azure API 來實作以 Azure 訂用帳戶資訊為基礎的探索機制。

> [AZURE.NOTE] 目前的 Azure 雲端外掛程式版本會要求您在 Elasticsearch 節點上的 Java 金鑰存放區中安裝您的 Azure 訂用帳戶的管理憑證，並提供用於存取 elasticsearch.yml 檔案中金鑰存放區的位置和認證。此檔案會以純文字格式保存，因此請務必確定只有執行 Elasticsearch 服務的帳戶可以存取這個檔案。此外，這種方法可能與 Azure 資源管理員 (ARM) 部署不相容。基於這些理由，建議您對主要節點使用靜態 IP 位址，並使用這些節點在叢集中實作 Zen 探索單點傳送訊息。在下列組態 (取自範例資料節點的 elasticsearch.yml 檔案) 中，主機 IP 位址會參考叢集中的主要節點：

>`discovery.zen.ping.multicast.enabled: false`  
`discovery.zen.ping.unicast.hosts: ["10.0.0.10","10.0.0.11","10.0.0.12"]`

## 一般系統指導方針

Elasticsearch 可以在各種電腦上執行，範圍從一部膝上型電腦到高階伺服器叢集。不過，可用的資源 (記憶體、運算能力和快速磁碟) 愈多，效能就愈好。下列各節摘要說明執行 Elasticsearch 的基本硬體和軟體需求。

### 記憶體需求
Elasticsearch 會嘗試在記憶體中儲存資料，以提高速度。裝載一個節點以便在 Azure 上進行一般企業或中型商業部署的生產伺服器應該有 14 GB 到 28 GB 的 RAM (D3 或 D4 VM)。將負載分散於多個節點，而非建立具有更多記憶體的節點 (實驗顯示有更多記憶體的節點可能會導致在發生失敗時復原時間延長)。 不過，儘管建立包含非常大量之小型節點的叢集可以增加可用性和輸送量，但也會增加管理和維護這類系統所需的工作。

將伺服器上 50% 的可用記憶體配置給 Elasticsearch 堆積。如果您使用 Linux，請先設定 ES\_HEAP\_SIZE 環境變數，再執行 Elasticsearch。或者，如果使用 Windows 或 Linux，您可於啟動 Elasticseach 時在 *Xmx* 和 *Xms* 參數中指定記憶體大小；將這兩個參數設定為相同的值，可避免 Java 虛擬機器 (JVM) 在執行階段調整堆積大小。不過，無法配置超過 30 GB。將其餘的記憶體使用於作業系統檔案快取。

> [AZURE.NOTE] Elasticsearch 會利用 Lucene 程式庫來建立及管理索引。Lucene 結構會使用以磁碟為基礎的格式，而快取檔案系統快取中的這些結構會大幅提升效能。

> 此外，Elasticsearch 是以 Java 撰寫。在 64 位元電腦上，Java 的最佳堆積大小上限剛好超過 30 GB。超過此大小，Java 會切換成使用延伸機制來參考堆積上的物件，這會增加每個物件的記憶體需求並降低效能。如果堆積大小超過 30 GB，則預設 Java 記憶體回收行程 (Concurrent Mark and Sweep) 也可能會以次佳方式執行。目前不建議切換至不同的記憶體回收行程，因為 Elasticsearch 和 Lucene 只經過對預設值的測試。

請勿過量使用記憶體，因為將主要記憶體交換到磁碟會嚴重影響效能。可能的話，請完全停用交換 (詳細資訊視作業系統而定)。如果不可能，則在 Elasticsearch 組態檔 (elasticsearch.yml) 中啟用 *mlockall* 設定，如下所示：

```yaml
bootstrap.mlockall: true
```

此組態設定會導致 JVM 鎖定其記憶體，並防止作業系統將它換出。

### 磁碟和檔案系統需求

使用固態硬碟 (SSD) 來儲存分區。磁碟應該調整大小，以保存您的分區中預期的資料數量上限，雖然稍後有可能新增其他磁碟。您可以將分區擴充至一個節點上的多個磁碟。

> [AZURE.NOTE] Elasticsearch 會使用 LZ4 演算法來壓縮已儲存欄位的資料，而在 Elasticsearch 2.0 以後的版本中，您可以變更壓縮類型。您可以將壓縮演算法切換成如 *zip* 和 *gzip* 公用程式所使用的 DEFLATE。此壓縮技術可能需要耗用更多資源，但您應考慮將它使用於冷索引 (例如封存的記錄檔資料)。這種方法有助於降低索引大小。

叢集中的所有節點不一定會有相同的磁碟配置和容量。不過，相較於叢集中的其他節點，具有極大磁碟容量的節點會吸引更多資料，並需要提升處理能力才能處理此資料。因此，相較於其他節點，此節點可能會變得「熱門」，可以影響效能。

> [AZURE.NOTE] Azure 提供了數個磁碟儲存體選項，包括標準儲存體、進階儲存體及暫時儲存體。標準儲存體是以轉動式媒體為基礎，而進階儲存體則使用 SSD。根據用來實作 VM 的 SKU 而定，暫時儲存體可以實作為轉動式媒體 (A 系列 VM) 或 SSD (D 系列 VM 和更新版本)。在考慮儲存體選項時，您需要平衡財務和技術的取捨，這在「在 Azure 上使用 Elasticsearch 最大化資料擷取效能」文件中有更詳細的說明。

可能的話，請使用 RAID 0 (串接)。不需要其他可實作同位檢查與鏡像的 RAID 形式，因為 Elasticsearch 會以複本形式提供自己的 HA 解決方案，而 Azure 磁碟無論如何都會儲存三份磁碟資料。

> [AZURE.NOTE] 在 Elasticsearch 2.0.0 以前，您也可藉由在 *path.data* 組態設定中指定多個目錄，於軟體層級實作串接。在 Elasticsearch 2.0.0 中，不再支援這種形式的串接。相反地，不同的分區可能會配置到不同的路徑，但單一分區中的所有檔案都會寫入至相同的路徑。如果您需要串接，您應該在作業系統或硬體層級串接資料。

您應該也會注意到，連接至 VM 的 Azure 磁碟的總 I/O 輸送量受限於其所屬的儲存體群組；單一標準儲存體帳戶可處理高達 20,000 IOPS 的要求率 (這項限制不適用於進階儲存體)。如果這個數字小於您所預期的磁碟 I/O 流量，則將叢集中 VM 的磁碟分散於多個儲存體帳戶，請記住，一個訂用帳戶最多可以建立 100 個儲存體帳戶。

Lucene 程式庫可以使用大量的檔案來儲存索引資料，而 Elasticsearch 可以開啟大量的通訊端以便節點之間以及與用戶端通訊。請確定作業系統已設定為支援足夠數量的開啟檔案描述元 (若有足夠的記憶體可用，最多為 64000 個)。請注意，許多 Linux 散發套件的預設組態將開啟檔案描述元的數目限制為 1024 (小太多)。

Elasticsearch 會使用記憶體對應 (mmap) IO 和 Java New IO (NIO) I/O 的組合，最佳化資料檔案和索引的並行存取。如果您使用 Linux，您應該設定作業系統以確保有足夠的虛擬記憶體可用於 256K 記憶體對應區域的空間。

> [AZURE.NOTE] 許多 Linux 散發套件在安排將資料寫入磁碟時，預設使用完全公平佇列 (CFQ) 排程器。這個排程器並未針對 SSD 最佳化。請考慮將作業系統重新設定為使用 NOOP 排程器或期限排程器，兩者都更適合用於 SSD。

### CPU 需求

使用具有多個核心的 CPU。偏好較多核心的 CPU，而非具有較少核心的更快 CPU。這是因為會根據可用的 CPU 核心數目來設定預設 Elasticsearch 執行緒集區大小。Elasticsearch 所使用的演算法會根據這些計算進行最佳化，建議您不要變更 Elasticsearch 的預設執行緒集區設定。

> [AZURE.NOTE] Azure VM 可用於各種 CPU 組態，並支援 1 到 32 個核心。對於資料節點，最好先考慮標準 D 系列 VM，並選取 D3 (4 核心) 或 D4 (8 核心) SKU。D3 也會提供 14 GB 的 RAM，然而 D4 包含 28GB。如果您需要進階磁碟儲存體，可以使用 DS 系列 VM (考慮 DS3 或 DS4 機器)，而如果您懷疑 CPU 效能可能是限制輸送量的重要因素，則 Dv2 系列會提供功效更高的 2.4 GHz Intel Xeon 處理器。G 系列 (適用於標準儲存體) 和 GS 系列 (適用於進階儲存體) 均使用 Xeon E5 V3 處理器，該處理器很適合用於高度計算密集的工作負載，例如大規模的彙總。如需最新資訊，請參閱[虛擬機器的大小][]。

### 網路需求

視實作的叢集大小和變動性而定，Elasticsearch 需要 1 到 10 Gbps 的網路頻寬。雖著更多節點加入至叢集，Elasticsearch 會在節點之間移轉分區。Elasticsearch 假設所有節點之間的通訊時間大致相等，而不會考慮這些節點上所保存的分區的相對位置。此外，複寫會引發分區之間的大量網路 I/O。基於這些理由，避免在分散於各地的節點上建立叢集。

請注意，Azure VM 的網路頻寬不只受到 SKU 限制，也受到 VM 執行所在實體硬體的整體網路使用量限制。共用硬體的 VM 頻寬會比在專用機器上執行的 VM 頻寬小，但部署成本較低。這表示除了為您的 VM 選取系列和 SKU 以外，您很難控制可用的網路頻寬。比方說，雖然您可以建立包含多個虛擬 NIC 的 VM，但這麼做並不會增加您的 VM 可用的整體頻寬；僅表示有更多虛擬 NIC 會競用相同的實體網路資源。

### 軟體需求

您可以在 Windows 或 Linux 上執行 Elasticsearch。Elasticsearch 服務會部署為 Java jar 程式庫，並相依於 Elasticsearch 封裝中包含的其他 Java 程式庫。您必須安裝 Java 7 (更新 55 或更新版本) 或 Java 8 (更新 20 或更新版本) JVM 才能執行 Elasticsearch。

> [AZURE.NOTE] 除了 *Xmx* 和 *Xms* 記憶體參數 (已指定為 Elasticsearch 引擎的命令列選項 – 請參閱[記憶體需求][]) 以外，請勿修改預設 JVM 組態設定。Elasticsearch 已設計成使用預設值；變更預設值可能會導致 Elasticsearch 失調且執行效能不佳。

### 在 Azure 上部署 Elasticsearch

雖然部署 Elasticsearch 的單一執行個體並不難，但是建立一些節點並在每個節點上安裝和設定 Elasticsearch 卻是費時又容易出錯的程序。如果您考慮在 Azure VM 上執行 Elasticsearch，有兩個選項可協助減少生發錯誤的機會。
- 使用 [Azure 資源管理員 (ARM) 範本](http://azure.microsoft.com/documentation/templates/elasticsearch/) 來建立叢集。此範本已完全參數化，可讓您指定實作節點之 VM 的大小和效能層、要使用的磁碟數目，以及其他常見因素。此範本可以建立以 Windows Server 2012 或 Ubuntu Linux 14.0.4 為基礎的叢集。
- 使用可以自動化或自動執行的指令碼。在 [Azure 快速入門範本][]網站上可取得用於建立和部署 Elasticsearch 叢集的指令碼。

## 叢集和節點大小和延展性考量

Elasticsearch 會啟用數個部署拓撲，其設計用來支援不同的需求和規模層級。本節會討論一些常見的拓樸，並根據這些拓撲說明實作叢集時的考量。

### Elasticsearch 拓撲

圖 4 說明為以 Azure VM 為基礎的雲端設計 Elasticsearch 拓樸的起點。

![](media/guidance-elasticsearch-general-startingpoint.png)

**圖 4.**
使用 Azure 建置 Elasticsearch 叢集的建議起點

此拓撲包含六個資料節點，以及三個用戶端節點和三個主要節點 (只能選擇一個主要節點，萬一選擇的主要節點失敗，則可選擇其他兩個)。 每個節點都會當作個別的 VM 實作。Azure Web 應用程式會透過負載平衡器被導向至用戶端節點。在此範例中，所有節點和 Web 應用程式都位於相同的 Azure VNET，以便有效地與外界隔離。如果叢集必須可供外部使用 (可能成為合併內部部署用戶端之混合式解決方案的一部分)，您可以使用 Azure 負載平衡器來提供公用 IP 位址，但您必須採取其他安全預防措施以避免未經授權存取叢集。選擇性「跳躍箱」是僅供系統管理員使用的 VM。此 VM 有 Azure VNET 的網路連線，但也有對外的網路連線，以便系統管理員從外部網路登入 (應使用強式密碼或憑證保護此登入)。系統管理員可以登入至「跳躍箱」，然後從該處直接連接到叢集中的任何節點。替代方法包括使用組織與 VNET 之間的站台對站 VPN 或使用 [ExpressRoute][] 電路連接到 VNET。這些機制允許對叢集進行系統管理存取，而不會對公用網際網路暴露此叢集。

為了維持 VM 可用性，資料節點會群組為相同的 Azure 可用性設定組。同樣地，用戶端節點會保存在另一個可用性設定組，而主要節點則會儲存在第三個可用性設定組。

此拓撲相當容易相應放大；只要新增更多適當類型的節點，並確保在 elasticsearch.yml 檔案中以相同的叢集名稱設定它們即可。用戶端節點也必須新增至 Azure 負載平衡器的後端集區。

**異地尋找叢集**

請勿將叢集中的節點分散到各區域，這可能會影響節點間的通訊效能 (請參閱[網路需求][])。在不同區域中異地尋找接近使用者的資料時，需要建立多個叢集。在此情況下，您必須考慮如何 (或甚至是否) 同步處理叢集。可能的解決方案包括：

**使用[部族節點][]連接至叢集。** 部族節點類似於用戶端節點，差別在於部族節點可以加入多個 Elasticsearch 叢集並將它們全都視為一個大叢集。資料仍由每個叢集在本機管理 (不會跨叢集界限傳播更新)，但可看見所有資料。部族節點可以查詢、建立和管理叢集中的任何文件。主要限制為部族節點不能用來建立新的索引，而且所有叢集中的索引名稱都必須是唯一的。因此，在設計預定從部族節點存取的叢集時，一定要考慮如何命名索引。

使用這項機制，每個叢集可以包含最可能由本機用戶端應用程式存取的資料，但這些用戶端仍可存取及修改遠端資料 (儘管可能擴大延遲)。圖 5 顯示此拓撲的範例。叢集 1 中的部族節點會反白顯示；其他叢集也可以有部族節點 (雖然這些未顯示在圖上)：

![](media/guidance-elasticsearch-general-tribenode.png)

**圖 5.**
透過部族節點存取多個叢集的用戶端應用程式

在此範例中，用戶端應用程式會連接至叢集 1 的部族節點 (共置於相同的區域中)，但此節點已設定為能夠存取可能位於不同區域的叢集 2 和叢集 3。用戶端應用程式可以傳送要求，以擷取或修改任何叢集中的資料。

> [AZURE.NOTE] 部族節點需要多點傳送探索才能連接到叢集，這可能存有安全疑慮。如需詳細資訊，請參閱[節點探索][]一節。

*	在叢集之間實作異地複寫。在此方法中，在每個叢集所做的變更幾乎會即時傳播到位於其他資料中心的叢集。Elasticsearch 可以使用支援這項功能的協力廠商外掛程式，例如 [PubNub 變更外掛程式][]。
*	使用 [Elasticsearch 快照與還原模組][]。如果資料移動非常緩慢且僅由單一叢集修改，您可以考慮使用快照來製作資料的定期複本，然後在其他叢集中還原這些快照 (如果您已安裝 [Azure 雲端外掛程式][]，則可將快照儲存在 Azure Blob 儲存體中)。不過，此解決方案並不擅於處理快速變更的資料，或是否可以在多個叢集中變更資料。

**小型拓撲**

由專用主要、用戶端和資料節點叢集所組成的大型拓撲可能不適合於每個案例。如果您要建置小型生產或開發系統，請考慮圖 6 所示的 3 節點叢集。用戶端應用程式會直接連接到叢集中任何可用的資料節點。叢集包含標示為 P1-P3 的三個分區 (以允許成長)，加上標示為 R1-R3 的複本。使用三個節點可讓 Elasticsearch 散佈分區和複本，因此如有任何單一節點失敗，將不會遺失任何資料。

![](media/guidance-elasticsearch-general-threenodecluster.png)

**圖 6.**
包含 3 個分區和複本的 3 節點叢集

如果您在獨立的電腦上執行開發安裝，您可以設定包含單一節點的叢集，以做為主要、用戶端和資料儲存體。或者，您可以啟動一個以上的 Elasticsearch 執行個體，進而啟動在同部電腦上以叢集形式執行的多個節點。圖 7 顯示一個範例。

![](media/guidance-elasticsearch-general-developmentconfiguration.png)

**圖 7.**
在同一部電腦上執行多個 Elasticsearch 節點的開發組態

請注意，除非您的開發電腦有大量記憶體和數個快速磁碟，否則不建議將這些獨立組態用於生產環境，因為它們可能會造成爭用情形。此外，它們並不提供任何高可用性保證；如果電腦失敗，所有節點都會遺失。

### 調整叢集和資料節點的考量

Elasticsearch 可以兩個維度調整：垂直方式 (使用愈大、功能愈強的電腦) 和水平方式 (將負載分散於電腦)。

**垂直調整 Elasticsearch 資料節點**

如果您使用 Azure VM 裝載 Elasticsearch 叢集，則每個節點均可對應至一部 VM。節點的垂直延展性限制主要由 VM 的 SKU 以及套用至個別儲存體帳戶和 Azure 訂用帳戶的整體限制所控管。[Azure 訂用帳戶和服務限制、配額和條件約束](azure-subscription-service-limits/)頁面會詳細描述這些限制，但是就建置 Elasticsearch 叢集而言，下列清單中的項目最為適當。此外，您或許不應考慮使用記憶體超過 64GB 的 VM (不需充足的理由)；如[記憶體需求][]一節所述，您不得將每部 VM 上超過 30GB 的 RAM 配置到 JVM 並允許作業系統利用剩餘的記憶體進行 I/O 緩衝：
- 每個儲存體帳戶都受限於 - 20,000 IOPS；使用相同的儲存體帳戶來保存一些 VHD 可能會限制這些 VHD 的效能。
- VNET 中的資料節點數目。如果您不是使用 Azure 資源管理員 (ARM)，每個 VNET 的限制為 2048 個 VM 執行個體。雖然這應足以應付許多情況，如果您有包含數千個節點的超大型組態，這可能是一項限制。
- 每個區域中每個訂用帳戶的儲存體帳戶數目。您可以為每個區域中的每個 Azure 訂用帳戶建立多達 100 個儲存體帳戶。儲存體帳戶用來保存虛擬磁碟，而每個儲存體帳戶的空間限制為 500TB。
- 每個訂用帳戶的核心數目。每個訂用帳戶的預設限制為 20 個核心，但是 Microsoft 可將此限制提高至 10,000 個核心。請記住，有些 VM 大小 (A9、A11、D14 和 DS14) 可以包含 16 個核心，而 G5 VM 有 32 個核心。
- 每個 VM 大小的記憶體數量。較小型 VM 可用的記憶體數量有限 (D1 電腦有 3.5GB，而 D2 電腦有 7GB)。這些機器可能不適合需要 Elasticsearch 快取大量資料以達到良好效能的案例 (例如，在資料擷取期間彙總資料，或分析大量的文件)。
- 每個 VM 大小的磁碟數目上限。這項限制可以限制叢集的大小和效能。磁碟較少表示可以保存的資料較少，而較少的磁碟用於串接可能會降低效能。
- 每個可用性設定組的更新網域 / 容錯網域數目。如果您使用 ARM 建立 VM，則每個可用性設定組可以配置最多 3 個容錯網域和 20 個更新網域。這項限制可能會影響輪流更新很頻繁的大型叢集的恢復能力。

牢記這些限制，您應一律將叢集中 VM 的虛擬磁碟分散於儲存體帳戶，以降低 I/O 節流的機會。在極大型的叢集中，您可能需要重新設計您的邏輯基礎結構，並將它分割為個別的功能磁碟分割。例如，您可能需要將叢集分割於訂用帳戶，雖然此程序可能因為需要連接 VNET 而變得更加複雜。

>	[AZURE.NOTE] 請注意在 Azure 中，儲存體帳戶會以特定的儲存體戳記釘選。這個內部機制用來維護一致性及可用性。本文件[具備強大一致性的高可用性雲端儲存體服務][]提供更多運作方式的詳細資料。如果儲存體中斷已當地語系化為特定戳記，使用該帳戶建立的所有裝置都會收到錯誤。如果發生這種情況，使用這些磁碟的所有 VM 都會失敗。使用多個儲存體帳戶為 VM 託管不同磁碟會增加該 VM 失敗的風險。因此，建議您在每個節點使用單一的儲存體帳戶，並在該帳戶中儲存系統磁碟和所有資料磁碟。

**水平調整 Elasticsearch 叢集**

在 Elasticsearch 內部，水平延展性的限制取決於針對每個索引定義的分區數目。一開始，許多分區可以配置到叢集中的相同節點，但隨著資料量成長，可以新增額外的節點並將分區分散於這些節點。理論上，只有在節點數目達到分區數目時，系統才會停止水平調整。

如同垂直調整，在盤算實作水平調整時，您應考量一些問題，其中包括：

- 您可以在 Azure VNET 中連接的 VM 數目上限。這可能會限制極大型叢集的水平延展性。您可以建立跨多個 VNET 的節點叢集來規避這項限制，但這種方法可能會因為缺乏每個節點與其對等項目的位置關係而導致效能降低。
- 每個 VM 大小的磁碟數目。不同的系列和 SKU 支援不同數目的連接磁碟。此外，您也可以考慮使用 VM 隨附的暫時儲存體來提供數量有限的更快速資料儲存體，雖然您應該考慮一些恢復和復原影響 (如需詳細資訊，請參閱「設定、測試和分析 Elasticsearch 恢復和復原」文件)。D 系列、DS 系列、Dv2 系列和 GS 系列的 VM 會使用 SSD 進行暫時儲存。
- 每個虛擬磁碟的最大 IOPS。使用標準儲存體建立的一般連接磁碟，受限於每個磁碟 500 IOPS。連接的 SSD 最多可支援每個磁碟 5000 IOPS。若要使用連接磁碟的 SSD (而不是暫時儲存體)，您必須建立可支援進階儲存體的 VM (DS 系列或 GS 系列電腦)。

您可以依照要求指示，考慮使用 Azure 調整集來啟動和停止 VM (如需詳細資訊，請參閱 [自動調整虛擬機器調整集中的機器][])。不過，這個方法可能不適合於 Elasticsearch 叢集，原因如下：

- 這種方法最適合用於無狀態的 VM。每次您在 Elasticsearch 叢集中新增或移除節點時，就會重新配置分區以平衡負載，而此程序可能會產生相當大量的網路流量和磁碟 I/O，並可能會嚴重影響資料擷取速率。您必須評估此額外負荷是否值得進行額外的處理及耗用藉由動態啟動更多 VM 所提供的記憶體資源。
- VM 啟動不會立即發生，而且可能需要幾分鐘的時間，其他 VM 才可供使用或是關閉。這種調整方式只能用來處理所需要的持續變更。
- 相應放大之後，您真的需要考慮調整回來嗎？ 從 Elasticsearch 叢集中移除 VM 可能是資源密集的程序，其需要 Elasticsearch 復原位於該 VM 的分區和複本，並且在一或多個剩餘節點上予以重建。同時移除數個 VM 可能會危及叢集的完整性，讓復原變得更加困難。此外，許多 Elasticsearch 實作會隨著時間成長，但資料的本質傾向於不縮減數量。您可以手動刪除文件，也可以設定文件的 TTL (存留時間)，而文件會在 TTL 之後過期並遭到移除，但在大部分的情況下，新增或修改過的文件可能會快速重複使用先前配置的空間。移除或變更文件時，索引中可能會出現片段，在此情況下，您可以使用 Elasticsearch HTTP [最佳化][] API (Elasticsearch 2.0.0 或更早版本) 或 [強制合併][] API (Elasticsearch 2.1.0 和更新版本) 執行磁碟重組。

> [AZURE.NOTE] 最佳化是非常耗費資源的作業，您不應該在高度活躍的索引上執行該作業。不過，最好能最佳化靜止的索引，因為這有助於降低執行搜尋所需的資源。

### 決定索引的分區數目

叢集中的節點數目會隨著時間改變，但建立索引後，索引中的分區數目則是固定的。若要新增或移除分區，則需要重新編製資料的索引 – 此程序會建立包含所需分區數目的新索引，然後將資料從舊索引複製到新索引 (您可以使用別名，讓使用者脫離資料已重編索引的這項事實 – 如需詳細資訊，請參閱「在 Azure 上使用 Elasticsearch 最大化資料彙總和查詢效能」文件)。因此，請務必在叢集中建立第一個索引之前，決定您可能需要的分區數目。您可以執行下列步驟來建立此數字：

1. 使用相同的硬體組態，建立您想要在生產環境中部署的單一節點叢集。
2. 建立與您打算在生產環境中使用的結構相符的索引。為此索引提供單一分區，但不提供任何複本。
3. 將特定數量的實際生產資料新增至索引。
4. 對索引執行一般查詢、彙總和其他工作負載，並測量輸送量和回應時間。
5. 如果輸送量和回應時間在可接受的限制內，則重複從步驟 3 (新增更多資料) 起的程序。
6. 當您似乎已達到分區的容量時 (回應時間和輸送量開始變得無法接受)，請記下文件的數量。
7. 從單一分區的容量至生產環境中的預期文件數目進行推斷，以計算所需的分區數目 (因為推斷不是一門精確的科學，所以您應該在這些計算中納入一些誤差範圍)。

> [AZURE.NOTE] 請記住，每個分區都會當作耗用記憶體、CPU 能力和檔案控制代碼的 Lucene 索引實作。擁有的分區愈多，就需要有更多的資源。

此外，建立更多分區可能會提高延展性 (視您的工作負載和案例而定)，也可以增加資料擷取輸送量，但可能會降低許多查詢的效率。根據預設，查詢會詢問索引所使用的每個分區 (如果您知道您所需的資料位於哪些哪分區上，即可使用[自訂路由][]來修改此行為)。

遵循此程序只能產生分區數目的預估值，但無法得知生產環境中預期的文件數量。在此情況下，您應該判斷初始數量 (如上所述) 和預測的成長率。建立適當數目的分區，以便在您願意重編資料庫索引前的這段期間內處理資料成長。其他用於事件管理和記錄等案例的策略，包括使用循環索引；為每天擷取的資料建立新索引，並透過每天切換以指向最新索引的別名來存取此索引。這種方法可讓您更輕鬆地淘汰舊資料 (您可以刪除不再需要其資訊的索引)，並保持可管理的資料量。

請記住，節點數目不一定要符合分區數目。例如，如果您建立 50 個分區，一開始可以將它們分散到 10 個節點，然後再新增更多節點，以便隨著工作量的增加相應放大系統。不過，請避免在少量的節點上建立非常大量的分區 (例如，1000 個分區散佈於 2 個節點)。雖然理論上系統可以根據此組態調整為 1000 個節點，但是在單一節點上執行 500 個分區可能會減損節點的效能。

> [AZURE.NOTE] 對於大量擷取資料的系統，請考慮使用分區是質數。Elasticsearch 用於將文件路由傳送到分區的預設演算法，在此情況下會產生更加均勻的散佈。

### 安全性考量

根據預設，Elasticsearch 實作最小的安全防護，而且不提供任何驗證和授權方法。這些層面需要設定基礎作業系統和網路，以及使用外掛程式和協力廠商公用程式。範例包括 [Shield][] 和 [Search Guard][]。

> [AZURE.NOTE] Shield 是 Elastic 針對使用者驗證、資料加密、角色型存取控制、IP 篩選和稽核所提供的外掛程式。您必須設定基礎作業系統，才能實作進一步的安全性措施，例如磁碟加密。

在生產系統中，您應該考慮如何：

- 防止未經授權存取叢集。
- 識別和驗證使用者。
- 授權經過驗證的使用者可以執行的作業。
- 保護叢集免於遭受惡意或有害的作業。
- 保護資料免於遭受未經授權的存取。
- 符合商業資料安全性的法規需求 (如果適用)。

### 保護叢集存取

Elasticsearch 是一項網路服務。Elasticsearch 叢集中的節點會使用 HTTP 接聽傳入用戶端要求，並使用 TCP 通道彼此通訊。您應該採取相關步驟，以防止未經授權的用戶端或服務透過 HTTP 和 TCP 路徑傳送要求。請考慮下列項目：

- 定義網路安全性群組，讓 VNET 或 VM 的輸入和輸出網路流量僅限於特定的連接埠。
- 變更用於用戶端 Web 存取 (9200) 和程式設計網路存取 (9300) 的預設連接埠。使用防火牆保護每個節點免於遭受惡意網際網路流量。
- 根據用戶端的位置和連線能力而定，將叢集置於無法直接存取網際網路的私人子網路上。如果叢集必須在子網路外部公開，請透過夠堅強的防禦伺服器或 Proxy 來路由傳送所有要求，以保護叢集。
- 如果您必須提供節點的直接存取，請使用 Elasticsearch Jetty 外掛程式來提供 SSL 連線能力、驗證和連線記錄。或者，設定 nginx Proxy 伺服器並設定 HTTPS 驗證。

> [AZURE.NOTE] 使用 Proxy 伺服器 (例如 nginx)，您也可以限制功能存取。例如，如果需要防止用戶端執行其他作業，您可以設定 nginx，只允許對 \_search 端點提出要求。

- 如果您需要更完善的網路存取安全性，請使用 Shield 或 Search Guard 外掛程式。

### 識別和驗證使用者

用戶端對叢集提出的所有要求都應該經過驗證。此外，您應該防止未經授權的節點加入叢集，因為這些節點可能會略過驗證而提供進入系統的後門。

Elasticsearch 外掛程式可用於執行不同類型的驗證，包括：
- HTTP 基本驗證。每次驗證都包含使用者名稱和密碼。應使用 SSL/TLS 或同層級的保護來加密所有的要求。
- LDAP 和 Active Directory 整合。這個方法需要將 LDAP 或 AD 群組中的角色指派給用戶端。
- 使用 Elasticsearch 叢集本身定義的身分識別進行的原生驗證。
- 叢集內用來驗證所有節點的 TLS 驗證。
- IP 篩選，可防止未經授權的子網路進行連線，也可防止這些子網路的節點加入叢集。

### 授權用戶端要求

授權作業取決於用來提供這項服務的 Elasticsearch 外掛程式。例如，提供基本驗證的外掛程式通常會提供用來定義驗證層級的功能，而使用 LDAP 或 AD 的外掛程式通常會將用戶端與角色產生關聯，然後再指派存取權限給這些角色。使用任何指定的外掛程式時，您應該考慮下列幾點：
- 您需要限制用戶端可以執行的作業嗎？ 例如，用戶端是否能夠監視叢集的狀態，或建立和刪除索引？
- 用戶端應該受限於特定的索引？ 這在多租用戶的情況下很實用：租用戶可能會被指派自己的一組特定索引，而其他租用戶無法存取這些索引。
- 用戶端是否能夠讀取和寫入資料至索引？ 例如，用戶端可以執行使用索引來擷取資料的搜尋，但必須避免在該索引中新增或刪除資料。

目前，大部分的安全性外掛程式將作業的範圍限定於叢集或索引層級，而不是索引內的文件子集。這是基於效率考量。因此，要將要求限制為單一索引內的特定文件並不容易。如果您需要這個層級的資料粒度，請在個別的索引中儲存文件，並使用將索引群組在一起的別名。例如，在個人系統中，如果使用者 A 需要存取含有 X 部門員工相關資訊的所有文件，使用者 B 需要存取含有 Y 部門員工相關資訊的所有文件，而 C 使用者需要存取含有這兩個部門員工相關資訊的所有文件，請建立兩個索引 (針對 X 部門和 Y 部門) 以及可參考這兩個索引的別名。授與使用者 A 讀取第一個索引的權限，授與使用者 B 讀取第二個索引的權限，以及授與使用者 C 透過別名讀取這兩個索引的權限。如需詳細資訊，請參閱[利用別名來假造每個使用者的索引][]。

### 保護叢集

如果叢集未受到細心保護，則可能容易遭到誤用，如下列各點所述：

- 停用 Elasticsearch 查詢中的動態查詢指令碼，因為它們可能會導致安全性漏洞。使用原生指令碼，而非查詢指令碼；原生指令碼是以 Java 撰寫並編譯成 JAR 檔案的 Elasticsearch 外掛程式。

> [AZURE.NOTE] 動態查詢指令碼現在預設為停用；除非您有很充分的理由將它重新啟用，否則請勿這麼做。

- 避免對使用者公開查詢字串搜尋。查詢字串搜尋可讓使用者執行不受妨礙的資源密集型查詢。這些搜尋可能會嚴重影響叢集的效能，並且讓系統易於遭受 DOS 攻擊。此外，查詢字串搜尋可能會公開私密資訊。
- 避免作業耗用大量記憶體，因為這可能會造成記憶體不足例外狀況，進而導致 Elasticsearch 在節點上發生失敗。長時間執行的資源密集型作業也可用來實作 DOS 攻擊。範例包括：
- 嘗試將非常大量的欄位載入記憶體中的搜尋要求 (如果查詢在這些欄位上執行排序、指令碼或 Facet)。
  
> [AZURE.NOTE] 使用 [Doc 值][]可以降低索引的記憶體需求，其作法是將欄位資料儲存到磁碟，而不是載入記憶體中。這有助於減少節點上記憶體耗盡的機會，但速度會跟著降低。

- 同時查詢多個索引的搜尋作業。
- 擷取大量欄位的搜尋作業。這些搜尋作業可能會導致快取大量的資料欄位，進而耗盡記憶體。根據預設，欄位資料快取的大小不受限，但是您可以在 elasticsearch.yml 組態檔中設定 [indices.fielddata.cache.*](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html) 屬性，以限制可用的資源。您也可以設定[欄位資料斷路器][]來防止單一欄位的快取資料耗盡記憶體，以及設定[要求斷路器][]來阻止個別的查詢獨佔記憶體。設定這些參數的代價是提高某些查詢失敗或逾時的可能性。
  
> [AZURE.NOTE] Elasticsearch 一直假設它有足夠的記憶體可執行其目前的工作負載。如果事實並非如此，則 Elasticsearch 服務可能會損毀。Elasticsearch 會提供可傳回資源使用量相關資訊的端點 (HTTP [cat Api][])，您應該仔細監視這項資訊。

- 等待排清進行中記憶體區段的時間太長。這可能會耗盡記憶體中的緩衝區空間。如有必要，請[設定 translog][] 以降低將資料排清至磁碟的臨界值。

- 建立包含大量中繼資料的索引。內含文件中的欄位名稱有極大差異的索引，可能會耗用大量的記憶體。如需詳細資訊，請參閱[對應遽增][]。
  
> [AZURE.NOTE] 長時間執行或查詢密集型作業的定義高度視案例而定。一個叢集通常預期的工作負載可能與另一個叢集上的工作負載具有完全不同的設定檔。判斷無法接受哪些作業時，需要大量研究和測試您的應用程式。

先發制人；偵測惡意活動，並且在其造成重大損害或資料損失前予以阻止。請考慮使用安全性監視和通知系統，該系統可以快速偵測不尋常的資料存取模式，並且適時 (例如使用者登入要求失敗、非預期的節點加入或離開叢集，或作業所花的時間超出預期) 引發警示。可以執行這些工作的工具包括 Elasticearch [Watcher][]。

### 保護資料

您可以使用 SSL/TLS 來保護傳遞中的資料，但是對於磁碟上儲存的資訊，Elasticsearch 並不提供任何內建形式的資料加密功能。請記住，此資訊會保存在一般磁碟檔案中，而任何可存取這些檔案的使用者都可能會危害其中保存的資料，例如將檔案複製到自己的叢集。請考慮下列幾點：
- 保護 Elasticsearch 用來保存資料的檔案。不允許 Elasticsearch 服務以外的項目任意讀取或寫入身分識別。

- 使用加密檔案系統，加密這些檔案中保存的資料。

> [AZURE.NOTE] Azure 現在支援 Linux 和 Windows VM 的磁碟加密。如需詳細資訊，請參閱 [Windows 和 Linux IaaS VM Preview 適用的 Azure 磁碟加密][]。

### 符合法規需求

法規需求主要是關於用來維護事件歷程記錄的稽核作業，並確保這些作業的隱私權，協助防止外部機構監視 (和重新執行) 這些作業。您特別需要考慮如何：

- 追蹤所有要求 (不論成功與否)，以及所有對系統的存取嘗試。
- 加密用戶端對叢集所做的通訊，以及叢集所執行的節點對節點通訊。您應該對所有的叢集通訊實作 SSL/TLS。如果您組織的需求不同於可透過 SSL/TLS 取得的編碼器，Elasticsearch 也支援隨插即用的編碼器。
- 安全地儲存所有稽核資料。稽核資訊的數量可能會急速成長，必須受到妥善保護，才能防止竄改稽核資訊。
- 安全地封存稽核資料。

### 監視考量

作業系統層級和 Elasticsearch 層級的監視都很重要。

您可以使用作業系統特有的工具，在作業系統層級執行監視。在 Windows 中，這包括具有適當效能計數器的「效能監視器」等項目，而在 Linux 中，您可以使用 *vmstat* 、 *iostat* 和 *top* 等工具。要在作業系統層級監視的主要項目包括 CPU 使用率、磁碟 I/O 數量、磁碟 I/O 等候時間、網路流量。在精心調整的 Elasticsearch 叢集中，Elasticsearch 程序的 CPU 使用率應該很高，而磁碟 I/O 等候時間應該很小。

在軟體層級，您應該監視要求的輸送量和回應時間，以及失敗的要求詳細資料。Elasticsearch 提供數個 API，您可用來檢查叢集的各層面效能。最重要的兩個 API 是 *\_cluster/health* 和 *\_nodes/stats* 。 *\_cluster/health* API 可用來提供叢集整體健康狀態的快照，以及提供每個索引的詳細資訊，如下列範例所示：

`GET _cluster/health?level=indices`

使用此 API 產生的範例輸出如下所示：

```json
{
    "cluster_name": "elasticsearch",
    "status": "green",
    "timed_out": false,
    "number_of_nodes": 6,
    "number_of_data_nodes": 3,
    "active_primary_shards": 10,
    "active_shards": 20,
    "relocating_shards": 0,
    "initializing_shards": 0,
    "unassigned_shards": 0,
    "delayed_unassigned_shards": 0,
    "number_of_pending_tasks": 0,
    "number_of_in_flight_fetch": 0,
    "indices": {
        "systwo": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        },
        "sysfour": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        }
    }
}
```

此叢集包含名為 *systwo* 和 *sysfour* 的兩個索引。要針對每個索引監視的主要統計資料為狀態、active\_shards 和 unassigned\_shards。狀態應為綠色、active\_shards 數目應反映 number\_of\_shards 和 number\_of\_replicas，而 unassigned\_shards 應為零。如果狀態是「紅色」，則有一部分的索引遺失或損毀。如果 *active\_shards* 設定小於 *number\_of\_shards* - (*number\_of\_replicas* + 1) 且 unassigned\_shards 不為零，您可以驗證此狀態。請注意，黃色狀態表示索引處於過渡狀態，這是新增更多複本或正在重置分區的結果。轉換完成時，狀態應會切換為綠色。如果狀態停留在黃色的時間延長或變更為紅色，您應該查看作業系統層級是否發生任何重要的 I/O 事件 (例如磁碟或網路失敗)。
\_nodes/stats API 會發出有關叢集中各節點的廣泛資訊：

`GET _nodes/stats`

產生的輸出包括有關如何在每個節點上儲存索引的詳細資訊 (包括文件的大小和數目)、執行索引編製、查詢、搜尋、合併和 快取所花費的時間、作業系統和程序資訊、有關 JVM 的統計資料 (包括記憶體回收效能) 以及執行緒集區。如需詳細資訊，請參閱[監視個別的節點][]。

如果大部分的 Elasticsearch 要求失敗並出現 *EsRejectedExecutionException* 錯誤訊息，則 Elasticsearch 便無法跟上在傳送途中的工作。在此情況下，您需要找出導致 Elasticsearch 落後的瓶頸。請考慮下列項目：

- 如果由於資源條件約束而造成瓶頸 (例如配置給 JVM 的記憶體不足導致過多的記憶體回收)，則考慮配置額外的資源 (在此情況下，請設定 JVM 使用更多的記憶體，最多為節點上 50% 的可用儲存體 – 請參閱[記憶體需求][])。
- 如果叢集顯示冗長的 I/O 等候時間，而且使用 \_node/stats API 為索引收集的合併統計資料包含很大的數值，此索引則為寫入頻繁索引。再次瀏覽 [編製索引作業的最佳化資源](編製索引作業的最佳化資源) 一節中提出的各點，以微調索引效能。
- 對執行資料擷取作業的用戶端應用程式進行節流，並判斷這對於效能的影響。如果這種方式顯示大幅改進，請考慮保持節流，或將寫入頻繁索引的負載分散於多個節點，進而相應放大。如需詳細資訊，請參閱「在 Azure 上使用 Elasticsearch 最大化資料擷取效能」。
- 如果索引的搜尋統計資料指出查詢花費很長的時間，則考慮如何最佳化查詢。如需詳細資訊，請參閱[查詢微調][]一節。請注意，您可以使用搜尋統計資料所報告的 *query\_time\_in\_millis* 和 *query\_total* 值來計算查詢效率的概略指南；*query\_time\_in\_millis* / *query\_total* 方程式將會提供每個查詢的平均時間。

### 用於監控 Elasticsearch 的工具

有各種工具可用來執行生產環境中 Elasticsearch 的日常監視。這些工具通常使用基礎 Elasticsearch API 來收集資訊，並以比原始資料更易於觀察的方式呈現詳細資料。常見範例包括 [Elasticsearch-Head][]、[Bigdesk][]、[Kopf][] 和 [Marvel][]。

Elasticsearch-Head、Bigdesk 和 Kopf 可當作 Elasticsearch 軟體的外掛程式執行。比較新的 Marvel 版本可以獨立執行，但需要 [Kibana][] 來提供資料擷取和裝載環境。使用 Marvel 搭配 Kibana 的優點是，您可以在與 Elasticsearch 叢集不同的環境中實作監視，讓您得以探索監視工具當作 Elasticsearch 軟體的一部分執行時不可能出現的 Elasticsearch 問題。比方說，如果 Elasticsearch 重複失敗，或執行速度非常慢，當作 Elasticsearch 外掛程式執行的工具也會受到影響，而讓監控與診斷變得更加困難。

在作業系統層級，您可以使用各種工具 (例如 [Azure Operations Management Suite][] 的 [記錄檔分析] 功能或 [Azure 入口網站的 Azure 診斷][]，擷取裝載 Elasticsearch 節點之 VM 的效能資料。另一種方法是使用 [Logstash][] 來擷取效能和記錄檔資料，將此資訊儲存在個別的 Elasticsearch 叢集中 (請勿使用您用於應用程式的相同叢集)，然後使用 Kibana 將資料視覺化。如需詳細資訊，請參閱[採用 ELK 的 Microsoft Azure 診斷][]。

### 用於測試 Elasticsearch 效能的工具

如果您要進行 Elasticsearch 效能評定或叢集效能測試，可以使用其他工具。這些工具適合用於開發或測試環境，而非生產環境。[Apache JMeter][] 是常用的範例。

JMeter 用來執行本指導方針相關文件中所述的效能評定和其他負載測試。「在 Elasticsearch 上使用 JMeter 執行效能測試」文件會詳細說明 JMeter 的設定和使用方式。

## 後續步驟
- [Elasticsearch：完全指南](https://www.elastic.co/guide/en/elasticsearch/guide/master/index.html)

[Apache JMeter]: http://jmeter.apache.org/
[Apache Lucene]: https://lucene.apache.org/
[自動調整虛擬機器調整集中的機器]: virtual-machines-vmss-walkthrough/
[Windows 和 Linux IaaS VM Preview 適用的 Azure 磁碟加密]: azure-security-disk-encryption/
[Azure 負載平衡器]: load-balancer-overview/
[ExpressRoute]: expressroute-introduction/
[內部負載平衡器]: load-balancer-internal-overview/
[虛擬機器的大小]: virtual-machines-size-specs/

[記憶體需求]: #memory-requirements
[網路需求]: #network-requirements
[節點探索]: #node-discovery
[查詢微調]: #query-tuning

[A Highly Available Cloud Storage Service with Strong Consistency]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx
[具備強大一致性的高可用性雲端儲存體服務]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx
[Azure 雲端外掛程式]: https://www.elastic.co/blog/azure-cloud-plugin-for-elasticsearch
[Azure 入口網站的 Azure 診斷]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[Azure Operations Management Suite]: https://www.microsoft.com/server-cloud/operations-management-suite/overview.aspx
[Azure 快速入門範本]: https://azure.microsoft.com/documentation/templates/
[Bigdesk]: http://bigdesk.org/
[cat Api]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/cat.html
[設定 translog]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-translog.html
[自訂路由]: https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-routing-field.html
[Doc 值]: https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html
[Elasticsearch]: https://www.elastic.co/products/elasticsearch
[Elasticsearch-Head]: https://mobz.github.io/elasticsearch-head/
[Elasticsearch.Net 和 NEST]: http://nest.azurewebsites.net/
[Elasticsearch 快照與還原模組]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html
[利用別名來假造每個使用者的索引]: https://www.elastic.co/guide/en/elasticsearch/guide/current/faking-it.html
[欄位資料斷路器]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html#fielddata-circuit-breaker
[強制合併]: https://www.elastic.co/guide/en/elasticsearch/reference/2.1/indices-forcemerge.html
[gossiping]: https://en.wikipedia.org/wiki/Gossip_protocol
[Kibana]: https://www.elastic.co/downloads/kibana
[Kopf]: https://github.com/lmenezes/elasticsearch-kopf
[Logstash]: https://www.elastic.co/products/logstash
[對應遽增]: https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion
[Marvel]: https://www.elastic.co/products/marvel
[採用 ELK 的 Microsoft Azure 診斷]: https://github.com/mspnp/semantic-logging/tree/elk
[監視個別的節點]: https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_monitoring_individual_nodes
[nginx]: http://nginx.org/en/
[節點用戶端 API]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/node-client.html
[最佳化]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/indices-optimize.html
[PubNub 變更外掛程式]: http://www.pubnub.com/blog/quick-start-realtime-geo-replication-for-elasticsearch/
[要求斷路器]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html#request-circuit-breaker
[Search Guard]: https://github.com/floragunncom/search-guard
[Shield]: https://www.elastic.co/products/shield
[傳輸用戶端 API]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/transport-client.html
[部族節點]: https://www.elastic.co/blog/tribe-node
[Watcher]: https://www.elastic.co/products/watcher
[Zen]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html

<!---HONumber=AcomDC_0211_2016-->
