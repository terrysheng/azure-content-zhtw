<properties
   pageTitle="開始使用 Reliable Services | Microsoft Azure"
   description="概述使用無狀態與具狀態服務來建立 Microsoft Azure Service Fabric 應用程式。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/15/2015"
   ms.author="vturecek"/>

# 開始使用 Service Fabric Reliable Services

Azure Service Fabric 應用程式包含一個或多個執行您的程式碼的服務。本指南說明如何使用 [Reliable Services](service-fabric-reliable-services-introduction.md) 同時建立無狀態與具狀態的 Service Fabric 應用程式。

## 建立無狀態服務

無狀態服務是目前在雲端應用程式中做為基準的服務類型。服務會視為無狀態，因為服務本身不包含需要可靠地儲存或設為高度可用的資料。如果無狀態服務的執行個體關閉，其所有內部狀態都會遺失。在此類型的服務中，狀態必須保存到外部存放區，例如 Azure 資料表或 SQL 資料庫中，才能成為高度可用且可靠。

以管理員身分啟動 Visual Studio 2015 RC，並建立新的 Service Fabric 應用程式專案，命名為 HelloWorld：

![使用新增專案對話方塊來建立新的 Service Fabric 應用程式](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

然後建立名為 HelloWorldStateless 的無狀態服務專案：

![在第二個對話方塊中，建立無狀態服務專案](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

您的方案現在包含兩個專案：

 - HelloWorld。這是包含您服務的應用程式專案。它也包含描述應用程式的應用程式資訊清單，以及一些幫助您部署應用程式的 PowerShell 指令碼。
 - HelloWorldStateless。這是服務專案。它包含無狀態服務實作。


## 實作服務

在服務專案中開啟 **HelloWorldStateless.cs** 檔案。在 Service Fabric 中，服務可以執行任何商務邏輯。服務 API 為您的程式碼提供兩個進入點：

 - 開放式的進入點方法，稱為 RunAsync，您可以在這裡開始執行任何工作負載。包含長時間執行的計算工作負載。

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - 通訊進入點，您可以在這裡插入選擇的通訊堆疊，例如 ASP.NET Web API。這就是您可以開始接收來自使用者和其他服務要求的地方。

```C#
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

在本教學課程中，我們將著重在 `RunAsync()` 進入點方法。這就是您可以立即開始執行程式碼的地方。專案範本包括 `RunAsync()` 的實作範例，它會遞增一個循環計數。

> [AZURE.NOTE]如需有關如何使用通訊堆疊的詳細資訊，請參閱 [Service Fabric Web API 服務與 OWIN 自我裝載](service-fabric-reliable-services-communication-webapi.md)


### RunAsync

```C#
protected override async Task RunAsync(CancellationToken cancelServiceInstance)
{
    // TODO: Replace the following sample code with your own logic.

    int iterations = 0;
    // This service instance continues processing until the instance is terminated.
    while (!cancelServiceInstance.IsCancellationRequested)
    {

        // Log what the service is doing
        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", iterations++);

        // Pause for 1 second before continue processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServiceInstance);
    }
}
```

當服務的執行個體已放置並且可以執行時，平台會呼叫這個方法。針對無狀態服務，這僅表示開啟服務執行個體的時間。會提供取消語彙基元以協調服務執行個體何時需要關閉。在 Service Fabric 中，服務執行個體的開啟/關閉循環可能會在整個服務存留期中發生許多次。發生這種情形的原因有很多，包括：

- 系統可能為了資源平衡移動您的服務執行個體。
- 在您的程式碼中發生錯誤。
- 應用程式或系統已升級。
- 基礎硬體發生中斷。

此協調流程是由系統管理，可讓您的服務維持高度可用且正確平衡。

`RunAsync()` 在它自己的工作中執行。請注意，在上述的程式碼片段中，我們直接跳入 while 迴圈，不需要為您的工作負載排定個別的工作。取消您的工作負載是由所提供的取消語彙基元協調的協同努力。系統會先等待您工作結束 (成功完成、取消或錯誤) 再繼續。系統要求取消時，務必接受取消權杖、完成任何工作，並儘快結束 `RunAsync()`。

在這個無狀態服務範例中，計數會儲存在本機變數。但是，因為這是無狀態服務，所儲存的值只針對其服務執行個體的目前生命週期而存在。當服務移動或重新啟動時，值將會遺失。

## 建立具狀態服務

Service Fabric 導入了一種可設定狀態的新服務。具狀態服務能夠可靠地在服務本身維持狀態，和使用它的程式碼共置。狀態是由 Service Fabric 設為高度可用，而不需要將狀態保存到外部存放區。

若要將計數器值從無狀態轉換成高度可用且持續，即使服務移動或重新啟動亦然，您需要具狀態服務。

在同一個 HelloWorld 應用程式中，您可以加入新的服務，在應用程式專案上按一下滑鼠右鍵並選取 [新增 Fabric Service]。

![將服務加入 Service Fabric 應用程式](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

選取 [具狀態服務] 並將它命名為 HelloWorldStateful。按一下 [確定]。

![使用新增專案對話方塊來建立新的 Service Fabric 具狀態服務](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

您的應用程式現在應該有兩個服務：無狀態服務 HelloWorld 和具狀態服務 HelloWorldStateful。

在 HelloWorldStateful 中開啟 **HelloWorldStateful.cs**，其中包含下列 RunAsync 方法：

```C#
protected override async Task RunAsync(CancellationToken cancelServicePartitionReplica)
{
    // TODO: Replace the following sample code with your own logic.

    // Gets (or creates) a replicated dictionary called "myDictionary" in this partition.
    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    // This partition's replica continues processing until the replica is terminated.
    while (!cancelServicePartitionReplica.IsCancellationRequested)
    {

        // Create a transaction to perform operations on data within this partition's replica.
        using (var tx = this.StateManager.CreateTransaction())
        {

            // Try to read a value from the dictionary whose key is "Counter-1".
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

            // Log whether the value existed or not.
            ServiceEventSource.Current.ServiceMessage(this, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            // If the "Counter-1" key doesn't exist, set its value to 0
            // else add 1 to its current value.
            await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

            // Committing the transaction serializes the changes and writes them to this partition's secondary replicas.
            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is sent to this partition's secondary replicas.
            await tx.CommitAsync();
        }

        // Pause for one second before continuing processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServicePartitionReplica);
    }
}
```

### RunAsync

具狀態服務與無狀態服務具有相同的進入點。主要差異在於能否使用可靠的集合和狀態管理員。具狀態服務中的 `RunAsync()` 運作方式類似於無狀態服務。只不過在具狀態服務中，平台會代替您執行額外的工作，然後才執行 `RunAsync()`。這項工作可包含確保狀態管理員和可靠的集合可以使用。

### 可靠的集合與狀態管理員

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

IReliableDictionary 是一個字典實作，您可以在服務中可靠地儲存狀態。這屬於 Service Fabric 中內建的[可靠的集合](service-fabric-reliable-services-reliable-collections.md)。有了 Service Fabric 和可靠的集合，您可以直接在服務中儲存資料，而不需要外部的持續性存放區。這個方法可讓您的資料具備高可用性。Service Fabric 會藉由為您建立與管理服務的多個複本來完成此作業。它也提供 API 讓管理這些複本和其狀態轉換的複雜性抽象化。

可靠的集合可以儲存任何 .NET 類型 (包括您的自訂類型)，不過有幾個需要注意的事項：

 - Service Fabric 藉由在節點之間複寫狀態，並將它儲存到本機磁碟，來使您的狀態高度可用。這表示所有儲存在可靠的集合中的項目必須可序列化。根據預設，可靠的集合使用 [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) 進行序列化，因此在您使用預設序列化程式時，請務必確定您的類型受到[資料合約序列化程式支援](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx)。

 - 當您在可靠的集合上認可交易時，物件會複寫以獲得高可用性。儲存在可靠集合中的物件會保留在服務中的本機記憶體。這表示您有物件的本機參考。

    很重要的一點是，您不要改變那些物件的本機執行個體而不在交易中的可靠集合上執行更新作業。這是因為那些變更不會自動複寫。

狀態管理員會為您管理可靠的集合。在您的服務中隨時隨地，您只需要以名稱向狀態管理員要求可靠的集合。狀態管理員會確保您取回參考。不建議您將可靠集合執行個體的參考儲存在類別成員變數或屬性中。請特別小心以確保在服務生命週期中隨時將參考設定為執行個體。狀態管理員會為您處理這項工作，並且針對重複造訪最佳化。

### 交易式和非同步作業

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

可靠的集合與其 `System.Collections.Generic` 和 `System.Collections.Concurrent` 對應項目具有許多相同的作業，包括 LINQ。不過，可靠的集合上的作業是非同步的。這是因為具備可靠集合的寫入作業已複寫。關於高可用性，這些作業會傳送到不同節點上的其他服務複本。

它們也支援交易式作業，因此您可以在多個可靠的集合之間維持狀態的一致。比方說，您可能會從可靠佇列清除工作項目佇列、對它執行作業，然後將結果儲存在可靠字典中，全都在單一交易中完成。這會視為不可部分完成的作業，而且它可以保證整個作業都會成功，或是都不會。如果您從佇列取消項目之後，但在您儲存結果之前發生錯誤，那麼會回復整個交易，且項目會保持在佇列中進行處理。

## 執行應用程式

現在我們回到 HelloWorld 應用程式。您現在可以建置並部署您的服務。當您按下 **F5**，您的應用程式會建置並部署至本機叢集。

服務開始執行之後，您可以在 [診斷事件] 視窗中檢視產生的 Windows 事件追蹤 (ETW) 事件。請注意，應用程式中會同時顯示無狀態服務和具狀態服務的事件。您可以按一下 [暫停] 按鈕來暫停串流。然後，您可以藉由展開訊息來檢查訊息的詳細資料。

>[AZURE.NOTE]在您執行應用程式之前，請確定您有執行中的本機開發叢集。查看[入門指南](service-fabric-get-started.md)以取得設定您的本機環境的資訊。

![在 Visual Studio 中檢視診斷事件](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## 後續步驟

[在 Visual Studio 中偵錯 Service Fabric 應用程式](service-fabric-debugging-your-application.md)

[開始使用：Service Fabric Web API 服務與 OWIN 自我裝載 | Microsoft Azure](service-fabric-reliable-services-communication-webapi.md)

[深入了解可靠的集合](service-fabric-reliable-services-reliable-collections.md)

[部署應用程式](service-fabric-deploy-remove-applications.md)

[應用程式升級](service-fabric-application-upgrade.md)

[可靠的服務的開發人員參考資料](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=AcomDC_0121_2016-->