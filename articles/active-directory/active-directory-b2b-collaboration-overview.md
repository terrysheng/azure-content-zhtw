<properties
   pageTitle="Azure Active Directory 企業對企業 (B2B) 共同作業"
   description="Azure Active Directory B2B 共同作業可讓商務夥伴存取您的公司應用程式"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="msStevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2015"
   ms.author="curtand"/>

# Azure Active Directory B2B 共同作業

Azure Active Directory B2B 共同作業允許以合作夥伴管理的身分識別來存取您的公司應用程式。您可以建立跨公司的關聯性，邀請並授權合作夥伴公司的使用者存取您的資源。複雜性降低，因為每一家公司與 Azure Active Directory (Azure AD) 只同盟一次，且每一位使用者都由單一 Azure AD 帳戶來代表。安全性提高，因為當合作夥伴使用者從他們的組織中離職時，存取權即撤銷，絕不可能再透過內部目錄中的成員資格來存取。對於還沒有 Azure AD 的商務夥伴，B2B 共同作業有簡便的註冊體驗提供 Azure AD 帳戶給您的商務夥伴。

-   您的商務夥伴使用他們自己的登入認證，您不必管理外部合作夥伴目錄，也不需要在使用者離開合作夥伴組織時移除存取權。

-   您可以獨立管理您的應用程式，與商務夥伴的帳戶生命週期無關。例如，這表示您可以自行撤銷存取權，而不必要求商務夥伴的 IT 部門進行任何動作。

## 功能

B2B 共同作業簡化管理並改善合作夥伴存取公司資源時的安全性，包括 SaaS 應用程式 (例如 Office 365、Salesforce、Azure 服務)，以及每個行動、雲端和內部部署宣告感知應用程式。B2B 共同作業可讓合作夥伴管理自己的帳戶，企業可以將安全性原則套用至合作夥伴存取權。

Azure Active Directory B2B 共同作業的設定很簡單，各種規模的合作夥伴都能透過電子郵件驗證程序，輕鬆地完成註冊，即使他們沒有自己的 Azure Active Directory 也沒問題。維護也很容易，不需要外部目錄或個別合作夥伴的同盟設定。

程序：

1. Azure AD B2B 共同作業可讓公司系統管理員將一個不超過 2000 行的逗號分隔值 (CSV) 檔案上傳至 B2B 共同作業入口網站，就能邀請並授權一群外部使用者。

  ![CSV 檔案上傳對話方塊](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. 入口網站會傳送電子郵件邀請給這些外部使用者。

3. 受邀的使用者將透過 Microsoft 登入現有的工作帳戶 (在 Azure AD 中管理)，或在 Azure AD 中取得新的工作帳戶。

4. 登入之後，使用者將重新導向至與他們共用的應用程式。

目前不支援邀請消費者電子郵件地址 (例如，gmail 或 [*comcast.net*](http://comcast.net/))。

如需有關 B2B 共同作業運作方式的詳細資訊，請觀賞[這段影片](http://aka.ms/aadshowb2b)。

## CSV 檔案格式

CSV 檔案採用以下的格式。

**電子郵件：**受邀使用者的電子郵件地址。<br/> **DisplayName：**受邀使用者的顯示名稱 (通常是名字和姓氏)。<br/> **InviteAppID：**在電子郵件邀請和接受網頁上用於表示商標的應用程式識別碼。<br/> **InviteReplyURL：**在受邀用者接受邀請後，將使用者導向此 URL。這應該是公司專屬的 URL (例如 [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/))。<br/> **InviteAppResources：**供應用程式指派使用者的 AppID。您可以呼叫 `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` 擷取 AppID<br/> **InviteGroupResources：**供群組新增使用者的 ObjectID。您可以呼叫 `Get-MsolGroup | fl DisplayName, ObjectId` 擷取 ObjectID<br/> **InviteContactUsUrl：**可加入電子郵件邀請中的「連絡我們」URL，方便受邀使用者連絡您的組織。<br/>

## CSV 檔案範例
以下是 CSV 範例，請依用途自行修改。將它儲存為您喜歡的任何檔案名稱，但必須確定副檔名為 '.csv'。

```
Email,DisplayName,InviteAppID,InviteReplyUrl,InviteAppResources,InviteGroupResources,InviteContactUsUrl
wharp@contoso.com,Walter Harp,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/<br/>
jsmith@contoso.com,Jeff Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/<br/>
bsmith@contoso.com,Ben Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
```

<!---HONumber=Sept15_HO3-->