<properties
   pageTitle="Service Fabric 叢集資源管理員簡介"
   description="Service Fabric 叢集資源管理員簡介。"
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


# 節流
有時，即使您已正確設定資源管理員，還是能夠中斷叢集 (許多節點或容錯網域失敗、正在進行升級時、正在套用更新時…等情況)。資源管理員將盡力修正這一切，但像這種時候，您可能要考慮設定一個停止機制，讓叢集本身有機會能夠穩定下來 (即將返回的節點會返回、網路條件可使本身的健康狀況良好、可部署已更正的位元)。為了協助達成這幾種情況，資源管理員提供了數個節流。請注意，這些都是「重量級」工具，通常不應使用，除非已進行一些謹慎的數學運算來解決實際上可在叢集中完成的平行工作量，以及經常需要回應這些種類的 (啊哈) 未經規劃且大型的重新設定事件 (也稱為：「非常糟糕的日子」)。

一般而言，我們建議您透過其他選項來避免非常糟糕的日子 (例如，一般程式碼更新，以及避免過度排程要啟動的叢集)，而不是為您的叢集設定節流，以防止它在嘗試修復其本身時使用資源)。也就是說，您可能判斷有些情況 (直到您解決它們為止) 您需要在適當的地方擁有數個節流，即使這表示叢集需要較長的時間才能穩定也一樣。

以下是預設包含的節流：
-	GlobalMovementThrottleThreshold – 這會控制一段時間內叢集中移動的總數 (已定義為 GlobalMovementThrottleCountingInterval，以秒為單位的值)
-	MovementPerPartitionThrottleThreshold – 這會控制一段時間內針對任何服務分割區的移動總數 (MovementPerPartitionThrottleCountingInterval，以秒為單位的值)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

請注意，我們在大部分時間看見的是客戶會使用這些節流，這是因為
-	他們已經處於資源受限的環境中 (例如，連至個別節點的有限網路頻寬)，這表示這類作業會失敗，或者無論如何速度都會變慢，而且
-	他們已充分了解可能需要延長叢集到達穩定狀態所需花費的時間量，包括了解在為其設定節流時，它們最終會以較低的整體可靠性來執行。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
- [深入了解叢集資源管理員如何平衡叢集中的負載](service-fabric-cluster-resource-manager-balancing.md)

<!---HONumber=AcomDC_0309_2016-->