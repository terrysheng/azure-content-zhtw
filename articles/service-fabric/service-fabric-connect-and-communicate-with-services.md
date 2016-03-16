<properties
   pageTitle="連接至 Azure Service Fabric 中的服務並與其進行通訊 | Microsoft Azure"
   description="了解如何連接至 Service Fabric 應用程式中的服務並與其進行通訊。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mfussell"
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

# 與服務通訊
在微服務的世界中，整體解決方案由許多不同的服務組成，每一個服務用來執行特定的工作。這些微服務會彼此進行通訊以啟用端對端工作流程。有些用戶端應用程式會連接到服務並與其通訊。本文件將討論 Azure Service Fabric 如何讓您輕鬆設定與您使用 Service Fabric 所撰寫服務進行通訊。

## 重要概念
當您設定與服務的通訊時，您需要了解某些重要概念。

### 通訊程序將表示為主從
Service Fabric 通訊 API 代表主從類型的互動，甚至包含在相同叢集中所執行兩個服務之間的互動。目標服務將會連接到用戶端來源，或其他服務作為伺服器並接聽傳入要求。用戶端可以是叢集中的另一個服務，用來連接到伺服器並進行呼叫。

### 服務的移動
在分散式系統中，您所執行的服務執行個體可能會隨著時間從一部電腦移動到另一部電腦。發生這種情形的原因有很多，包括為了資源平衡、升級、容錯移轉、相應放大而使用負載計量進行設定時的資源平衡。移動的影響在於，服務執行個體的端點位址可以變更。若要設定與服務執行個體的通訊，則需要執行下列迴圈。若您使用 Service Fabric 所提供的通訊 API，系統將從您擷取這些詳細資料。

* **解析：**Service Fabric 中的所有已命名服務執行個體，皆如 URL 擁有唯一的名稱。例如，`"fabric:/MyApplication/MyService"` 為已命名服務的固定名稱。每個已命名服務執行個體也會公開端點，當該以命名服務執行個體移動時，您可以變更端點。這類似於具有常數 URL 的網站，但 IP 位址可能會變更。並且類似於 Web 上的 DNS，可解析連接至 IP 位址的網站 URL，Service Fabric 具有名為命名服務的系統服務，可解析連接至端點的 URI。此步驟涉及解析連接至端點的服務執行個體 URI。

* **連接**：一旦已命名服務 URI 已解析至端點位址，下一步是嘗試與該服務進行連接。若端點位址因為服務移動而已變更，則此連接可能會失敗，這可能是由電腦故障或資源平衡等原因所導致。

* **重試**：若解析之前連線嘗試失敗，則必須重試連接步驟，且此循環會重複執行直到連接成功為止。

## 通訊 API 選項
作為 Service Fabric 的一部分，我們提供幾個不同的通訊 API 選項。最適合您選項的決定取決於如何選擇程式設計模型、通訊架構以及用來撰寫您服務的程式語言。

### 可靠動作項目的通訊
針對使用 Reliable Actors API 撰寫的服務，系統會擷取所有通訊詳細資料。在 ActorProxy 上呼叫方法時會進行通訊，所以您可以停止閱讀這裡的文章。

### 可靠服務的通訊選項
若您的服務使用可靠服務 API 寫入，則有幾個不同的選項。您選擇的通訊協定將會引導您選擇可能使用的 Service Fabric 通訊 API。

* **沒有特定通訊協定**：如果您沒有特定的通訊架構選擇，則但您想要快速啟動並執行，則適合您的理想選項為[預設堆疊](service-fabric-reliable-services-communication-remoting.md)，可允許類似的模型作為動作項目通訊模型。若要開始使用服務通訊，這是最簡單且快速的方式。其提供強型別 RPC 通訊，可擷取多數移出的通訊詳細資料，以便在程式碼中的遠端服務上叫用方法，看起來像在本機物件執行個體上呼叫方法。這些類別會在設定通訊通道時擷取解析、連接、重試和錯誤處理，並允許基於通訊模型的方法呼叫。為此，在伺服器端上使用 `ServiceRemotingListener` 類別，而在通訊的用戶端上使用 `ServiceProxy` 類別。

* **HTTP**：若要利用 HTTP 式通訊提供的彈性，您可以使用 Service Fabric 通訊 API，以便在解析、連接和重試仍從您擷取的邏輯時允許定義通訊機制。例如您可以使用 Web API 來指的通訊機制並利用 [`ICommunicationClient` 和 `ServicePartitionClient` 類別](service-fabric-reliable-services-communication.md)來設定通訊。
* **WCF**：若您有現有的程式碼且使用 WCF 作為通訊架構，則您可以針對伺服器端使用 `WcfCommunicationListener` 類別，並針對用戶端使用 `WcfCommunicationClient` 和 `ServicePartitionClient` 類別。如需詳細資訊，請參閱本文中[通訊堆疊的 WCF 式實作](service-fabric-reliable-services-communication-wcf.md)。

* **其他包含自訂通訊協定的通訊架構**：若您打算使用任何其他包含自訂通訊協定的通訊架構，您可以將 Service Fabric 通訊 API 外掛通訊堆疊，同時系統會從您擷取要探索和連接的所有工作。如需詳細資訊，請參閱本文中 [Reliable Service 通訊模型](service-fabric-reliable-services-communication.md)。

### 使用不同程式設計語言撰寫之服務之間的通訊
所有 Service Fabric 通訊 API 目前僅在 C# 中提供使用。這表示若服務以其他程式設計語言撰寫，例如 Java 或 Node.JS，則您必須撰寫自己的通訊機制。

## 後續步驟
* [可靠服務架構所提供的預設通訊堆疊 ](service-fabric-reliable-services-communication-remoting.md)
* [可靠服務通訊模型](service-fabric-reliable-services-communication.md)
* [開始使用 Microsoft Azure Service Fabric Web API 服務與 OWIN 自我裝載](service-fabric-reliable-services-communication-webapi.md)
* [適用於 Reliable Services 的 WCF 式通訊堆疊](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0224_2016-->