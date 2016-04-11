<properties
   pageTitle="Service Fabric 術語概觀 | Microsoft Azure"
   description="Service Fabric 的術語概觀討論重要術語概念和文件其餘部分中使用的詞彙。"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="chackdan;subramar"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/24/2016"
   ms.author="msfussell"/>

# Service Fabric 術語概觀

Service Fabric 是分散式系統平台，可讓您輕鬆封裝、部署及管理可調整和可信賴的微服務。本主題詳細說明 Service Fabric 中所使用的術語，以利您了解文件中的其他處使用的詞彙。

## 術語概念

### 基礎結構概念
**叢集**：由虛擬或實體機器連結組成的網路，微服務可於其中部署和管理。叢集可擴充至數千部機器。

**節點**：屬於叢集一部分的電腦或 VM 都稱為節點。需為每個節點指派節點名稱 (字串)。節點具有各種特性，如 placement 屬性。每個電腦或 VM 皆有自動啟動 Windows 服務 `FabricHost.exe`，會在開機時啟動執行，然後此服務會啟動兩個執行檔：`Fabric.exe` 和 `FabricGateway.exe`。這兩個執行檔構成節點。在測試案例中，您可以藉由執行 `Fabric.exe` 和 `FabricGateway.exe` 的多個執行個體，在單一電腦、VM 上裝載多個節點。

### 應用程式概念
**應用程式類型**：指派給服務類型集合的名稱/版本。這項資訊會定義在 `ApplicationManifest.xml` 檔案中，內嵌在應用程式封裝目錄，之後會複製到 Service Fabric 叢集的映像存放區中。然後，您可以從這個應用程式類型，在叢集內建立具名的應用程式。

如需詳細資訊，請閱讀[應用程式模型](service-fabric-application-model.md)一文。

**應用程式封裝**：磁碟目錄，包含此應用程式類型的 `ApplicationManifest.xml` 檔案。此檔案會參考組成此應用程式類型的每個服務類型的服務封裝。應用程式封裝目錄中的檔案會複製到 Service Fabric 叢集的映像存放區。例如，電子郵件應用程式類型的應用程式封裝可能包含指向佇列服務封裝、前端服務封裝、資料庫服務封裝的參考。

**具名應用程式**：應用程式封裝被複製到映像存放區之後，您可以指定應用程式封裝的應用程式類型 (使用其名稱/版本)，建立應用程式在叢集內的執行個體。每個應用程式類型的執行個體會被指派一個看起來像這樣的 URI 名稱：`"fabric:/MyNamedApp"`。在叢集中，您可以從單一應用程式類型建立多個具名應用程式。也可以從不同的應用程式類型建立具名應用程式。每個具名應用程式都是獨立管理和控制版本。

**服務類型**：指派給服務的程式碼封裝、資料封裝、組態封裝的名稱/版本。這項資訊會定義在 `ServiceManifest.xml` 檔案中，內嵌在服務封裝目錄中，之後應用程式封裝的 `ApplicationManifest.xml` 檔會參考此服務封裝目錄。在叢集內，建立具名應用程式之後，可以從應用程式類型的其中一個服務類型建立具名服務。服務類型的 `ServiceManifest.xml` 檔描述該服務。

如需詳細資訊，請閱讀[應用程式模型](service-fabric-application-model.md)一文。

服務分為兩種：

- **無狀態：**當服務的持續狀態是儲存在 Azure 儲存體、Azure SQL Database、Azure DocumentDB 等外部儲存服務時，使用無狀態服務。當服務完全沒有持續性儲存體時，也應該使用無狀態服務。以計算機服務為例，首先要傳遞值給服務，然後服務用這些值執行計算並傳回結果。

- **具狀態：**當您要讓 Service Fabric 透過其 Reliable Collections 或 Reliable Actors 程式設計模型管理您的服務狀態，則使用具狀態服務。建立具名服務時，指定您想要讓狀態分散到多少個分割 (提供擴充性)，並指定要在所有節點上複寫狀態幾次 (提供可靠性)。每個具名服務都有一個主要複本和多個次要複本。寫入主要複本中，即可修改您的具名服務的狀態；然後 Service Fabric 將此狀態複寫到所有的次要複本，讓您的狀態維持同步。當主要複本失敗時，Service Fabric 會自動偵測到此現象，將現有的次要複本升級為主要複本，然後建立新的次要複本。

**服務封裝**：磁碟目錄，包含此服務類型的 `ServiceManifest.xml` 檔案。這個檔案會參考此服務類型的程式碼、靜態資料和組態封裝。此應用程式類型的 `ApplicationManifest.xml` 檔會參考此服務封裝目錄中的檔案。例如，服務套件可能會參考構成資料庫服務的程式碼、靜態資料和組態封裝。

**具名服務**：建立具名應用程式之後，可以指定服務類型 (使用其名稱/版本)，在叢集內建立應用程式其中一種服務類型的執行個體。需為每個服務類型執行個體指派一個 URI (名稱範圍需在其具名應用程式的 URI 之下)。例如，如果您在具名應用程式 MyNamedApp 內建立 具名服務 MyDatabase，URI 看起來就像這樣：`"fabric:/MyNamedApp/MyDatabase"`。在具名應用程式中，可以建立數個具名服務。每個具名服務可以有自己的分割配置和執行個體/複本計數。

**程式碼封裝**：磁碟目錄，包含此服務類型的執行檔 (通常是 EXE/DLL 檔)。此服務類型的 `ServiceManifest.xml` 檔會參考此程式碼封裝目錄中的檔案。建立具名服務時，系統會將程式碼封裝中的檔案複製到 Service Fabric 選取用來執行具名服務的節點，然後程式碼開始執行。程式碼封裝執行檔分成兩種：

- **來賓執行檔**：在主機作業系統 (Windows 或 Linux) 上以原樣執行的執行檔。也就是說，這些執行檔未連結至或參考任何 Service Fabric 執行階段檔案作業，因此不會使用任何 Service Fabric 程式設計模型。這些執行檔都無法利用某些 Service Fabric 功能，例如端點探索的命名服務，且無法專門報告每個服務執行個體的負載度量。

- **服務主機執行檔**：這些執行檔藉由連結至 Service Fabric 執行階段檔案的方式使用 Service Fabric 程式設計模型。這會將執行檔的程式碼部分繫結到 Service Fabric，進而啟用其他功能。例如，具名服務執行個體可以在 Service Fabric 命名服務註冊端點，也可以報告負載度量。

**資料封裝**：磁碟目錄，包含此服務類型的靜態唯讀資料檔 (通常是相片、音訊和視訊檔)。此服務類型的 `ServiceManifest.xml` 檔會參考此資料封裝目錄中的檔案。建立具名服務時，系統會將資料封裝中的檔案複製到 Service Fabric 選取用來執行具名服務的節點，然後程式碼開始執行；程式碼現在已經可以存取這些資料檔。

**組態封裝**：磁碟目錄，包含此服務類型的靜態唯讀組態檔 (通常是文字檔)。此服務類型的 `ServiceManifest.xml` 檔會參考此組態封裝目錄中的檔案。建立具名服務時，系統會將組態封裝中的檔案複製到 Service Fabric 選取用來執行具名服務的節點，然後程式碼開始執行；程式碼現在已經可以存取這些組態檔。

**分割配置**：建立具名的服務時，要指定分割配置。含有大量狀態的服務會跨分割切割其資料，所以服務是分散在叢集的節點上。這使得具名服務的狀態可以擴充。在分割內，無狀態的具名服務會有執行個體，而具狀態的具名服務則有複本。通常，無狀態具名服務只會有 1 個分割，因為它們有沒有內部狀態。分割執行個體提供可用性；如果一個執行個體失敗，其他執行個體會繼續正常運作，接著 Service Fabric 會建立新的執行個體。具狀態的具名服務會在複本中維持其狀態，且每個分割都有自己的複本集，其中包含保持同步的所有狀態。複本失敗失敗時，Service Fabric 會從現有複本建立新的複本。

如需詳細資訊，請閱讀[分割 Service Fabric Reliable Services](service-fabric-concepts-partitioning.md)。

### 系統服務
有在每個叢集中建立的系統服務，用來提供 Service fabric 的平台功能。

**命名服務**：每個 Service Fabric 叢集有一個命名服務，其會將服務名稱解析至叢集中的位置，並可讓您能夠管理服務名稱和屬性。這正如同叢集的網際網路網域名稱服務 (DNS) 一般。使用命名服務，用戶端可以安全地與叢集中的任何節點通訊，來解析服務名稱和位置 (亦即掌握實際機器 IP 位址和目前執行所在的連接埠)。使用通訊用戶端 API 時，儘管應用程式會在叢集內移動 (由於失敗或資源平衡) 或重新調整叢集大小，您也可以開發能夠解析目前網路位置的服務和用戶端。

如需有關使用搭配命名服務運作之用戶端與服務通訊 API 的詳細資訊，請閱讀[與服務通訊](service-fabric-connect-and-communicate-with-services.md)一文。

**映像儲存區服務**︰每個 Service Fabric 叢集都有一個映像儲存區服務，其中保存已部署且版本化的應用程式封裝。您必須將應用程式封裝的內容複製到映像存放區，然後在該應用程式封裝內註冊該應用程式類型。佈建應用程式類型後，您可以建立其具名應用程式。只有在刪除應用程式類型的所有具名應用程式之後，才可以從映像存放區取消註冊該應用程式類型。

如需有關將應用程式部署至映像存放區的詳細資訊，請閱讀[部署應用程式](service-fabric-deploy-remove-applications.md)一文。

### 內建的程式設計模型
有一些 .NET Framework 程式設計模型可讓您建置 Service Fabric 服務：

**Reliable Services**：用於建置無狀態和具狀態服務的 API。具狀態服務將其狀態儲存在 Reliable Collections 中 (例如字典或佇列)。您也可以插入各種不同的通訊堆疊，例如 Web API、Windows Communication Foundation (WCF)。

**Reliable Actors** - 可透過虛擬的 Actor 程式設計模型，建置無狀態和具狀態物件的 API。當您有多個獨立的計算/狀態單位時，此模型相當實用。由於此模型使用回合式執行緒模型，因此最好避免向外呼叫其他動作項目或服務的程式碼，因為直到個別動作項目的所有輸出要求完成，動作項目才能處理其他連入要求。

如需詳細資訊，請閱讀[為服務選擇程式設計模型](service-fabric-choose-framework.md)一文。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
深入了解 Service Fabric

- [Service Fabric 概觀](service-fabric-overview.md)
- [為何要用微服務方式建置應用程式？](service-fabric-overview-microservices.md)
- [應用程式案例](service-fabric-application-scenarios.md)

<!---HONumber=AcomDC_0330_2016-->