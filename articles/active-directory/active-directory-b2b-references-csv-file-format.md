<properties
   pageTitle="Azure Active Directory B2B 共同作業預覽版本的 CSV 檔案格式 | Microsoft Azure"
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
   ms.workload="identity"
   ms.date="02/03/2016"
   ms.author="viviali"/>

# Azure AD B2B 共同作業預覽：CSV 檔案格式

預覽版本的 Azure AD B2B 共同作業需要指定合作夥伴使用者資訊的 CSV 檔案透過 Azure AD 入口網站上傳。CSV 檔案應該包含下列必要的標籤，以及視需要包含選擇性欄位。修改範例 CSV 檔案 (如下) 而不變更第一個資料列中的標籤拼字或重新排列資料行。

>[AZURE.NOTE] 需要標籤的第一列 (例如電子郵件、DisplayName...)，才能成功剖析 CSV 檔案。拼字必須符合以下指定的欄位。這些標籤會識別在其下的內容。對於不必要的選擇性欄位，可以從 CSV 檔案移除它們的標籤。整個資料行可以是空白。

## 必要欄位：<br/>
**電子郵件：**受邀使用者的電子郵件地址。<br/> **DisplayName：**受邀使用者的顯示名稱 (通常是名字和姓氏)。<br/> **InviteContactUsUrl：**可加入電子郵件邀請中的 URL，方便受邀使用者連絡您的組織。<br/>

## 選擇性欄位：<br/>
**InviteAppID：**在電子郵件邀請和接受網頁上用於表示商標的應用程式識別碼。<br/> **InviteAppResources：**供公司應用程式指派使用者的 AppID。您可以在 PowerShell 中呼叫 `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/> **InviteGroupResources：**供群組新增使用者的 ObjectID，來擷取 AppID。您可以在 PowerShell 中呼叫 `Get-MsolGroup | fl DisplayName, ObjectId`<br/> **InviteReplyURL：**在受邀用者接受邀請後，將使用者導向此 URL，來擷取 ObjectID。這應該是公司專屬的 URL (例如 [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/))。如果未指定這個選擇性欄位，受邀使用者會被導向至應用程式存取面板，他們可以在那裡瀏覽至您所選擇的公司應用程式。應用程式存取面板 URL 格式為 `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`。<br/> **語言：**邀請電子郵件和兌換經驗的語言，未指定時預設值為英文。其他 10 個支援的語言代碼是：<br/>1. de：德文<br/> 2. es：西班牙文<br/> 3. fr：法文<br/> 4. it：義大利文<br/> 5. ja：日文<br/> 6. ko：韓文<br/> 7. pt-BR：葡萄牙文 (巴西)<br/> 8. ru：俄文<br/> 9. zh-HANS：簡體中文<br/> 10. zh-HANT：繁體中文<br/>

## CSV 檔案範例
以下是您可以修改的範例 CSV 範例。

>[AZURE.NOTE] 將這個項目複製並貼上至 [記事本]，然後以 '.csv' 副檔名儲存。然後在 Excel 中編輯它。它會結構化成為在第一個資料列中具有標籤的資料表。

>[AZURE.NOTE] 在 Excel 中新增更多選擇性欄位，方法是指定標籤及填入其下方的資料行。

```
Email,DisplayName,InviteAppID,InviteReplyUrl,InviteAppResources,InviteGroupResources,InviteContactUsUrl
wharp@contoso.com,Walter Harp,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
jsmith@contoso.com,Jeff Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
bsmith@contoso.com,Ben Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
```

## 相關文章
請瀏覽有關 Azure B2B 共同作業的其他文章

- [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
- [運作方式](active-directory-b2b-how-it-works.md)
- [詳細的逐步解說](active-directory-b2b-detailed-walkthrough.md)
- [外部使用者權杖格式](active-directory-b2b-references-external-user-token-format.md)
- [外部使用者物件屬性變更](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [目前的預覽版本限制](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=AcomDC_0204_2016-->