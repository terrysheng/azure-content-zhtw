<properties
	pageTitle="Azure Active Directory B2C 預覽：建立 Azure Active Directory B2C 租用戶 | Microsoft Azure"
	description="有關如何建立 Azure Active Directory B2C 租用戶的主題"
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
	ms.topic="get-started-article"
	ms.date="02/25/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C 預覽：建立 Azure AD B2C 租用戶

若要開始使用 Microsoft Azure Active Directory (Azure AD) B2C，請遵循本文中所述的三個步驟。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 步驟 1：註冊 Azure 訂用帳戶

如果您已經有 Azure 訂用帳戶，請略過此步驟。如果沒有，請註冊 [Azure 訂用帳戶](../active-directory/sign-up-organization.md)並存取 Azure AD B2C。

> [AZURE.NOTE]
您目前可以免費使用 Azure AD B2C 預覽，但其使用方式是每個租用戶僅限 50,000 位使用者。您必須有 Azure 訂用帳戶，才能存取 [Azure 傳統入口網站](http://manage.windowsazure.com/)。

## 步驟 2：建立 Azure AD B2C 租用戶

請使用下列步驟，建立新的 Azure AD B2C 租用戶。目前您無法在現有目錄 (若有的話) 中開啟 B2C 功能。

1. 以訂用帳戶管理員身分登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。此為您註冊 Azure 時所用的相同工作或學校帳戶，或是相同的 Microsoft 帳戶。
2. 依序按一下 [新增] > [應用程式服務] > [Active Directory] > [目錄] > [自訂建立]。

    ![開始建立租用戶的螢幕擷取畫面](./media/active-directory-b2c-get-started/new-directory.png)

3. 為您的租用戶選擇 [名稱]、[網域名稱] 和 [國家或區域]。
4. 勾選顯示「**這是 B2C 目錄**」的選項。
5. 按一下核取記號以完成動作。

    ![建立 B2C 目錄的核取記號螢幕擷取畫面](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. 您的租用戶現會建立，且將會出現在 Active Directory 擴充功能中。系統亦會將您設為租用戶的「全域管理員」。您可視需要新增其他「全域管理員」。

    > [AZURE.IMPORTANT]
    建立租用戶的所需時間最長為兩分鐘。如果您在租用戶建立期間遇到問題，請參閱[建立 Azure AD 租用戶或 Azure AD B2C 租用戶 - 問題與解決方法](active-directory-b2c-support-create-directory.md)以取得指導方針。

## 步驟 3：瀏覽至 Azure 入口網站上的 B2C 功能刀鋒視窗

1. 瀏覽至位於左側導覽列的 Active Directory 擴充。
2. 在 [目錄] 索引標籤下方，尋找並按一下租用戶。
3. 按一下 [設定] 索引標籤。
4. 按一下 [B2C 管理] 區段中的 [管理 B2C 設定] 連結。

    ![B2C 目錄組態的螢幕擷取畫面](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. 系統會在新的瀏覽器索引標籤或視窗中，開啟顯示 B2C 功能刀鋒視窗的 Azure 入口網站。

    > [AZURE.IMPORTANT]
    有一個已知的問題，此頁面不會正確載入 (適用於少數的租用戶)。重新整理瀏覽器應該可修正它。如果沒有，請連絡 Azure 支援中心。

6. 將此刀鋒視窗釘選至「開始面板」，以方便您存取(釘選工具會在右上角的功能刀鋒視窗中標示為紅色)。

    ![B2C 功能刀鋒視窗的螢幕擷取畫面](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    您可以在 [Azure 傳統入口網站](https://manage.windowsazure.com/)上，管理租用戶的使用者和群組、自助式密碼重設組態，以及公司商標功能。

## 後續步驟

請閱讀 [Azure Active Directory B2C 預覽：註冊您的應用程式](active-directory-b2c-app-registration.md)，了解如何使用 Azure AD B2C 註冊應用程式，以及建置快速啟動應用程式。

<!---HONumber=AcomDC_0302_2016-->