<properties
	pageTitle="Azure Insights：Azure Insights 自動調整的常用度量| Microsoft Azure"
	description="了解哪些度量常用於自動調整您的雲端服務、虛擬機器和 Web Apps。"
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Azure Insights 自動調整的常用度量

Azure Insights 自動調整可讓您根據遙測資料 (度量) 增加或減少執行中執行個體的數目。本文件說明您可能會使用的常用度量。在 Azure 的雲端服務和伺服器陣列入口網站中，您可以選擇要做為調整依據的資源度量。不過，您也可以選擇其他資源的任何度量來做為調整依據。

以下詳細說明如何尋找並列出要做為調整依據的度量。下列作法也適用於調整「虛擬機器擴展集」。

## 計算度量
根據預設，Azure VM v2 隨附設定好的診斷擴充功能，並已開啟下列度量。

- [Windows VM v2 的來賓度量](#compute-metrics-for-windows-vm-v2-as-a-guest-os)
- [Linux VM v2 的來賓度量](#compute-metrics-for-linux-vm-v2-as-a-guest-os)

您可以使用 `Get MetricDefinitions` API/PoSH/CLI 來檢視 VMSS 資源的可用度量。

如果您使用 VM 擴展集，而且發現特定度量沒有列出來，可能是您的診斷擴充功能已*停用*它。

如果特定度量沒有取樣或以您想要的頻率傳輸，您可以更新診斷的組態設定。

如果是上述任一種情況，則請檢閱 PowerShell 的[使用 PowerShell 為執行 Windows 的虛擬機器啟用 Azure 診斷](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)，設定和更新您的 Azure VM 診斷擴充功能以啟用該度量。該文章也包含診斷組態檔的範例。

### Windows VM v2 作為客體 OS 時的計算度量

當您在 Azure 中建立新的 VM (v2) 時，使用診斷擴充功能會啟用診斷功能。

您可以在 PowerShell 中使用下列命令產生度量清單。


```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

您可以建立下列度量的警示。

|度量名稱|	單位|
|---|---|
|\\Processor(\_Total)\\% Processor Time |百分比| |\\Processor(\_Total)\\% Privileged Time |百分比| |\\Processor(\_Total)\\% User Time |百分比| |\\Processor Information(\_Total)\\Processor Frequency |計數| |\\System\\Processes| 計數| |\\Process(\_Total)\\Thread Count| 計數| |\\Process(\_Total)\\Handle Count |計數| |\\Memory\\% Committed Bytes In Use |百分比| |\\Memory\\Available Bytes| 位元組| |\\Memory\\Committed Bytes |位元組| |\\Memory\\Commit Limit| 位元組| |\\Memory\\Pool Paged Bytes| 位元組| |\\Memory\\Pool Nonpaged Bytes| 位元組| |\\PhysicalDisk(\_Total)\\% Disk Time| 百分比| |\\PhysicalDisk(\_Total)\\% Disk Read Time| 百分比| |\\PhysicalDisk(\_Total)\\% Disk Write Time| 百分比| |\\PhysicalDisk(\_Total)\\Disk Transfers/sec |每秒計數| |\\PhysicalDisk(\_Total)\\Disk Reads/sec |每秒計數| |\\PhysicalDisk(\_Total)\\Disk Writes/sec |每秒計數| |\\PhysicalDisk(\_Total)\\Disk Bytes/sec |每秒位元組| |\\PhysicalDisk(\_Total)\\Disk Read Bytes/sec| 每秒位元組| |\\PhysicalDisk(\_Total)\\Disk Write Bytes/sec |每秒位元組| |\\PhysicalDisk(\_Total)\\Avg.Disk Queue Length| 計數| |\\PhysicalDisk(\_Total)\\Avg.Disk Read Queue Length| 計數| |\\PhysicalDisk(\_Total)\\Avg.Disk Write Queue Length |計數| |\\LogicalDisk(\_Total)\\% Free Space| 百分比| |\\LogicalDisk(\_Total)\\Free Megabytes| 計數|



### Linux VM v2 作為客體 OS 時的計算度量

當您在 Azure 中建立新的 VM (v2) 時，使用診斷擴充功能預設會啟用診斷功能。

您可以在 PowerShell 中使用下列命令產生度量清單。

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 您可以建立下列度量的警示。

|度量名稱|	單位|
|---|---|
|\\Memory\\AvailableMemory |位元組|
|\\Memory\\PercentAvailableMemory|	百分比|
|\\Memory\\UsedMemory|	位元組|
|\\Memory\\PercentUsedMemory|	百分比|
|\\Memory\\PercentUsedByCache |百分比|
|\\Memory\\PagesPerSec|	每秒計數|
|\\Memory\\PagesReadPerSec|	每秒計數|
|\\Memory\\PagesWrittenPerSec |每秒計數|
|\\Memory\\AvailableSwap |位元組|
|\\Memory\\PercentAvailableSwap|	百分比|
|\\Memory\\UsedSwap |位元組|
|\\Memory\\PercentUsedSwap|	百分比|
|\\Processor\\PercentIdleTime|	百分比|
|\\Processor\\PercentUserTime|	百分比|
|\\Processor\\PercentNiceTime |百分比|
|\\Processor\\PercentPrivilegedTime |百分比|
|\\Processor\\PercentInterruptTime|	百分比|
|\\Processor\\PercentDPCTime|	百分比|
|\\Processor\\PercentProcessorTime |百分比|
|\\Processor\\PercentIOWaitTime |百分比|
|\\PhysicalDisk\\BytesPerSecond|	每秒位元組|
|\\PhysicalDisk\\ReadBytesPerSecond|	每秒位元組|
|\\PhysicalDisk\\WriteBytesPerSecond|	每秒位元組|
|\\PhysicalDisk\\TransfersPerSecond |每秒計數|
|\\PhysicalDisk\\ReadsPerSecond |每秒計數|
|\\PhysicalDisk\\WritesPerSecond |每秒計數|
|\\PhysicalDisk\\AverageReadTime|	秒|
|\\PhysicalDisk\\AverageWriteTime |秒|
|\\PhysicalDisk\\AverageTransferTime|	秒|
|\\PhysicalDisk\\AverageDiskQueueLength|	Count|
|\\NetworkInterface\\BytesTransmitted |位元組|
|\\NetworkInterface\\BytesReceived |位元組|
|\\NetworkInterface\\PacketsTransmitted |Count|
|\\NetworkInterface\\PacketsReceived |Count|
|\\NetworkInterface\\BytesTotal |位元組|
|\\NetworkInterface\\TotalRxErrors|	Count|
|\\NetworkInterface\\TotalTxErrors|	Count|
|\\NetworkInterface\\TotalCollisions|	Count|




## 常用的 Web (伺服器陣列) 度量

您也可以根據常用的 Web 伺服器度量 (如 Http 佇列長度) 執行自動調整。它的度量名稱是 **HttpQueueLength**。下一節會列出可用的伺服器陣列 (Web Apps) 度量。

### Web Apps 度量

您可以在 PowerShell 中使用下列命令產生 Web Apps 清單。

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

您可以針對這些度量發出警示通知或以其為調整依據。

|度量名稱|	單位|
|---|---|
|CpuPercentage|	百分比|
|MemoryPercentage |百分比|
|DiskQueueLength|	Count|
|HttpQueueLength|	Count|
|BytesReceived|	位元組|
|BytesSent|	位元組|


## 常用的儲存體度量
您可以「儲存體佇列長度」做為調整依據，它是儲存體佇列中的訊息數目。儲存體佇列長度是一個特殊度量，套用的臨界值是每個執行個體的訊息數。這表示如果有兩個執行個體，且如果臨界值設定為 100 時，當佇列中的訊息總數為 200 時將會進行調整。例如，每個執行個體 100 個訊息。

您可以在 Azure 入口網站的 [設定] 刀鋒視窗中進行此設定。若使用 VM 擴展集，您可以更新 ARM 範本中的自動調整設定，改為使用 *metricName* 做為 *ApproximateMessageCount*，並傳遞儲存體佇列的識別碼做為 *metricResourceUri*。


```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

## 常用的服務匯流排衡量標準

您可以依服務匯流排佇列長度做為調整依據，它是服務匯流排佇列中的訊息數目。服務匯流排佇列長度是一個特殊衡量標準，套用的特定臨界值是每個執行個體的訊息數。這表示如果有兩個執行個體，且如果臨界值設定為 100 時，當佇列中的訊息總數為 200 時將會進行調整。例如，每個執行個體 100 個訊息。

若使用 VM 擴展集，您可以更新 ARM 範本中的自動調整設定，改為使用 metricName 做為 ApproximateMessageCount，並傳遞儲存體佇列的識別碼做為 metricResourceUri。

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

>[AZURE.NOTE] 若使用服務匯流排，資源群組的概念不存在，但 Azure Resource Manager 會建立每個區域的預設資源群組。此資源群組通常是 'Default-ServiceBus-[region]' 的格式。例如，'Default-ServiceBus-EastUS'、'Default-ServiceBus-WestUS'、'Default-ServiceBus-AustraliaEast' 等。

<!---HONumber=AcomDC_0330_2016-->