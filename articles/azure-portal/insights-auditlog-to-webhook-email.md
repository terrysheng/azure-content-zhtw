<properties
	pageTitle="Azure Insights︰使用稽核記錄，在 Azure Insights 中傳送電子郵件和 webhook 警示通知。| Microsoft Azure"
	description="了解如何使用服務 auditlog 項目來呼叫 Web URL 或在 Azure Insights 中傳送電子郵件通知。"
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

# 使用稽核記錄，在 Azure Insights 中傳送電子郵件和 webhook 警示通知

這篇文章會顯示稽核記錄檔事件觸發 webhook 時的內容結構描述，並且說明如何使用相同的事件傳送電子郵件。

>[AZURE.NOTE] 這項功能目前為預覽版本。未來幾個月將會改善事件警示基礎結構和效能。在此預覽中，這項功能只能使用 Azure PowerShell 和 CLI 存取。稍後才能使用 Azure 入口網站存取相同功能。

## Webhook
Webhook 可讓您將 Azure 警示通知路由到其他系統進行後處理或自訂通知。例如，將警示路由到可處理傳入 Web 要求的服務，以傳送 SMS、記錄錯誤，或是使用聊天或傳訊服務通知某人等等。Webhook URI 必須是有效的 HTTP 或 HTTPS 端點。

## 電子郵件
電子郵件可以傳送至任何有效的電子郵件地址。也會通知執行該規則的訂用帳戶的系統管理員和共同管理員。

### 範例電子郵件規則
您必須設定電子郵件規則，webhook 規則，然後告知規則在稽核記錄檔事件發生時啟動。您可以在 [Azure Insights PowerShell 快速入門範例](insights-powershell-samples.md#alert-on-audit-log-event)中看到使用 PowerShell 的範例。


## 驗證
有兩種驗證 URI 形式︰

1. 權杖型驗證，方法是儲存 webhook URI 和權杖識別碼做為查詢參數。例如，https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. 基本驗證，方法是使用使用者識別碼和密碼。例如，https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## 稽核記錄檔事件通知 webhook 內容結構描述
可以使用新的事件時，稽核記錄檔事件的警示會執行已設定的 webhook，在 webhook 內容中具有事件中繼資料。下列範例顯示 webhook 內容結構描述：

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|元素名稱|	說明|
|---|---|
|status |一律設為「啟動」|
|context|事件的內容|
|resourceProviderName|受影響資源的資源提供者|
|conditionType |「事件」|
|名稱 |警示規則的名稱|
|id |警示的資源識別碼|
|說明|	警示建立者在警示上設定的描述|
|subscriptionId |Azure 訂用帳戶 GUID|
|timestamp|	Azure 服務產生事件的時間戳記，處理與事件對應的要求|
|resourceId |可唯一識別該資源的資源識別碼 URI|
|resourceGroupName|受影響資源的 resource-group-name|
|屬性 |一組包含事件相關詳細資料的 <Key  Value> 配對 (也就是字典<String  String>)|
|事件|元素，包含事件相關中繼資料|
|authorization|擷取事件的 RBAC 屬性。這些屬性通常包括「動作」、「角色」和「範圍」。|
|category | 事件的類別。支援值包括︰管理、警示、安全性、ServiceHealth、建議|
|呼叫者|已執行作業的使用者的電子郵件地址，根據可用性的 UPN 宣告或 SPN 宣告。特定系統呼叫可為 Null。|
|correlationId|	通常是字串格式的 GUID。具有屬於同一個較大動作的 correlationId 的事件，通常會共用相同的 correlationId。|
|eventDescription |描述事件的靜態文字|
|eventDataId|事件的唯一識別碼|
|eventSource |產生事件的 Azure 服務或基礎結構的名稱|
|httpRequest|	通常包括 “clientRequestId”、“clientIpAddress” 和 “method” (HTTP 方法，例如 PUT)。|
|層級|下列其中一個值：重大、錯誤、警告、資訊和詳細資訊|
|operationId|通常是對應至單一作業的事件之間共用的 GUID|
|operationName|作業的名稱|
|屬性 |事件元素內的元素包含事件的屬性。|
|status|字串，描述作業的狀態。常見的值包括︰已啟動、進行中、成功、失敗、使用中、已解決|
|子狀態|	通常包含對應 REST 呼叫的 HTTP 狀態碼。它也可以包含其他描述子狀態的字串。常見子狀態的值包括：確定 (HTTP 狀態碼︰200)，已建立 (HTTP 狀態碼︰201)、接受 (HTTP 狀態碼︰202)、沒有內容 (HTTP 狀態碼︰204)、不正確的要求 (HTTP 狀態碼︰400)、找不到 (HTTP 狀態碼︰404)，衝突 (HTTP 狀態碼︰409)、內部伺服器錯誤 (HTTP 狀態碼︰500)、服務無法使用 (HTTP 狀態碼︰503)、閘道逾時 (HTTP 狀態碼︰504)|

<!---HONumber=AcomDC_0330_2016-->