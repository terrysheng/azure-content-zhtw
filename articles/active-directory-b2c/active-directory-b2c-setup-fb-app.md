<properties
	pageTitle="Azure Active Directory B2C 預覽：Facebook 組態 | Microsoft Azure"
	description="在受 Azure Active Directory B2C 保護的應用程式中，針對具有 Facebook 帳戶的取用者提供註冊和登入"
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
	ms.date="10/08/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C 預覽：針對具有 Facebook 帳戶的取用者提供註冊和登入

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 建立 Facebook 應用程式

若要在 Azure Active Directory (AD) B2C 中使用 Facebok 做為身分識別提供者，您必須先建立 Facebook 應用程式，然後再對其提供正確參數。執行此動作必須具有 Facebook 帳戶；若您沒有該帳戶，則可在 [https://www.facebook.com/](https://www.facebook.com/) 取得帳戶。

1. 前往 [Facebook 開發人員網站](https://developers.facebook.com/)，並以您的 Facebook 帳戶認證登入。
2. 若您尚未完成此動作，請依序按一下 [應用程式]、[以開發人員身分註冊]，接受政策並遵循註冊步驟完成此動作。
3. 按一下 [應用程式]，然後按一下 [新增應用程式]。接著再選擇 [網站] 做為平台，然後按一下[略過並建立應用程式識別碼]。

    ![FB - 新建應用程式](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![FB - 新建應用程式 - 網站](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![FB - 建立應用程式識別碼](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. 在表單上提供**顯示名稱**，選擇適當 [類別]，然後按一下 [建立應用程式識別碼]。注意：這會要求您接受「Facebook 平台原則」並完成線上「安全性檢查」。

    ![FB - 建立應用程式識別碼](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. 在左側導覽中按一下 [設定]。輸入有效的**連絡人電子郵件**。
6. 按一下 [新增平台]，然後選取 [網站]。

    ![FB - 設定](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![FB - 設定](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. 在 [網站 URL] 欄位中輸入 [https://login.microsoftonline.com/](https://login.microsoftonline.com/)，然後按一下 [儲存變更]。
8. 複製 [應用程式識別碼] 的值。按一下 [顯示]，並複製 [應用程式密碼]的值。您必須同時使用這兩個值，將 Facebook 設定為您目錄中的身分識別提供者。

    > [AZURE.NOTE]**應用程式密碼**是重要的安全性認證。

    ![FB - 網站 URL](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

9. 按一下頂端的 [進階] 索引標籤，然後在 [有效的 OAuth 重新導向 URI] 欄位 (位於 [安全性] 區段) 中輸入 [https://login.microsoftonline.com/te/{directory}/oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp)，其中 **{directory}** 會取代為您目錄的名稱 (例如 contosob2c.onmicrosoft.com)。按一下頁面底部的 [儲存變更]。

    ![FB - OAuth 重新導向 URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

10. 若要讓您的 Facebook 應用程式可供 Azure AD B2C 使用，您必須將其設定為對外公開。若要執行此動作，請在左側導覽中按一下 [狀態與檢閱]，然後將頁面頂端的開關切換為 [是]。然後按一下 [確認]。

    ![FB - OAuth 重新導向 URI](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## 將 Facebook 設定為您目錄中的身分識別提供者

1. [瀏覽至 Azure Preview 入口網站上的 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 在 B2C 功能刀鋒視窗中，按一下 [**身分識別提供者**]。
3. 按一下刀鋒視窗頂端的 [新增]。
4. 針對身分識別提供者組態，提供容易辨識的**名稱**。例如，輸入「FB」。
5. 按一下 [身分識別提供者類型]、選取 [Facebook]，然後按一下 [確定]。
6. 按一下 [設定此身分識別提供者]，然後在 [應用程式識別碼] 與 [應用程式密碼] 欄位中，分別輸入您先前建立之 Facebook 應用程式的**應用程式識別碼** 和**應用程式密碼**。
7. 依序按一下 [確定]與 [建立]，以儲存您的 Facebook 組態。

<!---HONumber=Oct15_HO3-->