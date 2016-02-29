<properties
   pageTitle="Azure AD Connect︰版本發行歷程記錄 | Microsoft Azure"
   description="本主題列出 Azure AD Connect 和 Azure AD Sync 的所有版本"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/16/2016"
   ms.author="andkjell"/>

# Azure AD Connect︰版本發行歷程記錄

Azure Active Directory 團隊會定期以新的特性和功能更新 Azure AD Connect。並非所有新增項目都適用於所有的對象。

本文旨在協助您追蹤已發行的版本，以及了解您是否需要更新為最新版本。

相關連結：

- 如需套用更新所需權限的詳細資訊，請參閱[帳戶和權限](active-directory-aadconnect-accounts-permissions.md#upgrade)
- [下載 Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

## 1\.1.105.0
發行日期：2016 年 2 月

**新功能︰**

- 適用於「快速設定」客戶的[自動升級](active-directory-aadconnect-feature-automatic-upgrade.md)功能。
- 使用安裝精靈中的 MFA 和 PIM 來提供全域管理員支援。
    - 如果您使用 MFA，就必須讓您的 Proxy 也允許對 https://secure.aadcdn.microsoftonline-p.com 的流量。
    - 您必須將 https://secure.aadcdn.microsoftonline-p.com 新增到信任的網站清單中，MFA 才能正常運作。
- 允許在初始安裝之後變更使用者的登入方法。
- 允許在安裝精靈中使用[網域和 OU 篩選](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering)。這也會允許連線到並非所有網域都可供使用的樹系。
- [排程器](active-directory-aadconnectsync-feature-scheduler.md)是同步處理引擎的內建功能。

**從預覽版升級到 GA 的功能：**

- [裝置回寫](active-directory-aadconnect-feature-device-writeback.md)。
- [目錄擴充](active-directory-aadconnectsync-feature-directory-extensions.md)。

**新的預覽功能：**

- 新的預設同步處理循環間隔為 30 分鐘。過去所有舊版本都是 3 小時。新增可變更[排程器](active-directory-aadconnectsync-feature-scheduler.md)行為的支援。

**已修正的問題：**

- 驗證 DNS 網域頁面不一定都能辨識網域。
- 設定 ADFS 時，出現網域系統管理員認證提示。
- 當內部部署 AD 帳戶所在網域的 DNS 樹狀目錄與根網域不同時，安裝精靈即無法辨識這些帳戶。

## 1\.0.9131.0
發行時間︰2015 年 12 月

**已修正的問題：**

- 您變更 AD DS 中的密碼時，密碼同步可能會無法作用，但在您設定密碼時將可作用。
- 如果您有 Proxy 伺服器，在安裝期間或於組態頁面上進行升級時，向 Azure AD 進行驗證可能會失敗。
- 若您並非 SQL 中的 SA，從有完整 SQL 伺服器的舊版 Azure AD Connect 更新將會失敗。
- 從有遠端 SQL 伺服器的舊版 Azure AD Connect 更新時，將顯示錯誤訊息「無法存取 ADSync SQL Database」。

## 1\.0.9125.0
發行時間：2015 年 11 月

**新功能︰**

- 可以將 ADFS 重新設定為 Azure AD 信任。
- 可以重新整理 Active Directory 結構描述並重新產生同步處理規則。
- 可以停用同步處理規則。
- 可以在同步處理規則中定義 "AuthoritativeNull" 做為新的常值。

**新的預覽功能：**

- [適用於同步的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)。
- 支援 [Azure AD 網域服務](active-directory-get-started.md)密碼同步處理。

**新的支援案例：**

- 支援多個內部部署的 Exchange 組織。詳細資訊請參閱[內含多個 Active Directory 樹系的混合式部署](https://technet.microsoft.com/library/jj873754.aspx)。

**已修正的問題：**

- 密碼同步處理問題：
    - 從範圍外移到範圍內的物件不會同步處理其密碼。這包含 OU 及屬性篩選。
    - 選取要包含在同步處理的新 OU 不需要完整密碼同步處理。
    - 啟用已停用的使用者時，不會同步處理密碼。
    - 密碼重試佇列為無限，而且已移除 5000 個物件要淘汰的先前限制。
    - [改良的疑難排解](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization)。
- 無法連接到具 Windows Server 2016 樹系功能等級的 Active Directory。
- 初始安裝後，無法變更群組篩選所使用的群組。
- 對於在啟用密碼回寫的情況下執行密碼變更的每個使用者，將無法再於 Azure AD Connect 伺服器上建立新的使用者設定檔。
- 無法在同步處理規則範圍中使用長整數值。
- 如果有無法連線的網域控制站，[裝置回寫] 核取方塊會維持停用狀態。

## 1\.0.8667.0
發行日期：2015 年 8 月

**新功能︰**

- Azure AD Connect 安裝精靈現在已進行所有 Windows Server 語言的當地語系化。
- 新增在使用 Azure AD 密碼管理時的帳戶解除鎖定支援。

**已修正的問題：**

- 如果另一位使用者繼續安裝，而不是最先開始安裝的人，則 Azure AD Connect 安裝精靈會當機。
- 如果 Azure AD Connect 的先前解除安裝作業無法將 Azure AD Connect Sync 完全解除安裝，則不可能重新安裝。
- 如果使用者不在樹系的根網域中或已使用非英文版的 Active Directory，則無法使用 Express 安裝 Azure AD Connect。
- 如果無法解析 Active Directory 使用者帳戶的 FQDN，則會顯示「無法認可結構描述」的誤導錯誤訊息。
- 如果 Active Directory 連接器上使用的帳戶已在精靈外部變更，精靈將無法進行後續的執行。
- Azure AD Connect 有時無法在網域控制站上安裝。
- 如果已加入擴充屬性，則無法啟用和停用「預備模式」。
- 因為 Active Directory 連接器上的密碼不正確，所以有些設定的密碼回寫失敗。
- 如果 dn 用於屬性篩選，則無法升級 DirSync。
- 使用密碼重設時，CPU 使用率過高。

**已移除的預覽功能：**

- 根據預覽版客戶的意見反應，已暫時移除 [[使用者回寫](active-directory-aadconnect-feature-preview.md#user-writeback)] 預覽功能。當我們處理所提供的意見反應之後，未來將會重新加入它。

## 1\.0.8641.0
發行日期：2015 年 6 月

**Azure AD Connect 的最初發行版本。**

名稱從 Azure AD Sync 變更為 Azure AD Connect。

**新功能︰**

- [快速設定](active-directory-aadconnect-get-started-express.md)安裝
- 可以[設定 ADFS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
- 可以[從 DirSync 升級](active-directory-aadconnect-dirsync-upgrade-get-started.md)
- [防止意外刪除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- 引入[預備模式](active-directory-aadconnectsync-operations.md#staging-mode)

**新的預覽功能：**

- [使用者回寫](active-directory-aadconnect-feature-preview.md#user-writeback)
- [群組回寫](active-directory-aadconnect-feature-preview.md#group-writeback)
- [裝置回寫](active-directory-aadconnect-feature-device-writeback.md)
- [目錄擴充](active-directory-aadconnect-feature-preview.md#directory-extensions)


## 1\.0.494.0501
發行日期：2015 年 5 月

**新需求︰**

- Azure AD Sync 現在需要安裝 .Net framework 4.5.1 版。

**已修正的問題：**

- Azure AD 密碼回寫出現服務匯流排連接錯誤。

## 1\.0.491.0413
發行日期：2015年 4 月

**已修正的問題和改進︰**

- 如果已啟用資源回收筒且樹系中有多個網域，則 Active Directory 連接器不會正確處理刪除。
- Azure Active Directory 連接器的匯入作業效能已改善。
- 當群組已超過成員資格限制 (預設限制已設為 50k 個物件)，則會在 Azure Active Directory 中刪除此群組。新的行為是保留群組、擲回錯誤，而且不會匯出新的成員資格變更。
- 如果連接器空間已存在具有相同 DN 的分段刪除，則無法佈建新的物件。
- 在差異同步處理期間，儘管物件上沒有預備的變更，某些物件仍會被標示為同步處理中。
- 強制執行密碼同步也會移除慣用的 DC 清單。
- CSExportAnalyzer 有一些物件狀態的問題。

**新功能︰**

- 聯結現在可以連接到 MV 中的「任何」物件類型。

## 1\.0.485.0222
發行日期：2015 年 2 月

**改進：**

- 改進匯入效能。

**已修正的問題：**

- 密碼同步會接受屬性篩選所用的 cloudFiltered 屬性。經過篩選的物件不再於密碼同步處理的範圍中。
- 在拓撲有很多網域控制站的少數情況下，無法進行密碼同步處理。
- 在 Azure AD/Intune 中啟用裝置管理之後，從 Azure AD 連接器匯入時的「停止的伺服器」。
- 從相同樹系中的多個網域加入外部安全性主體 (FSP) 會造成模稜兩可的加入錯誤。

## 1\.0.475.1202
發行日期︰2014 年 12 月

**新功能︰**

- 現在支援透過以屬性為基礎的篩選進行密碼同步處理。如需詳細資訊，請參閱[透過篩選進行密碼同步](active-directory-aadconnectsync-configure-filtering.md)。
- msDS ExternalDirectoryObjectID 屬性會寫回至 AD。這可支援 Office 365 應用程式使用 OAuth2 存取混合式 Exchange 部署中的線上和內部部署信箱。

**已修正的升級問題︰**

- 在伺服器上可使用較新版的登入小幫手。
- 自訂安裝路徑用來安裝 Azure AD Sync。
- 無效的自訂加入準則會封鎖升級。

**其他修正︰**

- 已修正適用於 Office Pro Plus 的範本。
- 已修正以破折號開頭的使用者名稱所造成的安裝問題。
- 已修正第二次執行安裝精靈時遺失 sourceAnchor 設定的問題。
- 已修正密碼同步處理的 ETW 追蹤

## 1\.0.470.1023
發行日期：2014 年 10 月

**新功能︰**

- 從多個內部部署 AD 至 Azure AD 的密碼同步處理。
- 所有 Windows Server 語言的當地語系化安裝 UI。

**從 AADSync 1.0 GA 升級**

如果您已安裝 Azure AD Sync，您必須進行一個額外步驟，以免您已變更任何現成可用的同步規則。在升級至 1.0.470.1023 版本之後，您已修改的同步規則會重複。針對每個已修改的同步規則，執行下列作業︰

- 找出您已修改的同步規則並記下變更。
- 刪除同步規則。
- 找出 Azure AD Sync 建立的新同步規則並重新套用變更。

**AD 帳戶的權限**

AD 帳戶必須獲得其他權限，才能讀取來自 AD 的密碼雜湊。要授與的權限名為「複寫目錄變更」和「複寫所有目錄變更」。需要有這兩個權限才能讀取密碼雜湊

## 1\.0.419.0911
發行日期：2014 年 9 月

**Azure AD Sync 的最初發行版本。**

## 後續步驟
深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0218_2016-->