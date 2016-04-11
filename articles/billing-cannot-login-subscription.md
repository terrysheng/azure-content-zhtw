<properties
	pageTitle="我無法登入來管理我的 Azure 訂用帳戶 | Microsoft Azure"
	description="說明疑難排解一些常見 Azure 訂用帳戶登入問題的資訊"
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="jarrettr"
	editor="na"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="genli"/>

# 我無法登入來管理我的 Azure 訂用帳戶

本文將協助您疑難排解登入問題的常見原因。

## 您想進入哪個入口網站？

帳戶管理員只能存取[帳戶中心](https://account.windowsazure.com/)，而服務管理員 (SA) 和共同管理員 (CA) 只有 [Azure 入口網站](https://portal.azure.com)或 [Azure 傳統入口網站](https://manage.windowsazure.com/)的存取權。

如需 Azure 管理員的詳細資訊，請參閱[如何新增或變更 Azure 管理員角色](billing-add-change-azure-subscription-administrator.md)。

## 您的訂用帳戶是否與 Microsoft 帳戶或是組織帳戶相關聯？

您的 Microsoft 帳戶是您搭配密碼用來登入所有 Windows Live 程式或服務 (例如 Outlook、Hotmail、MSN 或 OneDrive) 的電子郵件地址。您可以使用您名下的電子郵件地址建立 Microsoft 帳戶，包括公司的電子郵件地址都適用。更多詳細資料請參閱 [www.microsoft.com/account](http://www.microsoft.com/account)。

如果您的帳戶與組織帳戶相關聯，請選取正確的登入選項，如下所示。如需有關使用組織帳戶的詳細資訊，請參閱[以組織身分註冊 Azure](./active-directory/sign-up-organization.md)。

![登入頁面](./media/billing-cannot-login-subscription/signin.png)

## 共同管理員：您是否使用正確的帳戶類型管理其他帳戶？

- 如果您是以 Microsoft 帳戶登入，就只能將其他 Microsoft 帳戶新增為共同管理員。這是基於安全考量，以防止非組織帳戶無意中發現某些有效帳戶 (例如 janedoe@contoso.com)。
- 如果您是以組織帳戶登入，就可以在組織中將其他組織帳戶新增為共同管理員。例如，abby@contoso.com 可以將 bob@contoso.com 新增為服務管理員或共同管理員，但無法新增 john@notcontoso.com。使用組織帳戶登入的使用者，也可以將 Microsoft 帳戶使用者新增為服務管理員或共同管理員。

現在可以使用組織帳戶登入 Azure，以下是變更服務管理員 (SA) 和共同管理員 (CA) 帳戶的需求：

| 登入方法| 是否能將 Microsoft 帳戶新增為共同管理員或服務管理員？ |是否能在同一組織中將組織帳戶新增為共同管理員或服務管理員？ |是否能在不同組織中將組織帳戶新增為共同管理員或服務管理員？
| ------------- | ------------- |---------------|---------------|
|Microsoft 帳戶 |是|否|否|
|組織帳戶|是|是|否|

## 使用 IE InPrivate 瀏覽模式，並也使用其他瀏覽器嘗試刪除快取/Cookies

如果您對本文內容有需要協助之處，可以連絡 [MSDN Azure 和堆疊溢位論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。或者，您也可以提出 Azure 支援事件。請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，按一下 [取得支援]。如需有關使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

<!---HONumber=AcomDC_0330_2016-->