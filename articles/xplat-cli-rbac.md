<properties pageTitle="Managing Role-Based Access Control with Azure Cross-Platform Command-Line Interface" metaKeywords="ResourceManager, Azure cross-platform command-line interface, Azure command-line, azure command-line, azure cli, RBAC" description="Managing role-based access control with cross-platform command-line interface" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Cross-Platform Command-Line Interface" authors="guayan" solutions="" manager="mohisri" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# 使用跨平台命令列介面管理角色存取控制

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/zh-tw/documentation/articles/xplat-cli-rbac.md" title="跨平台 CLI">跨平台 CLI</a></div>

在 Azure 預覽入口網站和 Azure 資源管理員 API 中，以角色為基礎的存取控制 (RBAC) 可讓您以精細的層級管理訂閱存取。在這項功能的協助下，您可以在特定範圍內將某些角色指派給 Active Directory 使用者、群組或服務主體，藉此授與存取權限。

在本教學課程中，您將學習如何使用 Azure 跨平台命令列介面 (xplat-cli) 來管理 RBAC。本課程將帶您逐一了解建立與檢查角色指派的程序。

<strong>預估完成時間：</strong>15 分鐘

## 必要條件

在使用 xplat-cli 來管理 RBAC 之前，您必須具備以下項目：

-   Azure 跨平台命令列介面 0.8.8 版或更新版本。若要安裝最新版本，並讓此版本與 Azure 訂閱建立關聯，請參閱[安裝與設定 Azure 跨平台命令列介面][安裝與設定 Azure 跨平台命令列介面]。
-   另請閱讀以下教學課程，以熟悉如何在 Azure 跨平台命令列介面中設定和使用 Azure 資源管理員：[搭配使用 Azure 跨平台命令列介面與資源管理員][搭配使用 Azure 跨平台命令列介面與資源管理員]

## 本教學課程內容

-   [連線到您的訂閱][連線到您的訂閱]
-   [查看現有的角色指派][查看現有的角色指派]
-   [建立角色指派][建立角色指派]
-   [驗證權限][驗證權限]
-   [後續步驟][後續步驟]

## <span id="connect"></span></a>連線到您的訂閱

由於 RBAC 只能搭配 Azure 資源管理員使用，您必須切換至 Azure 資源管理員模式。請輸入：

    azure config mode arm

如需詳細資訊，請參閱[搭配使用 Azure 跨平台命令列介面與資源管理員][搭配使用 Azure 跨平台命令列介面與資源管理員]

若要連線到您的 Azure 訂閱，請輸入：

    azure login -u <username>

在命令列提示字元中輸入 Azure 帳戶密碼 (僅支援組織識別碼)。Xplat-cli 會取得此帳戶的所有訂閱，並自行將第一個訂閱當做預設值。請注意，使用 RBAC 時，只有當您具備共同管理員身分或是具有某些角色指派時，才有取得訂閱項目的一些權限。

如果您有多個訂閱，而且想要切換至另一個訂閱，請輸入：

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

如需詳細資訊，請參閱[安裝與設定 Azure 跨平台命令列介面][安裝與設定 Azure 跨平台命令列介面]。

## <span id="check"></span></a>查看現有的角色指派

現在，我們來看看訂閱中已有哪些角色指派。輸入：

    azure role assignment list

如此會傳回訂閱中的所有角色指派。請注意以下兩點：

1.  您需要擁有訂閱層級的讀取權限。
2.  如果訂閱內含許多角色指派，您可能需要一點時間才能取得所有訂閱資料。

您也可以在特定範圍中，針對特定角色定義查看指派給特定使用者的現有角色指派。輸入：

    azure role assignment list -g group1 --mail <user's email> -o Owner

此指令會針對 AD 租用戶中具有資源群組「group1」之「Owner」角色指派的特定使用者，傳回該特定使用者的所有角色指派。角色指派可能來自兩個地方：

1.  針對資源群組使用者的「Owner」角色指派。
2.  針對資源群組父系使用者的「Owner」角色指派 (在此案例中為訂閱)，因為如果您在特定層級擁有訂閱，將對所有子系具有相同的權限。

此 Cmdlet 的所有參數都是選擇性參數。您可以組合運用這些參數與不同的篩選器，來檢查角色指派。

## <span id="create"></span></a>建立角色指派

若要建立角色指派，您需要考慮以下事項：

-   角色的指派對象：您可以使用下列 Azure Active Directory Cmdlet 來查看 AD 租用戶中有哪些使用者、群組及服務主體。

    `azure ad user list 
     azure ad user show 
     azure ad group list 
     azure ad group show 
     azure ad group member list 
     azure sp list 
     azure sp show`

-   您想要指派的角色：您可以使用下列 Cmdlet 來查看支援的角色定義。

    `azure role list`

-   您想要指派的範圍：範圍分為三個層級

    -   目前的訂閱
    -   資源群組；若要取得資源群組清單，請輸入 `azure group list`
    -   資源；若要取得資源清單，請輸入 `azure resource list`

接下來，使用 `azure role assignment create` 來建立角色指派。例如：

-   如此會在目前的訂閱層級中，為使用者建立讀者的角色指派。

    `azure role assignment create --mail <user's email> -o Reader`

-   如此會在資源群組層級中建立角色指派

    `PS C:PS C:\> azure role assignment create --mail <user's email> -o Contributor -g group1`gt; azure role assignment create --mail \<user's email\> -o Contributor -g group1</code>

-   如此會在資源層級中建立角色指派

    `azure role assignment create --mail <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <span id="verify"></span></a>驗證權限

確認您的帳戶內含一些角色指派之後，就能執行下列指令來實際看到這些角色指派授予您的權限：

    PS C:\> azure group list
    PS C:\> azure resource list

這兩組 Cmdlet 只會傳回您擁有讀取權限的資源群組或資源。而且會同時顯示您擁有的權限。

接下來，當您嘗試執行 `azure group create` 等其他 Cmdlet 時，如果您沒有權限，將會出現拒絕存取錯誤。

## <span id="next"></span></a>後續步驟

若要深入了解使用 xplat-cli 管理角色存取控制的詳細資訊，請參閱下列相關主題：

-   [安裝與設定 Azure 跨平台命令列介面][安裝與設定 Azure 跨平台命令列介面]
-   [搭配使用 Azure 跨平台命令列介面與資源管理員][搭配使用 Azure 跨平台命令列介面與資源管理員]
-   [使用資源群組管理您的 Azure 資源][使用資源群組管理您的 Azure 資源]：了解如何在 Azure 管理入口網站中建立與管理資源群組。
-   [Azure 部落格][Azure 部落格]：深入了解 Azure 的新功能。

  [安裝與設定 Azure 跨平台命令列介面]: http://azure.microsoft.com/zh-tw/documentation/articles/xplat-cli/
  [搭配使用 Azure 跨平台命令列介面與資源管理員]: http://azure.microsoft.com/zh-tw/documentation/articles/xplat-cli-azure-resource-manager/
  [連線到您的訂閱]: #connect
  [查看現有的角色指派]: #check
  [建立角色指派]: #create
  [驗證權限]: #verify
  [後續步驟]: #next
  [使用資源群組管理您的 Azure 資源]: http://azure.microsoft.com/zh-tw/documentation/articles/azure-preview-portal-using-resource-groups
  [Azure 部落格]: http://blogs.msdn.com/windowsazure
