<properties
   pageTitle="Reliable Services WCF 通訊堆疊 | Microsoft Azure"
   description="Service Fabric 內建的 WCF 通訊堆疊提供 Reliable Services 專用的用戶端服務 WCF 通訊。"
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
   ms.date="11/17/2015"
   ms.author="bharatn@microsoft.com"/>

# 適用於 Reliable Services 的 WCF 式通訊堆疊
Reliable Services 架構允許服務作者選擇其想要針對服務使用的通訊堆疊。他們可以透過從 [CreateServiceReplicaListeners 或 CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) 方法傳回的 **ICommunicationListener**，來外掛所選擇的通訊堆疊。服務作者如果想要使用 Windows Communication Foundation (WCF) 式通訊，架構可提供以 WCF 式實作的通訊堆疊。

## WCF 通訊接聽程式
**ICommunicationListener** 的 ECF 特定實作係由 **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** 類別所提供。

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    // TODO: If your service needs to handle user requests, return a list of ServiceReplicaListeners here.
    return new[] { new ServiceReplicaListener(parameters =>
        new WcfCommunicationListener(ServiceInitializationParameters,typeof(ICalculator), this)
        {
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            EndpointResourceName = "ServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            Binding = this.CreateListenBinding()
        }
    )};
}

```

## 撰寫適用於 WCF 通訊堆疊的用戶端
為了撰寫能夠使用 WCF 來與服務進行通訊的用戶端，架構提供了 **WcfClientCommunicationFactory**，也就是 [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md) 的 WCF 特定實作。

```csharp

public WcfCommunicationClientFactory(
    ServicePartitionResolver servicePartitionResolver = null,
    Binding binding = null,
    object callback = null,
    IList<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null)

```

WCF 通訊通道可以從 **WcfCommunicationClientFactory** 建立的 **WcfCommunicationClient** 來存取。

```csharp

public class WcfCommunicationClient<TChannel> : ICommunicationClient where TChannel : class
{
    public TChannel Channel { get; }
    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}

```

用戶端程式碼可以使用 **WcfCommunicationClientFactory** 連同 **ServicePartitionClient** 來決定服務端點，並與服務通訊。

```csharp

//
// Create a service resolver for resolving the endpoints of the calculator service.
//
ServicePartitionResolver serviceResolver = new ServicePartitionResolver(() => new FabricClient());

//
// Create the binding.
//
NetTcpBinding binding = CreateClientConnectionBinding();

var clientFactory = new WcfCommunicationClientFactory<ICalculator>(
    serviceResolver,// ServicePartitionResolver
    binding,        // Client binding
    null,           // Callback object
    null);          // do not retry Exception types


//
// Create a client for communicating with the calc service that has been created with the
// Singleton partition scheme.
//
var calculatorServicePartitionClient = new ServicePartitionClient<WcfCommunicationClient<ICalculator>>(
    clientFactory,
    ServiceName);

//
// Call the service to perform the operation.
//
var result = calculatorServicePartitionClient.InvokeWithRetryAsync(
    client => client.Channel.AddAsync(2, 3)).Result;

```

## 後續步驟
* [使用 Reliable Services 遠端服務進行遠端程序呼叫](service-fabric-reliable-services-communication-remoting.md)

* [在 Reliable Services 中搭配 OWIN 使用 Web API](service-fabric-reliable-services-communication-webapi.md)

<!---HONumber=AcomDC_0309_2016-->