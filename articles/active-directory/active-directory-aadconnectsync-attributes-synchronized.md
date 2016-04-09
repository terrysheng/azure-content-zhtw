<properties
	pageTitle="Azure AD Connect 同步處理：將屬性同步處理至 Azure Active Directory | Microsoft Azure"
	description="列出要同步處理至 Azure Active Directory 的屬性。"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect 同步處理：將屬性同步處理至 Azure Active Directory

本主題列出 Azure AD Connect 同步處理會同步處理的屬性。屬性會依相關的 Azure AD App 來分組。

## 要同步處理的屬性
常見的問題是同步處理的最少屬性清單為何。建議的預設方法是保留預設屬性，這樣可以在雲端建構完整的 GAL (全域通訊清單)，並取得 Office 365 工作負載的所有功能。在某些情況下，貴組織有些不想同步處理至雲端的屬性，因為它們包含機密或 PII (個人識別資訊) 資料，如下例所示。

![不正確的屬性](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png)

在此情況下，請從下面的屬性清單著手，識別出可能包含機密或 PII (個人識別資訊) 資料，不能同步處理的屬性。然後在安裝期間使用 [Azure AD 應用程式和屬性篩選](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering)將它們取消選取。

>[AZURE.WARNING] 取消選取屬性時，您應該小心，只取消選取那些絕對不可能進行同步處理的屬性。取消選取其他屬性可能對功能造成負面的影響。

## Office 365 ProPlus

| 屬性名稱| 使用者| 註解 |
| --- | :-: | --- |
| accountEnabled| X| 定義是否啟用帳戶。|
| cn| X| |
| displayName| X| |
| objectSID| X| 機械屬性。AD 使用者識別碼，可用來維持 Azure AD 和 AD 之間的同步處理。|
| pwdLastSet| X| 機械屬性。用來得知何時要讓已經發行的權杖失效。供密碼同步處理和同盟使用。|
| sourceAnchor| X| 機械屬性。不可變的識別碼，可維持 ADDS 與 Azure AD 之間的關聯性。|
| usageLocation| X| 機械屬性。使用者的國家/地區。用於授權指派。|
| userPrincipalName| X| UPN 是使用者的登入識別碼。最常與 [mail] 值相同。|


## Exchange Online

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 定義是否啟用帳戶。|
| assistant| X| X| | |
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| 說明| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| homePhone| X| X| | |
| info| X| X| X| 群組目前未使用這個屬性。|
| Initials| X| X| | |
| l| X| X| | |
| legacyExchangeDN| X| X| X| |
| mailNickname| X| X| X| |
| mangedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msDS-HABSeniorityIndex| X| X| X| |
| msDS-PhoneticDisplayName| X| X| X| |
| msExchArchiveGUID| X| | | |
| msExchArchiveName| X| | | |
| msExchAssistantName| X| X| | |
| msExchAuditAdmin| X| | | |
| msExchAuditDelegate| X| | | |
| msExchAuditDelegateAdmin| X| | | |
| msExchAuditOwner| X| | | |
| msExchBlockedSendersHash| X| X| | |
| msExchBypassAudit| X| | | |
| msExchCoManagedByLink| | | X| |
| msExchDelegateListLink| X| | | |
| msExchELCExpirySuspensionEnd| X| | | |
| msExchELCExpirySuspensionStart| X| | | |
| msExchELCMailboxFlags| X| | | |
| msExchEnableModeration| X| | X| |
| msExchExtensionCustomAttribute1| X| X| X| Exchange Online 目前未使用這個屬性。 |
| msExchExtensionCustomAttribute2| X| X| X| Exchange Online 目前未使用這個屬性。 |
| msExchExtensionCustomAttribute3| X| X| X| Exchange Online 目前未使用這個屬性。 |
| msExchExtensionCustomAttribute4| X| X| X| Exchange Online 目前未使用這個屬性。 |
| msExchExtensionCustomAttribute5| X| X| X| Exchange Online 目前未使用這個屬性。 |
| msExchHideFromAddressLists| X| X| X| |
| msExchImmutableID| X| | | |
| msExchLitigationHoldDate| X| X| X| |
| msExchLitigationHoldOwner| X| X| X| |
| msExchMailboxAuditEnable| X| | | |
| msExchMailboxAuditLogAgeLimit| X| | | |
| msExchMailboxGuid| X| | | |
| msExchModeratedByLink| X| X| X| |
| msExchModerationFlags| X| X| X| |
| msExchRecipientDisplayType| X| X| X| |
| msExchRecipientTypeDetails| X| X| X| |
| msExchRemoteRecipientType| X| | | |
| msExchRequireAuthToSendTo| X| X| X| |
| msExchResourceCapacity| X| | | |
| msExchResourceDisplay| X| | | |
| msExchResourceMetaData| X| | | |
| msExchResourceSearchProperties| X| | | |
| msExchRetentionComment| X| X| X| |
| msExchRetentionURL| X| X| X| |
| msExchSafeRecipientsHash| X| X| | |
| msExchSafeSendersHash| X| X| | |
| msExchSenderHintTranslations| X| X| X| |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| msExchUserHoldPolicies| X| | | |
| msOrg-IsOrganizational| | | X| |
| objectSID| X| | X| 機械屬性。AD 使用者識別碼，可用來維持 Azure AD 和 AD 之間的同步處理。|
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| proxyAddresses| X| X| X| |
| publicDelegates| X| X| X| |
| pwdLastSet| X| | | 機械屬性。用來得知何時要讓已經發行的權杖失效。供密碼同步處理和同盟使用。|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| 衍生自 groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| 機械屬性。不可變的識別碼，可維持 ADDS 與 Azure AD 之間的關聯性。|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| usageLocation| X| | | 機械屬性。使用者的國家/地區。用於授權指派。|
| userCertificate| X| X| | |
| userPrincipalName| X| | | UPN 是使用者的登入識別碼。最常與 [mail] 值相同。|
| userSMIMECertificates| X| X| | |
| wWWHomePage| X| X| | |



## SharePoint Online

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 定義是否啟用帳戶。|
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| 說明| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| hideDLMembership| | | X| |
| homephone| X| X| | |
| info| X| X| X| |
| initials| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| middleName| X| X| | |
| mobile| X| X| | |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointLinkedBy| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| objectSID| X| | X| 機械屬性。AD 使用者識別碼，可用來維持 Azure AD 和 AD 之間的同步處理。|
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherIpPhone| X| X| | |
| otherMobile| X| X| | |
| otherPager| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| postOfficeBox| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | 機械屬性。用來得知何時要讓已經發行的權杖失效。供密碼同步處理和同盟使用。|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| 衍生自 groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| 機械屬性。不可變的識別碼，可維持 ADDS 與 Azure AD 之間的關聯性。|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| url| X| X| | |
| usageLocation| X| | | 機械屬性。使用者的國家/地區。用於授權指派。|
| userPrincipalName| X| | | UPN 是使用者的登入識別碼。最常與 [mail] 值相同。|
| wWWHomePage| X| X| | |

## Lync Online

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 定義是否啟用帳戶。|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| department| X| X| | |
| 說明| X| X| X| |
| displayName| X| X| X| |
| facsimiletelephonenumber| X| X| X| |
| givenName| X| X| | |
| homephone| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailNickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msExchHideFromAddressLists| X| X| X| |
| msRTCSIP-ApplicationOptions| X| | | |
| msRTCSIP-DeploymentLocator| X| X| | |
| msRTCSIP-Line| X| X| | |
| msRTCSIP-OptionFlags| X| X| | |
| msRTCSIP-OwnerUrn| X| | | |
| msRTCSIP-PrimaryUserAddress| X| X| | |
| msRTCSIP-UserEnabled| X| X| | |
| objectSID| X| | X| 機械屬性。AD 使用者識別碼，可用來維持 Azure AD 和 AD 之間的同步處理。|
| otherTelephone| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | 機械屬性。用來得知何時要讓已經發行的權杖失效。供密碼同步處理和同盟使用。|
| securityEnabled| | | X| 衍生自 groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| 機械屬性。不可變的識別碼，可維持 ADDS 與 Azure AD 之間的關聯性。|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| usageLocation| X| | | 機械屬性。使用者的國家/地區。用於授權指派。|
| userPrincipalName| X| | | UPN 是使用者的登入識別碼。最常與 [mail] 值相同。|
| wWWHomePage| X| X| | |


## Azure RMS

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 定義是否啟用帳戶。|
| cn| X| | X| 一般名稱或別名。最常見的前置詞是 [mail] 值。|
| displayName| X| X| X| 字串，表示通常會顯示為易記名稱 (名字姓氏) 的名稱。|
| mail| X| X| X| 電子郵件地址。|
| member| | | X| |
| objectSID| X| | X| 機械屬性。AD 使用者識別碼，可用來維持 Azure AD 和 AD 之間的同步處理。|
| proxyAddresses| X| X| X| 機械屬性。由 Azure AD 所使用。包含使用者的所有次要電子郵件地址。|
| pwdLastSet| X| | | 機械屬性。用來得知何時要讓已經發行的權杖失效。|
| securityEnabled| | | X| 衍生自 groupType。|
| sourceAnchor| X| X| X| 機械屬性。不可變的識別碼，可維持 ADDS 與 Azure AD 之間的關聯性。|
| usageLocation| X| | | 機械屬性。使用者的國家/地區。用於授權指派。|
| userPrincipalName| X| | | 這個 UPN 是使用者的登入識別碼。最常與 [mail] 值相同。|


## Intune

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 定義是否啟用帳戶。|
| c| X| X| | |
| cn| X| | X| |
| 說明| X| X| X| |
| displayName| X| X| X| |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| member| | | X| |
| objectSID| X| | X| 機械屬性。AD 使用者識別碼，可用來維持 Azure AD 和 AD 之間的同步處理。|
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | 機械屬性。用來得知何時要讓已經發行的權杖失效。供密碼同步處理和同盟使用。|
| securityEnabled| | | X| 衍生自 groupType|
| sourceAnchor| X| X| X| 機械屬性。不可變的識別碼，可維持 ADDS 與 Azure AD 之間的關聯性。|
| usageLocation| X| | | 機械屬性。使用者的國家/地區。用於授權指派。|
| userPrincipalName| X| | | UPN 是使用者的登入識別碼。最常與 [mail] 值相同。|



## Dynamics CRM

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 定義是否啟用帳戶。|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| 說明| X| X| X| |
| displayName| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| l| X| X| | |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| objectSID| X| | X| 機械屬性。AD 使用者識別碼，可用來維持 Azure AD 和 AD 之間的同步處理。|
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| pwdLastSet| X| | | 機械屬性。用來得知何時要讓已經發行的權杖失效。供密碼同步處理和同盟使用。|
| securityEnabled| | | X| 衍生自 groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| 機械屬性。不可變的識別碼，可維持 ADDS 與 Azure AD 之間的關聯性。|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| title| X| X| | |
| usageLocation| X| | | 機械屬性。使用者的國家/地區。用於授權指派。|
| userPrincipalName| X| | | UPN 是使用者的登入識別碼。最常與 [mail] 值相同。|

## 協力廠商應用程式
這是作為一般工作負載或應用程式所需基本屬性的一組屬性。它可以用於上文未列出的工作負載或非 Microsoft 應用程式。它會明確用於下列︰

- Yammer (實際使用的只有 User)
- [SharePoint 等資源所提供的混合式企業對企業 (B2B) 跨組織共同作業案例](http://go.microsoft.com/fwlink/?LinkId=747036)

這是一組只有在未使用 Azure AD 目錄來支援 Office 365、Dynamics 或 Intune 時才能使用的屬性。它包含一小組的核心屬性。

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 定義是否啟用帳戶。|
| cn| X| | X| |
| displayName| X| X| X| |
| givenName| X| X| | |
| mail| X| | X| |
| managedBy| | | X| |
| mailNickName| X| X| X| |
| member| | | X| |
| objectSID| X| | | 機械屬性。AD 使用者識別碼，可用來維持 Azure AD 和 AD 之間的同步處理。|
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | 機械屬性。用來得知何時要讓已經發行的權杖失效。供密碼同步處理和同盟使用。|
| sn| X| X| | |
| sourceAnchor| X| X| X| 機械屬性。不可變的識別碼，可維持 ADDS 與 Azure AD 之間的關聯性。|
| usageLocation| X| | | 機械屬性。使用者的國家/地區。用於授權指派。|
| userPrincipalName| X| | | UPN 是使用者的登入識別碼。最常與 [mail] 值相同。|

## Windows 10
Windows 10 已加入網域的電腦 (裝置) 會將某些屬性同步處理至 Azure AD。如需這些案例的詳細資訊，請參閱[將已加入網域裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)。這些屬性會自動同步處理且 Windows 10 不會顯示為您可以取消選取的 app。Windows 10 已加入網域的電腦是透過填入屬性 userCertificate 來識別。

| 屬性名稱| 裝置| 註解 |
| --- | :-: | --- |
| accountEnabled| X| |
| deviceTrustType| X| 已加入網域的電腦的硬式編碼值。 |
| displayName | X| |
| ms-DS-CreatorSID | X| 也稱為 registeredOwnerReference。|
| objectGUID | X| 也稱為 deviceID。|
| objectSID | X| 也稱為 onPremisesSecurityIdentifier。|
| operatingSystem | X| 也稱為 deviceOSType。|
| operatingSystemVersion | X| 也稱為 deviceOSVersion。|
| userCertificate | X| |

這些使用者屬性不包括您已選取的其他應用程式。

| 屬性名稱| 使用者| 註解 |
| --- | :-: | --- |
| domainFQDN| X| 也稱為 dnsDomainName。例如 contoso.com。|
| domainNetBios| X| 也稱為 netBiosName。例如CONTOSO。|

## Exchange 混合回寫
當您選擇啟用 Exchange 混合時，這些屬性會從 Azure AD 被回寫到內部部署 Active Directory。根據您的 Exchange 版本有可能會同步處理較少的屬性。

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| msDS-ExternalDirectoryObjectID| X| | | 衍生自 Azure AD 中的 cloudAnchor。這是 Exchange 2016 中的新功能。|
| msExchArchiveStatus| X| | | 線上封存：可讓客戶封存郵件。|
| msExchBlockedSendersHash| X| | | 篩選：從用戶端回寫內部部署篩選及線上安全和已封鎖的寄件者資料。|
| msExchSafeRecipientsHash| X| | | 篩選：從用戶端回寫內部部署篩選及線上安全和已封鎖的寄件者資料。|
| msExchSafeSendersHash| X| | | 篩選：從用戶端回寫內部部署篩選及線上安全和已封鎖的寄件者資料。|
| msExchUCVoiceMailSettings| X| | | 啟用整合通訊 (UM) - 線上語音信箱：供 Microsoft Lync Server 整合用來向 Lync Server 內部部署表示使用者在線上服務中有語音信箱。|
| msExchUserHoldPolicies| X| | | 訴訟資料暫留：啟用雲端服務來識別哪些使用者正處於訴訟資料暫留狀態。|
| proxyAddresses| X| X| X| 只會插入 Exchange Online 的 x500 位址。|

## 裝置回寫
裝置物件是在 Active Directory 中建立。這些可以是加入 Azure AD 的裝置，或加入網域的 Windows 10 電腦。

| 屬性名稱| 裝置| 註解 |
| --- | :-: | --- |
| altSecurityIdentities | X| |
| displayName | X| |
| dn | X| |
| msDS-CloudAnchor | X| |
| msDS-DeviceID | X| |
| msDS-DeviceObjectVersion | X| |
| msDS-DeviceOSType | X| |
| msDS-DeviceOSVersion | X| |
| msDS-DevicePhysicalIDs | X| |
| msDS-KeyCredentialLink | X| 只在 Windows Server 2016 AD 結構描述才有 |
| msDS-IsCompliant | X| |
| msDS-IsEnabled | X| |
| msDS-IsManaged | X| |
| msDS-RegisteredOwner | X| |


## 注意事項
- 使用替代 ID 時，內部部署屬性 userPrincipalName 將會與 Azure AD 屬性 onPremisesUserPrincipalName 進行同步處理。替代 ID 屬性 (例如 mail) 將會與 Azure AD 屬性 userPrincipalName 進行同步處理。
- 在上列清單中，物件類型 **User** 也適用於物件類型 **iNetOrgPerson**。

## 後續步驟
深入了解 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)組態。

深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0323_2016-->