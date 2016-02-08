<properties
	pageTitle="使用 Azure CLI 管理角色型存取控制 (RBAC) | Microsoft Azure"
	description="了解如何使用 Azure 命令列介面，藉由列出角色和角色的動作、將角色指派給訂用帳戶和應用程式範圍，來管理角色型存取 (RBAC)。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="01/22/2016"
	ms.author="kgremban"/>

# 使用 Azure 命令列介面管理角色型存取控制

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## 列出角色型存取控制 (RBAC) 角色
###	列出所有可用的角色
若要列出所有可用的角色，請使用：

		azure role list

下列範例顯示*所有可用角色*的清單。

![RBAC Azure 命令列 - azure 角色清單 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

###	列出角色的動作
若要列出角色的動作，請使用：

    azure role show <role in quotes>

下列範例顯示*參與者*與*虛擬機器參與者*角色的動作。

![RBAC Azure 命令列 - azure 角色顯示 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##	列出存取權
###	列出資源群組上有效的角色指派
若要列出資源群組上有效的角色指派，請使用：

    azure role assignment list --resource-group <resource group name>

下列範例顯示 *pharma-sales-projecforcast* 群組上有效的角色指派。

![RBAC Azure 命令列 - 依群組顯示的 azure 角色指派清單 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

###	列出使用者的角色指派，包括指派給使用者群組的角色

下列範例顯示使用者 **sameert@aaddemo.com* 上有效的角色指派。

![RBAC Azure 命令列 - 依使用者顯示的 azure 角色指派清單 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##	授與存取權
一旦您已識別您想要指派的角色後，若要授與存取權，請使用：

    azure role assignment create

###	將角色指派給訂用帳戶範圍中的群組
若要將角色指派給訂用帳戶範圍中的群組，請使用：

	azure role assignment create --objId  <group's object id> --role <name of role> --scope <subscription/subscription id>

下列範例會將*讀者*角色指派給*訂用帳戶*範圍中的 *Christine Koch 小組*。

![RBAC Azure 命令列 - 群組所建立的 azure 角色指派 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

###	將角色指派給訂用帳戶範圍中的應用程式
若要將角色指派給訂用帳戶範圍中的應用程式，請使用：

    azure role assignment create --objId  <applications's object id> --role <name of role> --scope <subscription/subscription id>

下列範例會將*參與者*角色授與所選取之訂用帳戶上的 *Azure AD* 應用程式。

 ![RBAC Azure 命令列 - 應用程式所建立的 azure 角色指派](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

###	將角色指派給資源群組範圍中的使用者
若要將角色指派給資源群組範圍中的使用者，請使用：

	azure role assignment create --signInName  <user's email address> --roleName <name of role in quotes> --resourceGroup <resource group name>

下列範例會將*虛擬機器參與者*角色授與 *Pharma-Sales-ProjectForcast* 資源群組範圍中的使用者 **samert@aaddemo.com*。

![RBAC Azure 命令列 - 使用者所建立的 azure 角色指派 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

###	將角色指派給資源範圍中的群組
若要將角色指派給資源範圍中的群組，請使用：

    azure role assignment create --objId  <group id> --roleName <name of role in quotes> --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

下列範例會將*虛擬機器參與者*角色授與*子網路*上的 *Azure AD* 群組。

![RBAC Azure 命令列 - 群組所建立的 azure 角色指派 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##	移除存取
若要移除角色指派，請使用：

    azure role assignment delete --objId <object id to from which to remove role> --roleName <role name>

下列範例會從 *Pharma-Sales-ProjectForcast* 資源群組上的 **sammert@aaddemo.com* 移除 *虛擬機器參與者*角色指派。然後，它會從訂用帳戶上的群組移除角色指派。

![RBAC Azure 命令列 - azure 角色指派刪除 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## 建立自訂角色
若要建立自訂角色，請使用 `azure role create` 命令。

下列範例會建立名為 *Virtual Machine Operator* 的自訂角色，該角色會授與 *Microsoft.Compute*、*Microsoft.Storage*，和 *Microsoft.Network* 資源提供者對所有讀取作業的存取權限，以及授與啟動、重新啟動，和監視虛擬機器的存取權限。自訂角色可用於兩個訂用帳戶中。這個範例使用 JSON 檔案做為輸入。

![JSON - 自訂角色定義 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![RBAC Azure 命令列 - azure 角色建立 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## 修改自訂角色

若要先修改自訂角色，請使用 `azure role show` 命令擷取角色定義。接著，對角色定義進行想要的變更。最後，使用 `azure role set` 儲存已修改的角色定義。

下列範例將 Microsoft.Insights/diagnosticSettings/* 作業加入 **Actions**，並將 Azure 訂用帳戶加入 Virtual Machine Operator 自訂角色的 **AssignableScopes**。

![JSON - 修改自訂角色定義 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![RBAC Azure 命令列 - azure 角色設定 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## 刪除自訂角色

若要刪除自訂角色，請先使用 `azure role show` 命令找出角色的 **Id**。接著，使用 `azure role delete` 命令，指定 **Id** 來刪除角色。

下列範例會移除 *Virtual Machine Operator* 自訂角色

![RBAC Azure 命令列 - azure 角色刪除 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## 列出自訂角色

若要列出範圍中可供指派的角色，請使用 `azure role list` 命令。

下列範例會列出選取的訂用帳戶中所有可供指派的角色。

![RBAC Azure 命令列 - azure 角色清單 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

在下列範例中，*Virtual Machine Operator* 自訂角色無法在 *Production4* 訂用帳戶中使用，因為該訂用帳戶並沒有在角色的 **AssignableScopes** 中。

![RBAC Azure 命令列 - 自訂角色的 azure 角色清單 - 螢幕擷取畫面](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)





## RBAC 主題
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0128_2016-->