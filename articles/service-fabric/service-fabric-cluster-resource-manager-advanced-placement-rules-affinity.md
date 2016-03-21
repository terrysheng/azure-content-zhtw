<properties
   pageTitle="Service Fabric 叢集資源管理員 - 同質性"
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

# 同質性

同質性對於微服務環境而言，乍看之下似乎不具太大意義。這是因為它在微服務環境中不具太大意義。同質性是一個控制項，主要提供來協助簡化將先前已存在且較大型的單體式應用程式轉換到雲端和微服務世界的程序。

假設您正在將較大型 app，或只是設計時未將微服務納入考量的 app 引進到 Service Fabric。這實際上很常見，所以我們曾經有數個客戶 (內部和外部) 處於此情況。一開始先將整個 app 提升到該環境，將它封裝並執行。接著，開始將其分解成不同的較小服務，使其可彼此通訊。

然後就是「糟糕」了。「糟糕」通常屬於下列其中一個類別︰

1. 結果是單體式 app 中的元件 X 和元件 Y 之間具有未記載的相依性，而我們只是讓它變成一個服務，並在叢集間移動。但現已中斷。
2.	這些項目會透過 (本機具名管道| 共用記憶體 | 磁碟上的檔案) 進行通訊，但我真的很需要能夠個別更新它，好讓速度能夠稍微加快些。
3.	一切都沒問題，但事實上，這兩個元件具有非常多對話/效能敏感的內容，所以當我將它們移到個別服務時，我的效能會遭到重挫。

在所有這些情況下，我們不想遺失重構的工作，也不想返回單體式，但想嘗試一併回復所有項目，在解決問題之前讓它們「覺得」是在本機上。

怎麼辦？ 嗯，您可以嘗試開啟同質性。

## 同質性的運作方式
若要設定同質性，您可以定義兩個不同服務之間的同質關聯性。通常您可以將此關聯性想像成在一個服務上「指向」另一個服務，並假設「此服務只有在該服務正在執行時才能執行」。 有時我們會將這些關聯性稱為父子式關聯性 (您會在父系上指向子系)。此關聯性的作用是確保某一個服務的複本或執行個體會與和其產生同質性之服務的複本或執行個體放置於同一個節點上。

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## 各種同質性選項
同質性是透過數種相互關聯的結構描述之一來表示，而且有兩種不同的模式。同質性的最常見模式是我們稱為 NonAlignedAffinity 的模式。在 NonAlignedAffinity 中，不同服務的複本或執行個體會放在同一個節點上。另一個模式是 AlignedAffinity。對齊的同質性會與具狀態服務搭配使用。將一個具狀態服務設定為與另一個具狀態服務具有對齊的同質性，可確保這些服務的主要複本彼此都會放置於相同節點上，而且每個次要複本的配對也會放置於相同節點上。它也可能 (但較不常見) 針對具狀態服務設定 NonAlignedAffinity。在此情況下，您會取得許多與無狀態服務相同的行為 – 兩個具狀態服務的不同複本會共置於相同節點上，但不會嘗試確保主要複本和次要複本都位於相同節點上。

![同質性模式及其效果][Image1]

#### 盡力而為的期望狀態
在同質性與單體式架構之間有一些差異。它們幾乎全部都可歸結為下列事實：同質性關聯性就是盡力而為 – 例如，由於它們基本上是不同的服務，因此可單獨失敗。有其他因素會導致服務的不同複本分隔開來，例如容量限制。


#### 鏈結 vs.星形
我們目前無法建立同質關聯性鏈結的模型。這表示是，如果有一個服務是某一個同質關聯性中的子系，則該服務不能是另一個同質關聯性中的父系。這表示是，如果您想要建立這種關聯性的模型，您必須有效地將它的模型建立為星形，而不是鏈結，方法是改為將最下層子系的父系設定為「中間」子系的父系。

![同質關聯性內容中的鏈結 vs.星形][Image2]

目前關於同質關聯性的另一個要注意事項是它們是雙向的。這是難以察覺的，但實際上這表示「同質性」規則只會強制子系就是父系的所在之處 – 父系應該會突然容錯移轉到另一個節點 (或者任何其他受限的動作，其只會強制移動父系)，接著在資源管理員注意到子系不是與父系位於相同位置之前，它實際上不會覺得有任何不妥之處；關聯性不會立即強制執行。

#### 支援分割
請注意，關於同質性要注意的最後一點是，分割父系的地方不支援同質關聯性。我們最終可能會支援這一點，但目前並不允許。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
- [深入了解設定服務](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png

<!---HONumber=AcomDC_0309_2016-->