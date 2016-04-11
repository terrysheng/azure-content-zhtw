<properties
	pageTitle="升級到第 2 版文字分析 API | Microsoft Azure"
	description="Azure Machine Learning 文字分析 - 升級到第 2 版"
	services="cognitive-services"
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
	ms.date="03/24/2016"
	ms.author="onewth"/>

# 升級到第 2 版文字分析 API #

本指南將引導您從使用[第一版的 API](../machine-learning-apps-text-analytics/) 改為使用第二版，來更新您的程式碼。

如果您未曾使用過 API，並且想要進一步了解，您可以**[在這裡深入了解 API](//go.microsoft.com/fwlink/?LinkID=759711)**，或**[遵循快速啟動指南](//go.microsoft.com/fwlink/?LinkID=760860)**。如需技術參考，請參閱 **[API 定義](//go.microsoft.com/fwlink/?LinkID=759346)**。

### 第 1 部分。取得新的金鑰 ###

首先，您必須從 **Azure 入口網站**取得新的 API 金鑰：

1. 透過 [Cortana Analytics 資源庫](//gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2)瀏覽至文字分析服務。您也會在這裡找到文件和程式碼範例的連結。

1. 按一下 [登入]。此連結會帶您前往 Azure 管理入口網站，您可以在其中註冊服務。

1. 選取方案。您可以選取 **5000 次交易/月的免費層**。因為這是免費方案，您不需要為使用服務支付費用。您必須登入您的 Azure 訂用帳戶。

1. 註冊文字分析後，就會提供 **API 金鑰**給您。複製這個金鑰，您在使用 API 服務時需要它。

### 第 2 部分。更新標頭 ###

更新已提交的標頭值，如下所示。請注意，已不再編碼帳戶金鑰。

**第 1 版**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**第 2 版**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### 第 3 部分。更新基底 URL ###

**第 1 版**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**第 2 版**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### 第 4a 部分。更新的情感、主要片語和語言的格式 ###

#### Endpoints ####

GET 端點現在已被取代，所以所有輸入應該提交為 POST 要求。將端點更新為如下所示的端點。

| |第 1 版單一端點|第 1 版批次端點|第 2 版端點|
|---|---|---|---|
|呼叫類型|GET|POST|POST|
|情感|```GetSentiment```|```GetSentimentBatch```|```sentiment```|
|主要片語|```GetKeyPhrases```|```GetKeyPhrasesBatch```|```keyPhrases```|
|語言|```GetLanguage```|```GetLanguageBatch```|```languages```|

#### 輸入格式 ####

請注意，現在僅接受 POST 格式，因此您應該重新格式化先前使用單一文件端點的任何輸入。輸入不區分大小寫。

**第 1 版 (批次)**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**第 2 版**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### 情感的輸出 ####

**第 1 版**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**第 2 版**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### 主要片語的輸出 ####

**第 1 版**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**第 2 版**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### 語言的輸出 ####


**第 1 版**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**第 2 版**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### 第 4b 部分。更新主題的格式 ###

#### Endpoints ####

| |第 1 版端點 | 第 2 版端點|
|---|---|---|
|提交主題偵測 (POST)|```StartTopicDetection```|```topics```|
|擷取主題結果 (GET)|```GetTopicDetectionResult?JobId=<jobId>```|```operations/<operationId>```|

#### 輸入格式 ####

**第 1 版**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**第 2 版**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### 提交結果 ####

**第 1 版 (POST)**

先前，當作業完成時，您會收到下列 JSON 輸出，其中 jobId 會附加至 URL，以擷取輸出。

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**第 2 版 (POST)**

回應現在包含標頭值，如下所示，其中 `operation-location` 做為端點以輪詢結果︰

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### Operation results ####

**第 1 版 (GET)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**第 2 版 (GET)**

同樣地，**定期輪詢輸出** (建議的週期是每分鐘)，直到傳回輸出。

主題 API 完成時，會傳回 `succeeded` 狀態。然後以如下所示的格式包含輸出結果︰

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### 第 5 部分。測試它！ ###

您現在應已準備就緒！ 使用小型範例測試您的程式碼，以確保您可以成功處理資料。

<!---HONumber=AcomDC_0330_2016-->