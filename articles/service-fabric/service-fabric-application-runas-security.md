<properties
   pageTitle="了解 Service Fabric 應用程式 RunAs 安全性原則 |Microsoft Azure"
   description="如何以系統和本機安全性帳戶執行 Service Fabric 應用程式的概觀，包括應用程式在啟動前需要執行某些特殊權限動作的 SetupEntry 點"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/24/2015"
   ms.author="mfussell"/>

# RunAs：使用不同的安全性權限執行 Service Fabric 應用程式
Azure Service Fabric 能夠保護叢集中以不同使用者帳戶 (稱為 **RunAs**) 執行的應用程式。Service Fabric 也會利用此使用者帳戶來保護應用程式所使用的資源，例如檔案、目錄和憑證。

根據預設，Service Fabric 應用程式會在用以執行 Fabric.exe 程序的帳戶之下執行。Service Fabric 也能夠以本機使用者帳戶 (指定於應用程式的資訊清單內) 執行應用程式。RunAs 支援的帳戶類型為 **LocalUser**、**NetworkService**、**LocalService** 和 **LocalSystem**。

> [AZURE.NOTE]可使用 Azure Active Directory 的 Windows Server 部署支援網域帳戶。

您可以定義和建立使用者群組，以便將一或多個使用者新增至每個群組一起管理。當不同的服務進入點有多個使用者，而且他們需要具備可在群組層級取得的某些常見權限時，這會特別有用。

## 設定 SetupEntryPoint 的 RunAs 原則

如[應用程式模型](service-fabric-application-model.md)所示，**SetupEntryPoint** 是以與 Service Fabric 相同的認證執行的特殊權限進入點 (通常為「網路」帳戶)，優先於任何其他進入點。**EntryPoint** 指定的可執行檔通常是長時間執行的服務主機，因此，擁有個別的安裝進入點，可避免以高權限執行服務主機執行檔延長的期間。**EntryPoint** 指定的可執行檔是在 **SetupEntryPoint** 成功結束之後執行。如果曾經終止或當機，產生的程序會監視並重新啟動 (以 **SetupEntryPoint** 再次開始)。

以下簡單的服務資訊清單範例會顯示服務的 SetupEntryPoint 和主要的 EntryPoint。

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### 設定 RunAs 原則

在您設定服務以擁有安裝進入點之後，就能在應用程式資訊清單中變更用以執行的安全性權限。下列範例示範如何將服務設定成以系統管理員帳戶權限執行。

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

首先，以使用者名稱 (例如 SetupAdminUser) 建立 **Principals** 區段。這表示使用者是 Administrators 系統群組的成員。

接著在 **ServiceManifestImport** 區段下方設定原則，以將此主體套用至 **SetupEntryPoint**。這會告知 Service Fabric，當 **MySetup.bat** 檔案執行時，它應該是具有系統管理員權限的 RunAs。假設您尚未將原則套用至主要進入點，則 **MyServiceHost.exe** 中的程式碼將會以系統 **NetworkService** 帳戶執行。這是用來執行所有服務進入點的預設帳戶。

現在讓我們將 MySetup.bat 檔案加入 Visual Studio 專案，以測試系統管理員權限。在 Visual Studio 中，以滑鼠右鍵按一下服務專案，並加入名為 MySetup.bat 的新檔案。

接下來，確定 MySetup.bat 檔案已包含於服務封裝中。預設不會包含該檔案。選取該檔案、以滑鼠右鍵按一下操作功能表，然後選擇 [屬性]。在 [屬性] 對話方塊中，確定已將 [複製到輸出目錄] 設為 [有更新時才複製]。如以下螢幕擷取畫面所示。

![Visual Studio CopyToOutput for SetupEntryPoint 批次檔][image1]

現在開啟 MySetup.bat 檔案並加入下列命令：

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > test.txt
echo %TestVariable% >> test.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

接下來，建置解決方案並部署至本機開發叢集。一旦啟動服務之後 (如 Service Fabric 總管中所示)，您就可以看到 MySetup.bat 成功的方式有兩種。開啟 PowerShell 命令提示字元並輸入：

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

接著，記下已在 Service Fabric 總管中部署並啟動服務的節點名稱，例如節點 1。接下來，瀏覽至應用程式執行個體工作資料夾，以尋找顯示 **TestVariable** 之值的 out.txt 檔案。例如，如果此服務已部署至節點 2，則您可以移至 **MyApplicationType** 的這個路徑：

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

##  從 SetupEntryPoint 啟動 PowerShell 命令
若要從 **SetupEntryPoint** 點執行 PowerShell，您可以在指向 PowerShell 檔案的批次檔中執行 **PowerShell.exe**。首先，將 PowerShell 檔案加入服務專案 (例如 **MySetup.ps1**)。請記得設定 [有更新時才複製] 屬性，讓這個檔案也會包含於服務封裝內。下列範例顯示的範例批次檔可啟動名為 MySetup.ps1 的 PowerShell 檔案，以設定名為 **TestVariable** 的系統環境變數。

MySetup.bat 可啟動 PowerShell 檔案。

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

在 PowerShell 檔案中，加入以下項目來設定系統環境變數：

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

## 將 RunAs 原則套用到服務
在上述步驟中，您看到了如何將 RunAs 原則套用到 SetupEntryPoint。讓我們深入了解如何建立可當作服務原則套用的不同主體。

### 建立本機使用者群組
您可以定義和建立使用者群組，然後將一或多個使用者新增至群組。如果不同的服務進入點有多個使用者，而且他們需要有可在群組層級取得的某些常見權限，這是特別有用。下列範例顯示名為 **LocalAdminGroup** 且具有系統管理員權限的本機群組。Customer1 和 Customer2 這兩個使用者會成為此本機群組的成員。

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### 建立本機使用者
您可以建立一個本機使用者，以便用來保護應用程式內的服務。在應用程式資訊清單的主體區段中指定 **LocalUser** 帳戶類型時，Service Fabric 會在應用程式部署所在的電腦上建立本機使用者帳戶。根據預設，這些帳戶的名稱不會與應用程式資訊清單中所指定的名稱一樣 (例如，下列範例中的 Customer3)。相反地，它們會動態產生並具有隨機密碼。

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

## 將原則指派給服務程式碼封裝
**ServiceManifestImport** 的 **RunAsPolicy** 區段會指定主體區段中應用來執行程式碼封裝的帳戶。它也會將服務資訊清單中的程式碼封裝關聯至主體區段中的使用者帳戶。您可以為安裝或主要進入點指定此原則，或指定 [全部] 以將其套用到兩者。下列範例顯示套用不同的原則：

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

如果未指定 **EntryPointType**，則預設值會設定為 EntryPointType ="Main"。當您想要以系統帳戶執行某些高權限設定作業時，指定 **SetupEntryPoint** 特別有用。實際的服務程式碼可利用較低權限的帳戶來執行。

### 將預設原則套用到所有的服務程式碼封裝
**DefaultRunAsPolicy** 區段是用來針對未定義特定 **RunAsPolicy** 的所有程式碼封裝，指定預設的使用者帳戶。如果在應用程式所用的服務資訊清單中指定的大部分程式碼封裝必須以相同的 RunAs 使用者執行，則應用程式可以只定義該使用者帳戶的預設 RunAs 原則，而不需針對每個程式碼封裝指定 **RunAsPolicy**。下列範例指定如果程式碼封裝未指定 **RunAsPolicy**，則此程式碼封裝應該以主體區段中指定的 **MyDefaultAccount** 執行。

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~

## 為 HTTP 和 HTTPS 端點指派 SecurityAccessPolicy
如果您將 RunAs 原則套用到服務，而服務資訊清單宣告具有 HTTP 通訊協定的端點資源，您必須指定 **SecurityAccessPolicy**，以確保配置給這些端點的連接埠，都已針對用以執行服務的 RunAs 使用者帳戶正確列入存取控制清單中。否則，**http.sys** 無法存取服務，而您從用戶端呼叫時將會失敗。下列範例會將 Customer3 帳戶套用到名為 **ServiceEndpointName** 的端點，並給予完整的存取權限。

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

針對 HTTPS 端點，您也必須指出要傳回給用戶端的憑證名稱。您可以使用 **EndpointBindingPolicy** 搭配應用程式資訊清單之憑證區段中定義的憑證，來執行此動作。

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## 完整的應用程式資訊清單範例
下列應用程式資訊清單顯示上述許多不同的設定：

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
	 <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

* [了解應用程式模型](service-fabric-application-model.md)
* [在服務資訊清單中指定資源](service-fabric-service-manifest-resources.md)
* [部署應用程式](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

<!---HONumber=AcomDC_0107_2016-->