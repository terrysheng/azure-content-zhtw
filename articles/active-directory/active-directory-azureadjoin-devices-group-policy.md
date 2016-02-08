<properties 
	pageTitle="將已加入網域的裝置連接到 Azure AD 以體驗 Windows 10 | Microsoft Azure" 
	description="說明系統管理員應如何設定群組原則，使裝置成為企業網路中已加入網域的裝置。" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article"

	ms.date="11/19/2015" 

	ms.author="femila"/>

# 將已加入網域的裝置連接到 Azure AD 以體驗 Windows 10

「加入網域」是過去 15 年來組織使用連接的裝置進行工作的傳統方式。它讓可使用者透過其 AD 工作帳戶登入裝置，並且讓 IT 人員能夠完全管理這些裝置。組織通常依賴映像處理方法將裝置佈建給使用者，且通常使用 System Center Configuration Manager (SCCM) 或群組原則加以管理。

在連接到 Azure AD 後，Windows 10 中的「加入網域」將可提供下列優點：

- 從任何位置對 Azure AD 資源進行 SSO (單一登入)。
- 使用工作帳戶存取企業 Windows 市集 (不需要 Microsoft 帳戶)。
- 使用工作帳戶進行符合企業標準的跨裝置使用者設定漫遊 (不需要 Microsoft 帳戶)
- 透過 Microsoft Passport 和 Windows Hello 進行工作帳戶的增強式驗證與便利的登入。
- 能夠對符合組織裝置原則的裝置進行存取限制。

##先決條件

「加入網域」的運作方式並未改變，但若要透過 Azure AD 享有 SSO、使用工作帳戶漫遊設定、使用工作帳戶存取 Windows 市集的好處，您必須符合下列條件：

- Azure AD 訂用帳戶。
- Azure AD Connect，以將內部部署目錄擴充至 Azure AD。
- 設定將已加入網域的裝置連接到 Azure AD 的原則。
- 裝置的 Windows 10 組建 (組建 10551 或更新版本)。

若要啟用 Microsoft Passport for Work 和 Windows Hello，您還需要下列項目：

- 使用者憑證發行的 PKI 基礎結構。
- System Center Configuration Manager 1509 版 (Technical Preview)。如需詳細資訊，請參閱 [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) 和 [System Center Configuration Manager 小組部落格](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)。這是根據 Microsoft Passport 金鑰部署使用者憑證的必要條件。

若要以 PKI 以外的方式進行部署，您可以執行下列作業：

- 建立幾個 Windows Server 2016 Active Directory 網域服務的網域控制站。

若要啟用條件式存取，您可以建立允許存取「加入網域」裝置而不需要其他部署的原則。若要根據裝置的合規性來管理存取控制，您需要下列項目：

- 用於 Passport 案例的 System Center Configuration Manager 1509 版 (Technical Preview)。

## 部署指示


## 步驟 1：部署 Azure Active Directory Connect

Azure AD Connect 可讓內部部署電腦佈建為雲端中的裝置物件。若要部署 Azure AD Connect，請參閱「使用 Azure AD Connect 啟用目錄的混合式管理」。

 - 如果您先前是依照 [Azure AD Connect 的自訂安裝](active-directory-aadconnect-get-started-custom.md) (不是快速安裝) 操作，您必須遵循如下所述的**在內部部署 Active Directory 中建立服務連接點 (SCP)** 程序。
 - 如果您在安裝 Azure AD Connect 之前已有 Azure AD 的同盟組態 (例如，如果您先前已部署 Active Directory Federation Service (AD FS))，則必須遵循下方的**設定 AD FS 宣告規則**程序。

### 在內部部署 Active Directory 中建立服務連接點 (SCP)

使用 Azure 裝置註冊服務進行自動註冊時，加入網域的裝置將使用此物件探索 Azure AD 租用戶資訊。請在 Azure AD Connect 伺服器上執行下列 PowerShell 命令：

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


>[AZURE.NOTE] 請將 [連接器帳戶名稱] 取代為作為 AD 連接器帳戶的網域帳戶。


>[AZURE.NOTE] Get-Credential 快顯視窗顯示時輸入的認證使用者名稱必須採用 *user@example.com* 格式

### 設定 AD FS 宣告規則
這可讓電腦使用 Kerberos/NTLM 透過 AD FS 進行驗證，進而透過 Azure DRS 讓電腦即時註冊。若未進行此步驟，電腦將會以延遲的方式進入 Azure AD (受限於 Azure AD Connect 同步處理的時間)。


>[AZURE.NOTE] 如果您未以 AD FS 作為內部部署的同盟伺服器，請遵循廠商的指示建立宣告規則。

在 AD FS 伺服器上執行下列 PowerShell 命令 (或在連接到 AD FS 伺服器的工作階段上執行)：

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed 
     |   for DomainJoin++.  The rules include:
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


>[AZURE.NOTE] Windows 10 電腦將會使用 Windows 整合式驗證，對 AD FS 所裝載的作用中 WS-Trust 端點進行驗證。您必須確定此端點已啟用。如果您使用 Web 驗證 Proxy，則也必須確定此端點已透過 Proxy 發佈。若要進行此確認，您可以檢查 adfs/services/trust/13/windowstransport 在 AD FS 管理主控台中的 [服務] > [端點] 下是否顯示為已啟用。


## 步驟 2：透過 Active Directory 中的群組原則設定自動裝置註冊

您可以使用 Active Directory 群組原則，設定讓加入網域的 Windows 10 裝置自動向 Azure AD 註冊。若要這麼做，請參閱下列逐步指示：

1. 	開啟 [伺服器管理員] 並瀏覽至 [工具] > [群組原則管理]。
2.	從 [群組原則管理]，瀏覽至與您想要啟用 [加入 Azure AD] 的網域相對應的網域節點。
3.	以滑鼠右鍵按一下 [群組原則物件]，選取 [新增]。指定群組原則物件的名稱，例如「自動加入 Azure AD」。按一下 [確定]。
4.	以滑鼠右鍵按一下新的群組原則物件，然後選取 [編輯]。
5.	瀏覽至 [電腦設定] > [原則] > [系統管理範本] > [Windows 元件] > [加入工作場所]。
6.	以滑鼠右鍵按一下 [自動將用戶端電腦加入工作場所]，然後選取 [編輯]。
7.	選取 [啟用] 選項按鈕，然後按一下 [套用]。按一下 [確定]。
8.	您現在可以將群組原則物件連結到您所選擇的位置。若要對組織中所有加入網域的 Windows 10 裝置啟用此原則，請將群組原則連結至網域。例如：
 - AD 中將放置加入網域的 Windows 10 電腦的特定組織單位 (OU)。
 - 加入網域而會向 Azure AD 自動註冊的 Windows 10 電腦所屬的特定安全性群組。
 

>[AZURE.NOTE] 此群組原則範本在 Windows 10 中已重新命名。如果您從 Windows 10 電腦執行群組原則工具，原則將會顯示為：<br> [註冊加入網域的電腦為裝置]，且原則將會位於下列位置之下：<br> 電腦設定/原則/系統管理範本 /Windows 元件/裝置註冊

 
## 其他資訊
* [適合企業使用的 Windows 10：使用裝置工作的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [了解適用於 Azure AD Join 的使用案例](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [將已加入網域裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0128_2016-->