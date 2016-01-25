<properties
	pageTitle="Azure Active Directory B2C 預覽：Microsoft 帳戶組態 | Microsoft Azure"
	description="在受 Azure Active Directory B2C 保護的應用程式中，針對具有 Microsoft 帳戶的取用者提供註冊和登入"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C 預覽：針對具有 Microsoft 帳戶的取用者提供註冊和登入

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 建立 Microsoft 帳戶應用程式

若要在 Azure Active Directory (AD) B2C 中使用 Microsoft 帳戶做為身分識別提供者，您必須先建立 Microsoft 帳戶應用程式，然後再對其提供正確參數。執行此動作必須具有 Microsoft 帳戶；若您沒有該帳戶，則可在 [https://www.live.com/](https://www.live.com/) 取得帳戶。

1. 移至 [Microsoft 帳戶開發人員中心](https://account.live.com/developers/applications)，並使用您的「Microsoft 帳戶」認證登入。
2. 按一下 [建立應用程式]。

    ![MSA - 新增應用程式](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. 提供**應用程式名稱**，然後按一下 [我接受]。注意：您需要接受 Microsoft 服務的使用規定。

    ![MSA - 應用程式名稱](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. 在左側導覽中按一下 [API 設定]。輸入有效的**連絡人電子郵件**。

    ![MSA - API 設定](./media/active-directory-b2c-setup-msa-app/msa-api-settings.png)

5. 在 [重新導向 URL] 欄位中輸入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`，其中 **{tenant}** 要取代為您租用戶的名稱 (例如 contosob2c.onmicrosoft.com)。按一下頁面底部的 [儲存]。

    ![MSA - 重新導向 URL](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

6. 在左側導覽中按一下 [應用程式設定]。複製 [**用戶端識別碼**] 和 [**用戶端密碼**] 的值。您必須使用這兩個值，將 Microsoft 帳戶設為租用戶中的身分識別提供者。注意：[用戶端密碼] 是重要的安全性認證。

    ![MSA - 用戶端密碼](./media/active-directory-b2c-setup-msa-app/msa-client-secret.png)

## 將 Microsoft 帳戶設為您租用戶中的身分識別提供者

1. [遵循下列步驟以瀏覽至 Azure 入口網站上的 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 在 B2C 功能刀鋒視窗中，按一下 [**身分識別提供者**]。
3. 按一下刀鋒視窗頂端的 [+新增]。
4. 針對身分識別提供者組態，提供容易辨識的**名稱**。例如，輸入 "MSA"。
5. 按一下 [身分識別提供者類型]、選取 [Microsoft 帳戶]，然後按一下 [確定]。
6. 按一下 [設定此身分識別提供者]，然後輸入您先前建立之 Microsoft 帳戶應用程式的 [用戶端識別碼] 與 [用戶端密碼]。
7. 依序按一下 [確定] 與 [建立]，以儲存您的 Microsoft 帳戶組態。

<!---HONumber=AcomDC_0114_2016-->