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
   ms.date="10/16/2015"
   ms.author="kgremban; liviodlc"/>

# 整合 Azure Active Directory SSO 與現有的應用程式

## 概觀

為組織已使用的應用程式設定單一登入 (SSO) 是與為新應用程式建立新帳戶不同的程序。有幾個基本步驟包括：將應用程式中的使用者身分識別對應到 Azure Active Directory (AD) 身分識別，以及了解整合之後使用者如何體驗登入應用程式。

> [AZURE.NOTE]若要為現有的應用程式設定 SSO，您必須在 Azure AD 和 SaaS 應用程式同時具有全域系統管理員權限。

## 對應使用者帳戶

使用者的身分識別通常有唯一身分識別碼，可能是電子郵件地址或通用個人名稱 (UPN)。您必須將每個使用者的應用程式身分識別連結 (對應) 至其各自的 Azure AD 身分識別。根據您的應用程式驗證的需求，有好幾種方法可完成這項作業。

如需對應應用程式身分識別與 Azure AD 識別身分的詳細資訊，請參閱[自訂 SAML 權杖中發出的宣告](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx)和[自訂佈建的屬性對應](active-directory-saas-customizing-attribute-mappings.md)。

## 了解使用者的登入體驗

當您為已在使用中的應用程式整合 SSO 時，請務必了解使用者經驗將會受到影響。對於所有應用程式，使用者將會開始使用其 Azure AD 認證來登入。此外，他們也可能需要使用不同的入口網站來存取應用程式。

某些應用程式的 SSO 可在應用程式本身的登入介面上執行，但對於其他應用程式，使用者必須透過中央應用程式入口網站 (例如[我的應用程式](http://myapps.microsoft.com)或 [Office365](http://portal.office.com/myapps)) 來登入。請在[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)中深入了解不同類型的 SSO 與其的使用者體驗。

請參閱[引導開發人員](active-directory-applications-guiding-developers-for-lob-applications.md)文章中的*隱藏使用者同意*。

## 相關文章
隱藏使用者同意和其他引導開發人員子文章的連結

<!---HONumber=Oct15_HO4-->