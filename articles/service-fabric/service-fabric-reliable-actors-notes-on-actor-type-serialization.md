<properties
   pageTitle="Reliable Actors 的動作項目類型序列化註解 | Microsoft Azure"
   description="說明定義可用於定義 Service Fabric Reliable Actors 狀態與介面其可序列化類別的基本需求"
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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Service Fabric Reliable Actors 類型序列化的注意事項

當您定義動作項目的介面和狀態時，您應牢記一些重要的部分。類型必須可資料合約序列化。如需資料合約的詳細資訊，請參閱 [MSDN](https://msdn.microsoft.com/library/ms731923.aspx)。

## 動作項目介面類型

所有方法的引數，以及[動作項目介面](service-fabric-reliable-actors-introduction.md#actors)中定義的每個方法所傳回之工作的結果類型，必須可資料合約序列化。這也適用於在[動作項目事件介面](service-fabric-reliable-actors-events.md#actor-events)中定義的方法引數。(動作項目事件介面方法一律會傳回無效)。 例如，如果 `IVoiceMail` 介面將方法定義為：

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

`List<T>` 是已資料合約序列化的標準 .NET 類型。`Voicemail` 類型也必須可資料合約序列化。

```csharp

[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}

```

## 動作項目狀態類別

動作項目的狀態必須可資料合約序列化。例如，動作項目類別定義可以如下所示：

```csharp

public class VoiceMailActor : StatefulActor<VoicemailBox>, IVoiceMail
{
...

```

定義狀態類別，並分別以 **DataContract** 和 **DataMember** 屬性註解類別及其成員。

```csharp

[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}

```

<!---HONumber=AcomDC_0121_2016-->