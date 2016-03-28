<properties 
    pageTitle="使用 Logic Apps 的 DocumentDB 變更通知 | Microsoft Azure" 
    description="." 
    keywords="變更通知"
    services="documentdb" 
    authors="hedidin" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="rest-api" 
    ms.topic="article" 
    ms.date="03/10/2016" 
    ms.author="b-hoedid"/>

# 使用 Logic Apps 的新增或已變更 DocumentDB 資源的通知

這篇文章的靈感來自我在某一個 DocumentDB 社群論壇上看到的一個問題。這個問題是 **DocumentDB 是否支援已修改資源的通知？**

我已使用 BizTalk Server 許多年，這是使用 [WCF LOB 配接器](https://msdn.microsoft.com/library/bb798128.aspx)時非常常見的案例。因此我決定查看是否可以在新增和/或已修改過文件的 DocumentDB 中複製此功能。

本文提供變更通知解決方案的元件概觀，其中包含[觸發程序](documentdb-programming.md#trigger)和[邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)。重要程式碼片段會以內嵌方式提供，並可在 [GitHub](https://github.com/HEDIDIN/DocDbNotifications) 上取得整個解決方案。

## 使用案例

下列案例是這篇文章的使用案例。

DocumentDB 是 Health Level Seven International (HL7) Fast Healthcare Interoperability Resources (FHIR) 文件的儲存機制。假設您的 DocumentDB 資料庫與您的 API 和邏輯應用程式一起構成 HL7 FHIR Server。醫療保健機構會將病患的資料儲存在 DocumentDB 的 "Patients" 資料庫。病患資料庫中有數個集合：Clinical、Identification 等。病患資訊位於識別之下。您具有名為 "Patient" 的集合。

Cardiology 部門會追蹤個人健康和練習資料。搜尋新增或已修改的病患記錄相當耗時。他們詢問 IT 部門是否有辦法讓他們收到新增或已修改病患記錄的通知。

IT 部門表示他們可以輕鬆提供此通知。他們還表示可以將文件推送至 [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/)，以便 Cardiology 部門存取。

## IT 部門如何解決此問題

為了建立此應用程式，IT 部門決定先建立其模型。使用「商務程序模型和標記法 (BPMN)」的好處就是技術和非技術人員均可輕鬆瞭解。這整個通知程序會被視為商務程序。

## 通知程序的高階檢視

1. 從具有計時器觸發程序的邏輯應用程式開始。根據預設，每小時都會執行觸發程序。
2. 接下來，您可以對邏輯應用程式執行 HTTP POST。
3. 邏輯應用程式會執行所有的工作。

![高階檢視](./media/documentdb-change-notification/high-level-view.png)

### 讓我們看一下此邏輯應用程式的作用
如果查看下圖，LogicApp 工作流程中會有幾個步驟。

![主要邏輯程序](./media/documentdb-change-notification/main-logic-app-process.png)

步驟如下：

1. 您需要從 API 應用程式取得目前的 UTC 日期時間。預設值為一小時前。

2. UTC 日期時間會轉換成 Unix 時間戳記格式。這是 DocumentDB 中時間戳記的預設格式。

3. 您將此值 POST 至 API 應用程式，這會進行 DocumentDB 查詢。此值使用於查詢中。

    ```SQL
     	SELECT * FROM Patients p WHERE (p._ts >= @unixTimeStamp)
    ```

    > [AZURE.NOTE] \_ts 表示所有 DocumentDB 資源的時間戳記中繼資料。

4. 如果已找到文件，則回應主體會傳送至 Azure Blob 儲存體。

    > [AZURE.NOTE] Blob 儲存體需要 Azure 儲存體帳戶。您必須佈建 Azure Blob 儲存體帳戶，並加入名為 patients 的新 Blob。如需詳細資訊，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)和[開始使用 Azure Blob 儲存體](../storage/storage-dotnet-how-to-use-blobs.md)。

5. 最後會傳送電子郵件，通知收件者已找到的文件數目。如果找不到任何文件，電子郵件本文會是「找到 0 份文件」。

您現在已了解工作流程的作用，讓我們看看其實作方式。

### 我們從主要邏輯應用程式開始討論。

如果您不熟悉 Logic Apps，可以在 [Azure Marketplace](https://portal.azure.com/) 加以取得，而且您可以在[什麼是 Logic Apps？](../app-service-logic/app-service-logic-what-are-logic-apps.md)中進一步了解

當您建立新的邏輯應用程式時，系統會詢問您「您要如何開始？」

當您按一下文字方塊內部時，您有各種事件可供選擇。對於此邏輯應用程式，選取 [手動 - 收到 HTTP 要求時]，如下所示。

![開始進行](./media/documentdb-change-notification/starting-off.png)

### 完整邏輯應用程式的設計檢視
讓我們往前跳並查看邏輯應用程式 (名為 DocDB ) 的完整設計檢視。

![邏輯應用程式工作流程](./media/documentdb-change-notification/workflow-expanded.png)

在邏輯應用程式設計工具中編輯動作時，您可以選取來自 HTTP 要求或來自前一個動作的 [輸出]，如以下 sendMail 動作所示。

![選擇輸出](./media/documentdb-change-notification/choose-outputs.png)

在工作流程中的每個動作之前，您可以進行決策；如下圖所示的 [新增動作] 或 [新增條件]。

![進行決策](./media/documentdb-change-notification/add-action-or-condition.png)

如果您選取 [新增條件]，即會出現如下圖所示的表單，以便輸入您的邏輯。基本上，這是商務規則。如果您按一下欄位內部，您可以選取來自前一個動作的參數。您也可以直接輸入值。

![新增條件](./media/documentdb-change-notification/condition1.png)

> [AZURE.NOTE] 您也能夠輸入 [程式碼檢視] 中的所有一切。

讓我們在程式碼檢視中看一下完整的邏輯應用程式。

```JSON
   
   	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
    "actions": {
        "Conversion": {
            "conditions": [
                {
                    "dependsOn": "GetUtcDate"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "currentdateTime": "@{body('GetUtcDate')}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "Createfile": {
            "conditions": [
                {
                    "expression": "@greater(length(body('GetDocuments')), 0)"
                },
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "@body('GetDocuments')",
                "host": {
                    "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['azureblob']['connectionId']"
                    }
                },
                "method": "post",
                "path": "/datasets/default/files",
                "queries": {
                    "folderPath": "/patients",
                    "name": "Patient_@{guid()}.json"
                }
            },
            "type": "ApiConnection"
        },
        "GetDocuments": {
            "conditions": [
                {
                    "dependsOn": "Conversion"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "unixTimeStamp": "@body('Conversion')"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "GetUtcDate": {
            "conditions": [],
            "inputs": {
                "method": "get",
                "queries": {
                    "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "sendMail": {
            "conditions": [
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
                "headers": {
                    "Content-type": "application/x-www-form-urlencoded"
                },
                "method": "POST",
                "uri": "https://api.sendgrid.com/api/mail.send.json"
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(body('GetDocuments')))} Records Found"
        }
    },
    "parameters": {
        "$connections": {
            "defaultValue": {},
            "type": "Object"
        },
        "fromAddress": {
            "defaultValue": "user@msn.com",
            "type": "String"
        },
        "toAddress": {
            "defaultValue": "XXXXX@XXXXXXX.net",
            "type": "String"
        }
    },
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {
                    "properties": {},
                    "required": [],
                    "type": "object"
                }
            },
            "type": "Manual"
        }
	
```

如果您不熟悉程式碼中各區段所代表的意義，您可以檢視[邏輯應用程式工作流程定義語言](http://aka.ms/logicappsdocs)文件。

在此工作流程中，您會使用 [HTTP Webhook 觸發程序](https://sendgrid.com/blog/whats-webhook/)。如果您查看上述程式碼，您會看到以下範例所示的參數。

```C#

    =@{triggerBody()['Subject']}

```

`triggerBody()` 代表邏輯應用程式 REST API 的 REST POST 主體中包含的參數。`()['Subject']` 代表此欄位。這些參數會構成 JSON 格式的主體。

> [AZURE.NOTE] 使用 Webhook，您可以完整存取觸發程序的要求標頭和主體。在此應用程式中，您會想要主體。

如先前所述，您可以使用設計工具來指派參數，或在程式碼檢視中指派參數。如果您在程式碼檢視中指派參數，您會接著定義需有值的屬性，如下列程式碼範例所示。

```JSON

	"triggers": {
		"manual": {
		    "inputs": {
			"schema": {
			    "properties": {
			"Subject": {
			    "type" : "String"	

			}
			},
			    "required": [
			"Subject"
			     ],
			    "type": "object"
			}
		    },
		    "type": "Manual"
		}
	    }
```

您正在建立將從 HTTP POST 主體傳入的 JSON 結構描述。若要引發觸發程序，您需要回呼 URL。您將在稍後的教學課程中了解如何產生回呼 URL。

## 動作
我們來看一下在我們的邏輯應用程式中每個動作的作用。

### GetUTCDate

**設計工具檢視**

![](./media/documentdb-change-notification/getutcdate.png)

**程式碼檢視**

```JSON

	"GetUtcDate": {
		    "conditions": [],
		    "inputs": {
			"method": "get",
			"queries": {
			    "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
			},
			"uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
		    },
		    "metadata": {
			"apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
		    },
		    "type": "Http"
		},

```

此 HTTP 動作會執行 GET 作業。它會呼叫 API 應用程式 GetUtcDate 方法。Uri 會使用傳遞至觸發程序主體的 'GetUtcDate\_HoursBack' 屬性。'GetUtcDate\_HoursBack' 值會設定於第一個邏輯應用程式中。您將在稍後的教學課程中深入了解觸發程序邏輯應用程式。

這個動作會呼叫 API 應用程式以傳回 UTC 日期字串值。

#### 作業

**要求**

```JSON

	{
	    "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization",
	    "method": "get",
	    "queries": {
		  "hoursBack": "24"
	    }
	}

```

**回應**

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		  "pragma": "no-cache",
		  "cache-Control": "no-cache",
		  "date": "Fri, 26 Feb 2016 15:47:33 GMT",
		  "server": "Microsoft-IIS/8.0",
		  "x-AspNet-Version": "4.0.30319",
		  "x-Powered-By": "ASP.NET"
	    },
	    "body": "Fri, 15 Jan 2016 23:47:33 GMT"
	}

```

下一個步驟是將 UTC 日期時間值轉換為 Unix 時間戳記，這是 .NET double 類型。

### 轉換

##### 設計工具檢視

![轉換](./media/documentdb-change-notification/conversion.png)

##### 程式碼檢視

```JSON

	"Conversion": {
	    "conditions": [
		{
		    "dependsOn": "GetUtcDate"
		}
	    ],
	    "inputs": {
		"method": "post",
		"queries": {
		    "currentDateTime": "@{body('GetUtcDate')}"
		},
		"uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
	    },
	    "metadata": {
		"apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
	    },
	    "type": "Http"
	},

```

在此步驟中，您會傳入從 GetUTCDate 傳回的值。會有 dependsOn 條件，這表示 GetUTCDate 動作必須順利完成。如果未順利完成，就會略過這個動作。

這個動作會呼叫 API 應用程式以處理轉換。

#### 作業

##### 要求

```JSON

	{
	    "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion",
	    "method": "post",
	    "queries": {
		"currentDateTime": "Fri, 15 Jan 2016 23:47:33 GMT"
	    }
	}   
```

##### Response

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		  "pragma": "no-cache",
		  "cache-Control": "no-cache",
		  "date": "Fri, 26 Feb 2016 15:47:33 GMT",
		  "server": "Microsoft-IIS/8.0",
		  "x-AspNet-Version": "4.0.30319",
		  "x-Powered-By": "ASP.NET"
	    },
	    "body": 1452901653
	}
```

在下一個動作中，您可以對我們的 API 應用程式執行 POST 作業。

### GetDocuments 

##### 設計工具檢視

![取得文件](./media/documentdb-change-notification/getdocuments.png)

##### 程式碼檢視

```JSON

	"GetDocuments": {
	    "conditions": [
		{
		    "dependsOn": "Conversion"
		}
	    ],
	    "inputs": {
		"method": "post",
		"queries": {
		    "unixTimeStamp": "@{body('Conversion')}"
		},
		"uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
	    },
	    "metadata": {
		"apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
	    },
	    "type": "Http"
	},

```

對於 GetDocuments 動作，您將會傳入來自 Conversion 動作的回應主體。這是 Uri 中的參數︰

 
```C#

	unixTimeStamp=@{body('Conversion')}

```

QueryDocuments 動作會對 API 應用程式執行 HTTP POST 作業。

呼叫的方法為 **QueryForNewPatientDocuments**。

#### 作業

##### 要求

```JSON

	{
	    "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient",
	    "method": "post",
	    "queries": {
		"unixTimeStamp": "1452901653"
	    }
	}
```

##### Response

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		"pragma": "no-cache",
		"cache-Control": "no-cache",
		"date": "Fri, 26 Feb 2016 15:47:35 GMT",
		"server": "Microsoft-IIS/8.0",
		"x-AspNet-Version": "4.0.30319",
		"x-Powered-By": "ASP.NET"
	    },
	    "body": [
		{
		    "id": "xcda",
		    "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
		    "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
		    "_ts": 1454874620,
		    "_etag": ""00007d01-0000-0000-0000-56b79ffc0000"",
		    "resourceType": "Patient",
		    "text": {
			"status": "generated",
			"div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
		    },
		    "identifier": [
			{
			    "use": "usual",
			    "type": {
				"coding": [
				    {
					"system": "http://hl7.org/fhir/v2/0203",
					"code": "MR"
				    }
				]
			    },
			    "system": "urn:oid:2.16.840.1.113883.19.5",
			    "value": "12345"
			}
		    ],
		    "active": true,
		    "name": [
			{
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

下一個動作是將文件儲存至 [Azure Blog 儲存體](https://azure.microsoft.com/services/storage/)。

> [AZURE.NOTE] Blob 儲存體需要 Azure 儲存體帳戶。您必須佈建 Azure Blob 儲存體帳戶，並加入名為 patients 的新 Blob。如需詳細資訊，請參閱[開始使用 Azure Blob 儲存體](../storage/storage-dotnet-how-to-use-blobs.md)。

### 建立檔案

##### 設計工具檢視

![建立檔案](./media/documentdb-change-notification/createfile.png)

##### 程式碼檢視

```JSON

	{
    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": ""00007d01-0000-0000-0000-56b79ffc0000"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

程式碼是從設計工具中的動作產生而來。您不需要修改程式碼。

如果您不熟悉如何使用 Azure Blob API，請參閱[開始使用 Azure Blob 儲存體 API](../connectors/create-api-azureblobstorage.md)。

#### 作業

##### 要求

```JSON

	"host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": ""00007d01-0000-0000-0000-56b79ffc0000"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },….


```

##### Response

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		"pragma": "no-cache",
		"x-ms-request-id": "2b2f7c57-2623-4d71-8e53-45c26b30ea9d",
		"cache-Control": "no-cache",
		"date": "Fri, 26 Feb 2016 15:47:36 GMT",
		"set-Cookie": "ARRAffinity=29e552cea7db23196f7ffa644003eaaf39bc8eb6dd555511f669d13ab7424faf;Path=/;Domain=127.0.0.1",
		"server": "Microsoft-HTTPAPI/2.0",
		"x-AspNet-Version": "4.0.30319",
		"x-Powered-By": "ASP.NET"
	    },
	    "body": {
		"Id": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE",
		"Name": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
		"DisplayName": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
		"Path": "/Patient/Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
		"LastModified": "2016-02-26T15:47:36.215Z",
		"Size": 65647,
		"MediaType": "application/octet-stream",
		"IsFolder": false,
		"ETag": ""c-g_a-1OtaH-kNQ4WBoXLp3Zv9s/MTQ1NjUwMTY1NjIxNQ"",
		"FileLocator": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE"
	    }
	}
```

最後一個步驟是傳送電子郵件通知

### sendEmail

##### 設計工具檢視

![傳送電子郵件](./media/documentdb-change-notification/sendemail.png)

##### 程式碼檢視

```JSON


	"sendMail": {
	    "conditions": [
		{
		    "dependsOn": "GetDocuments"
		}
	    ],
	    "inputs": {
		"body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
		"headers": {
		    "Content-type": "application/x-www-form-urlencoded"
		},
		"method": "POST",
		"uri": "https://api.sendgrid.com/api/mail.send.json"
	    },
	    "type": "Http"
	}
```

在這個動作中，您會傳送電子郵件通知。您會使用 [SendGrid](https://sendgrid.com/marketing/sendgrid-services?cvosrc=PPC.Bing.sendgrib&cvo_cid=SendGrid%20-%20US%20-%20Brand%20-%20&mc=Paid%20Search&mcd=BingAds&keyword=sendgrib&network=o&matchtype=e&mobile=&content=&search=1&utm_source=bing&utm_medium=cpc&utm_term=%5Bsendgrib%5D&utm_content=%21acq%21v2%2134335083397-8303227637-1649139544&utm_campaign=SendGrid+-+US+-+Brand+-+%28English%29)。

其程式碼是使用邏輯應用程式的範本以及 [101-logic-app-sendgrid Github 儲存機制](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sendgrid)中的 SendGrid 所產生。
 
HTTP 作業是一個 POST。

authorization 參數位於觸發程序屬性中

```JSON

	},
		"sendgridPassword": {
			 "type": "SecureString"
		 },
		 "sendgridUsername": {
			"type": "String"
		 }

		In addition, other parameters are static values set in the Parameters section of the Logic App. These are:
		},
		"toAddress": {
		    "defaultValue": "XXXX@XXXX.com",
		    "type": "String"
		},
		"fromAddress": {
		    "defaultValue": "XXX@msn.com",
		    "type": "String"
		},
		"emailBody": {
		    "defaultValue": "@{string(concat(int(length(actions('QueryDocuments').outputs.body)) Records Found),'/n', actions('QueryDocuments').outputs.body)}",
		    "type": "String"
		},

```

emailBody 會串連查詢所傳回的文件數目 (可能是 "0" 或更多) 與 "Records Found"。其餘的參數會從觸發程序參數設定。

這個動作取決於 **GetDocuments** 動作。

#### 作業

##### 要求
```JSON

	{
	    "uri": "https://api.sendgrid.com/api/mail.send.json",
	    "method": "POST",
	    "headers": {
		"Content-type": "application/x-www-form-urlencoded"
	    },
	    "body": "api_user=azureuser@azure.com&api_key=Biz@Talk&from=user@msn.com&to=XXXX@XXXX.com&subject=New Patients&text=37 Documents Found"
	}

```

##### Response

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		"connection": "keep-alive",
		"x-Frame-Options": "DENY,DENY",
		"access-Control-Allow-Origin": "https://sendgrid.com",
		"date": "Fri, 26 Feb 2016 15:47:35 GMT",
		"server": "nginx"
	    },
	    "body": {
		"message": "success"
	    }
	}
```

最後，您要能夠在 Azure 入口網站上看到邏輯應用程式的結果。若要這麼做，請將參數加入至 outputs 區段。


```JSON

	"outputs": {
		"Results": {
		    "type": "String",
		    "value": "@{int(length(actions('QueryDocuments').outputs.body))} Records Found"
		}

```

這會傳回在電子郵件本文中傳送的相同值。下圖顯示「找到 29 筆記錄」的範例。

![結果](./media/documentdb-change-notification/logic-app-run.png)

## 度量
您可以在入口網站中設定主要邏輯應用程式的監視。這可讓您檢視 [執行延遲] 和其他事件，如下圖所示。

![](./media/documentdb-change-notification/metrics.png)

## DocDb 觸發程序

此邏輯應用程式是在主要邏輯應用程式上啟動工作流程的觸發程序。

下圖顯示 [設計工具檢視]。

![](./media/documentdb-change-notification/trigger-recurrence.png)

```JSON

	{
	    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	    "actions": {
		"Http": {
		    "conditions": [],
		    "inputs": {
			"body": {
			    "EmailTo": "XXXXXX@XXXXX.net",
			    "GetUtcDate_HoursBack": "24",
			    "Subject": "New Patients",
			    "sendgridPassword": "********",
			    "sendgridUsername": "azureuser@azure.com"
			},
			"method": "POST",
			"uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c"
		    },
		    "type": "Http"
		}
	    },
	    "contentVersion": "1.0.0.0",
	    "outputs": {
		"Results": {
		    "type": "String",
		    "value": "@{body('Http')['status']}"
		}
	    },
	    "parameters": {},
	    "triggers": {
		"recurrence": {
		    "recurrence": {
			"frequency": "Hour",
			"interval": 24
		    },
		    "type": "Recurrence"
		}
	    }
	}

```

觸發程序已設定為 24 個小時的週期。此動作是 HTTP POST，其使用主要邏輯應用程式的回呼 URL。主體包含 JSON 結構描述中所指定的參數。

#### 作業

##### 要求

```JSON

	{
	    "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c",
	    "method": "POST",
	    "body": {
		"EmailTo": "XXXXXX@XXXXX.net",
		"GetUtcDate_HoursBack": "24",
		"Subject": "New Patients",
		"sendgridPassword": "********",
		"sendgridUsername": "azureuser@azure.com"
	    }
	}

```

##### Response

```JSON

	{
	    "statusCode": 202,
	    "headers": {
		"pragma": "no-cache",
		"x-ms-ratelimit-remaining-workflow-writes": "7486",
		"x-ms-ratelimit-burst-remaining-workflow-writes": "1248",
		"x-ms-request-id": "westus:2d440a39-8ba5-4a9c-92a6-f959b8d2357f",
		"cache-Control": "no-cache",
		"date": "Thu, 25 Feb 2016 21:01:06 GMT"
	    }
	}
```

讓我們現在查看 API 應用程式。

## DocDBNotificationApi

雖然應用程式中有數個作業，但您只會使用三個。

* GetUtcDate
* ConvertToTimeStamp
* QueryForNewPatientDocuments

### DocDBNotificationApi 作業
讓我們查看 Swagger 文件

> [AZURE.NOTE] 為了讓您從外部呼叫作業，您需要在 API 應用程式的設定中加入 CORS 允許的原始值 "*" (不含引號)，如下圖所示。

![Cors 組態](./media/documentdb-change-notification/cors.png)

#### GetUtcDate

![G](./media/documentdb-change-notification/getutcdateswagger.png)

#### ConvertToTimeStamp

![取得 UTC 日期](./media/documentdb-change-notification/converion-swagger.png)

#### QueryForNewPatientDocuments

![查詢](./media/documentdb-change-notification/patientswagger.png)

讓我們看看這項作業背後的程式碼。

#### GetUtcDate

```C#

    /// <summary>
	/// Gets the current UTC Date value
	/// </summary>
	/// <returns></returns>
	[H ttpGet]
	[Metadata("GetUtcDate", "Gets the current UTC Date value minus the Hours Back")]
	[SwaggerOperation("GetUtcDate")]
	[SwaggerResponse(HttpStatusCode.OK, type: typeof (string))]
	[SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
	public string GetUtcDate(
	   [Metadata("Hours Back", "How many hours back from the current Date Time")] int hoursBack)
	{


	    return DateTime.UtcNow.AddHours(-hoursBack).ToString("r");
	}
```

這項作業只會傳回目前的 UTC 日期時間減去 HoursBack 值。

#### ConvertToTimeStamp

``` C#

        /// <summary>
        ///     Converts DateTime to double
        /// </summary>
        /// <param name="currentdateTime"></param>
        /// <returns></returns>
        [Metadata("Converts Universal DateTime to number")]
        [SwaggerResponse(HttpStatusCode.OK, null, typeof (double))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "DateTime is invalid")]
        [SwaggerResponse(HttpStatusCode.InternalServerError)]
        [SwaggerOperation(nameof(ConvertToTimestamp))]
        public double ConvertToTimestamp(
            [Metadata("currentdateTime", "DateTime value to convert")] string currentdateTime)
        {
            double result;

            try
            {
                var uncoded = HttpContext.Current.Server.UrlDecode(currentdateTime);

                var newDateTime = DateTime.Parse(uncoded);
                //create Timespan by subtracting the value provided from the Unix Epoch
                var span = newDateTime - new DateTime(1970, 1, 1, 0, 0, 0, 0).ToLocalTime();

                //return the total seconds (which is a UNIX timestamp)
                result = span.TotalSeconds;
            }
            catch (Exception e)
            {
                throw new Exception("unable to convert to Timestamp", e.InnerException);
            }

            return result;
        }

```

這項作業會將 GetUtcDate 作業的回應轉換為雙精度值。

#### QueryForNewPatientDocuments

```C#

	    /// <summary>
        ///     Query for new Patient Documents
        /// </summary>
        /// <param name="unixTimeStamp"></param>
        /// <returns>IList</returns>
        [Metadata("QueryForNewDocuments",
            "Query for new Documents where the Timestamp is greater than or equal to the DateTime value in the query parameters."
            )]
        [SwaggerOperation("QueryForNewDocuments")]
        [SwaggerResponse(HttpStatusCode.OK, type: typeof (Task<IList<Document>>))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "The syntax of the SQL Statement is incorrect")]
        [SwaggerResponse(HttpStatusCode.NotFound, "No Documents were found")]
        [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
        // ReSharper disable once ConsiderUsingAsyncSuffix
        public IList<Document> QueryForNewPatientDocuments(
            [Metadata("UnixTimeStamp", "The DateTime value used to search from")] double unixTimeStamp)
        {
            var context = new DocumentDbContext();
            var filterQuery = string.Format(InvariantCulture, "SELECT * FROM Patient p WHERE p._ts >=  {0}",
                unixTimeStamp);
            var options = new FeedOptions {MaxItemCount = -1};


            var collectionLink = UriFactory.CreateDocumentCollectionUri(DocumentDbContext.DatabaseId,
                DocumentDbContext.CollectionId);

            var response =
                context.Client.CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();

            return response.ToList();
	}

```

這項作業會使用 [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 建立文件查詢。

```C#
     CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();
```

會傳入 ConvertToTimeStamp 作業 (unixTimeStamp) 的回應。此作業會傳回文件清單 `IList<Document>`。

我們先前曾談論 CallbackURL。若要在主要邏輯應用程式中啟動工作流程，您必須使用 CallbackURL 呼叫它。

## CallbackURL

若要開始進行，您將需要 Azure AD 權杖。取得此權杖可能很困難。我曾尋找簡單的方法和 Jeff Hollan，他是 Azure Logic App 的程式管理員，建議在 PowerShell 中使用 [armclient](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)。您可以依照所提供的指示進行安裝。

您想要使用的作業為「登入」和「呼叫 ARM API」。
 
登入：您使用相同的認證登入 Azure 入口網站。

「呼叫 ARM API」作業將會產生您的 CallBackURL。

在 PowerShell 中，您會如下呼叫它︰

```powershell

	ArmClient.exe post https://management.azure.com/subscriptions/[YOUR SUBSCRIPTION ID/resourcegroups/[YOUR RESOURCE GROUP]/providers/Microsoft.Logic/workflows/[YOUR LOGIC APP NAME/triggers/manual/listcallbackurl?api-version=2015-08-01-preview

```

結果應該如下所示：

```powershell

	https://prod-02.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-prevaiew&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=XXXXXXXXXXXXXXXXXXX

```

您可以使用 [postman](http://www.getpostman.com/) 等工具來測試您的主要邏輯應用程式，如下圖所示。

![Postman](./media/documentdb-change-notification/newpostman.png)

下表列出的觸發程序參數會構成 DocDB 觸發程序邏輯應用程式的主體。

參數 | 說明 
--- | --- 
GetUtcDate\_HoursBack | 用來設定搜尋開始日期的時數
sendgridUsername | 用來設定搜尋開始日期的時數
sendgridPassword | Send Grid 電子郵件的使用者名稱
EmailTo | 將會收到電子郵件通知的電子郵件地址
主旨 | 電子郵件的主旨

## 在 Azure Blob 服務中檢視病患資料

請移至您的 Azure 儲存體帳戶，並選取 [服務] 之下的 Blob，如下圖所示。

![儲存體帳戶](./media/documentdb-change-notification/docdbstorageaccount.png)

您將可檢視 Patient Blob 檔案資訊，如下所示。

![Blob 服務](./media/documentdb-change-notification/blobservice.png)


## 摘要

在本逐步解說中，您會了解下列各項：

* 有可能在 DocumentDB 中實作通知。
* 使用 Logic Apps，您可以將此程序自動化。
* 使用 Logic Apps，您可以減少傳遞應用程式所需的時間。
* 使用 HTTP，您可以輕鬆取用邏輯應用程式內的 API 應用程式。
* 您可以輕鬆地建立 CallBackURL，以取代 HTTP 接聽程式。
* 您可以利用 Logic Apps 設計工具輕鬆建立自訂工作流程。

重點在於事先規劃並建立您的工作流程模型。

## 後續步驟
請下載並使用 [Github](https://github.com/HEDIDIN/DocDbNotifications) 上提供的邏輯應用程式程式碼。竭誠邀請您建置應用程式，並將變更提交至儲存機制。

若要深入了解 DocumentDB，請瀏覽[學習路徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)。

<!---HONumber=AcomDC_0316_2016-->