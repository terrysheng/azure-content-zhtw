<properties
   pageTitle="資源平衡器叢集描述 | Microsoft Azure"
   description="將容錯網域、升級網域、節點屬性和節點容量指定給資源平衡器以描述 Service Fabric。"
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
   ms.date="03/03/2016"
   ms.author="masnider"/>

# 描述 Service Fabric 叢集

Service Fabric 叢集資源管理員提供數種機制，來描述叢集。在執行階段，資源管理員會使用此資訊以確保叢集中執行之服務的高可用性，同時也確保適當地使用叢集中的資源。

## 重要概念
描述叢集的叢集資源管理員功能包括︰
- 容錯網域
- 升級網域
- 節點屬性
- 節點容量

### 容錯網域

容錯網域是協調失敗的任何區域。單一機器是容錯網域 (因為當機有很多個不同的理由，從電源供應器故障到 NIC 韌體不正確的磁碟機失敗)。連接到相同乙太網路交換器的許多機器會位於相同的容錯網域，連接到單一電力來源的機器也是如此。

如果您已設定您自己的叢集，您需要考慮失敗的所有不同區域，以確保您的容錯網域已正確設定，讓 Service Fabric 知道可以安全放置服務的位置。我們所謂的「安全」實際上是指智慧 – 我們不想要將服務放置在會造成服務當機的容錯網域。在 Azure 環境中，我們運用 Azure 網狀架構控制器/資源管理員所提供的容錯網域資訊，以正確地代表您設定叢集中的節點。在下圖 (圖 7) 中，我們會為所有實體上色，讓容錯網域成為簡單的範例，並且列出產生的所有不同的容錯網域。在此範例中，我們有資料中心 (DC)、機架 (R) 和刀鋒視窗 (B)。如果每個刀鋒視窗包含一個以上的虛擬機器，容錯網域階層上可能有另一個層級。

![透過容錯網域組織的節點][Image1]

 在執行階段期間，Service Fabric 叢集資源管理員會考慮叢集中的容錯網域，並嘗試針對指定的服務分散複本，以免將它們全都放在不同的容錯網域中。在任何一個容錯網域發生錯誤時，此程序有助確保該服務的可用性沒有遭到破壞。

 Service Fabric 的叢集資源管理員不在意階層中有多少層級，不過因為它會嘗試確保遺失階層的任何一部分並不會影響在其上執行的叢集或服務，通常最好是在容錯網域的每一個深度層級有相同的機器數目。這可防止階層的某一個部分在一天的結尾包含比其他部分還要多的服務。

 以容錯網域的「樹系」不平衡這樣的方式設定您的叢集，會讓資源管理員相當難以釐清最佳的複本配置，特別是因為遺失特定網域會嚴重影響叢集的可用性 – 資源管理員在於該「繁重」網域有效使用機器與放置服務以讓網域遺失不會造成問題之間左右為難。

 在下圖中我們設定兩個不同的叢集，其中一個叢集的節點完善地分散到容錯網域，而另一個叢集中其中一個容錯網域會有多出許多的節點。請注意，在 Azure 中會為您處理哪個節點位於哪個容錯和升級網域的選擇，因此您應該不會看見這類失衡。不過，如果您曾經在內部部署或另一個環境中維護您自己的叢集，您需要考慮一些事項。

 ![兩個不同的叢集配置][Image2]

### 升級網域

升級網域是另一項功能，可協助 Service Fabric 資源管理員了解叢集的版面配置，讓它可以針對失敗事先計劃。升級網域會定義區域 (實際上是節點集)，在升級期間會在相同的時間停機。

升級網域非常類似容錯網域，但是有幾個主要的差異。首先，升級網域通常會定義原則；而容錯網域是由協調失敗區域嚴格定義 (因此通常是環境的硬體配置)。但是，在升級網域的情況下，您必須決定您要的數量。另一個差異是 (截至目前為止)，升級網域不是階層式，它們較像是簡單的標記。

下圖顯示虛構的設定，其中有等量分散在三個容錯網域的三個升級網域。它也會示範一個可能的情況，具狀態服務的三個不同複本。請注意，它們全部都在不同的容錯和升級網域。這表示我們可能會在服務升級途中遺失容錯網域，而且叢集中仍然有一個程式碼和資料的執行中複本。根據您的需求，這樣可能已經足夠，不過您可能會發現這個複本是舊的 (因為 Service Fabric 使用以仲裁為基礎的複寫）。若要真正突破兩個失敗，您需要多個複本 (最少五個)。

![放置在容錯和升級網域][Image3]

擁有大量升級網域有利有弊 – 優點是升級的每個步驟更細微，因此會影響較少的節點或服務。這會導致每次需要移動的服務較少，讓系統的流失較少，並且整體改善可靠性 (因為受到任何問題所影響的服務較少)。擁有許多升級網域的缺點是，Service Fabric 會在升級網域升級時確認每個升級網域的健康狀態，並且確保升級網域狀況良好，再進行至下一個升級網域。這項檢查的目的是確保服務可以穩定，其健康狀態會在繼續進行升級之前進行驗證，以便偵測任何問題。缺點是可接受的，因為它會防止不良的變更一次影響到太多服務。

太少升級網域也有它的副作用，當每個個別升級網域關閉並且進行升級時，您的大部分整體容量無法使用。例如，如果您只有三個升級網域，您一次只能採用整體服務或叢集容量的 1/3。這並非您想要的結果，因為您必須在其餘的叢集中擁有足夠的容量以涵蓋工作負載，這表示正常的情況下這些節點比起增加 COGS 時負載較少。

環境中容錯或升級網域的總數沒有實際限制，對於它們如何重疊也沒有條件約束。我們看到的一般結構是 1:1 (其中每個唯一的容錯網域對應至其本身的升級網域)、每個節點 (實體或虛擬作業系統執行個體) 一個升級網域，形成具有機器的矩陣的容錯網域和升級網域中的「等量」或「矩陣」模型通常會在對角線下。

![容錯和升級網域配置][Image4]

對於選擇哪個配置並沒有最佳答案，每個答案都各有優缺點。例如，1FD:1UD 模型相當容易設定，而每個節點 1 UD 模型是最接近以前用來管理少量機器的模型 (其中每個機器會獨立卸下)。

最常見的模型 (我們用於裝載 Azure Service Fabric 叢集的模型) 是 FD/UD 矩陣，其中 FD 和 UD 會形成資料表，且節點是沿著對角線啟動。最後是疏鬆或壓縮取決於相較於 FD 和 UD 數目的節點總數 (針對大型叢集以不同的方式放置，幾乎所有項目最終看起來就像是密集矩陣模式，如 [圖 10] 的右下角選項所示)。

## 設定容錯和升級網域
定義容錯網域和升級網域是在 Azure 託管的 Service Fabric 部署中自動完成；Service Fabric 只會從 Azure 拾取環境資訊。接著您的使用者可以選擇您想要的網域數目。在 Azure 中容錯和升級網域資訊看起來是「單一層級」，但是實際上是從 Azure Stack 的較低層級封裝資訊，並且僅從使用者的角度顯示邏輯容錯和升級網域。

如果您維護自己的叢集 (或只是想要在您的開發機器上嘗試執行特定拓撲)，必須自行提供容錯網域和升級網域資訊。在此範例中，我們會定義一個 9 節點的叢集，跨越三個「資料中心」(各有三個機架)，並且跨這三個資料中心等量分散三個升級網域。在您的叢集資訊清單中，看起來如下︰

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```
> [AZURE.NOTE] 在 Azure 部署中，容錯網域和升級網域是由 Azure 指派。因此，您適用的節點與 Azure 基礎結構選項中的角色定義不包含容錯網域或升級網域資訊。

## 放置條件約束和節點屬性
有時候 (事實上是大部分的情況下) 您想要確保只在特定節點或一組特定的節點上執行某些工作負載。例如，某些工作負載可能需要 GPU 或 SSD，而有些則不用。絕佳的範例就是幾乎每個多層式架構，其中特定機器做為應用程式的前端/介面服務端，而不同的集合 (通常是使用不同的硬體資源) 會處理計算或儲存體層級的工作。Service Fabric 預期甚至在微服務的世界中有特定工作負載，需要在特定硬體組態上執行，例如︰

- 現有的多層式架構應用程式已「提升並移轉」到 Service Fabric 環境
- 針對效能、級別或安全性隔離原因而想要在特定硬體上執行的工作負載
-	針對原則或資源耗用量原因而需要從其他工作負載隔離的工作負載

為了支援這種組態，Service Fabric 有我們稱為放置條件約束的第一級概念。條件約束可以用於指出應在何處執行特定服務。條件約束的集合可由使用者延伸，表示人員可以使用自訂屬性標記節點，然後也針對這些項目進行選取。

![叢集配置不同的工作負載][Image5]

在節點上的不同索引鍵/值標記即為節點放置屬性 (或僅稱為節點屬性)，而在服務的陳述式稱為放置條件約束。節點屬性中指定的值可以是字串、bool，或帶正負號長值。條件約束可以是在叢集中不同節點屬性上運作的布林陳述式，而只有其中陳述式評估為「True」的節點可以在上面放置服務。未定義屬性的節點不符合包含該屬性的任何放置條件約束。Service Fabric 也會定義一些預設屬性，可以自動使用，不需要使用者加以定義。有鑑於此，在每個節點撰寫定義的預設屬性是 NodeType 和 NodeName。通常我們發現 NodeType 是其中一個最常使用的屬性，因為它通常與機器的類型對應為 1:1，這會對應至傳統的多層式架構應用程式架構的工作負載類型。

![放置條件約束和節點屬性][Image6]

假設下列節點屬性是針對指定節點類型定義︰ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasDisk" Value="true"/>
        <Property Name="Value" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

您可以針對服務建立服務放置條件約束，如下所示︰

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasDisk == true && Value >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasDisk == true && Value >= 4"
```

如果您確定 NodeType01 的所有節點都有效，您也可以只選取該節點類型，使用如上圖所示的放置條件約束。

服務放置條件約束其中很棒的一點是它們可以在執行階段動態更新。所以如果您需要，可以在叢集中移動服務、加入和移除需求等等。Service Fabric 會負責確保服務保持執行且可用，即使這類變更持續進行。

C#：

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell：

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

放置條件約束 (以及我們即將討論的許多其他屬性) 是針對每個不同的服務執行個體指定。更新一律會取代 (覆寫) 先前指定的項目。

### 容量
任何 Orchestrator 的其中一個最重要的作業是協助管理叢集中的資源耗用量。如果您想要有效率地執行服務，最不想遇到的情形是許多節點是熱的 (導致資源爭用和效能不佳)，而其他是冷的 (浪費資源)。但是試著想想比平衡還要基本的事情 (在一分鐘內) – 只要確保節點不會在第一時間執行用完資源？

結果 Service Fabric 會以稱為「度量」的項目來代表資源。度量是您想要向 Service Fabric 描述的任何邏輯或實體資源。度量的範例是例如「WorkQueueDepth」或「MemoryInMb」的項目。度量不同於條件約束和節點屬性，在該節點屬性中節點屬性通常是節點本身的靜態描述項，而度量是當它們在節點上執行時，服務使用的實體資源。因此屬性看起來像是 HasSSD，可能會設定為 true 或 false，但是該 SSD 上的可用空間數量 (和服務的使用量) 就是類似「DriveSpaceInMb」的度量。節點上的容量會將「DriveSpaceInMb」設定為磁碟機的非保留總空間量的數量，服務會報告在執行階段使用多少度量。

如果您關閉所有資源平衡，Service Fabric 的資源管理員仍能確保最終沒有節點超出其容量 (除非叢集整體已滿)。容量是 Service Fabric 用來了解節點有多少資源的機制，我們從其中減去不同服務的耗用量 (稍後會詳細說明)，以便知道還剩多少。服務層級的容量和耗用量均以度量來表示。

在執行階段，資源管理員會追蹤每個節點上的每個資源有多少 (依據其容量定義)，以及剩餘多少 (從每個服務減去任何宣告的使用量)。利用此資訊，Service Fabric 資源管理員可找出要放置或移動複本的位置，讓節點不會超過容量。

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "Memory";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 1024;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,1024,1024”)
```

![叢集節點和容量][Image7]

您可以在叢集資訊清單中看到這些項目︰

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="Memory" Value="10"/>
        <Capacity Name="Disk" Value="10000"/>
      </Capacities>
    </NodeType>
```

此外，服務的負載也會動態變更。在此情況下，目前放置複本或執行個體的位置可能會變成無效，因為該節點上所有複本和執行個體的結合使用量超出該節點的容量。我們稍後會更詳細討論負載可以動態變更的案例，但是只要還有容量，就會以相同的方式處理 - Service Fabric 資源管理會自動執行，並取回低於容量的節點，方法是將該節點上的一或多個複本或執行個體移至不同的節點。執行這項操作時，資源管理員會嘗試將所有移動的成本降到最低 (我們稍後再回頭討論「成本」的概念)。

###叢集容量

那麼，我們要如何防止整體叢集太滿？ 使用動態負載，實際上我們並沒有太多可以執行的作業 (因為服務有自己的負載尖峰，獨立於資源管理員所執行的動作 – 今天您的叢集具有許多空餘空間，但是明天當您成名之後空間就會不足)，但是有一些內建控制項可以防止基本錯誤。我們可以做的第一件事是防止建立新的工作負載，該工作負載會導致叢集空間變滿。

假設您要建立簡單的無狀態服務，而且它具有某些與其相關聯的負載 (比預設值還多且動態負載稍後才報告)。對於此服務，讓我們假設它關心某些資源 (例如磁碟空間)，依預設它會針對服務的每個執行個體使用 5 個單位的磁碟空間。您想要建立服務的 3 個執行個體。太棒了！ 因此表示我們需要叢集中有 15 個單位的磁碟空間，才能建立這些服務執行個體。Service Fabric 持續計算整體容量和每個度量的耗用量，因此我們可以輕鬆地進行決定並且在空間不足時拒絕建立服務呼叫。

請注意，由於需求僅是 15 個可用單位，此空間可以使用不同的方式配置；例如，可能是在 15 個不同節點上各一個剩餘單位，或是在 5 個不同節點上各三個剩餘單位等等。如果三個不同節點上沒有足夠的容量，Service Fabric 將會重新組織已在叢集中的服務，以便在三個必要的節點上挪出空間。這類的重新排列幾乎一律可行，除非整個叢集幾乎已滿。

我們為了協助使用者管理整體叢集容量所做的另一件事是，對每個節點的指定容量加入一些保留緩衝區的概念。此設定是選擇性的，但是可以讓使用者保留整體節點容量的某些部分，讓它只會用來在升級和失敗期間放置服務 - 叢集的容量降低的情況。現在緩衝區是透過 ClusterManifest 針對所有節點全域指定。您針對保留容量挑選的值會是您的服務更受到條件約束的資源的函數，以及您在叢集中具有的容錯和升級網域的數目。通常較多的容錯和升級網域表示您可以挑選較少數目的緩衝處理容量，因為您會預期在升級和失敗期間有較少量的叢集無法使用。請注意，指定緩衝區百分比只有在您也指定了度量的節點容量時才具有意義。

ClusterManifest.xml

```xml
        <Section Name=" NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="10" />
            <Parameter Name="Memory" Value="15" />
            <Parameter Name="SomeOtherMetric" Value="20" />
        </Section>
```
建立呼叫會在叢集耗盡緩衝處理容量時導致新的服務失敗，確保叢集會保留足夠的備用額外負荷，讓升級和失敗不會造成節點實際超過容量。資源管理員透過 PowerShell 和查詢 API 來公開許多此類資訊，讓您看見緩衝處理容量設定、總容量及每個指定度量的目前耗用量。我們可以看到該輸出的範例如下︰

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2015 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2015 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
- [深入了解叢集資源管理員架構](service-fabric-cluster-resource-manager-architecture.md)
- [深入了解重組度量](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- [取得 Service Fabric 叢集資源管理員的簡介](service-fabric-cluster-resource-manager-introduction.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

<!---HONumber=AcomDC_0309_2016-->