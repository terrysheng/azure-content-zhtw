<properties
   pageTitle="開始使用 Azure Batch PowerShell Cmdlet | Microsoft Azure"
   description="介紹用來管理 Azure Batch 服務的 Azure PowerShell Cmdlet"
   services="batch"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="08/07/2015"
   ms.author="danlep"/>

# 開始使用 Azure Batch PowerShell Cmdlet
本文是 Azure PowerShell Cmdlet 的簡介，可用來管理 Batch 帳戶並取得 Batch 作業、工作及其他詳細資訊的相關資訊。

如需詳細的 Cmdlet 語法，請輸入 `get-help <Cmdlet_name>`，或請參閱 [Azure Batch Cmdlet 參考資料](https://msdn.microsoft.com/library/azure/mt125957.aspx)。

## 必要條件

* **Azure PowerShell** - 請參閱＜[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)＞以了解必要條件、下載和安裝指示。自 0.8.10 起及後續版本導入 Batch Cmdlet。上傳 Batch cmdlet 以在 0.9.6 版中使用一般可用性 API。

## 使用 Batch Cmdlet

使用標準程序啟動 Azure PowerShell 並[連接到 Azure 訂用帳戶](../powershell-install-configure.md#Connect)。此外：

* **選取 Azure 訂用帳戶** - 如果您有多個訂用帳戶，請選取一個訂用帳戶：

    ```
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

* **切換到 AzureResourceManage 模式** - Azure 資源管理員模組中隨附的 Batch Cmdlet。請參閱＜[將 Windows PowerShell 與資源管理員搭配使用](../powershell-azure-resource-manager.md)＞以獲取詳細資訊。若要使用此模組，請執行 [Switch-AzureMode](https://msdn.microsoft.com/library/dn722470.aspx) Cmdlet：

    ```
    Switch-AzureMode -Name AzureResourceManager
    ```

* **向 Batch 提供者命名空間註冊 (一次性操作)**：在您可以管理 Batch 帳戶之前，必須向 Batch 提供者命名空間註冊。每個訂用帳戶只需要執行這項作業一次。

    ```
    Register-AzureProvider -ProviderNamespace Microsoft.Batch
    ```

## 管理 Batch 帳戶和金鑰

您可以使用 Azure PowerShell Cmdlet 建立及管理 Batch 帳戶和金鑰。

### 建立 Batch 帳戶：

**New-AzureBatchAccount** 將在指定的資源群組中建立新的批次帳戶。如果您還沒有資源群組，請執行 [New-azureresourcegroup](https://msdn.microsoft.com/library/dn654594.aspx) Cmdlet 建立一個，並在**位置** 參數中指定其中一個 Azure 區域。(您也可以藉由執行 [Get-azurelocation](https://msdn.microsoft.com/library/dn654582.aspx) 尋找不同 Azure 資源可用的區域。) 例如：

```
New-AzureResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

然後，在資源群組中建立新的 Batch 帳戶，同時為 <*account\_name*> 指定帳戶名稱和 Batch 服務可用的位置。建立帳戶可能需要幾分鐘才能完成。例如：

```
New-AzureBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]Batch 帳戶名稱對 Azure 必須是唯一的，介於 3 到 24 個字元，並只能使用小寫字母和數字。

### 取得帳戶存取金鑰
**Get-AzureBatchAccountKeys** 將顯示與 Azure 批次帳戶相關聯的存取金鑰。例如，執行下列命令以取得您所建立帳戶的主要和次要金鑰。

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>
$Account.PrimaryAccountKey
$Account.SecondaryAccountKey
```

### 產生新的存取金鑰
**New-AzureBatchAccountKey** 將為 Azure 批次帳戶產生新的主要或次要帳戶金鑰。例如，若要為您的 Batch 帳戶產生新的主要金鑰，請輸入：

```
New-AzureBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]若要產生新的次要金鑰，請將 **KeyType** 參數指定為「次要」。您必須個別重新產生主要和次要金鑰。

### 刪除 Batch 帳戶
**Remove-AzureBatchAccount** 將刪除批次帳戶。例如：

```
Remove-AzureBatchAccount -AccountName <account_name>
```

出現提示時，請確認您想要移除的帳戶。帳戶移除可能需要一些時間來完成。

## 查詢作業、工作及其他詳細資料

使用 Cmdelt (例如 **Get-AzureBatchJob**、**Get-AzureBatchTask** 和 **Get-AzureBatchPool**) 查詢在 Batch 帳戶下建立的實體。

若要使用這些指令程式，您必須先建立一個 AzureBatchContext 物件以儲存您的帳戶名稱和金鑰：

```
$context = Get-AzureBatchAccountKeys "<account_name>"
```

使用 **BatchContext** 參數將此內容傳入與批次服務互動的 Cmdlet。

> [AZURE.NOTE]依預設，帳戶的主要金鑰用於驗證，但是您可以透過變更 BatchAccountContext 物件的 **KeyInUse** 屬性，明確地選取要使用的金鑰：`$context.KeyInUse = "Secondary"`。


### 查詢資料

例如，使用 **Get-AzureBatchPools** 尋找您的集區。依預設，這將查詢您帳戶下的所有集區，並假設您已經將 BatchAccountContext 物件儲存在 *$context* 中：

```
Get-AzureBatchPool -BatchContext $context
```
### 使用 OData 篩選

您可以提供**篩選**參數給 OData 篩選，只尋找您感興趣的物件。例如，您可以找到識別碼以 “myPool” 開頭的所有集區：

```
$filter = "startswith(id,'myPool')"
Get-AzureBatchPool -Filter $filter -BatchContext $context
```

雖然這個方法比在本機管線中使用 “Where-Object” 較不具有彈性，不過查詢將直接傳送進 Batch 服務，讓所有篩選在伺服器端運作，進而省下網際網路頻寬。

### 使用識別碼參數

使用**識別碼**參數可做為 OData 篩選的替代方式。若要查詢識別碼為 "myPool" 的特定集區：

```
Get-AzureBatchPool -Id "myPool" -BatchContext $context

```
**識別碼**參數僅支援完整識別碼的搜尋，不能使用萬用字元或 OData 樣式的篩選。

### 使用管線

Batch Cmdlet 可以利用 PowerShell 管線在 Cmdlet 之間傳送資料。這和指定參數有相同效果，但讓列出多個實體更容易。例如，您可以在您的帳戶下找到所有的作業：

```
Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

### 使用 MaxCount 參數

依預設，每個 Cmdlet 最多傳回 1000 個物件。如果您已經到達此限制，您可以縮小您的篩選以傳回較少的物件，或使用 **MaxCount** 參數明確地設定最大值。例如：

```
Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

```

若要移除上限，將 **MaxCount** 設定為 0 或更少。

## 相關主題
* ＜[批次技術概觀](batch-technical-overview.md)＞
* ＜[下載 Azure PowerShell](http://go.microsoft.com/p/?linkid=9811175)＞
* [Azure 批次 Cmdlet 參考資料](https://msdn.microsoft.com/library/azure/mt125957.aspx)
* [有效率的清單查詢](batch-efficient-list-queries.md)

<!---HONumber=Oct15_HO3-->