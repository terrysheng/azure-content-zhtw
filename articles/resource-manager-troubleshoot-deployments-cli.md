<properties
   pageTitle="使用 Azure ACI 疑難排解部署 | Microsoft Azure"
   description="描述如何使用 Azure CLI 來偵測並修正源自資源管理員部署的問題。"
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="03/21/2016"
   ms.author="tomfitz"/>

# 使用 Azure ACI 疑難排解資源群組部署

> [AZURE.SELECTOR]
- [入口網站](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

如果您在將資源部署至 Azure 時發生錯誤，您需要針對出錯的地方進行疑難排解。Azure CLI 提供命令，可讓您找出錯誤並判斷可能的修正方法。

您可以藉由查看稽核記錄檔或部署作業來疑難排解您的部署。本主題說明這兩種方法。

部署之前先驗證您的範本和基礎結構，即可避免發生一些錯誤。如需詳細資訊，請參閱[使用 Azure Resource Manager 範本部署資源群組](resource-group-template-deploy.md)。

## 使用稽核記錄檔進行疑難排解

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

若要查看部署相關錯誤，請使用下列步驟 ︰

1. 若要查看稽核記錄檔，請執行 **azure group log show** 命令。您可以加入 **--last-deployment** 選項，就能只擷取最新部署的記錄檔。

        azure group log show ExampleGroup --last-deployment

2. **azure group log show** 命令可傳回大量資訊。在疑難排解時，您通常想要將焦點放在失敗的作業。下列指令碼會使用 **--json** 選項和 [jq](https://stedolan.github.io/jq/) JSON 公用程式來搜尋部署失敗的記錄檔。

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
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

3. 若要專注於失敗項目的狀態訊息，請使用下列命令︰

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == "Failed") | .properties.statusMessage"


## 使用部署作業進行疑難排解

1. 利用 **azure group deployment show** 命令取得部署的整體狀態。下列範例中的部署失敗。

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
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

2. 若要查看部署失敗作業的訊息，請使用︰

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == "Failed") | .properties.statusMessage.Message"


## 後續步驟

- 若要了解如何使用稽核記錄檔來監視其他類型的動作，請參閱[使用資源管理員來稽核作業](resource-group-audit.md)。
- 若要在執行之前驗證您的部署，請參閱[使用 Azure Resource Manager 範本部署資源群組](resource-group-template-deploy.md)。

<!---HONumber=AcomDC_0323_2016-->