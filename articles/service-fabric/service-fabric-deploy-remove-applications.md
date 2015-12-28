<properties
   pageTitle="Service Fabric 應用程式部署 | Microsoft Azure"
   description="如何部署和移除 Service Fabric 中的應用程式"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="12/10/2015"
   ms.author="seanmck"/>

# 部署應用程式

[應用程式類型封裝][10]後，隨即準備好部署至 Azure Service Fabric 叢集。部署涉及下列三個步驟：

1. 上傳應用程式封裝
2. 註冊應用程式類型
3. 建立應用程式執行個體

>[AZURE.NOTE]如果您使用 Visual Studio 部署和偵錯您本機開發叢集上的應用程式，則下面所述的所有步驟都會透過應用程式專案的 [指令碼] 資料夾中的 PowerShell 指令碼自動處理。本文章提供背景執行這些指令碼的說明，以便您可以在 Visual Studio 之外執行相同的作業。

## 上傳應用程式封裝

上傳應用程式封裝會將它放在一個可由內部 Service Fabric 元件存取的位置。您可以使用 PowerShell 執行上傳。執行本文中的任何 PowerShell 命令之前，請一律透過使用 **Connect-ServiceFabricCluster** 連接至 Service Fabric 叢集的方式啟動。

假設您有一個名為 [*MyApplicationType*] 的資料夾，其中包含必要的應用程式資訊清單、服務資訊清單，以及程式碼/組態/資料封裝。**Copy-ServiceFabricApplicationPackage** 命令會上傳封裝。例如：

~~~
PS D:\temp> dir

    Directory: D:\temp

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----         3/19/2015   8:11 PM            MyApplicationType

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Copy-ServiceFabricApplicationPackage MyApplicationType
Copy application package succeeded

PS D:\temp>
~~~

## 註冊應用程式封裝

註冊應用程式套件，讓應用程式類型和應用程式資訊清單中宣告的版本可供使用。系統會讀取在上一個步驟中所上傳的封裝，請確認封裝 (相當於本機執行 **Test-ServiceFabricApplicationPackage**)、處理封裝內容，然後將處理過的封裝複製至內部系統位置。

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

**Register-ServiceFabricApplicationType** 命令只有在系統成功複製應用程式封裝之後才會傳回。此作業所需時間取決於應用程式封裝的內容。如有需要，**-TimeoutSec** 參數可用來提供較長的逾時時間。(預設逾時值為 60 秒。)

**Get-ServiceFabricApplicationType** 命令會列出所有成功註冊的應用程式類型版本。

## 建立應用程式

您可以使用任何已透過 **New-ServiceFabricApplication** 命令成功註冊的應用程式類型版本具現化應用程式。每個應用程式名稱的開頭必須為 *fabric:* 配置，並且是每個應用程式執行個體的唯一名稱。如果已在目標應用程式類型的應用程式資訊清單中定義預設服務，則這些服務也會一併建立。

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

**Get-ServiceFabricApplication** 命令會列出所有已成功建立的應用程式執行個體及其整體狀態。

**Get-ServiceFabricService** 命令會列出所有已指定應用程式執行個體內成功建立的服務執行個體。此處會列出預設的服務 (若有)。

多個應用程式執行個體可以針對任何指定的已註冊應用程式類型版本來建立。每個應用程式執行個體將在隔離狀態下執行，包含本身的工作目錄和程序。

## 移除應用程式

當不再需要應用程式執行個體時，您可以使用 **Remove-ServiceFabricApplication** 命令將其永久移除。此命令會自動移除屬於應用程式的所有服務，以及永久移除所有服務狀態。此作業無法回復，且應用程式狀態無法復原。

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

當不再需要應用程式類型的特定版本時，請使用 **Unregister-ServiceFabricApplicationType** 命令將該版本取消註冊。取消註冊未使用類型會釋放映像存放區上該類型應用程式封裝內容所使用的儲存空間。只要沒有對應的應用程式針對應用程式類型進行具現化，且沒有擱置中的應用程式升級進行參考該類性，便可取消註冊該應用程式類型。

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

<!--
## Next steps

TODO [Upgrade applications][11]
-->

## 疑難排解

### Copy-ServiceFabricApplicationPackage 要求 ImageStoreConnectionString

Service Fabric SDK 環境應已正確設定預設值。但若有需要，所有命令的 ImageStoreConnectionString都應符合 Service Fabric 叢集正在使用的值。您可以在透過 **Get-ServiceFabricClusterManifest** 命令擷取的叢集資訊清單中找到此值：

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## 後續步驟

[Service Fabric 應用程式升級](service-fabric-application-upgrade.md)

[Service Fabric 健康狀態簡介](service-fabric-health-introduction.md)

[診斷和疑難排解 Service Fabric 服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[在 Service Fabric 中模型化應用程式](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md

<!---HONumber=AcomDC_1217_2015-->