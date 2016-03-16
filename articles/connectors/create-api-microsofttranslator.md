<properties
	pageTitle="在 PowerApps Enterprise 或邏輯應用程式中新增 Microsoft Translator | Microsoft Azure"
	description="搭配 REST API 參數來使用 Microsoft Translator API 的概觀"
	services=""
    suite=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/25/2016"
   ms.author="mandia"/>

# 開始使用 Microsoft Translator API
連線到 Microsoft Translator 來翻譯文字、偵測語言種類等等。您可以從下列應用程式使用 Microsoft Translator API：

- PowerApps 
- 邏輯應用程式 

您可以利用 Microsoft Translator 來：

- 根據您從 Microsoft Translator 所取得的資料，來建置您的商務流程。 
- 使用動作來翻譯文字、偵測語言種類等等。這些動作會收到回應，然後輸出能讓其他動作使用的資料。舉例來說，當 Dropbox 中有新檔案建立時，您可以利用 Microsoft Translator 把該檔案中的文字翻譯成另一種語言。
- 將 Microsoft Translator API 新增到 PowerApps Enterprise，讓您的使用者能夠在自己的應用程式中使用這個 AP。 

如需有關如何在 PowerApps Enterprise 中新增 API 的資訊，請移至[在 PowerApps 中為 API 註冊](../power-apps/powerapps-register-from-available-apis.md)。

如果要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作
Microsoft Translator 包含下列動作，但不包含觸發程序。

觸發程序 | 動作
--- | ---
None | <ul><li>偵測語言種類</li><li>將文字轉換成語音</li><li>翻譯文字</li><li>取得語言種類</li><li>取得語音的語言種類</li></ul>

所有 API 都支援 JSON 和 XML 格式的資料。

## 建立至 Microsoft Translator 的連線

### 在 PowerApps 中新增其他組態
當您將 Microsoft Translator 新增到 PowerApps Enterprise 時，請輸入您 Microsoft Translator 應用程式的 [用戶端識別碼] 和 [用戶端密碼] 值。如果您沒有 Microsoft Translator 應用程式，可以建立一個：

1. 移至 [Azure Data Market 開發人員頁面][5]，並以您的 Microsoft 帳戶登入。 

2. 選取 [註冊您的應用程式]：

	1. 輸入 [用戶端識別碼] 的值。
	2. 輸入您應用程式的 [名稱]。
	3. 在 [重新導向 URL] 中輸入虛擬值。例如，輸入 *https://contosoredirecturl*。
	4. 輸入 [說明]。
	5. 選取 [**建立**]。  

	![註冊您的應用程式][6]

現在，在 Azure 入口網站的翻譯工具組態中，複製/貼上 [用戶端識別碼] 及 [應用程式密碼] 值。


## Swagger REST API 參考
適用的版本：1.0。

### 偵測語言種類    
偵測指定文字的原始語言種類。```GET: /Detect```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|query|字串|yes|query|無 |要確認其語言種類的文字|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 將文字轉換成語音    
將指定文字轉換成語音，成為 Wave 格式的音訊串流。```GET: /Speak```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|query|字串|yes|query|無 |要轉換的文字|
|語言|字串|yes|query|無 |用來產生語音的語言代碼 (例如：「zh-TW」)|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 翻譯文字    
利用 Microsoft Translator 來將文字翻譯成指定語言。```GET: /Translate```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|query|字串|yes|query|無 |要翻譯的文字|
|languageTo|字串|yes|query| 無|目標語言代碼 (例如：「fr」)|
|languageFrom|字串|no|query|無 |原始語言；如果未提供，Microsoft Translator 會嘗試自動偵測 (例如：en)。|
|category|字串|no|query|一般 |翻譯類別 (預設值：「general」)|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 取得語言種類    
擷取 Microsoft Translator 支援的所有語言種類。```GET: /TranslatableLanguages```

這個呼叫沒有參數。

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 取得語音的語言種類    
擷取提供語音合成功能的語言種類。```GET: /SpeakLanguages```

這個呼叫沒有參數。

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|

## 物件定義

#### Language：Microsoft Translator 可翻譯語言的語言模型

| 名稱 | 資料類型 | 必要|
|---|---|---|
|代碼|字串|no|
|名稱|字串|no|


## 後續步驟
當您把 Microsoft Translator API 新增到 PowerApps Enterprise 之後，請[授與使用者在自己的應用程式中使用 API 的權限](../power-apps/powerapps-manage-api-connection-user-access.md)。

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/create-api-microsofttranslator/register-your-application.png

<!---HONumber=AcomDC_0302_2016-------->

