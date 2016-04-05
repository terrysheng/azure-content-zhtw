<properties
	pageTitle="建立存取權變更歷程記錄報告 | Microsoft Azure"
	description="產生一份報告，其中列出您的 Azure 訂用帳戶 (採用角色型存取控制) 在過去 90 天內的所有存取權變更。"
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
	ms.date="03/23/2016"
	ms.author="kgremban"/>

# 建立存取權變更歷程記錄報告

如果您不是 Azure 訂用帳戶或其中資源和資源群組的唯一擁有者，您必須能夠追蹤所有的存取權變更。每當有人授與或撤銷您訂用帳戶中的存取權時，變更就會記錄在 Azure 事件中。您可以建立存取權變更歷程記錄報告，以查看過去 90 天內的所有變更。

## 使用 Azure PowerShell 建立報告
若要在 PowerShell 中建立存取權變更歷程記錄報告，請使用下列命令：

```
Get-AzureRMAuthorizationChangeLog
```

您可以指定您要列出的指派屬性，其中包括下列項目︰

| 屬性 | 說明 |
| -------- | ----------- |
| **Action** | 已授與或已撤銷存取權 |
| **Caller** | 負責存取權變更的擁有者 |
| **Date** | 變更存取權的日期和時間 |
| **DirectoryName** | Azure Active Directory 目錄 |
| **PrincipalName** | 使用者、群組或應用程式的名稱 |
| **PrincipalType** | 指派對象為使用者、群組或應用程式 |
| **RoleId** | 已授與或已撤銷之角色的 GUID |
| **RoleName** | 已授與或已撤銷的角色 |
| **ScopeName** | 訂用帳戶、資源群組或資源的名稱 |
| **ScopeType** | 指派的範圍是訂用帳戶、資源群組或資源 |
| **SubscriptionId** | Azure 訂用帳戶的 GUID |
| **SubscriptionName** | Azure 訂用帳戶的名稱 |

此範例命令會列出過去 7 天訂用帳戶中的所有存取權變更。

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - 螢幕擷取畫面](./media/role-based-access-control-configure/access-change-history.png)

## 使用 Azure CLI 建立報告
若要在 Azure 命令列介面 (CLI) 中建立存取權變更歷程記錄報告，請使用下列命令：
```
azure authorization changelog
```

## 匯出為試算表
若要儲存報告或處理此資料，請將存取權變更匯出為 .csv 檔案。您即可在試算表中檢閱此報告。

![以試算表形式來檢視的變更記錄 - 螢幕擷取畫面](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## 另請參閱
- 開始使用 [Azure 角色型存取控制](role-based-access-control-configure.md)。
- 使用 [Azure RBAC 中的自訂角色](role-based-access-control-custom-roles.md)

<!---HONumber=AcomDC_0330_2016-->