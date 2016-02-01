<properties
   pageTitle="加入自訂 Service Fabric 健康狀態報告 | Microsoft Azure"
   description="描述如何將自訂健康狀態報告傳送給 Azure Service Fabric 健康狀態實體。提供設計和實作高品質健康狀態報告的建議。"
   services="service-fabric"
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
   ms.date="01/20/2015"
   ms.author="oanapl"/>

# 新增自訂 Service Fabric 健康狀態報告
Azure Service Fabric 引入了[健康狀態模型](service-fabric-health-introduction.md)，主要用於在特定實體上標示狀況不良的叢集和應用程式條件。使用**健康狀態報告程式**即可達成 (系統元件及監視程式)。目標為輕易迅速的診斷並修復問題。服務寫入器必須預先考慮到健康狀態。任何可能會影響到健康狀態的條件都需加以回報，尤其是如果它有助標示出接近根目錄的問題。一旦在雲端 (不論是私人或 Azure) 啟動並執行服務，此舉可大幅減少偵錯和調查所需的時間和工作量。

Service Fabric 報告程式可監控感興趣的已識別條件。它們會依據其本機檢視回報這些條件。[健康狀態存放區](service-fabric-health-introduction.md#Health-Store)可彙總報告程式送出的所有健康狀態資料，以判斷實體的健康狀態是否為全域良好。必須為豐富、彈性且容易使用的模型。健康狀態報告的品質可決定叢集的健康狀態檢視準確度。不正確顯示出健康不良的問題之誤報，會對升級或其他使用健康狀態資料的服務產生負面影響。其中包含修復服務和警示機制。因此，需要針對報告加以考量，才能讓其以最佳的方式擷取感興趣的條件。

若要設計與實作健康狀態的報告，監視程式及系統元件必須：

- 定義它們感興趣的條件，受監視的方式，以及對叢集或應用程式功能的影響。這會定義健康狀態報告屬性與健康狀態。

- 決定報告適用的[實體](service-fabric-health-introduction.md#health-entities-and-hierarchy)。

- 決定完成報告的位置，從服務內部或從內部或外部監視程式。

- 定義用於識別報告程式的來源。

- 選擇報告策略是定期或是轉換時。建議以定期的方式，因為它只需較簡單的程式碼，因此較不容易發生錯誤。

- 決定健康狀態不良條件的報告，可在健康狀態存放區中停留多久，以及應該如何清除。這會定義報告的存留時間和到期移除行為。

如上所述，報告可以從以下項目完成：

- 受監視的 Service Fabric 服務複本。

- 內部監視程式會部署為 Service Fabric 服務 (例如：可監視條件和問題報告的 Service Fabric 無狀態服務)。可以將監視程式部署為所有節點，或可以與受監視的服務相關。

- 在 Service Fabric 節點上執行，但未以 Service Fabric 服務實作的內部監視程式。

- 從 Service Fabric 叢集外探查資源的外部監視程式。

> [AZURE.NOTE]根據現有設定，叢集會填入系統元件傳送的健康狀態報告。在此閱讀更多[使用系統健康狀態報告進行疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)的相關資訊。必須在系統所建立的[健康狀態實體](service-fabric-health-introduction.md#health-entities-and-hierarchy)上傳送使用者報告。

一旦健康狀態報告設計清楚，即可輕鬆傳送健康狀態報告。透過 API (使用 **FabricClient.HealthManager.ReportHealth**)、透過 Powershell 或透過 REST 即可完成。就內部而言，所有方法都會使用網狀架構用戶端內含的健康狀態用戶端。組態旋鈕批次報告可提升效能。

> [AZURE.NOTE]報告健康狀態會同步處理，且只代表用戶端上的驗證工作。健康狀態用戶端接受報告的這項事實並不表示該報告會套用在存放區中。它會以非同步方式傳送並可能與其他報告進行批次處理。在伺服器上處理仍可能會失敗 (例如序號已過時、必須套用報告的實體已被刪除等)。

## 健康狀態用戶端
健康狀態報告會透過存在於該網狀架構用戶端內的健康狀態用戶端來傳送至健康狀態存放區。可以使用下列來設定健康狀態用戶端：

- **HealthReportSendInterval**：報告新增至該用戶端，與其傳送至健康狀態存放區之間的時間延遲。這用於將報告批次處理為單一訊息，而不是針對每份報告傳送一則訊息。如此即可改善效能。預設值：30 秒。

- **HealthReportRetrySendInterval**：健康狀態用戶端重新傳送累積的健康狀態報告至健康狀態存放區的間隔時間。預設值：30 秒。

- **HealthOperationTimeout**：報告訊息傳送至健康狀態存放區的逾時期間。如果訊息逾時，健康狀態用戶端就會不斷重試，直到健康狀態存放區確認報告已處理。預設值：兩分鐘。

> [AZURE.NOTE]批次處理報告時，網狀架構用戶端必須至少保持運作長達 HealthReportSendInterval，以確保報告已傳送。如果訊息遺失或健康狀態存放區因為暫時性錯誤而無法套用它們，則網狀架構用戶端必須保持運作久一點，讓其有再試一次的機會。

用戶端上的緩衝會將報告的唯一性納入考量。例如，如果特定的錯誤報告程式在相同實體的相同屬性上每秒產生 100 個報告，則會以最後一個版本取代所有報告。最多只有一份這類報告存在於用戶端佇列中。如果設定批次處理，則傳送至健康狀態存放區的報告數目為每次傳送間隔一份報告。這是最後新增的報告，可反映實體的最新狀態。在建立 **FabricClient** 時，藉由針對健康狀態相關項目傳遞 **FabricClientSettings** 的所需值，即可指定所有組態參數。

以下會建立網狀架構用戶端，並指定一旦它們新增後就應該儘快傳送報告。在可重試的錯誤或逾時發生時，會每 40 秒重試一次。

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

透過 PowerShell 建立叢集連線時，可以指定相同的參數。下列會啟動連線至本機叢集：

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

> [AZURE.NOTE]若要確保未授權的服務無法針對叢集中的實體回報健康狀態，則可將伺服器設定為只接受來自受保護用戶端的要求。由於報告是透過 FabricClient 完成，這表示 FabricClient 必須啟用安全性才能與叢集通訊 (例如以 Kerberos 或憑證驗證)。

## 設計健康狀態報告
產生高品質報告的第一個步驟，是識別可能影響該服務健康狀態的條件。在條件啟動或甚至在發生之前，任何有助於在服務或在叢集中標示問題的條件，都有可能替您省下數十億元。好處包含停機時間變少，花在調查和修復問題的夜間時間也變少，且客戶滿意度變高。

一旦識別出條件，監視程式寫入器需要找出最佳的監視方式，以取得額外負荷和實用性之間的平衡。例如，考慮可使用共用上的一些暫存檔案進行複雜計算的服務。監視程式可以監視共用，以確保有足夠的空間。它可以接聽檔案或目錄變更的通知。它可以在達到預先臨界值時回報警告，並在共用已滿時回報錯誤。在警告時，修復系統可以開始清除共用上較舊的檔案。在錯誤時，修復系統可將服務複本移至另一個節點。請注意從健康狀態方面描述條件狀態的方式：可被視為狀況良好或狀況不良 (警告或錯誤) 的條件狀態。

一旦設定好監視的詳細資料，監視程式寫入器需要了解如何實作監視程式。如果條件可在服務內決定，則該監視程式可以成為監視服務本身的一部分。例如，服務程式碼可以在本機網狀架構用戶端每次嘗試寫入檔案時，檢查並回報共用使用量。這個方法的優點是報告變得輕而易舉。為避免監視程式的錯誤影響到服務功能，因此需多加留意。

您並非一定要選擇在受監視的服務內報告。服務中的監視程式可能無法偵測條件。可能沒有邏輯或資料可供做決定。監視條件的額外負荷可能很高。條件也可能不專屬某項服務，但是會影響服務之間的互動。另一個選項是在叢集中以監視程式做為個別處理序。監視程式只監視條件和報告，不會以任何方式影響到主要服務。例如，這些監視程式可在相同應用程式上以無狀態服務實作，或是當作服務在所有節點或相同節點上部署。

有時在叢集中執行監視程式也並非必要選項。如果監視條件是使用者所見的服務可用性或功能，監視程式最好能與使用者用戶端在相同的位置。在那裡，監視程式可以使用者呼叫它們的相同方式測試作業。例如，您的監視程式可以留存在叢集以外並對服務發出要求，然後檢查結果的延遲性和正確性。(例如在計算機服務中，2+2 是否會在合理的時間內傳回 4？)

一旦確定監視程式詳細資料，您應決定可識別它的唯一來源 ID。如果叢集中有多個相同類型的監視程式在運作，它們必須報告不同的實體，如果它們報告相同的實體，則必須確定來源 ID 或屬性皆不相同。如此一來，其報告才可並存。健康狀態報告的屬性應該能擷取受監視的條件。(如上述範例，該屬性可能是 **ShareSize**)。 如果多份報告套用至相同的條件，該屬性應該包含一些動態資訊，才可讓報告共存。例如，如果需要監視多個共用，該屬性名稱可以是 **ShareSize-sharename**。

> [AZURE.NOTE]健康狀態存放區不應該用來保留狀態資訊。只有與健康狀態相關的資訊才該回報健康狀態，因為此資訊會影響實體的健康狀態評估資訊。健康狀態存放區並非設計做為一般用途存放區。它會使用健康狀態評估邏輯來彙總健康狀態的所有資料。傳送與健康狀態不相關的資訊 (例如在健康狀態良好下報告狀態) 不會影響到彙總的健康狀態，但可能對健康狀態存放區的效能造成負面影響。

下一個決策點是在報告哪個實體。大部分的情況下，顯然是依據條件而定。您應選擇可能具最佳資料粒度的實體。如果條件在分割中會影響到所有複本，則在分割中回報，而非在服務中回報。以下為需要仔細考量的極端例子。如果條件影響到實體 (例如複本)，但所需標示條件要比複本存留期間更久，則應該要在資料分割上回報。否則當刪除複本時，與其相關的所有報告都會從存放區中清除。這表示監視程式寫入器也必須將實體和報告的存留期納入考量。報告需從存放區中清除的時間點必須清楚說明 (例如，當實體上回報的錯誤不再適用時)。

讓我們以一個例子解釋上述的要點。假設有一個由主要具狀態保存服務和所有節點上部署的從屬無狀態服務所組成的 Service Fabric 應用程式 (每種工作類型有一個從屬服務類型)。主要服務有一個處理佇列，其中包含從屬服務所要執行的命令。從屬服務會執行傳入要求並送回認可訊號。可以監視的其中一個條件是主要服務處理佇列的長度。如果主要服務佇列長度達到臨界值，則會回報警告。這表示從屬服務無法處理負載。如果佇列達到長度上限，而且命令已卸除，則會因為服務無法復原而回報錯誤。可以在 **QueueStatus** 屬性上回報。監視程式存留於服務內部，而它會在主要服務上的主要複本定期傳送。存留時間為 2 分鐘，其會於每隔 30 秒定期傳送。如果主要複本無法作用，報告會自動從存放區中清除。如果該服務複本已啟用，但發生死結或有其他問題，該報告將在健康狀態存放區中過期。在此情況下，將會在錯誤時評估實體。

其他可監視的條件為工作執行時間。主要服務會依據工作類型散發工作給從屬服務。根據設計而定，主要服務可以輪詢從屬服務，以取得工作狀態。也可以等候從屬服務在完成時送回認可。在第二個案例中，您必須特別注意並偵測出從屬服務停止或訊息遺失的情況。其中一種方法是主要服務傳送 Ping 要求給相同的從屬服務，而從屬服務會傳回其狀態。如果未收到狀態，則主要服務會將此視為失敗並重新排程工作。這會假設工作為等冪。

如果工作未在特定時間完成 (**t1**，如 10 分鐘)，我們可以將受監視的條件轉譯為警告，而如果工作未及時完成 (**t2**，如 20 分鐘)，則會轉譯為錯誤。此報告可以多種方式完成：

- 主要服務的主要複本本身會定期回報。針對在佇列中的所有暫止工作可以有一個屬性。如果至少有一個工作耗時較久，則 **PendingTasks** 屬性的報告狀態為警告或錯誤 (視情況而定)。如果沒有暫止工作或所有工作剛開始，則報告狀態為 [正常]。工作是持續性的，如果主要服務停止時，新升級的主要服務即可繼續適當地回報。

- 在雲端或外部的另一個監視程式處理序會檢查工作 (根據所需的工作結果從外部檢查)，以查看它們是否已完成。如果它們不採用臨界值，則會傳送主要服務的報告。也會傳送每個工作的報告，其中包含工作識別碼 (例如 **PendingTask+taskid**)。只有在狀況不良的狀態才需傳送報告。存留時間應設定為幾分鐘的時間，而報告應標示為要在過期時移除才可確保清除。

- 如果執行時間超過預期時間，執行工作的從屬服務就會回報。它會回報 **PendingTasks** 屬性上的服務執行個體。這樣會指出服務執行個體有問題，但它不會擷取執行個體停止的情況。因為報告在該時間已清除完畢。它可以回報從屬服務。如果從屬服務完成工作，則從屬執行個體會從存放區清除報告。如此並不會擷取到認可訊息遺失的情況，而從主要服務觀點看來，工作並未完成。

不過，在上述情況報告已完成，則評估過健康狀態後，會擷取應用程式健康狀態的報告。

## 定期報告與轉換時報告
使用健康狀態報告模型，監視程式可以定期或於轉換時傳送報告。建議以定期報告的方式，因為程式碼較為簡單，比較不容易發生錯誤。監視程式必須盡可能越簡單越好，以避免觸發誤報的錯誤。不正確的狀況不良報告將會影響健康狀態評估以及需依據健康狀態的情況，包括升級。不正確的狀況良好報告會隱藏叢集中的問題，我們不希望發生此情況。

針對定期報告，您可以用計時器實作監視程式。計時器回呼時，監視程式可以檢查狀態並依照目前情況傳送報告。不需要查看先前已傳送的報告或在傳訊方面進行任何最佳化。健康狀態用戶端具有批次邏輯以幫助此種情況。只要健康狀態用戶端持續作用，就會在內部重試直到報告被健康狀態存放區認可，或直到監視程式產生具有相同實體、屬性與來源的較新報告時。

轉換時的回報需要注意狀態處理。監視程式會監視某些條件，只會在條件改變時才回報。此方法的優點是需要較少的報告。缺點是監視程式的邏輯很複雜。還必須維護條件或報告，如此才可對其進行檢查，以判斷狀態變更。在容錯移轉時，必須注意是否傳送先前可能未傳送的報告 (已加入佇列，但尚未傳送至健康狀態存放區)。序號必須持續增加。若非如此，報告會因為過時而被拒絕。在造成資料遺失的少數情況下，可能需要同步處理報告程式的狀態與健康狀態存放區的狀態。

## 實作健康狀態報告
一旦清除了實體和報告的詳細資訊，即可透過 API、PowerShell 或 REST 完成傳送健康狀態報告。

### API
若要透過 API 回報，使用者必須建立其想要回報的實體類型特有的健康狀態報告。然後將此報告提供給健康狀態用戶端。

下列範例示範如何從叢集內的監視程式中定期回報。監控程式會檢查是否能在節點內存取外部資源。應用程式內服務資訊清單的所需資源。如果無法使用該資源，應用程式內的其他服務仍然可以正常運作。因此，會每隔 30 秒在已部署的服務封裝實體上傳送報告。

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### PowerShell
使用者可使用 **Send-ServiceFabric*EntityType*HealthReport** 傳送健康狀態報告。

下列範例會示範節點上 CPU 值的定期報告。報告應該每隔 30 秒傳送一次，其存留時間為 2 分鐘。如果過期，則報告程式有問題，因此會在錯誤時評估節點。當 CPU 高於臨界值時，報告的健康狀態為警告。當 CPU 保持高於臨界值超過設定的時間時，則會將其回報為錯誤。否則，報告程式傳送的健康狀態為 [正常]。

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

下列範例會回報在複本上的暫時性警告。它會先取得資料分割識別碼，再取得感興趣之服務的複本識別碼。然後從 **PowershellWatcher** 傳送 **ResourceDependency** 屬性的報告。此報告只需要存在 2 分鐘，就會從存放區中自動移除。

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

## 後續步驟

根據健康狀態資料，服務寫入器和叢集/應用程式管理員可想出使用該資訊的多種方式。例如，他們可以依據健康狀態為基礎設定警示，以便於嚴重問題誘發中斷前加以攔截。系統管理員也可以設定修復系統來自動修復問題。

[Service Fabric 健康狀態監視簡介](service-fabric-health-introduction.md)

[檢視 Service Fabric 健康狀態報告](service-fabric-view-entities-aggregated-health.md)

[使用系統健康狀態報告進行疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[在本機上監視及診斷服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric 應用程式升級](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0121_2016-->