<properties
   pageTitle="Service Fabric 叢集資源管理員 - 放置原則"
   description="Service Fabric 服務的其他放置原則和規則的概觀"
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

# 放置原則
有許多其他不同的規則，讓您最終可能關心 Service Fabric 叢集是否會跨越地理距離 (例如多個資料中心或 Azure 區域)，或者您的環境是否會跨越多個地緣政治控制區域 (或一些其他涵蓋您所關心之法律或政策範圍的情況)。這其中大部分規則都能透過節點屬性和放置條件約束來設定 (如同我們先前所討論)，但有一些規則比較複雜。在任何情況下，我們都會提供這些捷徑 (例如放置條件約束)，放置原則可以個別服務為基礎來設定。

-	InvalidDomain – 可讓您指定特定的容錯網域對此工作負載是無效的。能夠有效地確保特定的服務絕對不會在特定的地區上執行，例如，基於地緣政治或公司政策的緣故。

![無效的網域範例][Image1]

程式碼：

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DCEast/"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/dc9/”)
```

-	RequiredDomain (相當直接了當) 要求所有複本要出現在指定的網域中。

![必要的網域範例][Image2]

程式碼：

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

-	PreferPrimaryDomain – 慣用的主要網域是一個有趣的控制項，因為它允許選取容錯網域，如果能夠這樣做，主要複本就必須存在於該網域中。若一切狀況良好，主要複本最終將會在這個網域中。如果網域或主要複本失敗，或基於某些原因必須關機，則主要複本將會移轉至某些其他位置。它將會在適當時機移回慣用的網域。當然，此設定僅適用於具狀態服務。

![慣用的主要網域和容錯移轉][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUs/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUs")
```

-	RequireDomainDistribution – 要求網域發佈是另一個可用來防止某些情況的選項。請注意，在上述範例中，很可能暫時將多個複本 (事實上是一個仲裁，哎呀) 封裝到相同的資料中心。在此情況下，我們實際上可能沒問題 (因為主要複本並未與大部分複本位於同一個 DC 上)，但我離題了…這表示我們會暫時受限於我們希望避免的組態。這將取決於當時候叢集中的其他條件，但您仍可能想要藉由確保複本一律位於個別網域中，完全避免此情況。RequireDomainDistribution 旗標會提供此功能，但請注意，這可能表示，如果其將導致封裝，則我們不會放置全部複本。這表示在可用網域中的節點回來之前，您暫時會以低於目標複本數的情況來執行。我們看到了一些隨時都希望具備目標複本數 (狀態複本) 的客戶，而有些則不想在可用性顧慮上承擔風險 – 如果我們將複本封裝為容錯網域，而該網域失敗，則您同時會遺失這所有複本。對少量的複本來說，這可能導致仲裁遺失。由於大多數的使用者會執行超過 3 個複本，因此，預設值是不需要網域發佈，並讓平衡與容錯移轉能夠正常處理這些情況，即使這表示網域暫時具有多個要封裝到其中的複本也一樣。

程式碼：

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

您可以在單一服務上設定多個無效或必要的網域，不過其他兩個 (慣用網域和網域發佈) 只能針對每個服務設定一次。

現在，是否可針對未跨越地理區域的叢集中的服務使用這些組態？ 當然可以！ 但也沒有充分的理由 – 除非您實際上正在執行跨越地理區域的叢集，否則，應特別避免必要、無效及慣用的網域組態。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
- [深入了解設定服務](service-fabric-cluster-resource-manager-configure-services.md)



[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0309_2016-->