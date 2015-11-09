<properties
   pageTitle="Azure AD B2B 共同作業預覽：運作方式 | Microsoft Azure"
   description="描述 Azure Active Directory B2B 共同作業如何讓企業合作夥伴選擇性地存取您的公司應用程式，以支援公司間的關係"
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
   ms.workload="identity"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Azure Active Directory (Azure AD) B2B 共同作業預覽：運作方式
Azure AD B2B 共同作業是根據邀請和兌換模型。您提供您想要合作的合作對象電子郵件地址，以及您想要讓他們使用的應用程式。Azure AD 會以電子郵件將具有連結的邀請傳送給他們。合作夥伴使用者遵循連結並且會收到提示以使用其 Azure AD 帳戶登入或註冊新的 Azure AD 帳戶。

1. 您的系統管理員會藉由使用 Azure 入口網站上傳[結構化的 .csv 檔案](active-directory-b2b-references-csv-file-format.md)，邀請合作夥伴使用者。
2. 入口網站會將邀請電子郵件傳送給這些合作夥伴使用者。
3. 合作夥伴使用者按一下電子郵件中的連結，會收到提示使用其工作認證 (如果他們已經在 Azure AD) 登入，或註冊為 Azure AD B2B 共同作業使用者。
4. 合作夥伴使用者會被重新導向至他們受邀的應用程式，現在他們有存取權。

## 目錄作業
合作夥伴使用者已身為外部使用者存在於您的 Azure AD 中。這表示您的系統管理員可以像是為您的公司內的使用者所做的一樣，透過 Azure 入口網站或 Azure PowerShell 佈建授權、指派群組成員資格和進一步授與公司應用程式的存取權。

付費 Azure AD 訂用帳戶 (「基本」或「高階」) 並不需要使用 Azure AD B2B，沒有付費 Azure AD 訂用帳戶 (「基本」或「高階」) 的租用戶會獲得下列額外優點：

 - 系統管理員可以將群組指派給應用程式，提供受邀使用者存取權的更簡易管理。
 - 管理租用戶商標是用來給予邀請電子郵件和兌換經驗商標，為受邀合作夥伴使用者提供更多內容。

## 相關文章
 請瀏覽有關 Azure B2B 共同作業的其他文章

 - [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
 - [詳細的逐步解說](active-directory-b2b-detailed-walkthrough.md)
 - [CSV 檔案格式參考](active-directory-b2b-references-csv-file-format.md)
 - [外部使用者權杖格式](active-directory-b2b-references-external-user-token-format.md)
 - [外部使用者物件屬性變更](active-directory-b2b-references-external-user-object-attribute-changes.md)
 - [目前的預覽版本限制](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=Nov15_HO1-->