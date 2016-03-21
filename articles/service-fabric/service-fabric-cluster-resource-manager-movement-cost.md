<properties
   pageTitle="Service Fabric 叢集資源管理員 - 移動成本"
   description="Service Fabric 服務的移動成本概觀"
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

# 移動成本
我們在嘗試判斷要對叢集進行哪些變更和指定解決方案的分數時要納入考量的另一個重要因素是達成該解決方案的整體成本。

四處移動服務執行個體或複本會將 CPU 時間和網路頻寬的成本降至最低，而針對具狀態服務，它也會於您需要在關閉舊複本之前建立狀態複本的磁碟上產生空間量的成本。顯然您想要將所有和資源管理員一起出現的解決方案的成本降至最低，但也不想忽略可大幅改善叢集中資源配置的解決方案。

Service Fabric 資源管理員有兩種方式來計算成本和限制它們。首先，當資源管理員正在規劃叢集的新版面配置時，它會計算其所進行的每一個單一移動。有一個簡單的情況是，如果您最終取得兩個具備幾乎相同的整體平衡 (分數) 的解決方案，則請取得成本最低 (移動總數) 的那一個。在我們遇到預設或靜態負載的相同問題之前，這可以運作得很好 – 在任何複雜的系統中，不太可能所有移動都一樣，有些可能昂貴許多。因此就像負載，我們可讓服務在任何指定的時間自我報告移動服務所需的成本。

代碼

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost 有四個層級：零、低、中和高。再次提醒 – 這些只是相對於彼此，但「零」除外，「零」表示移動複本是免費的，不應算入解決方案的分數中。將您的移動成本設定為「高」，不保證複本不會移動，只是除非有移動的好理由，否則我們不會移動它。

![移動成本做為選取複本的因素來移動][Image1]

移動成本可協助我們在達成對等的平衡時，尋找整體導致最少中斷的解決方案。服務的成本概念可相對於許多事項，但最常見的事項如下︰

1.	服務必須移動的狀態或資料量
2.	用戶端中斷連線的成本 (因此，移動主要複本的成本高於移動次要複本的成本)
3.	中斷某些執行中作業的成本 (某些資料存放區層級作業的成本較高，而在某個特定時點之後，您不希望必須捨棄它們 (如果不必這樣做))。因此，在作業持續期間，您可以提高成本以降低服務複本或執行個體移動的可能性，接著，當作業完成之後，您可以讓它回復正常。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
- [深入了解計量](service-fabric-cluster-resource-manager-metrics.md)
- [深入了解叢集資源管理員如何平衡叢集中的負載](service-fabric-cluster-resource-manager-balancing.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

<!---HONumber=AcomDC_0309_2016-->