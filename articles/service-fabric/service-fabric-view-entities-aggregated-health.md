<properties
   pageTitle="如何檢視 Azure Service Fabric 實體的彙總健康情況 | Microsoft Azure"
   description="說明如何透過健康情況查詢和一般查詢，來查詢、檢視和評估 Azure Service Fabric 實體的彙總健康情況。"
   services="service-fabric"HealthManager
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/23/2016"
   ms.author="oanapl"/>

# 檢視 Service Fabric 健康狀態報告
Azure Service Fabric 導入了由健康情況實體組成的[健康情況模型](service-fabric-health-introduction.md)，系統元件和監視程式可以向健康情況實體報告它們所監視的本機情況。[健康情況存放區](service-fabric-health-introduction.md#health-store) 會彙總所有健康情況資料，以判斷實體是否狀況良好。

根據現有設定，叢集會填入系統元件傳送的健康狀態報告。請參閱[使用系統健康狀態報告進行疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)。

Service Fabric 會提供多種方式，以取得實體的彙總健康情況：

- [Service Fabric 總管](service-fabric-visualizing-your-cluster.md)或其他視覺效果工具

- 健康情況查詢 (透過 PowerShell、API 或 REST)

- 一般查詢，此查詢會傳回一份實體清單，這些實體的其中一個屬性即為健康情況 (透過 Powershell、API 或 REST)

為了示範這些選項，我們會使用具有五個節點的本機叢集。**fabric:/System** 應用程式 (原本即已存在) 旁邊，已部署一些其他應用程式。其中之一是 **fabric:/WordCount**。此應用程式包含具狀態服務，且已設有七個複本。因為只有五個節點，所以系統元件會顯示分割區低於目標計數的警告。

```xml
<Service Name="WordCountService">
    <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
      <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
    </StatefulService>
</Service>
```

## Service Fabric 總管中的健康情況
Service Fabric 總管提供叢集的視覺化檢視。在下圖中，您可以看到：

- 應用程式 **fabric:/WordCount** 是紅色 (錯誤)，因為它有 **MyWatchdog** 所回報的屬性「可用性」錯誤事件。

- 應用程式的其中一個服務 **fabric:/WordCount/WordCountService** 是黃色 (警告)。如上所述，服務已設有七個複本，但不能都放上去，因為我們只有五個節點。雖然這裡沒有顯示，但因為系統報告的緣故，所以服務分割區為黃色。黃色分割區觸發了黃色服務。

- 因為應用程式是紅色，所以叢集為紅色。

評估使用叢集資訊清單和應用程式資訊清單的預設原則。它們是嚴格的原則，不容許任何失敗。

使用 Service Fabric 總管檢視叢集：

![使用 Service Fabric 總管檢視叢集。][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] 深入了解 [Service Fabric 總管](service-fabric-visualizing-your-cluster.md)。

## 健康情況查詢
Service Fabric 會針對每種支援的[實體類型](service-fabric-health-introduction.md#health-entities-and-hierarchy)公開健康情況查詢。您可透過 API (方法可以在 **FabricClient.HealthManager** 上找到)、Powershell Cmdlet 和 REST 來存取查詢。這些查詢會傳回實體的完整健康情況資訊，包括彙總健康情況狀態、回報的實體健康情況事件、子健康情況狀態 (如果適用)，以及實體狀況不佳時的健康情況不佳評估。

> [AZURE.NOTE] 完全填入健康狀態存放區中時，健康情況實體會傳回給使用者。實體必須是作用中 (未刪除)，並且具有系統報告。階層鏈結上其父實體也必須有系統報告。如果無法達成上述任何條件，健康情況查詢會傳回顯示為何不傳回實體的例外狀況。

健康情況查詢需要傳入實體識別碼，識別碼會視實體類型而定。查詢會接受選擇性的健康情況原則參數。如果未指定，會使用叢集或應用程式資訊清單的[健康情況原則](service-fabric-health-introduction.md#health-policies)進行評估。查詢也接受只傳回部分事件或子事件的篩選，這些事件與所指定的篩選相符。

> [AZURE.NOTE] 輸出篩選會套用在伺服器端，所以訊息回覆的大小會減少。建議使用輸出篩選來限制傳回的資料，而不是在用戶端上套用篩選。

實體的健康情況含有下列資訊：

- 實體的彙總健康情況狀態。這是健康情況存放區根據實體健康情況報告、子健康情況狀態 (如果適用) 和健康情況狀態原則而得出的結果。深入了解[實體健康情況評估](service-fabric-health-introduction.md#entity-health-evaluation)。  

- 實體上的健康情況事件。

- 針對可以有子系的實體，提供所有子系的健康情況狀態集合。健康情況狀態包含實體識別碼和彙總健康情況狀態。若要取得子系的完整健康情況，請傳入子系識別碼，呼叫子實體類型的查詢健康情況。

- 如果實體的狀況不佳，則健康情況不佳的評估會指向觸發實體狀態的報告。

## 取得叢集健康情況
這會傳回叢集實體的健康情況，並且包含應用程式和節點 (叢集的子系) 的健康情況狀態。輸入：

- [選擇性] 用來評估節點和叢集事件的叢集健康狀態原則。

- [選擇性] 應用程式健康情況原則對應，加上用來覆寫應用程式資訊清單原則的健康情況原則。

- [選擇性] 事件、節點和應用程式的篩選，指定對那些項目感到興趣，並且應該在結果中傳回 (例如，僅限錯誤、或警告和錯誤)。請注意，所有事件、節點及應用程式都會用來評估實體彙總健康情況，不論篩選器為何。

### API
若要取得叢集健康情況，請建立 `FabricClient`，並在其 **HealthManager** 上呼叫 [GetClusterHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) 方法。

以下方法可取得叢集健康情況：

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

以下方法使用自訂的叢集健康情況原則和針對節點與應用程式的篩選，來取得叢集健康情況。請注意，這會建立包含所有輸入資料的 [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx)。

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### PowerShell
取得叢集健康情況的 Cmdlet 是 [Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx)。首先會使用 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) Cmdlet 連接到叢集。

叢集的狀態是五個節點、系統應用程式和如上述所設定的 fabric:/WordCount。

以下 Cmdlet 會使用預設的健康情況原則，取得叢集健康情況。彙總健康情況狀態為警告，因為 fabric:/WordCount 應用程式處於警告狀態。請注意健康情況不佳的評估，會如何提供觸發彙總健康情況的條件的詳細資料。

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=false.


NodeHealthStates        :
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

以下 Powershell Cmdlet 會使用自訂的應用程式原則，取得叢集的健康情況。它會篩選結果，以取得只有錯誤或警告的應用程式和節點。如此一來，就不會傳回任何狀況良好的節點。只有 fabric:/WordCount 應用程式符合應用程式篩選。由於自訂原則針對 fabric:/WordCount 應用程式，指定將警告視為錯誤，因此應用程式以及叢集，均被評估為錯誤。

```powershell
PS c:> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error"


AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

## 取得節點的健康情況
這會傳回節點實體的健康情況，並且包含節點上報告的健康狀態事件。輸入：

- [必要] 可識別節點的節點名稱。

- [選擇性] 用來評估健康情況的叢集健康情況原則設定。

- [選擇性] 事件的篩選，指定對那些項目感到興趣，並且應該在結果中傳回 (例如，僅限錯誤、或警告和錯誤)。請注意，所有事件都會用來評估實體彙總健康情況，不論篩選器為何。

### API
若要透過 API 取得節點健康情況，請建立 `FabricClient`，並在其 HealthManager 上呼叫 [GetNodeHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getnodehealthasync.aspx) 方法。

以下方法會取得指定節點名稱的節點健康情況：

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

以下方法會透過 [NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx) 傳入事件篩選和自訂原則，取得指定節點名稱的節點健康情況：

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### PowerShell
取得節點健康情況的 Cmdlet 是 [Get-ServiceFabricNodeHealth](https://msdn.microsoft.com/library/mt125937.aspx)。首先會使用 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) Cmdlet 連接到叢集。以下 Cmdlet 會使用預設的健康情況原則，取得節點健康情況：

```powershell
PS C:\> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 6
                        SentAt                : 3/22/2016 7:47:56 PM
                        ReceivedAt            : 3/22/2016 7:48:19 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:48:19 PM, LastWarning = 1/1/0001 12:00:00 AM
```

以下 Cmdlet 會取得叢集中所有節點的健康情況：

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_2                     Ok
_Node_0                     Ok
_Node_1                     Ok
_Node_3                     Ok
_Node_4                     Ok
```

## 取得應用程式健康情況
會傳回應用程式實體的健康情況。包含已部署應用程式和服務子系的健康情況狀態。輸入：

- [必要] 可識別應用程式的應用程式名稱 (URI)。

- [選擇性] 用來覆寫應用程式資訊清單原則的應用程式健康情況原則。

- [選擇性] 事件、服務和已部署應用程式的篩選，指定對那些項目感到興趣，並且應該在結果中傳回 (例如，僅限錯誤、或警告和錯誤)。請注意，所有事件、服務及已部署應用程式都會用來評估實體彙總健康情況，不論篩選器為何。

### API
若要取得應用程式健康情況，請建立 `FabricClient`，並在其 HealthManager 上呼叫 [GetApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getapplicationhealthasync.aspx) 方法。

以下方法會取得指定應用程式名稱 (URI) 的應用程式健康情況：

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

以下方法會透過 [ApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.applicationhealthquerydescription.aspx) 指定篩選和自訂原則，取得指定應用程式名稱 (URI) 的應用程式健康情況。

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### PowerShell
取得應用程式健康情況的 Cmdlet 是 [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx)。首先會使用 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) Cmdlet 連接到叢集。

以下 Cmdlet 會傳回 **fabric:/WordCount** 應用程式的健康情況：

```powershell
PS c:>
PS C:\WINDOWS\system32>  Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Ok
                                  SequenceNumber        : 131031545225930951
                                  SentAt                : 3/22/2016 9:08:42 PM
                                  ReceivedAt            : 3/22/2016 9:08:42 PM
                                  TTL                   : Infinite
                                  Description           : Availability checked successfully, latency ok
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 8:55:39 PM, LastWarning = 1/1/0001 12:00:00 AM
```

以下 PowerShell Cmdlet 會傳入自訂原則。它也會篩選子系和事件。

```powershell
PS C:\> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

## 取得服務健康情況
傳回服務實體的健康情況。包含分割區的健康情況狀態。輸入：

- [必要] 可識別服務的服務名稱 (URI)。

- [選擇性] 用來覆寫應用程式資訊清單原則的應用程式健康情況原則。

- [選擇性] 事件和分割區的篩選，指定對那些項目感到興趣，並且應該在結果中傳回 (例如，僅限錯誤、或警告和錯誤)。請注意，所有事件和分割區都會用來評估實體彙總健康情況，不論篩選器為何。

### API
若要透過 API 取得服務健康情況，請建立 `FabricClient`，並在其 HealthManager 上呼叫 [GetServiceHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getservicehealthasync.aspx) 方法。

以下範例會使用指定的服務名稱 (URI)，取得服務的健康情況：

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

以下方法會透過 [ServiceHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.servicehealthquerydescription.aspx) 指定篩選和自訂原則，取得指定服務名稱 (URI) 的服務健康情況：

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### PowerShell
取得服務健康情況的 Cmdlet 是 [Get-ServiceFabricServiceHealth](https://msdn.microsoft.com/library/mt125984.aspx)。首先會使用 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) Cmdlet 連接到叢集。

以下 Cmdlet 會使用預設的健康情況原則，取得服務健康情況：

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131031547693687021
                        SentAt                : 3/22/2016 9:12:49 PM
                        ReceivedAt            : 3/22/2016 9:12:49 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
```

## 取得分割區健康情況
傳回分割區實體的健康情況。包含複本的健康情況狀態。輸入：

- [必要] 可識別分割區的分割識別碼 (GUID)。

- [選擇性] 用來覆寫應用程式資訊清單原則的應用程式健康情況原則。

- [選擇性] 事件和複本的篩選，指定對那些項目感到興趣，並且應該在結果中傳回 (例如，僅限錯誤、或警告和錯誤)。請注意，所有事件和複本都會用來評估實體彙總健康情況，不論篩選器為何。

### API
若要透過 API 取得分割區健康情況，請建立 `FabricClient`，並在其 HealthManager 上呼叫 [GetPartitionHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getpartitionhealthasync.aspx) 方法。若要指定選擇性參數，請建立 [PartitionHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.partitionhealthquerydescription.aspx)。

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### PowerShell
取得分割區健康情況的 Cmdlet 是 [Get-ServiceFabricPartitionHealth](https://msdn.microsoft.com/library/mt125869.aspx)。首先會使用 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) Cmdlet 連接到叢集。

以下 Cmdlet 會取得 **fabric:/WordCount/WordCountService** 服務之所有分割區的健康情況：

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 131031502143040223
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844060
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844059
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844061
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844058
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 76
                        SentAt                : 3/22/2016 7:57:26 PM
                        ReceivedAt            : 3/22/2016 7:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

## 取得複本健康情況
這樣會傳回可設定具狀態服務複本或無狀態服務執行個體的健康狀態。輸入：

- [必要] 可識別複本的分割區識別碼 (GUID) 和複本識別碼。

- [選擇性] 用來覆寫應用程式資訊清單原則的應用程式健康情況原則參數。

- [選擇性] 事件的篩選，指定對那些項目感到興趣，並且應該在結果中傳回 (例如，僅限錯誤、或警告和錯誤)。請注意，所有事件都會用來評估實體彙總健康情況，不論篩選器為何。

### API
若要透過 API 取得複本健康情況，請建立 `FabricClient`，並在其 HealthManager 上呼叫 [GetReplicaHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getreplicahealthasync.aspx) 方法。若要指定進階參數，請使用 [ReplicaHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.replicahealthquerydescription.aspx)。

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### PowerShell
取得複本健康情況的 Cmdlet 是 [Get-ServiceFabricReplicaHealth](https://msdn.microsoft.com/library/mt125808.aspx)。首先會使用 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) Cmdlet 連接到叢集。

以下 Cmdlet 會針對服務的所有分割區取得主要複本健康情況：

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
ReplicaId             : 131031502143040223
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131031502145556748
                        SentAt                : 3/22/2016 7:56:54 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## 取得已部署應用程式的健康情況
會傳回部署在節點實體上的應用程式健康情況。包含已部署的服務封裝健康情況狀態。輸入：

- [必要] 可識別已部署應用程式的應用程式名稱 (URI) 和節點名稱 (字串)。

- [選擇性] 用來覆寫應用程式資訊清單原則的應用程式健康情況原則。

- [選擇性] 事件和已部署服務封裝的篩選，指定對那些項目感到興趣，並且應該在結果中傳回 (例如，僅限錯誤、或警告和錯誤)。請注意，所有事件和已部署服務封裝都會用來評估實體彙總健康情況，不論篩選器為何。

### API
若要透過 API 取得部署在節點上之應用程式的健康情況，請建立 `FabricClient` 並在其 HealthManager 上呼叫 [GetDeployedApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync.aspx) 方法。若要指定選擇性參數，請使用 [DeployedApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedapplicationhealthquerydescription.aspx)。

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### PowerShell
取得已部署應用程式健康情況的 Cmdlet 是 [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx)。首先會使用 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) Cmdlet 連接到叢集。若要找出應用程式的部署位置，請執行 [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx)，並查看部署的應用程式子系。

以下 Cmdlet 會取得部署在 **\_Node\_2** 節點上的 **fabric:/WordCount** 應用程式健康情況。

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_2


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_2
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131031502143710698
                                     SentAt                : 3/22/2016 7:56:54 PM
                                     ReceivedAt            : 3/22/2016 7:57:12 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## 取得已部署服務封裝的健康情況
會傳回已部署服務封裝實體的健康情況。輸入：

- [必要] 可識別已部署服務封裝的應用程式名稱 (URI)、節點名稱 (字串) 及服務資訊清單名稱 (字串)。

- [選擇性] 用來覆寫應用程式資訊清單原則的應用程式健康情況原則。

- [選擇性] 事件的篩選，指定對那些項目感到興趣，並且應該在結果中傳回 (例如，僅限錯誤、或警告和錯誤)。請注意，所有事件都會用來評估實體彙總健康情況，不論篩選器為何。

### API
若要透過 API 取得已部署服務封裝的健康情況，請建立 `FabricClient` 並在其 HealthManager 上呼叫 [GetDeployedServicePackageHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync.aspx) 方法。若要指定選擇性參數，請使用 [DeployedServicePackageHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedservicepackagehealthquerydescription.aspx)。

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### PowerShell
取得已部署服務封裝健康情況的 Cmdlet 是 [Get-ServiceFabricDeployedServicePackageHealth](https://msdn.microsoft.com/library/mt163525.aspx)。首先會使用 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) Cmdlet 連接到叢集。若要查看應用程式的部署位置，請執行 [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx)，並查看部署的應用程式。若要查看應用程式中的服務封裝為何，請檢視 [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx) 輸出中已部署服務封裝的子系。

以下 Cmdlet 會取得部署在 **\_Node\_2** 上 **fabric:/WordCount** 應用程式的 **WordCountServicePkg** 服務封裝健康情況。此實體的 **System.Hosting** 報告具有成功的服務封裝和進入點啟用，以及成功的服務類型註冊。

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : _Node_2
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 131031502301306211
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 131031502301568982
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 131031502314788519
                        SentAt                : 3/22/2016 7:57:11 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## 健全狀況區塊查詢
健全狀況區塊查詢可以傳回每個輸入篩選器的多層級叢集子系 (以遞迴方式)。它支援進階的篩選器，可提供許多彈性來表示要由唯一識別碼或其他群組識別碼及/或健康狀態傳回與識別的特定子系。根據預設，沒有子系包含在內，不同於永遠包含第一個層級子系的健全狀況命令。

[健全狀況查詢](service-fabric-view-entities-aggregated-health.md#health-queries)只會傳回每個必要篩選器之指定實體的第一個層級子系。若要取得子系的子系，使用者必須呼叫每個相關實體的其他健全狀況 API。同樣地，若要取得特定實體的健全狀況，使用者必須呼叫每個所需實體的一個健全狀況 API。區塊查詢進階篩選可讓使用者在一個查詢中要求多個相關項目，將訊息大小和訊息數目降至最低。

區塊查詢的值是使用者可以在一個呼叫中取得多個叢集實體 (可能是在必要的根開始的所有叢集實體) 的健康狀態。您可以如下表示複雜的健全狀況查詢︰

- 只傳回錯誤的應用程式，以及針對這些應用程式，包含所有警告|錯誤的服務。針對傳回的服務，包含所有分割。

- 只傳回 4 個應用程式的健全狀況，由其名稱指定。

- 只傳回所需應用程式類型的應用程式健全狀況。

- 傳回單一節點上所有已部署的實體。這樣會傳回所有應用程式，單一指定節點上所有已部署的應用程式，與該節點上所有已部署的服務封裝。

- 傳回所有錯誤的複本。傳回所有應用程式、服務、分割，以及只傳回錯誤的複本。

- 傳回所有應用程式。針對指定的服務，包含所有分割。

目前的健全狀況區塊查詢僅對叢集實體公開。它會傳回叢集健全狀況區塊，其中包含︰

- 叢集彙總健康狀態。

- 採用輸入篩選器的節點健康狀態區塊清單。

- 採用輸入篩選器的應用程式健康狀態區塊清單。每個應用程式健康狀態區塊都包含下列兩個區塊清單，具備所有採用輸入篩選器之服務的區塊清單，以及具備所有採用篩選器之部署應用程式的區塊清單。對於服務和已部署應用程式的子系亦然。如此一來，叢集中的所有實體都有可能在要求時以階層的形式傳回。

### 叢集健全狀況區塊查詢
這會傳回叢集實體的健全狀況，並包含必要子系的階層健康狀態區塊。輸入：

- [選擇性] 用來評估節點和叢集事件的叢集健康狀態原則。

- [選擇性] 應用程式健康情況原則對應，加上用來覆寫應用程式資訊清單原則的健康情況原則。

- [選擇性] 節點和應用程式的篩選器，指定對那些項目感到興趣，並且應該在結果中傳回。篩選器專屬於實體的實體/群組，或適用於該層級的所有實體。篩選器清單可包含一個一般篩選器及/或由查詢傳回之細微實體的特定識別碼篩選器。如果子系是空的，依預設不會傳回子系。如需進一步了解篩選器，請參閱 [NodeHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.nodehealthstatefilter.aspx) 和 [ApplicationHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthstatefilter.aspx)。應用程式篩選器可以遞迴方式指定進階的篩選器給子系。

區塊結果包含採用篩選器的子系。

區塊查詢目前不會傳回狀況不良的評估或實體事件。這些項目可使用現有的叢集健全狀況查詢取得。

### API
若要取得叢集健康區塊，請建立 `FabricClient`，並在其 **HealthManager** 上呼叫 [GetClusterHealthChunkAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync.aspx) 方法。您可以傳入 [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthchunkquerydescription.aspx) 來描述健康狀態原則和進階篩選器。

下列項目使用進階篩選器取得叢集健全狀況區塊。

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### PowerShell
取得叢集健康情況的 Cmdlet 是 [Get-ServiceFabricClusterChunkHealth](https://msdn.microsoft.com/library/mt644772.aspx)。首先會使用 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) Cmdlet 連接到叢集。

下列項目只有在錯誤時才會取得節點，只有一個特定節點例外，應該一律將其傳回。

```xml
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters

HealthState                  : Error
NodeHealthStateChunks        :
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

下列 Cmdlet 會利用應用程式篩選器取得叢集區塊。

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters

HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks :
                                   TotalCount            : 1

                                   ServiceName           : fabric:/WordCount/WordCountService
                                   HealthState           : Error
                                   PartitionHealthStateChunks :
                                       TotalCount            : 1

                                       PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                                       HealthState           : Error
                                       ReplicaHealthStateChunks :
                                           TotalCount            : 5

                                           ReplicaOrInstanceId   : 131031502143040223
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844060
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844059
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844061
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844058
                                           HealthState           : Error
```

下列 Cmdlet 會傳回單一節點上所有的已部署實體。

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 1

                                       ServiceManifestName   : FAS
                                       HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 2

                                       ServiceManifestName   : WordCountServicePkg
                                       HealthState           : Ok

                                       ServiceManifestName   : WordCountWebServicePkg
                                       HealthState           : Ok
```

## 一般查詢
一般查詢會傳回指定類型的 Service Fabric 實體清單。這些查詢會透過 API (透過 **FabricClient.QueryManager** 上的方法)、Powershell Cmdlet 和 REST 來公開。這些查詢會從多個元件彙總子查詢。其中一個元件是[健健康狀態資料存放區](service-fabric-health-introduction.md#health-store)，它會針對每個查詢結果填入彙總健康情況狀態。

> [AZURE.NOTE] 一般查詢會傳回實體的彙總健康情況狀態，但不包含豐富的健康情況資料。如果實體狀況不佳，您可以使用健康情況查詢加以追蹤，以取得所有的健康情況資訊，包括事件、子系健康情況狀態和健康情況不佳的評估。

如果一般查詢傳回不明的實體健康狀態，則可能是健康狀態存放區中沒有實體的完整資料。此外，也可能是健康狀態存放區的子查詢未成功 (例如，發生通訊錯誤，或已節流處理健康狀態存放區)。請針對實體使用健康情況查詢加以追蹤。如果子查詢發生暫時性錯誤，例如網路問題，此待處理的查詢可能會成功。它也可以從健康狀態存放區提供關於為何實體不公開的詳細資料。

包含實體的 **HealthState** 的查詢為：

- 節點清單：會傳回叢集中的節點清單 (已分頁)。
  - API：[FabricClient.QueryClient.GetNodeListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getnodelistasync.aspx)
  - PowerShell：Get-ServiceFabricNode
- 應用程式清單：會傳回叢集中的應用程式清單 (已分頁)。
  - AP：[FabricClient.QueryClient.GetApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getapplicationlistasync.aspx)
  - PowerShell：Get-ServiceFabricApplication
- 服務清單：傳回應用程式中的服務清單 (已分頁)。
  - API：[FabricClient.QueryClient.GetServiceListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getservicelistasync.aspx)
  - PowerShell：Get-ServiceFabricService
- 分割區清單：傳回服務中的分割區清單 (已分頁)。
  - API：[FabricClient.QueryClient.GetPartitionListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getpartitionlistasync.aspx)
  - PowerShell：Get-ServiceFabricPartition
- 複本清單：傳回分割區中的複本清單 (已分頁)。
  - API：[FabricClient.QueryClient.GetReplicaListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getreplicalistasync.aspx)
  - PowerShell：Get-ServiceFabricReplica
- 已部署應用程式清單：傳回節點上已部署應用程式的清單。
  - API：[FabricClient.QueryClient.GetDeployedApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync.aspx)
  - PowerShell：Get-ServiceFabricDeployedApplication
- 已部署服務封裝清單：傳回已部署應用程式中的服務封裝清單。
  - API：[FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync.aspx)
  - PowerShell：Get-ServiceFabricDeployedApplication

> [AZURE.NOTE] 有些查詢會傳回已分頁的結果。這些查詢的傳回內容是衍生自 [PagedList<T>](https://msdn.microsoft.com/library/azure/mt280056.aspx) 的清單。如果結果不符合訊息，只會傳回頁面，且 ContinuationToken 設為追蹤列舉停止之處。使用者應該繼續呼叫相同的查詢，並從先前的查詢傳入接續權杖以取得後續結果。

### 範例

以下方法會取得叢集中健康情況不佳的應用程式：

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

以下 Cmdlet 會取得 fabric:/WordCount 應用程式的應用程式詳細資料。請注意，健康情況狀態為警告。

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

以下 Cmdlet 會取得健康情況狀態為警告的服務：

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## 叢集和應用程式升級
叢集與應用程式的受監視升級期間，Service Fabric 會檢查健康情況，以確保一切都能維持在健康情況良好的狀態。如果實體藉由使用已設定的健康狀況原則評估為狀況不良，升級會套用升級特定原則來決定下一個動作。升級可能會暫停，以允許使用者互動 (例如修正錯誤條件或變更原則)，或是它會自動回復成先前的良好版本。

在叢集升級期間，您可以取得叢集升級狀態。這包括任何狀況不良的評估，指向叢集中狀況不良的項目。如果因為健康情況問題使升級遭到回復，升級狀態會保留最後一批狀況不佳的原因。如此可以保留資訊，協助管理員調查問題出在哪裡。

同樣地，在應用程式升級期間，應用程式升級狀態也會包含任何狀況不良的評估。

以下顯示修改後的 fabric:/WordCount 應用程式的應用程式升級狀態。監視程式回報了它其中一個複本的錯誤。因為健康情況檢查未通過，所以會將升級回復。

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

深入了解 [Service Fabric 應用程式升級](service-fabric-application-upgrade.md)。

## 使用健康狀況評估以進行疑難排解
叢集或應用程式中發生問題時，查看叢集或應用程式的健康情況，可以找出發生問題的原因。健康情況不佳的評估會提供觸發目前狀況不佳狀態的原因的詳細資料。如果需要，您可以向下鑽研至狀況不良的子實體，以識別根本原因。

> [AZURE.NOTE] 狀況不良的評估顯示實體的第一個原因評估為目前的健康狀態。可能有多個其他事件觸發此狀態，但是評估中不會反映這些事件。您必須向下鑽研健全狀況實體，以找出叢集中所有狀況不良的報告。

## 後續步驟
[使用系統健康狀態報告進行疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[新增自訂 Service Fabric 健康狀態報告](service-fabric-report-health.md)

[在本機上監視及診斷服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric 應用程式升級](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0323_2016-->