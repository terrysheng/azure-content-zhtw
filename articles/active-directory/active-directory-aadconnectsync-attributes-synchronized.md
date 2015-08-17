<properties
	pageTitle="Azure AD Connect 同步處理：將屬性同步處理至 Azure Active Directory"
	description="列出要同步處理至 Azure Active Directory 的屬性。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect 同步處理：將屬性同步處理至 Azure Active Directory

本主題列出 Azure AD Connect 同步處理會同步處理的屬性。<br> 屬性會依相關的 Azure AD App 來分組。
 




## Office 365 ProPlus

| 屬性名稱| 使用者| 註解 |
| --- | :-: | --- |
| accountEnabled| X| 衍生自 userAccountControl|
| cn| X| |
| displayName| X| |
| objectSID| X| |
| pwdLastSet| X| |
| sourceAnchor| X| 這個適用於使用者的屬性是設定於安裝指南中。|
| usageLocation| X| AD DS 中的 msExchUsageLocation|
| userPrincipalName| X| |


## Exchange Online

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 衍生自 userAccountControl|
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
| info| X| X| X| |
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
| msExchExtensionCustomAttribute1| X| X| X| |
| msExchExtensionCustomAttribute2| X| X| X| |
| msExchExtensionCustomAttribute3| X| X| X| |
| msExchExtensionCustomAttribute4| X| X| X| |
| msExchExtensionCustomAttribute5| X| X| X| |
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
| objectSID| X| | X| |
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| proxyAddresses| X| X| X| |
| publicDelegates| X| X| X| |
| pwdLastSet| X| | | |
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| 衍生自 groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| 這個適用於使用者的屬性是設定於安裝指南中。|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| usageLocation| X| | | AD DS 中的 msExchUsageLocation|
| userCertificate| X| X| | |
| userPrincipalName| X| | | |
| userSMIMECertificates| X| X| | |
| wWWHomePage| X| X| | |


## SharePoint Online

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 衍生自 userAccountControl|
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
| objectSID| X| | X| |
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
| pwdLastSet| X| | | |
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| 衍生自 groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| 這個適用於使用者的屬性是設定於安裝指南中。|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| url| X| X| | |
| usageLocation| X| | | AD DS 中的 msExchUsageLocation|
| userPrincipalName| X| | | |
| wWWHomePage| X| X| | |

## Lync Online

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 衍生自 userAccountControl|
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
| objectSID| X| | X| |
| otherTelephone| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| securityEnabled| | | X| 衍生自 groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| 這個適用於使用者的屬性是設定於安裝指南中。|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| usageLocation| X| | | AD DS 中的 msExchUsageLocation|
| userPrincipalName| X| | | |
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
| accountEnabled| X| | | 衍生自 userAccountControl|
| c| X| X| | |
| cn| X| | X| |
| 說明| X| X| X| |
| displayName| X| X| X| |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| member| | | X| |
| objectSID| X| | X| |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| securityEnabled| | | X| 衍生自 groupType|
| sourceAnchor| X| X| X| 這個適用於使用者的屬性是設定於安裝指南中。|
| usageLocation| X| | | AD DS 中的 msExchUsageLocation|
| userPrincipalName| X| | | |


## Dynamics CRM

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 衍生自 userAccountControl|
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
| objectSID| X| | X| |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| pwdLastSet| X| | | |
| securityEnabled| | | X| 衍生自 groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| 這個適用於使用者的屬性是設定於安裝指南中。|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| title| X| X| | |
| usageLocation| X| | | AD DS 中的 msExchUsageLocation|
| userPrincipalName| X| | | |


## 其他資源

* [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=August15_HO6-->