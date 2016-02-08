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
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C 預覽：如何建立 Azure AD B2C 租用戶

若要開始使用 Azure Active Directory (AD) B2C，請遵循以下所述的 3 個步驟。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 步驟 1：註冊 Azure 訂用帳戶

若您已有 Azure 訂用帳戶，請繼續進行下一個步驟。若沒有，請註冊 [Azure 訂用帳戶](sign-up-organization.md)並存取 Azure AD B2C。

> [AZURE.NOTE]
Azure AD B2C 預覽目前提供免費使用但有限制 (每個租用戶最多允許 50,000 位使用者)。必須有 Azure 訂用帳戶才能存取 [Azure 傳統入口網站](http://manage.windowsazure.com/)。

## 步驟 2：建立 Azure AD B2C 租用戶

請使用下列步驟，建立新的 Azure AD B2C 租用戶。目前您無法在現有目錄 (若有的話) 中開啟 B2C 功能。

1. 以「訂用帳戶管理員」身分登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。此為與您註冊 Azure 時所用的相同工作或學校帳戶，或是相同的「Microsoft 帳戶」。
2. 依序按一下 [新增] > [應用程式服務] > [Active Directory] > [目錄] > [自訂建立]。

    ![建立租用戶](./media/active-directory-b2c-get-started/new-directory.png)

3. 選擇 [名稱]、[網域名稱] 和 [國家或區域] 做為您的租用戶。
4. 檢查顯示「**這是 B2C 目錄**」的選項。
5. 按一下核取記號以完成動作。

    ![建立 B2C 租用戶](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. 您的租用戶現會建立，且將會出現在 Active Directory 擴充功能中。系統亦會將您設為租用戶的「全域管理員」。您可視需要新增其他「全域管理員」。

    > [AZURE.IMPORTANT]
    建立租用戶的所需時間最長為兩分鐘。若在租用戶建立過程中發生問題，請參閱[本文](active-directory-b2c-support-create-directory.md)中提供的指引。

## 步驟 3：瀏覽至 Azure 入口網站上的 B2C 功能刀鋒視窗

1. 瀏覽至位於左側導覽列的 Active Directory 延伸模組。
2. 在 [目錄] 索引標籤下方，尋找並按一下租用戶。
3. 按一下 [設定] 索引標籤。
4. 按一下 [管理 B2C 設定] 連結 (位於 [B2C 管理] 區段)。

    ![建立 B2C 租用戶](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

4. 系統會在新的瀏覽器索引標籤或視窗中，開啟具有 B2C 功能刀鋒視窗的 Azure 入口網站。

    > [AZURE.IMPORTANT]
    有一個已知的問題，此頁面不會正確載入 (適用於少數的租用戶)。重新整理瀏覽器應該可修正它。否則請連絡支援人員。

5. 將此刀鋒視窗 (查看右上角) 釘選至「開始面板」，以方便您存取。

    ![B2C 功能刀鋒視窗](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    您可在 [Azure 傳統入口網站](https://manage.windowsazure.com/)上，管理租用戶的使用者和群組、自助式密碼重設組態，以及公司商標功能。

## 後續步驟

移至[使用 Azure AD B2C 註冊應用程式並建置快速啟動應用程式](active-directory-b2c-app-registration.md)。

<!---HONumber=AcomDC_0128_2016-->