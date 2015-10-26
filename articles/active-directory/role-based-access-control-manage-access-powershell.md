<properties
	pageTitle="使用 Azure PowerShell 管理角色型存取控制 (RBAC) | Microsoft Azure"
	description="如何使用 Azure PowerShell 管理 RBAC，包括列出角色、指派角色，以及刪除角色指派。"
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="10/12/2015"
	ms.author="inhenk"/>

# 使用 Azure PowerShell 管理角色型存取控制 (RBAC)
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## 列出 RBAC 角色
### 列出所有可用的角色
若要列出可以指派的 RBAC 角色，以及若要檢查它們獲得存取權的作業，請使用：

		Get-AzureRoleDefinition

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### 列出角色的動作
若要列出特定角色的動作，請使用：

    Get-AzureRoleDefinition <role name>

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## 列出存取權
### 列出所選取之訂用帳戶中的所有角色指派
若要列出在指定的訂用帳戶、資源或資源群組有效的 RBAC 存取權指派，請使用：

    Get-AzureRoleAssignment

###	列出資源群組上有效的角色指派
若要列出資源群組的存取權指派，請使用：

    Get-AzureRoleAssignment -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### 列出使用者的角色指派，包括指派給使用者群組的角色
若要列出指定使用者和使用者為其成員之群組的存取權指派，請使用：

    Get-AzureRoleAssignment -ExpandPrincipalGroups

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### 列出傳統服務管理員和共同管理員角色指派
若要列出傳統訂用帳戶管理員和共同管理員的存取權指派，請使用：

    Get-AzureRoleAssignment -IncludeClassicAdministrators

## 授與存取權
### 搜尋物件識別碼
若要使用下列命令順序，您必須先找到物件識別碼。假設您已經知道您正在使用的訂用帳戶識別碼，否則，請參閱 MSDN 上的[Get-azuresubscription](https://msdn.microsoft.com/library/dn495302.aspx)。

#### 尋找 Azure AD 群組的物件識別碼
若要取得 Azure AD 群組的物件識別碼，請使用：

    Get-AzureADGroup -SearchString <group name in quotes>

#### 尋找 Azure AD 服務主體的物件識別碼
若要取得 Azure AD 服務主體的物件識別碼，請使用：Get AzureADServicePrincipal SearchString <service name in quotes>

### 將角色指派給訂用帳戶範圍中的群組
若要將存取權授與訂用帳戶範圍中的群組，請使用：

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### 將角色指派給訂用帳戶範圍中的應用程式
若要將存取權授與訂用帳戶範圍中的應用程式，請使用：

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### 將角色指派給資源群組範圍中的使用者
若要將存取權授與資源群組範圍中的使用者，請使用：

    New-AzureRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### 將角色指派給資源範圍中的群組
若要將存取權授與資源範圍中的群組，請使用：

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## 移除存取
若要移除使用者、群組和應用程式的存取權，請使用：

    Remove-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## RBAC 主題
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=Oct15_HO3-->