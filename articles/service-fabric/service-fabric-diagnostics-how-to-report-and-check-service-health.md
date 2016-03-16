<properties
   pageTitle="使用 Service Fabric 回報和檢查健康情況 | Microsoft Azure"
   description="了解如何使用 Azure Service Fabric 所提供的健康情況監視工具，從您的服務程式碼傳送健康情況報告及檢查您服務的健康情況。"
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="toddabel"/>


# 回報和檢查服務健康情況
當您的服務發生問題時，您必須能夠快速偵測問題，才能回應並修正所有產生的事件和中斷。透過從您的服務程式碼向 Service Fabric 健康情況管理員回報問題和失敗，您便可以使用 Service Fabric 提供的標準健康情況監視工具來檢查健康狀態。

本文將逐步引導您完成將健康情況報告新增至服務的範例，並說明如何使用 Service Fabric 所提供的工具來檢查健康狀態。本文旨在快速介紹 Service Fabric 中的健康情況監視功能。如需更詳細的資訊，您可以從本文結尾的連結開始，閱讀一系列有關健康情況的深入文章。

## 先決條件
您必須安裝下列項目：* Visual Studio 2015 * Service Fabric SDK

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

6. 您也可以使用 PowerShell 來檢查健康情況。您可以使用 ```Get-ServiceFabricApplicationHealth``` 來檢查應用程式的健康情況，以及使用 ```Get-ServiceFabricServiceHealth``` 來檢查服務健康情況。該相同應用程式的健康情況報告在 PowerShell 中看起來像這樣。![PowerShell 中狀況良好的應用程式](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## 將自訂健康情況事件新增至您的服務程式碼
Service Fabric Visual Studio 專案範本包含範例程式碼。以下步驟說明如何從您的服務程式碼回報自訂健康情況事件。這類報告會自動顯示在 Service Fabric 所提供之健康情況監視的標準工具中，例如 Service Fabric 總管、Azure 入口網站健康情況檢視以及 PowerShell。

1. 在 Visual Studio 中重新開啟您在上面建立的應用程式，或從 Visual Studio 範本使用具狀態的服務建立新應用程式。
2. 開啟 **Stateful1.cs** 檔案。尋找 `var myDictionary` 的宣告，然後將以下程式碼新增在緊接於 `var myDictionary` 宣告之後。稍後將會使用這裡建立的 `fabricClient` 物件來回報健康情況。

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    另外，也請將此命名空間新增至 **Stateful1.cs** 檔案。

    ```csharp
    using System.Fabric;
    ```

4. 接著，查詢 *RunAsync* 方法中的呼叫 `myDictionary.TryGetValueAsync`。您可以看到這會傳回存放目前計數器值的 `result`，因為此應用程式的主要邏輯是要讓計數能夠執行。如果這是一個實際的應用程式且缺乏結果代表失敗，則您會想要向健康情況管理員回報該情況。
5. 若要回報代表失敗的缺少結果健康情況事件，請在 `myDictionary.TryGetValueAsync` 呼叫之後新增以下程式碼。我們會以 `StatefulServiceReplicaHealthReport` 的形式回報該事件，因為這是回報自具狀態服務。傳遞給報告事件的 PartitionId 和 ReplicaId 將可協助您在其中一個「健康情況監視」工具中看到此報告時識別此報告的來源。因為部署的具狀態服務可以有多個資料分割，而每一個資料分割又可以有多個複本，所以很重要。`HealthInformation` 參數會儲存所要回報之健康情況問題的相關資訊。請將此命名空間新增至 **Stateful1.cs** 檔案。

    ```csharp
    using System.Fabric.Health;
    ```

    將此程式碼新增在 `myDictionary.TryGetValueAsync` 呼叫之後。

    ```csharp
    if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));

        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. 讓我們來模擬此失敗並看它顯示在健康情況監視工具中。若要模擬此失敗，請將您在上方新增的健康情況回報程式碼中的第一行標記成註解。將第一行標記成註解之後，程式碼會看起來如以下所示。現在，這就會在每次 RunAsync 執行時引發此健康情況報告。進行變更之後，請使用 **F5** 來執行應用程式。

    ```csharp
    //if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));

        var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

6. 在應用程式執行之後，開啟 [Service Fabric 總管] 來檢查應用程式的健康情況。這次，[Service Fabric 總管] 會將應用程式顯示為狀況不良。這是因為我們在上方新增的程式碼所報告的錯誤所致。![Service Fabric 總管中狀況不良的應用程式](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. 如果您在 [Service Fabric 總管] 的樹狀結構檢視中選取主要複本，您將會看到它也將健康情況顯示為發生錯誤。也會顯示已新增至程式碼中 `HealthInformation` 參數的健康情況報告詳細資料。除了 Azure 入口網站，您也可以在 PowerShell 中看到相同的健康情況報告。![Service Fabric 總管中的複本健康情況](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

此報告將會保留在健康情況管理員中，直到被另一份報告取代或此複本被刪除為止。由於我們並未在 HealthInformation 物件中設定此健康情況報告的 TimeToLive，所以此報告將永不過期。若要能夠查詢和回報健康情況，FabricClient 必須在具備系統管理員權限的程序中。

## 後續步驟
[深入了解 Service Fabric 健康情況](service-fabric-health-introduction.md)

<!---HONumber=AcomDC_0224_2016-->