<properties pageTitle="使用 Azure 跨平台命令列介面管理角色存取控制" metaKeywords="ResourceManager, Azure 跨平台命令列介面, Azure 命令列, azure 命令列, azure cli, RBAC" description="使用跨平台命令列介面管理角色存取控制" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Cross-Platform Command-Line Interface" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="11/03/2014" ms.author="guayan" />

# 使用跨平台命令列介面管理角色存取控制 #

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/zh-tw/documentation/articles/xplat-cli-rbac.md" title="Cross-Platform CLI">跨平台 CLI</a></div>

Azure Preview 入口網站以及 Azure 資源管理員 API 裡的角色存取控制 (RBAC) 功能，可讓您深入地管理訂閱與資源的存取。透過這項功能，您可以為 Active Directory 使用者、群組或是服務主體指派特定範圍的一些角色，藉此賦予其存取權限。

在本教學課程中，您將學習如何使用 Azure 跨平台命令列介面 (xplat-cli) 來管理角色存取控制。本課程將帶您逐一了解建立與檢查角色指派的程序。

**預估完成時間：**  15 分鐘

## 必要條件 ##

在使用 xplat-cli 來管理 RBAC 之前，您必須具備以下項目：

- Azure 跨平台命令列介面 0.8.8 版或更新版本。若要安裝最新版本，並讓此版本與 Azure 訂閱建立關聯，請參閱[安裝與設定 Azure 跨平台命令列介面](http://azure.microsoft.com/zh-tw/documentation/articles/xplat-cli/)。
- 另請閱讀以下教學課程，以熟悉如何在 Azure 跨平台命令列介面中設定和使用 Azure 資源管理員：[搭配使用 Azure 跨平台命令列介面與資源管理員](http://azure.microsoft.com/zh-tw/documentation/articles/xplat-cli-azure-resource-manager/)

## 本教學課程內容 ##

* [連線到您的訂閱](#connect)
* [查看現有的角色指派](#check)
* [建立角色指派](#create)
* [驗證權限](#verify)
* [後續步驟](#next)

## <a id="connect"></a>連線到您的訂閱 ##

由於 RBAC 只能搭配 Azure 資源管理員一起運作，因此您必須先切換至 Azure 資源管理員模式。輸入：

    azure config mode arm

如需詳細資訊，請參閱[搭配使用 Azure 跨平台命令列介面與資源管理員](http://azure.microsoft.com/zh-tw/documentation/articles/xplat-cli-azure-resource-manager/)

若要連線至您的 Azure 訂閱，請輸入：

    azure login -u <username>

在命令列提示字元中，輸入您的 Azure 帳戶密碼 (僅使用組織帳戶)。Xplat-cli 會取得您在此帳戶上的所有訂閱項目，並將自己預設為使用第一個訂閱。請注意，使用角色存取控制時，只有當您具備共同管理員身分或是具有某些角色指派時，才能取得訂閱項目的一些權限。 

如果您有多個訂閱，而且想要切換至另一個訂閱，請輸入：

    # 這會顯示帳戶之下的訂閱。
    azure 帳戶清單
    # 使用訂閱名稱來選取您要處理的一個帳戶。
    azure 帳戶集 <訂閱名稱>

如需詳細資訊，請參閱[安裝與設定 Azure 跨平台命令列介面](http://azure.microsoft.com/zh-tw/documentation/articles/xplat-cli/)。

## <a id="check"></a>查看現有的角色指派 ##

現在，我們來看看訂閱中已有哪些角色指派。輸入：

    azure role assignment list

如此會傳回訂閱中的所有角色指派。請注意以下兩點：

1. 您需要擁有訂閱層級的讀取權限。
2. 如果訂閱內含許多角色指派，您可能需要一點時間才能取得所有訂閱資料。

您也可以針對特定使用者，檢查特定範圍內特定角色定義的現有角色指派。輸入：

    azure role assignment list -g group1 --upn <user email> -o Owner

此指令會針對 Azure AD 目錄中具有資源群組「group1」之「Owner」角色指派的特定使用者，傳回該特定使用者的所有角色指派。角色指派可能來自兩個地方：

1. 針對資源群組使用者的「Owner」角色指派。
2. 針對資源群組父系使用者的「Owner」角色指派 (在此案例中為該訂閱)，因為如果您在特定父系資源層級具有權限的話，您將對其所有子系具有相同的權限。

此 Cmdlet 的所有參數都是選擇性參數。您可以組合運用這些參數與不同的篩選器，來檢查角色指派。

## <a id="create"></a>建立角色指派 ##

若要建立角色指派，您需要思考：

- 角色的指派對象：您可以使用下列的 Azure Active Directory Cmdlet 來查看您的目錄中目前有哪些使用者、群組與服務主體。

    `azure ad user list
    azure ad user show
    azure ad group list
    azure ad group show
    azure ad group member list
    azure ad sp list
    azure ad sp show`

- 您想要指派的角色：您可以使用下列 Cmdlet 來查看支援的角色定義。

    `azure role list`

- 您想要指派的範圍：您有三層範圍可以指派：

    - 目前的訂閱
    - 資源群組。若要取得資源群組清單，請輸入 `azure group list`
    - 資源。若要取得資源清單，請輸入 `azure resource list`

然後使用 `azure role assignment create` 來建立角色指派。例如：

 - 此舉會在目前的訂閱層級中，為使用者建立讀取器的角色指派：

    `azure role assignment create --upn <user's email> -o Reader`

- 此舉會在資源群組層級中建立角色指派：

    `PS C:\> azure role assignment create --upn <user's email> -o Contributor -g group1`

- 此舉會在資源層級中建立角色指派：

    `azure role assignment create --upn <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <a id="verify"></a>驗證權限 ##

確認您的帳戶內含一些角色指派之後，就能執行下列指令來實際看到這些角色指派授予您的權限：

    PS C:\> azure group list
    PS C:\> azure resource list

這兩組 Cmdlet 只會傳回您擁有讀取權限的資源群組或資源。而且會同時顯示您擁有的權限。

接著當您嘗試執行其他 Cmdlet 時 (像是 `azure group create`)，如果您沒有權限就會傳回存取拒絕錯誤。

## <a id="next"></a>後續步驟 ##

若要深入了解使用 xplat-cli 管理角色存取控制的詳細資訊，請參閱下列相關主題：

- [Windows Azure 中的角色存取控制](http://azure.microsoft.com/zh-tw/documentation/articles/role-based-access-control-configure/)
- [安裝與設定 Azure 跨平台命令列介面](http://azure.microsoft.com/zh-tw/documentation/articles/xplat-cli/)
- [搭配使用 Azure 跨平台命令列介面與資源管理員](http://azure.microsoft.com/zh-tw/documentation/articles/xplat-cli-azure-resource-manager/)
- [使用資源群組管理您的 Azure 資源](http://azure.microsoft.com/zh-tw/documentation/articles/azure-preview-portal-using-resource-groups)：了解如何在 Azure 管理入口網站中建立和管理資源群組。
- [Azure 部落格](http://blogs.msdn.com/windowsazure)：深入了解 Azure 的新功能。
- [使用 Windows PowerShell 來設定角色存取控制](http://azure.microsoft.com/zh-tw/documentation/articles/role-based-access-control-powershell/)
- [為角色存取控制進行疑難排解](http://azure.microsoft.com/zh-tw/documentation/articles/role-based-access-control-troubleshooting/)
