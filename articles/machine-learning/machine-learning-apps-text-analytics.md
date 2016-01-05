<properties
	pageTitle="機器學習 API：文字分析 | Microsoft Azure"
	description="Azure Machine Learning 提供的文字分析 API。此 API 可用來分析情緒分析、關鍵片語擷取及語言偵測的非結構化文字。"
	services="machine-learning"
	documentationCenter=""
	authors="onewth"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="onewth"/>


# 機器學習 API：情感文字分析、關鍵片語擷取及語言偵測

## 概觀

文字分析 API 是一套以 Azure Machine Learning 服務建置的文字分析 [Web 服務](https://datamarket.azure.com/dataset/amla/text-analytics)。此 API 可用來分析工作的非結構化文字，例如情感分析、關鍵片語擷取及語言偵測。使用此 API 不需要任何訓練資料，只要將文字資料帶入即可。此 API 使用進階的自然語言處理技術來提供最佳預測。

您可以在我們的[示範網站](https://text-analytics-demo.azurewebsites.net/)看到文字分析的運作，其中您也可以找到如何以 C# 和 Python 實作文字分析的[範例](https://text-analytics-demo.azurewebsites.net/Home/SampleCode)。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

---

## 情感分析

API 會傳回一個 0 到 1 之間的分數。接近 1 的分數表示正面的情感，而接近 0 的分數則表示負面的情感。情感分數使用分類技術產生。輸入分類器的特徵包括 n-grams、從 part-of-speech 標記產生的特徵以及字詞內嵌。目前，英文是唯一支援的語言。
 
## 關鍵片語擷取

API 會傳回輸入文字中代表說話重點的字串清單。我們採用的技術來自 Microsoft Office 複雜的自然語言處理工具組。目前，英文是唯一支援的語言。

## 語言偵測

此 API 會傳回偵測到的語言和 0 到 1 之間的分數。接近 1 的分數表示 100% 確定已識別的語言為真實。總共支援 120 種語言。

---

## API 定義

### 標頭

請確定要求中包含正確的標頭，應該如下：

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

您可以在 [Azure 資料市場](https://datamarket.azure.com/account/keys)中找到您帳戶中的帳戶金鑰。

---

## 單一回應 API

### GetSentiment

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**範例要求**

在下面的呼叫中，我們要求片語 "Hello World" 的情緒分析：

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

這會傳回如下的回應：

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

### GetKeyPhrases

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**範例要求**

在下面的呼叫中，我們要求 "這是很棒的旅館，裝潢獨樹一幟，工作人員服務態度甚佳" 這段文字中找到的關鍵片語：

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

這會傳回如下的回應：

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	  "KeyPhrases":[
	    "wonderful hotel",
	    "unique decor",
	    "friendly staff"
	  ]
	}
 
---

### GetLanguage

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**範例要求**

在下面的 GET 呼叫中，我們要求 *Hello World* 這段文字中關鍵片語的情緒

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
	Text=Hello+World

這會傳回如下的回應：

	{
	  "UnknownLanguage": false,
	  "DetectedLanguages": [{
	    "Name": "English",
	    "Iso6391Name": "en",
	    "Score": 1.0
	  }]
	}

**選擇性參數**

`NumberOfLanguagesToDetect` 是選擇性參數。預設值為 1。

---

## Batch API

文字分析服務可讓您以 Batch 模式執行情感和關鍵片語的擷取。請注意，每一筆評分記錄都是一個交易。例如，如果您在單一呼叫中要求 1000 筆記錄的情緒，則會扣除 1000 個交易。

請注意，在系統中輸入的識別碼是由系統傳回的識別碼。Web 服務不會檢查這些是否為唯一的識別碼。呼叫端必須負責驗證唯一性。


### GetSentimentBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**範例要求**

在下面的 POST 呼叫中，我們在要求本文中要求 "Hello World"、"Hello Foo World" 和 "Hello My World" 這些片語的情緒：

	POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

要求本文：

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
    	    {"Id":"2","Text":"hello foo world"},
    	    {"Id":"3","Text":"hello my world"},
	]}

在以下回應中，您會取得與文字識別碼相關聯的分數的清單：

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", 
	  "SentimentBatch":
	  [
		{"Score":0.9549767,"Id":"1"},
		{"Score":0.7767222,"Id":"2"},
		{"Score":0.8988889,"Id":"3"}
	  ],  
	  "Errors":[]
	}


---

### GetKeyPhrasesBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**範例要求**

在此範例，我們要求下列文字中關鍵片語的情緒清單：

* "It was a wonderful hotel to stay at, with unique decor and friendly staff"
* "It was an amazing build conference, with very interesting talks"
* "The traffic was terrible, I spent three hours going to the airport"

此要求是對端點的 POST 呼叫：

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

要求本文：

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

在以下回應中，您會取得與文字識別碼相關聯的片語的清單：

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[]
	}

---

### GetLanguageBatch

在下面的 POST 呼叫中，我們要求兩個文字輸入的語言偵測：

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

要求本文：

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

這會傳回下列回應，在第一個輸入中偵測到英文，在第二個輸入中偵測到法文：

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "English",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "French",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

<!----HONumber=AcomDC_1125_2015-->