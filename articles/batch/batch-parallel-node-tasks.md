<properties
   pageTitle="搭配平行工作使用最大化 Batch 節點 | Microsoft Azure"
   description="在 Azure Batch 集區中的每個節點上執行並行工作時，使用較少的運算節點以增加效率和降低成本"
   services="batch"
   documentationCenter=".net"
   authors="mmacy"
   manager="timlt"
   editor=""/>

   <tags
   	ms.service="batch"
   	ms.devlang="multiple"
   	ms.topic="article"
   	ms.tgt_pltfrm="vm-windows"
   	ms.workload="big-compute"
   	ms.date="11/02/2015"
   	ms.author="v-marsma"/>

# 使用並行節點工作最大化 Azure Batch 運算資源使用量

大規模平行工作執行是 Azure Batch 的核心功能，這項功能不僅可以擴充到執行您的工作的多個運算節點，並且可以在那些節點上執行並行工作。使用 Batch，平行工作執行相應增加以及相應放大。

在集區的運算節點上啟用並行工作執行，可以在集區中較少數量的節點上最大化資源使用量。雖然某些案例受益於可用於配置給單一工作的所有節點的資源，但是許多案例受益於允許多個工作共用這些資源：

 - 當工作可以共用資料時**最小化資料傳輸**。在此案例中，將共用資料複製到較少數量的節點以及在每個節點上平行執行工作，可以大幅降低資料傳輸費用，尤其是當要複製到每個節點的資料必須在地理區域之間傳輸時。

 - 當工作需要大量記憶體時**最大化記憶體使用量**，但是只有在短時間之內，以及在執行期間的變數時間。具有更多記憶體的較少但較大的節點執行個體可能會用來有效率地處理這類高峰流量，在每個節點上執行平行工作以在不同時間運用節點的大量記憶體。

 - 當集區內需要節點間通訊時**緩和節點數目限制**。目前，針對節點間通訊設定的集區限制為 50 個節點，因此如果這類的集區中的每個節點能夠以平行方式執行工作，則可以同時執行較多的工作。

 - **複寫內部部署運算叢集**，例如當第一次將運算環境移至 Azure 時。可能會完成增加節點工作的最大數目，以更密切地鏡像現有的實體組態，如果該組態目前在每個運算節點上執行多個工作。

## 範例案例

為了說明平行工作執行的優點，讓我們假設您的工作應用程式的 CPU 和記憶體需求 Standard\_D1 節點大小是適合的，但是為了在要求的時間內執行作業，需要 1000 個此類節點。如果不使用 Standard\_D1 節點，您可以採用具有 16 個核心的 Standard\_D14 節點，並啟用平行工作執行。在此情況下，不需使用 1000 個節點，而是可以使用 *16 倍較少的節點*，僅需 63 個節點。如果每個節點需要大型應用程式檔案或參考資料，這樣可大幅改進作業執行時間和效率。

## 啟用平行工作執行

針對在集區層級完成平行工作執行，在您的 Batch 解決方案中設定運算節點。使用 Batch .NET API 時，[CloudPool.MaxTasksPerComputeNode][maxtasks_net] 屬性是在建立集區時設定。在 Batch REST API 中，[maxTasksPerNode][maxtasks_rest] 元素是在集區建立期間於要求主體中設定。

Azure Batch 允許每個節點的最大化工作設定為多達節點核心數目的 4 倍 (4x)。例如，如果集區設定的節點大小為 [大] (四個核心)，則 `maxTasksPerNode` 可以設定為十六個。每個節點大小的核心數目的詳細資料可於[雲端服務的大小](../cloud-services/cloud-services-sizes-specs.md)中找到，關於服務限制的詳細資訊可於 [Azure Batch 服務的配額與限制](batch-quota-limit.md)中找到。

> [AZURE.TIP]當為您的集區建構[自動調整公式][enable_autoscaling]時，請務必考慮 `maxTasksPerNode` 值。例如，評估 `$RunningTasks` 的公式可能大幅受到每個節點的工作增加的影響。如需詳細資訊，請參閱[自動調整 Azure Batch 集區中的運算節點](batch-automatic-scaling.md)。

## 工作的分佈

在集區內的運算節點能夠同時執行工作時，務必指定您要如何跨集區內的節點分佈您的工作。

使用 [CloudPool.TaskSchedulingPolicy][task_schedule] 屬性，您可以指定工作應該跨集區中的所有節點平均指派 (「散佈」)，或是在工作指派到集區中其他節點之前盡可能將最多工作指派給每個節點 (「封裝」)。

做為這項功能有何重要的範例，請考慮上述範例中 Standard\_D14 節點的集區，以 [CloudPool.MaxTasksPerComputeNode][maxtasks_net] 的值 16 進行設定。如果 [CloudPool.TaskSchedulingPolicy][task_schedule] 以 [ComputeNodeFillType][fill_type] 為 *Pack* 進行設定，它會最大化每個節點全部 16 個核心的使用量，並且允許[自動調整集區](./batch-automatic-scaling.md)從集區剪除未使用的節點 (未指派任何工作的節點)，因而最小化資源使用量和節省金錢。

## Batch .NET 範例

這個 [Batch .NET][api_net] API 程式碼片段示範建立集區的要求，該集區包含四個大型節點，每個節點有最多四項工作，並且指定工作排程原則，該原則會先以工作填滿每個節點，再將工作指派給集區中的其他節點。如需有關使用 Batch .NET API 新增集區的詳細資訊，請參閱 [BatchClient.PoolOperations.CreatePool][poolcreate_net]。

        CloudPool pool = batchClient.PoolOperations.CreatePool(poolId: "mypool",
        													osFamily: "2",
        													virtualMachineSize: "large",
        													targetDedicated: 4);
        pool.MaxTasksPerComputeNode = 4;
        pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
        pool.Commit();

## Batch REST 範例

這個 [Batch REST][api_rest] API 片段示範建立集區的要求，該集區包含兩個大型節點，每個節點有最多四項工作。如需有關如何使用 REST API 新增集區的詳細資訊，請參閱[將集區新增至帳戶][maxtasks_rest]。

        {
          "id": "mypool",
          "vmSize": "Large",
          "osFamily": "2",
          "targetOSVersion": "*",
          "targetDedicated": 2,
          "enableInterNodeCommunication": false,
          "maxTasksPerNode": 4
        }

> [AZURE.NOTE]`maxTasksPerNode` 元素和 [MaxTasksPerComputeNode][maxtasks_net] 屬性只能在集區建立時設定。建立集區後無法修改。

## 探索範例專案

參閱 GitHub 上的 [ParallelNodeTasks][parallel_tasks_sample] 專案，這是說明使用 [CloudPool.MaxTasksPerComputeNode][maxtasks_net] 的工作程式碼範例。這個 C# 主控台應用程式使用 [Batch .NET][api_net] 文件庫建立具有一或多個運算節點的集區，並且在這些節點上執行可設定工作數目，以模擬可變負載。應用程式的輸出詳細說明哪些節點執行每個工作，以及工作參數和持續時間的摘要。兩個不同的範例應用程式執行的輸出摘要部分會在下方顯示。

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

首次執行範例應用程式會顯示該部分與集區中的單一節點，並且使用每個節點一項工作的預設值，工作持續時間超過三十分鐘。

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

第二次執行範例會顯示作業持續時間大幅降低，原因是集區已設定為每個節點四項工作，允許平行工作執行以在近一季的時間完成作業。

> [AZURE.NOTE]在上述摘要中的作業持續時間不包括集區建立時間。上述的每個工作已提交至先前建立的集區，其運算節點在提交時間處於 [閒置] 狀態。

## Batch 總管熱圖

[Batch 總管][batch_explorer]，其中一個 Azure Batch [範例應用程式][github_samples]，包含「熱圖」功能 ，提供工作執行的視覺效果。執行 [ParallelTasks][parallel_tasks_sample] 範例應用程式時，使用熱圖功能來輕易地視覺化每個節點上 平行工作的執行。

![Batch 總管熱圖][1]

*顯示包含四個節點的集區的Batch 總管熱圖，每個節點目前正在執行四個工作*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/zh-TW/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[maxtasks_rest]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png

<!---HONumber=Nov15_HO2-->