<properties
   pageTitle="整合 Azure Active Directory 單一登入與新取得的應用程式 | Microsoft Azure"
   description="Azure Active Directory 支援對新取得的 SaaS 應用程式啟用單一登入，以在 Azure 入口網站中啟用集中式存取管理"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/09/2015"
      ms.author="curtand"/>

# 整合 Azure Active Directory 單一登入與新取得的應用程式  

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

若要開始為將於組織部署的應用程式設定單一登入，您將使用 Azure Active Directory 中的現有目錄。您可以使用從 Microsoft Azure、Office 365 或 Windows Intune 取得的 Azure AD 目錄。如果您有兩個以上的項目，請參閱[管理 Azure AD 目錄](active-directory-administer.md)來判斷要使用哪一個。

## 其他考量

### 驗證

對於應用程式支援 SAML 2.0、WS-同盟或 OpenID Connect 通訊協定的應用程式，Azure Active Directory 使用簽章憑證來建立信任關係。如需詳細資訊，請參閱[管理同盟單一登入的憑證](active-directory-sso-certs.md)。

對於僅支援 HTML 表單型登入的應用程式，Azure Active Directory 使用「密碼儲存庫存」來建立信任關係。這可讓您組織中的使用者，使用 SaaS 應用程式的使用者帳戶資訊，由 Azure AD 自動登入 SaaS 應用程式。Azure AD 會收集並安全地儲存使用者帳戶資訊和相關的密碼。如需詳細資訊，請參閱[密碼單一登入](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)。

### Authorization

佈建的帳戶可授與通過單一登入驗證的使用者使用應用程式的權限。使用者佈建可以手動方式完成，或是在某些情況下，您可以依據在 Azure Active Directory 中所做的變更來新增和移除 SaaS 應用程式中的使用者資訊。如需使用現有的 Azure AD 連接器自動化佈建的詳細資訊，請參閱[自動化 SaaS 應用程式的使用者佈建和取消佈建](active-directory-saas-app-provisioning.md)。

否則，您可以手動將使用者資訊新增至應用程式，或使用市集中可用的其他佈建解決方案。

### Access

Azure AD 提供幾種可自訂的方式，來對您組織中的使用者部署應用程式。您不會受限於任一特定的部署或存取解決方案。您可以使用[最符合您需求的解決方案](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)。

## 後續步驟

若想為您組織中已有的 SaaS 應用程式啟用單一登入，請參閱[整合 Azure Active Directory SSO 與現有的應用程式](active-directory-sso-integrate-existing-apps.md)

對於您在應用程式庫中找到的 SaaS 應用程式，Azure Active Directory 提供一些[有關如何整合 SaaS 應用程式的教學課程](active-directory-saas-tutorial-list.md)。

如果應用程式不在應用程式庫中，您可以[將應用程式新增至 Azure Active Directory 應用程式庫做為自訂應用程式](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)。

Azure.com 文件庫中還有更多關於這些議題的詳細資訊，請先閱讀[什麼是應用程式存取與單一登入搭配 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

<!---HONumber=Oct15_HO3-->