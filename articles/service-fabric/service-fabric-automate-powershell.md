<properties
	pageTitle="使用 PowerShell 自動化 Service Fabric 應用程式管理 | Microsoft Azure"
	description="使用 PowerShell 部署、升級、測試和移除 Service Fabric 應用程式。"
	services="service-fabric"
	documentationCenter=".net"
	authors="rwike77"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-fabric"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="10/09/2015"
	ms.author="ryanwi"/>

# 使用 PowerShell 部署、升級、測試和移除 Service Fabric 應用程式

本文會為您示範如何使用 Powershell，自動化部署、升級、移除和測試 Service Fabric 應用程式的常見工作。

## 必要條件

在繼續進行本文中的工作之前，請務必[安裝執行階段、SDK 和工具](service-fabric-get-started.md)，這也會安裝 ServiceFabric 和 ServiceFabricTestability PowerShell 模組。[啟用 PowerShell 指令碼執行](service-fabric-get-started.md#enable-powershell-script-execution)以及[安裝並啟動本機叢集](service-fabric-get-started.md#install-and-start-a-local-cluster)，以便執行本文中的範例。

本文中的範例使用 [HelloWorldStateful 範例應用程式](https://github.com/Azure/servicefabric-samples/tree/master/samples/Services/VS2015/HelloWorldStateful)。下載和建置範例應用程式。

執行本文中的任何 PowerShell 命令之前，請先使用 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/azure/mt125938.aspx) 連接至本機 Service Fabric 叢集：

```powershell
Connect-ServiceFabricCluster
```

## 工作：部署 Service Fabric 應用程式

在您建置應用程式且已封裝應用程式類型之後，您可以將應用程式部署至 Service Fabric 叢集。首先，在 Visual Studio 中封裝 HelloWorldStateful 應用程式，方法是以滑鼠右鍵按一下 [方案總管] 中的 **HelloWorldStatefulApplication**，然後選取 [封裝]。如需服務和應用程式資訊清單與封裝配置的詳細資訊，請參閱[在 Service Fabric 中模型化應用程式](service-fabric-application-model.md)。部署牽涉到上傳應用程式套件、註冊應用程式類型，以及建立應用程式執行個體。使用本節中的指示，將新的應用程式部署至叢集。

### 步驟 1：上傳應用程式套件
上傳應用程式套件至 ImageStore，會將它放在一個可由內部 Service Fabric 元件存取的位置。應用程式套件包含必要的應用程式資訊清單、服務資訊清單，以及用來建立應用程式和服務執行個體的程式碼/組態/資料封裝。[Copy-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/azure/mt125905.aspx) 命令會上傳封裝。例如：

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\VS2015\HelloWorldStateful\HelloWorldStatefulApplication\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStore -ApplicationPackagePathInImageStore HelloWorldStateful
```

### 步驟 2：註冊應用程式類型
註冊應用程式套件，讓應用程式類型和應用程式資訊清單中宣告的版本可供使用。系統會讀取在上一個步驟中所上傳的封裝，請確認封裝 (相當於本機執行 [Test-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/azure/mt125950.aspx))、處理封裝內容，然後將處理過的封裝複製至內部系統位置。執行 [Register-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125958.aspx) Cmdlet：

```powershell
Register-ServiceFabricApplicationType HelloWorldStateful
```
若要查看在叢集中註冊的應用程式類型，執行 Cmdlet：

```powershell
Get-ServiceFabricApplicationType
```

### 步驟 3：建立應用程式執行個體
透過 [New-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt125913.aspx) 命令，應用程式可以使用已成功註冊的任何應用程式類型版本來進行具現化。每個應用程式名稱的開頭必須為 **fabric:** 配置，並且是每個應用程式執行個體的唯一名稱。應用程式類型名稱和應用程式類型版本在 ApplicationManifest.xml 檔案中宣告。若目標應用程式類型的應用程式資訊清單中已定義任何預設服務，則這些服務也會一併建立。

```powershell
New-ServiceFabricApplication fabric:/HelloWorldStatefulApplication HelloWorldStatefulApplication 1.0.0.0
```

[Get-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt163515.aspx) 命令會列出所有已成功建立的應用程式執行個體及其整體狀態。[Get-ServiceFabricService](https://msdn.microsoft.com/library/azure/mt125889.aspx) 命令會列出所有已指定應用程式執行個體內成功建立的服務執行個體。會列出預設的服務 (若有)。

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## 工作：升級 Service Fabric 應用程式

您可以升級先前部署的 Service Fabric 應用程式。這項工作會升級先前在「工作：部署 Service Fabric 應用程式」中部署的 HelloWorldStateful 應用程式。詳閱[應用程式升級](service-fabric-application-upgrade.md)以取得詳細資訊。

### 步驟 1：更新應用程式

對 HelloWorldStateful 服務中的程式碼進行變更。

更新服務程式碼之後，您必須累加 ServiceManifest.xml 檔案 (位於 HelloWorldStateful 專案的 PackageRoot 目錄) 中的服務版本號碼。尋找資訊清單的 **CodePackage** 元素，並且將服務版本變更為 2.0.0.0。ServiceManifest.xml 檔案中的對應程式碼行應該會如下所示：

```xml
<ServiceManifest Name="HelloWorldStatefulPkg"
                 Version="2.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0.0.0">
```

現在，您需要更新 ApplicationManifest.xml 檔案 (可於 HelloWorldStateful 解決方案下的 HelloWorldStatefulApplication 專案底下找到)。更新 **ServiceManifestRef** 元素以使用 2.0.0.0 版的 HelloWorldStatefulPkg 專案。同時將 **ApplicationTypeVersion** 從 1.0.0.0 更新為 2.0.0.0。ApplicationManifest.xml 中的對應程式碼行應該會如下所示：

```xml
<ApplicationManifest ApplicationTypeName="HelloWorldStatefulApplication" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<ServiceManifestRef ServiceManifestName="HelloWorldStatefulPkg" ServiceManifestVersion="2.0.0.0" />
```

進行這些變更之後，儲存檔案並且重建 HelloWorldStateful 專案。現在封裝已更新的應用程式，方法是以滑鼠右鍵按一下 HelloWorldStatefulApplication 專案，選取 [Service Fabric] 功能表，然後選擇 [封裝]。這應該會建立可部署的應用程式套件。更新的應用程式已準備好進行部署。

### 步驟 2：複製和註冊已更新的應用程式套件

現在應用程式已建置，並且準備好進行升級。如果您以系統管理員身分開啟 PowerShell 視窗並且輸入 [Get-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt163515.aspx)，您應該會看到已部署應用程式類型 1.0.0.0 的 **HelloWorldStatefulApplication**。對於 HelloWorldStateful 範例，應用程式套件可於下列位置中找到：*C:\\ServiceFabricSamples\\Services\\VS2015\\HelloWorldStateful\\HelloWorldStatefulApplication\\pkg\\Debug*。

現在將更新的應用程式套件複製到 Service Fabric ImageStore (Service Fabric 在其中儲存應用程式套件)。參數 *ApplicationPackagePathInImageStore* 會通知 Service Fabric 可以在哪裡找到應用程式套件。下列命令會將應用程式套件複製到 ImageStore 中的 *HelloWorldStatefulV2*：

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath C:\ServiceFabricSamples\Services\VS2015\HelloWorldStateful\HelloWorldStatefulApplication\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStore   -ApplicationPackagePathInImageStore HelloWorldStatefulV2
```

下一步是將新版本的應用程式註冊至 Service Fabric，可以使用 [Register-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125958.aspx) Cmdlet 來執行：

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore HelloWorldStatefulV2
```

如果此命令不成功，您可能需要重建服務，如步驟 1 中所述。

### 步驟 3：開始升級
各種升級參數、逾時和健全狀況準則可以套用至應用程式升級。若要深入了解，請詳閱[應用程式升級參數](service-fabric-application-upgrade-parameters.md)和[升級程序](service-fabric-application-upgrade.md)。對於此逐步解說，將服務健全狀況評估準則設定為預設值 (及建議值)。所有服務和執行個體在升級之後都應該是_狀況良好_。但是，讓我們將 *HealthCheckStableDuration* 增加為 60 秒 (如此一來，在升級繼續至下一個升級網域之前，至少有 20 秒的時間服務是健康狀態良好的)。同時也要將 *UpgradeDomainTimeout* 設為 1200 秒，將 *UpgradeTimeout* 設為 3000 秒。最後，將 *UpgradeFailureAction* 設為回復，以要求 Service Fabric 在升級期間遇到任何失敗時，將應用程式回復為舊版。

您現在可以使用 [Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125975.aspx) Cmdlet 啟動應用程式升級：

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/HelloWorldStatefulApplication -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

請注意應用程式名稱與先前部署的 v1.0.0.0 應用程式名稱相同 (fabric:/HelloWorldStatefulApplication)。Service Fabric 會使用這個名稱來識別要升級哪一個應用程式。如果您設定的逾時太短，您可能會遇到逾時失敗訊息，指出此問題。請參閱[疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md)，或增加逾時。

您可以使用 [Service Fabric 總管](service-fabric-visualizing-your-cluster.md)，或使用 [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) Cmdlet，監視應用程式升級進度：

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/HelloWorldStatefulApplication
```

幾分鐘後，[Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) Cmdlet 應該會顯示已升級所有升級網域 (完成)。

## 工作：測試 Service Fabric 應用程式

為了撰寫高品質的服務，開發人員必須能夠產生這類不可靠的基礎結構錯誤，才能測試其服務的穩定性。Service Fabric 讓開發人員可以引發錯誤動作，並且使用混亂和容錯移轉的測試案例以測試失敗情況下的服務。詳閱[可測試性概觀](service-fabric-testability-overview.md)以取得詳細資訊。

### 步驟 1：執行混亂測試案例
混亂案例會在整個 Service Fabric 叢集中產生錯誤。此案例會壓縮錯誤，通常是將幾個月或幾年壓縮到幾小時。交錯錯誤和高錯誤率的組合，可以找到會在其他情形下被遺漏的極端狀況。下列範例會執行 60 分鐘的混亂測試案例。

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### 步驟 2：執行容錯移轉測試案例
容錯移轉測試案例的目標是特定服務資料分割，而不是整個叢集，並且讓其他服務不會受到影響。案例會在您的商務邏輯執行時，反覆進行一連串模擬的錯誤及服務驗證。服務驗證中若有失敗，表示有需要進一步調查的問題。容錯移轉測試一次只會引發一個錯誤，不像混亂測試案例，會引發多個錯誤。下列範例會針對網狀架構執行 60 分鐘的容錯移轉測試：/HelloWorldStatefulApplication/HelloWorldStateful 服務。

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/HelloWorldStatefulApplication/HelloWorldStateful"

Connect-ServiceFabricCluster

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## 工作：移除 Service Fabric 應用程式
您可以刪除已部署應用程式的執行個體，從叢集中移除已佈建的應用程式類型，以及從 ImageStore 移除應用程式套件。

### 步驟 1：移除應用程式執行個體
當不再需要應用程式執行個體時，您可以使用 [Remove-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt125914.aspx) Cmdlet 來永久移除。這也會自動移除屬於應用程式的所有服務，同時永久移除所有服務狀態。此作業無法回復，且應用程式狀態無法復原。

```powershell
Remove-ServiceFabricApplication fabric:/HelloWorldStatefulApplication
```

### 步驟 2：取消註冊應用程式類型
當您不再需要特定應用程式類型版本時，使用 [Unregister-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125885.aspx) Cmdlet 來取消註冊。正在取消註冊的未使用類型，會釋放映像存放區上應用程式套件所使用的儲存空間。只要沒有對應的應用程式進行具現化，或擱置中的應用程式升級進行參考，則應用程式類型即可取消註冊。

```powershell
Unregister-ServiceFabricApplicationType HelloWorldStatefulApplication 1.0.0.0
```

### 步驟 3：移除應用程式套件
在取消註冊應用程式類型之後，可以使用 [Remove-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/azure/mt163532.aspx) Cmdlet，從 ImageStore 移除應用程式套件。

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStore -ApplicationPackagePathInImageStore HelloWorldStateful
```

## 其他資源
[Service Fabric 應用程式生命週期](service-fabric-application-lifecycle.md)

[管理 Service Fabric 服務](service-fabric-manage-your-service-index.md)

[Azure Service Fabric Cmdlet](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Azure Service Fabric 可測試性 Cmdlet](https://msdn.microsoft.com/library/azure/mt125844.aspx)

<!---HONumber=Oct15_HO3-->