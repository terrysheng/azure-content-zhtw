<properties 
	pageTitle="新結構描述版本 2015-08-01 預覽" 
	description="了解如何撰寫最新版邏輯應用程式的 JSON 定義" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="stepsic"/>
	
# 新結構描述版本 2015-08-01 預覽

邏輯應用程式的新結構描述和 API 版本有一些增強功能，可提升邏輯應用程式的可靠性和易用性。有 4 個主要的差異：

1. **APIApp** 動作類型已更新為新的 **APIConnection** 動作類型。
2. **Repeat** 已重新命名為 **Foreach**。
3. 不再需要 **HTTP 接聽程式** API 應用程式。
4. 呼叫子工作流程時使用新的結構描述。

## 1\.移轉至 API 連接

最大的改變是您不再需要將 API 應用程式部署至您的 Azure 訂用帳戶，也能使用 API。您有 2 種方式可以使用 API：* Managed API * 您自訂的 Web API

每一種都因為其管理和裝載模型不同，而有稍微不同的處理方式。此模型的優點之一是您不再受限於只能存取部署在資源群組中的資源。

### Managed API

有許多由 Microsoft 替您管理的 API，例如 Office 365、Salesforce、Twitter、FTP 等...其中有些 Managed API 可直接使用，例如 Bing 翻譯，而有些則需要設定。此設定稱為*連接*。

例如，當您使用 Office 365 時，您需要建立包含 Office 365 登入權杖的連接。此權杖會安全地儲存並重新整理，讓您的邏輯應用程式隨時都可以呼叫 Office 365 API。或者，如果您想要連線到 SQL 或 FTP 伺服器，您需要建立具有連接字串的連接。

這些動作在定義內稱為 `APIConnection`。以下是一個呼叫 Office 365 來傳送電子郵件的連接範例：

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

輸入中特別有關於 API 連接的部分是 `host` 物件。這包含兩個部分： `api` 和 `connection`。

`api` 有用來裝載該 Managed API 的執行階段 URL。您可以呼叫 `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview` 查看可供您使用的所有 Managed API。

當您使用 API 時，可能有也可能沒有定義任何**連接參數**。如果沒有的話，則不需要**連接**。如果有的話，則您必須建立連接。當您建立該連接時，它會有您所選擇的名稱，然後您在 `host` 物件內的 `connection` 物件中會參考它。若要在資源群組中建立連接，請呼叫：

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

使用下列主體：


```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
	"parameterValues" : {
		"accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
	}
  },
  "location" : "{Logic app's location}"
}
```

### 在 Azure Resource Manager 範本中部署 Managed API

只要應用程式不需要互動式登入，您可以在 ARM 範本中建立完整的應用程式。如果它需要登入，您可以使用 ARM 範本來設定所有項目，但仍然必須造訪入口網站來授權連接。

```
	"resources": [{
		"apiVersion": "2015-08-01-preview",
		"name": "azureblob",
		"type": "Microsoft.Web/connections",
		"location": "[resourceGroup().location]",
		"properties": {
			"api": {
				"id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
			},
			"parameterValues": {
				"accountName": "[parameters('storageAccountName')]",
				"accessKey": "[parameters('storageAccountKey')]"
			}
		}
	}, {
		"type": "Microsoft.Logic/workflows",
		"apiVersion": "2015-08-01-preview",
		"name": "[parameters('logicAppName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[resourceId('Microsoft.Web/connections', 'azureblob')]"
		],
		"properties": {
			"sku": {
				"name": "[parameters('sku')]",
				"plan": {
					"id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
				}
			},
			"definition": {
				"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
				"actions": {
					"Create_file": {
						"type": "apiconnection",
						"inputs": {
							"host": {
								"api": {
									"runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
								},
								"connection": {
									"name": "@parameters('$connections')['azureblob']['connectionId']"
								}
							},
							"method": "post",
							"queries": {
								"folderPath": "[concat('/',parameters('containerName'))]",
								"name": "helloworld.txt"
							},
							"body": "@decodeDataUri('data:,Hello+world!')",
							"path": "/datasets/default/files"
						},
						"conditions": []
					}
				},
				"contentVersion": "1.0.0.0",
				"outputs": {},
				"parameters": {
					"$connections": {
						"defaultValue": {},
						"type": "Object"
					}
				},
				"triggers": {
					"recurrence": {
						"type": "Recurrence",
						"recurrence": {
							"frequency": "Day",
							"interval": 1
						}
					}
				}
			},
			"parameters": {
				"$connections": {
					"value": {
						"azureblob": {
							"connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
							"connectionName": "azureblob",
							"id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
						}

					}
				}
			}
		}
	}]
```

在此範例中，您可以看到連接只是存在於資源群組中的一般資源。它們會參考您的訂用帳戶中可供您使用的 Managed API。

### 您自訂的 Web API

如果您使用您自己的 API (明確地說，不是 Microsoft 管理的 API)，則應該使用內建 **HTTP** 動作來呼叫它們。為了獲得理想的體驗，您應該公開您的 API 的 swagger 端點。這樣可讓邏輯應用程式設計工具呈現您的 API 的輸入和輸出。如果沒有 swagger，設計工具只能將輸入和輸出顯示成不透明的 JSON 物件。

下列範例顯示新的 `metadata.apiDefinitionUrl` 屬性：```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

如果您將 Web API 裝載於 **App Service**，它會自動顯示在設計工具可用的動作清單。如果不是的話，您必須直接貼在 URL 中。Swagger 端點必須未經驗證，才能在邏輯應用程式設計工具內使用 (雖然您可以使用 Swagger 中支援的任何方法來保護 API 本身)。

### 搭配 2015-08-01-preview 使用您已部署的 API 應用程式

如果您先前已部署 API 應用程式，您可以透過 **HTTP** 動作呼叫它。

例如，如果您使用 Dropbox 列出檔案，您的 **2014-12-01-preview** 結構描述版本定義中可能會有類似下面的內容：```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

您可以如下建構同等的 HTTP 動作 (邏輯應用程式定義的 parameters 區段保持不變)：

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

逐一解說這些屬性：

| 動作屬性 | 說明 |
| --------------- | -----------  |
| `type` | `Http` 而不是 `APIapp` |
| `metadata.apiDefinitionUrl` | 如果您想要在邏輯應用程式設計工具中使用此動作，您需要包含中繼資料端點。這是建構自：`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | 這是建構自：`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | 一律為 `POST` |
| `inputs.body` | 與 api app 參數相同 | 
| `inputs.authentication` | 與 api app 驗證相同 |

此方法應可適用於 API 應用程式的所有動作。不過，請記住，已不再支援這些先前的 API 應用程式，您應該移轉到上述的其他兩個選項 (Managed API 或裝載您自訂的 Web API)。

## 2\.Repeat 重新命名為 Foreach

針對先前的結構描述版本，我們接到許多的客戶意見，他們覺得 **Repeat** 造成混淆，並沒有正確表達它真的是 for each 迴圈。因此，我們已將它重新命名為 **Foreach**。例如：

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

現在會寫成：

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

先前的函式 `@repeatItem()` 用來參考目前反覆處理的項目。這已簡化到只剩下 `@item()`。

### 參考 Foreach 的輸出
為了進一步簡化，**Foreach** 動作的輸出不會包裝在稱為 **repeatItems** 的物件中。這表示，上述重複的輸出原本為：

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang="en" xml:lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:Web="http://schemas.live.com/Web/">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

現在變成：

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang="en" xml:lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:Web="http://schemas.live.com/Web/">...</html>"
        }
        "status": "Succeeded"
    }
]
```

當參考這些輸出時，若要取得動作的主體，您必須執行：

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

現在，您可以改為執行：

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

經過這些變更，已移除函式 `@repeatItem()`、`@repeatBody()` 和 `@repeatOutputs()`。

## 3\.原生 HTTP 接聽程式 
現在已內建 HTTP 接聽程式功能，所以您不再需要部署 HTTP 接聽程式 API 應用程式。請參閱[這裡有關如何讓您的邏輯應用程式端點可供呼叫的完整詳細資料](app-service-logic-http-endpoint.md)。

經過這些變更，已移除函式 `@accessKeys()`，改為以 `@listCallbackURL()` 函式來取得端點 (如果需要)。此外，您現在必須在邏輯應用程式中至少定義一個觸發程序。如果您想要 `/run` 工作流程，您必須有一個 `manual`、`apiConnectionWebhook` 或 `httpWebhook` 觸發程序。

## 4\.呼叫子工作流程

在以前，呼叫子工作流程時必須移至該工作流程、取得存取權杖，然後貼到要呼叫該子工作流程的邏輯應用程式的定義中。在新的結構描述版本中，邏輯應用程式引擎會在執行階段自動為子工作流程產生 SAS，這表示您不需要將任何機密資料貼到定義中。下列是一個範例：

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "conditions" : []
}
```

第二個改進是我們將允許子工作流程完整存取內送要求。這表示您可以將參數傳入 *queries* 區段和 *headers* 物件中，而且您可以完整定義整個主體。

最後是必須對子工作流程進行的變更。您以前可能只是直接呼叫子工作流程，但現在，您必須在工作流程中定義觸發程序端點供父工作流程呼叫。一般而言，這表示您需要加入**手動**類型的觸發程序，然後用在父定義中。請注意，`host` 屬性明確地具有 `triggerName`，因為您一定要指定您所叫用的觸發程序。

## 其他變更

### 新的 queries 屬性
所有動作類型現在支援一個稱為 **queries** 的新輸入。這可以是結構化物件，而不必是您手動組合的字串。

### 已重新命名 parse() 函式
由於我們即將新增更多內容類型，`parse()` 函式已重新命名為 `json()`。

## 敬請期待：企業整合 API
目前，我們尚未提供 Managed 版本的企業整合 API (例如 AS2)。這些都已納入[藍圖](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/)中，敬請期待。在此期間，您可以透過 HTTP 動作來使用現有已部署的 BizTalk API，如以上的「使用您已部署的 API 應用程式」所述。

<!---HONumber=AcomDC_0224_2016-->