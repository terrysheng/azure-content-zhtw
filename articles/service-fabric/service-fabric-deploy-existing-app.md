<properties
   pageTitle="在 Azure Service Fabric 中部署現有的應用程式 | Microsoft Azure"
   description="逐步解說如何封裝現有的應用程式，使其可以部署在 Azure Service Fabric 叢集上"
   services="service-fabric"
   documentationCenter=".net"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2015"
   ms.author="claudioc"/>

# 在 Azure Service Fabric 中部署現有的應用程式

Azure Service Fabric 可用來部署對健康情況監視和應用程式生命週期管理 (ALM) 有益的現有應用程式。

本教學課程說明與取得現有應用程式並加以封裝以便部署至 Service Fabric 叢集有關的程序和基本概念。


## 應用程式和服務資訊清單檔的快速概觀

在深入了解部署現有應用程式的詳細資訊之前，了解 Service Fabric 部署模型會很有幫助。Service Fabric 部署模型主要依賴兩個檔案：


* **應用程式資訊清單**

  應用程式資訊清單用來描述應用程式，並列出組成該應用程式的服務，以及用來定義應如何部署服務的其他參數 (例如執行個體數目)。在 Service Fabric 世界中，應用程式是「可升級的單位」。應用程式可以升級為單一單位，其中潛在的失敗 (及潛在的回復) 由平台所管理，以保證升級程序完全成功，若是失敗，則不會讓應用程式處於不明/不穩定的狀態。

  這是應用程式資訊清單的範例：

  ```xml <?xml version="1.0" encoding="utf-8"?> <ApplicationManifest ApplicationTypeName="actor2Application" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="actor2Pkg" ServiceManifestVersion="1.0.0.0" />
      <ConfigOverrides />
    </ServiceManifestImport>

    <DefaultServices>
      <Service Name="actor2">
        <StatelessService ServiceTypeName="actor2Type">
          <SingletonPartition />
        </StatelessService>
      </Service>
    </DefaultServices>

  </ApplicationManifest> ```

* **服務資訊清單**

  服務資訊清單描述服務的元件。其中包含一些資料，例如服務的名稱和類型 (Service Fabric 用來管理服務的資訊)、其程式碼、組態和資料元件，加上一些在服務部署後可用來設定服務的額外參數。我們不會詳細說明服務資訊清單中所有可用的不同參數，但我們將會說明在 Service Fabric 上執行現有應用程式所需的子集

  這是服務資訊清單的範例

  ```xml <?xml version="1.0" encoding="utf-8"?> <ServiceManifest Name="actor2Pkg" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"> <ServiceTypes> <StatelessServiceType ServiceTypeName="actor2Type" /> </ServiceTypes>

    <CodePackage Name="Code" Version="1.0.0.0">
      <EntryPoint>
        <ExeHost>
          <Program>actor2.exe</Program>
        </ExeHost>
      </EntryPoint>
    </CodePackage>

    <ConfigPackage Name="Config" Version="1.0.0.0" />

    <Resources>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" />
      </Endpoints>
    </Resources>
  </ServiceManifest> ```

## 應用程式套件
為了使用 powershell Cmdlet (例如) 來部署應用程式，應用程式必須遵循預先定義的目錄結構。

```
\applicationmanifest.xml
\MyServicePkg
    \servicemanifest.xml
    \code
    \config
    \data
```

根目錄包含可定義應用程式的 applicationmanifest.xml 檔案。應用程式內含的每個服務的子目錄用來包含服務所需的所有成品：servicemanifest.xml 以及通常有 3 個目錄：

- *code*：包含服務程式碼
- *config*：包含服務可在執行階段存取的 settings.xml 檔案 (和其他必要檔案)，以擷取特定組態設定。
- *data*：額外的目錄，用來儲存服務可能需要的其他本機資料。注意：資料應用來只儲存短期資料，如果在容錯移轉期間必須重新定位服務，Service Fabric 不會將變更複製/複寫到資料目錄。

注意：您可以將任意目錄名稱使用於 Code、Congig 和 Data。只需確保在 ApplicationManifest 檔案中使用相同的值。

## 封裝現有應用程式的程序

封裝現有應用程式的程序會以下列步驟為基礎：

- 建立封裝目錄結構
- 新增應用程式的程式碼和組態檔
- 更新服務資訊清單檔
- 更新應用程式資訊清單


### 建立封裝目錄結構
您可以如上所述，從建立目錄結構開始。我建立了一個目錄，並從先前在 Visual Studio 中建立的現有專案複製應用程式和服務資訊清單。

![][1] ![][2]


### 新增應用程式的程式碼和組態檔
建立目錄結構之後，您可以在 Code 和 Config 目錄之下加入應用程式的程式碼和組態檔。您也可以在 Code 和 Config 目錄之下建立其他目錄或子目錄。Service Fabric 會進行應用程式根目錄內容的 xcopy，所以除了建立兩個最上層目錄 Code 和 Settings 以外，沒有預先定義的結構可使用 (但您可以選擇不同的名稱，下一節中有更多詳細資訊)。

>[AZURE.NOTE]請確定您包含應用程式需要的所有檔案/相依項目。Service Fabric 將會複製叢集中所有節點上的應用程式套件內容，而該叢集即將部署複製應用程式的服務。套件應包含執行應用程式需要的所有程式碼。不建議假設已經安裝相依項目。

### 編輯服務資訊清單檔
下一步就是編輯服務資訊清單檔以包含下列資訊：

- 服務類型的名稱。這是Service Fabric 用來識別服務的「識別碼」
- 用來啟動應用程式 (ExeHost) 的命令
- 必須執行才能安裝/設定應用程式 (SetupEntrypoint) 的任何指令碼

這是「封裝」node.js 應用程式的 `servicemanifest.xnml` 檔案範例：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VisualObjectsNodejsWebServicePkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
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

  <ConfigPackage Name="Config" Version="1.0.0.0"/>

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>

</ServiceManifest>
```

我們來看看您需要更新的不同檔案部分：

### ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
</ServiceTypes>
```

- 您可以挑選任何您要用於 `ServiceTypeName` 的名稱，此值在 `applicationmanifest.xml` 中用來識別服務。
- 您必須指定 `UserImplicitHost = "true"`。此屬性會告知 Service Fabric，此服務是以獨立式 (Self-Contained) 應用程式為基礎，所以只要將啟動它為程序並監視其健康狀況即可。

### CodePackage
CodePackage 指定服務程式碼的位置 (和版本)。

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` 元素用來在包含服務程式碼的應用程式套件中指定目錄的名稱。`CodePackage` 也有 `version` 屬性，可用來指定程式碼的版本，而利用 Service Fabric 的 ALM 基礎結構，有可能用來升級服務的程式碼。


### 進入點

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>server.js</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```


服務資訊清單檔中的 `Entrypoint` 項目用來指定如何啟動服務。`ExeHost` 項目會指定應用來啟動服務的可執行檔 (和引數)。

- `Program`：指定應執行才能啟動服務的可執行檔名稱。
- `Arguments`：指定應傳遞至可執行檔的引數。這可以是具有引數的參數清單。
- `WorkingFolder`：指定即將啟動之程序的工作目錄。您可以指定兩個值：
	- `CodeBase`：工作目錄將會設為應用程式套件中的 Code 目錄 (結構中的 `Code` 目錄如下所示)
	- `CodePackage`：工作目錄將會設為應用程式套件的根目錄 (`MyServicePkg`)
- `WorkingDirectory` 項目適合用來設定正確的工作目錄，以便應用程式或初始化指令碼使用相對路徑。

您還可以為 `WorkingFolder` 元素指定另一個值 (`Work`)，但這非常不適用於取出現有應用程式的案例。


```
\applicationmanifest.xml
\MyServicePkg
	\servicemanifest.xml
	\code
		 \bin
			  \ ...
	\config
	\data
		\...
```


#### 安裝程式進入點

```xml
<SetupEntryPoint>
  <ExeHost>
    <Program>scripts\myAppsetup.cmd</Program>
  </ExeHost>
</SetupEntryPoint>
```

`SetupEntrypoint` 用來指定任何應在服務的程式碼啟動前執行的可執行檔或批次檔。這是選擇性元素，所以如果不需要初始化/安裝程式則不需納入該元素。每次重新啟動服務時，就會執行進入點。只有一個 SetupEntrypoint，所以如果應用程式的 setup/config 需要多個指令碼，則必須在單一批次檔上搭配 setup/config 指令碼。如同 `Entrypoint` 元素，`SetupEntrypoint` 可以執行任何類型的檔案：可執行檔、批次檔、PowerShell Cmdlet。在上述範例中，`SetupEntrypoint` 是以位於 Code 目錄的 scripts 子目錄中的批次檔 myAppsetup.cmd 為基礎 (假設 `WorkingDirectory` 項目已設為 `Code`)。

## 應用程式資訊清單檔案

一旦設定了 `servicemanifest.xml` 檔案，您就需要對 `applicationmanifest.xml` 檔進行一些變更，以確保使用正確的服務類型和名稱。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
<DefaultServices>
  <Service Name="actor2">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount = "1">
    </StatelessService>
  </Service>
</DefaultServices>
```

### ServiceManifestImport

在 `ServiceManifestImport` 中，您可以指定要包含在應用程式中的一個或多項服務。系統會使用 `ServiceManifestName` 參考服務，其指定 `servicemanifest.xml` 檔案所在的目錄名稱。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### DefaultServices

應用程式資訊清單檔中的 `DefaultServices` 項目會用來定義某些服務屬性。

```xml
<DefaultServices>
  <Service Name="actor2">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
    </StatelessService>
  </Service>
</DefaultServices>
```

* `ServiceTypeName` 會做為服務的 'Id'。在移植現有應用程式的環境中，`ServiceTypeName` 必須可唯一識別您的服務。
* `StatelessService`：Service Fabric 支援兩種服務類型：無狀態與可設定狀態。在移植現有應用程式的情況下，服務會是無狀態服務，所以應一律使用 `StatelessService`。

Service Fabric 服務可以各種「組態」部署，例如它可部署為單一或多個執行個體，也可以 Service Fabric 叢集的每個節點上有一個服務執行個體的方式部署。在 `applicationmanifest.xml` 檔案中，您可以指定部署應用程式的方式。

* `InstanceCount`：用來指定應在 Service Fabric 叢集中啟動多少個服務執行個體。您可以根據要部署的應用程式類型來設定 `InstanceCount` 值。兩個最常見的案例包括：

	* `InstanceCount = "1"`：在此情況下，只會在叢集上部署一個服務執行個體。Service Fabric 的排程器會決定即將部署服務的節點。單一執行個體計數也適用於需要不同組態的應用程式 (如果它們在多個執行個體上執行）。在此情況下，會比較容易在相同的應用程式資訊清單檔中定義多個服務，並使用 `InstanceCount = "1"`。因此最後的結果會是相同服務有多個執行個體，但每個都有特定的組態。只有在目標是要有組態完全相同的多個執行個體時，大於 1 的 `InstanceCount` 值才有意義。

	* `InstanceCount ="-1"`：在此情況下，會在 Service Fabric 叢集中的每個節點上部署一個服務執行個體。最後的結果會是叢集中的每個節點有一個 (且僅只一個) 服務執行個體。這是很有用的前端應用程式 (例如 REST 端點) 組態，因為用戶端應用程式只需要「連線」到叢集中的任何節點即可使用端點。當 Service Fabric 叢集的所有節點都連線到負載平衡器時，也可使用此組態，以便將用戶端流量分散於在叢集中所有節點上執行的服務。


### 測試
這對於現有的應用程式非常實用，能夠查看主控台記錄檔，以找出應用程式和設定指令碼是否未顯示任何錯誤。使用 `ConsoleRedirection` 元素可在 `servicemanifest.xml` 檔案中設定主控台重新導向。

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>server.js</Arguments>
    <WorkingFolder></WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection` 可用來將主控台輸出 (stdout 和 stderr) 重新導向到工作目錄，以便用來驗證在 Service Fabric 叢集中設定或執行應用程式期間沒有發生任何錯誤。

	* `FileRetentionCount` 可決定工作目錄中會儲存多少個檔案。比方說，值為 5 表示工作目錄中會儲存先前 5 次執行的記錄檔。
	* `FileMaxSizeInKb` 可指定記錄檔的大小上限。

記錄檔會儲存在服務的其中一個工作目錄上，以便判斷檔案的位置，您必須使用 Service Fabric 總管來判斷服務正在執行的節點以及目前使用的工作目錄。

在 Service Fabric 總管中，找出服務執行所在的節點

![][3]

您知道服務目前執行所在的節點之後，即可找出使用的工作目錄

![][4]

當您選取服務的名稱時，您可以在右面板上看到服務程式碼和設定的儲存位置

![][5]

如果您按一下 [磁碟位置] 欄位中的連結，即可存取服務執行所在的目錄。

![][6]

記錄檔目錄包含所有記錄檔。

注意：這個範例顯示在叢集中執行單一服務執行個體的情況。如果有多個執行個體，您可能需要在服務執行所在的所有節點上檢查記錄檔。


## 後續步驟
我們正在設計一個只需指向現有應用程式的目錄結構根目錄，即可用來封裝該應用程式的工具。此工具負責產生資訊清單檔案，並設定在 Service Fabric 服務中「轉換」應用程式所需的基本設定。

如需關於如何開發傳統 Service Fabric 應用程式的詳細資訊，請參閱[這裡](service-fabric-develop-your-service-index.md)。

[1]: ./media/service-fabric-deploy-existing-app/directory-structure-1.png
[2]: ./media/service-fabric-deploy-existing-app/directory-structure-2.png
[3]: ./media/service-fabric-deploy-existing-app/service-node-1.png
[4]: ./media/service-fabric-deploy-existing-app/service-node-2.png
[5]: ./media/service-fabric-deploy-existing-app/service-node-3.png
[6]: ./media/service-fabric-deploy-existing-app/service-node-4.png

<!---HONumber=Nov15_HO2-->