<properties pageTitle="Managing Role-Based Access Control with Windows PowerShell" metaKeywords="ResourceManager, PowerShell, Azure PowerShell, RBAC" description="Managing role-based access control with Windows PowerShell" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Windows PowerShell" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# 使用 Windows PowerShell 管理角色存取控制

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/zh-tw/documentation/articles/xplat-cli-rbac.md" title="跨平台 CLI">跨平台 CLI</a></div>

在 Azure 預覽入口網站和 Azure 資源管理員 API 中，以角色為基礎的存取控制 (RBAC) 可讓您以精細的層級管理訂閱存取。透過這項功能，您可以為 Active Directory 使用者、群組或是服務主體指派特定範圍的一些角色，藉此賦予其存取權限。

在本教學課程裡，您將學習如何使用 Windows PowerShell 來管理 RBAC。本課程將帶您逐一了解建立與檢查角色指派的程序。

<strong>預估完成時間：</strong>15 分鐘

## 必要條件

在使用 Windows PowerShell 來管理 RBAC 之前，您必須具備下列項目：

-   Windows PowerShell 3.0 或 4.0 版本。若要尋找 Windows PowerShell 版本，請輸入：`$PSVersionTable`並確認 `PSVersion` 的值是 3.0 或 4.0。若要安裝相容版本，請參閱 [Windows Management Framework 3.0][Windows Management Framework 3.0] 或 [Windows Management Framework 4.0][Windows Management Framework 4.0]。

-   Azure PowerShell 0.8.8 或更新版本。若要安裝最新版本，並將它與 Azure 訂閱建立關聯，請參閱[如何安裝和設定 Windows Azure PowerShell][如何安裝和設定 Windows Azure PowerShell] (英文)。

本教學課程是專為 Windows PowerShell 初學者所設計的，但它會假設您已了解基本概念，例如模組、Cmdlet 和工作階段。如需 Windows PowerShell 的詳細資訊，請參閱[開始使用 Windows PowerShell][開始使用 Windows PowerShell] (英文)。

若要取得您在本教學課程中任何所見 Cmdlet 的詳細說明，請使用 Get-Help Cmdlet。

    Get-Help <cmdlet-name> -Detailed

例如，如需取得 Add-AzureAccount Cmdlet 的說明，請輸入：

    Get-Help Add-AzureAccount -Detailed

請同時閱讀下列教學課程，熟悉在 Windows PowerShell 中設定與使用 Azure 資源管理員的程序：

-   [如何安裝和設定 Azure PowerShell][如何安裝和設定 Azure PowerShell]
-   [將 Windows PowerShell 與資源管理員搭配使用][將 Windows PowerShell 與資源管理員搭配使用]

## 本教學課程內容

-   [連線到您的訂閱][連線到您的訂閱]
-   [查看現有的角色指派][查看現有的角色指派]
-   [建立角色指派][建立角色指派]
-   [驗證權限][驗證權限]
-   [後續步驟][後續步驟]

## <span id="connect"></span></a>連線到您的訂閱

由於 RBAC 只能搭配 Azure 資源管理員使用，您必須切換至 Azure 資源管理員模式。請輸入：

    PS C:\> Switch-AzureMode -Name AzureResourceManager

如需詳細資訊，請參閱[搭配使用 Windows PowerShell 與資源管理員][將 Windows PowerShell 與資源管理員搭配使用]。

若要連線到您的 Azure 訂閱，請輸入：

    PS C:\> Add-AzureAccount

在快顯瀏覽器控制項中，輸入您的 Azure 帳戶使用者名稱與密碼。PowerShell 會取得您在此帳戶上的所有訂閱項目，並假設 PowerShell 預設會使用第一個訂閱。請注意，使用 RBAC 時，只有當您具備共同管理員身分或是具有某些角色指派時，才有取得訂閱項目的一些權限。

如果您有多個訂閱，而且想要切換至另一個訂閱，請輸入：

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

如需詳細資訊，請參閱[如何安裝及設定 Azure PowerShell][如何安裝和設定 Azure PowerShell]。

## <span id="check"></span></a>查看現有的角色指派

現在，我們來看看訂閱中已有哪些角色指派。輸入：

    PS C:\> Get-AzureRoleAssignment

如此會傳回訂閱中的所有角色指派。請注意以下兩點：

1.  您需要擁有訂閱層級的讀取權限。
2.  如果訂閱內含許多角色指派，您可能需要一點時間才能取得所有訂閱資料。

您也可以在特定範圍中，針對特定角色定義查看指派給特定使用者的現有角色指派。輸入：

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

此指令會針對 AD 租用戶中具有資源群組「group1」之「Owner」角色指派的特定使用者，傳回該特定使用者的所有角色指派。角色指派可能來自兩個地方：

1.  針對資源群組使用者的「Owner」角色指派。
2.  針對資源群組父系使用者的「Owner」角色指派 (在此案例中為訂閱)，因為如果您在特定層級擁有訂閱，將對所有子系具有相同的權限。

此 Cmdlet 的所有參數都是選擇性參數。您可以組合運用這些參數與不同的篩選器，來檢查角色指派。

## <span id="create"></span></a>建立角色指派

若要建立角色指派，您需要考慮以下事項：

-   角色的指派對象：您可以使用下列 Azure Active Directory Cmdlet 來查看 AD 租用戶中有哪些使用者、群組及服務主體。

    `PS C:\> Get-AzureADUser
    PS C:\> Get-AzureADGroup
    PS C:\> Get-AzureADGroupMember
    PS C:\> Get-AzureADServicePrincipal` 

-   您想要指派的角色：您可以使用下列 Cmdlet 來查看支援的角色定義。

    `PS C:\> Get-AzureRoleDefinition`

-   您想要指派的範圍：範圍分為三個層級

    -   目前的訂閱
    -   資源群組；若要取得資源群組清單，請輸入 `PS C:\> Get-AzureResourceGroup`
    -   資源群組；若要取得資源清單，請輸入 `PS C:\> Get-AzureResource`

接著使用 `New-AzureRoleAssignment` 來建立角色指派。例如：

-   如此會在目前的訂閱層級中，為使用者建立讀者的角色指派。

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Reader`

-   如此會在資源群組層級中建立角色指派

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Contributor -ResourceGroupName group1`

-   如此會在資源層級中建立角色指派

    `PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId`

## <span id="verify"></span></a>驗證權限

確認您的帳戶內含一些角色指派之後，就能執行下列指令來實際看到這些角色指派授予您的權限：

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

這兩組 Cmdlet 只會傳回您擁有讀取權限的資源群組或資源。而且會同時顯示您擁有的權限。

接著當您嘗試執行其他 Cmdlet 時 (像是 `New-AzureResourceGroup`)，如果您沒有權限就會傳回存取拒絕錯誤。

## <span id="next"></span></a>後續步驟

若要深入了解使用 Windows PowerShell 管理角色存取控制的詳細資訊，請參閱下列相關主題：

-   [Windows Azure 中的角色存取控制][Windows Azure 中的角色存取控制]
-   [Azure 資源管理員 Cmdlet][Azure 資源管理員 Cmdlet]：了解如何使用 AzureResourceManager 模組中的 Cmdlet。
-   [使用資源群組管理您的 Azure 資源][使用資源群組管理您的 Azure 資源]：了解如何在 Azure 管理入口網站中建立與管理資源群組。
-   [Azure 部落格][Azure 部落格]：深入了解 Azure 的新功能。
-   [Windows PowerShell 部落格][Windows PowerShell 部落格]：深入了解 Windows PowerShell 的新功能。
-   ["Hey, Scripting Guy!"部落格]["Hey, Scripting Guy!"部落格]：從 Windows PowerShell 社群中取得實際的秘訣及訣竅。
-   [使用 XPLAT CLI 設定角色存取控制][使用 XPLAT CLI 設定角色存取控制]
-   [為角色存取控制進行疑難排解][為角色存取控制進行疑難排解]

  [Windows PowerShell]: /zh-tw/documentation/articles/powershell-rbac.md "Windows PowerShell"
  [跨平台 CLI]: /zh-tw/documentation/articles/xplat-cli-rbac.md "跨平台 CLI"
  [Windows Management Framework 3.0]: http://www.microsoft.com/zh-tw/download/details.aspx?id=34595
  [Windows Management Framework 4.0]: http://www.microsoft.com/zh-tw/download/details.aspx?id=40855
  [如何安裝和設定 Windows Azure PowerShell]: http://www.windowsazure.com/zh-tw/documentation/articles/install-configure-powershell/
  [開始使用 Windows PowerShell]: http://technet.microsoft.com/zh-tw/library/hh857337.aspx
  [如何安裝和設定 Azure PowerShell]: http://azure.microsoft.com/zh-tw/documentation/articles/install-configure-powershell/
  [將 Windows PowerShell 與資源管理員搭配使用]: http://azure.microsoft.com/zh-tw/documentation/articles/powershell-azure-resource-manager/
  [連線到您的訂閱]: #connect
  [查看現有的角色指派]: #check
  [建立角色指派]: #create
  [驗證權限]: #verify
  [後續步驟]: #next
  [Windows Azure 中的角色存取控制]: http://azure.microsoft.com/zh-tw/documentation/articles/role-based-access-control-configure/
  [Azure 資源管理員 Cmdlet]: http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409
  [使用資源群組管理您的 Azure 資源]: http://azure.microsoft.com/zh-tw/documentation/articles/azure-preview-portal-using-resource-groups
  [Azure 部落格]: http://blogs.msdn.com/windowsazure
  [Windows PowerShell 部落格]: http://blogs.msdn.com/powershell
  ["Hey, Scripting Guy!"部落格]: http://blogs.technet.com/b/heyscriptingguy/
  [使用 XPLAT CLI 設定角色存取控制]: http://azure.microsoft.com/zh-tw/documentation/articles/role-based-access-control-xplat-cli/
  [為角色存取控制進行疑難排解]: http://azure.microsoft.com/zh-tw/documentation/articles/role-based-access-control-troubleshooting/
