<properties
	pageTitle="Azure Active Directory B2C 預覽︰使用者介面 (UI) 自訂 | Microsoft Azure"
	description="有關 Azure Active Directory B2C 中的使用者介面 (UI) 自訂功能的主題"
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
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C 預覽： 如何自訂 Azure AD B2C 使用者介面 (UI)

使用者經驗是取用者面向應用程式中最重要的。這是良好應用程式和絕佳應用程式之間，以及只是作用中的取用者與真正投入的取用者之間的差別。Azure Active Directory (AD) B2C 可讓您以精準的控制，自訂取用者註冊、登入 (*請參閱下面的注意事項*) 及設定檔編輯頁面。

> [AZURE.NOTE]目前僅可使用[公司品牌功能](./active-directory/active-directory-add-company-branding.md) (而不是經由本文中所述的機制)，自訂本機帳戶登入頁面、驗證電子郵件和自助式密碼重設頁面。

在本文中，您將了解：

- 頁面使用者介面 (UI) 自訂功能的概觀。
- 將協助您使用我們的範例內容測試頁面 UI 自訂功能的協助程式工具。
- 每種頁面中的核心 UI 元素。
- 執行這項功能時的最佳作法。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 頁面使用者介面 (UI) 自訂功能

頁面 UI 自訂功能可讓您自訂取用者註冊、登入及設定檔編輯頁面的外觀和操作方式 (藉由設定[原則](active-directory-b2c-references.policies.md))。取用者在您的應用程式與 Azure AD B2C 所提供的頁面間瀏覽時，將會有一致的體驗。

不同於其他只能透過 API 取得選項的服務，Azure AD B2C 使用新式 (且簡化的) 方法進行頁面 UI 自訂。以下是其運作方式：Azure AD B2C 會在取用者的瀏覽器中執行程式碼並使用稱為[跨原始資源共用 (CORS)](http://www.w3.org/TR/cors/) 的新式方法，從您在原則中指定的 URL 載入內容。您可以對不同的頁面指定不同的 URL。程式碼會合併 Azure AD B2C 的內容 (稱為 UI 元素) 與從您的 URL 載入的內容，並對取用者顯示此頁面。您只需要建立格式正確的 HTML5 內容，其中的 `<div id="api"></div>` 元素位於 `<body>` 中的某處 - 這就是 Azure AD B2C 的內容併入的位置。而且將此內容裝載於 HTTPS 端點 (允許 CORS)。您還可以完全設定 Azure AD B2C 的 UI 的元素樣式。

## 試試 UI 自訂功能

如果您想要嘗試使用裝載於 Azure Blob 儲存體的範例 HTML 和 CSS 內容來自訂 UX，我們提供[簡單的協助工具](active-directory-b2c-reference-ui-customization-helper-tool.md)以上傳與設定您的靜態內容。

## 每種頁面中的核心 UI 元素

您會在下列章節中找到 Azure AD B2C 合併至 <div id="api"></div> 元素 (位於您的內容中) 的 HTML5 片段範例 (適用於每種頁面)。您可以使用自己的樣式表來自訂這些 UI 元素。這些樣式表將覆寫我們在 <head> 片段中加入這些頁面的預設樣式表。

> [AZURE.IMPORTANT]在預覽期間，確切的 UI 元素應會隨著我們了解您的意見反應並據以調整而改變。請務必檢查預設頁面的原始程式碼有無最新更新。事實上，第一項考慮的變更就是移除我們的預設樣式表；這表示您務必要針對內容中的這些 UI 項目，提供自己的樣式表。

## 身分識別提供者選取頁面

此頁面包含使用者可以在註冊或登入期間選擇的身分識別提供者清單。這些是社交身分識別提供者 (如 Facebook 和 Google+) 或本機帳戶 (以電子郵件地址或使用者名稱為基礎)。

```HTML

<div id="api" data-name="IdpSelections">
	<div class="intro">
         <p>Sign up</p>
	</div>
	
	<div>
		<ul>
			<li>
				<button class="accountButton" id="FacebookExchange">Facebook</button>
			</li>
			<li>
				<button class="accountButton" id="GoogleExchange">Google+</button>
			</li>
			<li>
				<button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
			</li>
		</ul>
	</div>
</div>

```

## 本機帳戶註冊頁面

此頁面包含使用者在使用以電子郵件地址或使用者名稱為基礎的本機帳戶註冊時所需填寫的註冊表單。此表單可以包含不同的輸入控制項，例如文字輸入方塊、密碼輸入方塊、選項按鈕、單選下拉式清單和多選核取方塊。

```HTML

<div id="api" data-name="SelfAsserted"> 
	<div class="intro"> 
		<p>Create your account by providing the following details</p>
	</div>
	
	<div id="attributeVerification"> 
		<div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
		<div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
		<div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
		<div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
		<div class="attr" id="attributeList"> 
			<ul>
				<li> 
					<div class="attrEntry validate">
						<div> 
							<div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
							<div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
							<div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
							<div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
							<div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
							<div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
							<div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
						</div>

					<div class="helpText show">This information is required</div>
						<label>Email</label>
						<input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

					<div class="buttons verify" claim_id="email"> 
						<div id="email_ver_wait" class="working" style="display: none;"></div>
							<label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label> 
							<input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
							<button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
							<button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
							<button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
							<button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
							<button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button> 
						</div>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
						<label>Enter password</label>
						<input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ "; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"> This information is required</div>
						<label>Reenter password</label>
						<input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText">This information is required</div>
						<label>Name</label>
						<input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>Gender</label>
						<input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
						<label for="extension_Gender_F">Female</label>
						<input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
						<label for="extension_Gender_M">Male</label>
						<a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>Loyalty number</label>
						<input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>State</label>
						<select class="dropdown_single" id="state">
							<option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
							<option value="WA">Washington</option>
							<option value="NY">New York</option>
							<option value="CA">California</option>
						</select>
						<a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText">This information is required</div>
						<label>Zip code</label>
						<input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
					</div>
				</li>
			</ul> 
		</div>
		<div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
	</div>
	<div class="verifying-modal"> 
		<div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
		<div id="verifying_blurb"></div>
	</div>
</div>

```

## 社交帳戶註冊頁面

此頁面包含取用者在使用社交身分識別提供者 (例如 Facebook 或 Google+) 的現有帳戶註冊時所需填寫的註冊表單。此頁面類似於本機帳戶註冊頁面 (如前一節所示)，但密碼輸入欄位除外。

## Multi-Factor Authentication 頁面

此頁面可讓使用者在註冊或登入期間驗證其電話號碼 (使用文字或語音)。

```HTML

<div id="api" data-name="Phonefactor">
	<div id="phonefactor_initial">
		<div class="intro">
			<p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
		</div>
		<div class="errorText" id="errorMessage" style="display:none"></div>
		<div class="phoneEntry" id="phoneEntry">
			<div class="phoneNumber">
				<select id="countryCode" style="display:inline-block">
					<option value="+93">Afghanistan (+93)</option>
					<!-- Not all country codes listed -->
					<option value="+44">United Kingdom (+44)</option>
					<option value="+1" selected="">United States (+1)</option>
					<!-- Not all country codes listed -->
				</select>
			</div>
			<div class="phoneNumber">
				<input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
			</div>
		</div>
		<div id="codeVerification" style="display:none">
			<div>
				<p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
				<input type="text" id="verificationCode" placeholder="Verification code">
			</div>
		</div>
		<div class="buttons">
			<button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
			<button id="verifyPhone" style="display:inline-block">Call Me</button>
			<button id="cancel" style="display:inline-block">Cancel</button>
		</div>
	</div>
	<div class="dialing-modal">
		<div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
		<div id="dialing_blurb"></div><div id="dialing_number"></div>
	</div>
</div>

```

## 錯誤頁面


```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
	<h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
	<div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
	<div class="error-page-messagedetails">
		<div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
		<div>Timestamp:2015-09-14 23:22:35Z</div>
		<div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
	</div>
</div>

```

## 建置自己的內容時的注意事項

如果您打算使用頁面 UI 自訂功能，請檢閱以下最佳作法：

- 不要透過 Azure AD B2C 的預設範本複製並嘗試修改它。最好是從頭建置您的 HTML5 內容並使用預設範本作為參考。
- 基於安全性理由，我們不允許您在內容中包含任何 JavaScript。您所需的大多數功能應可立即使用。如果沒有，請使用 [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) 來要求新功能。
- 支援的瀏覽器版本︰
	- Internet Explorer 11
	- Internet Explorer 10
	- Internet Explorer 9 (有限)
	- Internet Explorer 8 (有限)
	- Google Chrome 43.0
	- Google Chrome 42.0
	- Mozilla Firefox 38.0
	- Mozilla Firefox 37.0

<!---HONumber=Oct15_HO1-->