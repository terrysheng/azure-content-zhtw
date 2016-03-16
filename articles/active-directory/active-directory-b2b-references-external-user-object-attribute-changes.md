<properties
   pageTitle="Azure Active Directory B2B 共同作業預覽版本的外部使用者物件屬性變更 | Microsoft Azure"
   description="Azure Active Directory B2B 可讓企業合作夥伴選擇性地存取您的公司應用程式，以支援公司間的關係"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="02/03/2016"
   ms.author="viviali"/>

# Azure AD B2B 共同作業預覽：外部使用者物件屬性變更

Azure AD 目錄中的每個使用者都以使用者物件形式呈現。Azure AD 中的使用者物件會在 B2B 共同作業邀請/兌換流程的各種階段經歷屬性變更。使用者物件代表目錄中具有會在兌換時 (夥伴使用者按一下邀請電子郵件中的連結) 變更屬性的夥伴使用者。具體而言：

- **SignInName** 和 **AltSecId** 屬性已填入
- **DisplayName** 屬性從使用者主體名稱 (user\_fabrikam.com#EXT#@contoso.com) 變更為登入名稱 (user@fabrikam.com)

在 Azure AD 中追蹤這些屬性，有助您疑難排解夥伴使用者是否已兌換其 B2B 共同作業邀請。

## 相關文章
請瀏覽有關 Azure B2B 共同作業的其他文章：

- [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
- [運作方式](active-directory-b2b-how-it-works.md)
- [詳細的逐步解說](active-directory-b2b-detailed-walkthrough.md)
- [CSV 檔案格式參考](active-directory-b2b-references-csv-file-format.md)
- [外部使用者權杖格式](active-directory-b2b-references-external-user-token-format.md)
- [目前的預覽版本限制](active-directory-b2b-current-preview-limitations.md)
- [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->