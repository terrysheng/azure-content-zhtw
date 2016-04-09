<properties
   pageTitle="Reliable Actors 架構中的多型 | Microsoft Azure"
   description="在 Reliable Actors 架構中建置 .NET 介面和類型的階層，以重複使用功能和 API 定義。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/08/2016"
   ms.author="seanmck"/>

# Reliable Actors 架構中的多型

Reliable Actors 架構會簡化分散式系統的程式設計。其做法是，讓您使用許多您將在物件導向設計中使用的相同技巧來建置服務。這些技巧的其中之一就是多型，此技巧允許從更一般化的父系繼承類型和介面。Reliable Actors 架構中的繼承通常會遵循 .NET 模型，但有幾個額外的條件約束。

## 介面

Reliable Actors 架構會要求您至少定義一個要由動作項目類型實作的介面。此介面會用來產生可供用戶端用來與您的動作項目進行通訊的 Proxy 類別。只要動作項目類型及其所有父系所實作的每個介面最終都是衍生自 IActor，這些介面便可以從其他介面繼承。IActor 是動作項目的平台定義基底介面。因此，使用圖形的傳統多型範例可能會看起來像這樣：

![圖形動作項目的介面階層][shapes-interface-hierarchy]


## 類型

您也可以建立衍生自平台所提供之基底「動作項目」類別的動作項目類型階層。針對可設定狀態的動作項目，您同樣可以建立狀態類型階層。如果是圖形，您可能會有一個狀態類型為 `ShapeState` 的基底 `Shape` 類型。

    public abstract class Shape : Actor<ShapeState>, IShape
    {
        ...
    }

`Shape` 的子類型可以使用 `ShapeType` 的子類型來儲存更明確的屬性。

    [ActorService(Name = "Circle")]
    public class Circle : Shape, ICircle
    {
        private CircleState CircleState => this.State as CircleState;

        public override ShapeState InitializeState()
        {
            return new CircleState();
        }

        [Readonly]
        public override Task<int> GetVerticeCount()
        {
            return Task.FromResult(0);
        }

       [Readonly]
       public override Task<double> GetArea()
       {
           return Task.FromResult(
               Math.PI*
               this.CircleState.Radius*
               this.CircleState.Radius);
       }

       ...
    }

請注意動作項目類型上的 `ActorService` 屬性。此屬性會告知 Azure Service Fabric SDK 它應該自動建立一個服務來裝載此類型的動作項目。在某些情況下，您可能會想要建立僅供與子類型共用功能而永遠不用來將具體動作項目具現化的基底類型。在這些情況下，您應該使用 `abstract` 關鍵字來指出您永遠不會根據該類型建立動作項目。


## 後續步驟

- 請參閱 [Reliable Actors 架構如何利用 Service Fabric 平台](service-fabric-reliable-actors-platform.md)以提供可靠性、延展性及一致的狀態。
- 了解[動作項目生命週期](service-fabric-reliable-actors-lifecycle.md)。

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png

<!---HONumber=AcomDC_0309_2016-->