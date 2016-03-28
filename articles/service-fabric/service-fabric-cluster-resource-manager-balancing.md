<properties
   pageTitle="使用 Azure Service Fabric 叢集資源管理員平衡叢集 | Microsoft Azure"
   description="使用 Azure Service Fabric 叢集資源管理員平衡叢集的簡介。"
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

# 平衡 Service Fabric 叢集
Service Fabric 叢集資源管理員允許報告動態負載、對叢集中的變更做出反應，以及產生平衡計劃，但何時執行這些作業呢？ 如果服務在建立時會根據其預設負載值放置，什麼在叢集中真正觸發重新平衡？ 有數個與此相關的控制項。

第一組控制項控管一組計時器的平衡，而這些計時器控管叢集資源管理員針對需要處理的事項來檢查叢集狀態的頻率。這些計時器與其一直執行之工作的不同工作階段相關。它們是：

1.	放置 - 這個階段負責安置任何遺漏的具狀態複本或無狀態執行個體。這涵蓋兩項新服務，可處理已失敗且需要重建的複本或執行個體。刪除與捨棄複本或執行個體也在這裡處理。
2.	條件約束檢查 – 這個階段會檢查並更正系統內不同放置條件約束 (規則) 的違規情形。規則範例包括確保節點不會超出容量，以及符合服務的放置條件約束 (稍後會詳細說明)。
3.	平衡 - 這個階段會根據為不同度量設定的所需平衡層級查看是否需要主動式重新平衡，而如果需要，則會嘗試尋找叢集中更平衡的排列方式。

## 設定叢集資源管理員的步驟和計時器
上述每個階段是由控管頻率的不同計時器所控制。比方說，如果您只想處理每小時在叢集中放置新的服務工作負載 (以進行分批處理)，但又想要每隔幾秒鐘定期平衡檢查，您可以強制這麼做。當每個計時器啟動時，就會設定旗標，表示我們需要處理資源管理員該部份的職責，而且會在下一個整體掃掠中透過狀態機器進行挑選 (這就是這些設定定義為「最小間隔」的原因)。根據預設，資源管理員會每 1/10 秒掃描其狀態並套用更新、每秒設定放置和條件約束檢查旗標，而每 5 秒設定平衡旗標。

ClusterManifest.xml：

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

今天我們一次只會依序執行其中一個動作。比方說，我們已經回應任何要求，已在繼續平衡叢集之前建立新的複本。如您所見，我們可以依照指定的預設時間間隔，掃描並檢查我們需要非常頻繁執行的任何事物，這表示每一組變更通常比較小 (我們不是掃描叢集中數小時的變更並嘗試一次更正全部，我們可以在事情發生時有效地進行處理)。這會使 Service Fabric 資源管理員對於叢集中發生的事情非常有反應。

基本上，叢集資源管理員也必須知道何時考慮不平衡的叢集，以及應該移動哪些複本才能修復事物。因此，我們有其他兩個主要的設定︰平衡臨界值和活動臨界值。

## 平衡臨界值
平衡臨界值是觸發主動式重新平衡的主要控制項。平衡臨界值定義特定度量的叢集有多不平衡，資源管理員才會將它視為不平衡並在下一次執行期間觸發平衡作業。平衡臨界值會根據每個度量定義為叢集資訊清單的一部分︰

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

度量的平衡臨界值是一個比率。如果負載最多之節點的負載量除以負載最少之節點的負載量超過此比率，此叢集則會被視為不平衡並在下一次執行資源管理員的狀態節點期間觸發平衡作業。

![平衡臨界值範例][Image1]

在此簡單的範例中，每個服務只取用一單位的度量。在最上面的範例中，節點的負載上限為 5，而下限為 2。假設此度量的平衡臨界值為 3。因此，在最上面的範例中，叢集會被視為平衡，而且不會觸發平衡作業。在最下面的範例中，節點的負載上限為 10，而下限為 2，所以超過設計的平衡臨界值 3。因此，一旦有機會執行資源管理員，負載幾乎肯定會分散到 Node3。請注意，我們不會使用其他人也可能加諸於 Node2 的窮盡方法，因為這會導致節點之間的整體差異縮減到最小。

![平衡臨界值範例動作][Image2]

請注意，低於平衡臨界值不是明確的目標 - 平衡臨界值只是觸發因子而已。

## 活動臨界值
有時候，雖然節點相當不平衡，但叢集中的負載總量很低。這可能只是因為當天時間，或是因為叢集是新的而且才剛開始啟動而已。在任何一種情況下，您可能不想花時間進行平衡，因為實際的收穫很少 – 您將只會將網路和計算資源用來四處移動項目。資源管理員內有另一個稱為活動臨界值的控制項，可讓您指定活動的絕對下限 – 如果沒有任何節點有至少這麼多的負載，則即使達到平衡臨界值，也不會觸發平衡作業。舉例來說，假設在我們的報告中，這些節點的使用量總數如下所示。同時假設我們讓平衡臨界值保持 3，但我們現在也有活動臨界值 1536。在第一個案例中，根據平衡臨界值，叢集為不平衡狀態，沒有任何節點符合活動臨界值下限，所以我們就順其自然發展。在最下面的範例中，Node1 超過活動臨界值，所以會執行平衡作業。

![活動臨界值範例][Image3]

如同平衡臨界值，活動臨界值會透過叢集資訊清單根據每個度量進行定義︰

``` xml
      <Section Name="MetricActivityThresholds">
        <Parameter Name="Memory" Value="1536"/>
      </Section>
```

## 一起平衡服務
值得一提的是，叢集是否不平衡是整個叢集的決策，但我們解決此種情況的方法是移動個別的服務複本和執行個體。這很合理，對吧？ 如果記憶體堆疊於某一個節點上，可能是由多個複本或執行個體所造成，所以需要移動所有使用受影響、不平衡度量的複本或執行個體。

雖然有時候客戶會呼叫我們或提出票證，表示已移動不平衡的服務。在有其他不平衡情形的時候，如果該服務的所有度量都已達到平衡 (即使全然如此)，怎麼會發生這種情形？ 我們一起看看！

以 S1、S2、S3 和 S4 這四個服務為例。S1 報告度量 M1 和 M2，S2 報告 M2 和 M3，S3 報告 M3 和 M4，而 S4 報告度量 M99。您當然可以看到我們在此要做什麼。我們有一連串的事要做！ 就資源管理員的觀點而言，我們的確沒有 4 個獨立的服務，但我們有一串相關的服務 (S1、 S2 和 S3) 以及另一個獨自遠離的服務。

![一起平衡服務][Image4]

因此，Metric1 不失衡可能會導致屬於 Service3 的複本或執行個體四處移動。這些移動通常很有限，但是根據 Metric1 究竟有多不平衡以及必須在叢集中進行哪些變更才能進行更正，移動幅度可能會變大。我們還可以肯定地說度量 1、2 或 3 不平衡絕不會導致 Service4 移動 – 沒有必要，因為四處移動屬於 Service4 的複本或執行個體完全不會影響度量 1、2 或 3 的平衡。

資源管理員會在每次執行時自動找出相關的服務，因為服務可能會新增、移除或有其度量設定變更 – 例如，在兩回平衡作業之間，Service2 可能已重設要移除 Metric2。這會中斷 Service1 和 Service2 之間的鏈結。您現在有三個服務群組 (而非兩個)︰

![一起平衡服務][Image5]

## 後續步驟
- 度量是 Service Fabric 叢集資源管理員管理叢集中的耗用量和容量的方式。若要深入了解度量及其設定方式，請查看[這篇文章](service-fabric-cluster-resource-manager-metrics.md)
- 移動成本是向叢集資源管理員發出訊號，表示移動某些服務會比較貴的其中一種方式。若要深入了解移動成本，請參閱[這篇文章](service-fabric-cluster-resource-manager-movement-cost.md)
- 叢集資源管理員有數個為減緩叢集的流失而可以設定的節流。這些節流通常不是必要的，但若有需要，您可以參閱[這裡](service-fabric-cluster-resource-manager-advanced-throttling.md)


[Image1]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

<!---HONumber=AcomDC_0316_2016-->