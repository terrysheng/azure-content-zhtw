<properties
	pageTitle="Azure Active Directory B2C 預覽：自助式密碼重設 | Microsoft Azure"
	description="此主題說明如何在 Azure Active Directory B2C 中為取用者設定自助式密碼重設"
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
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C 預覽：針對取用者設定自助式密碼重設

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

此功能可讓取用者 (已註冊本機帳戶) 重設自己的密碼。這可大幅減輕支援人員的負擔，特別是在您的應用程式具有數百萬名定期使用的取用者時更是如此。目前僅支援使用已驗證的電子郵件地址做為復原方法。我們將在未來新增其他復原方法 (例如已驗證的電話號碼、安全性問題等)。依預設，您的目錄並不會開啟自助式密碼重設。使用下列步驟將其開啟：

1. 以「訂用帳戶管理員」身分登入 [Azure 入口網站](https://manage.windowsazure.com/)。此為與您建立目錄時所用相同的工作或學校帳戶，或是相同的 Microsoft 帳戶。
2. 瀏覽至位於左側導覽列的 Active Directory 延伸模組。
3. 在 [目錄] 索引標籤下方，尋找並按一下目錄。
4. 按一下 [設定] 索引標籤。
5. 向下捲動至 [使用者密碼重設原則] 區段，然後將 [使用者啟用密碼重設] 選項切換為 [是]。請注意，系統會勾選 [替代電子郵件地址] 選項；請將其保留原狀。

    ![自助式密碼重設](./media/active-directory-b2c-reference-sspr/sspr.png)
 
6. 按一下頁面底部的 [儲存]。大功告成！

若要進行測試，請針對任何登入原則 (已使用本機帳戶做為身分識別提供者) 使用「立即執行」功能。在本機帳戶登入頁面上 (您會在其中輸入電子郵件地址和密碼，或是使用者名稱和密碼)，按一下 [無法存取您的帳戶？] 以驗證取用者體驗。

> [AZURE.NOTE]您可使用 [公司商標功能][](active-directory-add-company-branding.md) 自訂自助式密碼重設頁面。

<!---HONumber=Sept15_HO3-->