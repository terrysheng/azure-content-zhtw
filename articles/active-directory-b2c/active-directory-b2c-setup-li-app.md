<properties
	pageTitle="Azure Active Directory B2C 預覽：LinkedIn 組態 | Microsoft Azure"
	description="在受 Azure Active Directory B2C 保護的應用程式中，為具有 LinkedIn 帳戶的取用者提供註冊和登入"
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

# Azure Active Directory B2C 預覽：為具有 LinkedIn 帳戶的取用者提供註冊和登入

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 建立 LinkedIn 應用程式

如果要在 Azure Active Directory (Azure AD) B2C 中將 LinkedIn 作為識別提供者，您必須建立 LinkedIn 應用程式，然後為其提供正確的參數。您需要 LinkedIn 帳戶才能執行此動作。如果您沒有該帳戶，您可以在 [https://www.linkedin.com/](https://www.linkedin.com/) 上申請。

1. 前往 [LinkedIn 開發人員網站](https://www.developer.linkedin.com/)，並以您的 LinkedIn 帳戶認證登入。
2. 按一下最上方功能表列的 [**我的應用程式**]，然後按一下 [**建立應用程式**]。

    ![LinkedIn - 新建應用程式](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. 在 [建立新的應用程式] 表單中，填入相關資訊 (**公司名稱**、**名稱**、**說明**、**應用程式標誌 URL**、**應用程式使用**、**網站 URL**、**公司電子郵件**和**公司電話**)。
4. 同意 **LinkedIn API 使用條款**，然後按一下 [**提交**]。

    ![LinkedIn - 註冊應用程式](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. 複製 [**用戶端識別碼]** 和 [**用戶端密碼**] 的值。(您可以在 [**驗證金鑰**] 下方找到這些資訊)。 您必須使用這兩個值，將 LinkedIn 設為租用戶中的身分識別提供者。

	>[AZURE.NOTE] **用戶端密碼**是重要的安全性認證。

6. 在 [**授權重新導向 URL**] 欄位 (位於 [**OAuth 2.0**] 下方) 中輸入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。使用您的租用戶名稱 (例如 contoso.onmicrosoft.com) 取代 **{tenant}**。按一下 [**新增**]，然後按一下 [**更新**]。

    ![LinkedIn - 設定應用程式](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## 將 LinkedIn 設定為您租用戶中的識別提供者

1. 遵循下列步驟以瀏覽至 Azure 入口網站上的 [B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 在 B2C 功能刀鋒視窗中，按一下 [**身分識別提供者**]。
3. 按一下刀鋒視窗頂端的 [+新增]。
4. 針對身分識別提供者組態，提供容易辨識的**名稱**。例如，輸入「LI」。
5. 按一下 [**身分識別提供者類型**]、選取 [**LinkedIn**]，然後按一下 [**確定**]。
6. 按一下 [**設定此身分識別提供者**]，然後輸入您先前建立之 LinkedIn 應用程式的用戶端識別碼與用戶端密碼。
7. 按一下 [**確定**]，然後在按一下 [**建立**]，儲存您的 LinkedIn 組態。

<!---HONumber=AcomDC_0224_2016-->