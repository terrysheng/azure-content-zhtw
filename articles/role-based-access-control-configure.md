<properties title="Role Based Access Control in Azure Preview Portal" pageTitle="Azure Preview 入口網站中的角色存取控制" description="Describes how role based access control works and how to set it up" metaKeywords="" services="multiple" solutions="" documentationCenter="" authors="justinha" videoId="" scriptId="" manager="terrylan" />

<tags ms.service="multiple" ms.devlang="dotnet" ms.topic="article" ms.tgt_pltfrm="Ibiza" ms.workload="infrastructure-services" ms.date="09/12/2014" ms.author="justinha;Justinha@microsoft.com" />

<!--This is a basic template that shows you how to use mark down to create a topic that includes a TOC, sections with subheadings, links to other azure.microsoft.com topics, links to other sites, bold text, italic text, numbered and bulleted lists, code snippets, and images. For fancier markdown, find a published topic and copy the markdown or HTML you want. For more details about using markdown, see http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->

<!--Properties section (above): this is required in all topics. Please fill it out!-->

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Azure Preview 入口網站中的角色存取控制 

<p>我們在 Azure Preview 入口網站中新增了對角色存取控制 (RBAC) 的支援，希望協助組織輕鬆又準確地滿足其存取管理需求。此<a href="http://go.microsoft.com/fwlink/?LinkId=511576" target="_blank">部落格文章</a>將快速介紹各項功能，讓您有初步了解。本主題詳細說明各項概念，並涵蓋其他使用案例。 

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

##目錄##

* [Azure 中的 RBAC](#whatisrbac) 
* [RBAC 與訂閱共同管理員的並存機制](#coexist)
* [管理授權與資料作業](#authmgmt)
* [如何新增與移除存取](#addremoveaccess)
* [使用角色存取控制的已知問題](#knownissues)
* [如何提供意見](#feedback)
* [後續步驟](#next)

<h2><a id="whatisrbac"></a>Azure 中的 RBAC</h2>
                                                                   
每一個 Azure 訂閱都與 Azure Active Directory 相關聯。透過 Azure 管理入口網站或是 Azure 資源管理員 API 來存取訂閱資源的使用者與服務，都必須先向 Azure Active Directory 進行驗證。

![][1] 

Azure 角色存取控制可讓您將角色指派給訂閱、資源群組或個別資源層級中的 Azure AD 使用者、群組與服務，授予其適當的存取權限。被指派的角色可定義 Azure 資源上的使用者、群組或服務的存取等級。 

### 角色

角色是指可以在 Azure 資源上執行的動作集合。當使用者或服務受指派為內含某項動作的角色，便可在 Azure 資源上執行該動作。

#### 內建角色

在第一次預覽中，Azure 角色存取控制會隨附三個內建的角色，供您指派給使用者、群組與服務。

+ **擁有者**：具有 Azure 資源的完整控制權。擁有者可以在任何一項資源上執行所有管理作業，包括存取管理。 
+ **參與者**：可以執行所有管理作業 (存取管理則例外)。因此，參與者無法將存取權限授予其他人。
+ **讀取者**：只能檢視資源。讀取者無法檢視與資源相關聯的秘密。 

您無法修改內建角色定義。在即將發佈的 Azure RBAC 版本中，您將可以從可在 Azure 資源上執行的可用自訂角色清單中撰寫一系列動作，藉此定義自訂角色。

#### 動作與非動作

角色定義內的 **actions** 屬性可指定 Azure 資源上允許執行的動作。動作字串可以使用萬用字元。角色定義內的 **not actions** 屬性可指定必須從允許的動作中排除的動作。 

**內建角色** | 動作 | 沒有動作	
------------- | -------------  | ------------
擁有者 (允許所有動作)  | *  | 
參與者 (允許除了寫入或刪除角色指派的所有動作)  | *  | Microsoft.Authorization/ * /Write, Microsoft.Authorization/ * /Delete
讀取者 (允許所有讀取動作)  | */Read  | 

### 角色指派

您可以在 Azure 資源上為 Azure AD 使用者與服務指派適當的角色，以授予其存取權限。 

#### Azure AD 安全性主體

您可以將角色指派給下列的 Azure AD 安全性主體類型：

+ **使用者**：您可以將角色指派給 Azure AD 內與 Azure 訂閱相關聯的組織使用者。您也可以透過 Invite 動作為使用者指派 Azure Preview 入口網站內的某個角色，以將角色指派給外部 Microsoft 帳戶使用者 (例如 joe@outlook.com)。將角色指派給外部 Microsoft 帳戶使用者會導致 Azure AD 中為該角色建立來賓帳戶。當目錄停用此來賓帳戶時，外部使用者將無法存取該使用者已經獲得存取授權的任何 Azure 資源。
+ **群組**：您可以將角色指派給 Azure AD 安全性群組。當使用者成為具有存取權限的群組成員時，系統會自動為該使用者授予資源存取權限。但當系統將該使用者從群組中移除時，該使用者也會自動喪失對資源的存取權限。將角色指派給群組並將使用者新增至這些群組，而非將角色直接指派給使用者，是透過群組來管理存取權限的最佳方式。Azure RBAC 不允許將角色指派給通訊群組清單。
	將角色指派給群組的能力，可讓組織將其現有的存取控制模型從內部部署目錄延伸到雲端，這樣您便可重複使用之前建立來控制存取內部部署環境的安全性群組來控制 Azure Preview 入口網站中的資源存取權限。如需從內部部署目錄同步使用者與群組的不同選項詳細資訊，請參閱[目錄整合](http://technet.microsoft.com/library/jj573653.aspx)。Azure AD Premium 同時提供了[委派的群組管理功能](http://msdn.microsoft.com/library/azure/dn641267.aspx)相關文章，讓您得以建立並管理可以從 Azure AD 委派給非管理員使用者的群組。
+ **服務主體**：目錄中會以服務主體來代表服務身分。這些身分負責向 Azure AD 驗證，並會安全地與彼此通訊。您可以透過 Azure module for Windows PowerShell，將角色指派給代表該服務的 Azure AD 服務主體，為服務授予 Azure 資源的存取權限。 

#### 資源範圍

您無須將存取權限授予整個訂閱。角色可以同時指派給資源群組與個別資源。在 Azure RBAC 中，資源會繼承來自其父系資源的角色指派。因此如果使用者、群組或服務只獲得訂閱中某項資源群組的存取權限，他們便只能存取其中的資源群組或資源，而非該訂閱內的其他資源群組。再舉另一例說明，安全性群組可以新增至資源群組的讀取者角色，但可以新增至該資源群組內某個資料庫的參與者角色。

![][2]

<h2><a id="coexist"></a>RBAC 與訂閱共同管理員的並存機制</h2>

訂閱管理員與共同管理員將繼續擁有對 Azure 入口網站與管理 API 的完整存取權限。在 RBAC 模型中，它們會獲得訂閱層級的擁有者角色。  
不過，只有 Azure Preview 入口網站和 Azure 資源管理員 API 支援新的 RBAC 模型。獲得 RBAC 角色指派的使用者與服務，無法存取 Azure 管理入口網站與服務管理 API。將使用者新增到 Azure Preview 入口網站中訂閱的擁有者角色之後，並不會使該使用者變成完整的 Azure 入口網站裡的訂閱共同管理員。

如果您希望將存取權限授予 Azure 資源的使用者 (但該資源尚無法透過 Azure Preview 入口網站管理)，您應該透過完整的 Azure 管理入口網站，將他們新增至訂閱共同管理員中。服務匯流排與雲端服務都是目前尚無法透過 RBAC 來管理的資源範例。

<h2><a id="authmgmt"></a>管理授權與資料作業</h2>

角色存取控制僅支援對 Azure Preview 入口網站的 Azure 資源與 Azure 資源管理員 API 進行管理作業。並非所有 Azure 資源的資料作業都可以透過 RBAC 授權。舉例來說，儲存體帳戶的建立/讀取/更新/刪除作業可以透過 RBAC 控制，但是儲存體帳戶內的 Blob 或資料表的建立/讀取/更新/刪除作業，卻還無法透過 RBAC 來控制。同理，SQL DB 的建立/讀取/更新/刪除作業可以透過 RBAC 來控制，但是 DB 內的 SQL 資料表之建立/讀取/更新/刪除作業卻還無法透過 RBAC 來控制。

<h2><a id="addremoveaccess"></a>如何新增與移除存取</h2>

讓我們舉例說明組織內的資源擁有者如何管理存取權限。在此案例中，有多位使用者正在進行使用 Azure 資源建立的各項測試與生產專案。您想要遵照最佳實作來授予存取權限。使用者現在應該對所需的所有資源具備存取權限，但沒有額外的存取權限。您想要重複利用之前在流程中的所有投資與工具，以便使用內部部署 Active Directory 中所建立的安全性群組。以下各節涵蓋如何針對這些資源設定存取權限：

* [新增存取](#add)
* [移除存取](#remove)
* [為外部使用者新增或移除存取權限](#addremoveext)

<h3><a id="add"></a>新增存取</h2>

以下摘要說明存取需求以及如何在 Azure 中設定這些存取需求。

使用者/群組 | 存取需求 | 角色和存取範圍	
------------- | -------------  | ------------
所有 Jill Santos 團隊 | 讀取所有 Azure 資源 | 將代表 Jill Santos 團隊的 AD 群組新增至 Azure 訂閱的讀取者角色
所有 Jill Santos 團隊 | 建立和管理測試資源群組中的所有資源 | 將代表 Jill Santos 團隊的 AD 群組新增至測試資源群組的參與者角色
Brock | 建立和管理 Prod 資源群組中的所有資源 | 將 Brock 新增至 Prod 資源群組的參與者角色


首先，讓我們為訂閱裡的所有資源新增讀取存取權限。按一下 [**瀏覽 > 全部 > 訂閱**]。

![][3] 

按一下 [*您的訂閱名稱* ** > 讀取者 > 新增**]。從使用者與群組清單中，選取或是輸入 Active Directory 群組的名稱。

![][4]

接著將相同團隊新增至 Test 資源群組的參與者角色中。按一下資源群組以開啟其屬性分頁。在 [**角色**] 下方按一下 [**參與者 > 新增**]，然後輸入團隊名稱。

![][5]

若要將 Brock 新增至 Prod 資源群組的參與者角色中，依序按一下資源群組與 [**參與者 > 新增**]，然後輸入 Brock 名稱。 

![][6]

角色指派也可以透過 Microsoft Azure module for Windows PowerShell 進行管理。以下舉例說明如何使用 New-AzureRoleAssignment Cmdlet (而非透過入口網站)，新增 Brock 帳戶：

	PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

如需使用 Windows PowerShell 來新增並移除存取權限的詳細資訊，請參閱[使用 Windows PowerShell 管理角色存取控制](http://azure.microsoft.com/zh-tw/documentation/articles/role-based-access-control-powershell/) (英文)。 

<h3><a id="remove"></a>移除存取</h2>

您也可以輕易地移除指派。假設您想要針對名為 TestDB 的資源群組，從讀取者角色中移除名為 Brad Adams 的使用者。開啟資源群組分頁，並按一下 [**讀取者 > Brad Adams > 移除**]。

![][7]

以下舉例說明如何使用 Remove-AzureRoleAssignment Cmdlet 移除 Brad Adams：

	PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

<h3><a id="addremoveext"></a>為外部使用者新增或移除存取權限</h2>

目錄的 [**設定**] 索引標籤包含可控制外部使用者存取權限的選項。這些選項只可以由目錄全域管理員在完整 Azure 入口網站的 UI (沒有 Windows PowerShell 或 API 方法) 中進行變更。 
若要在完整 Azure 入口網站中開啟 [**設定**] 索引標籤，請按一下 [**Active Directory**]，然後按一下目錄的名稱。

![][10]

接著您可以編輯選項來控制外部使用者的存取權限。 

![][8]

根據預設，來賓無法列舉目錄裡的內容，因此他們不會看到 [**成員清單**] 裡的任何使用者或群組他們可以輸入使用者的完整電子郵件地址來搜尋使用者，然後授予存取權限。以下是預設的來賓限制集合：

- 他們無法列舉目錄中的使用者與群組。
- 他們可以查看有限的使用者詳細資料 (前提是他們知道使用者的電子郵件地址)。
- 他們可以查看有限的群組詳細資料 (但必須知道群組名稱)。

來賓得以查看使用者或群組有限之詳細資料的能力，可方便他們邀請其他人並查看與他們協同合作的人員詳細資料。  

讓我們逐一示範整個流程，為外部使用者新增存取權限。我們將把一位外部使用者新增到 TestDB 資源群組中的相同讀取者角色上，以便該使用者能夠協助進行偵錯。開啟資源群組分頁，按一下 [**讀取者 > 新增 > 邀請**]，然後輸入您想要新增的使用者電子郵件地址。

![][9]

當您新增外部使用者時，目錄會自動建立一位來賓。此後，系統會將該名來賓新增至群組或從群組中將之移除，或者您可以從角色中加以個別新增或移除，就像您對其他任何目錄使用者所做的處理一樣。 

您也可以從任何角色中移除來賓，就像您移除其他使用者一樣。從資源角色中移除來賓，並不會從目錄中一併移除該來賓。 
 
<h2><a id="knownissues"></a>使用角色存取控制的已知問題</h2>

當您在預覽模式中使用角色存取控制功能而碰到問題時，請參閱[為角色存取控制進行疑難排解](http://azure.microsoft.com/zh-tw/documentation/articles/role-based-access-control-troubleshooting/)，了解與該問題相關的任何已知問題。


<h2><a id="feedback"></a>如何提供意見</h2>

請嘗試使用 Azure RBAC 並告知我們 [feedback](http://aka.ms/azurerbacfeedback). 


<h2><a id="next"></a>後續步驟</h2>

以下是可協助您使用角色存取控制的其他資源： 

+ [使用 Windows PowerShell 管理角色存取控制](http://azure.microsoft.com/zh-tw/documentation/articles/role-based-access-control-powershell/)
+ [使用 XPLAT CLI 管理角色存取控制](http://azure.microsoft.com/zh-tw/documentation/articles/role-based-access-control-xplat-cli/)
+ [為角色存取控制進行疑難排解](http://azure.microsoft.com/zh-tw/documentation/articles/role-based-access-control-troubleshooting/)
+ [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx)
+ [Azure Active Directory Premium 與 Basic](http://msdn.microsoft.com/zh-tw/library/azure/dn532272.aspx)
+ [Azure 訂閱如何與 Azure AD 產生關聯](http://msdn.microsoft.com/zh-tw/library/azure/dn629581.aspx)
+ 如需安全性群組的自我服務群組管理介紹，請參閱 [Active Directory 團隊部落格](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx) (英文)



<!--Image references-->
[1]: ./media/role-based-access-control-configure/RBACSubAuthDir.png
[2]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
[3]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
[4]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
[5]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
[6]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
[7]: ./media/role-based-access-control-configure/RBACRemoveRole.png
[8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
[9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
[10]: ./media/role-based-access-control-configure/RBACDirConfigTab.png


