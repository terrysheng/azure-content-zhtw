<properties
   pageTitle="指定 Service Fabric 服務端點 | Microsoft Azure"
   description="如何在服務資訊清單中描述端點資源，包括如何設定 HTTPS 端點"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/26/2015"
   ms.author="sumukhs"/>

# 在服務資訊清單中指定資源

## 概觀

服務資訊清單可宣告/變更服務使用的資源，且不需變更已編譯的程式碼。Azure Service Fabric 支援針對服務的端點資源組態。透過應用程式資訊清單中的 SecurityGroup，即可控制存取服務資訊清單中的指定資源。資源宣告可讓您在部署階段變更這些資源，也就是服務不需要導入新的組態機制。

## 端點

如果沒有指定明確通訊埠 (例如，請查看下列 ServiceEndpoint1 端點)，在服務資訊清單中定義端點資源時，Service Fabric 會從保留的應用程式連接埠範圍指派連接埠。此外，服務也可以在資源中要求特定連接埠。不同的連接埠號碼可以指派給在不同叢集節點上執行的服務複本，而在同一節點上執行的相同服務複本也可以共用同一個連接埠。服務複本可將此類連接埠用於各種用途，例如複寫、接聽用戶端要求等。

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

請參閱[設定具狀態的 Reliable Services](../Service-Fabric/service-fabric-reliable-services-configuration.md)，從設定封裝設定檔 (settings.xml) 深入了解參考端點。

## 範例：指定服務的 HTTP 端點

以下服務資訊清單在 &lt;Resources&gt; 項目中定義了一個 TCP 端點資源和兩個 HTTP 端點資源。

Service Fabric 會自動將 HTTP 端點處理為 ACL。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## 範例：指定服務的 HTTPS 端點

HTTPS 通訊協定提供伺服器驗證，也能用於加密用戶端-伺服器通訊。為了在 Service Fabric 服務上啟用此通訊協定，在您定義服務時，請在服務資訊清單的 [資源] -> [端點] -> [端點] 區段指定通訊協定，如先前針對 ServiceEndpoint3 端點所示。

>[AZURE.NOTE]因為這會是一項重大變更，所以服務的通訊協定無法在應用程式升級期間變更。


以下是您必須為 HTTPS 設定的範例 ApplicationManifest。(您必須提供指紋給憑證。) EndpointRef 是在您設定 HTTPS 通訊協定的 ServiceManifest 中 EndpointResource 的參考。您可以加入一個以上的 Endpointcertificate。

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

<!---HONumber=AcomDC_0121_2016-->