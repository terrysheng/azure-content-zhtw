<properties
	pageTitle="快速啟動指南：Machine Learning 文字分析 API | Microsoft Azure"
	description="Azure Machine Learning 文字分析 - 快速啟動指南"
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

# 開始使用文字分析 API 來偵測情緒、主要片語、主題和語言

<a name="HOLTop"></a>

本文件說明如何裝載您的服務或應用程式，以使用[文字分析 API](//go.microsoft.com/fwlink/?LinkID=759711)。您可以使用這些 API 以偵測您的文字中的情感、主要片語、主題和語言。[按一下這裡以查看體驗的互動式示範。](//go.microsoft.com/fwlink/?LinkID=759712)

請參閱 [API 定義](//go.microsoft.com/fwlink/?LinkID=759346)，以取得 API 的技術文件。

本指南適用於第 2 版的 API。如需第 1 版 API 的詳細資訊，[請參閱這份文件](machine-learning-apps-text-analytics/)。

本教學課程結束時，您將能夠以程式設計方式偵測︰

- **情感** - 文字為正面或負面？

- **主要片語** - 人們在單一文章中所討論的是什麼？

- **主題** - 人們在多篇文章中所討論的是什麼？

- **語言** - 文字以何種語言寫下？

請注意，每提交 1 筆文字記錄，此 API 就會以 1 筆交易計費。

<a name="Overview"></a>
## 一般概觀 ##

本文件是一份逐步解說指南。我們的目標是引導您進行訓練模型所需的步驟，並且為您指點可讓您投入生產的資源。本練習需要大約 30 分鐘。

對於這些工作，您需要編輯器，並且使用您選擇的語言呼叫 RESTful 端點。

現在就開始吧！

## 工作 1 - 註冊文字分析 API ####

在這個工作中，您將會註冊文字分析服務。

1. 瀏覽至 [Azure 入口網站](//go.microsoft.com/fwlink/?LinkId=761108) 中的 [Cognitive Services]，並且確保選取 [文字分析] 做為 API 類型。

1. 選取方案。您可以選取 **5000 次交易/月的免費層**。因為這是免費方案，您不需要為使用服務支付費用。您必須登入您的 Azure 訂用帳戶。

1. 完成其他欄位，並且建立您的帳戶。

1. 註冊文字分析後，找到您的 **API 金鑰**。複製主要金鑰，您在使用 API 服務時需要它。


## 工作 2 - 偵測情感、主要片語和語言 ####

在您的文字中偵測情感、主要片語和語言很容易。以程式設計方式取得與[示範體驗](//go.microsoft.com/fwlink/?LinkID=759712)所傳回的相同結果。

**秘訣！** 對於情感分析，我們建議您將文字分割成句子。這通常可以讓情感預測的精確度更高。

1. 您必須將標頭設為下列項目。請注意，JSON 目前只接受 API 的輸入格式。不支援 XML。

		Ocp-Apim-Subscription-Key: <your API key>
		Content-Type: application/json
		Accept: application/json

1. 接下來，以 JSON 格式化您的輸入資料列。對於情感、主要片語和語言，它們的格式相同。請注意，每個識別碼應該是唯一的，並且是系統傳回的識別碼。可以提交的單一文件大小上限為 10KB，提交輸入總計的大小上限為 1MB。一次呼叫中不能提交超過 1000 份文件。輸入範例如下所示：

		{
			"documents": [
				{
					"id": "1",
					"text": "First document"
				},
                ...
                {
					"id": "100",
					"text": "Final document"
				}
			]
		}

1. 使用情感、主要片語和語言的輸入，對系統進行 **POST** 呼叫。URL 如下所示：

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages

1. 此呼叫會傳回 JSON 格式的回應，具有識別碼和偵測到的屬性。情感輸出的範例如下所示 (排除錯誤詳細資料)。在情感的案例中，會針對每份文件傳回 0 與 1 之間的分數︰

        // Sentiment response
		{
		  	"documents": [
				{
					"id": "1",
					"score": "0.934"
		        },
                ...
                {
					"id": "100",
					"score": "0.002"
		        },
			]
		}

        // Key phrases response
        {
		  	"documents": [
				{
					"id": "1",
					"keyPhrases": ["key phrase 1", ..., "key phrase n"]
		        },
                ...
                {
					"id": "100",
					"keyPhrases": ["key phrase 1", ..., "key phrase n"]
		        },
			]
		}

        // Languages response
        {
		  	"documents": [
				{
					"id": "1",
					"detectedLanguages": [
                        {
                            "name": "English",
                            "iso6391Name": "en",
                            "score": "1"
                        }
                    ]
		        },
                ...
                {
                    "id": "100",
                    "detectedLanguages": [
                        {
                            "name": "French",
                            "iso6391Name": "fr",
                            "score": "0.985"
                        }
                    ]
		        }
			]
		}

        

## 工作 3 - 在文字主體中偵測主題 ####

這是新發行的 API，可針對已提交的文字記錄清單傳回前幾個偵測到的主題。主題是以關鍵片語識別，可以是一或多個相關文字。此 API 的設計適用於簡短的人工書寫文字，例如評論和使用者意見反應。

這個 API **至少需要提交 100 筆文字記錄**，但其設計可偵測數百至數千筆記錄的主題。任何非英文的記錄或少於 3 個字詞的記錄都會被捨棄，因此不會被指派至主題。對於主題偵測，可以提交的單一文件大小上限為 30KB，提交輸入總計的大小上限為 30MB。

有兩個額外的**選擇性**輸入參數，可以協助改善結果的品質︰

- **停用字詞。** 這些字詞及其相關形式 (例如複數) 都將從整個主題偵測管線排除。針對常見字詞使用 (例如，“issue”、“error” 和 “user” 可能是有關客戶對於軟體的抱怨的適當選項)。每個字串應該是單一字詞。
- **停用片語** - 將會從傳回的主題清單中排除這些片語。使用此選項來排除您不想要在結果中看到的一般主題。例如，"Microsoft" 和 "Azure" 會是排除的主題的適當選擇。字串可以包含多個字詞。

依照下列步驟來偵測文字中的主題。

1. 以 JSON 格式化輸入。此時，您可以定義停用字詞和停用片語。

		{
			"documents": [
				{
					"id": "1",
					"text": "First document"
				},
                ...
                {
					"id": "100",
					"text": "Final document"
				}
			],
			"stopWords": [
				"issue", "error", "user"
			],
			"stopPhrases": [
				"Microsoft", "Azure"
			]
		}

1. 使用在「工作 2」中定義的相同標頭，對主題端點進行 **POST** 呼叫︰

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/topics

1. 這會傳回 `operation-location` 做為回應中的標頭，其中的值是用來查詢所產生的主題的 URL︰

        'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

1. 定期使用 **GET** 要求，查詢傳回的 `operation-location`。建議每分鐘一次。

        GET https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>

1. 端點會在處理之前傳回包括 `{"status": "notstarted"}` 的回應，在處理時且於 `{"status": "succeeded"}` 具有完成之後的輸出時，傳回包括 `{"status": "running"}` 的回應。然後，您可以使用輸出，其格式如下 (請注意，例如錯誤格式和日期的詳細資料已從這個範例中排除)︰

		{
			"status": "succeeded",
			"processingResult": {
			  	"topics": [
                    {
					    "id": "8b89dd7e-de2b-4a48-94c0-8e7844265196"
					    "score": "5"
					    "keyPhrase": "first topic name"
                    },
                    ...
                    {
					    "id": "359ed9cb-f793-4168-9cde-cd63d24e0d6d"
					    "score": "3"
					    "keyPhrase": "final topic name"
                    }
                ],
			  	"topicAssignments": [
                    {
					    "topicId": "8b89dd7e-de2b-4a48-94c0-8e7844265196",
					    "documentId": "1",
					    "distance": "0.354"
                    },
                    ...
                    {
					    "topicId": "359ed9cb-f793-4168-9cde-cd63d24e0d6d",
					    "documentId": "55",
					    "distance": "0.758"
                    },            
                ]
			}
		}

## 後續步驟 ##

恭喜！ 您已完成對資料使用文字分析。現在您可以考慮使用一種工具，例如 [Power BI](//powerbi.microsoft.com) 以視覺化呈現您的資料，以及自動化您的見解，提供文字資料的即時檢視。

<!---HONumber=AcomDC_0330_2016-->