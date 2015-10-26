<properties
	pageTitle="Azure Active Directory 網域服務預覽：開始使用 | Microsoft Azure"
	description="開始使用 Azure Active Directory 網域服務"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/12/2015"
	ms.author="maheshu"/>

# Azure AD 網域服務 (預覽) - 開始使用

## 步驟 5：啟用密碼同步處理
一旦您已針對 Azure AD 租用戶啟用 Azure AD 網域服務之後，下一個步驟就是啟用密碼的同步處理。這讓使用者能夠使用他們的公司認證來登入網域。

所需的步驟會根據組織是僅限雲端，或已設定為使用 Azure AD Connect 來同步處理內部部署目錄而有所不同。

### 針對僅限雲端的租用戶啟用密碼同步處理
如果您的組織是僅限雲端的 Azure AD 租用戶，需要使用 Azure AD 網域服務的使用者就必須變更其密碼。這個步驟的結果就是在 Azure AD 中產生 Azure AD 網域服務進行 Kerberos 和 NTLM 驗證所需的認證雜湊。您可以針對租用戶中所有需要使用 Azure AD 網域服務的使用者來使其密碼過期，或者指示這些使用者變更他們的密碼。

以下是您需要提供使用者才能變更其密碼的指示：

1. 瀏覽至組織的 [Azure AD 存取面板] 頁面。您通常可以在 [http://myapps.microsoft.com](http://myapps.microsoft.com) 上找到此頁面。
2. 選取此頁面上的 [設定檔] 索引標籤。
3. 按一下此頁面上的 [變更密碼] 圖格來初始密碼變更。

    ![建立適用於 Azure AD 網域服務的虛擬網路。](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. 這會顯示 [變更密碼] 頁面。使用者接著可以輸入現有的 (舊) 密碼，然後繼續變更密碼。

    ![建立適用於 Azure AD 網域服務的虛擬網路。](./media/active-directory-domain-services-getting-started/user-change-password2.png)

當使用者變更密碼之後，新的密碼將會在短時間內同步處理至 Azure AD 網域服務。完成密碼同步處理之後，使用者接著就能使用最近變更的密碼來登入網域。


### 針對同步處理的租用戶啟用密碼同步處理
如果貴組織的 Azure AD 租用戶已設定為使用Azure AD Connect 來同步處理內部部署目錄，則您必須設定 Azure AD Connect，來同步處理 NTLM 和 Kerberos 驗證所需的認證雜湊。這些雜湊預設不會同步處理到 Azure AD，而下列步驟可讓您將雜湊同步處理到 Azure AD 租用戶。

#### 安裝 Azure AD Connect

您必須在加入網域的電腦上安裝 GA 版本的 Azure AD Connect。如果您目前已設定 Azure AD Connect 的執行個體，就必須更新它來使用 Azure AD Connect GA 組建。

  [下載 Azure AD Connect - GA 組建](http://download.microsoft.com/download/B/0/0/B00291D0-5A83-4DE7-86F5-980BC00DE05A/AzureADConnect.msi)。

> [AZURE.WARNING]您「必須」安裝 GA 版本的 Azure AD Connect，才能將傳統密碼認證 (NTLM 和 Kerberos 驗證所需的認證) 同步處理到 Azure AD 租用戶。此功能無法在舊版的 Azure AD Connect 中使用。

Azure AD Connect 的安裝指示可於下列文章中取得：[開始使用 Azure AD Connect](../active-directory/active-directory-aadconnect.md)


#### 強制執行與 Azure AD 的完整密碼同步處理

若要強制執行完整密碼同步處理並啟用所有的內部部署使用者的密碼雜湊 (包括 NTLM/Kerberos 驗證所需的認證雜湊) 以同步處理到您的 Azure AD 租用戶，請在每個 AD 樹系上執行下列 PowerShell 指令碼。

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null  
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

視目錄的大小而定 (使用者的數目、群組等)，將認證同步處理到 Azure AD，然後到 Azure AD 網域服務，需要花一點時間。

<!---HONumber=Oct15_HO3-->