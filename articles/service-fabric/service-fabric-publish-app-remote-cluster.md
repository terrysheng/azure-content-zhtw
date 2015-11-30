<properties
    pageTitle="使用 VS 將應用程式發佈至遠端叢集 |Microsoft Azure"
    description="深入了解使用 Visual Studio 將應用程式發佈至遠端 Service Fabric 叢集所需的步驟。"
    services="service-fabric"
    documentationCenter="na"
    authors="cawa"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="10/28/2015"
    ms.author="cawa" />

# 使用 Visual Studio 將應用程式發佈至遠端叢集

Visual Studio Service Fabric 工具提供簡單、可重複且可編寫指令碼的方式，將應用程式發佈至 Service Fabric 叢集。這是透過 PowerShell 部署指令碼與發佈設定檔完成。發佈設定檔是 .XML 格式檔案，儲存基本的發佈資訊。[發佈 Service Fabric 應用程式] 對話方塊也已加入，可讓使用者輕鬆地將應用程式發佈到本機或遠端叢集。透過 [發佈] 對話方塊進行的任何設定變更都會擷取至發佈設定檔。這可讓您在稍後的自動化程序中手動編輯發佈設定。

## 將應用程式發佈至 Service Fabric 叢集需要的構件

### Deploy-FabricApplication.ps1

這是 PowerShell 指令碼，使用發佈設定檔路徑做為參數，以發佈 Service Fabric 應用程式。

### 發佈設定檔

名為 **PublishProfiles** 的 Service Fabric 應用程式專案中的資料夾，包含 **Cloud.XML** 和 **Local.XML** 檔案。這些是「發佈設定檔」，儲存用以發佈應用程式的基本資訊，例如：- Service Fabric 叢集連線參數 - 應用程式參數檔案的路徑 - 升級設定

### 應用程式參數檔案

名為 **ApplicationParameters** 的 Service Fabric 應用程式專案中的資料夾，包含使用者指定應用程式資訊清單參數值的 XML 檔案。應用程式資訊清單檔案可以參數化，讓您可以針對部署設定使用不同的值。

例如，您可以變更資料分割計數以適合每個部署的不同環境。當您建立專案時，應用程式資訊清單中的設定，例如 **TargetReplicaSetSize** 和 **PartitionCount**，會轉換成參數。這些參數的預設值是在應用程式資訊清單檔案 (ApplicationManifest.XML，位於 Service Fabric 應用程式專案) 中的 [參數] 區段中指定。您新增至應用程式參數檔案的任何值會覆寫應用程式資訊清單檔案中的預設值。

>[AZURE.NOTE]對於動作項目服務，您應該先建置專案以在資訊清單檔案中產生參數。

以下是範例應用程式資訊清單檔案。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application2Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Actor1ActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="Actor1ActorService_MinReplicaSetSize" DefaultValue="2" />
      <Parameter Name="Actor1ActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Actor1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Actor1ActorService">
         <StatefulService ServiceTypeName="Actor1ActorServiceType" TargetReplicaSetSize="[Actor1ActorService_TargetReplicaSetSize]" MinReplicaSetSize="[Actor1ActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[Actor1ActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## 使用 [發佈 Service Fabric 應用程式] 對話方塊，將應用程式發佈至 Service Fabric 叢集

下列步驟示範如何使用 Visual Studio Service Fabric 工具提供的 [發佈 Service Fabric 應用程式] 對話方塊，發佈應用程式。

1. 在 Service Fabric 應用程式專案的捷徑功能表上，選擇 [發佈…] 以檢視 [發佈 Service Fabric 應用程式] 對話方塊。

    ![][0]

    [目標設定檔] 下拉式清單中選取的檔案是儲存 [資訊清單版本] 以外所有設定的位置。您可以重複使用現有設定檔，或藉由選擇 [目標設定檔] 下拉式清單方塊中的 [<管理設定檔…>] 來建立一個新的設定檔。當您選擇發佈設定檔時，其內容會出現在對話方塊的對應欄位中。若要隨時儲存變更，請選擇 [儲存設定檔] 連結。

1. [連接端點] 區段可讓您指定本機或遠端 Service Fabric 叢集的發佈端點。若要新增或變更連接端點，請選擇 [選取...] 按鈕。[選取 Service Fabric 叢集] 對話方塊會顯示您可以對其發佈的可用 Service Fabric 叢集連接端點。(如果您尚未登入 Azure 訂用帳戶，系統會提示您登入。) 選擇連接端點。

    ![][1]

    一旦您選擇端點，Visual Studio 會驗證與選取的 Service Fabric 叢集的連線。如果叢集未受到保護，Visual Studio 可以立即與其連線。不過，如果叢集受到保護，您必須在本機電腦上安裝憑證才能繼續。如需詳細資訊，請參閱[如何設定安全連接](service-fabric-visualstudio-configure-secure-connections.md)。完成時，選擇 [確定] 按鈕。選取的叢集會出現在 [發佈 Service Fabric 應用程式] 對話方塊。

1. [應用程式參數檔案] 下拉式清單方塊可讓您瀏覽至應用程式參數檔案。應用程式參數檔案會保留應用程式資訊清單檔案中參數的使用者指定值。若要新增或變更參數，請選擇 [編輯] 按鈕。在 [參數] 方格中輸入或變更參數值。完成時，請選擇 [儲存] 按鈕。

    ![][2]

1. [升級應用程式] 核取方塊可讓您指定此發佈動作是否為升級。升級發佈動作不同於一般發佈動作。如需差異清單，請參閱 [Service Fabric 應用程式升級](service-fabric-application-upgrade.md)。若要設定升級設定，請選擇 [設定升級設定] 連結。升級參數編輯器隨即出現。若要深入了解升級參數，請參閱[設定 Service Fabric 應用程式的升級](service-fabric-visualstudio-configure-upgrade.md)。

1. 選擇 [資訊清單版本...] 按鈕以檢視 [編輯版本] 對話方塊。您需要更新應用程式和服務版本才能進行升級。請參閱 [Service Fabric 應用程式升級教學課程](service-fabric-application-upgrade-tutorial.md)，以深入了解應用程式和服務資訊清單版本如何影響升級程序。

    ![][3]

    如果應用程式和服務版本使用例如 1.0.0 的語意版本設定，或格式為 1.0.0.0 的數值，請選取 [自動更新應用程式和服務版本] 選項。當您選擇這個選項時，服務和應用程式版本號碼會在程式碼、組態中或資料封裝版本更新時自動更新。如果您想要以手動方式編輯版本，請清除核取方塊以停用此功能。

    >[AZURE.NOTE]對於針對動作項目專案出現的所有封裝項目，首先建置專案來產生服務資訊清單檔案中的項目。

1. 當您完成指定所有必要的設定，選擇 [發佈] 按鈕以將應用程式發佈至所選的 Service Fabric 叢集。您指定的設定會套用至發佈程序。

## 後續步驟

若要深入了解如何在連續整合環境中自動化發佈程序，請參閱 [Service Fabric 設定連續整合](service-fabric-set-up-continuous-integration.md)。


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png

<!---HONumber=Nov15_HO4-->