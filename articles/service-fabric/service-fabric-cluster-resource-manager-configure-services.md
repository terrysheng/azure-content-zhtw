<properties
   pageTitle="使用 Service Fabric 叢集資源管理員設定服務 | Microsoft Azure"
   description="藉由指定計量、放置條件約束及其他放置原則來描述 Service Fabric 服務。"
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


# 設定 Service Fabric 服務的叢集資源管理員設定
Service Fabric 叢集資源管理員讓您能夠非常細微地控制可管控每個個別具名服務的規則。每個服務執行個體都可以指定應如何在叢集中進行配置的特定規則，而且可定義一組它想要報告的計量，包括它們對於該服務的重要程度。設定服務通常會分成三個不同的工作︰

1. 設定放置條件約束
2. 設定計量
3. 設定進階放置規則 (較不常見)

讓我們依序討論每一個工作︰

## 放置條件約束
放置條件約束可用來控制服務實際可在叢集中的哪些節點上執行。通常您會看到特定的具名服務執行個體或受限於在特定類型節點上執行之類型的所有服務，但放置條件約束是可擴充的 - 您可以根據節點類型定義任意的屬性組合，接著在建立服務時，利用條件約束來選取它們。放置條件約束也會在服務存留時間內動態更新，讓您能夠回應叢集中的變更。

## 度量
計量是此服務應在其上取得平衡的資源清單，包括該服務的每個複本或執行個體預設耗用該資源的數量相關資訊。計量也會包含一個權數，表示該計量對於服務的重要程度 (以防需要取捨)。

## 其他放置規則
有其他類型的配置規則，主要適用於地理區域分散的叢集或其他較少見的案例。這些是透過相互關聯或原則來設定。雖然許多案例中都不會用到它們，但我們還是會說明它們的完整性。

## 後續步驟
- 度量是 Service Fabric 叢集資源管理員管理叢集中的耗用量和容量的方式。若要深入了解度量及其設定方式，請查看[這篇文章](service-fabric-cluster-resource-manager-metrics.md)
- 親和性是您可以針對服務設定的一種模式。它並不常見，但如果您需要，可以參閱[這裡](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- 有許多不同的放置規則可以在您的服務上設定以處理其他案例。您可以在[這裡](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)了解這些不同的放置原則
- 從頭開始，並[取得 Service Fabric 叢集資源管理員的簡介](service-fabric-cluster-resource-manager-introduction.md)
- 若要了解叢集資源管理員如何管理並平衡叢集中的負載，請查看關於[平衡負載](service-fabric-cluster-resource-manager-balancing.md)的文章
- 叢集資源管理員有許多描述叢集的選項。若要深入了解這些選項，請查看關於[描述 Service Fabric 叢集](service-fabric-cluster-resource-manager-cluster-description.md)的這篇文章

<!---HONumber=AcomDC_0316_2016-->