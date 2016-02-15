<properties
   pageTitle="Azure Active Directory B2B 共同作業預覽版本的外部使用者權杖格式 | Microsoft Azure"
   description="Azure Active Directory B2B 可讓企業合作夥伴選擇性地存取您的公司應用程式，以支援公司間的關係"
   services="active-directory"
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

# Azure AD B2B 共同作業預覽：外部使用者權杖格式

azure.microsoft.com 上的[支援的權杖和宣告類型](active-directory-token-and-claims.md)一文描述標準 Azure AD 權杖的宣告。

已驗證之 B2B 共同作業外部使用者使用下列不同的宣告：<br/> - **OID**：資源租用戶的物件識別碼<br/> - **TID**：資源租用戶的租用戶識別碼<br/> - **簽發者**：這是資源租用戶<br/> - **IDP**：這是使用者的家用租用戶<br/> - **AltSecId**：這是會對您隱藏的替代安全性識別碼<br/>

## 相關文章
請瀏覽有關 Azure B2B 共同作業的其他文章：

- [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
- [運作方式](active-directory-b2b-how-it-works.md)
- [詳細的逐步解說](active-directory-b2b-detailed-walkthrough.md)
- [CSV 檔案格式參考](active-directory-b2b-references-csv-file-format.md)
- [外部使用者物件屬性變更](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [目前的預覽版本限制](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=AcomDC_0204_2016-->