<properties urlDisplayName="Autoscaling" pageTitle="使用自動調整應用程式區塊 (.NET) - Azure" metaKeywords="Azure autoscaling, Azure autoscaling C#, Azure autoscaling .NET" description="Learn how to use the Autoscaling Application for Azure. Code samples are written in C# and use the .NET API." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="How to Use the Autoscaling Application Block" authors="rasquill" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/14/2014" ms.author="rasquill" />







# 如何使用自動調整應用程式區塊

本指南將示範如何從 [Microsoft Enterprise Library 5.0 Integration Pack for Azure][] 使用自動調整應用程式區塊執行一般案例。這些範例均以 C\# 撰寫並使用 .NET API。涵蓋的案例包括**主控區塊**、**使用限制規則**和**使用重新啟動規則**。如需自動調整應用程式區塊的詳細資訊，請參閱[後續步驟][]一節。

## 目錄

[何謂自動調整應用程式區塊？][]   
 [概念][]   
 [從目標 Azure 應用程式收集效能計數器資料][]   
 [設定自動調整應用程式區塊的主機應用程式][]   
 [作法：具現化和執行自動調整器][] [作法：定義您的服務模型][]   
 [作法：定義您的自動調整規則][]   
 [作法：設定自動調整應用程式區塊][]   
 [後續步驟][]

## <a id="WhatIs"> </a>何謂自動調整應用程式區塊？

自動調整應用程式區塊可以根據您特別針對應用程式所定義的規則，自動調整 Windows Azure 應用程式。您可以使用這些規則，來協助 Azure 應用程式維護其輸送量，以回應其工作負載中變更，同時控制與主控 Azure 中的應用程式相關聯的成本。除了增加或減少應用程式中的角色執行個體數目來進行調整外，區塊還可讓您使用其他調整動作，例如節流應用程式內的特定功能，或使用自定的動作。

您可以選擇主控 Azure 角色或內部部署應用程式中的區塊。 
自動調整應用程式區塊是 [Microsoft Enterprise Library 5.0 Integration Pack for Azure][] 的一部分。

## <a id="Concepts"> </a>概念

在下圖中，綠色線顯示兩天內 Azure 角色的執行中執行個體數目的繪圖。執行個體數目會在一段時間後自動變更，以回應一組自動調整規則。

![diagram of sample autoscaling](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling01.png)

區塊會使用兩種規則，來定義應用程式的自動調整行為：

-   **限制規則：**若要設定執行個體數目的上限和下限，比方說，在每個早上 8:00 與 10:00 之間，您想要最少四個，最多八個執行個體，則可以使用**限制規則**。在圖解中，紅色線和藍色線代表限制規則。例如，在圖解中的點 **A**，角色執行個體的數目下限從二升高至四，以便可在此時容納預期增加的應用程式工作負載。在圖解中的點 **B**，避免角色執行個體的數目上升超過五，以便可以控制應用程式的執行成本。

-   **重新啟動規則：** 若要讓角色執行個體的數目可以變更，以回應需求中不可預期的變更，請使用**重新啟動規則**。在圖解的點 **C**，區塊會自動將角色執行個體的數目從四減少至三，以回應工作負載的減少。在點 **D**，區塊偵測到工作負載增加，並自動將執行中的角色執行個體數目從三增加至四。

區塊會將其組態設定儲存在兩個存放區中：

-   **規則存放區：**規則存放區會為您保存商業組態；您已對 Azure 應用程式定義的所有自動調整規則的清單。此存放區通常為 XML 檔案，位於自動調整應用程式區塊可以讀取它的位置，例如，位於 Azure Blob 儲存體或位於本機檔案。

-   **服務資訊存放區：**服務資訊存放區會存放您的作業組態，也就是 Azure 應用程式的服務模型。此服務模型包括 Azure 應用程式的所有相關資訊 (例如名稱和儲存體帳戶詳細資料)，區塊需要這些資訊，才能從目標 Azure 應用程式收集資料點，並執行調整作業。

## <a id="PerfCounter"> </a>從目標 Azure 應用程式收集效能計數器資料

重新啟動規則可以使用角色中的效能計數器資料，作為規則定義的一部分。例如，重新啟動規則可能監視 Azure 角色的 CPU 使用率，以判定區塊是否應該初始化調整作業。區塊會從 Azure 儲存體中名稱為 **WADPerformanceCountersTable** 的 Azure 診斷資料表讀取效能計數器資料。

依預設，Azure 不會將效能計數器資料寫入 Azure 儲存體中的 Azure 診斷資料表。因此，您應該修改需要從中收集效能計數器資料的角色，以儲存此資料。如需如何在應用程式中啟用效能計數器的詳細資料，請參閱[在 Azure 中使用效能計數器][]。

## <a id="CreateHost"> </a>設定自動調整應用程式區塊的主機應用程式

您可以利用 Azure 角色或內部部署應用程式主控自動調整應用程式區塊。通常，從您要自動調整的目標應用程式中，利用個別應用程式主控自動調整應用程式區塊。本節提供如何設定主機應用程式的指導方針。

### 取得自動調整應用程式區塊 NuGet 套件

可在 Visual Studio 專案中使用自動調整應用程式區塊前，您將需要取得自動調整應用程式區塊二進位檔，並在專案新增中它們的參考。NuGet Visual Studio 擴充功能可讓您輕易地安裝和更新 Visual Studio 和 Visual Web Developer 中的程式庫和工具。自動調整應用程式區塊 NuGet 套件是取得自動調整應用程式區塊 API 的最簡單方法。如需 **NuGet** 的詳細資訊，以及如何安裝和使用 **NuGet** Visual Studio 擴充功能的詳細資訊，請參閱 [NuGet][] 網站。

一旦安裝了 NuGet Package Manager，若要在應用程式中安裝自動調整 NuGet 套件，請執行下列動作：

1.  開啟 [**NuGet Package Manager 主控台**] 視窗。在 [**工具**] 功能表中，選取 [**Library Package Manager**]，然後選取 [**Package Manager Console**]。

2.  在 NuGet Package Manager 主控台視窗中輸入下列命令：

        PM> Install-Package EnterpriseLibrary.WindowsAzure.Autoscaling

安裝 NuGet 更新，即可利用您使用自動調整應用程式區塊時所需的所有必要組件和參考來更新專案。您的專案現在包括 XML 結構描述檔案，用於自動調整規則定義和自動調整服務資訊。專案現在也包括 readme 檔案，其中包含自動調整應用程式區塊的重要資訊：

![files configured by autoscaling NuGet package](./media/cloud-services-dotnet-autoscaling-application-block/auotscaling02.png)


### 將目標 Framework 設定為 .NET Framework 4

您的專案必須以 .NET Framework 4 為目標。若要變更或驗證目標架構：

1.  在 [方案總管] 中，於專案名稱上按一下滑鼠右鍵，然後選取 [**內容**]。

2.  在內容視窗的 [**應用程式** 索引標籤中，確定目標架構設為 [**.NET Framework 4**]。

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling03.png)


### 新增命名空間參考

將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取自動調整應用程式區塊之任何 C\# 檔案內的頂端：

    using Microsoft.Practices.EnterpriseLibrary.Common.Configuration;
    using Microsoft.Practices.EnterpriseLibrary.WindowsAzure.Autoscaling;

## <a id="Instantiate"> </a>作法：具現化和執行自動調整器

使用 **IServiceLocator.GetInstance** 方法來具現化
Autoscaler，然後呼叫 **Autoscaler.Start** 方法來執行
**Autoscaler**。

    Autoscaler scaler =
        EnterpriseLibraryContainer.Current.GetInstance<Autoscaler>();
    scaler.Start();

## <a id="DefineServiceModel"> </a>作法：定義您的服務模型

通常，您會將服務模型 (Windows Azure 環境中的描述，包括訂閱、代管服務、角色和儲存體帳戶的相關資訊) 儲存在 XML 檔案中。您可在專案的 **AutoscalingServiceModel.xsd** 檔案中找到此 XML 檔案的結構描述複本。在 Visual Studio 中，當您編輯服務模型 XML 檔案時，此結構描述會提供 Intellisense 和驗證。

在專案中建立一個稱為 **services.xml** 的新 XML 檔案。

在 Visual Studio 中，您必須確定服務模型檔案複製
至輸出資料夾。作法：

1.  在檔案上按一下滑鼠右鍵，然後選取 [**內容**]。

2.  在 [內容] 窗格內，設定 [**複製到輸出目錄**]
    值為 [**永遠複製**]。

    ![Set Copy to Output Directory value](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling04.png)


	下列程式碼範例顯示 **services.xml** 檔案中的範例服務模型：

    <?xml version="1.0" encoding="utf-8" ?>
    <serviceModel xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/serviceModel">
      <subscriptions>
        <subscription name="[subscriptionname]"
                      certificateThumbprint="[managementcertificatethumbprint]"
                      subscriptionId="[subscriptionid]"
                      certificateStoreLocation="CurrentUser"
                      certificateStoreName="My">
          <services>
            <service dnsPrefix="[hostedservicednsprefix]" slot="Staging">
              <roles>
                <role alias="AutoscalingApplicationRole"
                      roleName="[targetrolename]"
                      wadStorageAccountName="targetstorage"/>
              </roles>
            </service>
          </services>
          <storageAccounts>
            <storageAccount alias="targetstorage"
              connectionString="DefaultEndpointsProtocol=https;AccountName=[storageaccountname];AccountKey=[storageaccountkey]">
            </storageAccount>
          </storageAccounts>
        </subscription>
      </subscriptions>
    </serviceModel>

您必須將方括弧中的值取代為
環境和目標應用程式特有的值。若要尋找這些值，
您將需要登入 [Azure 管理入口網站][]。

登入管理入口網站。

-   **[subscriptionname]：**選擇易記名稱以參照 Azure 訂閱，而此訂閱包含您要在其中使用自動調整的應用程式。

-   **[subscriptionid]：**Azure 訂閱的唯一 ID，而此訂閱包含您要在其中使用自動調整的應用程式。

    1.在 Azure 管理入口網站中，按一下
        **雲端服務**。

    2.在雲端服務清單中，按一下主控您要在其中使用自動調整之應用程式的服務。右邊的 [快速瀏覽] 窗格將顯示 [**訂閱識別碼**]。

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling05.png)

  
	-   **[hostedservicednsprefix]：** 您要在其中使用自動調整之代管服務的 DNS 前置詞。

    1.在 Azure 管理入口網站中，按一下 [**雲端服務**]。 
    2.在雲端服務清單中，找出主控您要在其中使用自動調整之應用程式的服務。雲端服務的名稱為 **DNS Prefix**。

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling06.png)
 
	-   **[targetrolename]：**作為自動調整規則目標的角色名稱。

    1.在 Azure 管理入口網站中，按一下**雲端服務**。

    2.在雲端服務清單中，按一下主控您要在其中使用自動調整之應用程式的服務，然後按一下 [**執行個體**]。 **角色* 資料欄顯示目標角色的名稱。

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling07.png)


	-   **[storageaccountname]** 和 **[storageaccountkey]：**您用於目標 Azure 應用程式的 Azure 儲存體帳戶名稱。

    1.在 Azure 管理入口網站中，按一下 [**儲存體**]。

    2.在儲存體帳戶的清單中，選取您要使用的儲存體帳戶。[**名稱**] 資料行將顯示 [**名稱**]。

    3.按一下畫面底端的 [**管理金鑰**] 按鈕，以取得主要存取金鑰。

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling08.png)
  
 
	-   **[managementcertificatethumbprint]：**管理憑證的 [**指紋**]，區塊將使用此管理憑證，來保護目標應用程式的調整要求的安全。

    1.在 Azure 管理入口網站中，按一下 [**設定**]。

    2.[**指紋**] 資料行將顯示 [**指紋**]。

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling09.png)
 

若要深入了解服務模型檔案的內容，請參閱[儲存您的服務資訊資料][]。

## <a id="DefineAutoscalingRules"> </a>作法：定義您的自動調整規則

通常，您可以將控制目標應用程式中角色執行個體數目的自動調整規則儲存在 XML 檔案中。您可在專案的 **AutoscalingRules.xsd** 檔案中找到此 XML 檔案的結構描述複本。在 Visual Studio 中，當您編輯 XML 檔案時，此結構描述會提供 Intellisense 和驗證。

在專案中建立一個名稱為 **rules.xml** 的新 XML 檔案。

在 Visual Studio 中，您必須確定規則檔案複製至輸出資料夾。作法：

1.  在檔案上按一下滑鼠右鍵，然後選取 [**內容**]。

2.  在 [內容] 窗格內，將 [**複製到輸出目錄**] 值設為 [**永遠複製**]。

下列程式碼範例顯示 **
file:

    <?xml version="1.0" encoding="utf-8" ?>
    <rules xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/rules">
      <constraintRules>
        <rule name="default" enabled="true" rank="1" description="The default constraint rule">
          <actions>
            <range min="2" max="6" target="AutoscalingApplicationRole"/>
          </actions>
        </rule>
      </constraintRules>
      <reactiveRules>
        <rule name="ScaleUpOnHighUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <any>
               <greaterOrEqual operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </any>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="1"/>
          </actions>
        </rule>
        <rule name="ScaleDownOnLowUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <all>
              <less operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </all>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="-1"/>
          </actions>
        </rule>
      </reactiveRules>
      <operands>
        <performanceCounter alias="WebRoleA_CPU_Avg_5m"
          performanceCounterName="\Processor(_Total)\% Processor Time"
          source ="AutoscalingApplicationRole"
          timespan="00:05:00" aggregate="Average"/>
      </operands>
    </rules>

In this example there are three autoscaling rules (one **constraint rule** and two **reactive rules**) that operate on a target named **AutoscalingApplicationRole** that is the alias of a role defined in the **service model**:

-   The constraint rule is always active and sets the minimum number of     role instances to two and the maximum number of role instances to     six.

-   Both reactive rules use an **operand** named     **WebRoleA\_CPU\_Avg\_5m** that calculates the average CPU usage     over the last five minutes for an Azure role named     **AutoscalingApplicationRole.** This role is defined in the     **service model**.

-   The reactive rule named **ScaleUpOnHighUtilization** increments the     instance count of the target role by one if the average CPU     utilization over the last five minutes has been greater than or     equal to 60%.

-   The reactive rule named **ScaleDownOnLowUtilization** decrements the     instance count of the target role by one if the average CPU     utilization over the last five minutes has been less than 60%.

## <a id="Configure"> </a>How To: Configure the Autoscaling Application Block

After you have defined your service model and autoscaling rules, you must configure the Autoscaling Application Block to use them. This operational configuration information is stored in the application configuration file.

By default, the Autoscaling Application Block expects the autoscaling rules and service model to be stored in Azure blobs. In this example, you will configure the block to load them from the local file system.

### Configure the Autoscaling Application Block in the host application

1.  Right-click on the **App.config** file in Solution Explorer and then     click **Edit Configuration File**.

2.  In the **Blocks** menu, click **Add Autoscaling Settings**:  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling10.png)
  
3.  Expand the **Autoscaling Settings** and then click the ellipsis (...)     next to the **Data Points Store Storage Account**, add the **Account     name** and **Account key** of the Azure storage account     where the block will store the data points that it collects (see     [How To: Define your Service Model][] if you are unsure about where     to find these values), and then click **OK**:  

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling11.png)

4.  Expand the **Autoscaling Settings** section to reveal the **Rules     Store** and **Service Information Store** sections. By default, they     are configured to use Azure blob storage:  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling12.png)


5.  Click the plus sign (+) next to **Rules Store**, point to **Set     Rules Store**, then click **Use Local File Rules Store**, and then     click **Yes**.

6.  In the **File Name** box, type **rules.xml**. This is the name of     the file that contains your autoscaling rules:  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling13.png)


7.  Click the plus sign (+) next to **Service Information Store**, point     to **Set Service Information Store**, then click **Use Local File     Service Information Store**, and then click **Yes**.

8.  In the **File Name** box, type **services.xml**. This is the name of     the file that contains your autoscaling rules:  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling14.png)


9.  In the Enterprise Library Configuration window, on the **File**     menu, click **Save** to save your configuration changes. Then in the     Enterprise Library Configuration window, on the **File** menu, click     **Exit**.

To get detailed information about the actions that the Autoscaling Application Block is performing you need to capture the log messages that it writes. For example, if you are hosting the block in a console application, you can view the log messages in the Output window in Visual Studio. The following section shows you how to configure this behavior.

### Configure logging in the Autoscaling Application Block host application

1.  In Visual Studio, double-click on the **App.config** file in     Solution Explorer to open it in the editor. Then add the     **system.diagnostics** section as shown in the following sample:

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
          ...
          <system.diagnostics>
            <sources>
              <sourcename="Autoscaling General" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
              <sourcename="Autoscaling Updates" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
            </sources>
            <switches>
              <addname="SourceSwitch" value="Verbose, Information, Warning, Error, Critical" />
            </switches>
          </system.diagnostics>
        </configuration>

2.  Save your changes.

You can now run your Autoscaling Application Block host console application and observe how the autoscaling rules work with your target Azure application. When you run the host console application, you should see messages similar to the following in the Output window in Visual Studio. These log messages help you to understand the behavior of the block. For example, they indicate which rules are being matched by the block and what actions the block is taking.

    Autoscaling General Verbose: 1002 : Rule match.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "MatchingRules":[{"RuleName":"default","RuleDescription":"The default constraint rule",
    "Targets":["AutoscalingApplicationRole"]},{"RuleName":"ScaleUp","RuleDescription":"Scale up the web role",
    "Targets":[]},{"RuleName":"ScaleDown","RuleDescription":"Scale up the web role","Targets":[]}]}

    Autoscaling Updates Verbose: 3001 : The current deployment configuration for a hosted service is about to be checked
    to determine if a change is required (for role scaling or changes to settings).
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{}}

    Autoscaling Updates Verbose: 3003 : Role scaling requests for hosted service about to be submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},
    "ScaleRequests":{"AutoscalingApplicationRole":
    {"Min":2,"Max":6,"AbsoluteDelta":0,"RelativeDelta":0,"MatchingRules":"default"}},
    "SettingChangeRequests":{},"InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{}}

    Autoscaling Updates Information: 3002 : Role configuration changes for deployment were submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{},
    "InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{},"RequestID":"f8ca3ada07c24559b1cb075534f02d44"}

## <a id="NextSteps"> </a>Next Steps

Now that you've learned the basics of using the Autoscaling Application Block, follow these links to learn how to implement more complex autoscaling scenarios:

-   [Hosting the Autoscaling Application Block in a Worker Role][]
-   [Implementing Throttling Behavior][]
-   [Understanding Rule Ranks and Reconciliation][]
-   [Extending and Modifying the Autoscaling Application Block][]
-   [Using the Optimizing Stabilizer to prevent high frequency oscillation and to optimize costs][]
-   [Using Notifications and Manual Scaling][]
-   [Defining Scale Groups][]
-   [Using the WASABiCmdlets for manipulating the block via Windows PowerShell][]
-   [Developer's Guide to the Enterprise Library 5.0 Integration Pack for Azure][]
-   [How Sage Reduces Azure Hosting Costs Using Autoscaling][]
-   [Reducing TechNet and MSDN hosting costs and environmental impact with autoscaling on Azure][]

  [Microsoft Enterprise Library 5.0 Integration Pack for Azure]:
    http://go.microsoft.com/fwlink/?LinkID=235134
  [Next Steps]: #NextSteps
  [What is the Autoscaling Application Block?]: #WhatIs
  [Concepts]: #Concepts
  [Collect Performance Counter Data from your Target Azure Application]: #PerfCounter
  [Set up a Host Application for the Autoscaling Application Block]: #CreateHost
  [How to: Instantiate and Run the Autoscaler]: #Instantiate
  [How To: Define your Service Model]: #DefineServiceModel
  [How To: Define your Autoscaling Rules]: #DefineAutoscalingRules
  [How To: Configure the Autoscaling Application Block]: #Configure
  [Using performance counters in Azure]: http://www.windowsazure.com/zh-tw/develop/net/common-tasks/performance-profiling/
  [NuGet]: http://nuget.org/
  [Azure Management Portal]: http://manage.windowsazure.com
  [Storing Your Service Information Data]: http://msdn.microsoft.com/zh-tw/library/hh680878(PandP.50).aspx		
  [Hosting the Autoscaling Application Block in a Worker Role]: http://msdn.microsoft.com/zh-tw/library/hh680914(PandP.50).aspx
  [Implementing Throttling Behavior]: http://msdn.microsoft.com/zh-tw/library/hh680896(PandP.50).aspx
  [Understanding Rule Ranks and Reconciliation]: http://msdn.microsoft.com/zh-tw/library/hh680923(PandP.50).aspx
  [Extending and Modifying the Autoscaling Application Block]: http://msdn.microsoft.com/zh-tw/library/hh680889(PandP.50).aspx
  [Using the Optimizing Stabilizer to prevent high frequency oscillation and to optimize costs]: http://msdn.microsoft.com/zh-tw/library/hh680951(PandP.50).aspx
  [Using Notifications and Manual Scaling]: http://msdn.microsoft.com/zh-tw/library/hh680885(PandP.50).aspx
  [Defining Scale Groups]: http://msdn.microsoft.com/zh-tw/library/hh680902(PandP.50).aspx
  [Using the WASABiCmdlets for manipulating the block via Windows PowerShell]: http://msdn.microsoft.com/zh-tw/library/hh680938(PandP.50).aspx
  [Developer's Guide to the Enterprise Library 5.0 Integration Pack for Azure]: http://msdn.microsoft.com/zh-tw/library/hh680949(PandP.50).aspx
  [How Sage Reduces Azure Hosting Costs Using Autoscaling]: http://msdn.microsoft.com/zh-tw/library/jj838716(PandP.50).aspx
  [Reducing TechNet and MSDN hosting costs and environmental impact with autoscaling on Azure]: http://msdn.microsoft.com/zh-tw/library/jj838718(PandP.50).aspx


<!--HONumber=35_1-->
