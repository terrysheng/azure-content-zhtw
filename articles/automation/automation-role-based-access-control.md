<properties 
   pageTitle="Azure 自動化中的角色型存取控制 | Microsoft Azure"
   description="角色型存取控制 (RBAC) 可以啟用對 Azure 資源的存取權管理。本文說明如何在 Azure 自動化中設定 RBAC。"
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/13/2016"
   ms.author="sngun"/>

# Azure 自動化中的角色型存取控制

## 角色型存取控制

角色型存取控制 (RBAC) 可以啟用對 Azure 資源的存取權管理。您可以使用 [RBAC](../active-directory/role-based-access-control-configure.md) 來區隔小組內的職責，僅授與使用者、群組和應用程式執行作業所需的存取量。使用 Azure 入口網站、Azure 命令列工具或 Azure 管理 API 的使用者，可以獲授與角色型存取權。

## 自動化帳戶中的 RBAC

在 Azure 自動化中，於自動化帳戶範圍將適當的 RBAC 角色指派給使用者、群組和應用程式，即可授與存取權。以下是自動化帳戶支援的內建角色：

|**角色** | **說明** |
|:--- |:---|
| 擁有者 | 擁有者角色允許存取自動化帳戶中的所有資源和動作，包括存取提供給其他使用者、群組和應用程式，以管理自動化帳戶。 |
| 參與者 | 參與者角色可讓您管理各個項目，修改其他使用者的自動化帳戶存取權限除外。 |
| 讀取者 | 讀取者角色可讓您檢視自動化帳戶中的所有資源，但無法進行任何變更。 |
| 自動化操作員 | 自動化操作員角色可讓您執行作業的工作，例如啟動、停止、暫停、繼續和排程作業。如果您想要保護認證資產和 Runbook 等自動化帳戶資源不被檢視或修改，但仍然允許組織的成員來執行這些 Runbook，這個角色會有所幫助。[自動化操作員動作](../active-directory/role-based-access-built-in-roles.md#automation-operator)會列出自動化帳戶和其資源的自動化操作員角色所支援的動作。 |
| 使用者存取系統管理員 | 使用者存取系統管理員角色可讓您管理 Azure 自動化帳戶的使用者存取。 |

在本文中，我們將逐步引導您如何在 Azure 自動化中設定 RBAC。

## 使用 Azure 入口網站為您的自動化帳戶設定 RBAC

1.	登入 [Azure 入口網站](http://portal.azure.com/)，並從 [自動化帳戶] 刀鋒視窗開啟您的自動化帳戶。  

2.	按一下右上角的 [存取] 控制項。這會開啟 [使用者] 刀鋒視窗，您可以在其中加入新使用者、群組及應用程式來管理您的自動化帳戶，並檢視可以為自動化帳戶設定的現有角色。

    ![[存取] 按鈕](media/automation-role-based-access-control/automation-01-access-button.png)

>[AZURE.NOTE]**訂用帳戶管理員**已經存在做為預設的使用者。訂用帳戶管理員 Active Directory 群組包含服務系統管理員和 Azure 訂用帳戶的共同管理員。服務系統管理員為您的 Azure 訂用帳戶和其資源的擁有者，而且會具有對自動化帳戶繼承的擁有者角色。這表示存取權是對訂用帳戶的**服務系統管理員和共同管理員****繼承**，並且對所有其他的使用者**指派**。按一下 [訂用帳戶管理員] 來檢視有關其權限的其他詳細資料。

### 加入新使用者並指派角色

1.	從 [使用者] 刀鋒視窗中，按一下 [加入] 以開啟 [加入存取] 刀鋒視窗，您可以在其中加入使用者、群組或應用程式，並將角色指派給他們。  

    ![新增使用者](media/automation-role-based-access-control/automation-02-add-user.png)

2.	從可用角色的清單中選取角色。我們將選擇**讀取者**角色，但是您可以選擇自動化帳戶支援的任何可用的內建角色，或您已定義的任何自訂角色。

    ![選取角色](media/automation-role-based-access-control/automation-03-select-role.png)

3.	按一下 [加入使用者] 以開啟 [加入使用者] 刀鋒視窗。如果您已加入任何使用者、群組或應用程式以管理您的訂用帳戶，則會列出這些使用者，您可以選取他們來加入存取權。如果沒有列出任何使用者，或未列出您有興趣加入的使用者，則請按一下 [邀請] 來開啟 [邀請來賓] 刀鋒視窗，您可以在此邀請具有有效 Microsoft 帳戶電子郵件地址 (例如 Outlook.com、OneDrive 或 Xbox Live ID) 的使用者。一旦您輸入使用者的電子郵件地址，按一下 [選取] 以加入使用者，然後按一下 [確定]。

    ![新增使用者](media/automation-role-based-access-control/automation-04-add-users.png)
 
現在您應該會看到使用者加入至 [使用者] 刀鋒視窗，並且獲指派**讀取者**角色。

![列出使用者](media/automation-role-based-access-control/automation-05-list-users.png)

您也可以從 [角色] 刀鋒視窗指派角色給使用者。從 [使用者] 刀鋒視窗按一下 [角色] 以開啟 [角色]刀鋒視窗。從這個刀鋒視窗中，您可以檢視角色的名稱、指派給該角色的使用者和群組數目。

![從 [使用者] 刀鋒視窗指派角色](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)
   
>[AZURE.NOTE]只能在自動化帳戶層級，而非低於自動化帳戶的任何資源中設定以角色為基礎的存取控制。

您可以將多個角色指派給使用者、群組或應用程式。例如，如果我們加入**自動化操作員**角色連同**讀取者角色**給使用者，則他們可以檢視所有的自動化資源，以及執行 Runbook 作業。您可以展開下拉式清單以檢視指派給使用者的角色清單。

![檢視多個角色](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)
 
### 移除使用者

您可以移除未管理自動化帳戶的使用者，或不再為組織工作之使用者的存取權限。以下是移除使用者的步驟：

1.	從 [使用者] 刀鋒視窗中，選取您想要移除的角色指派。

2.	按一下指派詳細資料刀鋒視窗上的 [移除] 按鈕。

3.	按一下 [是] 以確認移除。

    ![移除使用者](media/automation-role-based-access-control/automation-08-remove-users.png)

## 角色指派的使用者

當獲派角色的使用者登入他們的自動化帳戶時，他們可以看到擁有者的帳戶列於**預設目錄**清單中。若要檢視他們被加入的自動化帳戶，他們必須將預設目錄切換到擁有者的預設目錄。

![預設目錄](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)

### 自動化操作員角色的使用者經驗

當指派為自動化操作員角色的使用者檢視指派給他的自動化帳戶時，只能檢視在自動化帳戶中建立的 Runbook、Runbook 作業和排程的清單，但無法檢視其定義。他們可以啟動、停止、暫停、繼續或排程 Runbook 作業。使用者將無法存取其他自動化資源，例如組態、混合式背景工作群組或 DSC 節點。

![沒有資源的存取權](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

當使用者按一下 Runbook 時，不會提供檢視來源或編輯 Runbook 的命令，因為自動化操作員角色不允許存取它們。

![沒有編輯 Runbook 的存取權](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)

使用者將可檢視及建立排程，但無法存取任何其他資產類型。

![沒有資產的存取權](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)

這位使用者也沒有存取權可以檢視與 Runbook 相關聯的 Webhook

![沒有 Webhook 的存取權](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)

## 使用 Azure PowerShell 為您的自動化帳戶設定 RBAC

使用下列 [Azure PowerShell Cmdlet](../active-directory/role-based-access-control-manage-access-powershell.md) 也可以將角色型存取設定到自動化帳戶。

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) 會列出 Azure Active Directory RBAC 中可用的所有角色。您可以使用這個命令搭配**名稱**屬性，列出具有特定角色的所有使用者。**範例：**![取得角色定義](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) 會列出在指定範圍的 Azure RBAC 角色指派。如果沒有任何參數，此命令會傳回在訂用帳戶下所做的所有角色指派。使用 **ExpandPrincipalGroups** 參數以列出指定使用者和使用者為其成員之群組的存取權指派。**範例：**使用下列命令來列出自動化帳戶中的所有使用者以及其角色。

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![取得角色指派](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) 可在特定範圍授與使用者、群組和應用程式的存取權。**範例：**使用下列命令來為自動化帳戶範圍內的使用者建立新角色「自動化操作員」。

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![新角色指派](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• 使用 [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) 來移除特定範圍內指定的使用者、群組或應用程式的存取權。**範例：**使用下列命令來為自動化帳戶範圍內的使用者建立新角色「自動化操作員」。

    Remove-AzureRmRoleAssignment -SignInName "<sign-in Id of a user you wish to remove>" -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

在上述 Cmdlet 中，請以您的帳戶詳細資料取代登入名稱、訂用帳戶 ID、資源群組名稱和自動化帳戶名稱。當系統提示您繼續刪除角色指派時選擇 [是]。


## 後續步驟
-  如需對 Azure 自動化設定 RBAC 的不同方式的詳細資訊，請參閱[使用 Azure PowerShell 管理 RBAC](../active-directory/role-based-access-control-manage-access-powershell.md)。
- 如需以不同方式啟動 Runbook 的詳細資訊，請參閱[啟動 Runbook](automation-starting-a-runbook.md)
- 如需不同類型的詳細資訊，請參閱 [Azure 自動化 Runbook 類型](automation-runbook-types.md)

<!---HONumber=AcomDC_0121_2016-->