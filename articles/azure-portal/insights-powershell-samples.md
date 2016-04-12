<properties
	pageTitle="Azure Insights：Azure Insights PowerShell 快速入門範例。| Microsoft Azure"
	description="Azure Insights 快速入門範例中的 PowerShell 命令可協助您快速存取 Azure Insights 監視功能。"
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

# Azure Insights PowerShell 快速入門範例

本文顯示 PowerShell 命令範例來協助您快速存取 Azure Insights 監視功能。Azure Insights 可讓您根據設定的遙測資料值，自動調整雲端服務、虛擬機器和 Web Apps，以及傳送警示通知，或呼叫 Web URL。

## 設定 PowerShell
設定要在電腦上執行的 PowerShell (如果您還未設定)。如需詳細資訊，請參閱[如何安裝和設定 PowerShell](../powershell-install-configure.md)。

## 本文中的範例

本文中的範例將說明如何使用 Azure Insights Cmdlet。您也可以在 [Azure Insights Cmdlet](https://msdn.microsoft.com/library/mt282452.aspx) 檢閱整個 Azure Insights (監視) Cmdlet 清單。


## 登入和使用訂用帳戶

首先，登入您的 Azure 訂用帳戶。

```
Login-AzureRmAccount
```

這會要求您登入。一旦登入之後，就會顯示您的帳戶、TenantId 和預設的訂用帳戶識別碼。所有 Azure Cmdlet 都會在您的預設訂用帳戶內容中運作。若要檢視您具有存取權的訂用帳戶的清單，請使用下列命令。

```
Get-AzureRmSubscription
```

若要將使用中的內容變更為不同的訂用帳戶，請使用下列命令。

```
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## 擷取訂用帳戶的稽核記錄檔
使用 `Get-AzureRmLog` Cmdlet。以下是一些常見的範例。

取得此時間/日期迄今的記錄檔項目︰

```
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

取得某個時間/日期範圍間的記錄檔項目︰

```
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

取得特定資源群組中的記錄檔項目︰

```
Get-AzureRmLog -ResourceGroup 'myrg1'
```

從特定的資源提供者取得某個時間/日期範圍間的記錄檔項目︰

```
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

取得包含特定呼叫者的所有記錄檔項目︰

```
Get-AzureRmLog -Caller 'myname@company.com'
```

下列命令會擷取稽核記錄檔中的最後 1000 個事件︰

```
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` 支援其他許多參數。如需詳細資訊，請參閱 `Get-AzureRmLog` 參考。

>[AZURE.NOTE] `Get-AzureRmLog` 只提供 15 天的歷程記錄。使用 **-MaxEvents** 參數可讓您查詢超過 15 天的前 N 個事件。若要存取 15 天前的事件，請使用 REST API 或 SDK (使用 SDK 的 C# 範例)。如果您未包含 **StartTime**，則預設值是 **EndTime** 減去一小時。如果您未包含 **EndTime**，則預設值是目前的時間。所有時間都是採用 UTC 格式。

## 擷取警示歷程記錄
若要檢視所有警示事件，您可以使用下列範例查詢 Azure Resource Manager (ARM) 記錄檔。

```
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

若要檢視特定警示規則的歷程記錄，您可以使用 `Get-AzureRmAlertHistory` Cmdlet，傳入警示規則的資源識別碼。

```
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

`Get-AzureRmAlertHistory` Cmdlet 支援多各種參數。如需詳細資訊，請參閱 [Get AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx)。


## 擷取警示規則的相關資訊
下列所有命令都會處理一個名為 "montest" 的資源群組。

檢視警示規則的所有屬性︰

```
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

擷取資源群組上的所有警示︰

```
Get-AzureRmAlertRule -ResourceGroup montest
```

擷取針對目標資源設定的所有警示規則。例如，在 VM 上設定的所有警示規則。

```
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` 支援其他參數。如需詳細資訊，請參閱 [Get AlertRule](https://msdn.microsoft.com/library/mt282459.aspx)。

## 建立警示規則
您可以使用 `Add-AlertRule` Cmdlet 建立、更新或停用警示規則。

您可以分別使用 `New-AzureRmAlertRuleEmail` 和 `New-AzureRmAlertRuleWebhook` 建立電子郵件和 Webhook 屬性。在警示規則 Cmdlet 中，將這些屬性當做動作，指派給警示規則的 [動作] 屬性。

下節所包含的範例會為您示範如何使用各種參數建立警示規則。

### 計量的警示規則
下表描述使用計量建立警示所使用的參數和值。


|參數|value|
|---|---|
|名稱|	simpletestdiskwrite|
|此警示規則的位置|	美國東部|
|ResourceGroup|	montest|
|TargetResourceId|	/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig|
|所建立警示的 MetricName|	\\PhysicalDisk(\_Total)\\Disk Writes/sec。請參閱以下的 `Get-MetricDefinitions` Cmdlet，了解如何擷取確實的計量名稱| |運算子| GreaterThan| |臨界值 (此計量每秒的計數)| 1| |WindowSize (hh:mm:ss 格式)| 00:05:00| |彙總器 (計量的統計資料，在此範例中使用的是 Average 計數)| Average| |自訂電子郵件 (字串陣列)|'foo@example.com','bar@example.com'| |將電子郵件傳送給擁有者、參與者和讀取者| -SendToServiceOwners|

建立電子郵件動作

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

建立 Webhook 動作

```
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

在傳統 VM 上建立 CPU 百分比計量的警示規則

```
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

擷取警示規則

```
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

如果指定屬性的警示規則已存在，Add alert Cmdlet 也會更新規則。若要停用警示規則，請加上參數 **-DisableRule**。

### 稽核記錄檔事件的警示

>[AZURE.NOTE] 此功能仍然處於預覽狀態。

在此案例中，當我的訂用帳戶中的網站在資源群組 *abhingrgtest123* 中成功啟動時，您將會傳送電子郵件。

設定電子郵件規則

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

設定 Webhook 規則

```
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

建立事件的規則

```
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

擷取警示規則

```
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

`Add-AlertRule` Cmdlet 允許使用其他多種參數。如需詳細資訊，請參閱 [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx)。

## 取得可用警示計量的清單
您可以使用 `Get-AzureRmMetricDefinition` Cmdlet 檢視特定資源的所有計量的清單。

```
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

下列範例會產生包含計量名稱及其單位的資料表。

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

`Get-AzureRmMetricDefinition` 的可用選項完整清單可在 [Get MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx) 取得。

若要檢視各種 Azure 服務的可用計量及其單位的清單，請參閱 [WEB PAGE TITLE HERE](http://link)。

## 建立和管理自動調整設定
諸如 Web app、VM、雲端服務或 VM 調整集之類的資源只能設定一個自動調整設定。不過，每個自動調整設定都可以有多個設定檔。例如，一個用於以效能為基礎的調整設定檔，以及一個用於以排程為基礎的設定檔。每個設定檔都可以設定多個規則。如需有關自動調整的詳細資訊，請參閱[如何自動調整應用程式](../cloud-services/cloud-services-how-to-scale.md)。

以下是我們將使用的步驟：

1. 建立規則。
2. 建立將您先前所建立的規則對應到設定檔的設定檔。
3. 選擇性︰設定 Webhook 和電子郵件的屬性，以建立自動調整的通知。
4. 藉由對應您在先前步驟中建立的設定檔和通知，使用目標資源上的名稱建立自動調整設定。

下列範例將示範如何使用 CPU 使用率計量，為 Windows 作業系統的 VM 調整集建立自動調整設定。

首先，建立相應放大的規則，讓執行個體計數增加。

```
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```		

接著，建立相應縮小的規則，讓執行個體計數減少。

```
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

然後，建立規則的設定檔。

```
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

建立 Webhook 屬性。

```
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

建立自動調整設定的通知屬性，包括您先前建立的電子郵件與 Webhook。

```
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

最後，建立自動調整設定來新增您在以上建立的設定檔。

```
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

如需有關管理自動調整設定的詳細資訊，請參閱 [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx)。

## 自動調整歷程記錄
下列範例示範如何檢視最新的自動調整和警示的事件。使用稽核記錄檔搜尋檢視自動調整歷程記錄。

```
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

您可以使用 `Get-AzureRmAutoScaleHistory` Cmdlet 擷取自動調整歷程記錄。

```
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

如需詳細資訊，請參閱 [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx)。

### 檢視自動調整設定的詳細資料
您可以使用 `Get-Autoscalesetting` Cmdlet 擷取有關自動調整設定的詳細資訊。

下列範例會顯示資源群組 'myrg1' 中所有自動調整設定的詳細資料。

```
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

下列範例會顯示資源群組 'myrg1' 中所有自動調整設定的詳細資料，尤其是名為 'MyScaleVMSSSetting' 的自動調整設定。

```
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### 移除自動調整設定
您可以使用 `Remove-Autoscalesetting` Cmdlet 刪除自動調整設定。

```
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## 管理稽核記錄檔的記錄檔設定檔

您可以建立*記錄檔設定檔*，並將資料從稽核記錄檔匯出至儲存體帳戶，而且您可以為其設定資料保留期。或者，您也可以將資料串流至事件中樞。請注意，此功能目前處於預覽狀態，而且每個訂用帳戶只能建立一個記錄檔設定檔。您可以使用下列 Cmdlet 搭配您目前的訂用帳戶來建立和管理記錄檔設定檔。您也可以選擇特定的訂用帳戶。PowerShell 預設為目前的訂用帳戶，但是您隨時可以使用 `Set-AzureRmContext` 加以變更。您可以設定稽核記錄檔，將資料路由至任何儲存體帳戶或該訂用帳戶內的事件中樞。資料會以 JSON 格式的 Blob 檔案寫入。

### 取得記錄檔設定檔
若要擷取現有的記錄檔設定檔，請使用 `Get-AzureRmLogProfile` Cmdlet。

### 新增沒有資料保留期的記錄檔設定檔

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### 移除記錄檔設定檔

```
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### 新增包含資料保留期的記錄檔設定檔

您可以使用保留資料的天數，以正整數指定 **-RetentionInDays** 屬性。

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### 新增包含保留期與 EventHub 的記錄檔設定檔
除了將資料路由至儲存體帳戶之外，您也可以將它串流到事件中樞。請注意，在此預覽版本中，儲存體帳戶設定是強制性的，但事件中樞設定則是選擇性的。

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## 設定診斷記錄檔
許多 Azure 服務都會提供額外的記錄檔和遙測，包括 Azure 網路安全性群組、軟體負載平衡器、金鑰保存庫、Azure 搜尋服務，以及 Logic Apps，而且它們可以設定為將資料儲存在 Azure 儲存體帳戶中。該作業只能在資源層級執行，而且儲存體帳戶應該存在於進行診斷設定所在目標資源的相同區域中。

### 取得診斷設定

```
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

停用診斷設定

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

啟用不含保留期的診斷設定

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

啟用含保留期的診斷設定

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

針對特定的記錄檔類別，啟用含保留期的診斷設定

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

<!---HONumber=AcomDC_0330_2016-->