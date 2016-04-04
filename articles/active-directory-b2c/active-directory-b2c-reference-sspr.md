<properties
	pageTitle="Azure Active Directory B2C 預覽：自助式密碼重設 | Microsoft Azure"
	description="此主題示範如何在 Azure Active Directory B2C 中為您的取用者設定自助式密碼重設"
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
	ms.date="03/22/2016"
	ms.author="swkrish"/>


# Azure Active Directory B2C 預覽：為您的取用者設定自助式密碼重設

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

自助式密碼重設功能可讓您的取用者 (已註冊本機帳戶的使用者) 自行重設自己的密碼。這可大幅減輕支援人員的負擔，特別是在您的應用程式具有數百萬名定期使用的取用者時更是如此。目前僅支援使用已驗證的電子郵件地址做為復原方法。我們將在未來新增其他復原方法 (例如已驗證的電話號碼、安全性問題等)。

> [AZURE.NOTE]
本文適用於登入原則內容中使用的自助式密碼重設。如果您需要從應用程式叫用的可完全自訂密碼重設原則，請參閱[這篇文章](./active-directory-b2c-reference-policies.md#create-a-password-reset-policy)。

依預設，您的目錄並不會開啟自助式密碼重設。使用下列步驟將其開啟：

1. 以訂用帳戶管理員身分登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。此為您建立目錄時所用的工作或學校帳戶，或者當時所用的 Microsoft 帳戶。
2. 瀏覽至左側導覽列上的 Active Directory 擴充。
3. 在 [目錄]索引標籤下方，找到您的目錄並按一下。
4. 按一下 [設定] 索引標籤。
5. 向下捲動至 [使用者密碼重設原則] 區段，然後將 [使用者啟用密碼重設] 選項切換為 [是]。請注意，已核取 [備用電子郵件地址] 選項，請保留原狀。

    ![自助式密碼重設](./media/active-directory-b2c-reference-sspr/sspr.png)

6. 按一下頁面底部的 [儲存]。大功告成！

如果要進行測試，請針對所有將本機帳戶作為識別提供者的登入原則使用 [立即執行] 功能。在本機帳戶登入頁面上 (您輸入電子郵件地址和密碼，或使用者名稱和密碼的頁面)，按一下 [無法存取您的帳戶？] 驗證取用者體驗。

> [AZURE.NOTE]
您可以使用[公司商標功能](../active-directory/active-directory-add-company-branding.md)自訂自助式密碼重設頁面。

<!---HONumber=AcomDC_0323_2016-->