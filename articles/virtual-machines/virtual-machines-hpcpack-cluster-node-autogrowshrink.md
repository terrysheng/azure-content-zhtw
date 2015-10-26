<properties
 pageTitle="自動調整 HPC 叢集中的運算資源 | Microsoft Azure"
 description="了解在 Azure 的 HPC Pack 叢集中自動增加和縮減運算資源的方法"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# 在 HPC Pack 叢集中根據叢集工作負載自動向上和向下調整 Azure 運算資源

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。


如果在您的 HPC Pack 叢集部署 Azure「高載」節點，或您在 Azure VM 中建立 HPC Pack 叢集，您可能需要能根據叢集上目前的工作和任務的工作負載自動增加和縮減 Azure 運算資源的方式。這可讓您更有效率地使用您的 Azure 資源及控制其成本。若要這樣做，請使用隨著 HPC Pack 安裝的 **AzureAutoGrowShrink.ps1** HPC PowerShell 指令碼。

>[AZURE.TIP]從 HPC Pack 2012 R2 更新 2 開始，HPC Pack 包括的內建服務可自動增加和縮減 Azure 高載節點或 Azure VM 運算節點。在 [HPC Pack IaaS 部署指令碼](virtual-machines-hpcpack-cluster-powershell-script.md)中設定服務的設定，或手動設定 **AutoGrowShrink** 叢集屬性。請參閱 [Microsoft HPC Pack 2012 R2 更新 2 的新功能](https://technet.microsoft.com/library/mt269417.aspx)。

## 必要條件

* **HPC Pack 2012 R2 更新 1 或更新版本叢集** - **AzureAutoGrowShrink.ps1** 指令碼會安裝在 %CCP\_HOME%bin 資料夾中。叢集前端節點可以部署在內部部署或在 Azure VM 中。請參閱[使用 HPC Pack 設定混合式叢集](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)，以開始使用內部部署前端節點與 Azure「高載」節點。請參閱 [HPC Pack IaaS 部署指令碼](virtual-machines-hpcpack-cluster-powershell-script.md))，以在 Azure VM 中快速部署 HPC Pack 叢集。

* **針對具備 Azure 高載節點的叢集** - 在已安裝 HPC Pack 的用戶端電腦上或在前端節點上執行指令碼。如果是在用戶端電腦上執行，請確定您正確設定變數 $env:CCP\_SCHEDULER 以指向前端節點。Azure「高載」節點必須已經加入到叢集，但它們可能處於「未部署」狀態。


* **針對部署在 Azure VM 中的叢集** - 在前端節點 VM 執行指令碼，因為它相依於所安裝的 **Start-HpcIaaSNode.ps1** 和 **Stop-HpcIaaSNode.ps1** 指令碼。這些指令碼額外需要 Azure 管理憑證或發佈設定檔 (請參閱[在 Azure 中的 HPC Pack 叢集管理運算節點](virtual-machines-hpcpack-cluster-node-manage.md))。請確定您所需的所有運算節點 VM 已加入叢集，但是它們可能處於「已停止」狀態。

## 語法

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
## 參數

 * **NodeTemplates** - 定義增加和縮減節點範圍的節點範本的名稱。如果未指定 (預設值是 @())，當 **NodeType** 具有 AzureNodes 的值時，**AzureNodes** 節點群組中的所有節點會在範圍內，並且當 **NodeType** 具有 ComputeNodes 的值時，**ComputeNodes** 節點群組中的所有節點會在範圍內。

 * **JobTemplates** - 定義節點增加範圍的工作範本的名稱。

 * **NodeType** - 增加和縮減節點的類型。支援的值包括：

     * **AzureNodes** - 針對內部部署或 Azure IaaS 叢集中的 Azure PaaS (高載) 節點。

     * **ComputeNodes** - 針對只在 Azure IaaS 叢集的運算節點 VM。

* **NumOfQueuedJobsPerNodeToGrow** - 要增加一個節點所需的已佇列工作數目。

* **NumOfQueuedJobsToGrowThreshold** - 要開始增加處理程序的已佇列工作的臨界值數目。

* **NumOfActiveQueuedTasksPerNodeToGrow** - 要增加一個節點所需的作用中已佇列工作的數目。如果 **NumOfQueuedJobsPerNodeToGrow** 指定了大於 0 的值，則會忽略這個參數。

* **NumOfActiveQueuedTasksToGrowThreshold** - 要開始增加處理程序的作用中已佇列任務的臨界值數目。

* **NumOfInitialNodesToGrow** - 如果範圍中的所有節點為**未部署**或**已停止 (取消配置)**，要增加的初始最小節點數目。

* **GrowCheckIntervalMins** - 要進行增加的檢查之間的間隔分鐘數。

* **ShrinkCheckIntervalMins** - 要進行縮減的檢查之間的間隔分鐘數。

* **ShrinkCheckIdleTimes** - 連續縮減檢查的數目 (以 **ShrinkCheckIntervalMins** 分隔)，以指出節點為閒置。

* **UseLastConfigurations*** 儲存於引數檔的先前的組態。

* **ArgFile** - 用來儲存並更新組態以執行指令碼的引數檔案的名稱。

* **LogFilePrefix** - 記錄檔名稱的前置詞。您可以指定路徑。記錄檔預設會寫入目前的工作目錄。

### 範例 1

下列範例會設定 Azure 高載節點，其中部署了預設 AzureNode 範本以自動增加和縮減。如果所有節點最初的狀態為**未部署**狀態，則至少啟動了 3 個節點。如果佇列工作數目超過 8 個，指令碼會啟動節點，直到它們的數目超過 **NumOfQueuedJobsPerNodeToGrow** 已佇列工作的比率。如果節點被發現處於閒置達 3 個連續閒置時間，則會將它停止。

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### 範例 2

下列範例會設定 Azure 運算節點 VM，其中部署了預設 ComputeNode 範本以自動增加和縮減。預設工作範本所設定的工作會定義叢集上工作負載的範圍。如果所有節點最初為已停止，則至少啟動了 5 個節點。如果使用中任務數目超過 15 個，指令碼會啟動節點，直到它們的數目超過 **NumOfActiveQueuedTasksPerNodeToGrow** 使用中已佇列任務的比率。如果節點被發現處於閒置達 10 個連續閒置時間，則會將它停止。

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```

<!---HONumber=Oct15_HO3-->