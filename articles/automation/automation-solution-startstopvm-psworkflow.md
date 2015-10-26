<properties 
	pageTitle="使用 Azure 自動化啟動和停止虛擬機器 - PowerShell 工作流程 | Microsoft Azure"
	description="Azure 自動化解決方案的圖形化版本，包含可啟動和停止傳統虛擬機器的 Runbook。"
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/25/2015"
	ms.author="bwren" />

# Azure 自動化解決方案 - 啟動和停止虛擬機器

此 Azure 自動化解決方案包含可啟動和停止傳統虛擬機器的 Runbook。此解決方案可作為下列任何用途：

- 在您自己的環境中不修改而直接使用 Runbook。 
- 修改 Runbook 來執行自訂的功能。  
- 在整個解決方案中從另一個 Runbook 呼叫 Runbook。 
- 將 Runbook 當作教學課程來了解 Runbook 撰寫概念。 

> [AZURE.SELECTOR]
- [Graphical](automation-solution-startstopvm-graphical.md)
- [PowerShell Workflow](automation-solution-startstopvm-psworkflow.md)

這是此解決方案的 PowerShell 工作流程 Runbook 版本。也可以使用[圖形化 Runbook](automation-solutions-startstopvm-graphical.md) 取得。

## 取得解決方案

此解決方案包含兩個可以從下列連結下載的 PowerShell 工作流程 Runbook。請參閱這個解決方案的[圖形化版本](automation-solutions-startstopvm-graphical.md)，以取得圖形化 Runbook 的連結。

| Runbook | 連結 | 類型 | 說明 |
|:---|:---|:---|:---|
| Start-AzureVMs | [啟動 Azure 傳統 VM](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | PowerShell 工作流程 | 啟動 Azure 訂用帳戶中的所有傳統虛擬機器，或具有特定服務名稱的所有虛擬機器。 |
| Stop-AzureVMs | [停止 Azure 傳統 VM](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | PowerShell 工作流程 | 停止自動化帳戶中的所有虛擬機器，或具有特定服務名稱的所有虛擬機器。 |


## 安裝解決方案

### 1\.安裝 Runbook

下載 Runbook 之後，您可以使用[匯入 Runbook](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook) 中的程序匯入它們。

### 2\.檢閱描述和需求
Runbook 包含註解說明文字，其中含有描述和所需的資產。您也可以從這份文件取得相同的資訊。

### 3\.設定資產
Runbook 需要下列資產，您必須建立這些資產並填入適當的值。

| 資產類型 | 資產名稱 | 說明 |
|:---|:---|:---|:---|
| 認證 | AzureCredential | 包含帳戶的認證，此帳戶有權啟動和停止 Azure 訂用帳戶中的虛擬機器。或者，您也可以在 **Add-AzureAccount** 活動的 **Credential** 參數中指定其他認證資產。 |
| 變數 | AzureSubscriptionId | 包含 Azure 訂用帳戶的訂用帳戶識別碼。 |

## 使用解決方案

### 參數

每個 Runbook 都有下列參數。您必須提供任何必要參數的值，另外可根據您的需求，選擇性地提供其他參數的值。

| 參數 | 類型 | 強制 | 說明 |
|:---|:---|:---|:---|
| ServiceName | 字串 | 否 | 如果提供一個值，則會啟動或停止具有該服務名稱的所有虛擬機器。如果不提供任何值，則會啟動或停止 Azure 訂用帳戶中的所有傳統虛擬機器。 |
| AzureSubscriptionIdAssetName | 字串 | 否 | 包含[變數資產](#installing-the-solution)的名稱，此資產含有 Azure 訂用帳戶的訂用帳戶識別碼。如果不指定任何值，則會使用 *AzureSubscriptionId*。 |
| AzureCredentialAssetName | 字串 | 否 | 包含[認證資產](#installing-the-solution)的名稱，此資產含有要使用的 Runbook 認證。如果不指定任何值，則會使用 *AzureCredential*。 |

### 啟動 Runbook

您可以使用[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md) 中的任何方法，啟動此解決方案中的任一個 Runbook。

下列範例命令使用 Windows PowerShell 執行 **StartAzureVMs**，以啟動服務名稱為 *MyVMService* 的所有虛擬機器。

	$params = @{"ServiceName"="MyVMService"}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### 輸出

Runbook 將為每部虛擬機器[輸出訊息](automation-runbook-output-and-messages.md)，指出是否已成功提交啟動或停止指令。您可以在輸出中尋找特定的字串，以判斷每一個 Runbook 的結果。下表列出可能的輸出字串。

| Runbook | 條件 | 訊息 |
|:---|:---|:---|
| Start-AzureVMs | 虛擬機器已在執行中 | MyVM 已在執行中 |
| Start-AzureVMs | 成功提交虛擬機器的啟動要求 | MyVM 已啟動 |
| Start-AzureVMs | 虛擬機器的啟動要求失敗 | MyVM 無法啟動 |
| Stop-AzureVMs | 虛擬機器已在執行中 | MyVM 已停止 |
| Stop-AzureVMs | 成功提交虛擬機器的啟動要求 | MyVM 已啟動 |
| Stop-AzureVMs | 虛擬機器的啟動要求失敗 | MyVM 無法啟動 |

例如，Runbook 的下列程式碼片段會嘗試啟動服務名稱為 *MyServiceName* 的所有虛擬機器。如果有任何啟動要求失敗，則可以採取錯誤動作。

	$results = Start-AzureVMs -ServiceName "MyServiceName"
	foreach ($result in $results) {
		if ($result -like "* has been started" ) {
			# Action to take in case of success.
		}
		else {
			# Action to take in case of error.
		}
	}


## 詳細分解圖

以下是此解決方案中所有 Runbook 的詳細分解圖。您可以使用這項資訊來自訂 Runbook，或只是從中學習。

### 參數

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

工作流程首先取得[輸入參數](#using-the-solution)的值。如果沒有提供資產名稱，則會使用預設名稱。

### 輸出

    # Returns strings with status messages
    [OutputType([String])]

下面這行宣告 Runbook 的輸出會是一個字串。這並不需要，但卻是使用 Runbook 作為[子 Runbook](automation-child-runbooks.md) 時的最佳作法，如此父 Runbook 就可知道預期的輸出類型。

### 驗證

	# Connect to Azure and select the subscription to work against
	$Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
	$null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
	$SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

下面幾行設定將用於 Runbook 其餘部分的[認證](automation-configuring.md#configuring-authentication-to-azure-resources)和 Azure 訂用帳戶。首先，我們使用 **Get-AutomationPSCredential** 取得資產，此資產中的認證有權啟動和停止 Azure 訂用帳戶中的虛擬機器。**Add-AzureAccount** 接著使用此資產來設定認證。輸出會指派給虛擬變數，所以不會併入 Runbook 輸出中。

然後，使用 **Get-AutomationVariable** 擷取含有訂用帳戶識別碼的變數資產，並使用 **Select-AzureSubscription** 設定訂用帳戶。

### 取得 VM

	# If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName) 
	{ 
		$VMs = Get-AzureVM -ServiceName $ServiceName
	}
    else 
	{ 
		$VMs = Get-AzureVM
	}

**Get-AzureVM** 用來擷取 Runbook 將使用的虛擬機器。如果在 **ServiceName** 輸入變數中提供一個值，則只會擷取具有該服務名稱的虛擬機器。如果 **ServiceName** 是空的，則會擷取所有虛擬機器。

### 啟動/停止虛擬機器和傳送輸出

    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
		if ($VM.PowerState -eq "Started")
		{
			# The VM is already started, so send notice
			Write-Output ($VM.InstanceName + " is already running")
		}
		else
		{
			# The VM needs to be started
        	$StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

	        if ($StartRtn.OperationStatus -ne 'Succeeded')
	        {
				# The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
	        }
			else
			{
				# The VM started, so send notice
				Write-Output ($VM.InstanceName + " has been started")
			}
		}
    }

接下來的幾行逐步執行每個虛擬機器。首先，檢查虛擬機器的 **PowerState**，查看它是否在執行中或已停止，視 Runbook 而定。如果已處於目標狀態，則將訊息傳送至輸出，且 Runbook 會結束。如果不是，則使用 **Start-AzureVM** 或 **Stop-AzureVM** 嘗試啟動或停止虛擬機器，並將要求的結果儲存至變數。然後，將訊息傳送至輸出，指出是否已成功提交啟動或停止的要求。


## 相關文章

- [Azure 自動化中的子 Runbook](automation-child-runbooks.md) 
- [Azure 自動化中的 Runbook 輸出與訊息](automation-runbook-output-and-messages.md)

<!---HONumber=Oct15_HO3-->