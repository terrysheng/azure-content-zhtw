<properties
   pageTitle="Node.js 和可靠的動作項目 | Microsoft Azure"
   description="詳細說明如何組建 node.js express 應用程式，其使用可靠的動作項目並在 Azure Service Fabric 平台上執行。"
   services="service-fabric"
   documentationCenter="nodejs"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/17/2015"
   ms.author="claudioc"/>


# Node.js 和可靠的動作項目：致勝組合
本文是關於如何建置使用 node.js 和可靠動作項目之應用程式的概觀。最終的解決方案是 javascript 程式碼的混合體，其主要用於提供應用程式 (Web/Rest API) 前端與 C# 以供更複雜的運算。利用 Service Fabric 程式設計模型，現成的應用程式既可調整又可靠。此程序以下列步驟為基準：

1. 使用 Visual Studio 的 Service Fabric 工具建立新的 Service Fabric 無狀態或可設定狀態的動作項目專案
2. 使用如 [Visual Studio 的 node.js 工具](https://github.com/Microsoft/nodejstools/releases/tag/v1.1.RC)建立 node.js 專案 
3. 在 Service Fabric 解決方案中加入 node.js 專案 (例如 Basic Express 4 應用程式)。使用解決方案/加入現有專案即可包含 node.js 專案。 
4. 封裝 node.js 應用程式以便使用 Service Fabric 的 VS 工具部署此應用程式

## 建立 node.js 專案
建立 node.js 專案並加入相依性之後，您需要變更專案的目錄結構，讓它遵循 Visual Studio 的 Service Fabric 工具所要求的結構，以便封裝及部署應用程式，就像處理任何其他 Service Fabric 服務一樣。目標是只變更一次目錄結構，自此而後即能夠在 Visual Studio 中使用部署程序，並可部署 node.js 應用程式及解決方案的其他服務。目錄結構必須完成的變更如下：

1. 建立 PackageRoot 目錄
2. 在 PackageRoot 下建立 Code 目錄
3. 移動 Code 目錄下的所有檔案和目錄

完成之後，Visual Studio 的專案結構應如下：

![][8]

您可以看到所有的 node.js 程式碼都位在 PackageRoot/Code 目錄下。Service Fabric 對要部署應用程式之節點上所安裝的應用程式/程式庫，不預設任何反應，所以您需要包含所有相依性。如為需要包含 node.exe，Service Fabric 才能執行程式碼的 node.js (您可在 program files\\nodejs 目錄下找到 node.exe)。

![][3]

## 加入 servicemanifest.xml 中繼資料檔
為部署 node.js 應用程式，我們需要加入必要的中繼資料檔，指定 Service Fabric 會使用的某些屬性，以判斷如何部署和啟動應用程式。

這是 servicemanifest.xml 應有形貌的範例，如需必須更新的重要元素詳細資訊，請參閱[本文](service-fabric-deploy-existing-app.md)，通常需要更新的是：

* 名稱 (ServiceManifest 元素)
* ServiceTypeName (StatelessServiceType 元素)
* 指定應用於啟動應用程式之 js 檔案的引數 (ExeHost 元素)。


```xml

<?xml version="1.0" encoding="utf-8"?>

<ServiceManifest Name="NodejsFrontendPkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="NodejsFrontendType" UseImplicitHost="true" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>node.exe</Program>
        <Arguments>server.js</Arguments>
        <WorkingFolder>CodeBase</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0.0" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

> [AZURE.NOTE]請注意，servicemanifest.xml 選項之一可能無法搭配 node.js。`ConsoleRedirection` 在某些情況下無法運作，如果 node.js 模組 (例如Express) 假設 stdout 和 stderr 的 fd 為 1 和 2。

將 `servicemanifest.xml` 檔案加入 node.js 專案之後，專案結構應如下：

![][4]

## 加入 Service Fabric 二進位檔
下一步是加入 Service Fabric 二進位檔，其用以連接在 Service Fabric 叢集中執行的動作項目。如您在 Edge.js 範例中所見，組件有一些參考。為使 edge.js 取用這些組件，組件需和 node.js 程式碼一起複製。最簡單的方式是複製現有專案的二進位檔，Code目錄需要包含 (且將為 edge.js 所用) 的檔案如下：

```
Microsoft.ServiceFabric.Actors.dll
Microsoft.ServiceFabric.Collections.dll
Microsoft.ServiceFabric.Data.dll
Microsoft.ServiceFabric.Data.Log.dll
Microsoft.ServiceFabric.ReplicatedStore.dll
Microsoft.ServiceFabric.Replicator.dll
Microsoft.ServiceFabric.Services.dll
ServiceFabricServiceModel.dll
System.Fabric.Common.Internal.dll
System.Fabric.Common.Internal.Strings.resources.dll
System.Fabric.dll
```

將二進位檔加入專案之後，專案結構應如下：

![][5]

## 在 applicationmanifest 檔案中加入 node.js 專案的參考
下一步是將 node.js 服務加入應用程式資訊清單，以使用 Visual Studio 的 Service Fabric 工具部署此服務。這麼做有其必要，因為 Visual Studio 的 Service Fabric 工具並未與 node.js 專案整合，所以它必須以手動方式加入。

`applicationmanifest.xml` 檔案中需要加入下列元素：

* `ServiceManifestImport`
* `Service`

> [AZURE.NOTE]請確定 `servicemanifest.xml` 使用相同的名稱指定 `ServiceName` 和 `ServiceTypeName`。`applicationmanifest.xml` 檔案應該類似下列檔案：

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeServiceFabricSampleApplication" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeServiceFabricSamplePkg" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="VisualObjectsNodejsWebServicePkg" ServiceManifestVersion="1.0.0.0" />
       </ServiceManifestImport>
   <DefaultServices>
      <Service Name="NodeServiceFabricSampleActorService">
         <StatelessService ServiceTypeName="NodeServiceFabricSampleActorServiceType">
            <UniformInt64Partition PartitionCount="9" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatelessService>
      </Service>
     <Service Name="NodejsFrontendPkg">
       <StatelessService ServiceTypeName="NodejsFrontendType">
         <SingletonPartition />
       </StatelessService>
     </Service>
   </DefaultServices>
</ApplicationManifest>

```

## 在 Node.js 應用程式中使用可靠的動作項目
現在專案結構已設定，我們可專注於讓 node.js 應用程式連接叢集中可靠動作項目的程式碼，就像您可從 .NET 應用程式所執行的一樣。我們想要啟用的案例，是組建做為用戶端應用程式前端/閘道的 node.js 應用程式，以及公開用戶端應用程式可以使用的 Web 應用程式或一組 REST API。Node.js 提供應用程式的前端，而可靠的動作項目則提供應用程式可調整和可靠的「應用程式伺服器」層。在 Service Fabric 中，可使用 [ActorProxy 類別](service-fabric-reliable-actors-introduction.md#actor-communication)叫用可靠的動作項目。幸好有絕佳的 node.js 模組可用於呼叫 .NET 程式碼：[Edge.js](https://github.com/tjanczuk/edge)。您可以使用 github 儲存機制中的指示了解如何在電腦上安裝邊緣。

 
![][2]

在 Visual Studio 中使用 NPM 或 NPM UI 安裝邊緣之後，您必須將新安裝的模組移入 Code 子目錄下的 node\_modules 目錄中 (我們變更了 node.js 專案的結構，並移動了 PackageRoot/Code 目錄下的所有程式碼)。您可以在 Github 的 edge.js 儲存機制中，找到如何使用邊緣呼叫 .NET 程式碼的好例子，以下是當您需要使用 ActorProxy 類別呼叫可靠動作項目時，程式碼應有形貌的簡單範例。

```javascript

var getActorStatus = edge.func(function () {

/*

    #r "Microsoft.ServiceFabric.Actors.dll"
    #r "System.Globalization.dll"
    #r "System.dll"
    #r  "visualobjects.interfaces.dll"
    
    using Microsoft.ServiceFabric.Actors;
    using System.Globalization;
    using VisualObjects.Interfaces;
    using System.Threading.Tasks;
    using System; 


public class Startup
    {
         public async Task<object> Invoke(dynamic input)
        {

            var objectId = (string) input.actorId;
            var serviceUri = (string) input.serviceUri;
            var actId = new ActorId(objectId);
            var serId = new Uri(serviceUri);
            var actorProxy = ActorProxy.Create<IVisualObjectActor>(actId, serId);
            var buffer = await actorProxy.GetStateAsJsonAsync();
            if (!string.IsNullOrEmpty(buffer))
                return buffer;
                else 
                return "null";
         }
  }
 
 */ 

});

```

> [AZURE.NOTE]ActorProxy 要使用動作項目介面才知道它應該連接到哪一個動作項目。為使在 node.js 處理序中執行的 Managed 程式碼可以具現化該類別，動作項目介面組件必須像其他 Service Fabric dll 一樣複製到 Code 目錄中。注意：每當您變更介面中的方法/參數，即必須複製 (或在 VS 中設定建置後動作) dll。

![][6]

## Deployment
此時可以使用 Visual Studio 的 Service Fabric 工具部署專案。這個程序的最後步驟是參考 Service Fabric 應用程式專案中的專案，使其納入應用程式封裝和部署在叢集中。

![][9]
 
例如，您可以使用解決方案操作功能表，部署會包含 node.js 應用程式的應用程式。

![][10]

## 後續步驟
* 深入了解[可靠的動作項目](service-fabric-reliable-actors-introduction.md)
* 深入了解 Service Fabric [應用程式週期](service-fabric-application-lifecycle.md)
* 深入了解升級 [Service Fabric 應用程式](service-fabric-application-upgrade.md) 

<!-- images -->
[1]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure.PNG
[2]: ./media/service-fabric-node-and-reliable-actors-app/edge-js.PNG
[3]: ./media/service-fabric-node-and-reliable-actors-app/node-exe.PNG
[4]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-manifest.PNG
[5]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-dlls.PNG
[6]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-interface-dll.PNG
[7]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-config.PNG
[8]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure1.PNG
[9]: ./media/service-fabric-node-and-reliable-actors-app/application-project-reference.PNG
[10]: ./media/service-fabric-node-and-reliable-actors-app/solution-deploy.PNG

<!---HONumber=Oct15_HO3-->