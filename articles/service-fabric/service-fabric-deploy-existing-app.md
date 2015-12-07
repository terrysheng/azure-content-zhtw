<properties
   pageTitle="在 Azure Service Fabric 中部署自訂應用程式 | Microsoft Azure"
   description="逐步解說如何封裝現有的應用程式，使其可以部署在 Azure Service Fabric 叢集上"
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="bscholl"/>

# 將自訂應用程式部署至 Service Fabric

您可以在 Service Fabric 中執行任何類型的現有應用程式，例如 Node.js、Java 或原生應用程式。Service Fabric 會將這些應用程式視為無狀態服務，並根據可用性和其他度量，將它們放在叢集的節點上。這篇文章說明如何將現有的應用程式封裝並部署至 Service Fabric 叢集。

## 在 Service Fabric 中執行自訂應用程式的優點

在 Service Fabric 叢集中執行應用程式有幾項優點：

- 高可用性：Service Fabric 中執行的應用程式天生就有高用性。Service Fabric 會確保應用程式一定有一個執行個體已啟動並執行
- 狀況監控：現成可用的 Service Fabric 狀況監控會偵測應用程式是否已啟動並執行，而且會在失敗的情況下提供診斷資訊   
- 應用程式生命週期管理：除了有無需停機的升級，Service Fabric 也能夠在升級期間發生問題時回復到之前的版本。    
- 密度：您可以在叢集中執行多個應用程式，每個應用程式不必在自己的硬體上執行

本文中涵蓋將現有的應用程式封裝並部署至 Service Fabric 的基本步驟。


## 應用程式和服務資訊清單檔的快速概觀

在深入探討部署現有應用程式的細節之前，最好先了解 Service Fabric 封裝和部署模型。Service Fabric 封裝部署模型主要依賴兩個檔案：


* **應用程式資訊清單**

  應用程式資訊清單用來描述應用程式，並列出組成該應用程式的服務，以及用來定義應如何部署服務的其他參數 (例如執行個體數目)。在 Service Fabric 領域中，應用程式是「可升級的單位」。應用程式可以升級為單一單位，其中潛在的失敗 (及潛在的回復) 由平台所管理，以保證升級程序完全成功，若是失敗，則不會讓應用程式處於不明/不穩定的狀態。


* **服務資訊清單**

  服務資訊清單描述服務的元件。其中包含一些資料，例如服務的名稱和類型 (Service Fabric 用來管理服務的資訊)、其程式碼、組態和資料元件，加上一些在服務部署後可用來設定服務的額外參數。我們不會詳細說明服務資訊清單中所有可用的不同參數，但我們將會說明在 Service Fabric 上執行現有應用程式所需的子集


## 應用程式封裝檔案結構
為了將應用程式部署至 Service Fabric，應用程式必須遵循預先定義的目錄結構。以下是該結構的範例。

```
|-- AppplicationPackage
	|-- code
		|-- existingapp.exe
	|-- config
		|--Settings.xml
    |--data    
    |-- ServiceManifest.xml
|-- ApplicationManifest.xml
```

根目錄包含可定義應用程式的 ApplicationManifest.xml 檔案。對於應用程式包含的每個服務，都有一個子目錄用來包含服務所需的所有構件：ServiceManifest.xml 以及通常有 3 個目錄：

- *code*：包含服務程式碼
- *config*：包含服務可在執行階段存取的 settings.xml 檔案 (和其他必要檔案)，以擷取特定組態設定。
- *data*：額外的目錄，用來儲存服務可能需要的其他本機資料。注意：資料應用來只儲存短期資料，如果在容錯移轉期間必須重新定位服務，Service Fabric 不會將變更複製/複寫到資料目錄。

注意：如果不需要 `config` 和 `data` 目錄，則不必建立。

## 封裝現有應用程式的程序

封裝現有應用程式的程序會以下列步驟為基礎：

- 建立封裝目錄結構
- 新增應用程式的程式碼和組態檔
- 更新服務資訊清單檔
- 更新應用程式資訊清單

>[AZURE.NOTE]：我們提供可讓您自動建立 ApplicationPackage 的封裝工具。此工具目前為預覽狀態。您可以從[這裡](http://aka.ms/servicefabricpacktool)下載。

### 建立封裝目錄結構
您可以如上所述開始建立目錄結構。

### 新增應用程式的程式碼和組態檔
建立目錄結構之後，您可以在 code 和 config 目錄之下加入應用程式的程式碼和組態檔。您也可以在 code 和 config 目錄之下建立其他目錄或子目錄。Service Fabric 會進行應用程式根目錄內容的 xcopy，所以除了建立兩個最上層目錄 code 和 settings 以外，沒有預先定義的結構可使用 (但您可以選擇不同的名稱，下一節中有更多詳細資訊)。

>[AZURE.NOTE]請確定您包含應用程式需要的所有檔案/相依項目。Service Fabric 將會複製叢集中所有節點上的應用程式套件內容，而該叢集即將部署複製應用程式的服務。套件應包含執行應用程式需要的所有程式碼。不建議假設已經安裝相依項目。

### 編輯服務資訊清單檔
下一步就是編輯服務資訊清單檔以包含下列資訊：

- 服務類型的名稱。這是Service Fabric 用來識別服務的「識別碼」
- 用來啟動應用程式 (ExeHost) 的命令
- 必須執行才能安裝/設定應用程式 (SetupEntrypoint) 的任何指令碼

以下是 `ServiceManifest.xml` 的範例：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

我們來看看您需要更新的不同檔案部分：

### ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- 您可以挑選任何您要用於 `ServiceTypeName` 的名稱，此值在 `ApplicationManifest.xml` 中用來識別服務。
- 您必須指定 `UseImplicitHost="true"`。此屬性會告知 Service Fabric，此服務是以獨立式 (Self-Contained) 應用程式為基礎，所以只要將啟動它為程序並監視其健康狀況即可。

### CodePackage
CodePackage 指定服務程式碼的位置 (和版本)。

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` 元素用來在包含服務程式碼的應用程式封裝中指定目錄的名稱。`CodePackage` 也有 `version` 屬性，可用來指定程式碼的版本，而利用 Service Fabric 的 ALM 基礎結構，有可能用來升級服務的程式碼。
### SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntrypoint 用來指定任何應在服務的程式碼啟動前執行的可執行檔或批次檔。這是選擇性元素，所以如果不需要初始化/安裝程式則不需納入該元素。每次重新啟動服務時，就會執行 SetupEntrypoint。只有一個 SetupEntrypoint，所以如果應用程式的 setup/config 需要多個指令碼，則必須在單一批次檔上搭配 setup/config 指令碼。如同 Entrypoint 元素，SetupEntrypoint 可以執行任何類型的檔案：可執行檔、批次檔、PowerShell Cmdlet。在上述範例中，SetupEntrypoint 是以位於 Code 目錄的 `scripts` 子目錄中的批次檔 launchConfig.cmd 為基礎 (假設 WorkingDirectory 項目已設為 Code)。

### 進入點

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

服務資訊清單檔中的 `Entrypoint` 元素用來指定如何啟動服務。`ExeHost` 元素指定應用來啟動服務的可執行檔 (和引數)。

- `Program`：指定應執行才能啟動服務的可執行檔名稱。
- `Arguments`：指定應傳遞至可執行檔的引數。這可以是具有引數的參數清單。
- `WorkingFolder`：指定即將啟動之程序的工作目錄。您可以指定兩個值：
	- `CodeBase`：工作目錄將會設為應用程式封裝中的 Code 目錄 (如下所示結構中的 `Code` 目錄)
	- `CodePackage`：工作目錄將會設為應用程式封裝的根目錄 (`MyServicePkg`)
- `WorkingDirectory` 元素適合用來設定正確的工作目錄，以便應用程式或初始化指令碼使用相對路徑。

### 端點

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
`Endpoint` 元素指定應用程式可以接聽的端點。在此範例中，Node.js 應用程式會接聽連接埠 3000。

## 應用程式資訊清單檔案

設定 `servicemanifest.xml` 檔案之後，您需要對 `ApplicationManifest.xml` 檔進行一些變更，以確保使用正確的服務類型和名稱。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

### ServiceManifestImport

在 `ServiceManifestImport` 中，您可以指定要包含在應用程式中的一個或多項服務。`ServiceManifestName` 指定 `ServiceManifest.xml` 檔案所在的目錄名稱，用來參考服務。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### 設定記錄
對於現有的應用程式，最好能夠查看主控台記錄檔，以查明應用程式和設定指令碼是否沒有顯示任何錯誤。在 `ServiceManifest.xml` 檔案中，可使用 `ConsoleRedirection` 元素設定主控台重新導向。

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection` 可用來將主控台輸出 (stdout 和 stderr) 重新導向到工作目錄，以便用來驗證在 Service Fabric 叢集中設定或執行應用程式期間沒有發生任何錯誤。

	* `FileRetentionCount` 決定工作目錄中儲存多少個檔案。比方說，值為 5 表示工作目錄中會儲存先前 5 次執行的記錄檔。
	* `FileMaxSizeInKb` 指定記錄檔的大小上限。

記錄檔會儲存在服務的其中一個工作目錄上，以便判斷檔案的位置，您必須使用 Service Fabric 總管來判斷服務正在執行的節點以及目前使用的工作目錄。本文稍後會說明作法。

### Deployment
最後一個步驟是部署應用程式。下列 PowerShell 指令碼示範如何將應用程式部署至本機開發叢集，並啟動新的 Service Fabric 服務。

```Powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MulitpleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'Store\nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'Store\nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Service Fabric 服務可以各種「組態」部署，例如它可部署為單一或多個執行個體，也可以 Service Fabric 叢集的每個節點上有一個服務執行個體的方式部署。

`New-ServiceFabricService` Cmdlet 的 `InstanceCount` 參數用來指定應在 Service Fabric 叢集中啟動多少個服務執行個體。您可以根據要部署的應用程式類型來設定 `InstanceCount` 值。兩個最常見的情況為：* `InstanCount = "1"`：在此情況下，只會在叢集上部署一個服務執行個體。Service Fabric 的排程器會決定即將部署服務的節點。

* `InstanceCount ="-1"`：在此情況下，Service Fabric 叢集中的每個節點上會部署一個服務執行個體。最後的結果會是叢集中的每個節點有一個 (且僅只一個) 服務執行個體。這是很有用的前端應用程式 (例如 REST 端點) 組態，因為用戶端應用程式只需要「連線」到叢集中的任何節點即可使用端點。當 Service Fabric 叢集的所有節點都連線到負載平衡器時，也可使用此組態，以便將用戶端流量分散於在叢集中所有節點上執行的服務。

### 來查執行中的應用程式

在 Service Fabric 總管中，找出執行服務的節點。在此範例中，它是在 Node1 上執行：

![執行中應用程式](./media/service-fabric-deploy-existing-app/runningapplication.png)

如果您巡覽至節點並瀏覽至應用程式，您會看到基本節點資訊，包括它在磁碟上的位置。

![在磁碟上的位置](./media/service-fabric-deploy-existing-app/locationondisk.png)

如果您使用 [伺服器總管] 瀏覽至目錄，您可以找到工作目錄和服務的記錄檔資料夾，如下所示。

![在磁碟上的位置](./media/service-fabric-deploy-existing-app/loglocation.png)


## 後續步驟
在本文中，您已經學會如何封裝現有的應用程式並部署至 Service Fabric。接下來，您可以閱讀這個主題的其他內容。

- 在 [Github](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Custom/SimpleApplication) 上封裝和部署自訂應用程式的範例，包括封裝工具預先發行版本的連結。
- 了解如何[部署多個自訂應用程式](service-fabric-deploy-multiple-apps.md)。
- 如何開始[使用 Visual Studio 建立第一個 Service Fabric 應用程式](service-fabric-create-your-first-application-in-visual-studio.md)。

<!---HONumber=AcomDC_1125_2015-->