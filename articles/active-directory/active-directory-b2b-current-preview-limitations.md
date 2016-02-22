<properties
   pageTitle="Azure Active Directory B2B 共同作業目前的預覽限制 | Microsoft Azure"
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
   ms.workload="identity"
   ms.date="02/09/2016"
   ms.author="viviali"/>

# Azure AD B2B 共同作業預覽：目前預覽限制

- 外部使用者無法使用 Multi-Factor Authentication (MFA)。例如，如果 Contoso 有 MFA，但合作夥伴組織沒有，則合作夥伴組織的使用者無法透過 B2B 共同作業獲得 MFA。
- 只能透過 CSV 邀請；不支援個別的邀請和 API 存取。
- 只有 Azure AD 全域系統管理員可以上傳 .csv 檔案。
- 目前不支援向消費者電子郵件地址寄送邀請 (例如，hotmail.com、Gmail.com 或 comcast.net)。
- 未測試內部部署應用程式的外部使用者存取。
- 從實際使用者的目錄中刪除實際使用者時，並不會自動清除外部使用者。
- 不支援向 DL 寄送邀請。
- 透過 CSV 最多可以上傳 2,000 筆記錄。

## 相關文章
請瀏覽有關 Azure B2B 共同作業的其他文章：

- [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
- [運作方式](active-directory-b2b-how-it-works.md)
- [詳細的逐步解說](active-directory-b2b-detailed-walkthrough.md)
- [CSV 檔案格式參考](active-directory-b2b-references-csv-file-format.md)
- [外部使用者權杖格式](active-directory-b2b-references-external-user-token-format.md)
- [外部使用者物件屬性變更](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->