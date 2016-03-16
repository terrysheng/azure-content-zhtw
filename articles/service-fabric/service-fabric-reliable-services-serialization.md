<properties
   pageTitle="Reliable Service 序列化 | Microsoft Azure"
   description="Service Fabric Reliable Service 序列化的概念文件"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson,tyadam"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/18/2015"
   ms.author="mcoskun"/>

# Reliable Services 的序列化
Reliable 狀態管理員可容納多個 Reliable 物件。這些 Reliable 物件中，有些可以是泛型資料結構，例如立即可用的 Reliable 字典和 Reliable 佇列。由於這些都是可靠的泛型資料結構，它們保存的泛型物件必須序列化，以便可以複寫及保存到磁碟。

Reliable 狀態管理員支援三種序列化程式：* 自訂序列化程式；* 有限類型數目的內建序列化程式；* DataContractSerilizer

當 Reliable 物件需要序列化物件時，它會向 Reliable 狀態管理員查詢是否有適合指定類型的序列化程式。Reliable 狀態管理員會先檢查輸入類型是否已有註冊的自訂序列化程式。如果沒有，它會檢查內建序列化程式的其中之一是否可以序列化類型。Reliable 狀態管理員有適合下列類型的內建序列化程式：guid、bool、byte、sbyte、char、decimal、double、float、int、uint、long、ulong、short、ushort 和 string。如果沒有，它會傳回 DataContractSerializer。

本文著重於使用自訂序列化的時機和方式。

## 使用自訂序列化的時機
使用自訂序列化的常見原因有二：* 效能 * 加密

至於內建類型不包含的類型，使用自訂內建的序列化程式，不用 DataContractSerializer，可有相當大的效能改進。其中一個原因是，自訂序列化程式不需要序列化類型資訊。Service Fabric 保證特定類型的狀態序列化程式，僅用於該類型的序列化和還原序列化。

序列化程式產生的序列化資料會依原樣複寫及保存在磁碟上。至於機密資料，最好能確定網路和磁碟上的資料位元都加密。

## 使用自訂序列化的方式
若要針對指定類型使用自訂序列化程式，我們需要 * 建置自訂狀態序列化程式 * 在 Reliable Service 中註冊自訂狀態序列化程式

### 如何實作自訂序列化程式
自訂序列化程式需要實作 IStateSerializer <T> 介面。此介面的兩個核心方法為：* T Read(BinaryReader binaryReader); * void Write(T value, BinaryWriter binaryWriter);

第一個為 ReliableObject 所用，使用 BinaryReader 從串流讀取序列化的物件。第二個用於反向作業：使用 Binary Writer 將物件寫入串流。

以下是自訂類別範例及適用的序列化程式。

```C#
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }
    public short District { get; set; }
    public int Customer { get; set; }
    public long Order { get; set; }
```

```C#
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
    void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
    {
        writer.Write(value.Warehouse);
        writer.Write(value.District);
        writer.Write(value.Customer);
        writer.Write(value.Order);
    }

    OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
    {
        OrderKey value = new OrderKey();
        value.Warehouse = reader.ReadByte();
        value.District = reader.ReadInt16();
        value.Customer = reader.ReadInt32();
        value.Order = reader.ReadInt64();

        return value;
    }

    void IStateSerializer<OrderKey>.Write(OrderKey currentValue, OrderKey newValue, BinaryWriter writer)
    {
        ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
    }

    OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
    {
        return ((IStateSerializer<OrderKey>)this).Read(reader);
    }
}
```
>[AZURE.NOTE]在上例中，讀取和寫入的多載實作只會呼叫其對應的多載。這是因為下列兩種方法會用於尚未提供的功能。

### 如何註冊自訂序列化程式
若要註冊自訂序列化程式，我們首先需要可以註冊所有自訂序列化程式的方法。這個方法不需要任何引數，並且能夠傳回工作。在這個方法中，必須使用 IReliableStateManager.TryAddStateSerializer<T> 註冊 Reliable Service 的所有自訂序列化程式。

```C#
protected Task InitializeStateSerializers()
{
    this.StateManager.TryAddStateSerializer(new OrderKeySerializer());
    return Task.FromResult(false);
}
```

下一步是將上述方法註冊為委派，供 Reliable 狀態管理員在所有自訂狀態序列化程式應該註冊的時間呼叫。因為序列化程式可能需要從磁碟讀取序列化的資料，所以只會在 Reliable Service 複寫之初、本機復原啟動之前呼叫這個方法。註冊序列化程式之後，所有 Reliable 物件中的相關類型都會使用這個序列化程式序列化和還原序列化其物件。

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            onInitializeStateSerializersEvent : this.InitializeStateSerializers));
}
```
### 版本控制
Service Fabric 期望序列化程式能與新版和舊版完全相容。至於使用內建序列化程式的類型，Service Fabric 確保其與新版和舊版相容。至於使用 DataContractSerializer 或自訂序列化程式的類型，使用者一律不可執行中斷變更。如需 DataContract 的版本設定，請參閱[資料合約版本設定](https://msdn.microsoft.com/library/ms731138.aspx)。如果需要中斷變更，狀態就必須從具有舊資料版本的服務執行個體，移至位於應用程式層級有新資料版本的服務執行個體。

### 使用序列化程式的時機
 * 在 Reliable 物件上撰寫作業會導致物件序列化、複寫並儲存在記錄檔。
 * 記錄足夠的作業之後，記憶體的最新資料就會被序列化以及在磁碟被檢查點檢查。
 * 為重新建立複本，在磁碟上執行過檢查點檢查的檔案以及記錄檔中的新資料，會直接從主要複本逐一傳送每個位元組 (也就是資料不會重新序列化)。這些位元組在次要複本上會還原序列化到 C# 物件。
 * 在復原期間，會還原序列化檢查點檔案和新的資料記錄。
 * 在備份期間，會逐一複製檢查點檔案和新記錄資料的每個位元組。
 * 在復原期間，之前備份的檢查點檔案和記錄檔資料會複製回原位並還原序列化。

## 後續步驟
 * [可靠的服務程式設計模型進階用法](service-fabric-reliable-services-advanced-usage.md)

<!---HONumber=AcomDC_1203_2015-->