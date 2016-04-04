<properties
	pageTitle="使用資源管理員來稽核作業 |Microsoft Azure"
	description="在 [資源管理員] 中使用稽核記錄檔，以檢閱使用者動作和錯誤。顯示 Azure 入口網站 PowerShell、Azure CLI 和 REST。"
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/21/2016"
	ms.author="tomfitz"/>

# 使用資源管理員來稽核作業

透過稽核記錄檔，您可以判斷︰

- 訂用帳戶的資源在進行哪些作業
- 誰起始作業 (雖然由後端服務起始的作業不會傳回使用者做為呼叫端)
- 作業進行的時間
- 作業的狀態
- 其他可能協助您研究作業的屬性值

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

本主題著重於稽核作業。若要了解如何使用稽核記錄檔針對部署進行疑難排解，請參閱[在 Azure 中針對資源群組部署進行疑難排解](resource-manager-troubleshoot-deployments-portal.md)。

您可以透過 Azure 入口網站、Azure PowerShell、Azure CLI、Insights REST API 或 [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 擷取稽核記錄檔中的資訊。

## 檢視稽核記錄檔的入口網站

1. 若要透過入口網站檢視稽核記錄檔，請選取 [瀏覽] 和 [稽核記錄檔]。

    ![選取稽核記錄檔](./media/resource-group-audit/select-audit-logs.png)

2. 在 [稽核記錄檔] 刀鋒視窗中，您會看到訂用帳戶中所有資源群組的最新作業摘要。它會包含表示作業的時間與狀態的圖形，以及作業的清單。

    ![顯示動作](./media/resource-group-audit/audit-summary.png)

3. 若要尋找特定類型的動作，您可以篩選 [稽核記錄檔] 刀鋒視窗中顯示哪些作業。選取刀鋒視窗頂端的 [篩選]。

    ![篩選記錄檔](./media/resource-group-audit/filter-logs.png)

4. 從 [篩選] 刀鋒視窗，您可以選取許多不同的條件來限制顯示的作業數目。例如，您可以看到過去一週由特定使用者所採取的所有動作。

    ![設定篩選選項](./media/resource-group-audit/set-filter.png)

更新稽核記錄檔的檢視之後，您只會看到符合指定條件的作業。這些設定會保留到您下次檢視稽核記錄檔時，因此您可能需要變更這些值，以擴大您的作業檢視。

您也可以藉由從特定資源的刀鋒視窗選取稽核記錄檔，以自動篩選該資源。在入口網站中，選取要稽核的資源，並選取 [稽核記錄檔]。

![稽核資源](./media/resource-group-audit/audit-by-resource.png)

請注意，稽核記錄檔會自動由過去一週選取的資源來篩選。

![依資源篩選](./media/resource-group-audit/filtered-by-resource.png)

## 檢視稽核記錄檔的 PowerShell

1. 若要擷取記錄檔項目，請執行 **Get-AzureRmLog** 命令。您可提供額外的參數來篩選項目清單。如果未指定開始和結束時間，則會傳回最後一個小時的項目。例如，若要在過去一小時執行期間擷取資源群組的作業：

        PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup

    下列範例示範如何使用稽核記錄檔來研究指定期間所採取的作業。以日期格式指定開始和結束日期。

        PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00

    或者，您可以使用日期函數來指定日期範圍，例如過去 14 天。

        PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)

2. 視您指定的開始時間而定，先前的命令可以傳回該資源群組的一長串作業。您可以提供搜尋準則，以篩選您所尋找的結果。例如，假如您想研究 Web 應用程式停止執行的方式，您可以執行下列命令，並藉此了解停止動作原來是由 someone@contoso.com 執行的。

        PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
        
        Authorization     :
        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Action    : Microsoft.Web/sites/stop/action
        Role      : Subscription Admin
        Condition :
        Caller            : someone@contoso.com
        CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
        EventSource       : Administrative
        EventTimestamp    : 8/28/2015 4:08:18 PM
        OperationName     : Microsoft.Web/sites/stop/action
        ResourceGroupName : ExampleGroup
        ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Status            : Succeeded
        SubscriptionId    : xxxxx
        SubStatus         : OK

3. 您可以查閱由特定使用者採取的動作，即使是針對已不存在的資源群組。

        PS C:\> Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com

## 檢視稽核記錄檔的 Azure CLI

1. 若要擷取記錄檔項目，您可執行 **azure group log show** 命令。

        azure group log show ExampleGroup

2. 您可以使用 JSON 公用程式 (例如 [jq](http://stedolan.github.io/jq/download/)) 來篩選結果。下列範例示範如何尋找更新 Web 組態檔的作業。

        azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

3. 您可以查詢特定使用者的動作。

        azure group log show ExampleGroup --json | jq ".[] | select(.caller=="someone@contoso.com")"

## 檢視稽核記錄檔的 REST API

可用來處理稽核記錄檔的 REST 作業屬於 [Insights REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx) 的一部分。若要擷取稽核記錄檔事件，請參閱[列出訂用帳戶中的管理事件](https://msdn.microsoft.com/library/azure/dn931934.aspx)。

## 後續步驟

- Azure 稽核記錄檔可以搭配 Power BI 用來更深入了解訂用帳戶中的動作。請參閱[在 Power BI 和其他工具中檢視和分析 Azure 稽核記錄](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)。
- 如要了解如何設定安全性原則，請參閱[Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。
- 若要了解針對部署進行疑難排解的命令，請參閱[在 Azure 中針對資源群組部署進行疑難排解](resource-manager-troubleshoot-deployments-portal.md)。
- 若要了解如何防止刪除所有使用者的資源，請參閱[使用 Azure Resource Manager 鎖定資源](resource-group-lock-resources.md)。

<!---HONumber=AcomDC_0323_2016-->