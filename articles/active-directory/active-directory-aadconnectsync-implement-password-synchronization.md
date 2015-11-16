<properties
	pageTitle="Azure AD Connect 同步處理 - 實作密碼同步處理 | Microsoft Azure"
	description="提供您所需的資訊，以了解密碼同步處理的運作方式，及如何在您的環境中啟用此功能。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/03/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect 同步處理：實作密碼同步處理

藉由密碼同步處理，您可讓使用者使用與登入您的內部部署 Active Directory 時使用的相同密碼登入 Azure Active Directory。

本主題的目標是提供您所需的資訊，以了解密碼同步處理的運作方式，及如何在您的環境中啟用此功能。

## 什麼是密碼同步處理

密碼同步處理是 Azure Active Directory Connect 同步處理服務 (Azure AD Connect 同步處理) 的一項功能，可將使用者密碼從您的內部部署 Active Directory 同步至 Azure Active Directory (Azure AD)。此功能可讓您的使用者以登入您內部部署網路的相同密碼登入其 Azure Active Directory 服務 (如 Office 365、Microsoft Intune 和 CRM Online)。

> [AZURE.NOTE]如需針對 FIPS 和密碼同步處理設定的 Active Directory 網域服務的詳細資訊，請參閱[密碼同步處理和 FIPS](#password-synchronization-and-fips)。

### 密碼同步處理的可用性

任何 Azure Active Directory 的客戶都有權執行密碼同步處理。請參閱以下有關密碼同步處理相容性，及同盟驗證等其他功能的資訊。

### 密碼同步處理如何運作

密碼同步處理是 Azure AD Connect 同步處理實作的目錄同步作業功能的延伸。因此，這項功能需要設定您的內部部署與 Azure Active Directory 之間的目錄同步作業。

Active Directory 網域服務是以代表使用者實際密碼的雜湊值格式儲存密碼。密碼雜湊無法用來登入您的內部部署網路。它也被設計為可反轉以藉此取得使用者的純文字密碼。為了同步密碼，Azure AD Connect 同步處理會從內部部署的 Active Directory 擷取使用者的密碼。密碼在同步到 Azure Active Directory 驗證服務之前，會先套用額外的安全性處理。密碼同步處理程序的實際資料流程，就類似同步處理 DisplayName 或電子郵件地址等使用者資料。

相較於其他屬性的標準目錄同步作業週期，密碼會更頻繁地進行同步。密碼是以每個使用者為基礎來同步，且通常會依照時間先後順序進行。當使用者的密碼從內部部署 AD 同步到雲端時，會覆寫現有的雲端密碼。

您第一次啟用密碼同步處理功能時，系統會執行初始同步處理，將所有範圍內使用者的密碼從內部部署 Active Directory 同步到 Azure Active Directory。您無法明確定義一組使用者來指定將他們的密碼同步到雲端。此後，當內部部署使用者變更密碼時，通常在幾分鐘內密碼同步處理功能就會偵測到變更的密碼並加以同步。密碼同步處理功能會自動重試失敗的使用者密碼同步。若嘗試同步密碼期間發生錯誤，該錯誤會記錄在事件檢視器中。

密碼同步處理不會影響目前已登入的使用者。如果使用者已登入雲端服務並變更內部部署密碼，則雲端服務工作階段將會繼續運作不受干擾。不過，一旦雲端服務要求使用者重新驗證，就需要提供新的密碼。此時，使用者必須提供新的密碼 – 也就是最近從內部部署 Active Directory 同步至雲端的密碼。

### 密碼同步處理對 Azure AD 網域服務的運作方式

如果您在 Azure AD 中啟用這項服務，則需要密碼同步處理選項，才能獲得單一登入體驗。啟用此服務時，密碼同步處理的行為會變更，而也會從您的內部部署 Active Directory 依原樣同步處理密碼雜湊到 Azure AD 網域服務。此功能類似於 ADMT (Active Directory 遷移工具)，並可讓 Azure AD 網域服務能夠以內部部署 AD 中可用的所有方法驗證使用者。

### 安全性考量

同步密碼的時候，純文字版本的使用者密碼不會向密碼同步處理功能及 Azure AD 或任何相關服務公開。

此外，內部部署 Active Directory 不需使用可反轉加密的格式儲存密碼。Active Directory 密碼雜湊的摘要會用於內部部署 AD 和 Azure Active Directory 之間的傳輸。密碼雜湊的摘要無法用來存取客戶的內部部署環境中的資源。

### 密碼原則考量

啟用密碼同步處理會影響兩種類型的密碼原則：

1. 密碼複雜性原則
2. 密碼到期原則

**密碼複雜性原則**

當您啟用密碼同步處理之後，內部部署 Active Directory 中設定的密碼複雜性原則，會覆寫任何可能針對已同步使用者在雲端定義的複雜性原則。這表示任何在客戶內部部署 Active Directory 環境中有效的密碼，都可用來存取 Azure AD 服務。

> [AZURE.NOTE]直接在雲端建立的使用者的密碼仍受制於在雲端定義的密碼原則。

**密碼到期原則**

如果使用者位於密碼同步處理範圍內，則雲端帳戶的密碼會設為「永不到期」。這表示使用者的密碼可能在內部部署環境中已到期，但他們仍可以繼續使用到期的密碼登入雲端服務。

雲端密碼會於下一次使用者在內部部署環境中變更密碼時更新。

### 覆寫已同步的密碼

系統管理員可以使用 Azure Active Directory PowerShell 手動重設使用者的密碼。

在此情況下，新的密碼會覆寫使用者已同步處理的密碼，且雲端中定義的所有密碼原則會都套用到新的密碼。

如果使用者再次變更內部部署密碼，則新的密碼會同步到雲端，並且會覆寫手動更新的密碼。

## 準備密碼同步處理


### 啟用密碼同步處理

如果您在安裝 Azure AD Connect 時使用快速設定，則會依預設啟用密碼同步處理。

如果您在安裝 Azure AD Connect 時使用自訂設定，則必須在使用者登入頁面上啟用密碼同步處理。![usersignin](./media/active-directory-aadsync-implement-password-synchronization/usersignin.png)

如果您選擇使用**與 AD FS 同盟**，則可以選擇性地啟用密碼同步，作為 AD FS 基礎結構失敗時的備用方式。如果您打算使用 Azure AD 網域服務，您也可以啟用它。

### 密碼同步處理和 FIPS

如果已經根據 FIPS (美國聯邦資訊處理標準) 鎖定您的伺服器，則已停用 MD5。若要啟用此密碼同步處理，請在 C:\\Program Files\\Azure AD Sync\\Bin 的 miiserver.exe.config 中加入 enforceFIPSPolicy 索引鍵。

```
<configuration>
    <runtime>
        <enforceFIPSPolicy enabled="false"/>
    </runtime>
</configuration>
```

可以在組態檔的結尾找到執行階段組態/節點。

如需安全性和 FIPS 的詳細資訊，請參閱 [AAD 密碼同步、加密和 FIPS 法規遵循](http://blogs.technet.com/b/ad/archive/2014/06/28/aad-password-sync-encryption-and-and-fips-compliance.aspx)

## 管理密碼同步處理

### 疑難排解密碼同步處理

啟動 **Synchronization Service Manager**，開啟 [連接器]，選取使用者所在的 Active Directory 連接器，選取 [搜尋連接器空間]，並找到您要尋找的使用者。

![csuser](./media/active-directory-aadsync-implement-password-synchronization/cspasswordsync.png)

在使用者上，選取 [歷程] 索引標籤，並確認至少有一個同步規則的**密碼同步**顯示為 **True**。對於預設組態，這會是名為 **In from AD - User AccountEnabled** 的同步規則。

若要查看物件的密碼同步詳細資料，請此頁面底部按一下按鈕 [記錄...]。這將會產生此頁面在過去一週使用者的密碼同步狀態的歷程記錄檢視。

![物件記錄檔](./media/active-directory-aadsync-implement-password-synchronization/csobjectlog.png)

狀態欄可能有下列值，其指出問題以及為何未同步處理密碼。

| 狀態 | 說明 |
| ---- | ----- |
| 成功 | 已成功同步處理密碼 |
| SourceConnectorNotPresent | 在內部部署 Active Directory 連接器空間中找不到任何物件 |
| NoTargetConnection | Metaverse 或 Azure AD 連接器空間中沒有任何物件 |
| TargetNotExportedToDirectory | 尚未匯出 Azure AD 連接器空間中的物件 |


### 觸發所有密碼的完整同步處理
強制執行所有密碼的完整同步應該不是必要，但如果基於某些原因您需要，以下就是此動作的 PowerShell。

    $adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
    $aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
    Import-Module adsync
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter “Microsoft.Synchronize.ForceFullPasswordSync”, String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true




## 其他資源

* [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Nov15_HO2-->