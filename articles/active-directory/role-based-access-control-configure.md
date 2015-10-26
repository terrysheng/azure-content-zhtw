<properties
	pageTitle="Azure Active Directory AD 角色型存取控制 | Microsoft Azure"
	description="本文說明 Azure 角色型存取控制"
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

# Azure Active Directory 角色型存取控制

## 角色型存取控制
Azure 角色型存取控制 (RBAC) 可以對 Azure 進行更細緻的存取權管理。您可以使用 RBAC，隔離您的開發小組的職責，僅授與使用者執行其工作所需的存取權。

### Azure 的存取權管理基本
每一個 Azure 訂用帳戶都以 Azure Active Directory 為依歸。只有來自該目錄的使用者、群組和應用程式可以獲得存取權，使用 Azure 管理入口網站、Azure 命令列工具和 Azure 管理 API 來管理 Azure 訂用帳戶中的資源。

透過在正確範圍將適當的 RBAC 角色指派給使用者、群組和應用程式，授與存取權。若要將存取權授與整個訂用帳戶，請在訂用帳戶範圍指派角色。若要將存取權授與訂用帳戶內的特定資源群組，請在資源群組範圍指派角色。您可能也會在特定資源指派角色，例如網站、虛擬機器和子網路，只授與該資源的存取權。

![](./media/role-based-access-control-configure/overview.png)

您指派給使用者、群組和應用程式的 RBAC 角色，指定使用者 (或應用程式) 可以在該範圍內管理的資源。

### Azure RBAC 內建角色
Azure RBAC 有適用於所有資源類型的三個基本角色：擁有者、參與者和讀者。擁有者具有所有資源的完整存取權，包括將存取權委派給其他人的權限。參與者可以建立和管理所有類型的 Azure 資源，但是不能將存取權授與其他人。讀者只能檢視現有的 Azure 資源。Azure 中其餘的 RBAC 角色可以管理特定 Azure 資源。例如，虛擬機器參與者角色可以建立和管理虛擬機器，但是無法管理虛擬網路或虛擬機器所連接的子網路。

[RBAC 內建角色](role-based-access-built-in-roles.md)列出 Azure 中可用的內建 RBAC 角色。它會針對每個角色指定內建角色獲得存取權的作業。

### Azure 資源階層和存取繼承
在 Azure 中的每個訂用帳戶只屬於一個目錄，每個資源群組只屬於一個訂用帳戶，每個資源只屬於一個資源群組。您在父範圍授與的存取權會在子範圍繼承。如果您在訂用帳戶範圍授與 Azure AD 群組讀者角色，該群組的成員可以檢視訂用帳戶中的每個資源群組和每個資源。如果您在資源群組範圍授與應用程式參與者角色，它可以管理該資源群組中的所有類型資源，但是無法管理訂用帳戶中的其他資源群組。

### Azure RBAC 與傳統訂用帳戶系統管理員和共同管理員
傳統訂用帳戶系統管理員和共同管理員具有 Azure 訂用帳戶的完整存取權。他們可以使用傳統入口網站 (https://manage.windowsazure.com)，和 Azure 服務管理員 API)，以及新的管理入口網站 (https://portal.azure.com)，和新的 Azure 資源管理員 API)，來管理資源。在 RBAC 模型中，傳統系統管理員會獲指派訂用帳戶範圍的擁有者角色。

更細緻的授權模型 (Azure RBAC) 僅受到新的管理入口網站 (https://portal.azure.com) 和 Azure 資源管理員 API 支援。獲指派 RBAC 角色 (在訂用帳戶/資源群組/資源範圍) 的使用者和應用程式無法使用傳統管理入口網站 (http://manage.windowsazure.com) 和 Azure 服務管理 API。

### 管理授權與資料作業
更細緻的授權模型 (Azure RBAC) 僅支援對 Azure 入口網站的 Azure 資源與 Azure 資源管理員 API 進行管理作業。並非所有 Azure 資源的資料作業都可以透過 RBAC 授權。舉例來說，儲存體帳戶的建立/讀取/更新/刪除作業可以透過 RBAC 控制，但是儲存體帳戶內的 Blob 或資料表的建立/讀取/更新/刪除作業，卻還無法透過 RBAC 來控制。同理，SQL DB 的建立/讀取/更新/刪除作業可以透過 RBAC 來控制，但是 DB 內的 SQL 資料表之建立/讀取/更新/刪除作業卻還無法透過 RBAC 來控制。

## 使用 Azure 管理入口網站管理存取權
### 檢視存取權
在資源群組刀鋒視窗的基本功能區段，選取存取權設定。[使用者] 刀鋒視窗會列出已獲得資源群組存取權的所有使用者、群組和應用程式。存取權是在資源群組上指派或繼承自父訂用帳戶指派。

![](./media/role-based-access-control-configure/view-access.png)

> [AZURE.NOTE]傳統訂用帳戶管理員和共同管理員實際上是新 RBAC 模型中訂用帳戶的擁有者。

### 新增存取權
1. 按一下 [使用者] 刀鋒視窗上的 [新增] 圖示。![](./media/role-based-access-control-configure/grant-access1.png)
2. 選取您想要指派的角色。
3. 搜尋並選取您想要授與存取權的使用者或群組或應用程式。
4. 使用顯示名稱、電子郵件地址和物件識別碼，搜尋使用者、群組和應用程式的目錄。![](./media/role-based-access-control-configure/grant-access2.png)

### 移除存取權
1. 在 [使用者] 刀鋒視窗中，選取您想要移除的角色指派。
2. 按一下指派詳細資料刀鋒視窗上的 [移除] 圖示。
3. 按一下 [是] 以確認移除。

![](./media/role-based-access-control-configure/remove-access1.png)


> [AZURE.NOTE]繼承的指派無法從子領域移除。瀏覽至父範圍然後移除這類指派。


![](./media/role-based-access-control-configure/remove-access2.png)

## 使用 Azure PowerShell 管理存取權
可以使用 Azure PowerShell 工具中的 Azure RBAC 命令管理存取權。

-	使用 `Get-AzureRoleDefinition` 以列出可以指派的 RBAC 角色，以及檢查它們獲得存取權的作業。

-	使用 `Get-AzureRoleAssignment` 以列出在指定的訂用帳戶或資源群組或資源有效的 RBAC 存取權指派。使用 `ExpandPrincipalGroups` 參數以列出指定使用者和使用者為其成員之群組的存取權指派。使用 `IncludeClassicAdministrators` 參數同時列出傳統訂用帳戶系統管理員和共同管理員。

-	使用 `New-AzureRoleAssignment` 以將存取權授與使用者、群組和應用程式。

-	使用 `Remove-AzureRoleAssignment` 以移除存取權。

請參閱[使用 Azure PowerShell 管理存取權](role-based-access-control-manage-access-powershell.md)以取得使用 Azure PowerShell 管理存取權的詳細範例。

## 使用 Azure 命令列介面管理存取權
可以使用 Azure 命令列介面中的 Azure RBAC 命令管理存取權。

-	使用 `azure role list` 以列出可以指派的 RBAC 角色。使用 Azure 角色顯示以檢查它們獲得存取權的作業。

-	使用 `azure role assignment list` 以列出在指定的訂用帳戶或資源群組或資源有效的 RBAC 存取權指派。使用 `expandPrincipalGroups` 選項以列出指定使用者和使用者為其成員之群組的存取權指派。使用 `includeClassicAdministrators` 參數同時列出傳統訂用帳戶系統管理員和共同管理員。

-	使用 `azure role assignment create` 以將存取權授與使用者、群組和應用程式。

-	使用 `azure role assignment delete` 以移除存取權。

請參閱[使用 Azure CLI 管理存取權](role-based-access-control-manage-access-azure-cli.md)以取得使用 Azure CLI 管理存取權的詳細範例。

## 使用存取權變更歷程記錄報告
您的 Azure 訂用帳戶中發生的所有存取權變更都會記錄在 Azure 事件。

### 使用 Azure PowerShell 建立報告
若要建立誰在您的 Azure 訂用帳戶中於哪個範圍對誰授與/撤銷何種存取權的報告，請使用下列 PowerShell 命令：

    Get-AzureAuthorizationChangeLog

### 使用 Azure CLI 建立報告
若要建立誰在您的 Azure 訂用帳戶中於哪個範圍對誰授與/撤銷何種存取權的報告，請使用下列 Azure 命令列介面 (CLI) 命令：

    azure authorization changelog

> [AZURE.NOTE]可以查詢過去 90 天 (一批 15 天) 的存取權變更。

下列範例會列出過去 7 天訂用帳戶中的所有存取權變更。

![](./media/role-based-access-control-configure/access-change-history.png)

### 將存取權變更匯出至試算表
將存取權變更匯出至試算表進行檢閱很方便。

![](./media/role-based-access-control-configure/change-history-spreadsheet.png)

<!---HONumber=Oct15_HO3-->