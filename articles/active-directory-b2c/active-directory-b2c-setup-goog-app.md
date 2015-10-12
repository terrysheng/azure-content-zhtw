<properties
	pageTitle="Azure Active Directory B2C 預覽：Google+ 組態 | Microsoft Azure"
	description="在受 Azure Active Directory B2C 保護的應用程式中，針對具有 Google+ 帳戶的取用者提供註冊和登入"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C 預覽：針對具有 Google+ 帳戶的取用者提供註冊和登入

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 建立 Google+ 應用程式

若要在 Azure Active Directory (AD) B2C 中使用 Google+ 做為身分識別提供者，您必須先建立 Google+ 應用程式，然後再對其提供正確參數。執行此動作必須具有 Google+ 帳戶；若您沒有該帳戶，則可在 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) 取得帳戶。

1. 前往 [Google 開發人員主控台](https://console.developers.google.com/)，並以您的 Google + 帳戶認證登入。
2. 按一下 [建立專案]，輸入**專案名稱**，同意「服務條款」，然後按一下 [建立]。

    ![G+ - 開始使用](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![G+ - 新增專案](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. 在左側導覽中，按一下 [APIs & Auth]，然後按一下 [認證]。
4. 按一下位於頂端的 [OAuth 同意畫面] 索引標籤。

    ![G+ - 認證](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. 選取或指定有效的**電子郵件地址**，提供**產品名稱**，然後按一下 [儲存]。

    ![G+ - OAuth 同意畫面](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. 在 [應用程式類型] 下方，選取 [Web 應用程式]。
7. 提供您應用程式的**名稱**，在 [授權的重新導向 URI] 欄位中輸入 [https://login.microsoftonline.com](https://login.microsoftonline.com)，並在 [授權的重新導向 URI] 欄位中輸入 [https://login.microsoftonline.com/te/{tenant}/oauth2/authresp](https://login.microsoftonline.com/te/{tenant}/oauth2/authresp)，其中 **{tenant}** 要取代為您租用戶的名稱 (例如 contosob2c.onmicrosoft.com)。按一下 [建立]。

    > [AZURE.NOTE]**{tenant}** 值會區分大小寫。

    ![G+ - 建立用戶端識別碼](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

8. 複製 [用戶端識別碼] 和 [用戶端密碼] 的值。您必須使用這兩個值，將 Google+ 設為租用戶中的身分識別提供者。

    > [AZURE.NOTE]**用戶端密碼**是重要的安全性認證。

    ![G+ - 用戶端密碼](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## 將 Google+ 設為您租用戶中的身分識別提供者

1. [瀏覽至 Azure Preview 入口網站上的 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 在 B2C 功能刀鋒視窗中，按一下 [社交身分識別提供者]。
3. 按一下刀鋒視窗頂端的 [新增]。
4. 針對身分識別提供者組態，提供容易辨識的**名稱**。例如，輸入 "G+"。
5. 按一下 [身分識別提供者類型]、選取 [Google]，然後按一下 [確定]。
6. 按一下 [設定此身分識別提供者]，然後輸入您先前建立之 Google+ 應用程式的 [用戶端識別碼] 與 [用戶端密碼]。
7. 依序按一下 [確定]、[建立]，以儲存您的 Google+ 組態。

<!---HONumber=Oct15_HO1-->