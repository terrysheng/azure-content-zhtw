<properties
   pageTitle="Service Fabric 概觀 | Microsoft Azure"
   description="組成多個微服務以提供調整和恢復功能的 Service Fabric 概觀。Service Fabric 是分散式系統平台，用於建置可擴充、可靠且可輕鬆管理的雲端應用程式。"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/24/2016"
   ms.author="msfussell"/>

# Service Fabric 概觀
Service Fabric 是分散式系統平台，可讓您輕鬆封裝、部署及管理可調整和可信賴的微服務，並解決開發與管理雲端應用程式時遭遇的重大挑戰。透過使用 Service Fabric，開發人員與管理員能夠避免解決複雜的基礎結構問題，改為專注於實作關鍵且嚴格要求的工作負載，並了解其為可調整、可信賴及可管理的。Service Fabric 代表新一代的中介軟體平台，可建置與管理這些企業級的 Tier-1 雲端規模應用程式。

## 由微服務組成的應用程式
Service Fabric 可讓您建置並管理由微服務組成的應用程式，這類應用程式可調整且可靠，以非常高的密度在共用的機器集區上執行 (稱為 Service Fabric 叢集)。其提供精密的執行階段，可建置分散式、可擴充的無狀態與可設定狀態的微服務。還提供完整的應用程式管理功能，以佈建、部署、監視、升級/修補及刪除所部署的應用程式。

為什麼微服務方式如此重要？ 兩個主要原因如下：

1. 微服務可讓您根據其需求，調整應用程式的不同部分

2. 開發團隊能夠更敏捷地推出更動，藉以更快速且更頻繁地為您的客戶提供新功能。

Service Fabric 提供技術支援給目前許多的 Microsoft 服務，包括 Azure SQL Database、Azure DocumentDB、Cortana、Power BI、Microsoft Intune、Azure 事件中樞、Azure IoT、商務用 Skype 以及許多核心 Azure 服務等等。

Service Fabric 針對建立「雲端原生」服務量身打造而成，此類服務可視需要以小規模開始，接著擴充為包含成千上萬個機器的大規模服務。

現今的網際網路級別服務是使用微服務建立。微服務範例包括通訊協定閘道器、使用者設定檔、購物車、清查處理、佇列和快取。Service Fabric 是微服務平台，會給予每一個可以是無狀態或可設定狀態的微服務唯一的名稱。

Service Fabric 為由微服務組成的應用程式，提供完整的執行階段與生命週期管理功能。其將微服務裝載在容器中，這些容器部署在整個 Service Fabric 叢集之中並已啟動。正如同密度增加程度的順序可透過從 VM 移至容器來決定，經由從容器移至微服務，即可有類似的密度程度順序。例如，一個建立在 Service Fabric 上的 Azure SQL Database 叢集是由數百個機器所組成，這些機器執行數萬個容器，而容器總共裝載了數十萬個資料庫 (每個資料庫都是 Service Fabric 的可設定狀態微服務)。 事件中樞與上述的其他服務也是同樣的狀況。這也是為何「超大規模」一詞可用於描述 Service Fabric 的容量。如果容器的密度高，微服務就有超大的規模。

如需有關微服務方法的詳細資訊，請閱讀[為何要用微服務方式建置應用程式？](service-fabric-overview-microservices.md)

## 隨處建立 Service Fabric 叢集
您可以在許多要部署應用程式的環境中建立 Service Fabric 叢集。可以是在 Azure 或內部部署中，Windows Server 或 Linux 上。此外，SDK 中的開發環境等同於沒有涉及模擬器的生產環境。換句話說，如果在本機開發叢集上執行，它會部署到其他環境中的相同叢集。

如需詳細資訊，請參閱[使用 Service Fabric 在 Windows Server 或 Linux 上隨處部署](service-fabric-deploy-anywhere.md)

![Service Fabric 平台][Image1]

## 無狀態與可設定狀態的 Service Fabric 微服務

Service Fabric 可讓您建置由微服務組成的應用程式。無狀態微服務 (通訊協定閘道器、Web Proxy 等) 不會維護該服務任何指定要求及其回應之外的可變動狀態。Azure 雲端服務背景工作角色即為無狀態服務的範例。可設定狀態的微服務 (使用者帳戶、資料庫、裝置、購物車、佇列等) 會維護要求及其回應外的可變動授權狀態。現今的網際網路級別應用程式包含無狀態與可設定狀態微服務的組合。

為什麼有具狀態的微服務以及無狀態的微服務？ 兩個主要原因如下：

1. 能夠在同一部機器上完成程式碼與資料，因此可建構高輸送量、低延遲性、容錯線上交易處理 (OLTP) 服務，如互動式店面、搜尋、物聯網 (IoT) 系統、貿易系統、信用卡處理、詐欺偵測系統，及個人記錄管理等。

2. 應用程式設計簡化成可設定狀態的微服務，因此不需要其他佇列與快取。傳統上，這些是滿足純無狀態應用程式的可用性與延遲需求所需的項目。可設定狀態服務有高可用性、低延遲性的特性，這表示整體管理應用程式的移動組件較少。

如需有關使用 Service Fabric 的應用程式模式的詳細資訊，請參閱[應用程式案例](service-fabric-application-scenarios.md)和[選擇程式設計模型架構](service-fabric-choose-framework.md)做為您的服務

## 應用程式生命週期管理
從開發到部署、到每日管理、維護，以及最終的解除委任，Service Fabric 為雲端應用程式的完整應用程式生命週期管理 (ALM) 提供第一等的支援。

Service Fabric ALM 功能讓應用程式管理員/IT 操作員能夠使用簡單、低接觸的工作流程來佈建、部署、修補與監視應用程式。這些內建的工作流程能大幅降低 IT 操作員的負擔，讓應用程式持續可用。

大多數的應用程式都結合了無狀態與可設定狀態的微服務，以及其他一起部署的可執行檔/執行階段。Service Fabric 擁有應用程式與封裝微服務的強式型別，因此能部署多個應用程式執行個體，每個執行個體都能獨立管理與升級。重要的是，Service Fabric 能夠部署「任何」的可執行檔或執行階段，並使其可靠。例如，它可用於部署 ASP.NET Core 1、Node.js、Java VM、指令碼或其他可組成應用程式的任何一切。

如需有關應用程式週期管理的詳細資訊，請參閱[應用程式週期](service-fabric-application-lifecycle.md)，有關部署任何程式碼的詳細資訊，請參閱[部署客體可執行檔](service-fabric-deploy-existing-app.md)

## 主要功能
藉由使用 Service Fabric，您可以：

- 開發可大幅調整且自我修復的應用程式。

- 使用 Service Fabric 程式設計模型，開發由微服務，或僅主機來賓可執行檔，以及您所選擇的其他應用程式架構 (例如 ASP.NET Core 1、node.js 等) 組成的應用程式。

- 開發無狀態與可設定狀態的微服務，並使其高度可靠。

- 使用可設定狀態的微服務取代快取和佇列，以簡化應用程式的設計。

- 部署至 Azure，或部署至執行 Windows Server 或 Linux 的內部部署雲端，但零程式碼變更。只要撰寫一次，就能夠在任何地方部署至任何 Service Fabric 叢集。

- 採用「機器的資料中心」法開發。本機開發環境的程式碼與在 Azure 資料中心執行的程式碼相同。

- 部署應用程式 (單位為秒)。

- 部署密度比虛擬機器高的應用程式，亦即為每部機器部署數百或數千個應用程式。

- 並列部署相同應用程式的不同版本，每個版本可獨立升級。

- 無須停機即可管理可設定狀態應用程式的生命週期，包括即時升級與非即時升級。

- 使用 .NET API、PowerShell 或 REST 介面管理應用程式。

- 獨立地升級與修補應用程式內的微服務。

- 監視和診斷應用程式的健全狀況，並設定用來執行自動修復的原則。

- 應用程式是根據可用資源調整的，因此能輕鬆地相應增加或相應減少 Service Fabric 叢集。

- 監視自我修復資源平衡器如何協調應用程式在 Service Fabric 叢集之間的重新分配，以從故障中復原，並根據可用資源最佳化負載的散佈。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

* 其他資訊：
	* [為何要用微服務方式建置應用程式？](service-fabric-overview-microservices.md)
	* [術語概觀](service-fabric-technical-overview.md)
* 設定 Service Fabric [開發環境](service-fabric-get-started.md)  
* 為您的服務[選擇程式設計模型架構](service-fabric-choose-framework.md)

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

<!---HONumber=AcomDC_0330_2016-->