<properties
	pageTitle="Azure Insights：使用自動調整動作傳送電子郵件和 Webhook 警示通知 | Microsoft Azure"
	description="了解如何在 Azure Insights 中使用自動調整動作呼叫 Web URL 或傳送電子郵件通知。"
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# 使用自動調整動作在 Azure Insights 中傳送電子郵件和 Webhook 警示通知

本文將告訴您如何設定觸發程序，讓您可以根據 Azure 中的自動調整動作呼叫特定的 Web URl 或傳送電子郵件。

## Webhook
Webhook 可讓您將 Azure 警示通知路由到其他系統進行後處理或自訂通知。例如，將警示路由到可處理傳入 Web 要求的服務，以傳送 SMS、記錄錯誤、使用聊天或傳訊服務通知團隊等等。Webhook URI 必須是有效的 HTTP 或 HTTPS 端點。

## 電子郵件
電子郵件可以傳送至任何有效的電子郵件地址。也會通知執行該規則的訂用帳戶的系統管理員和共同管理員。


## 雲端服務和 Web Apps
您可以在 Azure 的雲端服務和伺服器陣列 (Web Apps) 入口網站中選擇加入。

- 選擇做為 [調整依據] 的度量。

![調整依據](./media/insights-autoscale-to-webhook-email/insights-autoscale-scale-by.png)

## 虛擬機器擴展集
若是較新的 ARM 型虛擬機器 (虛擬機器擴展集)，您可以使用 REST API、PowerShell 和 CLI 來設定它。目前尚無入口網站介面。


## Webhook 中的驗證
有兩種驗證 URI 形式︰

	1. Token-base authentication, where you save the webhook URI with a token ID as a query parameter. For example, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
	2. Basic authentication, where you use a user ID and password. For example, https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## 自動調整通知 Webhook 承載結構描述
產生自動調整通知時，Webhook 承載會包含下列中繼資料︰

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


|欄位 |是否為強制？|	說明|
|---|---|---|
|status |yes |此狀態表示產生了自動調整動作|
|operation|	yes |若執行個體增加，它會「相應放大」，若執行個體減少，它會「相應縮小」。|
|context|	yes |自動調整動作內容|
|timestamp|	yes |自動調整動作觸發時的時間戳記|
|id |是|	自動調整設定的 ARM (Azure Resource Manager) 識別碼|
|名稱 |是|	自動調整設定的名稱|
|詳細資料|	是 |說明自動調整服務所採取的動作和執行個體計數的變更|
|subscriptionId|	是 |正在調整的目標資源的訂用帳戶識別碼|
|resourceGroupName|	是|	正在調整的目標資源的資源群組|
|resourceName |是|	正在調整的目標資源的名稱|
|resourceType |是|	支援三個值：microsoft.classiccompute/domainnames/slots/roles" (雲端服務角色)、"microsoft.compute/virtualmachinescalesets" (虛擬機器擴展集) 以及 "Microsoft.Web/serverfarms" - (Web 應用程式)|
|resourceId |是|正在調整的目標資源的 ARM 識別碼|
|portalLink |是 |連到目標資源摘要頁面的 Azure 入口網站連結|
|oldCapacity|	是 |自動調整進行調整動作時的當前 (舊) 執行個體計數|
|newCapacity|	是 |自動調整要將資源調整為此數目的新執行個體計數|
|屬性|	否|	選用。<Key  Value> 對組 (例如 Dictionary <String  String>)。properties 欄位是選擇性的。在自訂 UI 或邏輯應用程式的工作流程中，您可以輸入可使用承載傳遞的索引鍵和值。另一個將自訂屬性傳回給連出 Webhook 呼叫的替代做法，是使用 Webhook URI 本身 (做為查詢參數)|

<!---HONumber=AcomDC_0330_2016-->