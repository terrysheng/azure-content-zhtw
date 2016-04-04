<properties
   pageTitle="使用 PowerShell 疑難排解部署 | Microsoft Azure"
   description="描述如何使用 Azure PowerShell 來偵測並修正源自資源管理員部署的問題。"
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

# 使用 Azure PowerShell 疑難排解資源群組部署

> [AZURE.SELECTOR]
- [入口網站](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

如果您在將資源部署至 Azure 時發生錯誤，您需要針對出錯的地方進行疑難排解。Azure PowerShell 提供 Cmdlet，可讓您輕鬆地找到錯誤並判斷可能的修正方法。

您可以藉由查看稽核記錄檔或部署作業來疑難排解您的部署。本主題說明這兩種方法。

部署之前先驗證您的範本和基礎結構，即可避免發生一些錯誤。如需詳細資訊，請參閱[使用 Azure Resource Manager 範本部署資源群組](resource-group-template-deploy.md)。

## 使用稽核記錄檔進行疑難排解

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

若要查看部署相關錯誤，請使用下列步驟 ︰

1. 若要擷取記錄檔項目，請執行 **Get-AzureRmLog** 命令。您可以使用 **ResourceGroup** 和 **Status** 參數，只傳回單一資源群組失敗的事件。如果未指定開始和結束時間，則會傳回最後一個小時的項目。例如，若要擷取過去一小時失敗的作業，請執行︰

        PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    您可以指定特定的時間範圍。在下一個範例中，我們將尋找過去 14 天失敗的動作。

        PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Status Failed
      
    或者，您可以針對失敗的動作設定確切的開始和結束時間︰

        PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. 如果這個命令傳回太多項目和屬性，您可以擷取 **Properties** 屬性，專注於進行稽核。我們也將加入 **DetailedOutput** 參數，以顯示錯誤訊息。

        PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties
        
        Content
        -------
        {}
        {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
        {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

3. 您可以藉由查看某一個特定項目的狀態訊息，進一步精簡結果。

        PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :


## 使用部署作業進行疑難排解

1. 若要取得部署的整體狀態，請使用 **Get-AzureRmResourceGroupDeployment** 命令。您可以篩選結果，只取得失敗的部署。

        PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
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

2. 每個部署通常由多個作業所組成，而每個作業代表部署程序中的一個步驟。若要探索部署有何問題，您通常需要查看有關部署作業的詳細資訊。您可以利用 **Get-AzureRmResourceGroupDeploymentOperation** 查看作業的狀態。

        PS C:\> Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment | Format-List
        
        Id          : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/ExampleDeployment/operations/8518B32868A437C8
        OperationId : 8518B32868A437C8
        Properties  : @{ProvisioningOperation=Create; ProvisioningState=Failed; Timestamp=2016-03-16T20:05:37.2638161Z;
                      Duration=PT2.8834832S; TrackingId=192fbfbf-a2e2-40d6-b31d-890861f78ed3; StatusCode=Conflict;
                      StatusMessage=; TargetResource=}

3. 若要取得更多有關作業的詳細資訊，請擷取 **Properties** 物件。

        PS C:\> (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties
        
        ProvisioningOperation : Create
        ProvisioningState     : Failed
        Timestamp             : 2016-03-16T20:05:37.2638161Z
        Duration              : PT2.8834832S
        TrackingId            : 192fbfbf-a2e2-40d6-b31d-890861f78ed3
        StatusCode            : Conflict
        StatusMessage         : @{Code=Conflict; Message=Website with given name mysite already exists.; Target=;
        Details=System.Object[]; Innererror=}
        TargetResource        : @{Id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
        Microsoft.Web/Sites/mysite; ResourceType=Microsoft.Web/Sites; ResourceName=mysite}

4. 若要專注於失敗作業的狀態訊息，請使用下列命令︰

        PS C:\> ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :

## 後續步驟

- 若要了解如何使用稽核記錄檔來監視其他類型的動作，請參閱[使用資源管理員來稽核作業](resource-group-audit.md)。
- 若要在執行之前驗證您的部署，請參閱[使用 Azure Resource Manager 範本部署資源群組](resource-group-template-deploy.md)。

<!---HONumber=AcomDC_0323_2016-->