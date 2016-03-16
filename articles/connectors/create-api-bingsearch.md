<properties
	pageTitle="在 PowerApps 或邏輯應用程式中新增 Bing 搜尋 API | Microsoft Azure"
	description="搭配 REST API 參數來使用 Bing 搜尋 API 的概觀"
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
   ms.date="03/02/2016"
   ms.author="mandia"/>

# 開始使用 Bing 搜尋 API 
連線到 Bing 搜尋來搜尋新聞、搜尋影片等等。您可以從下列應用程式使用 Bing 搜尋 API：

- 邏輯應用程式 
- PowerApps

> [AZURE.SELECTOR]
- [邏輯應用程式](../articles/connectors/create-api-bingsearch.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-bingsearch.md)


您可以利用 Bing 搜尋來：

- 根據您透過搜尋所取得的資料，來建置您的商務流程。 
- 使用動作來搜尋圖像、搜尋新聞等等。這些動作會收到回應，然後輸出能讓其他動作使用的資料。舉例來說，您可以搜尋某支影片，然後利用 Twitter 把該影片張貼在某個 Twitter 摘要上。
- 將 Bing 搜尋 API 新增至 PowerApps Enterprise，讓您的使用者能夠在自己的應用程式中使用這個 AP。 

如需有關如何在 PowerApps Enterprise 中新增 API 的資訊，請移至[在 PowerApps 中為 API 註冊](../power-apps/powerapps-register-from-available-apis.md)。

如果要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作
Bing 搜尋包含下列動作，但不包含觸發程序。

觸發程序 | 動作
--- | ---
None | <ul><li>搜尋網站</li><li>搜尋影片</li><li>搜尋圖像</li><li>搜尋新聞</li><li>搜尋相關結果</li><li>搜尋拼字</li><li>搜尋所有結果</li></ul>

所有 API 都支援 JSON 和 XML 格式的資料。

## 新增其他組態
當您把 Bing 搜尋新增到 PowerApps Enterprise 時，系統會提示您輸入帳戶金鑰。如果您沒有 Bing 搜尋金鑰，請使用免費的 [Bing 搜尋優惠](https://datamarket.azure.com/dataset/bing/search)來取得金鑰。


## Swagger REST API 參考
適用的版本：1.0。

### 搜尋網站 
擷取某次 Bing 搜尋結果中的網站。```GET: /Web```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|query|字串|yes|query|無 |要搜尋的文字 (例如「xbox」)|
|maxResult|integer|no|query|無 |要傳回的結果數目上限|
|startOffset|integer|no|query| 無|要略過的結果數目|
|adultContent|字串|no|query|無 |成人內容篩選器。有效值：<ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|字串|no|query|無 |用來縮小搜尋範圍的市場或區域 (例如：zh-TW)|
|經度|number|no|query| 無|用來縮小搜尋範圍的經度 (東/西向座標) (例如：47.603450)|
|緯度|number|no|query| 無|用來縮小搜尋範圍的緯度 (南/北向座標) (例如：-122.329696)|
|webFileType|字串|no|query|無 |用來縮小搜尋範圍的檔案類型 (例如：「DOC」)|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 搜尋影片 
擷取某次 Bing 搜尋結果中的影片。```GET: /Video```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|query|字串|yes|query|無 |要搜尋的文字 (例如「xbox」)|
|maxResult|integer|no|query| 無|要傳回的結果數目上限|
|startOffset|integer|no|query|無 |要略過的結果數目|
|adultContent|字串|no|query|無 |成人內容篩選器。有效值：<ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|字串|no|query|無 |用來縮小搜尋範圍的市場或區域 (例如：zh-TW)|
|經度|number|no|query|無 |用來縮小搜尋範圍的經度 (東/西向座標) (例如：47.603450)|
|緯度|number|no|query|無 |用來縮小搜尋範圍的緯度 (南/北向座標) (例如：-122.329696)|
|videoFilters|字串|no|query|無 |根據影片的大小、外觀比例、色彩、樣式、方向，或上述條件的任何組合來篩選搜尋。有效值：<ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>例如：「Duration:Short+Resolution:High」|
|videoSortBy|字串|no|query|無 |搜尋結果的排序次序。有效值：<ul><li>Date</li><li>Relevance</li></ul> <p>日期的排序次序必須為遞減。</p>|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 搜尋圖像    
擷取某次 Bing 搜尋結果中的圖像。```GET: /Image```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|query|字串|yes|query|無 |要搜尋的文字 (例如「xbox」)|
|maxResult|integer|no|query|無 |要傳回的結果數目上限|
|startOffset|integer|no|query|無 |要略過的結果數目|
|adultContent|字串|no|query|無 |成人內容篩選器。有效值：<ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|字串|no|query|無 |用來縮小搜尋範圍的市場或區域 (例如：zh-TW)|
|經度|number|no|query| 無|用來縮小搜尋範圍的經度 (東/西向座標) (例如：47.603450)|
|緯度|number|no|query|無 |用來縮小搜尋範圍的緯度 (南/北向座標) (例如：-122.329696)|
|imageFilters|字串|no|query|無 |根據影片的大小、外觀比例、色彩、樣式、方向，或上述條件的任何組合來篩選搜尋。有效值：<ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[寬度]</li><li>Size:Height:[高度]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>例如：「Size:Small+Aspect:Square」|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 搜尋新聞    
擷取某次 Bing 搜尋結果中的新聞。```GET: /News```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|query|字串|yes|query|無 |要搜尋的文字 (例如「xbox」)|
|maxResult|integer|no|query|無 |要傳回的結果數目上限|
|startOffset|integer|no|query| 無|要略過的結果數目|
|adultContent|字串|no|query|無 |成人內容篩選器。有效值：<ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|字串|no|query|無 |用來縮小搜尋範圍的市場或區域 (例如：zh-TW)|
|經度|number|no|query|無 |用來縮小搜尋範圍的經度 (東/西向座標) (例如：47.603450)|
|緯度|number|no|query|無 |用來縮小搜尋範圍的緯度 (南/北向座標) (例如：-122.329696)|
|newsSortBy|字串|no|query| 無|搜尋結果的排序次序。有效值：<ul><li>Date</li><li>Relevance</li></ul> <p>日期的排序次序必須為遞減。</p>|
|newsCategory|字串|no|query| |用來縮小搜尋範圍的新聞類別 (例如：「rt\_Business」)|
|newsLocationOverride|字串|no|query|無 |覆寫 Bing 位置偵測的結果。此參數僅適用於 zh-TW 市場。輸入的格式為 US./<state /> (例如：「US.WA」)|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 搜尋拼字    
擷取拼字建議。```GET: /SpellingSuggestions```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|query|字串|yes|query| 無|要搜尋的文字 (例如「xbox」)|
|maxResult|integer|no|query|無 |要傳回的結果數目上限|
|startOffset|integer|no|query| 無|要略過的結果數目|
|adultContent|字串|no|query|無 |成人內容篩選器。有效值：<ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|字串|no|query| 無|用來縮小搜尋範圍的市場或區域 (例如：zh-TW)|
|經度|number|no|query|無 |用來縮小搜尋範圍的經度 (東/西向座標) (例如：47.603450)|
|緯度|number|no|query|無 |用來縮小搜尋範圍的緯度 (南/北向座標) (例如：-122.329696)|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 搜尋相關結果    
擷取某次 Bing 搜尋結果中的相關搜尋結果。```GET: /RelatedSearch```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|query|字串|yes|query|無 |要搜尋的文字 (例如「xbox」)|
|maxResult|integer|no|query|無 |要傳回的結果數目上限|
|startOffset|integer|no|query| 無|要略過的結果數目|
|adultContent|字串|no|query|無 |成人內容篩選器。有效值：<ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|字串|no|query|無 |用來縮小搜尋範圍的市場或區域 (例如：zh-TW)|
|經度|number|no|query|無 |用來縮小搜尋範圍的經度 (東/西向座標) (例如：47.603450)|
|緯度|number|no|query| 無|用來縮小搜尋範圍的緯度 (南/北向座標) (例如：-122.329696)|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 搜尋所有項目    
擷取某次 Bing 搜尋結果中的所有網站、視訊、圖像等等。```GET: /CompositeSearch```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|query|字串|yes|query|無 |要搜尋的文字 (例如「xbox」)|
|maxResult|integer|no|query|無 |要傳回的結果數目上限|
|startOffset|integer|no|query|無 |要略過的結果數目|
|adultContent|字串|no|query|無 |成人內容篩選器。有效值：<ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|字串|no|query|無 |用來縮小搜尋範圍的市場或區域 (例如：zh-TW)|
|經度|number|no|query|無 |用來縮小搜尋範圍的經度 (東/西向座標) (例如：47.603450)|
|緯度|number|no|query|無 |用來縮小搜尋範圍的緯度 (南/北向座標) (例如：-122.329696)|
|webFileType|字串|no|query|無 |用來縮小搜尋範圍的檔案類型 (例如：「DOC」)|
|videoFilters|字串|no|query|無 |根據影片的大小、外觀比例、色彩、樣式、方向，或上述條件的任何組合來篩選搜尋。有效值：<ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>例如：「Duration:Short+Resolution:High」|
|videoSortBy|字串|no|query|無 |搜尋結果的排序次序。有效值：<ul><li>Date</li><li>Relevance</li></ul> <p>日期的排序次序必須為遞減。</p>|
|imageFilters|字串|no|query|無 |根據影片的大小、外觀比例、色彩、樣式、方向，或上述條件的任何組合來篩選搜尋。有效值：<ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[寬度]</li><li>Size:Height:[高度]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>例如：「Size:Small+Aspect:Square」|
|newsSortBy|字串|no|query|無 |搜尋結果的排序次序。有效值：<ul><li>Date</li><li>Relevance</li></ul> <p>日期的排序次序必須為遞減。</p>|
|newsCategory|字串|no|query|無 |用來縮小搜尋範圍的新聞類別 (例如：「rt\_Business」)|
|newsLocationOverride|字串|no|query|無 |覆寫 Bing 位置偵測的結果。此參數僅適用於 zh-TW 市場。輸入的格式為 US./<state /> (例如：「US.WA」)|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## 物件定義

#### WebResultModel：Bing 的網頁搜尋結果

|屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|課程名稱|字串|no|
|說明|字串|no|
|DisplayUrl|字串|no|
|識別碼|字串|no|
|FullUrl|字串|no|

#### VideoResultModel：Bing 的影片搜尋結果

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|課程名稱|字串|no|
|DisplayUrl|字串|no|
|識別碼|字串|no|
|MediaUrl|字串|no|
|執行階段|integer|no|
|縮圖|沒有定義|no|

#### ThumbnailModel：多媒體元素的縮圖屬性

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|MediaUrl|字串|no|
|ContentType|字串|no|
|寬度|integer|no|
|高度|integer|no|
|FileSize|integer|no|

#### ImageResultModel：Bing 的圖像搜尋結果

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|課程名稱|字串|no|
|DisplayUrl|字串|no|
|識別碼|字串|no|
|MediaUrl|字串|no|
|SourceUrl|字串|no|
|縮圖|沒有定義|no|

#### NewsResultModel：Bing 的新聞搜尋結果

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|課程名稱|字串|no|
|說明|字串|no|
|DisplayUrl|字串|no|
|識別碼|字串|no|
|來源|字串|no|
|Date|字串|no|

#### SpellResultModel：Bing 的拼字建議結果

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|識別碼|字串|no|
|值|字串|no|

#### RelatedSearchResultModel：Bing 的相關搜尋結果

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|課程名稱|字串|no|
|識別碼|字串|no|
|BingUrl|字串|no|

#### CompositeSearchResultModel：Bing 的綜合搜尋結果

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|WebResultsTotal|integer|no|
|ImageResultsTotal|integer|no|
|VideoResultsTotal|integer|no|
|NewsResultsTotal|integer|no|
|SpellSuggestionsTotal|integer|no|
|WebResults|array|no|
|ImageResults|array|no|
|VideoResults|array|no|
|NewsResults|array|no|
|SpellSuggestionResults|array|no|
|RelatedSearchResults|array|no|

## 後續步驟

當您把 Bing 搜尋 API 新增到 PowerApps Enterprise 之後，請[授與使用者在自己的應用程式中使用 API 的權限](../power-apps/powerapps-manage-api-connection-user-access.md)。

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

<!---HONumber=AcomDC_0302_2016-------->