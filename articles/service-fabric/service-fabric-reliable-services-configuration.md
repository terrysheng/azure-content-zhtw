<properties
   pageTitle="Azure Service Fabric Reliable Services 組態概觀 | Microsoft Azure"
   description="深入了解在 Azure Service Fabric 中設定具狀態的 Reliable Services。"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/28/2015"
   ms.author="sumukhs"/>

# 設定具狀態可靠的服務
可設定狀態的可靠服務，其預設組態可以透過組態封裝 (組態)，或在服務實作 (程式碼) 中加以修改。

+ **組態** - 您可以藉由變更在 Visual Studio 封裝的根目錄 "Config" 資料夾下，為應用程式中的每個服務產生的 "settings.xml" 檔案，來透過組態封裝完成組態。
+ **程式碼** - 您可以覆寫 StatefulService.CreateReliableStateManager，並使用 ReliableStateManagerConfiguration 物件搭配適當的選項設定建立 ReliableStateManager，來透過程式碼完成組態。

根據預設，Service Fabric 執行階段會在建立基礎執行階段元件時，於 "settings.xml" 檔案中尋找預先定義的區段名稱，並使用組態值。

> [AZURE.NOTE]請**不要**刪除在 Visual Studio 方案中產生之 "settings.xml" 檔案中的下列組態區段名稱，除非您打算透過程式碼設定服務。設定 ReliableStateManager 時，重新命名組態封裝或區段名稱將需要變更程式碼。


## 複寫器安全性組態
複寫器安全性組態用來保護在複寫期間使用的通訊通道。這表示服務將無法看到彼此的複寫流量，並且也會確保高度可用資料的安全。依預設，空白的安全性組態區段不會啟用複寫安全性。

### 預設區段名稱
ReplicatorSecurityConfig

> [AZURE.NOTE]若要變更此區段名稱，請在建立此服務的 ReliableStateManager 時，將 replicatorSecuritySectionName 參數覆寫至 ReliableStateManagerConfiguration 建構函式。


## 複寫器組態
複寫器組態用來設定複寫器，負責藉由複寫和保存在本機的狀態，讓具狀態之可靠的服務狀態非常可靠。預設組態由 Visual Studio 範本所產生，且應該已經足夠。本節說明可用於微調複寫器的其他組態。

### 預設區段名稱
ReplicatorConfig

> [AZURE.NOTE]若要變更此區段名稱，請在建立此服務的 ReliableStateManager 時，將 replicatorSettingsSectionName 參數覆寫至 ReliableStateManagerConfiguration 建構函式。


### 組態名稱
|名稱|單位|預設值|備註|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|秒|0\.05|次要複寫器收到作業後，將通知傳回給主要複寫器前所等待的時間間隔。任何要在此間隔內傳送給作業處理的其他通知，會集中以一個回應傳送。|
|ReplicatorEndpoint|N/A|N/A - RequiredParameter|主要/次要複寫器將用於與複本集中其他複寫器通訊的 IP 位址與連接埠。這應該參考服務資訊清單中的 TCP 資源端點。請參閱＜[服務資訊清單資源](service-fabric-service-manifest-resources.md)＞，深入瞭解如何在服務資訊清單中定義端點資源。 |
|MaxPrimaryReplicationQueueSize|作業數目|8192|主要佇列中作業數目上限。主要複寫器收到所有次要複寫器的通知後，系統便會釋放作業。此值必須大於 64 且為 2 的乘冪。|
|MaxSecondaryReplicationQueueSize|作業數目|16384|次要佇列中作業數目上限。透過持續性讓狀態成為高可用性後，系統便會釋放作業。此值必須大於 64 且為 2 的乘冪。|
|CheckpointThresholdInMB|MB|200|狀態完成檢查點作業後的記錄檔空間量。|
|MaxRecordSizeInKB|KB|1024|複寫器可以寫入記錄檔中的最大記錄大小。此值必須是 4 的倍數且大於 16。|
|OptimizeLogForLowerDiskUsage|布林值|true|為 true 時會設定記錄檔，以便使用 NTFS 疏鬆檔案來建立複本的專用記錄檔。這會降低檔案實際使用的磁碟空間量。為 false 時，將以固定配置建立檔案，並提供最佳寫入效能。|
|MaxRecordSizeInKB|KB|1024|複寫器可以寫入記錄檔中的最大記錄大小。此值必須是 4 的倍數且大於 16。|
|SharedLogId|guid|""|指定用於識別此複本共用記錄檔的唯一 GUID。服務通常不應使用此設定，不過如果指定了 SharedLogId，則也必須指定 SharedLogPath。|
|SharedLogPath|完整路徑名稱|""|指定建立此複本共用記錄檔的完整路徑。服務通常不應使用此設定，不過如果指定了 SharedLogPath，則也必須指定 SharedLogId。|


## 透過程式碼的範例組態
```csharp
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            new ReliableStateManagerReplicatorSettings
            {
                RetryInterval = TimeSpan.FromSeconds(3)
            }));
}
```


## 範例組態檔
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


## 備註
BatchAcknowledgementInterval 會控制複寫延遲性。值為 '0' 時延遲可能性最低，但代價是降低輸送量 (隨著必須傳送與處理的通知訊息增加，每個訊息包含的通知會變少)。BatchAcknowledgementInterval 的值越大，整體複寫輸送量越高，代價是作業延遲變高。這會直接轉換成交易認可的延遲。

CheckpointThresholdInMB 的值控制複寫器可用來將狀態資訊儲存在複本專用記錄檔中的磁碟空間量。若此值大於預設值，當記錄中具有更多可用的歷程記錄作業而造成部分狀態傳送以至於將複本新增至集合時，會加速重新設定的時間，並且可能在當機之後增加複本的復原時間。

OptimizeForLowerDiskUsage 設定允許「過度佈建」記錄檔空間，讓作用中的複本可以在其記錄檔中儲存較多狀態資訊，而非作用中的複本則會使用較少的磁碟空間。雖然這允許在一個節點上裝載更多複本，但不使用時將造成磁碟空間不足，而透過將 OptimizeForLowerDiskUsage 設定為 False，狀態資訊會更快速地寫入記錄檔。

MaxRecordSizeInKB 定義複寫器可以寫入記錄檔的記錄大小上限。在大部分情況下，預設的 1024 KB 記錄大小是最佳作法，不過如果服務造成更大的資料項目成為狀態資訊的一部分，則可能需要增加此值。讓 MaxRecordSizeInKB 小於 1024 的好處不大，因為較小的記錄只會使用需要較小記錄的空間。預期只會在極少數的情況下需要變更。

SharedLogId 和 SharedLogPath 設定永遠會一起使用，並允許服務使用與節點預設共用記錄檔不同的共用記錄檔。如需最佳效率，請儘可能讓所有服務指定相同的共用記錄檔。共用記錄檔應該放在共用記錄檔專用的磁碟上，以減少磁頭移動爭用情形。預期它只會在極少數的情況下需要變更。

<!---HONumber=AcomDC_1203_2015-->