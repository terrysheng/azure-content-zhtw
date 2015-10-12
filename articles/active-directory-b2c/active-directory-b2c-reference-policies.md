<properties
	pageTitle="Azure Active Directory B2C 預覽：可延伸的原則架構 | Microsoft Azure"
	description="關於 Azure Active Directory B2C 的可延伸原則架構及如何建立各種原則類型的主題"
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

# Azure Active Directory B2C 預覽：可延伸的原則架構

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 基本概念

Azure Active Directory (AD) B2C 的可延伸原則架構是服務的核心力量。原則可完整描述取用者身分識別體驗，例如註冊、登入或設定檔編輯。比方說，註冊原則可讓進行下列設定來控制行為：

- 可供取用者用來註冊應用程式的帳戶類型 (例如 Facebook 及 Google 等社交帳戶，及/或電子郵件地址/使用者名稱和密碼等本機帳戶)。
- 註冊時向取用者收集的屬性 (例如名字、郵遞區號、鞋子尺寸等)。 
- 使用多重要素驗證。
- 所有註冊頁面的外觀與風格。
- 原則執行完成，應用程式所接收的資訊 (以權杖中的宣告表示)。

您可以在租用戶中建立多個不同類型的原則，並視需要在您的應用程式中使用。原則可以跨應用程式重複使用。這可讓開發人員在少量變更或完全不變更程式碼的情況，定義及修改取用者身分識別體驗。我們將持續將更豐富的原則類型加入至我們的服務。

透過簡單的開發人員介面就可使用原則。您的應用程式使用標準 HTTP 驗證要求來觸發原則 (在要求中傳遞原則參數)，並從回應中收到自訂的權杖。例如，在叫用註冊原則和登入原則的要求之間，唯一的差別在於 "p" 查詢字串參數中使用的原則名稱：

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

## 如何建立註冊原則

若要在您的應用程式中啟用註冊功能，您必須建立註冊原則。此原則描述取用者在註冊期間將會經歷的體驗，以及成功註冊時，應用程式將收到的權杖內容。

1. [瀏覽至 Azure Preview 入口網站上的 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [註冊原則]。
3. 按一下刀鋒視窗頂端的 [+新增]。
4. [名稱] 決定您的應用程式所使用的註冊原則名稱。例如，輸入 "SiUp"。
5. 按一下 [身分識別提供者]，選取 [電子郵件地址]。(選擇性) 您也可以選取社交身分識別提供者 (如果已經設定)。按一下 [確定]。

    > [AZURE.NOTE]對於本機帳戶，Azure AD B2C 的註冊原則會使用「強式」密碼 (它們設定為「永久有效」)。如需其他設定 (目前 Azure AD B2C 未使用)，請參閱 [Azure AD 密碼原則](https://msdn.microsoft.com/library/azure/jj943764.aspx)。

6. 按一下 [註冊屬性]。您可以在這裡選擇註冊期間要向取用者收集的屬性。例如，選取 [國家/區域]、[顯示名稱] 和 [郵遞區號]。按一下 [確定]。
7. 按一下 [應用程式宣告]。您在這裡選擇成功註冊之後，您要在權杖中傳回給應用程式的宣告。例如，選取 [顯示名稱]、[身分識別提供者]、[郵遞區號]、[使用者是新的] 和 [使用者的物件識別碼]。
8. 按一下 [建立]。請注意，剛建立的原則會在 [註冊原則] 刀鋒視窗中顯示為 "**B2C\_1\_SiUp**" (開頭自動加上 **B2C\_1\_** 部分)。
9. 按一下 [B2C\_1\_SiUp] 以開啟原則。
10. 在 [應用程式] 下拉式清單中選取 [Contoso B2C 應用程式]，在 [回覆 URL / 重新導向 URI] 下拉式清單中選取 `https://localhost:44321/`。按一下 [立即執行] 按鈕。新的瀏覽器索引標籤隨即開啟，您可以開始進行取用者體驗來註冊您的應用程式。

    > [AZURE.NOTE]建立和更新原則後，需要經過一分鐘才會生效。

## 如何建立登入原則

若要在您的應用程式中啟用登入功能，您必須建立登入原則。此原則描述取用者在登入期間將會經歷的體驗，以及成功登入時，應用程式將收到的權杖內容。

1. [瀏覽至 Azure Preview 入口網站上的 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [登入原則]。
3. 按一下刀鋒視窗頂端的 [+新增]。
4. [名稱] 決定您的應用程式所使用的登入原則名稱。例如，輸入 "SiIn"。
5. 按一下 [身分識別提供者]，選取 [電子郵件地址]。(選擇性) 您也可以選取社交身分識別提供者 (如果已經設定)。按一下 [確定]。
6. 按一下 [應用程式宣告]。您在這裡選擇成功登入後，您要在權杖中傳回給應用程式的宣告。例如，選取 [顯示名稱]、[身分識別提供者]、[郵遞區號] 和 [使用者的物件識別碼]。按一下 [確定]。
7. 按一下 [建立]。請注意，剛建立的原則會在 [登入原則] 刀鋒視窗中顯示為 "**B2C\_1\_SiIn**" (自動加上 **B2C\_1\_** 部分)。
8. 按一下 [B2C\_1\_SiIn] 以開啟原則。
9. 在 [應用程式] 下拉式清單中選取 [Contoso B2C 應用程式]，在 [回覆 URL / 重新導向 URI] 下拉式清單中選取 `https://localhost:44321/`。按一下 [立即執行] 按鈕。新的瀏覽器索引標籤隨即開啟，您可以開始進行取用者體驗來登入您的應用程式。

    > [AZURE.NOTE]建立和更新原則後，需要經過一分鐘才會生效。

## 如何建立設定檔編輯原則

若要在您的應用程式中啟用設定檔編輯功能，您必須建立設定檔編輯原則。此原則描述取用者在設定檔編輯期間將會經歷的體驗，以及成功完成時，應用程式將收到的權杖內容。

1. [瀏覽至 Azure Preview 入口網站上的 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [設定檔編輯原則]。
3. 按一下刀鋒視窗頂端的 [+新增]。
4. [名稱] 決定您的應用程式所使用的設定檔編輯原則名稱。例如，輸入 "SiPe"。
5. 按一下 [身分識別提供者]，選取 [電子郵件地址]。(選擇性) 您也可以選取社交身分識別提供者 (如果已經設定)。按一下 [確定]。
6. 按一下 [設定檔屬性]。您在這裡選擇取用者可以檢視及編輯的屬性。例如，選取 [國家/區域]、[顯示名稱] 和 [郵遞區號]。按一下 [確定]。
7. 按一下 [應用程式宣告]。您在這裡選擇成功編輯設定檔後，您要在權杖中傳回給應用程式的宣告。例如，選取 [顯示名稱] 和 [郵遞區號]。
8. 按一下 [建立]。請注意，剛建立的原則會在 [設定檔編輯原則] 刀鋒視窗中顯示為 "**B2C\_1\_SiPe**" (開頭自動加上 **B2C\_1\_** 部分)。
9. 按一下 [B2C\_1\_SiPe] 以開啟原則。
10. 在 [應用程式] 下拉式清單中選取 [Contoso B2C 應用程式]，在 [回覆 URL / 重新導向 URI] 下拉式清單中選取 `https://localhost:44321/`。按一下 [立即執行] 按鈕。新的瀏覽器索引標籤隨即開啟，您可以開始在應用程式中進行設定檔編輯取用者體驗。

    > [AZURE.NOTE]建立和更新原則後，需要經過一分鐘才會生效。

<!---HONumber=Oct15_HO1-->