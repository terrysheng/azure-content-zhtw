<properties
   pageTitle="Service Fabric 中的應用程式生命週期 |Microsoft Azure"
   description="描述開發、部署、測試、升級、維護和移除 Service Fabric 應用程式。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="ryanwi"/>


# Service Fabric 應用程式生命週期
如同其他平台，Azure Service Fabric 上的應用程式通常會經歷下列階段：設計、開發、測試、部署、升級、維護和移除。從開發到部署、到每日管理、維護，以及最終的解除委任，Service Fabric 為雲端應用程式的完整應用程式生命週期提供第一等的支援。服務模型可以啟用數個不同的角色，在應用程式生命週期中獨立參與。本文章提供 API 的概觀以及其使用方式，這些 API 是由不同的角色在 Service Fabric 應用程式生命週期的各個階段使用。

## 服務模型角色
服務模型角色是：

- **服務開發人員**：開發模組化和泛型服務，可以重新制定目的以及在相同類型或不同類型的多個應用程式中使用。例如，佇列服務可以用於建立票證應用程式 ( 技術支援中心) 或電子商務應用程式 (購物車)。

- **應用程式開發人員**：藉由整合服務集合來建立應用程式，以滿足部分特定需求或案例。例如，電子商務網站可能會整合「JSON 無狀態前端服務」、「拍賣可設定狀態服務」和「佇列可設定狀態服務」，以建立拍賣解決方案。

- **應用程式系統管理員**：進行應用程式組態 (填入組態範本參數)、部署 (對應至可用的資源)，以及服務品質的決策。例如，應用程式系統管理員會決定應用程式的語言地區設定 (例如，在美國使用美式英文、在日本使用日文)。以不同方式部署的應用程式可以有不同的設定。

- **運算子**：根據應用程式組態和應用程式系統管理員所指定的需求，部署應用程式。例如，操作員佈建和部署應用程式，並確保它在 Azure 中執行。操作員會監視應用程式健康狀況和效能資訊，並視需要維護實體基礎結構。


## 開發
1. *服務開發人員*使用[可靠的執行者](service-fabric-reliable-actors-introduction.md)或[可靠的服務](service-fabric-reliable-services-introduction.md)程式設計模型來開發不同類型的服務。
2. *服務開發人員*以宣告方式描述服務資訊清單檔案中的已開發服務類型，該檔案包含一或多個程式碼、組態和資料封裝。
3. 然後，*應用程式開發人員*建置使用不同服務類型的應用程式。
4. *應用程式開發人員*以宣告方式描述應用程式資訊清單中的應用程式類型，方法是參考組成服務的服務資訊清單，並且適當地覆寫和參數化組成服務的不同組態和部署設定。

如需範例，請參閱[開始使用可靠的執行者](service-fabric-reliable-actors-get-started.md)和[開始使用可靠的服務](service-fabric-reliable-services-quick-start.md)。

## 部署
1. *應用程式系統管理員*會將應用程式類型調整為要部署至 Service Fabric 叢集的特定應用程式，方法是在應用程式資訊清單中指定 **ApplicationType** 元素的適當參數。

2. *運算子*會使用 [**CopyApplicationPackage** 方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx)或 [**Copy-ServiceFabricApplicationPackage** Cmdlet](https://msdn.microsoft.com/library/azure/mt125905.aspx)，將應用程式封裝上傳至叢集映像存放區。應用程式封裝包含應用程式資訊清單和服務封裝集合。Service Fabric 會從儲存在映像存放區 (可以是 Azure Blob 存放區或 Service Fabric 系統服務) 中的應用程式封裝部署應用程式。

3. 然後，「運算子」會使用 [**ProvisionApplicationAsync** 方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx)、[**Register-ServiceFabricApplicationType** Cmdlet](https://msdn.microsoft.com/library/azure/mt125958.aspx)，或[**佈建應用程式** REST 作業](https://msdn.microsoft.com/library/azure/dn707672.aspx)，從上傳的應用程式封裝在目標叢集中佈建應用程式類型。

4. 佈建應用程式之後，*運算子*會使用*應用程式系統管理員*提供的參數啟動應用程式，方法是使用 [**CreateApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx) 方法、[**New-ServiceFabricApplication** Cmdlet](https://msdn.microsoft.com/library/azure/mt125913.aspx)，或[**建立應用程式** REST 作業](https://msdn.microsoft.com/library/azure/dn707676.aspx)。

5. 在部署應用程式之後，「運算子」會使用 [**CreateServiceAsync** 方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx)、[**New-ServiceFabricService** Cmdlet](https://msdn.microsoft.com/library/azure/mt125874.aspx)，或[**建立服務** REST 作業](https://msdn.microsoft.com/library/azure/dn707657.aspx)，根據可用的服務類型針對應用程式建立新的服務執行個體。

6. 應用程式現在會在 Service Fabric 叢集中執行。

如需範例，請參閱[部署應用程式](service-fabric-deploy-remove-applications.md)。

## 測試
1. 在部署至本機開發叢集或測試叢集之後，*服務開發人員*會執行內建的容錯移轉測試案例，方法是使用 [**FailoverTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx) 和 [**FailoverTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) 類別，或 [**Invoke-ServiceFabricFailoverTestScenario** Cmdlet](https://msdn.microsoft.com/library/azure/mt125935.aspx)。容錯移轉測試案例會在重要的轉換和容錯移轉期間執行指定服務，以確保它是仍然可用且可運作。

2. 然後，*服務開發人員*會執行內建混亂測試案例，方法是使用 [**ChaosTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) 和 [**ChaosTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) 類別，或 [**Invoke-ServiceFabricChaosTestScenario** Cmdlet](https://msdn.microsoft.com/library/azure/mt126036.aspx)。混亂測試案例會在叢集中隨機引發多個節點、程式碼封裝和複本錯誤。

如需範例，請參閱 [Testability 案例](service-fabric-testability-scenarios.md)。

## 升級
1. *服務開發人員*會更新具現化應用程式的組成服務及/或修正錯誤，並提供新版本的服務資訊清單。

2. *應用程式開發人員*會覆寫和參數化組成服務的組態和部署設定，並提供新版本的應用程式資訊清單。然後，應用程式開發人員會將新版本的服務資訊清單併入應用程式，並在更新的應用程式封裝中提供新版本的應用程式類型。

3. *應用程式系統管理員*會將新版本的應用程式類型併入目標應用程式，方法是更新適當的參數。

4. *運算子*會使用 [**CopyApplicationPackage** 方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx)或 [**Copy-ServiceFabricApplicationPackage** Cmdlet](https://msdn.microsoft.com/library/azure/mt125905.aspx)，將更新的應用程式封裝上傳至叢集映像存放區。應用程式封裝包含應用程式資訊清單和服務封裝集合。

5. *運算子*使用 [**ProvisionApplicationAsync** 方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx)、[**Register-ServiceFabricApplicationType** Cmdlet](https://msdn.microsoft.com/library/azure/mt125958.aspx)，或[**佈建應用程式** REST 作業](https://msdn.microsoft.com/library/azure/dn707672.aspx)，在目標叢集中佈建新版本的應用程式。

6. 「運算子」使用 [**UpgradeApplicationAsync** 方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx)、[**Start-ServiceFabricApplicationUpgrade** Cmdlet](https://msdn.microsoft.com/library/azure/mt125975.aspx) 或[**升級應用程式** REST 作業升級應用程式](https://msdn.microsoft.com/library/azure/dn707633.aspx)，將目標應用程式升級為新版本。

7. *運算子*使用 [**GetApplicationUpgradeProgressAsync** 方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx)、[**Get-ServiceFabricApplicationUpgrade** Cmdlet](https://msdn.microsoft.com/library/azure/mt125988.aspx)，或[**取得應用程式升級進度** REST 作業](https://msdn.microsoft.com/library/azure/dn707631.aspx)，檢查升級進度。

8. 如果需要，*運算子*使用 [**UpdateApplicationUpgradeAsync** 方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx)、[**Update-ServiceFabricApplicationUpgrade** Cmdlet](https://msdn.microsoft.com/library/azure/mt126030.aspx)，或[**更新應用程式升級** REST 作業](https://msdn.microsoft.com/library/azure/mt628489.aspx)，修改並重新套用目前應用程式升級的參數。

9. 如果需要，*運算子*使用 [**RollbackApplicationUpgradeAsync** 方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx)、[**Start-ServiceFabricApplicationRollback** Cmdlet](https://msdn.microsoft.com/library/azure/mt125833.aspx)，或[**回復應用程式升級** REST 作業](https://msdn.microsoft.com/library/azure/mt628494.aspx)，回復目前的應用程式升級。

10. Service Fabric 會升級在叢集中執行的目標應用程式，而不會遺失任何其組成服務的可用性。

如需範例，請參閱[應用程式升級教學課程](service-fabric-application-upgrade-tutorial.md)。

## 維護
1. 對於作業系統升級和修補程式，Service Fabric 會與 Azure 基礎結構連結，以保證在叢集中執行之所有應用程式的可用性。

2. 對於 Service Fabric 平台的升級和修補程式，Service Fabric 會自行升級，而不會遺失在叢集上執行之任何應用程式的可用性。

3. *應用程式系統管理員*在分析歷史容量使用率資料和預測的未來需求之後，核准在叢集中新增或移除節點。

4. *操作員*會新增或移除*應用程式系統管理員*指定的節點。

5. 當新的節點加入至叢集或從叢集移除現有節點時，Service Fabric 會自動對叢集中所有節點的執行中應用程式進行負載平衡，以達到最佳效能。

## Remove
1. *運算子*可以使用 [**DeleteServiceAsync** 方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx)、[**Remove-ServiceFabricService** Cmdlet](https://msdn.microsoft.com/library/azure/mt126033.aspx)，或[**刪除服務** REST 作業](https://msdn.microsoft.com/library/azure/dn707687.aspx)，刪除叢集內執行中服務的特定執行個體，而不需要移除整個應用程式。  

2. *運算子*也可以使用 [**DeleteApplicationAsync** 方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx)、[**Remove-ServiceFabricApplication** Cmdlet](https://msdn.microsoft.com/library/azure/mt125914.aspx)，或[**刪除應用程式** REST 作業](https://msdn.microsoft.com/library/azure/dn707651.aspx)，刪除應用程式執行個體及其所有服務。

3. 應用程式和服務停止之後，*運算子*可以使用 [**UnprovisionApplicationAsync** 方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx)、[**Unregister-ServiceFabricApplicationType** Cmdlet](https://msdn.microsoft.com/library/azure/mt125885.aspx)，或[**解除佈建應用程式** REST 作業](https://msdn.microsoft.com/library/azure/dn707671.aspx)，解除佈建應用程式類型。解除佈建應用程式類型不會從 ImageStore 移除應用程式封裝。您必須手動移除應用程式封裝。

4. *運算子*會使用 [**RemoveApplicationPackage** 方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx)或 [**Remove-ServiceFabricApplicationPackage** Cmdlet](https://msdn.microsoft.com/library/azure/mt163532.aspx)，從 ImageStore 移除應用程式封裝。

如需範例，請參閱[部署應用程式](service-fabric-deploy-remove-applications.md)。

## 後續步驟

如需開發、測試及管理 Service Fabric 應用程式和服務的詳細資訊，請參閱：

- [Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)
- [部署應用程式](service-fabric-deploy-remove-applications.md)
- [應用程式升級](service-fabric-application-upgrade.md)
- [Testability 概觀](service-fabric-testability-overview.md)
- [REST 架構應用程式生命週期範例](service-fabric-rest-based-application-lifecycle-sample.md)

<!---HONumber=AcomDC_0211_2016-->