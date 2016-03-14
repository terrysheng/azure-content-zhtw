<properties
	pageTitle="Azure Active Directory B2C 預覽：Google+ 組態 | Microsoft Azure"
	description="在受 Azure Active Directory B2C 保護的應用程式中，針對具有 Google+ 帳戶的取用者提供註冊和登入。"
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

# Azure Active Directory B2C 預覽：針對具有 Google+ 帳戶的取用者提供註冊和登入

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 建立 Google+ 應用程式

若要在 Azure Active Directory (Azure AD) B2C 中使用 Google+ 做為身分識別提供者，您必須建立 Google+ 應用程式，並對其提供正確參數。您需要 Google+ 帳戶才能執行此動作。如果您沒有該帳戶，您可以在 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) 上申請。

1. 前往 [Google 開發人員主控台](https://console.developers.google.com/)，並以您的 Google + 帳戶認證登入。
2. 按一下 [建立專案]，輸入**專案名稱**，接著按一下 [建立]。

    ![Google+ - 開始使用](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![Google+ - 新增專案](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. 在左側導覽中，按一下 [API 管理員]，然後按一下 [認證]。
4. 按一下位於頂端的 [OAuth 同意畫面] 索引標籤。

    ![Google+ - 認證](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. 選取或指定有效的**電子郵件地址**、提供**產品名稱**，然後按一下 [儲存]。

    ![Google+ - OAuth 同意畫面](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. 按一下 [新增認證]，然後選擇 [OAuth 用戶端識別碼]。

    ![Google+ - OAuth 同意畫面](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. 在 [**應用程式類型**] 下方，選取 [**Web 應用程式**]。

    ![Google+ - OAuth 同意畫面](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. 提供應用程式的**名稱**，在 [授權 JavaScript 來源] 欄位中輸入 `https://login.microsoftonline.com`，接著在 [授權重新導向 URI] 欄位中輸入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。使用您的租用戶名稱 (例如 contosob2c.onmicrosoft.com) 來取代 **{tenant}**。**{tenant}** 值會區分大小寫。按一下 [建立]。

    ![Google+ - 建立用戶端識別碼](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. 複製 [**用戶端識別碼**] 和 [**用戶端密碼**] 的值。您必須使用這兩個值，將 Google+ 設為租用戶中的身分識別提供者。**用戶端密碼**是重要的安全性認證。

    ![Google+ - 用戶端密碼](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## 將 Google+ 帳戶於租用戶中設定為識別提供者

1. 遵循下列步驟以[瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) (位於 Azure 入口網站上)。
2. 在 B2C 功能刀鋒視窗中，按一下 [**身分識別提供者**]。
3. 按一下刀鋒視窗頂端的 [新增]。
4. 針對身分識別提供者組態，提供容易辨識的**名稱**。例如，輸入 "G+"。
5. 按一下 [識別提供者類型]、選取 [Google]，然後按一下 [確定]。
6. 按一下 [設定此識別提供者]，然後輸入您先前建立的 Google+ 應用程式用戶端識別碼和用戶端密碼。
7. 依序按一下 [確定] 和 [建立]，儲存您的 Google+ 組態。

<!---HONumber=AcomDC_0302_2016-->