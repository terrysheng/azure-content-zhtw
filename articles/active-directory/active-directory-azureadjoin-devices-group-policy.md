<properties
	pageTitle="將已加入網域的裝置連接到 Azure AD 以體驗 Windows 10 | Microsoft Azure"
	description="說明系統管理員應如何設定群組原則，使裝置成為企業網路中已加入網域的裝置。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="femila"/>

# 將已加入網域的裝置連接到 Azure AD 以體驗 Windows 10

「加入網域」是過去 15 年來組織使用連接的裝置進行工作的傳統方式。這讓使用者能夠使用他們的 Windows Server Active Directory (Active Directory) 工作或學校帳戶來登入其裝置，並允許 IT 能夠完全管理這些裝置。組織通常依賴映像處理方法將裝置佈建給使用者，且通常使用 System Center Configuration Manager (SCCM) 或群組原則加以管理。

當您將裝置連接到 Azure Active Directory (Azure AD) 後，Windows 10 中的「網域加入」將可提供下列優點：

- 從任何位置單一登入 (SSO) 至 Azure AD 資源
- 使用工作或學校帳戶存取企業 Windows 市集 (不需要 Microsoft 帳戶)
- 使用工作或學校帳戶進行符合企業標準的跨裝置使用者設定漫遊 (不需要 Microsoft 帳戶)
- 透過 Microsoft Passport 和 Windows Hello 進行工作或學校帳戶的增強式驗證與便利的登入。
- 能夠限制為只能存取遵循組織裝置群組原則設定的裝置

## 先決條件

「網域加入」仍持續有用。但是，若要透過 Azure AD 享有 SSO、使用工作或學校帳戶漫遊設定，以及使用工作或學校帳戶存取 Windows 市集的好處，您必須符合下列條件：

- Azure AD 訂用帳戶
- Azure AD Connect，以將內部部署目錄擴充至 Azure AD
- 設定來將已加入網域的裝置連接到 Azure AD 的原則
- 裝置的 Windows 10 組建 (組建 10551 或更新版本)

若要啟用 Microsoft Passport for Work 和 Windows Hello，您還需要下列項目：

- 適用於使用者憑證發行的公開金鑰基礎結構 (PKI)。
- System Center Configuration Manager 1509 版 (Technical Preview)。如需詳細資訊，請參閱 [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) 和 [System Center Configuration Manager 小組部落格](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)。這是根據 Microsoft Passport 金鑰部署使用者憑證的必要條件。

若要以 PKI 以外的方式進行部署，您可以執行下列作業：

- 建立幾個 Windows Server 2016 Active Directory 網域服務的網域控制站。

若要啟用條件式存取，您可以建立群組原則設定，允許存取已加入網域的裝置，而不需其他部署。若要根據裝置的合規性來管理存取控制，您需要下列項目：

- 用於 Passport 案例的 System Center Configuration Manager 1509 版 (Technical Preview)

## 部署指示



### 步驟 1：部署 Azure Active Directory Connect

Azure AD Connect 可讓您將內部部署電腦佈建為雲端中的裝置物件。若要部署 Azure AD Connect，請參閱[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect/#install-azure-ad-connect) 一文中的 ＜安裝 Azure AD Connect＞。

 - 如果您先前是依照[自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md) (不是快速安裝) 操作，則請遵循此步驟稍後的**在內部部署 Active Directory 中建立服務連接點**程序。
 - 如果您在安裝 Azure AD Connect 之前已有 Azure AD 的同盟組態 (例如，如果您先前已部署 Active Directory Federation Service (AD FS))，則請遵循此步驟稍後的**設定 AD FS 宣告規則**程序。

#### 在內部部署 Active Directory 中建立服務連接點

使用 Azure 裝置註冊服務進行自動註冊時，已加入網域的裝置將使用服務連接點來探索 Azure AD 租用戶資訊。

在 Azure AD Connect 伺服器上，執行下列 PowerShell 命令：

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


執行 Cmdlet $aadAdminCred = Get-credential 時，針對在 Get-credential 快顯視窗出現時輸入的認證使用者名稱使用格式 **user@example.com* 。

執行 Cmdlet Initialize-ADSyncDomainJoinedComputerSync ... 時，以用來做為 Active Directory 連接器帳戶的網域帳戶來取代 [*連接器帳戶名稱*]。

#### 設定 AD FS 宣告規則
設定 AD FS 宣告規則可讓電腦使用 Kerberos/NTLM 透過 AD FS 進行驗證，進而透過 Azure 裝置註冊服務來讓電腦即時註冊。若未進行此步驟，電腦將會以延遲的方式進入 Azure AD (受限於 Azure AD Connect 同步處理的時間)。

>[AZURE.NOTE]
如果您未以 AD FS 做為內部部署的同盟伺服器，請遵循廠商的指示建立宣告規則。

在 AD FS 伺服器上 (或在連接到 AD FS 伺服器的工作階段上)，執行下列 PowerShell 命令：

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $rule1 = '@RuleName = "Issue object GUID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain joined computers"
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

    $rule3 = '@RuleName = "Pass through primary SID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(claim = c2);'

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

>[AZURE.NOTE]
Windows 10 電腦將會使用 Windows 整合式驗證，對 AD FS 所裝載的作用中 WS-Trust 端點進行驗證。請確定此端點已啟用。如果您使用 Web 驗證 Proxy，也需確定已透過 Proxy 發佈此端點。您可以藉由檢查 adfs/services/trust/13/windowstransport 來執行此動作。它應該會在 AD FS 管理主控台的 [服務] > [端點] 下方顯示為已啟用。


### 步驟 2：透過 Active Directory 中的群組原則設定自動裝置註冊

您可以使用 Active Directory 中的群組原則，將已加入網域的 Windows 10 裝置設定為自動向 Azure AD 註冊。若要這麼做，請使用下列逐步指示：

1. 	開啟 [伺服器管理員] 並瀏覽至 [工具] > [群組原則管理]。
2.	從 [群組原則管理]，瀏覽至與您想要啟用 [加入 Azure AD] 的網域相對應的網域節點。
3.	以滑鼠右鍵按一下 [群組原則物件]，然後選取 [新增]。指定群組原則物件的名稱，例如「自動加入 Azure AD」。按一下 [確定]。
4.	以滑鼠右鍵按一下新的群組原則物件，然後選取 [編輯]。
5.	瀏覽至 [電腦設定] > [原則] > [系統管理範本] > [Windows 元件] > [加入工作場所]。
6.	以滑鼠右鍵按一下 [自動將用戶端電腦加入工作場所]，然後選取 [編輯]。
7.	選取 [已啟用] 選項按鈕，然後按一下 [套用]。按一下 [確定]。
8.	將群組原則物件連結到您選擇的位置。若要對組織中所有已加入網域的 Windows 10 裝置啟用此原則，請將群組原則物件連結至網域。例如：
 - Active Directory 中將放置已加入網域的 Windows 10 電腦的特定組織單位 (OU)
 - 已加入網域且會向 Azure AD 自動註冊的 Windows 10 電腦所屬的特定安全性群組

>[AZURE.NOTE]
此群組原則範本在 Windows 10 中已重新命名。如果您從 Windows 10 電腦執行群組原則工具，原則將會顯示為：<br> **註冊加入網域的電腦為裝置**<br>原則將位於下列位置中：<br> ***電腦設定/原則/系統管理範本 /Windows 元件/裝置註冊***


## 其他資訊
* [適合企業使用的 Windows 10：使用裝置工作的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [了解適用於 Azure AD Join 的使用案例](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [將已加入網域裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0309_2016-->