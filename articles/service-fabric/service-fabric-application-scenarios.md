<properties
   pageTitle="應用程式案例和設計 | Microsoft Azure"
   description="Service Fabric 中雲端應用程式類別概觀。討論使用具狀態和無狀態服務的應用程式設計。"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="mfussell"/>

# Service Fabric 應用程式案例

Azure Service Fabric 提供可靠且彈性的平台，可讓您撰寫及執行許多類型的商務應用程式和服務。這些應用程式和微服務可以是無狀態或具狀態，而且它們會跨虛擬機器進行資源平衡，以達到最佳效率。Service Fabric 的獨特架構可讓您在應用程式中執行接近即時的資料分析、記憶體中計算、平行交易和事件處理。您可以輕鬆地根據變更的資源需求上下調整 (其實是收發) 您的應用程式。

Azure 中的 Service Fabric 平台適用於下列應用程式和服務的類別：

- **高可用性的服務**：Service Fabric 服務藉由建立多個次要服務複本，提供快速的容錯移轉。如果節點、處理序或個別服務因為硬體或其他故障而停機，其中一個次要複本會升級為主要複本，以便將服務損失降到最低。

- **可調整的服務**：可以分割個別服務，以便在叢集中相應放大。此外，可以建立並立即移除個別服務。服務可以快速且輕鬆地由數個節點上的少數執行個體，向外延展成許多節點上的數千個執行個體，然後再次下調，視資源需求而定。您可以使用 Service Fabric 來建置這些服務，以及管理其完整的生命週期。

- **非靜態資料的運算**：Service Fabric 可讓您建置資料、輸入/輸出，以及計算密集且具狀態的應用程式。Service Fabric 允許在應用程式中的處理 (運算) 和資料共置。一般而言，當應用程式需要存取資料時，會發生與外部資料快取或儲存體層有關的網路延遲。使用具狀態的 Service Fabric 服務可以消除這種延遲，讓更多高效能的讀取和寫入發揮功效。例如，假設您的應用程式為客戶執行近乎即時的選項建議 (來回時間低於 100 毫秒)。相較於必須從遠端儲存體擷取必要資料的標準實作模型，選項建議運算與資料和規則共置的 Service Fabric 服務，其延遲和效能特性會提供使用者回應靈敏的經驗。

- **工作階段架構的互動式應用程式**：如果您的應用程式 (例如線上遊戲或立即訊息) 需要低度延遲讀取和寫入，Service Fabric 就很有用。Service Fabric 可讓您建置這些具狀態的互動式應用程式，而不像無狀態 app 需建立不同的存放區或快取。(這會增加延遲且可能造成一致性問題)。

- **分散式圖形處理**：社交網路的成長已開始大幅增加平行分析大型圖形的需求。快速調整和平行載入處理讓 Service Fabric 成為處理大型圖形的自然平台。Service Fabric 可讓您為群組建置高度可延展服務，例如社交網路、商務智慧和科學研究。

- **資料分析和工作流程**：Service Fabric 的快速讀取和寫入可以成為必須可靠處理事件或資料流的應用程式的依靠。Service Fabric 也可讓應用程式描述處理管線，其中的結果必須能夠可靠地傳遞到下一個處理階段而不會遺失。這其中包括交易和財務系統，其中的資料一致性和計算保證是不可或缺。

## 設計由無狀態與具狀態的微服務組成的應用程式
使用 Azure 雲端服務背景工作角色建置應用程式，即為一個無狀態服務的範例。相較之下，具狀態的微服務會維護要求及其回應以外的授權狀態。這可透過簡單的 API 提供高可用性和狀態的一致性，其提供受複寫支援的交易式保證。Service Fabric 的具狀態服務會將高可用性普及至所有類型的應用程式，而不會侷限於資料庫和其他資料存放區。這是自然的進展。針對高可用性，應用程式已經從使用單純的關聯式資料庫進展到 NoSQL 資料庫的境界。現在應用程式就能在其本身內管理它們的「熱門」狀態和資料，以便進一步提高效能，而不需犧牲可靠性、一致性或可用性。

建置由微服務組成的應用程式時，您通常會擁有無狀態 Web Apps (ASP.NET、node.js 等等) 的組合，呼叫無狀態和具狀態的商務中間層服務，使用 Service Fabric 部署命令，全部部署到相同的 Service Fabric 叢集。這其中的每個服務都擁有各自的可調整、可靠性和資源使用量，可大幅提升開發和生命週期管理的靈活性。

具狀態的微服務簡化了應用程式設計，因此不需要傳統上為滿足純無狀態應用程式的可用性與延遲需求時所需的其他佇列與快取。由於具狀態服務具有高可用性、低延遲的特性，這表示應用程式中需管理的移動組件整體上減少了。下列圖表說明設計無狀態與具狀態之應用程式間的差異。藉由運用 [Reliable Services](service-fabric-reliable-services-introduction.md) 和 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 程式設計模型，具狀態服務會減少應用程式的複雜度，同時達到高輸送量和低延遲。

## 使用無狀態服務建置的應用程式##
![使用無狀態服務的應用程式][Image1]

## 使用可設定狀態的服務建置的應用程式##
![使用無狀態服務的應用程式][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟


開始使用 Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) 和 [Reliable Actors](service-fabric-reliable-actors-get-started.md) 程式設計模型，來建置無狀態與具狀態的服務。

另請參閱下列主題：

[Microservices 詳細說明](service-fabric-overview-microservices.md)

[定義及管理服務狀態](service-fabric-concepts-state.md)

[Service Fabric 服務的可用性](service-fabric-availability-services.md)

[調整 Service Fabric 服務](service-fabric-concepts-scalability.md)

[分割 Service Fabric 服務](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg

<!---HONumber=AcomDC_0224_2016-->