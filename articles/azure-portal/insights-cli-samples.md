<properties
	pageTitle="Azure Insights：Azure Insights CLI 快速入門範例 | Microsoft Azure"
	description="範例 CLI 命令可協助您存取 Azure Insights 監視功能。Azure Insights 是一項 Microsoft Azure 服務，可讓您根據設定的遙測資料值，自動調整雲端服務、虛擬機器和 Web Apps，傳送警示通知，或呼叫 Web URL。"
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

# Azure Insights 跨平台 CLI 快速入門範例

本文提供可協助您存取 Azure Insights 監視功能的命令列 (CLI) 命令範例。Azure Insights 可讓您根據設定的遙測資料值，自動調整雲端服務、虛擬機器和 Web Apps，以及傳送警示通知，或呼叫 Web URL。

## 必要條件

如果您尚未安裝 Azure CLI，請參閱[安裝 Azure CLI](../xplat-cli-install.md)。若您不熟悉 Azure CLI，可閱讀[搭配使用 Mac、Linux 和 Windows 適用的 Azure CLI 與 Azure Resource Manager](../xplat-cli-azure-resource-manager.md) 以深入了解。


在 Windows 中，從[Node.js 網站](https://nodejs.org/) 安裝 npm。以「以系統管理員身分執行」權限使用 CMD.exe 完成安裝後，從安裝 npm 的資料夾中執行下列命令︰

```
npm install azure-cli --global
```

接下來，瀏覽至您想要的資料夾/位置，在命令列輸入︰

```
azure help
```

## 登入 Azure

第一步是登入您的 Azure 帳戶。

```
azure login
```

這會要求您登入。之後，您會看到您的帳戶、TenantId 和預設的訂用帳戶識別碼。所有命令都可在您的預設訂用帳戶內容中運作。

若要列出目前訂用帳戶的詳細資料，使用下列命令。

```
azure account show
```

若要將使用中的內容變更為其他訂用帳戶，請使用下列命令。

```
azure account set "subscription ID or subscription name"
```

若要使用 Azure Resource Manager 和 Azure Insights 命令，必須處於 ARM 模式。

```
azure config mode arm
```

若要檢視所有支援的 Azure Insights 命令清單，執行下列命令。

```
azure insights
```

## 檢視訂用帳戶的稽核記錄檔

若要檢視稽核記錄檔的清單，執行下列命令。

```
azure insights logs list [options]
```

嘗試下列命令以檢視所有可用的選項。

```
azure insights logs list -help
```

以下是依 resourceGroup 列出記錄檔清單的範例

```
azure insights logs list --resourceGroup "myrg1"
```

依 caller 列出記錄檔的範例

```
azure insights logs list --caller "myname@company.com"
```

依 caller 列出開始與結束日期之間在資源類型上的記錄檔的範例

```
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## 使用警示
您可以在此區段中運用資訊來使用警示。

### 取得資源群組中的警示規則

```
node bin\azure insights alerts rule list abhingrgtest123
node bin\azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### 建立度量警示規則

```
node bin\azure insights alerts actions email create --customEmails foo@microsoft.com
node bin\azure insights alerts actions webhook create https://someuri.com
node bin\azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### 建立記錄檔警示規則

```
insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### 建立 WebTest 警示規則

```
node bin\azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### 刪除警示規則

```
node bin\azure insights alerts rule delete abhingrgtest123 andy0323
```

## 記錄檔的設定檔
在此區段中運用資訊來使用記錄檔的設定檔。

### 取得記錄檔設定檔

```
node bin\azure insights logprofile list
node bin\azure insights logprofile get -n default
```


### 新增沒有保留期的記錄檔設定檔

```
node bin\azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### 移除記錄檔設定檔

```
node bin\azure insights logprofile delete --name default
```

### 新增有保留期的記錄檔設定檔

```
node bin\azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### 新增有保留期與 EventHub 的記錄檔設定檔

```
node bin\azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## 診斷
在此區段中運用資訊來使用診斷設定。

### 取得診斷設定

```
node bin\azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### 停用診斷設定

```
node bin\azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### 啟用沒有保留期的診斷設定

```
node bin\azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## Autoscale
在此區段中運用資訊來使用自動調整設定。您必須修改這些範例。

### 取得資源群組的自動調整設定

```
node bin\azure insights autoscale setting list montest2
```

### 依名稱取得資源群組中的自動調整設定

```
node bin\azure insights autoscale setting list montest2 -n setting2
```


### 設定自動調整設定

```
node bin\azure insights autoscale setting set montest2 -n setting2 --settingSpec
```

<!---HONumber=AcomDC_0330_2016-->