<properties
	pageTitle="Azure Active Directory B2C 預覽：應用程式註冊 | Microsoft Azure"
	description="如何向 Azure Active Directory B2C 註冊您的應用程式"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="swkrish"/>


# Azure Active Directory B2C 預覽：註冊您的應用程式

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 必要條件

若要建置可接受取用者註冊與登入的應用程式，您必須先使用 Azure Active Directory B2C 租用戶註冊該應用程式。使用[建立 Azure AD B2C 租用戶](active-directory-b2c-get-started.md)中概述的步驟來建立您自己的租用戶。在您遵循該文章中的所有步驟之後，B2C 功能刀鋒視窗應會釘選至您的「開始面板」。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 瀏覽至 B2C 功能刀鋒視窗

您可以從 Azure 入口網站或 Azure 傳統入口網站瀏覽至 B2C 功能刀鋒視窗。

### 1\.在 Azure 入口網站上進行存取

若您已將 B2C 功能刀鋒視窗釘選至「開始面板」，則在您以 B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)時，即會看見該刀鋒視窗。

您也可以在 [Azure 入口網站](https://portal.azure.com/)的左側功能窗格中依序按一下 [瀏覽] 和 [Azure AD B2C]，來存取刀鋒視窗。

您也可以瀏覽至 [https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/](https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/) (其中 **{tenant}** 是使用建立租用戶時所用的名稱來取代，例如 contosob2c)，直接存取刀鋒視窗。您可將此連結加入書籤供日後使用。

    > [AZURE.IMPORTANT]
    您必須是 B2C 租用戶的全域管理員，才能存取 B2C 功能刀鋒視窗。其他租用戶的全域管理員或所有租用戶的使用者均無法存取。

### 2\.在 Azure 傳統入口網站上進行存取

以訂用帳戶管理員身分登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。(此為您註冊 Azure 時所用的相同工作或學校帳戶，或是相同的 Microsoft 帳戶)。 瀏覽至左側的 Active Directory 擴充，然後按一下 B2C 租用戶。在 [快速啟動] 索引標籤 (第一個開啟的索引標籤) 上，按一下 [管理] 下方的 [管理 B2C 設定]。此動作將會在新的瀏覽器視窗或索引標籤中，開啟 B2C 功能刀鋒視窗。

您也可以在 [設定] 索引標籤的 [B2C 管理] 區段中找到 [管理 B2C 設定] 連結。

## 註冊應用程式

1. 在 Azure 入口網站的 B2C 功能刀鋒視窗中，按一下 [應用程式]。
2. 按一下刀鋒視窗頂端的 [+新增]。
3. 輸入應用程式的**名稱**，此名稱將會為取用者說明您的應用程式。例如，您可以輸入「Contoso B2C app」。
4. 如果您正在撰寫 Web 應用程式，請將 [包含 Web 應用程式/Web API] 切換為 [是]。**回覆 URL** 是 Azure AD B2C 傳回您應用程式要求之任何權杖的所在端點。例如，輸入 `https://localhost:44321/`。若您的應用程式包含需要保護的伺服器端元件 (API)，則您亦可按一下 [產生金鑰] 按鈕，以建立 (和複製) **應用程式密碼**。

    > [AZURE.NOTE]
    **應用程式密碼**是重要的安全性認證。

5. 如果您正在撰寫行動應用程式，請將 [包含原生用戶端] 切換為 [是]。複製系統自動為您建立的預設**重新導向 URI**。
6. 按一下 [建立] 以註冊您的應用程式。
7. 按一下您剛才建立的應用程式，並複製稍後要在程式碼中使用的全域唯一**應用程式識別碼**。

## 建置快速啟動應用程式

您現已在 Azure AD B2C 註冊應用程式，只需完成其中一個快速啟動教學課程，即可開始操作和執行。以下是一些建議：

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=AcomDC_0224_2016-->