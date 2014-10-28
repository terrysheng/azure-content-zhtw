<properties pageTitle="How to use the API Inspector to trace calls in Azure API Management" metaKeywords="" description="Learn how to trace calls using the API Inspector in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to use the API Inspector to trace calls in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# 如何在 Azure API 管理中使用 API 偵測器來追蹤呼叫

API 管理 (預覽) 提供 API 偵測器工具協助您進行 API 的偵錯和疑難排解。API 偵測器可以在應用程式的程式設計中，也可以直接從開發人員入口網站中使用。本指南提供使用 API 偵測器的逐步解說。

## 本主題內容

-   [使用 API 偵測器來追蹤呼叫][使用 API 偵測器來追蹤呼叫]
-   [檢查追蹤][檢查追蹤]
-   [後續步驟][後續步驟]

## <a name="trace-call"> </a> 使用 API 偵測器來追蹤呼叫

若要使用 API 偵測器，請加入 **ocp-apim-trace:true** 要求標頭至您的操作呼叫，然後使用 **ocp-apim-trace-location** 回應標頭所指出的 URL 來下載並檢查追蹤。這可以透過程式設計來進行，也可以直接從開發人員入口網站來執行。

本教學課程示範如何使用 API 偵測器，以追蹤使用 Echo API 的操作。

> 每個 API 管理服務執行個體隨附預先設定的範例 Echo API，可供您試驗與了解 API 管理。Echo API 會將任何傳送給它的輸入傳回。若要使用該 API，您可以叫用任何 HTTP 指令動詞，而傳回值就是您傳送的值。

若要開始，請在 API 管理服務的 Azure 入口網站中按一下 [開發入口網站]。這會帶您前往 API 管理的管理入口網站。

> 如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][開始使用 Azure API 管理]教學課程中的[建立 API 管理服務執行個體][建立 API 管理服務執行個體]。

![API Management developer portal][API Management developer portal]

您可以從開發人員入口網站直接呼叫作業，以便檢視和測試 API 的操作。在此教學課程步驟中，您將呼叫 **Echo API** 的 **Get Resource** 方法。

從上方功能表中按一下 [API]，然後按一下 [Echo API]。

![Echo API][Echo API]

> 如果您的帳戶只設定或只看見一個 API，按一下 API 將帶您直接前往該 API 的作業。

選取 [GET Resource] 作業，然後按一下 [開啟主控台]。

![Open console][Open console]

保留預設的參數值，並從 **subscription-key** 下拉式清單中選取您的訂閱金鑰。

輸入 **ocp-apim-trace:true** 至 [要求標頭] 文字方塊中，然後按一下 [HTTP Get]。

![HTTP Get][HTTP Get]

在回應標頭中，將會有一個 **ocp-apim-trace-location**，且值類似下列範例。

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

可從指定的位置下載追蹤來檢閱，如下一個步驟所示。

## <a name="inspect-trace"> </a>檢查追蹤

若要檢閱追蹤裡的值，請從 **ocp-apim-trace-location** URL 下載追蹤檔案。它是一個 JSON 格式的文字檔，且包含類似下列範例的項目。

    {
      "validityGuid":"a43a07a03de04fcb8b1425df38514742",
      "logEntries":[
        {
            "timestamp":"2014-05-03T21:00:13.2182473Z",
            "source":"Microsoft.WindowsAzure.ApiManagement.Proxy.Gateway.Handlers.DebugLoggingHandler",
            "data":{
            "originalRequest":{
                "method":"GET",
                "url":"https://contosoltd.current.int-azure-api.net/echo/resource?param1=sample&subscription-key=...",
                "headers":[
                {
                    "name":"ocp-apim-tracing",
                    "value":"true"
                },
                {
                    "name":"Host",
                    "value":"contosoltd.current.int-azure-api.net"
                }
                ]
            }
            }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"request handler",
          "data":{
            "configuration":{
              "api":{
                "from":"echo",
                "to":"http://echoapi.cloudapp.net/api"
              },
              "operation":{
                "method":"GET",
                "uriTemplate":"/resource"
              },
              "user":{
                "id":1,
                "groups":[
              
                ]
              },
              "product":{
                "id":1
              }
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "message":"Sending request to the service.",
            "request":{
              "method":"GET",
              "url":"http://echoapi.cloudapp.net/api/resource?param1=sample&subscription-key=...",
              "headers":[
                {
                  "name":"X-Forwarded-For",
                  "value":"138.91.78.77"
                },
                {
                  "name":"ocp-apim-tracing",
                  "value":"true"
                }
              ]
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "status":{
              "code":200,
              "reason":"OK"
            },
            "headers":[
              {
                "name":"Pragma",
                "value":"no-cache"
              },
              {
                "name":"Host",
                "value":"echoapi.cloudapp.net"
              },
              {
                "name":"X-Forwarded-For",
                "value":"138.91.78.77"
              },
              {
                "name":"ocp-apim-tracing",
                "value":"true"
              },
              {
                "name":"Content-Length",
                "value":"0"
              },
              {
                "name":"Cache-Control",
                "value":"no-cache"
              },
              {
                "name":"Expires",
                "value":"-1"
              },
              {
                "name":"Server",
                "value":"Microsoft-IIS/8.0"
              },
              {
                "name":"X-AspNet-Version",
                "value":"4.0.30319"
              },
              {
                "name":"X-Powered-By",
                "value":"Azure API Management - http://api.azure.com/,ASP.NET"
              },
              {
                "name":"Date",
                "value":"Sat, 03 May 2014 21:00:17 GMT"
              }
            ]
          }
        }
      ]
    }

## <a name="next-steps"> </a>後續步驟

-   在[開始使用進階 API 組態][開始使用進階 API 組態]教學課程中查看其他主題。

  [使用 API 偵測器來追蹤呼叫]: #trace-call
  [檢查追蹤]: #inspect-trace
  [後續步驟]: #next-steps
  [開始使用 Azure API 管理]: ../api-management-get-started
  [建立 API 管理服務執行個體]: ../api-management-get-started/#create-service-instance
  [API Management developer portal]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
  [Echo API]: ./media/api-management-howto-api-inspector/api-management-echo-api.png
  [Open console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
  [HTTP Get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
  [開始使用進階 API 組態]: ../api-management-get-started-advanced
