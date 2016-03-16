<properties
   pageTitle="使用 ASP.NET Web API 的服務通訊 |Microsoft Azure"
   description="了解如何藉由使用 ASP.NET Web API 與 OWIN 自我裝載，在 Reliable Services API 中逐步實作服務通訊。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# 開始使用：Service Fabric Web API 服務與 OWIN 自我裝載 | Microsoft Azure

Azure Service Fabric 讓您能決定您的服務與使用者以及服務彼此之間如何進行通訊。本教學課程著重於使用 ASP.NET Web API 與 Open Web Interface for .NET (OWIN) 自我裝載在 Service Fabric 的 Reliable Services API 中實作服務通訊。我們將深入探討 Reliable Services 隨插即用的通訊 API。我們也將在逐步範例中使用 Web API，示範如何設定自訂通訊接聽程式。


## Service Fabric 中的 Web API 簡介

ASP.NET Web API 是在 .NET Framework 建置 HTTP API 的常用且功能強大的架構。如果您不熟悉此架構，請參閱[開始使用 ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) 以深入了解。

Service Fabric 中的 Web API 是您熟知而且喜愛的相同 ASP.NET Web API。不同之處在於您如何裝載 Web API 應用程式。您不會使用 Microsoft Internet Information Services。若要進一步了解差異，讓我們將它分成兩個部分：

 1. Web API 應用程式 (包括控制器和模型)
 2. 主機 (Web 伺服器，通常是 IIS)

Web API 應用程式本身不會變更。它與您過去撰寫的 Web API 應用程式並無不同，您應該能夠直接搬移大部分的應用程式程式碼。但是如果您裝載在 IIS 上，裝載應用程式的位置可能會與您過去習慣的稍有不同。在我們進入裝載部分之前，讓我們從較熟悉的部分開始：Web API 應用程式。


## 建立應用程式

從在 Visual Studio 2015 中，使用單一無狀態服務建立新的 Service Fabric 應用程式開始：

![建立新的 Service Fabric 應用程式](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

![建立單一無狀態服務](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

這讓我們有空的無狀態服務，它會裝載 Web API 應用程式。我們將從頭設定應用程式，看看它如何整合。

第一個步驟是提取一些 Web API 的 NuGet 封裝。我們想要使用的封裝是 Microsoft.AspNet.WebApi.OwinSelfHost。此封裝包含所有必要的 Web API 封裝和主機封裝。這在稍後會很重要。

![藉由使用 NuGet 封裝管理員來建立 Web API](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

安裝封裝後，您就可以馬上開始建置出基本的 Web API 專案結構。如果您已使用 Web API，專案結構看起來應該很熟悉。從建立基本的 Web API 目錄開始：

 + App\_Start
 + Controllers
 + Models

在 App\_Start 目錄中加入基本的 Web API 組態類別。現在，我們要新增空的媒體類型格式組態：

**FormatterConfig.cs**

```csharp

namespace WebApiService
{
    using System.Net.Http.Formatting;

    public static class FormatterConfig
    {
        public static void ConfigureFormatters(MediaTypeFormatterCollection formatters)
        {
        }
    }
}

```

在 Controllers 目錄中加入預設控制器：

**DefaultController.cs**

```csharp

namespace WebApiService.Controllers
{
    using System.Collections.Generic;
    using System.Web.Http;

    [RoutePrefix("api")]
    public class DefaultController : ApiController
    {
        // GET api/values
        [Route("values")]
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5
        [Route("values/{id}")]
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values
        [Route("values")]
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5
        [Route("values/{id}")]
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5
        [Route("values/{id}")]
        public void Delete(int id)
        {
        }
    }
}

```

最後，在專案根目錄加入 Startup 類別以註冊路由、格式器及任何其他組態設定。這也是 Web API 插入至主機的位置，稍後我們將再重返此處。在您設定 Startup 類別時，為 Startup 類別建立稱為 IOwinAppBuilder 的介面，定義 configuration 方法。雖然技術上不需要這麼做即可讓 Web API 運作，它能讓稍後使用 Startup 類別時更有彈性。

**Startup.cs**

```csharp

namespace WebApiService
{
    using Owin;
    using System.Web.Http;

    public class Startup : IOwinAppBuilder
    {
        public void Configuration(IAppBuilder appBuilder)
        {
            HttpConfiguration config = new HttpConfiguration();

            config.MapHttpAttributeRoutes();
            FormatterConfig.ConfigureFormatters(config.Formatters);

            appBuilder.UseWebApi(config);
        }
    }
}

```

**IOwinAppBuilder.cs**

```csharp

namespace WebApiService
{
    using Owin;

    public interface IOwinAppBuilder
    {
        void Configuration(IAppBuilder appBuilder);
    }
}

```

應用程式部分就這樣。現在我們只設定基本的 Web API 專案配置。到目前為止，看起來應該與您過去可能已撰寫的 Web API 專案或基本的 Web API 範本有太多不同。您的商務邏輯如往常般在控制器和模型中運作。

現在我們怎麼辦才能實際執行裝載？


## 服務裝載

在 Service Fabric 中，您的服務會在服務主機處理序中執行，這是執行您的服務程式碼的可執行檔。當您使用 Reliable Services API 撰寫服務時，您的服務專案只會編譯註冊您的服務類型並執行程式碼的可執行檔。當您在 .NET 中的 Service Fabric 上撰寫服務時，在大部分情況下都是如此。當您在無狀態服務專案中開啟 Program.cs，您應該會看到：

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterServiceType("WebApiServiceType", typeof(Service));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e);
            throw;
        }
    }
}

```

如果看起來疑似主控台應用程式的進入點，這是因為它是。

關於服務裝載處理序和服務註冊的進一步詳細資料已超出本文的範圍。但是現在請務必了解您的服務程式碼已在自己的處理序中執行。

## 自我裝載 Web API 與 OWIN 主機

假設您的 Web API 應用程式程式碼裝載在自己的處理序中，您如何將它連結到 Web 伺服器？ 輸入 [OWIN](http://owin.org/)。OWIN 只是 .NET Web 應用程式和 Web 伺服器之間的合約。傳統上使用 ASP.NET (直到 MVC 5) 時，Web 應用程式已透過 System.Web 緊密結合至 IIS。不過，Web API 會實作 OWIN，所以您可以撰寫獨立於裝載它的 Web 伺服器的 Web 應用程式。因此，您可以使用可在自己的程序中啟動的自我裝載 OWIN web 伺服器。這樣完全符合我們先前所提到的 Service Fabric 裝載模型。

在本文中，我們將使用 Katana 做為 Web API 應用程式的 OWIN 主機。Katana 是開放原始碼的 OWIN 主機實作。

> [AZURE.NOTE]若要深入了解 Katana，請移至 [Katana 網站](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana)。如需如何使用 Katana 自我裝載 Web API 的快速概觀，請參閱[使用 OWIN 自我裝載 ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api)。


## 設定 Web 伺服器

Reliable Services API 提供通訊進入點，您可在其中插入通訊堆疊，讓使用者和用戶端連線到服務：

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

Web 伺服器 (和您未來使用的任何其他通訊堆疊，例如 WebSockets) 應該使用 ICommunicationListener 介面來正確地與系統整合。這樣做的原因會在接下來的步驟中變得明顯。

首先，建立一個稱為 OwinCommunicationListener 的類別，其實作 ICommunicationListener：

**OwinCommunicationListener.cs**：

```csharp

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

    public class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}

```

ICommunicationListener 介面提供三個方法來管理服務的通訊接聽程式：

 - OpenAsync。開始接聽要求。
 - CloseAsync。停止接聽要求，完成任何進行中的要求，並正常關機。
 - Abort。取消所有項目，並立即停止。

若要開始，請加入 URL 路徑前置詞的私用類別成員和稍早建立的 Startup 類別。這些會透過建構函式初始化，並在您稍後設定接聽 URL 時使用。另外也加入私用類別成員，以儲存在初始化期間建立的接聽位址，並儲存啟動伺服器時建立的伺服器控制代碼。

```csharp

public class OwinCommunicationListener : ICommunicationListener
{
    private readonly IOwinAppBuilder startup;
    private readonly string appRoot;
    private IDisposable serverHandle;
    private string listeningAddress;
    private readonly ServiceInitializationParameters serviceInitializationParameters;

    public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
    {
        this.startup = startup;
        this.appRoot = appRoot;
        this.serviceInitializationParameters = serviceInitializationParameters;
    }        

    ...

```

## 實作 OpenAsync

若要設定 Web 伺服器，您需要兩項資訊：

 - URL 路徑前置詞。雖然這是選擇性的，但您最好現在就設定，以便您能安全地在應用程式中裝載多個 Web 服務。
 - 連接埠。

在您抓取 Web 伺服器的連接埠之前，必須了解 Service Fabric 提供一個應用程式層，做為您的應用程式與其執行所在之基礎作業系統之間的緩衝區。因此，Service Fabric 讓您能夠設定服務的端點。Service Fabric 可確保端點可供您的服務使用。如此一來，您就不用在基礎作業系統環境中自行設定它們。您可以輕易在不同環境中裝載 Service Fabric 應用程式，而不必對您的應用程式進行任何變更。(例如，您可以在 Azure 或您自己的資料中心內裝載相同的應用程式。)

在 PackageRoot\\ServiceManifest.xml 中設定 HTTP 端點：

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="80" />
    </Endpoints>
</Resources>

```

這個步驟很重要，因為服務裝載處理序要在受限制的認證 (在 Windows 上的網路服務) 之下執行。這表示您的服務並沒有自行設定 HTTP 端點的存取權。藉由使用端點組態，Service Fabric 會知道要為服務將接聽的 URL 設定適當的存取控制清單 (ACL)。Service Fabric 也會提供標準位置來設定端點。


返回 OwinCommunicationListener.cs 中，您可以開始實作 OpenAsync。您會從此處啟動 Web 伺服器。首先，取得端點資訊，並建立服務將接聽的 URL。

```csharp

public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = String.Format(
        CultureInfo.InvariantCulture,
        "http://+:{0}/{1}",
        port,
        String.IsNullOrWhiteSpace(this.appRoot)
            ? String.Empty
            : this.appRoot.TrimEnd('/') + '/');
    ...

```

請注意這裡用 "http://+"。這是為了確定 Web 伺服器正在接聽所有可用的位址，包括 localhost、FQDN，以及電腦的 IP。

OpenAsync 實作是 Web 伺服器 (或任何通訊堆疊) 之所以實作為 ICommunicationListener，而非直接從服務中的 `RunAsync()` 開啟它，最重要的原因之一。OpenAsync 的傳回值是 Web 伺服器正在接聽的位址。當傳回這個位址給系統時，它會向服務註冊位址。Service Fabric 會提供一個 API，讓用戶端和其他服務依服務名稱來要求這個位址。這一點很重要，因為服務位址不是靜態的。服務會為了資源平衡和可用性目的在叢集中移動。這是可讓用戶端解析服務接聽位址的機制。

記住這一點之後，OpenAsync 會啟動 Web 伺服器，並傳回它接聽的位址。請注意它會接聽 "http://+"，但 OpenAsync 傳回位址之前，"+" 會取代為目前所在節點的 IP 或 FQDN。這個方法所傳回的位址就是向系統註冊的位址。它也是用戶端和其他服務要求服務位址時所看到的位址。用戶端若要能正確地連接到它，它們需要位址中有實際的 IP 或 FQDN。

```csharp

    ...

    this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
    string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

    return Task.FromResult(publishAddress);
}

```

請注意，這會參考傳遞給建構函式中之 OwinCommunicationListener 的 Startup 類別。這個 startup 執行個體由 Web 伺服器用來啟動 Web API 應用程式。

稍後當您執行應用程式時，`ServiceEventSource.Current.Message()` 列會出現在 [診斷事件] 視窗，確認已成功啟動 Web 伺服器。

## 實作 CloseAsync 和 Abort

最後，實作 CloseAsync 和 Abort 可停止 Web 伺服器。處置 OpenAsync 時所建立的伺服器控制代碼，可以停止 Web 伺服器。

```csharp

public Task CloseAsync(CancellationToken cancellationToken)
{
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.serverHandle != null)
    {
        try
        {
            this.serverHandle.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}

```

在此實作範例中，CloseAsync 和 Abort 只會停止 Web 伺服器。您也可以選擇在 CloseAsync 中執行更妥善協調的 web 伺服器關閉。例如，關閉可以等候進行中的要求，在傳回之前完成。

## 啟動 Web 伺服器

您現在可以開始建立並傳回 OwinCommunicationListener 的執行個體以啟動 Web 伺服器。回到 Service 類別 (Service.cs)，覆寫 `CreateServiceInstanceListeners()` 方法：

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new[]
    {
        new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
    };
}

```

這正是 Web API 應用程式 和 OWIN 主機 最後相會之處。提供應用程式的執行個體 (透過 startup 的 Web API) 給主機 (OwinCommunicationListener)。然後 Service Fabric 會管理其生命週期。通常可以針對任何通訊堆疊運用相同的模式。

## 組合在一起

在此範例中，您不需要在 `RunAsync()` 方法中執行任何動作，因此可以移除覆寫。

最終的服務實作應該非常簡單。它只需要建立通訊接聽程式：

```csharp

namespace WebApiService
{
    using System.Collections.Generic;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;
    using Microsoft.ServiceFabric.Services.Runtime;

    public class WebApiService : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            return new[]
            {
                new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
            };
        }
    }
}

```

完整 `OwinCommunicationListener` 類別：

```csharp

namespace WebApiService
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

    public class OwinCommunicationListener : ICommunicationListener
    {
        private readonly IOwinAppBuilder startup;
        private readonly string appRoot;
        private readonly ServiceInitializationParameters serviceInitializationParameters;
        private IDisposable serverHandle;
        private string listeningAddress;

        public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
        {
            this.startup = startup;
            this.appRoot = appRoot;
            this.serviceInitializationParameters = serviceInitializationParameters;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
            int port = serviceEndpoint.Port;

            this.listeningAddress = String.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/{1}",
                port,
                String.IsNullOrWhiteSpace(this.appRoot)
                    ? String.Empty
                    : this.appRoot.TrimEnd('/') + '/');

            this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
            string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

            return Task.FromResult(publishAddress);
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            ServiceEventSource.Current.Message("Close");

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            ServiceEventSource.Current.Message("Abort");

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.serverHandle != null)
            {
                try
                {
                    this.serverHandle.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}

```

所有細節就緒之後，您的專案看起來應該像一般 Web API 應用程式，並且具有 Reliable Services API 進入點與 OWIN 主機：


![包含 Reliable Services API 輸入點和 OWIN 主機的 Web API](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## 透過 Web 瀏覽器執行並連線

如果您尚未這麼做，請[設定開發環境](service-fabric-get-started.md)。


您現在可以建置並部署您的服務。在 Visual Studio 中按 **F5** 以建置及部署應用程式。在 [診斷事件] 視窗中，您應該會看到一則訊息指出 Web 伺服器在 http://localhost:80/webapp/api 中開啟。


![Visual Studio 診斷事件視窗](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE]如果電腦上的另一個處理序已經開啟連接埠，您可能會在此看到錯誤訊息。這就表示無法開啟接聽程式。如果是這種情況，請嘗試在 ServiceManifest.xml 中的端點組態使用不同的通訊埠。


一旦服務正常執行，請開啟瀏覽器並瀏覽至 [http://localhost/webapp/api/values](http://localhost/webapp/api/values) 進行測試。

## 相應放大

相應放大無狀態的 Web 應用程式通常表示加入更多電腦，並加快其上的 Web 應用程式。每當新的節點加入至叢集時，Service Fabric 的協調流程引擎便可以為您完成。當您建立無狀態服務的執行個體時，您可以指定要建立的執行個體數目。Service Fabric 會在叢集中放置執行個體的數目在節點上。它可確保所有節點上都只會建立一個執行個體。您也可以指定 **-1** 的執行個體計數，指示 Service Fabric，一定要在每個節點上建立執行個體。這樣可保證每當您加入節點相應放大您的叢集，便會在新節點上建立無狀態服務的執行個體。這個值是服務執行個體的屬性，因此它會在您建立服務執行個體時設定：您可以透過 PowerShell 設定：

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

您也可以在 Visual Studio 無狀態服務專案中定義預設服務時設定：

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

如需如何建立應用程式和服務執行個體的詳細資訊，請參閱[部署應用程式](service-fabric-deploy-remove-applications.md)。

## ASP.NET 5

在 ASP.NET 5 中，分隔應用程式與 Web 應用程式中的主機的概念和程式設計模型維持不變。它也可以套用至其他形式的通訊。此外，雖然主機在 ASP.NET 5 中可能不同，但 Web API 應用程式層維持不變。這是大量應用程式邏輯實際存在的位置。

## 後續步驟

[使用 Visual Studio 偵錯 Service Fabric 應用程式](service-fabric-debugging-your-application.md)

<!---HONumber=AcomDC_0121_2016-->