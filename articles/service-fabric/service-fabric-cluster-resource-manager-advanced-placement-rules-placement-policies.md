<properties
   pageTitle="Service Fabric 叢集資源管理員 - 放置原則 | Microsoft Azure"
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
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Service Fabric 服務的放置原則
有許多其他不同的規則，讓您最終可能關心 Service Fabric 叢集是否會跨越地理距離 (例如多個資料中心或 Azure 區域)，或者您的環境是否會跨越多個地緣政治控制區域 (或一些其他涵蓋您所關心之法律或政策範圍的情況)。這其中大部分規則都能透過節點屬性和放置條件約束來設定 (如同我們先前所討論)，但有一些規則比較複雜。在任何情況下，我們都會提供這些捷徑 (例如放置條件約束)，放置原則可以根據個別服務來設定。

## 指定無效的網域
InvalidDomain 放置原則可讓您指定特定的容錯網域對此工作負載是無效的。此原則可確保特定的服務絕對不會在特定的區域中執行，例如，基於地緣政治或公司政策的緣故。您可以指定多個無效的網域

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
## 指定所需的網域
所需的網域放置原則要求所有複本要出現在指定的網域中。您可以指定多個所需的網域。

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

## 指定主要複本的慣用網域
慣用的主要網域是一個有趣的控制項，因為它允許選取容錯網域，如果能夠這樣做，主要複本就必須放置在該網域中。若一切狀況良好，主要複本最終將會在這個網域中。如果網域或主要複本失敗，或基於某些原因必須關機，則主要複本將會移轉至某些其他位置。它將會在適當時機移回慣用的網域。當然，此設定僅適用於具狀態服務。此原則最常用於跨越地理區域的叢集中，您將在此叢集中使用其他位置進行備援，但希望主要複本會放置在特定的位置，才能為前往主要複本的作業提供較低的延遲 (寫入以及預設也由主要複本提供的所有讀取)。

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

## 要求在所有網域之間分佈複本，而且不允許封裝
您可以指定的另一個原則是要求一律在可用的網域之間分佈複本。請注意，在上述範例中，很可能暫時將多個複本 (事實上是一個仲裁，哎呀) 封裝到相同的資料中心。在此情況下，我們實際上可能沒問題 (因為主要複本並未與大部分複本位於同一個 DC 上)，但這表示我們會暫時受限於我們希望避免的組態。這將取決於當時候叢集中的其他條件，但您仍可能想要藉由確保複本一律位於個別網域中，完全避免此情況。請記住，如果您選取此選項，您將選擇停機時間/失敗，而不是讓服務能夠繼續在剩餘的節點上執行。RequireDomainDistribution 原則會設定此行為，但請注意，這可能表示，如果其將導致封裝，則我們根本不會放置複本。這表示在可用網域中的節點回來之前，您暫時會以低於目標複本數的情況來執行。我們看到了一些隨時都希望具備目標複本數 (狀態複本) 的客戶，而有些則不想在可用性顧慮上承擔風險 – 如果我們將複本封裝為容錯網域，而該網域失敗，則您同時會遺失這所有複本。對少量的複本來說，這可能導致仲裁遺失。在網域不太可能永遠不會出現的情況下，這可能會導致資料遺失。由於大多數的使用者會執行超過 3 個複本，因此，預設值是不需要網域發佈，並讓平衡與容錯移轉能夠正常處理這些情況，即使這表示網域暫時具有多個要封裝到其中的複本也一樣。

程式碼：

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

現在，是否可針對未跨越地理區域的叢集中的服務使用這些組態？ 當然可以！ 但也沒有充分的理由 – 除非您實際上正在執行跨越地理區域的叢集，否則，應特別避免必要、無效及慣用的網域組態。

## 後續步驟
- 如需可用來設定服務的其他選項的詳細資訊，請查看[深入了解設定服務](service-fabric-cluster-resource-manager-configure-services.md)中提供的其他叢集資源管理員組態的相關主題

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0316_2016-->