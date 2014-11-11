<properties linkid="dev-net-how-to-service-bus-relay" urlDisplayName="Service Bus Relay" pageTitle="How to use Service Bus relay (.NET) - Azure" metaKeywords="get started azure Service Bus Relay C# " description="Learn how to use the Azure Service Bus relay service to connect two applications hosted in different locations." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use the Service Bus Relay Service" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# 如何使用服務匯流排轉送服務

本指南將示範如何使用「服務匯流排」轉送服務。這些範例均以 C# 撰寫，並使用 Windows Communication Foundation API以及包含在服務匯流排組件 (Azure .NET 程式庫的一部分) 中的擴充功能。如需服務匯流排轉送的詳細資訊，請參閱 [後續步驟][後續步驟]一節。

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <span class="short-header">什麼是服務匯流排轉送</span>什麼是服務匯流排轉送

服務匯流排**轉送**服務可讓您建立一個可在 Azure 資料中心和您自己的內部部署企業環境中執行的**混合式**應用程式。服務匯流排轉送可幫助達成此目標，方法是讓您以安全的方式，向公用雲端公開位於企業網路內部的 Windows Communication Foundation (WCF) 服務，而無需開啟防火牆連線或要求對企業網路的基礎結構進行侵入式變更。

![轉送概念][轉送概念]

服務匯流排轉送可讓您代管位於現有企業環境內的 WCF 服務。您可以接著將接聽這些 WCF 服務的傳入工作階段和要求，委派給在 Azure 內部執行的服務匯流排。這可讓您將這些服務公開給在 Azure 中執行的應用程式程式碼，或是給行動工作者或外部網路合作夥伴環境。服務匯流排可讓您以精細的層次安全地控制可存取這些服務的使用者。它提供了功能強大且安全的方式，來公開應用程式功能及現有企業解決方案的資料，並從雲端加以利用。

本指南將示範如何使用服務匯流排轉送來建立 WCF Web 服務，使用可在兩端之間實作安全交談的 TCP 通道繫結來加以公開。

## <span class="short-header">建立服務命名空間</span>建立服務命名空間

若要開始在 Azure 中使用服務匯流排轉送，您首先必須建立服務命名空間。服務命名空間提供範圍容器，可在應用程式內定址服務匯流排資源。

建立服務命名空間：

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。

2.  在管理入口網站的左側瀏覽窗格中，按一下 [服務匯流排]
    。

3.  在管理入口網站的下方窗格中，按一下 [建立]。

    ![][0]

4.  在 [加入新的命名空間] 對話方塊中，輸入命名空間名稱。系統會立即檢查此名稱是否可用。

    ![][1]

5.  確定可以使用此命名空間名稱之後，選擇要裝載命名空間的國家或地區 (必須使用您要部署運算資源的相同國家/地區)。

    重要事項：請挑選您想要選擇來部署應用程式的**相同區域**。這樣可以獲得最佳效能。

6.  按一下核取記號。此時系統會建立並啟用服務命名空間。系統為帳戶提供資源時，您可能需要等幾分鐘。

    ![][2]

    然後，您建立的命名空間就會出現在管理入口網站中，稍待片刻就會生效。等到狀態變成 [作用中] 之後再繼續。

## <span class="short-header">取得管理認證</span>取得命名空間的預設管理認證

若要在新的命名空間上執行管理作業 (例如建立轉送連線)，您必須取得命名空間的管理認證。

1.  在左側瀏覽窗格中，按一下 [服務匯流排] 節點，以顯示可用的命名空間清單：
    ![][0]

2.  從顯示的清單中，選取您剛建立的命名空間：
    ![][3]

3.  按一下 [連線資訊]。
    ![][4]

4.  在 [Access connection information] 對話方塊中，找出 [Default Issuer] 和 [Default Key] 項目。請記下這些值，接下來會利用此資訊對命名空間執行作業。

## <span class="short-header">取得 NuGet 封裝</span>取得服務匯流排 NuGet 封裝

要取得服務匯流排 API，並對應用程式進行設定，以使用所有服務匯流排相依性的最簡單方法，便是使用服務匯流排 **NuGet** 封裝。NuGet Visual Studio 擴充功能可讓您輕鬆地在 Visual Studio 和 Visual Studio Express 2012 for Web 中安裝並更新程式庫與工具。要取得服務匯流排 API，並對應用程式進行設定，以使用所有服務匯流排相依性的最簡單方法，便是使用服務匯流排 NuGet 封裝。

若要在應用程式中安裝 NuGet 封裝，請執行下列動作：

1.  在 [方案總管] 中，以滑鼠右鍵按一下 [喜好設定]，然後按一下 [Manage NuGet Packages]。
2.  搜尋 "WindowsAzure" 並選取 [Azure 服務匯流排] 項目。按一下 [安裝] 完成安裝作業，然後關閉此對話方塊。

    ![][5]

## <span class="short-header">公開與取用 SOAP Web 服務</span>如何使用服務匯流排來公開與取用使用 TCP 的 SOAP Web 服務

若要將現有的 WCF SOAP Web 服務公開給外部使用，您必須對服務繫結和位址進行變更。這可能需要變更組態檔，或可能需要變更程式碼，視您如何設定和配置 WCF 服務而定。請注意，WCF 可讓您對相同的服務有多個網路端點，因此您可以保留現有的內部端點，並同時新增用於外部存取的服務匯流排端點。

在本工作中，您將建立一個簡單的 WCF 服務，並為它新增服務匯流排接聽程式。本練習假設您對 Visual Studio 2012 有一定程度的了解，因此將不會逐步解說完成建立專案的所有詳細資料。而是會將重點放在程式碼。

開始下面的步驟之前，請完成下列設定環境的程序：

1.  在 Visual Studio 中，建立解決方案中包含兩個專案("Client" 和 "Service") 的主控台應用程式。
2.  將這兩個專案的目標架構設為 .NET Framework 4。
3.  將 [Azure 服務匯流排 NuGet] 封裝新增至這兩個專案。這會將所有必要組件參考新增至您的專案。

### 如何建立服務

首先建立服務本身。任何 WCF 服務都包含至少三個獨特部分：

-   說明會交換哪些訊息以及會叫用哪些作業的合約定義。
-   所述合約的實作。
-   代管該服務並公開多個端點的主機。

本節中的程式碼範例將逐一說明這些元件。

此合約會定義 **AddNumbers** 這個單一作業，以新增兩個數字並傳回結果。 **IProblemSolverChannel** 介面可讓用戶端更輕鬆地管理 Proxy 存留期。建立此類介面被視為是最佳做法。最好能夠將此合約定義置於個別檔案中，以便您可以分別從 "Client" 和 "Service" 專案中參照該檔案，但您也可以將此程式碼複製到這兩個專案內：

        using System.ServiceModel;
     
        [ServiceContract(Namespace = "urn:ps")]
        interface IProblemSolver
        {
            [OperationContract]
            int AddNumbers(int a, int b);
        }
     
        interface IProblemSolverChannel : IProblemSolver, IClientChannel {}

合約準備好了以後，實作便微不足道：

        class ProblemSolver : IProblemSolver
        {
            public int AddNumbers(int a, int b)
            {
                return a + b;
            }
        }

**如何以程式設計方式設定服務主機**

在準備好合約和實作之後，您便可以開始代管服務。代管會發生在 **System.ServiceModel.ServiceHost** 物件內，此物件將負責
管理服務的執行個體並代管接聽訊息的端點。下面的程式碼將設定包含一般本機端點和服務匯流排端點的服務，以緊密地說明內部和外部端點的外觀。使用您的命名空間名稱來取代字串 "**namespace**"，並使用在上述設定步驟中所取得的發行者金鑰來取代 "**key**"。

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

    sh.AddServiceEndpoint(
       typeof (IProblemSolver), new NetTcpBinding(), 
       "net.tcp://localhost:9358/solver");

    sh.AddServiceEndpoint(
       typeof(IProblemSolver), new NetTcpRelayBinding(), 
       ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver"))
        .Behaviors.Add(new TransportClientEndpointBehavior {
              TokenProvider = TokenProvider.CreateSharedSecretTokenProvider( "owner", "**key**")});

    sh.Open();

    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();

    sh.Close();

在此範例中，您將建立相同合約實作的兩個端點。一個是本機，一個是透過服務匯流排的投射。兩者之間的主要差異在於繫結。
本機端點是 **NetTcpBinding**，服務匯流排端點和位址是 **NetTcpRelayBinding**。本機端點會包含具有獨特連接埠的
本機網路位址。服務匯流排端點會包含一個由字串 "sb"、您的命名空間名稱及路徑 "solver" 組合而成的端點位址。這會產生 URI "sb://[serviceNamespace].servicebus.windows.net/solver"，指出服務端點為具有完整外部 DNS 名稱的服務匯流排 TCP 端點。如前所述，如果您將要取代預留位置的程式碼置入 "Service" 應用程式的 **Main** 函數中，您將會有一個功能性服務。如果您想要服務專門接聽服務匯流排，請移除本機端點宣告。

**如何在 App.config 檔案中設定服務主機**

您也可以使用 App.config 檔案來設定主機。此案例中的服務代管程式碼會如下所示：

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
    sh.Open();
    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();
    sh.Close();

端點定義移入 App.config 檔案。請注意，**NuGet** 封裝已在 App.config 檔案中新增許多定義，這些都是服務匯流排的必要組態擴充功能。下列程式碼片段 (與上述程式碼完全相同)應會出現在 **system.serviceModel** 元素的正下方。此程式碼片段會假設您的專案 C# 命名空間名稱為 "Service"。請使用您的服務匯流排服務命名空間和金鑰來取代預留位置。

    <services>
        <service name="Service.ProblemSolver">
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpBinding"
                      address="net.tcp://localhost:9358/solver"/>
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpRelayBinding"
                      address="sb://**namespace**.servicebus.windows.net/solver"
                      behaviorConfiguration="sbTokenProvider"/>
        </service>
    </services>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

在進行這些變更之後，此服務便會和以前一樣啟動，但多了兩個即時端點：一個在本機和一個在雲端中進行接聽。

### 如何建立用戶端

**如何以程式設計方式設定用戶端**

若要取用此服務，您可以使用 **ChannelFactory** 物件來建構 WCF 用戶端。服務匯流排會使用透過存取控制服務 (ACS) 實作的宣告型安全模型。**TokenProvider** 類別代表安全性權杖提供者，包含可傳回部分知名權杖提供者的內建原廠方法。
以下範例會使用 **SharedSecretTokenProvider** 來保存共用的密碼認證，並處理來自存取控制服務的適當權杖擷取。如上一節所述，將從入口網站取得這些名稱和金鑰。

首先，將 **IProblemSolver** 合約程式碼從服務中參照或複製到您的用戶端專案。

然後，取代用戶端 **Main** 方法中的程式碼，並再次使用您的服務匯流排服務命名空間和金鑰來取代預留位置文字：

    var cf = new ChannelFactory<IProblemSolverChannel>(
        new NetTcpRelayBinding(), 
        new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver")));

    cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
                { TokenProvider = TokenProvider.CreateSharedSecretTokenProvider("owner","**key**") });
     
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

您現在可以開始編譯用戶端和服務、執行它們 (先執行服務)，然後用戶端將呼叫此服務並列印 "9"。您可以在不同機器上 (即使是在不同網路上) 執行用戶端和伺服器，通訊仍然可以運作。您也可以在雲端或在本機上執行用戶端程式碼。

**如何在 App.config 檔案中設定用戶端**

您也可以使用 App.config 檔案來設定用戶端。此作業的用戶端程式碼會如下所示：

    var cf = new ChannelFactory<IProblemSolverChannel>("solver");
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

端點定義移入 App.config 檔案。下列程式碼片段 (與上述程式碼相同) 應會出現在 **system.serviceModel** 元素的正下方。和以前一樣，您必須在此處使用您的服務匯流排服務命名空間和金鑰來取代預留位置。

    <client>
        <endpoint name="solver" contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://**namespace**.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </client>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

## <span class="short-header">後續步驟</span>後續步驟

了解基本的服務匯流排**轉送**服務之後，請參考下列連結以取得更多資訊。

-   建立服務：[建立服務匯流排的服務][建立服務匯流排的服務]。
-   建立用戶端：[建立服務匯流排用戶端應用程式][建立服務匯流排用戶端應用程式]。
-   服務匯流排範例：從 [Azure 範例][Azure 範例]中下載。

  [後續步驟]: #next_steps
  [create-account-note]: ../includes/create-account-note.md
  [轉送概念]: ./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [0]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png
  [1]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-04.png
  [2]: ./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-27.png
  [3]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-09.png
  [4]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-06.png
  [5]: ./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png
  [建立服務匯流排的服務]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee173564.aspx
  [建立服務匯流排用戶端應用程式]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee173543.aspx
  [Azure 範例]: http://code.msdn.microsoft.com/windowsazure
