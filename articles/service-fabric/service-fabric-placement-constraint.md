<properties
   pageTitle="Service Fabric 叢集協調流程中的位置限制 │ Microsoft Azure"
   description="Service Fabric 中位置限制的概念式概觀"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/03/2016"
   ms.author="masnider"/>

# 位置限制概觀

Azure Service Fabric 可讓開發人員將服務複本的位置限制在滿足特定條件的節點。這些條件以經過適當的服務內容特定值評估後的布林運算式表示。


## 功能
使用位置限制可讓您：

- 透過定義節點上的 NodeProperties，來將不同類型的服務侷限在不同類型的節點上。

- 把條件約束做為目標來同時套用到服務的主要和次要複本，或是把條件約束做為目標的來只套用到主要複本。


## 重要概念
NodeProperty--使用者定義或系統定義從字串對值的對應，其會隨著每個節點 (亦即 NodeName) 改變。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

如需詳細資訊，請參閱：[應用程式案例](../service-fabric-application-scenarios)。

<!---HONumber=AcomDC_0204_2016-->