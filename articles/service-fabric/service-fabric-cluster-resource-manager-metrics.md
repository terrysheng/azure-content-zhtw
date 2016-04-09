<properties
   pageTitle="使用 Azure Service Fabric 叢集資源管理員管理度量 | Microsoft Azure"
   description="深入了解如何在 Service Fabric 中設定及使用度量。"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/10/2016"
   ms.author="masnider"/>

# 在 Service Fabric 中使用度量管理資源耗用量和負載
度量是 Service Fabric 中您的服務所關切的資源的通用詞彙。一般而言，度量就是任何您想要從資源觀點進行管理，以便處理您的服務效能的項目。

在上述所有範例中，我們一直以隱含方式提到度量；諸如記憶體、磁碟、CPU 使用率 – 這些全部都是度量的範例。這些是實體度量，也就是對應至節點上需要管理之實體資源的資源。度量也可以是邏輯度量，例如應用程式定義的 “MyWorkQueueDepth”，其對應於某個層級的資源耗用量 (但應用程式並沒有真正了解或不知道如何測量它）。

## 預設度量
假設您只想要開始使用，但不知道您即將取用哪些資源，或甚至哪些資源對您很重要。所以您進行實作，然後建立您的服務，但未指定任何度量。沒關係！ 我們將為您挑選一些度量。如果您未指定任何自己的度量，我們今天為您使用的預設度量就稱為 PrimaryCount、ReplicaCount 和 (我們知道有點模糊) Count。下表顯示我們預設追蹤的這些度量各有多少負載︰

| 度量 | 無狀態執行個體負載 |	具狀態次要負載 |	具狀態主要負載 |
|--------|--------------------------|-------------------------|-----------------------|
| PrimaryCount | 0 |	0 |	1 |
| ReplicaCount | 0 | 1 | 1 |
| Count |	1 |	1 |	1 |

好了，透過這些預設度量，您得到什麼？ 結果就是，對於基本工作負載，您得到相當不錯的工作分配。在下列範例中，我們來看看當我們建立一個包含三個資料分割且目標複本集大小為 3 的具狀態服務，以及一個執行個體計數為 3 的無狀態服務時，會發生什麼狀況 - 您會得到如下的內容！

![使用預設度量的叢集配置][Image1]

在此範例中，我們看到：
-	具狀態服務的主要複本並未堆疊於單一節點上
-	相同資料分割的複本不在相同節點上
-	主要複本與次要複本的總數均勻地分佈於叢集中
-	服務物件 (無狀態與具狀態) 的總數平均配置於每個節點

相當不錯！

在您開始思考下面問題之前，這是很適合的方法︰不同服務的所有主要複本竟然立即展現相同的負載，真正的可能性為何？ 此外，指定服務的負載在一段時間內保持不變的機率為何？ 結果是，對任何嚴重的工作負載而言，可能性實際上很低。

實際上，您絕對可以搭配預設度量執行，或至少搭配靜態自訂度量執行，但這麼做通常意味著您的叢集使用率低於您的期望 (因為報告不具調適性)；在最糟的情況下，也可能導致過度排程的節點，因而造成效能問題。使用自訂度量和動態負載報告，我們可以處理得比較好。我們一起來看看後續的章節 – 自訂度量和動態負載。

## 自訂度量
我們已經討論過可以有實體和邏輯度量，而且使用者可以定義自己的度量。要如何處理這些度量？ 真的很簡單！ 只要在建立服務時設定度量和預設初始負載，就大功告成！ 當您建立服務時，可以為每個服務執行個體設定任何一組度量和預設值。

請注意，當您開始定義自訂度量時，如果您希望我們也將預設度量用於平衡您的服務，則必須明確地加回預設度量。這是因為我們希望您能清楚了解預設度量與自訂度量之間的關聯性 – 您或許比較在意記憶體耗用量 (相較於主要分配)，或者您也許有數個想要與預設度量「混合」的度量。

假設您想要設定一項服務，該服務會報告名為「記憶體」的度量 (除了預設度量以外)。對於記憶體，我們假設您已完成一些基本測量，並且知道該服務的主要複本通常佔用 20Mb 的記憶體，而該相同服務的次要複本則佔用 5Mb。您知道就管理這項特定服務的效能而論，記憶體是最重要的度量，但您仍希望主要複本達到平衡，某個節點或容錯網域的遺失才不會取用過多的主要複本。除此之外，您將採用預設值。

以下是您要執行的動作：

程式碼：

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription memoryMetric = new ServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.PrimaryDefaultLoad = 20;
memoryMetric.SecondaryDefaultLoad = 5;
memoryMetric.Weight = ServiceLoadMetricWeight.High;

ServiceLoadMetricDescription primaryCountMetric = new ServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

ServiceLoadMetricDescription replicaCountMetric = new ServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

ServiceLoadMetricDescription totalCountMetric = new ServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

(提醒您，如果您只想要使用預設度量，您就完全不必碰觸度量集合。）

我們已示範如何定義您自己的度量，接著來討論度量可具有的各種屬性。我們已向您展示過這些屬性，現在讓我們來討論它們的實質意義！ 一個度量目前可有四個不同的屬性

-	度量名稱︰這是度量的名稱。從資源管理員的觀點來看，這是叢集內度量的唯一識別碼。
-	PrimaryDefaultLoad：此服務將為此度量 (當作主要度量) 行使的預設負載量
-	SecondaryDefaultLoad：此服務將為此度量 (當作次要度量) 行使的預設負載量
-	權數︰這是度量對於此服務的重要程度 (相對於其他設定的度量)。

## 負載
負載是特定節點上的某個服務執行個體或複本取用多少特定度量的一般概念。

## 預設負載
預設負載是資源管理員從實際的服務執行個體或複本收到任何更新之前，應假設每個服務執行個體或複本將取用多少負載。對於較簡單的服務，這就是永遠不會動態更新的靜態定義，因此將用於服務的存留期。這很適合用於簡單的容量規劃，因為這正好是我們習慣的做法 – 將某些資源投注於某些工作負載，好處是至少我們現在是以微服務的心態運作，其中的資源實際上不會靜態指派給特定工作負載，而其中的人員也不在决策圈內。

我們允許具狀態服務指定其主要複本和次要複本的預設負載 – 實際上對許多服務而言，這些數字都不同，因為主要複本和次要複本會執行不同的工作負載，而且主要複本通常可用於讀取和寫入 (以及大部分的運算負荷)，而主要複本的預設負載會大於次要複本。

現在假設您已執行您的服務一段時間，而您已注意到服務的某些執行個體或複本耗用比其他執行個體或複本更多的資源，或其耗用量會隨時間改變 – 或它們許與特定客戶相關聯，或許只是對應至會在一天內產生變化的工作負載，例如傳訊流量或股票交易。無論如何，您會注意到，若不依照一個有意義的數量進行區隔，您就沒有「單一數字」可用於此負載。您也注意到，在初始估計值中「進行區隔」會導致 Service Fabric 將過多或過少的資源配置給您的服務，以致您節點的使用量過高或過低。怎麼辦？ 您的服務可能正在報告負載！

## 動態負載
動態負載報告可讓複本或執行個體在其存留期中調整其在叢集中的配置/報告的度量使用量。閒置且未執行任何工作的服務複本或執行個體通常會回報它使用少量的資源，而忙碌的複本或執行個體則會回報他們使用較多的資源。叢集中的這個一般流失層級可讓我們迅速重新組織叢集中的服務複本和執行個體，以確保服務和執行個體取得其所需的資源 – 實際上，忙碌的服務能夠從目前閒置或執行較少工作的其他複本或執行個體回收資源。透過 ReportLoad 方法 (可在 ServicePartition 上取得並可做為基底 StatefulService 的屬性使用) 可以迅速完成負載報告。在您的服務中，程式碼如下所示︰

程式碼：

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("Memory", 1234), new LoadMetric("Foo", 42) });
```

服務複本或執行個體只能針對它們設定要使用的度量報告負載。建立每個服務時會設定度量清單。如果服務複本或執行個體嘗試針對目前未設定要使用的度量報告負載，Service Fabric 會記錄此報告但予以忽略，這表示我們在計算或報告叢集的狀態時不會使用該度量。這很完美，因為它能夠進行更大規模的實驗 – 程式碼可以測量並報告它知道作法的所有項目，而運算子可以迅速設定、調整及更新該服務的資源平衡規則，但不必變更程式碼。比方說，這可能包括停用有錯誤報告的度量、根據行為重新設定度量的權數，或僅在部署及驗證程式碼之後啟用新的度量。

## 混用預設負載值和動態負載報告
為即將以動態方式報告負載的服務指定預設負載，是否合理？ 當然！ 在此情況下，預設負載可做為估計值，直到真正的報告開始從實際的服務複本或執行個體顯示為止。這是適合，因為它可讓資源管理員在建立複本或執行個體時進行放置 – 預設負載最後成為初始估計值，讓資源管理員一開始就將服務執行個體或複本放在適當的位置；如果未提供任何資訊，放置方式實際上是隨機的，而我們幾乎肯定必須在真正的負載報告開始出現時移動項目。

所以讓我們採用先前的範例，看看當我們加入一些自訂負載時會發生什麼狀況，然後在建立服務之後，它會以動態方式更新。在此範例中，我們將以「記憶體」為例，並假設我們一開始使用下列命令建立具狀態服務︰

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

我們之前討論過此語法 (MetricName、MetricWeight、PrimaryDefaultLoad、SecondaryDefaultLoad)，但我們稍後會詳細討論權數的特定值有何意義。

讓我們看看可能的叢集配置看起來如何︰

![使用預設和自訂度量平衡的叢集][Image2]

有幾件事值得一提：

-	由於複本或執行個體在報告自己的負載前，會使用服務的預設負載，我們知道具狀態服務的資料分割 1 內的複本尚未自行報告負載
-	資料分割內的次要複本可以有自己的負載
-	整體度量看起來很不錯，節點上最大和最小負載之間的差異 (對於記憶體 – 我們說過是最在意的自訂度量) 比率只有 1.75 (具有最多記憶體負載的節點是 N3，最少是 N2，而 28/16 = 1.75) – 相當平衡！

我們仍必須說明下列事項：

-	何者決定 1.75 的比率是否合理？ 我們如何知道這是否夠好，或者還需要再做些什麼？
-	何時發生平衡？
-	記憶體的權數「很高」是什麼意思？

## 度量權數
度量權數允許兩個不同的服務報告相同的度量，但不得以不同方式看待平衡該度量的重要性。例如，請考慮記憶體中分析引擎和持續性資料庫；兩者可能都很在意「記憶體」度量，但記憶體中服務可能不太在意「磁碟」度量 – 它可能會少量使用，但並非服務效能的關鍵。能夠跨越不同的服務來追蹤相同的度量真的很棒，因為此功能可讓資源管理員追蹤叢集中的實際耗用量，確保節點不會超過容量等。

度量權數可讓資源管理員在沒有完美解答時，真正決定如何平衡叢集 (這是很多時間)。度量可以有四個不同的權數層級︰零、低、中和高。在考慮是否平衡時，權數為「零」的度量沒有任何貢獻，但其負載對於容量測量仍有所貢獻。

叢集中不同度量權數的實際影響是我們達到實質不同的服務排列方式，因為資源管理員被告知要確保某些度量比其他度量更加重要，因此如果這些度量與其他度量 (較低優先順序的度量) 發生衝突，則偏好使用這些度量。

讓我們看看簡單的負載報告範例，以及不同的度量權數如何在叢集中造成不同的配置。在此範例中，我們看到切換度量的相對權數會導致資源管理員藉由建立不同的服務排列，而偏好某些解決方案。

![度量權數範例及其對平衡解決方案的影響][Image3]

在此範例中，有四個不同的服務，全部都是報告兩個不同度量 A 和 B 的不同值。在所有服務定義的其中一個案例中，度量 A 最為重要 (權數 = 高) 而 MetricB 則相對不重要 (權數 = 低)，我們確實看到 Service Fabric 資源管理員放置這些服務，所以 MetricA 比 MetricB 更加平衡 (偏差較小)。在第二個案例中，我們反轉度量權數，我們看到資源管理員可能會交換服務 A 與 B，以便產生 MetricB 比 MetricA 更加平衡的配置。

### 全域度量權數
如果 ServiceA 將 MetricA 定義為最重要，而且 ServiceB 完全不在意 MetricA，則資源管理員最終使用的實際權數為何？

我們實際會為每個度量追蹤兩個權數 – 服務本身定義的權數，以及所有在意該度量的服務所適用的全域平均權數。我們會使用這兩個權數來計算我們所產生之解決方案的分數，因為務必要確保服務與它自己的優先順序達到平衡，且叢集整體上配置正確。

如果我們不在意全域和區域平衡，會發生什麼狀況？ 建構全域平衡的解決方案雖然微不足道，但這會導致個別服務的平衡和資源配置極為不佳。在下列範例中，請考慮用來設定具狀態服務的預設度量 PrimaryCount、ReplicaCount 和 TotalCount，並了解當我們只考慮全域平衡時會發生什麼狀況︰

![全域唯一解決方案的影響][Image4]

在最上面的範例中，我們只探討全域平衡，叢集整體上的確達到平衡 – 所有節點的主要複本和總複本的計數都相同，太棒了！ 各項達到平衡。不過，如果您查看此配置的實際影響，就不是那麼理想︰遺失任何節點會對特定工作負載帶來不成比例的影響，因為這會除取其所有的主要複本。舉例來說，如果我們遺失第一個節點時。如果發生這種情形，圓形服務的三個不同資料分割的三個主要複本會全部同時遺失。相反地，其他兩個服務 (三角形和六邊形) 的資料分割遺失一個複本，這不會導致中斷 (除了必須復原已關閉的複本以外)。

在最下方的範例中，我們根據全域和個別服務平衡來散發複本。在計算解決方案的分數時，我們會將大部分的權數提供給全域解決方案，但是 (可設定) 的部分會確保服務本身儘可能平衡。因此，如果我們遺失相同的第一個節點，我們會看到遺失的主要複本 (和次要複本) 分散於所有服務的所有資料分割，其對每個資料分割的影響都一樣。

將度量權數納入考量，全域平衡會根據度量權數的平均值計算。我們會平衡服務與其自己的已定義度量權數。

## 後續步驟
- 如需可用來設定服務的其他選項的詳細資訊，請查看[深入了解設定服務](service-fabric-cluster-resource-manager-configure-services.md)中提供的其他叢集資源管理員組態的相關主題
- 定義重組度量是合併 (而不是擴增) 節點上負載的一種方式。若要了解如何設定重組，請參閱[這篇文章](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- 若要了解叢集資源管理員如何管理並平衡叢集中的負載，請查看關於[平衡負載](service-fabric-cluster-resource-manager-balancing.md)的文章
- 從頭開始，並[取得 Service Fabric 叢集資源管理員的簡介](service-fabric-cluster-resource-manager-introduction.md)
- 移動成本是向叢集資源管理員發出訊號，表示移動某些服務會比較貴的其中一種方式。若要深入了解移動成本，請參閱[這篇文章](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]: ./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png

<!---HONumber=AcomDC_0316_2016-->