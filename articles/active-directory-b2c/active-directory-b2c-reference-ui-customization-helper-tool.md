<properties
	pageTitle="Azure Active Directory B2C 預覽︰頁面 UI 自訂協助程式工具 | Microsoft Azure"
	description="用來示範 Azure Active Directory B2C 中頁面 UI 自訂功能的協助程式工具"
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

# Azure Active Directory B2C 預覽︰用來示範頁面使用者介面 (UI) 自訂功能的協助程式工具

本文可搭配有關 Azure Active Directory (AD) B2C 中頁面使用者介面 (UI) 自訂功能的[主要文章](active-directory-b2c-reference-ui-customization)一起閱讀。

遵循下面所列的步驟，可讓您使用我們的範例內容練習網頁 UI 自訂功能。為了便於示範，我們的範例內容是**靜態** HTML5。

1. 將我們的範例內容上傳到 [Azure Blob 儲存體](http://azure.microsoft.com/documentation/services/storage/)，讓它可透過 HTTPS 公開存取並在這些 URL 上開啟 CORS (跨原始資源共用)。
2. 修改現有註冊原則中的頁面 UI 自訂設定，並指定上述 URL。

開始之前︰

- 建置[這裡](active-directory-b2c-overview.md)所列的其中一個 Azure AD B2C 快速入門應用程式。這麼做的時候，您將建立將在此修改的註冊原則。
- 從[這裡](https://github.com/AzureADSamples/B2C-AzureBlobStorage-Client)下載協助程式工具。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### 步驟 1：在正確的位置上傳我們的範例內容

1. 登入 [Azure Preview 入口網站](https://portal.azure.com/)。
2. 按一下 [+ 新增] -> [資料 + 儲存體] -> [儲存體帳戶]。您需要有 Azure 訂用帳戶才能建立 Azure Blob 儲存體帳戶。您可以[在此](https://azure.microsoft.com/zh-TW/pricing/free-trial/)註冊免費試用。
3. 提供儲存體帳戶的 [名稱] (例如 "contoso.core.windows.net") 並且為 [定價層]、[資源群組] 和 [訂用帳戶] 挑選適當的選取項目。確定您已核取 [釘選到「開始面板」] 選項。按一下 [建立]。
4. 回到「開始面板」，按一下您剛建立的儲存體帳戶。
5. 在 [摘要] 區段之下，按一下 [容器]，然後按一下 [+ 新增]。
6. 提供容器的 [名稱] (例如 "b2c")，並選取 [Blob] 作為 [存取類型]。按一下 [確定]。
7. 您建立的容器將會出現在 [Blob] 刀鋒視窗上的清單中。請記下容器的 URL；例如，看起來應該像 `https://contoso.blob.core.windows.net/b2c`。關閉 [Blob] 刀鋒視窗。
8. 在儲存體帳戶刀鋒視窗中，按一下 [金鑰] 並記下 [儲存體帳戶名稱] 和 [主要存取金鑰] 欄位的值。

    > [AZURE.NOTE][主要存取金鑰] 是重要的安全性認證。

9. 執行協助程式工具，並將在上一個步驟中複製的 [儲存體帳戶名稱] 和 [主要存取金鑰] 值提供給它。這會將我們的範例內容上傳到您的儲存體帳戶。
10. 試著在瀏覽器上存取 `https://contoso.blob.core.windows.net/b2c/idp.html`，確認該內容已正確地上傳。此外，使用 [http://test-cors.org/](http://test-cors.org/) 來確定該內容現在已啟用 CORS (在結果中尋找 `XHR status: 200`)。

### 步驟 2：修改註冊原則中的頁面 UI 自訂選項

1. 在 [Azure Preview 入口網站](https://portal.azure.com)上登入您的目錄並瀏覽至 B2C 功能刀鋒視窗。
2. 按一下 [註冊原則]，然後按一下您的註冊原則 (例如 "B2C\_1\_SiIn")。
3. 按一下 [頁面 UI 自訂]，然後按一下 [身分識別提供者選取頁面]。
4. 將 [使用自訂範本] 開關切換到 [是]。在 [自訂頁面 URI] 欄位中，輸入已上傳至儲存體帳戶之內容的適當 URL。例如，`https://contoso.blob.core.windows.net/b2c/idp.html`。按一下 [確定]。
5. 按一下 [本機帳戶註冊頁面]。將 [使用自訂範本] 開關切換到 [是]。在 [自訂頁面 URI] 欄位中，輸入已上傳至儲存體帳戶之內容的適當 URL。例如：`https://contoso.blob.core.windows.net/b2c/su.html`。按兩次 [確定]。
6. 按一下 [儲存]。
7. 按一下刀鋒視窗頂端的 [立即執行] 命令。選取 [應用程式] 下拉式清單中的 [B2C 應用程式] 以及 [回覆 URL / 重新導向 URI] 下拉式清單中的 `https://localhost:44321/`。按一下 [立即執行] 按鈕。
8. 新的瀏覽器索引標籤隨即開啟，您可以使用新的內容完整經歷註冊您的應用程式的使用者體驗！

> [AZURE.NOTE]請注意，您的原則變更最多需要一分鐘的時間才會生效。

<!---HONumber=Sept15_HO3-->