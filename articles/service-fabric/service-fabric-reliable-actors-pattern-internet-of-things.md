
<properties
   pageTitle="適用於物聯網的 Reliable Actors | Microsoft Azure"
   description="Service Fabric Reliable Actors 是系統中的主要建置組塊，結合了支援多個傳輸 (例如 HTTPS、MQTT 和 AMQP)。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/14/2015"
   ms.author="vturecek"/>

# Reliable Actors 設計模式：物聯網
隨著科技進步，物聯網 (IoT) 已成為裝置與雲端服務的新趨勢，因此開發人員已開始尋找用來開發其系統的主要建置組塊。下圖說明使用 Service Fabric Reliable Actors 可達成的重要案例：

![Service Fabric Reliable Actors 主要案例][1]

Service Fabric Reliable Actors 是系統中的主要建置組塊 (作為中介層)，結合了支援多個傳輸 (例如 HTTPS、MQTT 和 AMQP) 的傳訊系統前端，以及與代表個別裝置的動作項目通訊。由於動作項目能夠維持狀態，因此依裝置製作串流的模型與彙總是很簡單的，尤其是可設定狀態的串流處理。如果必須保存資料，您也可以輕鬆地按照需求或按照計時器排清。這可能會發生於您仍可在另一個變數中維持最新 N 位元的資料，以供快速查詢時。

請注意，為了專注在我們的範例中的 Reliable Actors 模型，我們刻意省略讓動作項目能與裝置通訊的事件/傳訊層詳細資料。基本上通常有兩個組合在一起的案例：

* **收集一個或一組裝置的遙測與狀態資料，並維護其狀態**。想一想成千上萬個會傳送資訊的補鼠器 (這是真實的客戶案例)。此資料和裝置是否已補獲到老鼠一樣的基本。此類資料會依照區域彙總，若在一個區域中補獲到足夠的老鼠，便會派遣工程師清除裝置。將補鼠器比做動作項目？ 是。將各區域的群組動作項目作為彙總者？ 當然。

* **將裝置行為與組態推送到一個裝置或一組裝置**。請想一想家庭太陽能裝置，廠商會根據耗用模式和季節推出不同的組態。

## 遙測資料與裝置群組

首先，讓我們看一看有數萬個裝置群組在一起的案例，而這些裝置全部將遙測資料傳送到其相關聯的群組。在下列範例中，客戶已經將裝置部署至每個區域。裝置開啟時，第一件事就是傳送 **ActivateMe** 訊息及相關資訊 (包括位置和版本)。與裝置相關聯 (透過裝置識別碼) 的動作項目會設定裝置的初始狀態，例如在本機儲存狀態 (也可加以保存) 和註冊群組動作項目。在此狀況中，我們已指派隨機群組進行模擬。

在初始化的過程中，我們會從群組動作項目或其他的代理程式擷取組態，來設定裝置。這種方式會讓裝置一開始時很「愚蠢」，然後在初始化時變得有「智慧」。完成時，裝置和動作項目會準備好傳送與處理遙測資料。

所有裝置會定期將遙測資訊傳送給對應的動作項目。如果動作項目已啟用，則會使用同一個動作項目。否則會啟用該動作項目。此時可以視需要從穩定的存放區復原狀態。動作項目收到遙測資訊時，會將該資訊儲存至本機變數。

我們將這麼做來簡化範例。在實際的實作中，我們可能會將其儲存到外部存放區。這可讓作業來監視和診斷裝置的健康狀態與效能。最後，我們會將遙測資料推送到裝置動作項目在邏輯上所屬的群組動作項目。

## IoT 程式碼範例：遙測

```csharp
public interface IThing : IActor
{
    Task ActivateMe(string region, int version);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingState
{
	[DataMember]
	public List<ThingTelemetry> _telemetry;
	[DataMember]
	public ThingInfo _deviceInfo;
	[DataMember]
	long _deviceGroupId;
}

public class Thing : StatefulActor<ThingState>, IThing
{

    protected override Task OnActivateAsync()
    {
        State._telemetry = new List<ThingTelemetry>();
        State._deviceGroupId = -1; // not activated
        return base.OnActivateAsync();
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        State._telemetry.Add(telemetry); // saving data at the device level
        if (State._deviceGroupId != -1)
        {
            var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
            return deviceGroup.SendTelemetryAsync(telemetry); // sending telemetry data for aggregation
        }
        return Task.FromResult(true);
    }

    public Task ActivateMe(string region, int version)
    {
        State._deviceInfo = new ThingInfo()
        {
            DeviceId = this.GetPrimaryKeyLong(),
            Region = region,
            Version = version
        };

        // based on the info, assign a group... for demonstration we are assigning a random group
        State._deviceGroupId = new Random().Next(10, 12);

        var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
        return deviceGroup.RegisterDevice(State._deviceInfo);
    }
}
```

在群組層級，我們的範例中的目標是監視群組中的裝置，並將遙測資料彙總，來為工程師產生警示。在此狀況下，我們的客戶會想要將工程師送到有一些裝置故障的特定區域。當然我們的客戶也想要減少工程師花費在交通上的時間，藉此降低成本。基於這個理由，每個群組動作項目會為各區域維護一個故障裝置彙總狀態。當這個數字到達臨界值時，客戶就會派遣工程師到該區域以修理或更換故障裝置。

讓我們了解其運作方式：

## IoT 程式碼範例：群組與彙總

```csharp
public interface IThingGroup : IActor
{
    Task RegisterDevice(ThingInfo deviceInfo);
    Task UnregisterDevice(ThingInfo deviceInfo);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingGroupState
{
    [DataMember]
    public List<ThingInfo> _devices;
    [DataMember]
    public Dictionary<string, int> _faultsPerRegion;
    [DataMember]
    public List<ThingInfo> _faultyDevices;
}

public class ThingGroup : StatefulActor<ThingGroupState>, IThingGroup
{

    protected override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return Task.FromResult(true);
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return Task.FromResult(true);
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        if (telemetry.DevelopedFault)
        {
            if (false == _faultsPerRegion.ContainsKey(telemetry.Region))
            {
                State._faultsPerRegion[telemetry.Region] = 0;
            }
            State._faultsPerRegion[telemetry.Region]++;
            State._faultyDevices.Add(_devices.Where(d => d.DeviceId == telemetry.DeviceId).FirstOrDefault());

            if (State._faultsPerRegion[telemetry.Region] > _devices.Count(d => d.Region == telemetry.Region) / 3)
            {
                Console.WriteLine("Sending an engineer to repair/replace devices in {0}", telemetry.Region);
                foreach(var device in State._faultyDevices.Where(d => d.Region == telemetry.Region).ToList())
                {
                    Console.WriteLine("\t{0}", device);
                }
            }
        }

        return Task.FromResult(true);
    }
}
```

如您所見，此程式碼相當簡單。執行簡單測試後，輸出結果如下所示：

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

您可能會認為裝置最好已依區域分組。這完全取決於您選擇如何分組和/或分割裝置，不論是按地區位置、裝置類型、版本、租用戶或其他準則。

在此須注意一點，即裝置狀態對報告和分析：您必須避免展開傳送查詢給動作項目，來建置報告動作項目。這可能會導致不必要的具現化和效能問題 (只是其中兩個缺點)。這就是為何我們製作明確的模式圖。我們建議兩種報告的方法：

* 使用群組層級動作項目，例如彙總器，來維護群組的檢視。讓每個動作項目只會將相關資料主動地推送給此動作項目。群組層級的動作項目可用來檢視群組中裝置的狀態。

* 維護針對報告設計的明確存放區。彙總器可以緩衝處理，並定期將資料推送到報告存放區進一步查詢和分析。

## 裝置作業
目前為止很好。但是這些裝置的作業該怎麼辦？ 如同裝置，動作項目可以公開操作介面，讓管理員能夠在裝置上執行作業。例如，假設管理員想要根據季節/區域的改變，將新的組態推送給一組家庭太陽能裝置 (另一個真實的案例)。

這裡的關鍵思維是，更精確地使用 “Thing” 動作項目控制每個裝置，以及使用 “ThingGroup” 動作項目控制群組作業。無論您是彙總來自裝置的資料 (例如遙測)，或是將資料 (例如組態) 傳送到大量的裝置，都是如此。平台會負責散發裝置動作項目，並在動作項目之間傳遞訊息。這對開發人員完全公開透明。

對於機器對機器 (M2M) 通訊，我們在[有關分散式網路與圖形一節](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)中討論的中樞和輪輻模式，或是直接的動作項目對動作項目互動都非常適合。對於 M2M 案例，您會為一組裝置製作 “Directory/Index” 動作項目的模型，讓其能夠互相探索與傳送訊息，如下圖所示：

![一組裝置的目錄/索引動作項目的模型][2]

Service Fabric Reliable Actors 模型也負責控管動作項目的生命週期。不妨如此思考：如果您有永遠開啟的裝置，以及偶爾連線的裝置，您為何會將負責每 14 小時即連線之裝置的動作項目保留在記憶體中？ Service Fabric 可讓您在理想的時間和位置中儲存與還原裝置狀態。

愈來愈多的客戶會將 Service Fabric Reliable Actors 視為其 IoT 實作的主要建置組塊。

## 後續步驟
[模式：智慧型快取](service-fabric-reliable-actors-pattern-smart-cache.md)

[模式：分散式網路和圖形](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[模式：資源管理](service-fabric-reliable-actors-pattern-resource-governance.md)

[模式：可設定狀態的服務組合](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[模式：分散式計算](service-fabric-reliable-actors-pattern-distributed-computation.md)

[某些反向模式](service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric Actor 簡介](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png

<!---HONumber=AcomDC_0121_2016-->