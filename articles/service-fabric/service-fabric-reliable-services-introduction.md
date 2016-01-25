<properties
   pageTitle="Service Fabric Reliable Services 程式設計模型概觀 | Microsoft Azure"
   description="深入了解 Service Fabric 可靠的服務的程式設計模型，並開始撰寫自己的服務。"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="jessebenson; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/13/2016"
   ms.author="masnider;jesseb"/>

# Reliable Services 概觀
Azure Service Fabric 可簡化撰寫和管理無狀態與具狀態的 Reliable Services。這份文件會討論：

- 無狀態和具狀態之服務的 Reliable Services 程式設計模型。
- 撰寫 Reliable Services 時必須進行的選擇。
- Reliable Services 使用時機及撰寫方式的一些案例和範例。

可靠的服務是 Service Fabric 上可用的其中一種程式設計模型。如需 Reliable Actors 程式設計模型的詳細資訊，請參閱 [Service Fabric Reliable Actors 簡介](service-fabric-reliable-actors-introduction.md)。

在 Service Fabric 中，服務包含組態、應用程式程式碼，以及 (選擇性的) 狀態。

Service Fabric 透過 [Service Fabric 應用程式管理](service-fabric-deploy-remove-applications.md)管理服務的存留期間，從佈建和部署一直到升級和刪除。

## 什麼是 Reliable Services？
Reliable Service 提供您簡易、功能強大、最高階的程式設計模型，以協助您針對應用程式的重要項目進行表達。使用 Reliable Services 程式設計模型，您會得到：

- 對於具狀態服務，Reliable Services 程式設計模型可讓您使用可靠的集合，直接在服務內以一致且可靠地方式儲存狀態。用過 C# 集合的使用者一定會熟悉這一組簡單的高可用性集合類別。傳統上，服務需要外部系統來進行可靠狀態管理。有了可靠的集合，您可以將狀態儲存在您的計算旁邊，並且具有您預期高度可用的外部存放區會具備的相同高可用性和可靠性，以及共置計算和狀態所提供的額外延遲改善。

- 類似您所習慣使用之程式設計模型的簡單模型，以便執行您自己的程式碼。您的程式碼會具有定義完善的切入點和容易管理的生命週期。

- 隨插即用的通訊模型。使用您選擇的傳輸方式，如包含 [Web API](service-fabric-reliable-services-communication-webapi.md) 的 HTTP、WebSockets、自訂 TCP 通訊協定等。Reliable Services 提供一些很棒的現成選項供您使用，或者您可以提供您自己的選項。

## Reliable Services 有什麼不同之處？
Service Fabric 中的 Reliable Services 與您之前撰寫的服務不同。Service Fabric 提供可靠性、可用性、一致性和延展性。

- **可靠性**--即使在您的電腦可能會失敗或遭受網路問題的不可靠環境中，您的服務仍會保持運作。

- **可用性**--您的服務可供存取且會有回應 (這不表示您不能有無法從外面找到或存取的服務)。

- **延展性**--服務與特定硬體分離，可以視需要透過加入或移除硬體或虛擬資源而成長或壓縮。服務可以輕鬆分割 (特別是在具狀態的情況下)，以確保服務的獨立部分可以獨立調整和回應失敗。最後，Service Fabric 鼓勵輕量服務，它允許在單一處理序內佈建數千個服務，而不需要或讓整個作業系統執行個體專屬於特定工作負載的單一執行個體。

- **一致性**--此服務中儲存的任何資訊可以保證是一致的 (這只適用於具狀態服務，稍後有更多詳細資訊)

## 服務生命週期
無論您的服務是具狀態還是無狀態，Reliable Services 會提供簡單的生命週期，可讓您快速插入您的程式碼，並開始著手。您只需要實作一個或兩個方法，即可讓您的服務啟動並執行。

- **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - 這是服務定義它要使用之通訊堆疊的地方。通訊堆疊 (例如 [Web API](service-fabric-reliable-services-communication-webapi.md)) 可定義服務的一或多個接聽端點 (用戶端將如何存取它)。它也會定義所顯示的訊息最終會如何與服務程式碼的其餘部分互動。

- **RunAsync** - 這是您的服務執行其商務邏輯的地方。所提供的取消語彙基元是針對該工作何時應該停止的訊號。比方說，如果您的服務需要不斷從可靠的佇列提取訊息並加以處理，這會是該工作會發生的位置。

### 服務啟動

Reliable Services 的生命週期中的主要事件如下：

1. 建構服務物件 (衍生自無狀態服務或具狀態服務的項目)。

2. 呼叫 `CreateServiceReplicaListeners`/`CreateServiceInstanceListeners` 方法，讓服務有機會傳回其選擇的一或多個通訊接聽程式。
  - 請注意這是選擇性的，雖然大部分的服務會直接公開某個端點。

3. 一旦建立通訊接聽程式，即會予以開啟。
  - 通訊接聽程式有一個稱為 `OpenAsync()` 的方法，此時會呼叫該方法並傳回服務的接聽位址。如果您的 Reliable Services 使用其中一個內建 ICommunicationListener，便會為您處理。

4. 通訊接聽程式為開啟之後，會在主要服務上呼叫 `RunAsync()` 方法。
  - 請注意，`RunAsync()` 是選擇性的。如果服務所有工作都只直接進行，以回應使用者呼叫，則它不需要實作 `RunAsync()`。

### 服務關閉

在關閉服務時 (要刪除、升級或移動)，呼叫順序是鏡像方式：先取消 `RunAsync()` 所持有的取消權杖，然後在通訊接聽程式上呼叫 `CloseAsync()`。

以下是有關於關閉具狀態服務時的一些重要注意事項：

- 在傳回 `CloseAsync` 和 `RunAsync` 後，Service Fabric 才會將服務的另一個複本提升為主要狀態。如果您要使用內建通訊接聽程式，會為您處理 `CloseAsync` 方法。

- 雖然從這些方法傳回沒有時間限制，但您會立即喪失寫入到可靠的集合的能力，並因此無法完成任何實際工作。建議您盡快在收到取消要求後傳回。

## 範例服務
了解這個程式設計模型之後，讓我們來快速看看兩個不同的服務，了解這些部分如何彼此搭配運作。

### 無狀態的可靠的服務
無狀態服務是服務內實際上沒有維護狀態的服務，或是存在的狀態是完全可處置，而且不需要同步處理、複寫、持續性或高可用性。

例如，想想沒有記憶體且會接收要一次執行之所有項和作業的計算機。

在此情況下，服務的 RunAsync() 可以是空的，因為沒有服務需要進行的背景工作處理。建立計算機服務時，它會傳回 ICommunicationListener (例如 [Web API](service-fabric-reliable-services-communication-webapi.md))，其會在某個連接埠上開啟接聽端點。此接聽端點將會連結到不同的方法 (範例："Add(n1, n2)")，其會定義計算機的公用 API。

從用戶端進行呼叫時，會叫用適當的方法，且計算機服務會在所提供的資料上執行作業，並傳回結果。它不會儲存任何狀態。

不儲存任何內部狀態會讓此範例中的計算機變得很簡單。不過大多數服務並不是真正無狀態。相反地，它們是將狀態外部化到其他某些存放區 (例如，任何依賴在備份存放區或快取中保留工作階段狀態的 Web 應用程式便不是完全無狀態)。

Service Fabric 中常見的無狀態服務使用範例是做為前端，其公開 Web 應用程式的公用 API。前端服務接著和具狀態服務交談，以完成使用者的要求。在此情況下，用戶端的呼叫會導向至無狀態服務接聽的已知連接埠 (例如 80)。這個無狀態服務收到呼叫，並判斷呼叫是否來自受信任的合作對象，以及其預定為哪些服務。然後，無狀態服務將呼叫轉送至正確的具狀態服務分割，並等候回應。當無狀態服務收到回應時，它會回覆原始用戶端。

### 具狀態可靠的服務
具狀態服務必須有某部分的狀態保持一致且存在，服務才能運作。假設有一個服務不斷地根據某個值收到的更新，計算它的滾動平均。若要這樣做，它必須擁有目前需要處理的連入要求集，以及目前的平均。擷取、處理並將資訊儲存在外部存放區 (例如現在的 Azure Blob 或資料表存放區) 的任何服務都是具狀態。它只將狀態保留在外部狀態存放區中。

現在的大部分服務會在外部儲存其狀態，因為外部存放區為該狀態提供可靠性、可用性、延展性和一致性。在 Service Fabric 中，具狀態服務不需要外部儲存其狀態，因為 Service Fabric 會為服務程式碼和服務狀態處理這些需求。

例如，假設我們想要撰寫的服務接受一系列需要在影像上執行的轉換，以及要轉換的影像的要求。針對這個服務，它會傳回通訊接聽程式 (假設是 Web API)，這會開啟通訊連接埠，並允許透過例如 `ConvertImage(Image i, IList<Conversion> conversions)` 的 API 提交。在這個 API 中，服務可能擷取資訊並將要求儲存在可靠的佇列，然後傳回某個權杖給用戶端，以便追蹤要求 (因為要求可能需要一些時間)。

在這個服務中，RunAsync 可能會更複雜。服務在其 RunAsync 內會有一個迴圈，將要求從 IReliableQueue 提取出來、執行列出的轉換，然後將結果儲存在 IReliableDictionary，以便當用戶端回來時可以取得轉換後的影像。為了確保即使發生失敗影像也不會遺失，這個 Reliable Services 會從佇列提取、執行轉換，並將結果儲存在交易中。在此情況下，訊息實際上只會從佇列中移除，而且當轉換完成時，結果會儲存在結果字典中。如果中途某個項目失敗 (例如此程式碼執行個體執行所在的電腦)，要求會保留在佇列中等候再次處理。

關於這個服務，要注意一件事，就是它聽起來像是一般的 .NET 服務。唯一的差別在於使用的資料結構 (IReliableQueue 和 IReliableDictionary) 是由 Service Fabric 提供，因此高度可靠、可用且一致。

## 使用 Reliable Services API 的時機
如果下列任何一項描述出您的應用程式服務需求，那麼便應該考慮 Reliable Services API：

- 您需要提供跨越多個狀態單位 (例如訂單和訂單行項目) 的應用程式行為。

- 您的應用程式狀態可以自然地模型化，做為可靠的字典和佇列。

- 您的狀態必須是高度可用且低延遲存取。

- 您的應用程式必須跨越一個或多個可靠的集合，控制交易作業的並行處理或資料粒度。

- 您想要管理通訊或控制服務的資料分割配置。

- 您的程式碼需要無限制執行緒的執行階段環境。

- 您的應用程式需要在執行階段以動態方式建立或終結可靠字典或佇列。

- 您需要以程式設計方式為您的服務狀態控制 Service Fabric 提供的備份和還原功能*。

- 您的應用程式需要維護其狀態單位的變更歷程記錄*。

- 您想要開發或使用協力廠商開發的自訂狀態提供者*。

> [AZURE.NOTE]*公開上市版 SDK 將提供這些功能。


## 後續步驟
+ [Reliable Services 快速入門](service-fabric-reliable-services-quick-start.md)
+ [Reliable Services 的進階用法](service-fabric-reliable-services-advanced-usage.md)
+ [Reliable Actors 程式設計模型](service-fabric-reliable-actors-introduction.md)

<!---HONumber=AcomDC_0114_2016-->