<properties
   pageTitle="使用 Service Fabric 回報和檢查健康情況 | Microsoft Azure"
   description="了解如何使用 Azure Service Fabric 所提供的健康情況監視工具，從您的服務程式碼傳送健康情況報告及檢查您服務的健康情況。"
   services="service-fabric"
   documentationCenter=".net"
   authors="punewa"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/18/2016"
   ms.author="toddabel"/>

# 回報和檢查服務健康情況
當您的服務發生問題時，您必須能夠快速偵測問題，才能回應並修正所有產生的事件和中斷。透過從您的服務程式碼向 Service Fabric 健康情況管理員回報問題和失敗，您便可以使用 Service Fabric 提供的標準健康情況監視工具來檢查健康狀態。

有兩種方式可讓您回報服務的健康情況。

1. 使用 [Partition](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.aspx) 或 [CodePackageActivationContext](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.aspx) 物件。使用 `Partition` 和 `CodePackageActivationContext` 物件，您可以在屬於目前內容一部分的項目中回報健康情況。比方說，做為複本一部分執行的程式碼只能回報該複本、其所屬的分割區，以及其所屬應用程式的健康情況。

2. 使用 `FabricClient`。只有在叢集不是[安全的](service-fabric-cluster-security.md)或者服務是以系統管理員權限執行時，您才能使用 `FabricClient`，從服務程式碼回報健康情況。在大部分的真實案例中都不會發生此情況。您可以使用 FabricClient，回報任何屬於叢集一部分之實體的健康情況。但在理想的情況下，服務程式碼應該只會傳送與其本身健康情況相關的報告。

本文將逐步引導您完成從服務程式碼回報健康情況的範例，並示範如何使用 Service Fabric 所提供的工具來檢查健康狀態。本文旨在快速介紹 Service Fabric 中的健康情況監視功能。如需更詳細的資訊，您可以從本文結尾的連結開始，閱讀一系列有關健康情況的深入文章。

## 必要條件
您必須安裝下列項目：

   * Visual Studio 2015
   * Service Fabric SDK

## 建立本機的安全開發人員叢集
以系統管理員權限啟動 PowerShell 並執行下列命令。

![建立安全的開發人員叢集](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## 部署應用程式並檢查其健康情況
若要部署應用程式並檢查其健康情況，請依照下列這些步驟：

1. 以系統管理員身分啟動 Visual Studio。

2. 為具狀態服務建立專案。

    ![建立與具狀態服務搭配使用的 Service Fabric 應用程式](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. 按 **F5** 以在偵錯模式中執行應用程式。應用程式將會部署至本機叢集。

4. 在應用程式執行之後，於 [本機叢集管理員] 系統匣應用程式上按一下滑鼠右鍵，然後從操作功能表中選擇 [管理本機叢集]，以啟動 [Service Fabric 總管]。

    ![從系統匣啟動 Service Fabric 總管](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. 應用程式健康情況應該會如下圖所示。此時，應用程式應該狀況良好而沒有任何錯誤。

    ![Service Fabric 總管中狀況良好的應用程式](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. 您也可以使用 PowerShell 來檢查健康情況。您可以使用 ```Get-ServiceFabricApplicationHealth``` 來檢查應用程式的健康情況，以及使用 ```Get-ServiceFabricServiceHealth``` 來檢查服務健康情況。適用於相同應用程式的健康情況報告在 PowerShell 中看起來像這樣。

    ![PowerShell 中狀況良好的應用程式](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## 將自訂健康情況事件新增至您的服務程式碼
Service Fabric Visual Studio 專案範本包含範例程式碼。以下步驟說明如何從您的服務程式碼回報自訂健康情況事件。這類報告會自動顯示在 Service Fabric 所提供之健康情況監視的標準工具中，例如 Service Fabric 總管、Azure 入口網站健康情況檢視以及 PowerShell。

1. 在 Visual Studio 中重新開啟您在上面建立的應用程式，或從 Visual Studio 範本使用具狀態的服務建立新應用程式。

2. 接下來，開啟 **Stateful1.cs** 檔案，並查閱 *RunAsync* 方法中的呼叫 `myDictionary.TryGetValueAsync`。您可以看到這會傳回存放目前計數器值的 `result`，因為此應用程式的主要邏輯是要讓計數能夠執行。如果這是一個實際的應用程式且缺乏結果代表失敗，則您會想要透過健康情況標示該情況。

3. 若要回報代表失敗的缺少結果健康情況事件，請在 `myDictionary.TryGetValueAsync` 呼叫之後新增以下程式碼。我們會回報複本健康情況，因為這是從具狀態服務回報的。`HealthInformation` 參數會儲存所要回報之健康情況問題的相關資訊。請將此命名空間新增至 **Stateful1.cs** 檔案。

    ```csharp
    using System.Fabric.Health;
    ```

    將此程式碼新增在 `myDictionary.TryGetValueAsync` 呼叫之後。

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```

    針對無狀態服務，請使用下列程式碼。

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```

    如果您的服務是以系統管理員權限執行，或者叢集不是[安全的](service-fabric-cluster-security.md)，您也可以使用 FabricClient 來回報健康情況，如下所示。

    在 `var myDictionary` 宣告之後建立 FabricClient。

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    此外，將此程式碼新增至 `myDictionary.TryGetValueAsync` 呼叫之後。

    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

4. 讓我們來模擬此失敗並看它顯示在健康情況監視工具中。若要模擬此失敗，請將您在上方新增的健康情況回報程式碼中的第一行標記成註解。將第一行標記成註解之後，程式碼會看起來如以下所示。現在，這就會在每次 RunAsync 執行時引發此健康情況報告。進行變更之後，請使用 **F5** 來執行應用程式。

    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```

5. 在應用程式執行之後，開啟 [Service Fabric 總管] 來檢查應用程式的健康情況。這次，[Service Fabric 總管] 會將應用程式顯示為狀況不良。這是因為我們在上方新增的程式碼所報告的錯誤所致。

    ![Service Fabric 總管中狀況不良的應用程式](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

6. 如果您在 [Service Fabric 總管] 的樹狀結構檢視中選取主要複本，您將會看到它也將健康情況顯示為發生錯誤。也會顯示已新增至程式碼中 `HealthInformation` 參數的健康情況報告詳細資料。除了 Azure 入口網站，您也可以在 PowerShell 中看到相同的健康情況報告。

    ![Service Fabric 總管中的複本健康情況](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

此報告將會保留在健康情況管理員中，直到被另一份報告取代或此複本被刪除為止。由於我們並未在 HealthInformation 物件中設定此健康情況報告的 TimeToLive，所以此報告將永不過期。

建議您應該在最細微的層級上回報健康情況，在上述案例中為複本。您也可以回報 `Partition` 的健康情況。

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

若要回報 `Application`、`DeployedApplication` 和 `DeployedServicePackage` 的健康情況，請使用 `CodePackageActivationContext`

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## 後續步驟
[深入了解 Service Fabric 健康情況](service-fabric-health-introduction.md)

<!---HONumber=AcomDC_0323_2016-->