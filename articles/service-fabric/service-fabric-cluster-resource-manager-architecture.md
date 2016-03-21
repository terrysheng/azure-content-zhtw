<properties
   pageTitle="資源管理員架構 | Microsoft Azure"
   description="Service Fabric 叢集資源管理員的架構概觀。"
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

# 叢集資源管理員架構概觀
若要管理叢集中的資源，叢集資源管理員必須具備幾個部分的資訊。它必須了解目前存在哪些服務，以及服務正在取用的資源目前 (預設) 的數量。同時必須了解叢集中節點的實際容量，以及可整體用於叢集中和特定節點中剩餘可用的資源數量。我們必須處理下列事實：指定服務的資源號用量可隨時間變更，以及服務實際上通常需要一個以上的資源。在許多不同的服務之間，可能會有「真實」的資源 (例如，記憶體和磁碟耗用，通常會跨許多不同類型的服務來使用)，以及只有特定服務需要的資源。

更複雜的事實是，叢集的擁有者與操作者偶爾會與服務作者不同，或者，至少是帶著不同帽子的同一組人，例如，在開發您的服務時，您會了解一些與資源相關的必要事項，以及部署不同元件的理想方式，但是身為要在生產環境中為該服務處理即時網站事件的人員，您有其他要執行的工作且需要不同的工具。此外，叢集和服務本身都不是以靜態方式設定的︰叢集中的節點數目可以放大和縮小、不同大小的節點可以進出，而且服務可以變更其資源配置，以及建立和移除。升級或其他管理作業可以在整個叢集中運作，當然事情可能隨時會失敗。

我們的資源管理員必須知道許多有關整體叢集本身的事項，以及特定服務的需求。若要達成此目的，在 Service Fabric 中，我們擁有資源管理員的兩個代理程式，其會在個別節點上執行以彙總本機資源耗用資訊，而集中式容錯移轉資源管理員服務會彙總關於服務和叢集的所有資訊，並根據叢集和服務的期望狀態組態來反應變更。容錯移轉是透過我們針對您服務所遵循的完全相同機制來達成，亦即將服務的狀態複製到某些數量的複本 (通常是 7 份)。

![資源平衡器架構][Image1]

讓我們看一下這個圖表 (圖 1) 做為範例。在執行階段期間，有一大堆可能發生的變更︰例如，假設服務耗用的資源數量中有一些變更、有些服務失敗、有些節點會加入或離開叢集等變更。特定節點上的所有變更會加以彙總，並定期傳送到中央資源管理員服務 (1，2)，它們會在其中再次彙總、分析及儲存。每隔幾秒鐘，中央服務就會查看所有變更，並決定是否需要任何動作 (3)。例如，它可能會注意到節點已加入至叢集且是空的，並決定要將某些服務移至這些節點。它可能也會注意到特定節點是超載的，或者某些服務已失敗 (或刪除)，在其他節點上釋放資源。

讓我們查看下圖 (圖 2)，看看此範例中會發生什麼事︰假設資源管理員決定需要變更。它會與其他系統服務 (尤其是容錯移轉管理員) 進行協調，以進行必要的變更。接著將變更要求傳送至適當的節點 (4)。在此案例中，我們假設資源管理員注意到節點 5 已超載，因此決定要將服務 B 從 N5 移至 N4。重新設定 (5) 結束時，叢集看起來像這樣︰

![資源平衡器架構][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
- [深入了解叢集資源管理員架構](service-fabric-cluster-resource-manager-architecture.md)
- [描述叢集](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png

<!---HONumber=AcomDC_0309_2016-->