<properties
   pageTitle="如何使用 Azure 診斷和 Operational Insights 收集記錄檔 | Microsoft Azure"
   description="本文將說明如何設定 Azure 診斷和 Azure Operational Insights 從 Azure 中執行的 Service Fabric 叢集收集記錄檔"
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="toddabel"/>


# 使用 Azure 診斷和 Operational Insights 從 Service Fabric 叢集收集記錄檔

當您執行 Azure Service Fabric 叢集時，最好從中央位置的所有節點收集記錄檔。將記錄檔集中在中央位置，可輕鬆分析和疑難排解您的叢集或該叢集中執行之應用程式與服務的問題。上傳和收集記錄檔的方式之一是使用可將記錄檔上傳至 Azure 儲存體的 Azure 診斷延伸模組。

Azure [Operational Insights](https://azure.microsoft.com/services/operational-insights/) (屬於 Microsoft Operations Management Suite 的一部分) 是可輕鬆分析和搜尋記錄檔的 SaaS 解決方案。下列步驟說明如何在叢集的 VM 上設定 Azure 診斷延伸模組，以便將記錄檔上傳至集中存放區，然後設定 Operational Insights 來提取記錄，讓您可以在 Operational Insights 入口網站中檢視記錄。

Operational Insights 會依儲存記錄檔的儲存體資料表的名稱，識別從 Service Fabric 叢集上傳的各種記錄檔的來源。這表示 Azure 診斷延伸模組必須設定為將記錄檔上傳至名稱與 Operational Insights 搜尋目標相符的儲存體資料表。這份文件中的組態設定範例將顯示儲存體資料表的名稱。


## 必要條件
這些工具將用來執行這份文件中的某些作業：

* [Azure PowerShell](../powershell-install-configure.md)
* [Operational Insights](https://azure.microsoft.com/services/operational-insights/)


## 設定 Operational Insights 來檢視和搜尋叢集記錄檔
在詳述[如何利用 Azure 診斷收集記錄檔](service-fabric-diagnostics-how-to-setup-wad.md)的本文中，記載如何啟用傳送至 Azure 儲存體帳戶之記錄檔的逐步指南。一旦診斷在叢集上的設定且開始將記錄檔上傳至儲存體帳戶，接下來就是設定 Operational Insights，讓您可以透過 Operational Insights 入口網站來檢視、搜尋和查詢所有叢集記錄檔。

### 建立 Operational Insights 工作區
若要查看建立 Operational Insights 工作區的步驟，請參閱下面的文章。請注意，其中描述兩種不同的方式來建立工作區。請選擇以 Azure 入口網站和訂用帳戶為基礎的方法。在本文件稍後的幾節中，您需要 Operational Insights 工作區的名稱。使用您用來建立所有叢集資源 (包括儲存體帳戶) 的相同訂用帳戶，建立 Operational Insights 工作區。

[Operational Insights 運作](https://technet.microsoft.com/library/mt484118.aspx)

### 設定 Operational Insights 工作區來顯示叢集記錄檔
如上所述建立 Operational Insights 工作區之後，接下來就是設定工作區，從 Azure 儲存體資料表中提取診斷延伸模組從叢集上傳的記錄檔。目前無法透過 Operational Insights 入口網站來進行此設定，只能透過 PowerShell 命令來完成。執行下列 PowerShell 指令碼：

```powershell

    <#
    This script will configure an Operations Management Suite workspace (aka Operational Insights workspace) to read Diagnostics from an Azure Storage account.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and Resource Manager storage accounts.

    If you have more than one OMS workspace you will be prompted for the workspace to configure.

    If you have more than one storage account you will be prompted for which storage account to configure.
    #>

Add-AzureAccount

Switch-AzureMode -Name AzureResourceManager

$validTables = "WADServiceFabric*EventTable", "WADETWEventTable"

function Select-Workspace {

    $workspace = ""

    $allWorkspaces = Get-AzureOperationalInsightsWorkspace  

    switch ($allWorkspaces.Count) {
        0 {Write-Error "No Operations Management Suite workspaces found"}
        1 {return $allWorkspaces}
        default {
            $uiPrompt = "Enter the number corresponding to the workspace you want to configure.`n"

            $count = 1
            foreach ($workspace in $allWorkspaces) {
                $uiPrompt += "$count. " + $workspace.Name + " (" + $workspace.CustomerId + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt) - 1
            $workspace = $allWorkspaces[$answer]
        }  
    }
    return $workspace
}

function Select-StorageAccount {

    $storage = ""

    $allStorageAccounts = (get-azureresource -ResourceType Microsoft.ClassicStorage/storageAccounts) + (get-azureresource -ResourceType Microsoft.Storage/storageAccounts)

    switch ($allStorageAccounts.Count) {
        0 {Write-Error "No storage accounts found"}
        1 {$storage = $allStorageAccounts}
        default {

            $uiPrompt = "Enter the number corresponding to the storage account with diagnostics.`n"

            $count = 1
            foreach ($storageAccount in $allStorageAccounts) {
                $uiPrompt += "$count. " + $storageAccount.Name + " (" + $storageAccount.Location + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt)

            $storage = $allStorageAccounts[$answer - 1]
        }
    }
    return $storage
}

$workspace = Select-Workspace
$storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try
{
    $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop
}
catch [Hyak.Common.CloudException]
{
    # HTTP Not Found is returned if the storage insight doesn't exist
}

if ($existingConfig) {
    Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else {
    if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") {
        Switch-AzureMode -Name AzureServiceManagement
        $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary
        Switch-AzureMode -Name AzureResourceManager
    } else {
        $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1
    }
    New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers
}
```

一旦設定 Operational Insights 工作區，以從您的儲存體帳戶中的 Azure 資料表讀取，您應該登入入口網站，並移至 Operational Insights 資源的 [儲存體] 索引標籤。應該會顯示類似下面的畫面：![Azure 入口網站中的 Operational Insights 儲存體設定](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### 在 Operational Insights 中搜尋和檢視記錄檔
將 Operational Insights 工作區設定為從指定的儲存體帳戶讀取記錄檔之後，可能需要長達 10 分鐘的時間，記錄才會出現在 Operational Insights UI 中。為了確保有新的記錄檔產生，最好將 Service Fabric 應用程式部署至您的叢集，因為它將會從 Service Fabric 平台產生操作事件。

1. 若要檢視記錄檔，請在 Operational Insights 入口網站的主頁面上選取 [記錄檔搜尋]。![Operational Insights 記錄檔搜尋選項](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. 在記錄檔搜尋頁面中，使用查詢 **Type=ServiceFabricOperationalEvent**，您應該會看到來自叢集的操作記錄，如下所示。若要查看所有 Actor 程式設計模型記錄檔，請查詢 **Type=ServiceFabricReliableActorEvent**。若要查看 Reliable Services 程式設計模型的所有記錄檔，請輸入 **Type=ServiceFabricReliableServiceEvent**。![Operational Insights 記錄檔查詢和檢視](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### 協助問題疑難排解的範例查詢
以下是幾個案例及可用來進行疑難排解的查詢範例：

1. **了解 Service Fabric 是否針對特定的服務呼叫 RunAsync：**如果您需要排除服務是否在啟動時損毀，您就可能想要這樣做。作法是使用類似下面的查詢來搜尋，但換成符合您已部署的服務和應用程式名稱，然後看看是否傳回任何結果：

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. **如果您執行具狀態服務，且想要檢查它是否擲回 Service Fabric 已標示為失敗的任何例外狀況：**使用類似下列的查詢來找出那些事件：

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. **若要尋找所有已部署的應用程式和服務中由 Actor 方法擲回的任何例外狀況的相對應事件：**可以使用如下的查詢：

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## 更新診斷從新的 EventSource 通道收集並上傳記錄檔
若要更新診斷從新的 EventSource 通道 (代表您將要部署的新應用程式) 收集記錄檔，您只需要執行[上述小節](#deploywadarm)中相同的步驟即可，其中描述現有叢集的診斷設定。

在透過資源管理員命令套用組態更新之前，您必須更新 WadConfigUpdate.json 中的 EtwEventSourceProviderConfiguration 區段，加入新的 EventSources 的項目。用於上傳的資料表相同 (ETWEventTable)，因為這是設定給 Operational Insights 讀取應用程式 ETW 事件的資料表。


## 後續步驟
查看針對 [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) 和 [Reliable Services](service-fabric-reliable-services-diagnostics.md) 所發出的診斷事件，以更詳細了解進行問題移難排解時應該調查哪些事件。

<!---HONumber=AcomDC_0330_2016-->