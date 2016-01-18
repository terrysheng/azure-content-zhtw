<properties
   pageTitle="Service Fabric 的服務遠端處理 | Microsoft Azure"
   description="Service Fabric 遠端處理可讓用戶端和服務使用遠端程序呼叫與服務進行通訊。"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/12/2015"
   ms.author="bharatn@microsoft.com"/>

# 使用 Reliable Services 的遠端服務
對於未繫結至特定通訊協定或堆疊 (例如 WebAPI、Windows Communication Foundation (WCF) 或其他項目) 的服務，此架構會提供遠端機制，以便快速且輕鬆設定服務遠端程序呼叫。

## 設定在服務上的遠端處理
只要兩個簡單步驟，就能設定服務的遠端處理：

1. 建立服務實作的介面。這個介面會定義方法，可在您的服務上用於遠端程序呼叫。方法也必須是傳回工作的非同步方法。此介面必須實作 `Microsoft.ServiceFabric.Services.Remoting.IService`，表示服務具有遠端處理介面。
2. 在您的服務中使用 `Microsoft.ServiceFabric.Services.Remoting.Runtime.ServiceRemotingListener`。這是提供遠端功能的 `ICommunicationListener` 實作。

例如，這個 Hello World 服務會公開單一方法，透過遠端程序呼叫取得 "Hello World"：

```csharp
public interface IHelloWorldStateful : IService
{
    Task<string> GetHelloWorld();
}

internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[] { new ServiceReplicaListener(parameters => new ServiceRemotingListener<HelloWorldStateful>(parameters, this)) };
    }

    public Task<string> GetHelloWorld()
    {
        return Task.FromResult("Hello World!");
    }
}

```
> [AZURE.NOTE]服務介面中的引數和傳回類型可以是任何簡單、複雜或自訂的類型，但必須可由 .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) 序列化。


## 呼叫遠端服務方法
透過 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` 類別使用連至服務的本機 Proxy，可以在服務上使用遠端堆疊呼叫方法。`ServiceProxy` 方法會使用該服務所實作的相同介面，建立本機 Proxy。您可以使用該 Proxy 直接在介面上遠端呼叫方法。


```csharp

IHelloWorldStateful helloWorldClient = ServiceProxy.Create<IHelloWorldStateful>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.GetHelloWorld();

```

遠端架構會將在服務擲回的例外狀況傳播給用戶端。因此在用戶端使用 `ServiceProxy` 的例外狀況處理邏輯，可以直接處理服務擲回的例外狀況。

## 後續步驟

* [在 Reliable Services 中搭配 OWIN 使用 Web API](service-fabric-reliable-services-communication-webapi.md)

* [使用 Reliable Services 的 WCF 通訊](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0107_2016-->