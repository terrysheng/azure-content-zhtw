<properties 
   pageTitle="使用 Service Fabric 的應用程式案例和設計"
	description="應用程式的類別。使用無狀態和可設定狀態的服務的應用程式設計"
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
	ms.date="08/21/2015"
	ms.author="mfussell"/>

# Service Fabric 應用程式案例

Microsoft Azure Service Fabric 提供可靠且彈性的平台，可讓您撰寫及執行許多類型的商務應用程式和服務。這些應用程式和微服務可以是無狀態或可設定狀態，而且它們是跨虛擬機器進行資源平衡，以達到最佳效率。Service Fabric 的獨特架構可讓您在應用程式中執行接近即時的資料分析、記憶體中計算、平行交易和事件處理。您可以輕鬆地根據變更的資源需求上下調整 (其實是收發) 您的應用程式。

Azure 中的 Service Fabric 平台適用於下列應用程式和服務的類別：

- **高可用性服務**：Service Fabric 服務提供極快速的容錯移轉。Service Fabric 可讓您建立多個次要服務複本。如果節點、處理序或個別服務因為硬體或其他失敗而停機，其中一個次要複本會立即升級為主要複本，只會產生對於客戶而言可以忽略的服務損失。 

- **可延展服務**：可以分割個別服務，以便在叢集中向外延展。此外，可以建立並立即移除個別服務。服務可以快速且輕鬆地由數個節點上的少數執行個體向外延展成許多節點上的數千個執行個體，然後再次立即下調，視資源需求而定。您可以使用 Service Fabric 建置這些服務以及管理其完整的生命週期。
 
- **非靜態資料的運算**：Service Fabric 可讓您建置資料、I/O 和計算密集、可設定狀態的應用程式。Service Fabric 允許在應用程式中的處理 (運算) 和資料共置。一般而言，現今的應用程式需要存取資料時，會發生與外部資料快取或儲存體層有關的網路延遲。使用可設定狀態的 Service Fabric 服務可消除這種延遲，讓更多高效能的讀取和寫入發揮功效。例如，假設您的應用程式為要求來回時間低於 100 毫秒的客戶，執行接近即時的選項建議。相較於必須從遠端儲存擷取必要資料的標準實作模型，選項建議運算與資料和規則共置的 Service Fabric 服務，其延遲和效能特性會提供使用者回應的經驗。
 
- **工作階段架構的互動式應用程式**：如果您的應用程式，例如線上遊戲或立即訊息，需要低度延遲讀取和寫入，Service Fabric 會很有用。Service Fabric 可讓您建置這些互動、可設定狀態的應用程式，而不需要建立無狀態應用程式所需的不同存放區或快取 (增加延遲和可能發生的一致性問題)。
 
- **分散式圖形處理**：社交網路的成長已開始大幅增加平行分析大型圖形的需求。快速調整和平行載入處理讓 Service Fabric 成為處理大型圖形的自然平台。Service Fabric 可讓您為群組建置高度可延展服務，例如社交網路、商務智慧和科學研究。
 
- **資料分析和工作流程**：Service Fabric 的快速讀取/寫入可以成為必須可靠地處理事件或資料流的應用程式的依靠。Service Fabric 也會啟用描述處理管線的應用程式，結果必須是可靠的並且通過，而不會對下一個處理階段造成損失，包括交易和財務系統，其中資料一致性和計算保證是不可或缺的。

## 設計由無狀態與可設定狀態微服務組成的應用程式 ##
使用 Azure 雲端服務背景工作角色建置應用程式即為無狀態服務的範例。相形之下，可設定狀態的微服務維護的授權狀態超越要求及其回應，在提供複寫所支援的交易式保證的簡易 API 中，提供高可用性和狀態一致性。Service Fabric 的可設定狀態服務將高可用性 (HA) 普及至所有類型的應用程式，不局限在資料庫和其他資料存放區。這是自然的進展：應用程式為取得 HA，已從只使用關聯式資料庫發展到 NoSQL 資料庫；現在應用程式可以自行管理本身的「熱門」狀態和資料以提高效能，不必犧牲可靠性、一致性或可用性。

建置由微服務組成的應用程式時，您通常有無狀態 Web Apps (ASP.NET、node.js 等等) 的組合，呼叫無狀態和可設定狀態的商務中間層服務，使用 Service Fabric 部署命令，全部部署到相同的 Service Fabric 叢集。每個微服務都獨立於延展、可靠性和資源使用量之外，大幅提升開發和生命週期管理的靈活性。
  
可設定狀態的微服務簡化應用程式設計，因此不需要為滿足純無狀態應用程式的可用性與延遲需求時傳統上需要的其他佇列與快取。可設定狀態服務有高可用性、低延遲性的特性，這表示整體管理應用程式的移動組件較少。下列圖表說明設計無狀態與可設定狀態之應用程式之間的差異。藉由運用[可靠的服務](../Service-Fabric/service-fabric-reliable-services-introduction.md)和[可靠的動作項目](service-fabric-reliable-actors-introduction.md)程式設計模型，可設定狀態的服務會減少應用程式的複雜度，同時達到高輸送量和低度延遲。

## 使用無狀態服務建置的應用程式##
![使用無狀態服務的應用程式][Image1]

## 使用可設定狀態的服務建置的應用程式##
![使用無狀態服務的應用程式][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟


開始使用 Service Fabric [可靠的服務](service-fabric-reliable-services-quick-start.md)和[可靠的執行者](service-fabric-reliable-actors-get-started.md)程式設計模型建置無狀態與可設定狀態的服務。

另請參閱下列主題：

[定義和管理服務狀態](service-fabric-concepts-state.md)

[服務的可用性](../service-fabric-concepts-availability-services.md)

[Service Fabric 服務的延展性](service-fabric-concepts-scalability.md)

[分割 Service Fabric 服務](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
 
 

<!---HONumber=August15_HO9-->