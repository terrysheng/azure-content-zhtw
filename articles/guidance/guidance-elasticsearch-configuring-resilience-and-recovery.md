<properties
   pageTitle="在 Azure 上設定 Elasticsearch 的恢復和復原"
   description="Elasticsearch 的恢復和復原相關考量。"
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
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# 在 Azure 上設定 Elasticsearch 的恢復和復原

本文是[系列文章的其中一篇](guidance-elasticsearch.md)。

Elasticsearch 的主要功能是在發生節點失敗和/或網路磁碟分割事件中提供復原能力的支援。複寫是可用以改善任何叢集復原能力的最明顯方法，還可讓 Elasticsearch 確保當某一個節點變得無法存取時，可在不同的節點上取得任何項目一個以上的複本。如果節點變得暫時無法使用，則在問題解決之前，包含遺失節點資料複本的其他節點可以做為遺失資料。在長期問題的事件中，可以新的節點取代遺失的節點，而且 Elasticsearch 可以從複本將資料還原至新的節點。

我們在此摘要說明 Elasticsearch 裝載於 Azure 時所提供的恢復和復原選項，並說明您應該考量的一些 Elasticsearch 叢集重要層面，以將資料遺失和延長資料復原時間的機會降到最低。

本文也說明一些已執行的範例測試，這些測試可顯示 Elasticsearch 叢集上不同失敗類型的影響，以及系統在復原時的回應方式。

Elasticsearch 叢集使用複本來維護可用性並提升讀取效能。複本應該存放在與其複寫的主要分區不同的 VM 上。用意在於如果裝載資料節點的 VM 失敗或變得無法使用，系統即可利用保留複本的 VM 繼續運作。

## 使用專用的主要節點

Elasticsearch 叢集中有一個節點會被選為主要節點。此節點的目的是要執行叢集管理作業，例如：

- 偵測失敗的節點並切換至複本，

- 重新放置分區以平衡節點工作負載，以及

- 在節點重新上線時復原分區。

您應該考慮在重要叢集中使用專用的主要節點，並確定有 3 個專用節點的唯一角色是主要。這項設定可以減少這些節點必須執行 (未儲存資料或處理查詢) 的資源密集工作數量，且有助於改善叢集穩定性。只會選擇其中一個節點，但其他節點將包含系統狀態的複本，而且可以在選擇的主要節點失敗時接管。

## 以 Azure 控制高可用性 – 更新網域和容錯網域 

不同的 VM 可以共用相同的實體硬體。在 Azure 資料中心，單一機架可以裝載數個 VM，而這些 VM 會共用一般的電源和網路交換器。因此，單一機架層級失敗會影響數個 VM。Azure 會使用容錯網域 (FD) 的概念，試著分散此風險。FD 大致對應於一組共用相同機架的 VM。若要確定機架層級失敗不會同時損毀某個節點以及保留其所有複本的節點，您應該確定 VM 會分散於 FD。

同樣地，[Azure 網狀架構控制器](https://azure.microsoft.com/documentation/videos/fabric-controller-internals-building-and-updating-high-availability-apps/)可將 VM 關閉，以執行計劃性維護和作業系統升級。Azure 會將 VM 配置到更新網域 (UD)。當計劃性維護事件發生時，只在單一 UD 中的 VM 會受影響；其他 UD 中的 VM 仍可繼續執行，直到正在更新的 UD 中的 VM 回到線上為止。因此，您還必須確保裝載節點與其複本的 VM 盡可能屬於不同的 UD。

> [AZURE.NOTE] 如需 FD 和 UD 的詳細資訊，請參閱[管理虛擬機器的可用性][]。

您無法明確地將 VM 配置至特定的 UD 和 FD；此配置會在建立 VM 時受到 Azure 控制；如需詳細資訊，請參閱[管理虛擬機器的可用性][]文件。不過，您可以指定 VM 應該建立為可用性設定組 (AS) 的一部分。相同 AS 中的 VM 會分散於 UD 和 FD。如果您手動建立 VM，Azure 將會每個 AS 與兩個 FD 和五個 UD 建立關聯，而機器會配置到這些 FD 和 UD，並在進一步佈建 VM 時循環使用，如下所示：

- 在 AS 中佈建的第一個 VM 會置於第一個 FD (FD 0) 和第一個 UD (UD 0)。
- 在 AS 中佈建的第二個 VM 會置於 FD 1 和 UD 1。
- 在 AS 中佈建的第三個 VM 會置於 FD 0 和 UD 2。- 在 AS 中佈建的第四個 VM 會置於 FD 1 和 UD 3。
- 在 AS 中佈建的第五個 VM 會置於 FD 0 和 UD 4。
- 在 AS 中佈建的第六個 VM 會置於 FD 1 和 UD 0。
- 在 AS 中佈建的第七個 VM 會置於 FD 0 和 UD 1。

> [AZURE.IMPORTANT] 如果您使用 Azure Resource Manager (ARM) 建立 VM，則每個可用性設定組可以配置最多 3 個 FD 和 20 個 UD。這就是使用 ARM 的不得已理由。

一般而言，將相同用途的所有 VM 放在相同的可用性設定組中，但是為執行不同功能的 VM 建立不同的可用性設定組。有了 Elasticsearch，這表示您應該考慮為下列各項建立至少個別的可用性設定組：

- 裝載資料節點的 VM。
- 裝載用戶端節點的 VM (如果您使用它們)。
- 裝載主要節點的 VM。

此外，您應該確定叢集中的每個節點會留意其所屬的更新網域和容錯網域。這項資訊有助於確保 Elasticsearch 不會在相同的容錯和更新網域中建立分區及其複本，進而將分區及其複本的範圍最小化，以免同時關閉。您可以設定[分區配置感知](https://www.elastic.co/guide/en/elasticsearch/reference/current/allocation-awareness.html#allocation-awareness)來設定 Elasticsearch 節點，以鏡像處理叢集的硬體分佈。例如，您可以在 elasticsearch.yml 檔案中定義一組自訂的節點屬性 (稱為 *faultDomain* 和 *updateDomain*)，如下所示：

```yaml
node.faultDomain: \${FAULTDOMAIN}
node.updateDomain: \${UPDATEDOMAIN}
```

在此情況下，會於 Elasticsearch 啟動時使用 *\\${FAULTDOMAIN}* 和 *\\${UPDATEDOMAIN}* 環境變數中保留的值設定屬性。您也需要將下列項目加入至 Elasticsearch.yml 檔案，以指出 *faultDomain* 和 *updateDomain* 為配置感知屬性，並指定這些屬性可接受的值集：

```yaml
cluster.routing.allocation.awareness.force.updateDomain.values: 0,1,2,3,4
cluster.routing.allocation.awareness.force.faultDomain.values: 0,1
cluster.routing.allocation.awareness.attributes: updateDomain, faultDomain
```

您可以搭配使用分區配置感知與[分區配置篩選](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/shard-allocation-filtering.html#shard-allocation-filtering)，明確指定哪些節點可以裝載任何給定索引的分區。

如果您需要擴充超出 AS 中的 FD 和 UD 數目，您可以在其他 AS 中建立 VM。不過，您必須瞭解不同 AS 中的節點可以關閉，以便同時進行維護。試著確保相同的 AS 內包含每個分區及其至少一個複本。

> [AZURE.NOTE] 目前有每個 AS 裡 100 個 VM 的限制。如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](../azure-subscription-service-limits.md)。

### 備份與還原

使用複本並不會提供重大失敗 (例如不小心刪除整個叢集) 的完整保護。您應該確定定期在叢集中備份資料，而且您已嘗試和測試用於從這些備份還原系統的策略。

使用 Elasticsearch [快照與還原 API](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html) 來備份及還原索引。快照可以儲存至共用的檔案系統。或者，可利用外掛程式將快照寫入至 HDFS ([HDFS 外掛程式](https://github.com/elasticsearch/elasticsearch-hadoop/tree/master/repository-hdfs)) 或 Azure 儲存體 ([Azure 雲端外掛程式](https://github.com/elasticsearch/elasticsearch-cloud-azure#azure-repository))。

選取快照儲存體機制時，您應該考慮下列幾點：

- 您可以使用 [Azure 檔案儲存體](https://azure.microsoft.com/services/storage/files/)實作可從所有節點存取的共用檔案系統。

- 如果您正在執行與 Hadoop 搭配的 Elasticsearch，只能使用 HDFS 外掛程式。

- HDFS 外掛程式會要求您停用在 JVM 的 Elasticsearch 執行個體內部執行的 Java 安全性管理員。

- HDFS 外掛程式支援任何與 HDFS 相容的檔案系統，前提是使用正確的 Hadoop 組態搭配 Elasticsearch。

  
## 處理節點之間的間歇連線

間歇性網路問題、在資料中心例行維護後的VM 重新開機，以及其他類似事件都可能導致節點變得暫時無法存取。在事件可能短暫存留的情況下，重新平衡連續快速出現兩次之分區的額外負荷 (一次在偵測到失敗時，另一次在主要節點看得見節點時) 可能會變成很大的額外負荷，進而影響效能。您可以藉由設定某個索引或所有索引的 *delayed\_timeout* 屬性，防止暫時無法存取節點而導致主要節點重新平衡叢集。以及範例將延遲設定為 5 分鐘：

```http
PUT /_all/settings
{
	"settings": {
    "index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```

如需詳細資訊，請參閱[當節點離開時延遲配置](https://www.elastic.co/guide/en/elasticsearch/reference/current/delayed-allocation.html)。

在容易發生中斷的網路中，您也可以修改一些參數，以設定主要節點偵測何時無法再存取另一個節點。這些參數屬於 Elasticsearch 隨附的 [Zen 探索](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html#modules-discovery-zen)模組，您可以在 Elasticsearch.yml 檔案中設定這些參數。例如，*discovery.zen.fd.ping.retries* 參數會指定主要節點在判定失敗之前會嘗試 Ping 叢集中另一個節點的次數。此參數的預設值為 3，但您可加以修改，如下所示：

```yaml
discovery.zen.fd.ping_retries: 6
```

## 控制復原

連線到在失敗後還原的節點時，需要復原該節點上的所有分區，才能變成最新狀態。根據預設，Elasticsearch 會依下列順序復原分區：

- 依反向索引建立日期。較新的索引會在較舊的索引之前復原。

- 依反向索引名稱。將會先還原名稱的文數字順序大於其他名稱的索引。

如果有些索引比其他索引更重要，但不符合這些條件，您可以設定 *index.priority* 屬性，以覆寫索引的優先順序。這個屬性值較高的索引將會在具有較低值的索引之前復原：

```http
PUT low_priority_index
{
	"settings": {
		"index.priority": 1
	}
}

PUT high_priority_index
{
	"settings": {
		"index.priority": 10
	}
}
```

如需詳細資訊，請參閱[索引復原優先順序](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/recovery-prioritization.html#recovery-prioritization)。

您可以使用 *\_recovery* API 監視一或多個索引的復原程序：

```http
GET /high_priority_index/_recovery?pretty=true
```

如需詳細資訊，請參閱[索引復原](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-recovery.html#indices-recovery)。

> [AZURE.NOTE] 具有需要復原之分區的叢集會有「黃色」狀態，表示並非所有的分區目前均可使用。當所有分區皆可使用時，叢集狀態應還原成「綠色」。狀態為「紅色」的叢集表示實際遺失一或多個分區；可能需要從備份還原資料。

## 防止裂腦 

如果節點之間的連線失敗，可能會發生裂腦。如果主要節點無法連到叢集的一部分，將會在仍可聯繫的網路區段中進行選擇，而另一個節點將會變成主要節點。在設定錯誤的叢集中，叢集的每個部分可能有不同的主要節點，以致資料不一致或損毀。這種現象稱為「裂腦」。

您可以在 elasticsearch.yml 檔案中設定探索模組的 *minimum\_master\_nodes* 屬性，以減少裂腦的機會。這個屬性會指定必須有多少個可用節點，才能夠選擇主要節點。下列範例會將這個屬性的值設為 2：

```yaml
discovery.zen.minimum_master_nodes: 2
```

此值應設為能夠履行主要角色的過半數節點數目下限。例如，如果您的叢集有 3 個主要節點，則 *minimum\_master\_nodes* 應該設定為 2；如果您有 5 個主要節點，則 *minimum\_master\_nodes* 應該設為 3。在理想情況下，您應該有奇數的主要節點數目。

**注意：**如果相同叢集中的多個主要節點同時啟動，就可能發生裂腦 (split brain)。雖然很少會發生這種情形，只要連續啟動節點 (各節點間短暫延遲 5 秒)，即可避免這種情形。

## 處理輪流更新

如果您自行執行軟體升級至節點 (例如移轉至較新版本，或執行修補程式)，您可能需要處理要求離線的個別節點，同時讓叢集的其餘部分保留可用狀態。在此情況下，請考慮實作下列程序：

確定分區重新配置延遲夠久，以防止選擇的主要節點將遺失節點中的分區重新平衡於叢集的其餘部分。根據預設，分區重新配置會延遲 1 分鐘，但如果節點無法使用的時間可能更長，您可以增加持續期間。下列範例將延遲增加為 5 分鐘：

```http
PUT /_all/_settings
{
	"settings": {
		"index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```

> [AZURE.IMPORTANT] 您也可以將叢集的 *cluster.routing.allocation.enable* 設定為 *none*，進而完全停用分區重新配置。不過，如果在節點離線時可能建立新的索引，您應該避免使用這個方法，因為這會造成索引配置失敗，進而導致叢集出現紅色狀態。

在即將維護的節點上停止 Elasticsearch。如果 Elasticsearch 是以服務方式執行，您可以使用作業系統命令，以受控制的方式中止這個程序。下列範例示範如何在 Ubuntu 上執行的單一節點上停止 Elasticsearch 服務：

```bash
service elasticsearch stop
```

或者，您可以直接在節點上使用關機 API：

```http
POST /_cluster/nodes/_local/_shutdown
```

在節點上執行所需的維護，然後重新啟動節點並等它加入叢集。

重新啟用分區配置：

```http
PUT /_cluster/settings
{
	"transient": {
		"cluster.routing.allocation.enable": "all"
	}
}
```

> [AZURE.NOTE] 如果您需要維護多個節點，請在重新啟用分區配置之前，於每個節點重複步驟 2、3 和 4。

如果可以的話，請在此程序期間停止檢索新的資料。這有助於在節點重新上線並重新加入叢集時，將復原時間降至最低。

請注意，請注意 JVM 等項目的自動更新 (理想上，停用這些項目的自動更新)，尤其是在 Windows 之下執行 Elasticsearch 時。Java 更新代理程式可以自動下載最新版的 Java，但是可能需要重新啟動 Elasticsearch，更新才會生效。根據 Java 更新代理程式的設定方式而定，這可能導致不協調的節點暫時遺失。這也會導致相同叢集中不同的 Elasticsearch 執行個體執行不同版本的 JVM，進而造成相容性問題。

## 測試和分析 Elasticsearch 恢復和復原

本節說明一系列已執行的測試，以評估 Elasticsearch 叢集 (包含三個資料節點及三個主要節點) 的恢復和復原。

已測試四個案例：

1.  節點失敗並重新啟動，未遺失資料。資料節點停止，並在 5 分鐘後重新啟動。Elasticsearch 已設定為在此間隔內不重新配置遺失的分區，因此移動分區時不會引發任何額外的 I/O。當節點重新啟動時，復原程序會將該節點上的分區回復到最新狀態。

2.  節點失敗，資料嚴重遺失。資料節點停止，並清除它所保留的資料以模擬磁碟嚴重損毀。然後，節點重新啟動 (在 5 分鐘後)，實際上成為原始節點的替代品。復原程序需要重建此節點遺失的資料，且可能需要重新配置其他節點上保留的分區。

3.  節點失敗並重新啟動，未遺失資料，但重新配置分區。資料節點停止，而它所保留的分區重新配置給其他節點。然後，節點重新啟動，並進一步重新配置來平衡叢集。

4.  輪流更新。叢集中的每個節點停止，並於短暫間隔後重新啟動，以模擬電腦在軟體更新之後重新開機。無論何時只有一個節點停止。節點關閉時，不會重新配置分區。

每個案例都有相同的工作負載，該工作負載是由在節點離線或復原時的資料擷取工作、彙總和篩選查詢混合而成。工作負載中的大量插入作業各自儲存了 1000 份文件，而當彙總和篩選查詢使用了包含數百萬份文件的個別索引時，會對一個索引執行這些大量插入作業；這是為了能夠與大量插入分開評估查詢的效能。每個索引包含五個分區和一個複本。

下列各節將摘要說明這些測試的結果，並指明當節點離線或進行復原時的任何效能降低，以及已回報的任何錯誤。結果會以圖形方式呈現，並反白顯示一或多個節點遺失的時間點，以及評估系統完全復原並達到節點離線前的類似效能等級所需的時間。

> [AZURE.NOTE] 可在線上取得用來執行這些測試的測試控制工具。您可以調整並使用這些控制工具來驗證自己的叢集設定的復原能力。如需詳細資訊，請參閱[執行自動化 Elasticsearch 復原測試][]。

## 節點失敗並重新啟動，未遺失資料：結果

<!-- TODO; reformat this pdf for display inline -->

這項測試的結果會顯示在 [ElasticsearchRecoveryScenario1.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario1.pdf) 檔案中。圖表顯示叢集中每個節點的工作負載和實體資源的效能設定檔。圖形的開頭部分顯示通常執行大約 20 分鐘的系統，此時節點 0 在重新啟動前已關閉 5 分鐘。其中說明另外 20 分鐘的統計資料；系統需要大約 10 分鐘的時間才能復原並穩定下來。這是以不同工作負載的交易速率和回應時間來說明。

請注意下列幾點：

- 在測試期間，未回報任何錯誤。未遺失資料，所有作業都順利完成。

- 當節點 0 離線時，三種作業類型 (大量插入、彙總查詢和篩選查詢) 的交易速率會下降，而平均回應時間會增加。

- 在復原期間，彙總查詢和篩選查詢作業的交易速率和回應時間已逐漸恢復。在降低之前，大量插入的效能會復原一小段時間。不過，這可能是由於導致大量插入所用索引成長的資料量，而且可以看到這項作業的交易速率變慢 (甚至在節點 0 離線之前)。

- 節點 0 的 CPU 使用率圖形顯示復原階段期間的活動減少，這是因為復原機制導致磁碟和網路活動增加；節點必須追回在離線時所遺失的任何資料並更新其內含的分區。

- 在所有節點上，索引分區的分佈方式不完全一樣；有兩個各自包含 5 個分區和 1 複本的索引，總計有 20 個分區。兩個節點會因此包含 6 個分區，而其他兩個則各自保留 7 個。這在最初 20 分鐘內的 CPU 使用率圖形中很明顯；節點 0 比其他兩個節點較不忙碌。在復原完成之後，似乎發生轉變，因為節點 2 似乎變成負載較輕的節點。

    
## 節點失敗，資料嚴重遺失：結果

<!-- TODO; reformat this pdf for display inline -->

這項測試的結果會顯示在 [ElasticsearchRecoveryScenario2.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario2.pdf) 檔案中。如同第一項測試，圖形的開頭部分顯示通常執行大約 20 分鐘的系統，此時節點 0 已關閉 5 分鐘。在此間隔期間，會移除這個節點上的 Elasticsearch 資料，並在重新啟動前，模擬資料嚴重遺失。在恢復測試前所看到的效能等級之前，完全復原似乎需要 12-15 分鐘的時間。

請注意下列幾點：

- 在測試期間，未回報任何錯誤。未遺失資料，所有作業都順利完成。

- 當節點 0 離線時，三種作業類型 (大量插入、彙總查詢和篩選查詢) 的交易速率會下降，而平均回應時間會增加。此時，測試的效能設定檔類似於第一個案例；這並不令人感到意外，到目前為止，案例都相同。

- 在復原期間，交易速率和回應時間已恢復，雖然在這段期間發據發生更多變動。這最有可能是因為叢集中的節點正在執行額外工作，並提供資料以還原遺失的分區。CPU 使用率、磁碟活動和網路活動圖形中明顯有此額外工作。

- 節點 0 和 1 的 CPU 使用率圖形顯示復原階段期間的活動減少，這是因為復原程序導致磁碟和網路活動增加。在第一個案例中，只有正在復原的節點會出現這種行為，但在此案例中，節點 0 大部分的遺失資料似乎可能正從節點 1 進行還原。

- 相較於第一個案例，節點 0 的 I/O 活動實際上減少。這可能是由於只複製整個分區的資料，而非複製讓現有分區保持最新狀態所需的一系列較小 I/O 要求的 I/O 效率所致。

- 上述三個節點的網路活動指出在節點間傳送和接收資料時活動會暴增。在案例 1 中，只有節點 0 顯示為較多網路活動，但此活動似乎持續更長的時間。同樣地，這項差異可能是由於以單一要求的形式傳送某個分區的完整資料，而非傳送復原分區時所收到的一系列較小要求的效率所致。

## 節點失敗並重新啟動，但重新配置分區：結果

<!-- TODO; reformat this pdf for display inline -->

[ElasticsearchRecoveryScenario3.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario3.pdf) 說明這項測試的結果。如同第一項測試，圖形的開頭部分顯示通常執行大約 20 分鐘的系統，此時節點 0 已關閉 5 分鐘。此時，Elasticsearch 叢集會嘗試重新建立遺失的分區，並將分區重新平衡於剩餘的節點。5 分鐘之後，節點 0 會再次上線，而叢集必須再次重新平衡分區。效能會在 12-15 分鐘後恢復。

請注意下列幾點：

- 在測試期間，未回報任何錯誤。未遺失資料，所有作業都順利完成。

- 相較於先前的兩項測試，當節點 0 離線時，三種作業類型 (大量插入、彙總查詢和篩選查詢) 的交易速率會下降，而平均回應時間會大幅增加。這是由於在這段期間內節點 1 和 2 的磁碟和網路活動數據提高，證明了重建遺失分區並重新平衡叢集會導致叢集活動增加。

- 在節點 0 再次上線後的這段期間，交易速率和回應時間仍會有變動。

- 節點 0 的 CPU 使用率和磁碟活動圖形顯示復原階段的初始動作非常精簡。這是因為在此時，節點 0 不會處理任何資料。大約 5 分鐘的時間過後，節點會突然付諸行動，而使網路、磁碟和 CPU 活動突然增加。這很可能是叢集將分區重新分配於節點所致。節點 0 則會顯示正常活動。
  
## 輪流更新：結果

<!-- TODO; reformat this pdf for display inline -->

在 [ElasticsearchRecoveryScenario4.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario4.pdf) 檔案中，這項測試的結果顯示每個節點如何離線而後接連著再次上線。每個節點會在重新啟動前關閉 5 分鐘，屆時序列中的下一個節點會停止。

請注意下列幾點：

- 雖然會循環使用每個節點，但從輸送量和回應時間的角度來看，效能仍然適切平均。

- 在重新上線後，每個節點的磁碟活動會增加一小段時間。這很有可能是因為復原程序向前滾進在節點關閉時所發生的任何變更。

- 當節點離線時，網路活動尖峰會發生在其餘的節點。重新啟動節點時，也會發生尖峰。

- 回收最後一個節點之後，系統就會進入大幅變動的期間。這很有可能是因為復原程序必須同步處理每個節點間的變更，並確保所有複本及其對應的分區一致。在某個時間點，這麼做會導致連續大量插入作業逾時並且失敗。每個案例所回報的錯誤：

```
Failure -- BulkDataInsertTest17(org.apache.jmeter.protocol.java.sampler.JUnitSampler$AnnotatedTestCase): java.lang.AssertionError: failure in bulk execution:
[1]: index [systwo], type [logs], id [AVEg0JwjRKxX_sVoNrte], message [UnavailableShardsException[[systwo][2] Primary shard is not active or isn't assigned to a known node. Timeout: [1m], request: org.elasticsearch.action.bulk.BulkShardRequest@787cc3cd]]

```

後續實驗顯示在循環使用每個節點之間加入幾分鐘的延遲會排除此錯誤，所以很有可能是由復原程序嘗試同時還原數個節點與大量插入作業嘗試儲存數千份新文件之間的爭用情況所造成。


## 摘要

所執行的測試指出：

- Elasticsearch 非常容易從叢集中可能發生的大部分失敗模式復原。

- 如果設計良好的叢集在節點上遭遇資料嚴重遺失，Elasticsearch 可以快速復原。如果您設定 Elasticsearch 要將資料儲存到暫時儲存體，而且節點在重新啟動後接著重新佈建，可能會發生這種情形。這些結果顯示，即使在此情況下，此類儲存體所提供的效能優勢，最有可能超乎使用暫時儲存體的風險。

- 在前三個案例中，當節點離線並復原時，並行大量插入、彙總和篩選查詢工作負載並未發生錯誤。

- 只有案例 4 指出可能遺失資料，而此遺失只會影響所加入的新資料。在執行資料擷取的應用程式中，最好能重試失敗的插入作業以降低這個可能性，因為回報的錯誤類型非常有可能是暫時性錯誤。

- 測試 4 的結果也顯示，當您在叢集中執行計劃性節點維護時，如果在循環使用某個節點與下一個節點之間容許數分鐘的時間，將會提升效能。在非計劃的情況下 (例如資料中心在執行作業系統更新後回收節點)，您較難掌控如何及何時將節點關閉並重新啟動。Elasticsearch 嘗試在連續節點中斷後復原叢集狀態時所引發的爭用情況，會導致逾時和錯誤。

[管理虛擬機器的可用性]: ../articles/virtual-machines/virtual-machines-manage-availability.md
[執行自動化 Elasticsearch 復原測試]: guidance-elasticsearch-running-automated-resilience-tests.md

<!---HONumber=AcomDC_0224_2016 for CC training test -->
<!---HONumber=AcomDC_0224_2016 for CC training test: reject -->
