<properties
	pageTitle="Azure RBAC 中的自訂角色 | Microsoft Azure"
	description="了解如何使用 Azure 角色型存取控制來定義自訂角色，以在您的 Azure 訂用帳戶中進行更精確的身分識別管理。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="03/25/2016"
	ms.author="kgremban"/>


# Azure RBAC 中的自訂角色


如果內建角色都不符合您的特定存取需求，請在 Azure 角色型存取控制 (RBAC) 中建立自訂角色。使用 Azure PowerShell、Azure 命令列介面 (CLI) 和 REST API，可以建立自訂角色。就像內建角色一樣，可以將自訂角色指派給訂用帳戶、資源群組和資源範圍的使用者、群組和應用程式。

以下是可以監視和重新啟動虛擬機器的自訂角色範例：

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## 動作
自訂角色的 **Actions** 屬性會指定角色授與存取權的 Azure 作業。它是識別 Azure 資源提供者的安全性實體作業的作業字串集合。包含萬用字元 (*) 的作業字串會授與符合作業字串的所有作業的存取權。例如：

-	`*/read` 授與所有 Azure 資源提供者的所有資源類型的讀取作業的存取權。
-	`Microsoft.Network/*/read` 授與 Azure 的 Microsoft.Network 資源提供者的所有資源類型的讀取作業的存取權。
-	`Microsoft.Compute/virtualMachines/*` 授與虛擬機器和其子系資源類型的所有作業的存取權。
-	`Microsoft.Web/sites/restart/Action` 授與重新啟動網站的存取權。

使用 `Get-AzureRmProviderOperation` (在 PowerShell 中) 或 `azure provider operations show` (在 Azure CLI 中) 來列出 Azure 資源提供者的作業。您也可以使用這些命令以確認作業字串有效，以及展開萬用字元作業字串。

![PowerShell 螢幕擷取畫面 - Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT 作業, OperationName](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

![Azure CLI 螢幕擷取畫面 - azure 提供者作業顯示 "Microsoft.Compute/virtualMachines/*/action"](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## NotActions
如果排除限制的作業可更輕鬆地定義您要允許的作業集合，請使用 **NotActions** 屬性。自訂角色授與的存取權是藉由從 **Actions** 作業中去掉 **NotActions** 作業來計算。

> [AZURE.NOTE] 如果使用者獲指派的角色排除 **NotActions** 中的作業，並且指派授與相同作業的存取權的第二個角色，將會允許使用者執行該作業。**NotActions** 不是拒絕規則 – 它只是一個便利的方式，可以在需要排除特定作業時建立允許作業集。

## AssignableScopes
自訂角色的 **AssignableScopes** 屬性會指定自訂角色可供指派的範圍 (訂用帳戶、資源群組或資源)。您可以讓自訂角色僅指派給需要它的訂用帳戶或資源群組，不會干擾其餘訂用帳戶或資源群組的使用者體驗。

有效的可指派範圍範例包括：

-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”, “/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624” - 讓角色可用於兩個訂用帳戶中的指派。
-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e” - 讓角色可用於單一訂用帳戶中的指派。
-  “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network” - 讓角色僅可用於網路資源群組中的指派。

> [AZURE.NOTE] 您至少必須使用一個訂用帳戶、資源群組或資源識別碼。

## 自訂角色存取控制
自訂角色的 **AssignableScopes** 屬性也會控制誰可以檢視、修改和刪除角色。

- 誰可以建立自訂角色？ 訂用帳戶、資源群組和資源的擁有者 (和使用者存取管理員) 可以建立自訂角色以在這些範圍中使用。建立角色的使用者必須能夠對角色的所有 **AssignableScopes** 執行 `Microsoft.Authorization/roleDefinition/write` 作業。

- 誰可以修改自訂角色？ 訂用帳戶、資源群組和資源的擁有者 (和使用者存取管理員) 可以在這些範圍中修改自訂角色。使用者必須能夠對自訂角色的所有 **AssignableScopes** 執行 `Microsoft.Authorization/roleDefinition/write` 作業。

- 誰可以檢視自訂角色？ Azure RBAC 中的所有內建角色允許檢視可用於指派的角色。可以在範圍中執行 `Microsoft.Authorization/roleDefinition/read` 作業的使用者，可以檢視可用於在該範圍中指派的 RBAC 角色。

## 另請參閱
- 在 Azure 入口網站中開始使用 [Azure 角色型存取控制](role-based-access-control-configure.md)。
- 了解如何使用下列各項管理存取權：
	- [PowerShell](role-based-access-control-manage-access-powershell.md)
	- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
	- [REST API](role-based-access-control-manage-access-rest.md)
- [建立存取權變更歷程記錄報告](role-based-access-control-access-change-history-report.md)

<!---HONumber=AcomDC_0330_2016-->