<properties
   pageTitle="整合 Azure Active Directory SSO 與現有的應用程式 | Microsoft Azure"
   description="在 Azure Active Directory 啟用單一登入驗證和使用者佈建，來管理您已經使用的 SaaS 應用程式。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/05/2015"
   ms.author="kgremban; liviodlc"/>

# 整合 Azure Active Directory SSO 與現有的應用程式

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

## 其他考量

為您已使用的應用程式設定 SSO，此一程序與在新的應用程式中建立新帳戶不同。當應用程式整合到 Azure AD 時，系統管理員和使用者都將遭遇變更。

### 系統管理員將必須知道什麼，才能為已在使用中的應用程式設定單一登入?

若要為現有的應用程式設定 SSO，您必須在 Azure AD 和 SaaS 應用程式同時具有全域系統管理員權限。

因為應用程式已在使用中，所以您需要將使用者建立的應用程式身分識別連結到其各自的 Azure AD 識別身分。請務必知道此應用程式使用什麼，做為登入的唯一識別碼，以及它是電子郵件地址、 通用個人名稱 (UPN)，還是使用者名稱。這將連結到使用者在 Azure AD 的唯一識別碼。如需連結應用程式身分識別與 Azure AD 識別身分的詳細資訊，請參閱[自訂 SAML 權杖中發出的宣告](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx)和[自訂佈建的屬性對應](active-directory-saas-customizing-attribute-mappings.md)。

### 這會如何影響使用者？

當您為已在使用中的應用程式整合 SSO 時，請務必了解使用者經驗將會受到影響。對於所有應用程式，使用者將會開始使用其 Azure AD 認證來登入。此外，他們也可能需要使用不同的入口網站來存取應用程式。某些應用程式的 SSO 可在應用程式本身的網站上執行，但對於其他應用程式，使用者必須通過中央應用程式入口網站 ([我的應用程式](myapps.microsoft.com)或 [Office365](portal.office.com/myapps)) 登入。請在[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)中深入了解不同類型的 SSO 與其的使用者體驗。

## 後續步驟
- 請參閱如何[整合 Azure Active Directory 單一登入與新取得的應用程式](active-directory-sso-newly-acquired-saas-apps.md)
- 深入了解[搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)
- 尋找[如何整合 SaaS 應用程式的教學課程](active-directory-saas-tutorial-list.md)
-	新增自訂的應用程式與 [Azure AD 自助 SAML 組態](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)

<!---HONumber=Oct15_HO3-->