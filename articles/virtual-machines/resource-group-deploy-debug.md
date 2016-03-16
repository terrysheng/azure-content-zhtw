<properties
   pageTitle="疑難排解資源群組部署 |Microsoft Azure"
   description="描述部署使用資源管理員部署模型建立之資源的常見問題，以及示範如何偵測並修正這些問題。"
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="01/28/2016"
   ms.author="tomfitz;rasquill"/>

# 在 Azure 中疑難排解資源群組部署

當您在部署期間遇到問題時，您需要了解發生什麼問題。[資源管理員] 提供兩種方法讓您了解發生什麼情形以及其發生原因。您可以使用部署命令來擷取有關資源群組的特定部署。或者，您可以使用稽核記錄來擷取在資源群組上執行的所有作業的相關資訊。您可以利用此資訊來修正問題，並在您的解決方案中繼續執行作業。

本主題主要著重於使用部署命令來排解部署疑難問題。如需使用稽核記錄檔來追蹤您的資源上所有作業的詳細資訊，請參閱[稽核作業與資源管理員](../resource-group-audit.md)。

本主題說明如何透過 Azure PowerShell、Azure CLI 和 REST API 擷取疑難排解資訊。如需使用入口網站來排解部署問題的相關資訊，請參閱[使用 Azure 入口網站管理 Azure 資源](../azure-portal/resource-group-portal.md)。

本主題也說明了使用者遇到的常見錯誤的解決方案。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。您無法以傳統部署模型建立資源群組。


## PowerShell 疑難排解

[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)]

您可以利用 **Get-AzureRmResourceGroupDeployment** 命令取得部署的整體狀態。下列範例中的部署失敗。

    PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment

    DeploymentName    : ExampleDeployment
    ResourceGroupName : ExampleGroup
    ProvisioningState : Failed
    Timestamp         : 8/27/2015 8:03:34 PM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    siteName         String                     ExampleSite
                    hostingPlanName  String                     ExamplePlan
                    siteLocation     String                     West US
                    sku              String                     Free
                    workerSize       String                     0

    Outputs           :

每個部署通常由多個作業所組成，而每個作業代表部署程序中的一個步驟。若要探索部署有何問題，您通常需要查看有關部署作業的詳細資訊。您可以利用 **Get-AzureRmResourceGroupDeploymentOperation** 查看作業的狀態。

    PS C:\> Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup
    Id                        OperationId          Properties         
    -----------               ----------           -------------
    /subscriptions/xxxxx...   347A111792B648D8     @{ProvisioningState=Failed; Timestam...
    /subscriptions/xxxxx...   699776735EFC3D15     @{ProvisioningState=Succeeded; Times...

它會顯示部署中的兩項作業。其中一個的佈建狀態為 [失敗]，而另一個的佈建狀態為 [成功]。

您可以使用下列命令擷取狀態訊息：

    PS C:\> (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties.StatusMessage

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :

## Azure CLI 疑難排解

您可以利用 **azure group deployment show** 命令取得部署的整體狀態。下列範例中的部署失敗。

    azure group deployment show ExampleGroup ExampleDeployment

    info:    Executing command group deployment show
    + Getting deployments
    data:    DeploymentName     : ExampleDeployment
    data:    ResourceGroupName  : ExampleGroup
    data:    ProvisioningState  : Failed
    data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
    data:    Mode               : Incremental
    data:    Name             Type    Value
    data:    ---------------  ------  ------------
    data:    siteName         String  ExampleSite
    data:    hostingPlanName  String  ExamplePlan
    data:    siteLocation     String  West US
    data:    sku              String  Free
    data:    workerSize       String  0
    info:    group deployment show command OK


您可以在稽核記錄檔中找出部署失敗原因的詳細資訊。若要查看稽核記錄檔，請執行 **azure group log show** 命令。您可以加入 **--last-deployment** 選項，就能只擷取最新部署的記錄檔。

    azure group log show ExampleGroup --last-deployment

**azure group log show** 命令可傳回大量資訊。在疑難排解時，您通常想要將焦點放在失敗的作業。下列指令碼會使用 **--json** 選項和 **jq** 來搜尋部署失敗的記錄檔。若要了解 **jq** 等工具，請參閱[與 Azure 互動的有用工具](#useful-tools-to-interact-with-azure)

    azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'

    {
      "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/<guid>/",
        "iat": "1442510510",
        "nbf": "1442510510",
        "exp": "1442514410",
        "ver": "1.0",
        "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
        "puid": "XXXXXXXXXXXXXXXX",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",

        "altsecid": "1:example.com:XXXXXXXXXXX",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
        "name": "Tom FitzMacken",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
        "groups": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
        "wids": "<guid>",
        "appid": "<guid>",
        "appidacr": "0",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "ipaddr": "000.000.000.000"
      },
      "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{"Code":"Conflict","Message":"Website with given name mysite already exists.","Target":null,"Details":[{"Message":"Website with given name
          mysite already exists."},{"Code":"Conflict"},{"ErrorEntity":{"Code":"Conflict","Message":"Website with given name mysite already exists.","ExtendedCode":
          "54001","MessageTemplate":"Website with given name {0} already exists.","Parameters":["mysite"],"InnerErrors":null}}],"Innererror":null}"
      },
    ...

您可以看到，**properties** 包含 json 中有關失敗作業的資訊。

您可以使用 **-verbose** 和 **-vv** 選項來查看記錄檔中的詳細資訊。使用 **--verbose** 選項可顯示作業在 `stdout` 上經歷的步驟。如需完整的要求歷程記錄，請使用 **-vv** 選項。這些訊息通常會提供任何失敗原因的重要線索。

## REST API 疑難排解

資源管理員 REST API 提供的 URI 可供擷取部署的相關資訊、部署和作業，以及有關特定作業的詳細資料。如需這些命令的完整描述，請參閱：

- [取得範本部署的相關資訊](https://msdn.microsoft.com/library/azure/dn790565.aspx)
- [列出所有範本部署作業](https://msdn.microsoft.com/library/azure/dn790518.aspx)
- [取得範本部署作業的相關資訊](https://msdn.microsoft.com/library/azure/dn790519.aspx)


## 重新整理過期的認證

如果您的 Azure 認證已過期或您尚未登入您的 Azure 帳戶，您的部署將會失敗。如果您的工作階段的開啟時間太長，您的認證可能會過期。您可以使用下列選項重新整理您的認證︰

- 針對 PowerShell，請使用 **Login-AzureRmAccount** Cmdlet。發行設定檔中的認證無法滿足 AzureResourceManager 模組中 Cmdlet 的需求。
- 針對 Azure CLI，請使用 **azure login**。　如需驗證錯誤的協助，請確定您[已正確地設定 Azure CLI](../xplat-cli-connect.md)。

## 檢查範本和參數的格式

如果您範本或參數檔案的格式不正確，您的部署將會失敗。執行部署之前，您可以測試您的範本和參數的有效性。

### PowerShell

針對 PowerShell，請使用 **Test-AzureRmResourceGroupDeployment**。

    PS C:\> Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\Azure\Templates\azuredeploy.json -TemplateParameterFile c:\Azure\Templates\azuredeploy.parameters.json
    VERBOSE: 12:55:32 PM - Template is valid.

### Azure CLI

針對 Azure CLI，請使用 **azure group template validate <resource group>**

下列範例顯示如何驗證範本和任何必要的參數。Azure CLI 會提示您輸入所需的參數值。

        azure group template validate \
        > --template-uri "https://contoso.com/templates/azuredeploy.json" \
        > resource-group
        info:    Executing command group template validate
        info:    Supply values for the following parameters
        adminUserName: UserName
        adminPassword: PassWord
        + Initializing template configurations and parameters
        + Validating the template
        info:    group template validate command OK

### REST API

針對 REST API，請參閱[驗證範本部署](https://msdn.microsoft.com/library/azure/dn790547.aspx)。

## 檢查哪些位置支援此資源

指定資源的位置時，您必須使用其中一個支援此資源的位置。在您輸入資源的位置之前，請使用下列其中一個命令來確認該位置是否支援此資源類型。

### PowerShell

針對比 PowerShell 1.0 更早的版本，您可以利用 **Get-AzureLocation** 命令來查看完整的資源和位置清單。

    PS C:\> Get-AzureLocation

    Name                                    Locations                               LocationsString
    ----                                    ---------                               ---------------
    ResourceGroup                           {East Asia, South East Asia, East US... East Asia, South East Asia, East US,...
    Microsoft.ApiManagement/service         {Central US, East US, East US 2, Nor... Central US, East US, East US 2, Nort...
    Microsoft.AppService/apiapps            {East US, West US, South Central US,... East US, West US, South Central US, ...
    ...

您可以利用下列命令來指定特定類型的資源︰

    PS C:\> Get-AzureLocation | Where-Object Name -eq "Microsoft.Compute/virtualMachines" | Format-Table Name, LocationsString -Wrap

    Name                                                        LocationsString
    ----                                                        ---------------
    Microsoft.Compute/virtualMachines                           East US, East US 2, West US, Central US, South Central US,
                                                                North Europe, West Europe, East Asia, Southeast Asia,
                                                                Japan East, Japan West

針對 PowerShell 1.0 Preview，請使用 **Get AzureRmResourceProvider** 來取得支援的位置。

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

    ProviderNamespace RegistrationState ResourceTypes               Locations
    ----------------- ----------------- -------------               ---------
    Microsoft.Web     Registered        {sites/extensions}          {Brazil South, ...
    Microsoft.Web     Registered        {sites/slots/extensions}    {Brazil South, ...
    Microsoft.Web     Registered        {sites/instances}           {Brazil South, ...
    ...

您可以利用下列命令來指定特定類型的資源︰

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia

### Azure CLI

針對 Azure CLI，您可以使用 **azure location list**。因為位置清單可能很長，而且有多個提供者，所以您可以先使用工具來檢查提供者和位置，再使用尚未使用的位置。下列指令碼使用 **jq** 來探索可取得 Azure 虛擬機器之資源提供者的位置。

    azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### REST API

針對 REST API，請參閱[取得資源提供者的相關資訊](https://msdn.microsoft.com/library/azure/dn790534.aspx)。

## 建立唯一的資源名稱

對於某些資源 (最顯著的儲存體帳戶、資料庫伺服器和網站)，您必須提供在整個 Azure 中是唯一的資源名稱。目前沒有方法可測試名稱是否唯一。我們建議使用其他組織不太可能使用的命名慣例。

## 驗證、訂用帳戶、角色和配額問題

可能有一個或多個問題防止部署成功，包含驗證和授權以及 Azure Active Directory。不論您如何管理 Azure 資源群組，您用來登入帳戶的身分識別都必須是 Azure Active Directory 物件。此身分識別可以是您建立或指派給您的工作或學校帳戶，或者您可以建立服務主體用於應用程式。

但是 Azure Active Directory 可讓您或您的系統管理員最精確地控制哪些身分識別可以存取哪些資源。如果您的部署失敗，請檢查要求本身是否顯示驗證或授權問題，並檢查資源群組的部署記錄。您可能會發現，當您擁有某些資源的權限時，就不會擁有其他資源的權限。使用 Azure CLI，您可以利用 `azure ad` 命令檢查 Azure Active Directory 租用戶和使用者 (如需完整的 Azure CLI 命令清單，請參閱[搭配 Azure 資源管理員使用適用於 Mac、Linux 和 Windows 的 Azure CLI](azure-cli-arm-commands.md))。

當部署達到預設配額 (可能是每個資源群組、訂用帳戶、帳戶及其他範圍的配額) 時，也可能會發生問題。請確保您有可正確部署的足夠資源。如需完整的配額資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](../azure-subscription-service-limits.md)。

如要檢查您訂用帳戶的核心配額，請使用 Azure CLI 中的 `azure vm list-usage` 命令，以及 PowerShell 中的 **Get-AzureVMUsage** Cmdlet。以下顯示 Azure CLI 中的命令，並說明免費試用帳戶的核心配額為 4：

    azure vm list-usage
    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

如果您嘗試部署的範本會在上述訂用帳戶內，於美國西部區域中建立 4 個以上的核心，則會發生部署錯誤，該錯誤看起來如下 (可能是在入口網站中，也可能是調查部署記錄時發現的)：

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

在這些情況下，您應該移至入口網站，並提出支援問題，以針對您想要部署的區域提高配額。

> [AZURE.NOTE] 請記住，對於資源群組，配額適用於每個個別區域，而不是整個訂用帳戶。如果您需要在美國西部部署 30 個核心，就必須要求在美國西部擁有 30 個資源管理員核心。如果您需要在任何具有存取權限的區域中部署 30 個核心，就應該要求在所有區域中擁有 30 個資源管理員核心。
<!-- --> 
舉例來說，若要更明確地了解核心，您可以使用下列命令來檢查應該要求適當配額數目的區域，這個命令可以使用管線傳送到 **jq** 以進行 JSON 剖析。
<!-- --> 
		azure provider show Microsoft.Compute --json | jq '.resourceTypes | select(.name == "virtualMachines") | { name,apiVersions, locations}' 
        {
          "name": "virtualMachines",
          "apiVersions": [
            "2015-05-01-preview",
            "2014-12-01-preview"
          ],
          "locations": [
            "East US",
            "West US",
            "West Europe",
            "East Asia",
            "Southeast Asia"
          ]
        }


## 檢查資源提供者註冊

資源是由資源提供者所管理，並且可能啟用帳戶或訂用帳戶以便使用特定提供者。如果您可以使用某個提供者，則也必須註冊該提供者才能使用。Azure 入口網站或正在使用的命令列介面會自動註冊大部分的提供者；但並非全部。

### PowerShell

針對比 PowerShell 1.0 更早的版本，如要取得資源提供者的清單及您的註冊狀態，請使用 **Get-AzureProvider**。

    PS C:\> Get-AzureProvider

    ProviderNamespace                       RegistrationState                       ResourceTypes
    -----------------                       -----------------                       -------------
    Microsoft.AppService                    Registered                              {apiapps, appIdentities, gateways, d...
    Microsoft.Batch                         Registered                              {batchAccounts}
    microsoft.cache                         Registered                              {Redis, checkNameAvailability, opera...
    ...

若要註冊提供者，請使用 **Register-AzureProvider**。

針對 PowerShell 1.0，請使用 **Get-AzureRmResourceProvider**。

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}

若要註冊提供者，請使用 **Register-AzureRmResourceProvider**。

### Azure CLI

若要使用 Azure CLI 查看是否註冊提供者以供使用，請使用 `azure provider list` 命令 (下列是截斷的輸出範例)。

        azure provider list
        info:    Executing command provider list
        + Getting ARM registered providers
        data:    Namespace                        Registered
        data:    -------------------------------  -------------
        data:    Microsoft.Compute                Registered
        data:    Microsoft.Network                Registered  
        data:    Microsoft.Storage                Registered
        data:    microsoft.visualstudio           Registered
        data:    Microsoft.Authorization          Registered
        data:    Microsoft.Automation             NotRegistered
        data:    Microsoft.Backup                 NotRegistered
        data:    Microsoft.BizTalkServices        NotRegistered
        data:    Microsoft.Features               Registered
        data:    Microsoft.Search                 NotRegistered
        data:    Microsoft.ServiceBus             NotRegistered
        data:    Microsoft.Sql                    Registered
        info:    provider list command OK

同樣地，如需提供者的詳細資訊，包括其區域可用性，請輸入 `azure provider list --json`。下列程式碼只會選取清單中的第一個項目來檢視：

        azure provider list --json | jq '.[0]'
        {
          "resourceTypes": [
            {
              "apiVersions": [
                "2014-02-14"
              ],
              "locations": [
                "North Central US",
                "East US",
                "West US",
                "North Europe",
                "West Europe",
                "East Asia"
              ],
              "properties": {},
              "name": "service"
            }
          ],
          "id": "/subscriptions/<guid>/providers/Microsoft.ApiManagement",
          "namespace": "Microsoft.ApiManagement",
          "registrationState": "Registered"
        }

如果提供者需要註冊，請使用 `azure provider register <namespace>` 命令，其中 *namespace* 值來自先前的清單。

### REST API

若要取得註冊狀態，請參閱[取得資源提供者的相關資訊](https://msdn.microsoft.com/library/azure/dn790534.aspx)。

若要註冊提供者，請參閱[向資源提供者註冊訂用帳戶](https://msdn.microsoft.com/library/azure/dn790548.aspx)。


## 了解自訂範本的部署何時成功

如果是使用您所建立的範本，請務必了解 Azure 資源管理員系統會在成功從部署傳回所有提供者時，報告部署成功。這表示已部署所有範本項目，供您使用。

不過，請注意，這不一定表示您的資源群組「作用中且準備好供使用者使用」。例如，大多數的部署都會要求部署下載升級、等候其他升級、非範本資源，或是安裝複雜的指令碼或 Azure 不知道的某個其他可執行活動，因為它不是提供者正在追蹤的活動。在這些情況下，可能需要一些時間，您的資源才能用於實際使用。因此，您應該預期部署狀態成功一段時間後，才能使用部署。

不過，建立自訂範本的自訂指令碼 (例如，使用 [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/))，即可防止 Azure 報告部署成功，而此自訂指令碼知道如何監視整個部署以了解系統是否準備就緒，並只在使用者可以與整個部署互動時才會順利傳回。如果您想要確保最後才執行您的延伸模組，請在範本中使用 **dependsOn** 屬性。在[建立範本部署](https://msdn.microsoft.com/library/azure/dn790564.aspx)時會顯示範例。

## 與 Azure 互動的有用工具
當您從命令列使用 Azure 資源時，將收集可協助您進行工作的工具。Azure 資源群組範本為 JSON 文件，而且 Azure 資源管理員 API 會接受並傳回 JSON，因此 JSON 剖析工具是您用來協助瀏覽資源相關資訊，以及設計範本和範本參數檔案或與之互動的首要選擇。

### Mac、Linux 和 Windows 工具
如果您使用適用於 Mac、Linux 和 Windows 的 Azure 命令列介面，則可能熟悉標準下載工具 (例如 **[curl](http://curl.haxx.se/)** 和 **[wget](https://www.gnu.org/software/wget/)** 或 **[Resty](https://github.com/beders/Resty)**) 以及 JSON 公用程式 (例如 **[jq](http://stedolan.github.io/jq/download/)**、**[jsawk](https://github.com/micha/jsawk)** 和擅於處理 JSON 的語言程式庫)。(這些工具亦多數具有 Windows 的連接埠，例如 [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)；事實上，有數種方法都可以取得 Linux 以及在 Windows 上執行的其他開放原始碼軟體工具)。

本主題包括一些您可以與 **jq** 搭配使用的 Azure CLI 命令，以精確地取得您想要更有效率的資訊。您應該選擇熟悉的工具組來協助您了解 Azure 資源使用狀況。

### PowerShell

PowerShell 有幾個基本命令來執行相同的程序。

- 使用 **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** Cmdlet 可下載檔案 (例如資源群組範本或參數 JSON 檔案)。
- 使用 **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** Cmdlet 可將 JSON 字串轉換成自訂物件 ([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx))，而自訂物件具有 JSON 字串中每個欄位的屬性。


## 後續步驟

若要精通範本建立，請閱讀[編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)，並逐步查看 [Azure 快速入門範本儲存機制](https://github.com/Azure/azure-quickstart-templates)以取得可部署的範例。**dependsOn** 屬性的一個範例是[建立具有多個可存取之 NIC 和 RDP 的 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-1-vm-loadbalancer-2-nics)。

<!--Image references-->

<!--Reference style links - using these makes the source content way more readable than using inline links-->

<!----HONumber=AcomDC_0204_2016-->