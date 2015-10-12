<properties
	pageTitle="如何設定 Azure 警示以傳送至其他系統"
	description="重設 Azure 警示的路徑到其他非 Azure 系統。"
	authors="rboucher"
	manager="ronmart"
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/25/2015"
	ms.author="robb"/>

# 如何設定警示的 Webhook

Webhook 允許使用者將 Azure 警示通知路由到其他系統進行後處理或自訂通知。這些範例可以是將警示路由到可處理傳入 Web 要求的服務，以傳送 SMS、記錄錯誤、透過聊天/傳訊服務通知小組等等。

Webhook URI 必須是有效的 HTTP 或 HTTPS 端點。Azure 警示服務會在指定的 Webhook 進行 POST 作業，傳遞特定的 JSON 承載和結構描述。

## 透過入口網站設定 Webhook

您可以在 [Azure 入口網站](https://portal.azure.com/)的 [建立/更新警示] 畫面中，新增或更新 Webhook URI。

![新增警示規則](./media/insights-webhooks-alerts/Alertwebhook.png)


## 驗證

驗證可以有兩種類型：

1. **權杖驗證** - 此種驗證是指您將使用權杖識別碼 (例如 **https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue*) 儲存 Webhook URI
2.	**基本驗證** - 使用使用者識別碼和密碼：此種驗證是指您會儲存 Webhook URI 為 **https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar*

## 承載結構描述

POST 作業對於所有以計量為基礎的警示將會包含下列 JSON 承載和結構描述。

```
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": “https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1”                                
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```

>[AZURE.NOTE]我們下次更新時，將增加事件 (“conditionType” : “Event”) 警示的支援


| 欄位 | 是否為強制？ | 是否有固定的值？ | 注意事項 |
| :-------------| :-------------   | :-------------   | :-------------   |
|status|Y|“Activated”、“Resolved”|這是您了解警示種類的方式。Azure 會針對某人設定的條件自動傳送已啟動和已解決的警示。|
|context| Y | | 警示內容|
|timestamp| Y | | 警示觸發的時間。從診斷儲存體一讀取到計量，警示就會立即觸發。|
|id | Y | | 每個警示規則都有唯一的識別碼。|
|名稱|Y | |
|說明 |Y | |有關警示的說明。|
|conditionType |Y |“Metric”、“Event” |支援兩種類型的警示。一個根據計量，一個根據事件。我們未來會支援事件的警示，因此使用此值可檢查警示是根據計量或事件|
|condition |Y | |這會根據 conditionType 檢查特定欄位|
|metricName |用於計量警示 | |定義規則所監視的計量名稱。|
|metricUnit |用於計量警示 |"Bytes"、"BytesPerSecond"、"Count"、"CountPerSecond"、"Percent"、"Seconds"|	 計量允許的單位。允許的值：https://msdn.microsoft.com/zh-TW/library/microsoft.azure.insights.models.unit.aspx|
|metricValue |用於計量警示 | |造成警示的計量實際值|
|threshold |用於計量警示 | |啟動警示的閾值|
|windowSize |用於計量警示 | |根據 threshold 用來監視警示活動的時間長度。必須介於 5 分鐘到 1 天之間。ISO 8601 持續時間格式。|
|timeAggregation |用於計量警示 |"Average"、"Last"、"Maximum"、"Minimum"、"None"、"Total" |	收集的資料應如何隨著時間結合。預設值為 Average。允許的值：https://msdn.microsoft.com/zh-TW/library/microsoft.azure.insights.models.aggregationtype.aspx|
|operator |用於計量警示 | |用來比較資料和閾值的運算子。|
|subscriptionId |Y | |Azure 訂用帳戶 GUID|
|resourceGroupName |Y | |受影響資源的 resource-group-name|
|resourceName |Y | |受影響資源的資源名稱|
|resourceType |Y | |受影響資源的資源類型|
|resourceId |Y | |可唯一識別該資源的資源識別碼 URI|
|resourceRegion |Y | |受影響資源的區域/位置|
|portalLink |Y | |連到資源摘要頁面的直接 Azure 入口網站連結|
|properties |N |選用 |是一組包含事件相關詳細資料的 <Key  Value> 配對 (也就是字典<String  String>)。properties 欄位是選擇性的。在自訂 UI 或邏輯應用程式的工作流程中，使用者可以輸入可透過承載傳遞的索引鍵/值。另一種將自訂屬性傳回給 Webhook 的替代方式是透過 Webhook URI 本身 (做為查詢參數)|


>[AZURE.NOTE]透過入口網站無法使用 properties 欄位。在我們即將推出的 Insights SDK 中，您可以透過警示 API 設定屬性。

## 後續步驟

如需額外的背景，請觀賞[使用 PagerDuty 整合 Azure 警示](http://go.microsoft.com/fwlink/?LinkId=627080)視訊，深入了解 Azure 警示和 Webhook

若要了解如何以程式設計方式建立 Webhook，請參閱[使用 Azure Insights SDK (C#) 建立含有 Webhook 的警示](https://code.msdn.microsoft.com/Create-Azure-Alerts-with-b938077a)。

設定了 Webhook 和警示之後，請探索這些其他選項來啟動自動化指令碼。

[執行 Azure 自動化指令碼 (Runbook)](http://go.microsoft.com/fwlink/?LinkId=627081)

使用 Azure 警示可傳送訊息至其他服務。請使用下列範例範本開始。

[使用邏輯應用程式透過 Twilio API 傳送 SMS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)

[使用邏輯應用程式傳送殘存訊息](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)

[使用邏輯應用程式傳送訊息至 Azure 佇列](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<!---HONumber=Oct15_HO1-->