<properties
   pageTitle="管理 Service Fabric 中的多個環境 |Microsoft Azure"
   description="Service Fabric 應用程式可以在任意大小 (從一部電腦至數千部電腦) 的叢集上執行。在某些情況下，您會想要針對各種環境以不同的方式設定應用程式。本文說明如何定義每個環境的不同應用程式參數。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/08/2016"
   ms.author="seanmck"/>

# 管理多個環境的應用程式參數

您可以使用任意數量的電腦 (從一至數千部) 來建立 Service Fabric 叢集。雖然不需針對各種環境進行修改，即可執行應用程式二進位檔，但您通常會視您要部署的機器數目，以不同的方式設定應用程式。

簡單來說，請考慮無狀態服務的 `InstanceCount`。當您在 Azure 中執行應用程式時，您通常要將此參數設定為特殊值 "-1"。這可確保您的服務在叢集中的每個節點上執行。不過，此設定不適用於單一電腦叢集，因為您不能有多個在單一電腦的相同端點上接聽的程序。然而，您通常會將 `InstanceCount` 設定為 "1"。

## 指定環境特有的參數

此設定問題的解決方案是一組參數化預設服務和應用程式參數檔案，其中會填入指定之環境的參數值。

### 預設服務

Service Fabric 應用程式是由服務執行個體集合所組成。雖然您可以先建立一個空的應用程式，然後再動態建立所有的服務執行個體，但是大部分的應用程式都有一組應一律在應用程式具現化時建立的核心服務。這些稱為「預設服務」。其在應用程式資訊清單中指定，而方括號中包含每個環境組態的預留位置：

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

必須在應用程式資訊清單的 [參數] 元素中定義每個具名參數：

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

DefaultValue 屬性指定當指定的環境缺少更特定的參數時所要使用的值。

>[AZURE.NOTE] 並非所有的服務執行個體參數都適用於每個環境組態。在上述範例中，已針對服務的所有執行個體明確定義服務資料分割配置的 LowKey 和 HighKey 值，因為資料分割範圍是資料網域 (而不是環境) 的函數。


### 每個環境的服務組態設定

[Service Fabric 應用程式模型](service-fabric-application-model.md)可讓服務包含組態封裝，內含可在執行階段讀取的自訂關鍵值組。在應用程式資訊清單中指定 `ConfigOverride`，也可依照環境區分這些設定的值。

假設您在 `Stateful1` 服務的 Config\\Settings.xml 檔案中有下列設定：


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

若要覆寫特定應用程式/環境組的這個值，請在應用程式資訊清單中匯入服務資訊清單時建立 `ConfigOverride`。

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

接著可如上所示，依照環境設定此參數。在應用程式資訊清單的參數區段中加以宣告，並在應用程式參數檔案中指定環境特有的值，即可執行這項操作。

>[AZURE.NOTE] 在服務組態設定的情況下，有三個地方可以設定索引鍵的值：服務組態封裝、應用程式資訊清單和應用程式參數檔案。Service Fabric 將一律先從應用程式參數檔案 (若已指定) 進行選擇，然後從應用程式資訊清單選擇，最後再從組態封裝選擇。


### 應用程式參數檔案

Service Fabric 應用程式專案可以包含一或多個應用程式參數檔案。每個檔案會為應用程式資訊清單中定義的參數定義特定值：

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

根據預設，新的應用程式包含兩個應用程式參數檔案 (名為 Local.xml 和 Cloud.xml)：

![方案總管中的應用程式參數檔案][app-parameters-solution-explorer]

若要建立新的參數檔案，只需複製並貼上現有的參數檔案並為它提供新名稱。

## 在部署期間識別環境特有的參數

在部署階段，您需選擇要套用於您的應用程式的適當參數檔案。您可以透過 Visual Studio 中的 [發佈] 對話方塊或透過 PowerShell 進行。

### 從 Visual Studio 部署

您可以在 Visual Studio 中發佈應用程式時，從可用的參數檔案清單進行選擇。

![在 [發佈] 對話方塊中選擇參數檔案][publishdialog]

### 從 PowerShell 部署

`DeployCreate-FabricApplication.ps1` PowerShell 指令碼接受以參數檔案做為參數。

    ./DeployCreate-FabricApplication -ApplicationPackagePath <app_package_path> -ApplicationDefinitionFilePath <app_instance_definition_path>

## 後續步驟

若要深入了解本主題中討論的一些核心概念，請參閱 [Service Fabric 技術概觀](service-fabric-technical-overview.md)。如需 Visual Studio 中其他可用的應用程式管理功能的相關資訊，請參閱[在 Visual Studio 中管理 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)。

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]: ./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png

<!---HONumber=AcomDC_0309_2016-->