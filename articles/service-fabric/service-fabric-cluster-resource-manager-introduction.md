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

# Service Fabric 叢集資源管理員簡介

以傳統方式管理 IT 系統或一組服務，意味著要讓一些電腦專屬於這些特定的服務或系統。許多主要服務都已分成「Web」層和「資料」或「儲存體」層，或許會有一些其他特殊化的元件，例如快取。其他類型的應用程式會有訊息層，要求會在其中流進和流出、連接到工作層以進行任何分析，或做為訊息一部分所需的轉換。每個組件都有特定的電腦或一些專屬的電腦︰資料庫會取得一些專屬的電腦，Web 伺服器也會取得一些。如果特定類型的工作負載導致其所在的電腦執行太過忙碌，則您可新增更多電腦，並在其上設定要執行該類型的工作負載，或者使用更大型電腦來取代一些電腦。簡單。如果某台電腦失敗，則在還原該電腦之前，整體應用程式中的那一個部分會以較低容量來執行。仍然相當簡單 (但不一定有趣)。

不過，我們現在假設您已經發現需要相應放大，並採用容器和/或微服務。您突然發現自己擁有數百台或甚至數千台電腦、數十種不同類型的服務，或許針對這些服務擁有數百個不同的執行各體，而每一個都擁有一或多個執行個體或複本以供高可用性 (HA) 使用。

突然間，管理您的環境並不像管理一些專屬於單一類型工作負載的電腦一樣簡單。您的伺服器是虛擬且不再擁有名稱 (畢竟您的心態已從[寵物到牛群](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20)進行完全轉換)，而電腦不會專屬於單一類型的工作負載。關於電腦的設定會變少，而關於服務本身的設定會增加。

由於這會解構您先前的單體式分層 app 並分解到個別服務，因此，您現在有許多更高程度的組合需要處理。誰要決定什麼類型的工作負載可在特定的硬體上，或彼此相鄰？ 當電腦當機時...哪些功能還能在其上執行？ 誰負責確保它會再次啟動來執行？ 您要等待 (虛擬) 電腦正常運作，或者您的工作負載會持續執行？ 需要人為介入才能再次執行工作嗎？ 此外，如何在這種環境中升級呢？ 我們需要一些協助，而您要有一個概念：招募潮以及嘗試使用人員來掩飾複雜度並非正確解答。

怎麼辦？

## Orchestrator 簡介
「Orchestrator」是一種軟體的一般用詞，可協助系統管理員管理這些類型的部署。Orchestrator 是在像是「我想要在環境中執行此服務的 5 個複本」的要求中取得的元件，可使其成真，並 (嘗試) 以該方式來保留它。Orchestrator (不是人類) 是當電腦失敗或工作負載基於某些意外因素而終止時，要迅速採取動作的事物。大部分的 Orchestrator 除了處理失敗還能執行更多動作，例如，協助新的部署、處理升級，以及處理資源耗用，但基本上全部都與在環境中維護組態的一些期望狀態有關。您想要能夠告知 Orchestrator 您的期望，並讓它能夠執行繁重的工作。Mesosphere 頂端的 Chronos、Fleet、Swarm、Kubernetes 和 Service Fabric 全都是 Orchestrator 或內建它們。由於在不同類型的環境中管理真實世界部署的複雜度和條件與日俱增且產生變化，因此會持續建立更多。

## 協調流程即服務
在 Service Fabric 叢集內，Orchestrator 的工作主要是由供叢集資源管理員來處理。Service Fabric 叢集資源管理員是 Service Fabric 內的其中一個系統服務，並會在每個叢集內自動啟動。一般而言，資源管理員的工作可分成三個部分︰

1. 強制執行規則
2. 將您的環境最佳化
3. 在其他程序中提供協助

在我們查看資源管理員擁有的所有功能之前，先簡短地看一下它的運作方式。Service Fabric 叢集資源管理員

## 一般架構和實作
在我們深入說明叢集資源管理員及其所有功能之前，先討論一下它真正代表什麼，以及它的運作方式。

### 哪些不是它的性質
在傳統的 N 層 Web app 中，一直有「負載平衡器」的概念存在，根據其在網路堆疊中的所在位置而定，通常稱為網路負載平衡器 (NLB) 或應用程式負載平衡器 (ALB)。某些負載平衡器是以硬體為基礎 (例如 F5 的 BigIP 供應項目)，有些則是以軟體為基礎 (例如 Microsoft 的 NLB)。在這些架構中，負載平衡的工作是確定所有不同的無狀態前端電腦或叢集中的不同電腦會接收到 (大致上) 相同數量的工作。對於這一點的策略則各不相同，範圍可從將每個呼叫傳送到不同的伺服器，到工作階段釘選/綁定，到根據它的預期成本和目前的電腦負載來進行的實際估計和呼叫配置。

請注意，這是確保 Web 層大致保持平衡的最佳機制。平衡資料層的策略則完全不同，而且根據資料儲存機制而定，通常集中於資料分區化、快取、資料庫管理的檢視及預存程序等。

雖然這其中有些策略很有趣，但 Service Fabric 叢集資源管理員並非任何像是網路負載平衡器或快取的功能。儘管 NLB 可確保前端能夠藉由將流量移至服務執行所在的位置來達成平衡，但 Service Fabric 資源管理員會採取一個完全不同的方針 – 基本上，Service Fabric 會將服務移至對它們最具意義的地方。例如，這可以是目前比較冷的節點，因為服務現在並未執行很多工作。它也可能遠離即將升級的節點，或者因為在其上執行的服務耗用量突然增加而導致超載的節點。因為它負責四處移動服務 (不會將網路流量傳遞到服務已經存在的位置)，所以 Service Fabric 資源管理員會更加靈活，也會包含其他功能來控制移動服務的位置與方式。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
- [深入了解叢集資源管理員架構](service-fabric-cluster-resource-manager-architecture.md)
- [描述叢集](service-fabric-cluster-resource-manager-cluster-description.md)
- [深入了解設定服務](service-fabric-cluster-resource-manager-configure-services.md)
- [深入了解計量](service-fabric-cluster-resource-manager-metrics.md)
- [深入了解叢集資源管理員如何與其他 Service Fabric 管理的功能整合](service-fabric-cluster-resource-manager-management-integration.md)

<!---HONumber=AcomDC_0309_2016-->