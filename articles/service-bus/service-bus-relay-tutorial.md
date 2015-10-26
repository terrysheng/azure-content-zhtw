<properties 
   pageTitle="服務匯流排轉送傳訊教學課程 | Microsoft Azure"
   description="使用服務匯流排轉送傳訊，建置服務匯流排用戶端應用程式和服務。"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/11/2015"
   ms.author="sethm" />

# 服務匯流排轉送傳訊教學課程

本教學課程說明如何使用服務匯流排「轉送」功能，建置簡單的服務匯流排用戶端應用程式和服務。如需說明如何建置使用服務匯流排「代理」或非同步傳訊功能之應用程式的對應教學課程，請參閱[服務匯流排代理傳訊 .NET 教學課程](https://msdn.microsoft.com/library/hh367512.aspx)。如需使用服務匯流排[代理傳訊](service-bus-messaging-overview.md/#Brokered-messaging)的類似教學課程，請參閱[服務匯流排代理傳訊 .NET 教學課程](https://msdn.microsoft.com/library/hh367512.aspx)。

循序完成本教學課程可讓您了解建立服務匯流排用戶端和服務應用程式所需的步驟。如同其對應的 WCF 項目，服務是可公開一或多個端點的結構，而每個端點都會公開一或多個服務作業。服務的端點指定可找到服務的位址、內含用戶端與服務通訊所需資訊的繫結，以及可定義服務提供給其用戶端之功能的合約。WCF 與服務匯流排服務的主要差異在於端點是在雲端公開，而不是在您的電腦本機上公開。

在您逐步完成本教學課程中的各個主題後，您會有一項執行中的服務，以及可叫用服務作業的用戶端。第一個主題說明如何設定帳戶。後續步驟說明如何定義使用合約的服務、如何實作服務，以及如何在程式碼中設定服務。此外，也會說明如何主控和執行服務。建立的服務會自我裝載，而用戶端和服務會在相同的電腦上執行。您可以使用程式碼或組態檔來設定服務。如需詳細資訊，請參閱[設定 WCF 服務以向服務匯流排登錄](https://msdn.microsoft.com/library/ee173579.aspx)和[建置服務匯流排的服務](https://msdn.microsoft.com/library/ee173564.aspx)。

最後三個步驟描述如何建立用戶端應用程式、設定用戶端應用程式，以及建立和使用可存取主機功能的用戶端。如需詳細資訊，請參閱[建置服務匯流排用戶端應用程式](https://msdn.microsoft.com/library/ee173543.aspx)和[探索和公開服務匯流排服務](https://msdn.microsoft.com/library/dd582704.aspx)。

這一節中的所有主題都假設您使用 Visual Studio 作為開發環境。

## 註冊帳戶

第一步是建立服務匯流排的服務命名空間，並取得共用存取簽章 (SAS) 金鑰。服務命名空間會為每個透過服務匯流排公開的應用程式提供應用程式界限。服務命名空間與 SAS 金鑰的結合提供了一個認證，供服務匯流排驗證對應用程式的存取權。

若要建立命名空間，請依照[作法：建立或修改服務匯流排服務命名空間](https://msdn.microsoft.com/library/hh690931.aspx)中簡述的步驟。

>[AZURE.NOTE]您不需要對用戶端和服務應用程式使用相同的命名空間。

1. 在 Azure 入口網站的主視窗中，按一下您在上一個步驟中建立的服務命名空間的名稱。

2. 按一下 [設定]，檢視您的服務命名空間的共用存取原則。

3. 請記下 **RootManageSharedAccessKey** 原則的主索引鍵，或是將它複製到剪貼簿。您稍後會在本教學課程中使用此值。

## 定義要搭配服務匯流排使用的 WCF 服務合約

服務合約會指定服務可支援哪些作業 (方法或函式的 Web 服務術語)。合約可以透過定義 C++、C# 或 Visual Basic 介面建立。介面中的每個方法會對應一個特定服務作業。每個介面都必須已套用 [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) 屬性，而且每個作業都必須已套用 [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) 屬性。如果介面中的方法有[ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) 屬性，但沒有 [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) 屬性，則不會公開該方法。程序後面的範例會提供這些工作的程式碼。如需有關如何定義合約的詳細資訊，請參閱[設計服務匯流排的 WCF 合約](https://msdn.microsoft.com/library/ee173585.aspx)。如需合約與服務的詳細討論，請參閱 WCF 文件中的[設計和實作服務](https://msdn.microsoft.com/library/ms729746.aspx)。

### 使用介面建立服務匯流排合約

1. 以系統管理員身分開啟 Visual Studio：以滑鼠右鍵按一下 [開始] 功能表中的程式，然後選取 [以系統管理員身分執行]。

1. 這會建立新的主控台應用程式專案。按一下 [檔案] 功能表，選取 [新增]，然後按一下 [專案]。在 [新增專案] 對話方塊中，按一下 [Visual C#] (如果 [Visual C#] 未出現，請查看 [其他語言] 下方)。按一下 [主控台應用程式] 範本，並將它命名為 **EchoService**。使用預設 [位置]。按一下 [確定] 以建立專案。

1. 將 `System.ServiceModel.dll` 的參考新增至專案︰在 [方案總管] 中，以滑鼠右鍵按一下專案資料夾下的**參考**資料夾，然後按一下 [加入參考]。選取 [加入參考] 對話方塊中的 [.NET] 索引標籤，並向下捲動直到您看到 **System.ServiceModel**。請加以選取，然後按一下 [確定]。

1. 在 [方案總管] 中，按兩下 Program.cs 檔案，以在編輯器中開啟它。

1. 新增 System.ServiceModel 命名空間的 `using` 陳述式。

	```
	using System.ServiceModel;
	```

	[System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) 是可讓您以程式設計方式存取 WCF 基本功能的命名空間。服務匯流排會使用 WCF 的許多物件和屬性來定義服務合約。

1. 將命名空間名稱從 **EchoService** 的預設名稱變更為 **Microsoft.ServiceBus.Samples**。

	>[AZURE.IMPORTANT]本教學課程使用 C# 命名空間 **Microsoft.ServiceBus.Samples**，也就是在「步驟 6︰設定 WCF 用戶端」的組態檔中使用之合約 Managed 型別的命名空間。您可以在建置此範例時指定您要的任何命名空間；不過，除非您後來在應用程式組態檔中相應地修改合約的命名空和服務，否則本教學課程將無法運作。在 App.config 檔案中指定的命名空間必須與在 C# 檔案中指定的命名空間相同。

1. 緊接著 `Microsoft.ServiceBus.Samples` 命名空間宣告後面 (但在命名空間內)，定義名為 `IEchoContract` 的新介面，並將 `ServiceContractAttribute` 屬性套用至命名空間值為 ****http://samples.microsoft.com/ServiceModel/Relay/** 的介面。命名空間值與您的整個程式碼範圍中使用的命名空間不同。然而，命名空間值會作為此合約的唯一識別碼。明確指定命名空間可避免將預設命名空間值新增至合約名稱。

	```
	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	publicinterface IEchoContract
	{
	}
	```

	>[AZURE.NOTE]一般而言，服務合約命名空間包含命名配置 (其中包含版本資訊)。在服務合約命名空間中包含版本資訊，可讓服務定義具有新命名空間的新服務合約並在新的端點上公開它，藉此區隔重大變更。此這種方式，用戶端可以繼續使用舊服務合約，而不需要更新。版本資訊可以包含日期或組建編號。如需詳細資訊，請參閱服務版本控制[](http://go.microsoft.com/fwlink/?LinkID=180498)。基於本教學課程的目的，服務合約命名空間的命名配置不包含版本資訊。

1. 在 IEchoContract 介面中，針對 `IEchoContract` 合約在介面中公開的單一作業宣告方法，並將 `OperationContractAttribute` 屬性套用至您想要公開為公用服務匯流排合約一部分的方法。

	```
	[OperationContract]
	string Echo(string text);
	```

1. 在合約外部，宣告同時繼承自 `IEchoChannel` 與 `IClientChannel` 介面的通道，如下所示：

	```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    publicinterface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    publicinterface IEchoChannel : IEchoContract, IClientChannel { }
	```

	通道是 WCF 物件，主機和用戶端可透過它彼此傳遞資訊。稍後，您將對此通道撰寫程式碼，以回應兩個應用程式之間的資訊。

1. 從 [建置] 功能表中，按一下 [建置方案] 或按 F6，確認您的工作正確無誤。

### 範例

下列程式碼示範定義了服務匯流排合約的基本介面。

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

現在已建立介面，您可以實作此介面。

## 實作 WCF 合約來使用服務匯流排

建立服務匯流排服務需要先建立合約，您可使用介面定義該合約。如需建立介面的詳細資訊，請參閱上一個步驟。下一步是實作介面。這牽涉到建立名為 `EchoService` 的類別，該類別會實作使用者定義的 `IEchoContract` 介面。實作合約後，接著可使用 App.config 組態檔設定介面。組態檔包含應用程式的必要資訊，例如服務名稱、合約名稱，以及用來與服務匯流排通訊的通訊協定類型。程序後面的範例提供用來執行這些工作的程式碼。如需如何實作服務合約的一般討論，請參閱 Windows Communication Foundation (WCF) 文件中的[實作服務合約](https://msdn.microsoft.com/library/ms733764.aspx)。

1. 緊接在 `IEchoContract` 介面的定義之後，建立名為 `EchoService` 的新類別。`EchoService` 類別會實作 `IEchoContract` 介面。 

	```
	class EchoService : IEchoContract
	{
	}
	```
	
	類似於其他介面實作，您可以在不同的檔案中實作定義。不過，在此教學課程中，實作會位於與介面定義和 `Main` 方法相同的檔案中。

1. 套用可表示服務名稱和命名空間的 [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) 屬性。

	```[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	class EchoService : IEchoContract
	{
	}
	```

1. 實作在 `EchoService` 類別的 `IEchoContract` 介面中定義的 `Echo` 方法。

	```
	public string Echo(string text)
	{
    	Console.WriteLine("Echoing: {0}", text);
    	return text;
	}
	```

1. 按一下 [建置]，然後按一下 [建置方案]，確認您的工作正確無誤。

### 定義服務匯流排的組態

1. 此組態檔非常類似於 WCF 組態檔。其中包含服務名稱、端點 (也就是公開的位置服務匯流排，讓用戶端與主機彼此通訊) 和繫結 (用於通訊的通訊協定類型)。主要差異在於這個已設定的服務端點會參考不屬於 .NET Framework 的 [netTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx)。[NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) 是服務匯流排所定義的其中一個繫結。

1. 在 [方案總管] 中，按一下目前包含下列 XML 元素的 App.config 檔。

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	</configuration>
	```

1. 將 `<system.serviceModel>` XML 元素新增至 App.config 檔案。這是定義一或多個服務的 WCF 元素。此範例使用它來定義服務名稱和端點。

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
  
	  </system.serviceModel>
	</configuration>
	```

1. 在 `<system.serviceModel>` 標記內，加入 `<services>` 元素。您可以在單一組態檔中定義多個服務匯流排應用程式。不過，本教學課程只會定義一個。
 
	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	    <services>

	    </services>
	  </system.serviceModel>
	</configuration>
	```

1. 在 `<services>` 元素內，加入 `<service>` 元素以定義服務的名稱。

	```
	<servicename="Microsoft.ServiceBus.Samples.EchoService">
	</service>
	```

1. 在 `<service>` 元素內，定義端點合約的位置，以及端點的繫結類型。

	```
	<endpointcontract="Microsoft.ServiceBus.Samples.IEchoContract"binding="netTcpRelayBinding"/>
	```

	端點會定義用戶端將在何處尋找主應用程式。稍後，本教學課程會使用此步驟來建立 URI，以透過服務匯流排完全公開主機。繫結會宣告我們使用 TCP 作為與服務匯流排通訊的通訊協定。


1. 緊接在 `<services>` 元素之後，加入下列繫結延伸模組︰
 
	```
	<extensions>
	  <bindingExtensions>
	    <addname="netTcpRelayBinding"type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
	  </bindingExtensions>
	</extensions>
	```

1. 從 [建置] 功能表中，按一下 [建置方案]，確認您的工作正確無誤。

### 範例

下列程式碼示範服務合約的實作。

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

下列程式碼顯示與服務相關聯之 App.config 檔案的基本格式。

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## 裝載和執行基本 Web 服務以向服務匯流排登錄

此步驟描述如何執行基本服務匯流排服務。

### 建立服務匯流排認證

1. 將 Microsoft.ServiceBus.dll 的參考加入至專案：請參閱[使用 NuGet 服務匯流排封裝](https://msdn.microsoft.com/library/dn741354.aspx)。

	>[AZURE.NOTE]使用命令列編譯器時，您也必須提供組件路徑。

1. 在 Program.cs 中，為 Microsoft.ServiceBus 命名空間加入 `using` 陳述式。

	```
	using Microsoft.ServiceBus;
	```

1. 在 `Main()` 中，建立兩個變數，以儲存命名空間以及從主控台視窗讀取的 SAS 金鑰。

	```
	Console.Write("Your Service Namespace: ");
	string serviceNamespace = Console.ReadLine();
	Console.Write("Your SAS key: ");
	string sasKey = Console.ReadLine();
	```

	SAS 金鑰稍後將用來存取服務匯流排專案。服務命名空間會當作參數傳遞至 `CreateServiceUri` 以建立服務 URI。

4. 使用 [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx) 物件，宣告您將使用 SAS 金鑰作為認證類型。將下列程式碼直接加在最後一個步驟中新增的程式碼之後。

	```
	TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
	sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
	```

### 建立服務的基底位址

1. 在最後一個步驟中加入的程式碼後面，建立服務基底位址的 `Uri` 執行個體。此 URI 會指定服務匯流排配置、命名空間和服務介面的路徑。

	```
	Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
	```

	"sb" 是服務匯流排配置的縮寫，並指出我們使用 TCP 作為通訊協定。當 [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) 被指定為繫結時，先前也已在組態檔中指出此項。
	
	在本教學課程中，URI 為 `sb://putServiceNamespaceHere.windows.net/EchoService`。

### 建立和設定服務主機

1. 將連線模式設定為 `AutoDetect`。

	```
	ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
	```

	連線模式可描述服務用來與服務匯流排通訊的通訊協定 (HTTP 或 TCP)。使用預設設定 `AutoDetect`，服務會嘗試透過 TCP 連接至服務匯流排，如果無法使用 TCP，則會透過 HTTP 連接。請注意，這有別於服務針對用戶端通訊指定的通訊協定。該通訊協定取決於使用的繫結。例如，服務可以使用 [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) 繫結，指定其端點 (已在服務匯流排上公開) 透過 HTTP 與用戶端通訊。該相同服務可以指定 **ConnectivityMode.AutoDetect**，以便透過 TCP 與服務匯流排服務通訊。

1. 使用本節稍早建立的 URI，建立服務主機。

	```
	ServiceHost host = new ServiceHost(typeof(EchoService), address);
	```

	服務主機是將服務具現化的 WCF 物件。在此，您會將您要建立的服務類型 (`EchoService` 類型)，以及您要公開服務的位址傳給它。

1. 在 Program.cs 檔案頂端，加入 [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) 和 [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx) 的參考。

	```
	using System.ServiceModel.Description;
	using Microsoft.ServiceBus.Description;
	```

1. 回到 `Main()`，設定要啟用公開存取的端點。

	```
	IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
	```

	此步驟會通知服務匯流排，檢查您的專案的服務匯流排 ATOM 摘要，即可公開找到您的應用程式。如果您將 **DiscoveryType** 設定為 [私人]，用戶端仍可存取服務。不過，服務在搜尋服務匯流排命名空間時並不會出現。相反地，用戶端必須事先知道端點路徑。

1. 將服務認證套用至在 App.config 檔案中定義的服務端點︰

	```
	foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
	{
	    endpoint.Behaviors.Add(serviceRegistrySettings);
	    endpoint.Behaviors.Add(sasCredential);
	}
	```

	如上一個步驟所述，您可能已在組態檔中宣告多個服務和端點。若是如此，此程式碼會周遊組態檔及搜尋每個應套用您的認證的端點。不過，在本教學課程中，組態檔只有一個端點。

### 開啟服務主機

1. 開啟服務。

	```
	host.Open();
	```

1. 通知使用者此服務正在執行，以及說明如何關閉服務。

	```
	Console.WriteLine("Service address: " + address);
	Console.WriteLine("Press [Enter] to exit");
	Console.ReadLine();
	```

1. 完成時，關閉服務主機。

	```
	host.Close();
	```

1. 按 F6 來建置專案。

### 範例

下列範例包括教學課程先前步驟的服務合約和實作，以及在主控台應用程式中裝載服務。將下列程式碼編譯為名為 EchoService.exe 的可執行檔。

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## 建立服務合約的 WCF 用戶端

下一步是建立基本服務匯流排用戶端應用程式並定義您將在後續步驟中實作服務合約。請注意，其中有許多步驟類似於用來建立服務的步驟︰定義合約、編輯 App.config 檔案、使用認證來連接至服務匯流排等等。程序後面的範例提供用來執行這些工作的程式碼。

1. 若要在目前的 Visual Studio 方案中為用戶端建立新專案，請執行下列作業︰
	1. 在 [方案總管] 中含有服務的相同方案中，以滑鼠右鍵按一下目前的方案 (而非專案)，然後按一下 [新增]。然後按一下 [新增專案]。
	2. 在 [新增專案] 對話方塊中，按一下 **Visual C#** (如果 **Visual C#** 未出現，請在 [其他語言] 中尋找)，選取 [主控台應用程式] 範本，並將它命名為 **EchoClient**。
	3. 按一下 [確定]。<br />

1. 在 [方案總管] 中，按兩下 **EchoClient** 專案中的 Program.cs 檔案以在編輯器中開啟。

1. 將命名空間名稱從 `EchoClient` 的預設名稱變更為 `Microsoft.ServiceBus.Samples`。

1. 將 System.ServiceModel.dll 的參考新增至專案：
	1. 在 [方案總管] 中以滑鼠右鍵按一下 **EchoClient** 專案底下的 [參考]。然後按一下 [加入參考]。
	2. 因為您已在本教學課程的第一個步驟中加入此組件的參考，所以它現在會列在 [最近] 索引標籤中。按一下 [最近]，然後從清單中選取 **System.ServiceModel.dll** 然後按一下 [確定]。如果在 [最近] 索引標籤中看不到 **System.ServiceModel.dll**，請按一下 [瀏覽] 索引標籤並移至 **C:\\Windows\\Microsoft.NET\\Framework\\v3.0\\Windows Communication Foundation**。然後從該處選取組件。<br />

1. 在 Program.cs 檔案中新增 [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) 命名空間的 `using` 陳述式。

	```
	using System.ServiceModel;
	```

1. 重複上述步驟，將 Microsoft.ServiceBus.dll 的參考和 [Microsoft.ServiceBus](https://msdn.microsoft.com/library/microsoft.servicebus.aspx) 命名空間新增至您的專案。

1. 下列範例所示，將服務合約定義新增至命名空間。請注意，此定義等與 [服務] 專案中使用的定義相同。您應該在 `Microsoft.ServiceBus.Samples` 命名空間的頂端加入此程式碼。

	```
	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	publicinterface IEchoContract
	{
	    [OperationContract]
	    string Echo(string text);
	}

	publicinterface IEchoChannel : IEchoContract, IClientChannel { }
	```

1. 按 F6 來建置用戶端。

### 範例

下列程式碼顯示 EchoClient 專案中 Program.cs 檔案的目前狀態。

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## 設定 WCF 用戶端

在此步驟中，您可為基本用戶端應用程式建立 App.config 檔案，以存取先前在本教學課程中建立的服務。此 App.config 檔案定義端點的合約、繫結和名稱。程序後面的範例提供用來執行這些工作的程式碼。

1. 在 [方案總管] 的用戶端專案中，按兩下 **App.config** 以開啟目前包含下列 XML 元素的檔案。

	```
	<?xmlversion="1.0"?>
	<configuration>
	  <startup>
	    <supportedRuntimeversion="v4.0"sku=".NETFramework,Version=v4.0"/>
	  </startup>
	</configuration>
	```

1. 將 XML 元素新增至 `system.serviceModel` 的 App.config 檔案。

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	
	  </system.serviceModel>
	</configuration>
	```
	
	此元素宣告您的應用程式使用 WCF 樣式的端點。如先前所述，服務匯流排應用程式與 WCF 應用程式的組態大致相同；主要差異在於組態檔所指向的位置。

1. 在 system.serviceModel 元素中加入 `<client>` 元素。

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	    <client>
	    </client>
	  </system.serviceModel>
	</configuration>
	```

	此步驟宣告您正在定義 WCF 樣式的用戶端應用程式。

1. 在 `client` 元素內，定義端點的名稱、合約和繫結類型。

	```
	<endpointname="RelayEndpoint"
					contract="Microsoft.ServiceBus.Samples.IEchoContract"
					binding="netTcpRelayBinding"/>
	```

	此步驟會定義端點的名稱、服務中定義的合約，以及用戶端應用程式使用 TCP 來與服務匯流排通訊的事實。端點名稱在下一步中用來連結此端點組態與服務 URI。

1. 緊接在 <client> 元素之後，加入下列繫結延伸模組。
 
	```
	<extensions>
	  <bindingExtensions>
	    <addname="netTcpRelayBinding"type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
	  </bindingExtensions>
	</extensions>
	```

1. 按一下 [檔案]，然後按一下 [全部儲存]。

## 範例

下列程式碼會顯示 Echo 用戶端的 App.config 檔案。

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## 實作 WCF 用戶端來呼叫服務匯流排

在此步驟中，您可實作基本用戶端應用程式，以存取您先前在本教學課程中建立的服務。與此服務類似，用戶端會執行許多相同的作業來存取服務匯流排：

1. 設定連線模式。

1. 建立可找出主機服務的 URI。

1. 定義安全性認證。

1. 將認證套用至連線。

1. 開啟連線。

1. 執行應用程式特定的工作。

1. 關閉連線。

不過，其中一個主要差異在於用戶端應用程式會使用通道來連接至服務匯流排，而此服務則是利用對 **ServiceHost** 的呼叫。程序後面的範例提供用來執行這些工作的程式碼。

### 實作用戶端應用程式

1. 將連線模式設定為 [AutoDetect]。在用戶端應用程式的 `Main()` 方法中加入下列程式碼。

	```
	ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
	```

1. 定義變數，以保留服務命名空間以及從主控台讀取之 SAS 金鑰的值。

	```
	Console.Write("Your Service Namespace: ");
	string serviceNamespace = Console.ReadLine();
	Console.Write("Your SAS Key: ");
	string sasKey = Console.ReadLine();
	```

1. 建立可定義服務匯流排專案中主機位置的 URI。

	```
	Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
	```

1. 建立服務命名空間端點的認證物件。

	```
	TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
	sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
	```

1. 建立通道工廠，以載入 App.config 檔案中所述的組態。

	```
	ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
	```

	通道工廠是一個 WCF 物件，可建立通道以供服務與用戶端應用程式進行通訊。

1. 套用服務匯流排認證。

	```
	channelFactory.Endpoint.Behaviors.Add(sasCredential);
	```

1. 建立並開啟服務的通道。

	```
	IEchoChannel channel = channelFactory.CreateChannel();
	channel.Open();
	```

1. 撰寫 Echo 的基本使用者介面和功能。

	```
	Console.WriteLine("Enter text to echo (or [Enter] to exit):");
	string input = Console.ReadLine();
	while (input != String.Empty)
	{
	    try
	    {
	        Console.WriteLine("Server echoed: {0}", channel.Echo(input));
	    }
	    catch (Exception e)
	    {
	        Console.WriteLine("Error: " + e.Message);
	    }
	    input = Console.ReadLine();
	}
	```

	請注意，程式碼會使用通道物件的執行個體作為服務的 Proxy。

1. 關閉通道，並關閉工廠。

	```
	channel.Close();
	channelFactory.Close();
	```

## 執行用戶端應用程式

1. 按 F6 來建置方案。這會建置您在本教學課程的上一個步驟中建立的用戶端專案和服務專案，並為每個專案建立一個可執行檔。

1. 執行用戶端應用程式之前，請確定服務應用程式正在執行。

	您現在應有 Echo 服務應用程式的可執行檔 (名為 EchoService.exe)，其位於您的服務專案資料夾底下︰\\bin\\Debug\\EchoService.exe (適用於偵錯組態) 或 \\bin\\Release\\EchoService.exe (適用於發行組態)。按兩下此檔案以啟動服務應用程式。

1. 主控台視窗隨即開啟並提示您輸入命名空間。在此主控台視窗中，輸入服務命名空間並按 Enter 鍵。

1. 接下來，系統會提示您輸入 SAS 金鑰。輸入 SAS 金鑰並按 ENTER 鍵。

	以下是主控台視窗的範例輸出。請注意，此處提供的值僅適用於範例。

	`Your Service Namespace: myNamespace`

	`Your SAS Key: <SAS key value>`

	服務應用程式會啟動並將它所接聽的位址列印到主控台視窗，如下列範例所示。

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`
    
1. 執行用戶端應用程式。您現在應有 Echo 用戶端應用程式的可執行檔 (名為 EchoClient.exe)，其位於 .\\bin\\Debug\\EchoClient.exe (適用於偵錯組態) 或 .\\bin\\Release\\EchoClient.exe (適用於發行組態)。按兩下此檔案以啟動用戶端應用程式。

1. 主控台視窗隨即開啟，並提示您輸入先前為服務應用程式輸入的相同資訊。遵循上述步驟，為用戶端應用程式的服務命名空間、簽發者名稱和簽發者密碼輸入相同的值。

1. 輸入這些值後，用戶端就會開啟服務通道，並提示您輸入一些文字，如下列主控台輸出範例所示。

	`Enter text to echo (or [Enter] to exit):`

	輸入一些文字以傳送至服務應用程式並按 Enter 鍵。這段文字會透過 Echo 服務作業傳送至服務，並如下列範例輸出所示出現在服務主控台視窗。

	`Echoing: My sample text`

	用戶端應用程式會接收 `Echo` 作業的傳回值 (此值是原始文字)，並將它列印至主控台視窗。以下是用戶端主控台視窗的範例輸出。

	`Server echoed: My sample text`

1. 您可以繼續以這種方式將文字訊息從用戶端傳送至服務。完成後，在用戶端和服務主控台視窗中按 Enter 鍵以結束這兩個應用程式。

## 範例

下列範例示範如何建立用戶端應用程式、如何呼叫服務的作業，以及如何在完成作業呼叫之後關閉用戶端。

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

啟動用戶端之前，請確定服務正在執行。

## 後續步驟

本教學課程示範了如何使用服務匯流排「轉送」功能，建置服務匯流排用戶端應用程式和服務。如需使用服務匯流排[代理傳訊](service-bus-messaging-overview.md/#Brokered-messaging)的類似教學課程，請參閱[服務匯流排代理傳訊 .NET 教學課程](https://msdn.microsoft.com/library/hh367512.aspx)。

若要深入了解服務匯流排，請參閱下列主題。

- [服務匯流排訊息概觀](service-bus-messaging-overview.md)
- [服務匯流排基本概念](service-bus-fundamentals-hybrid-solutions.md)
- [服務匯流排架構](service-bus-architecture.md)

<!---HONumber=Oct15_HO3-->