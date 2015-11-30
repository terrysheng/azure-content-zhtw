<properties
   pageTitle="如何使用 WAD 和 Operational Insights 收集記錄檔 | Microsoft Azure"
   description="本文將說明如何設定 Microsoft Azure 診斷和 Operational Insights 從 Azure 中執行的 Service Fabric 叢集收集記錄檔"
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/20/2015"
   ms.author="kunalds"/>


# 在 Azure 中使用 WAD (Microsoft Azure 診斷) 和 Operational Insights 從 Service Fabric 叢集收集記錄檔

在 Azure 中執行 Service Fabric 叢集時，您可以將所有節點的記錄檔收集到中央位置。將記錄檔集中在中央位置，可輕鬆分析和疑難排解您的叢集或該叢集中執行的應用程式與服務可能發生的任何問題。上傳和收集記錄檔的方式之一是使用可將記錄上傳至 Azure 資料表儲存體的 WAD (Microsoft Azure 診斷) 延伸。Operational Insights (屬於 Microsoft Operations Management Suite 的一部分) 是可輕鬆分析和搜尋記錄檔的 SaaS 解決方案。下列步驟說明如何在叢集的 VM 上設定 WAD，以便將記錄檔上傳至集中存放區，然後設定 Operational Insights 來提取記錄，讓您可以在 Operational Insights 入口網站中檢視記錄。Operational Insights 會依儲存記錄檔的 Azure 儲存體資料表的名稱，識別從 Service Fabric 叢集上傳的各種記錄檔的來源，所以 WAD 必須設定為將記錄檔上傳至名稱與 Operational Insights 搜尋目標相符的 Azure 儲存體資料表，這份文件中的組態設定範例將示範儲存體資料表的名稱。

## 建議閱讀資料
* [Microsoft Azure 診斷](../cloud-services/cloud-services-dotnet-diagnostics.md) (與雲端服務相關，但提供一些有用的資訊和範例)
* [Operational Insights](https://azure.microsoft.com/services/operational-insights/)
* [Azure 資源管理員](https://azure.microsoft.com/documentation/articles/resource-group-overview/)

## 必要條件
這些工具將用來執行這份文件中的某些作業：* [Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) * [ARM 用戶端](https://github.com/projectkudu/ARMClient)

## 您可能想要收集的不同記錄來源
1. Service Fabric 記錄檔：由平台發出到標準 ETW 和 EventSource 通道。這些可能是下列其中一種類型：
  - 操作事件：這些是 Service Fabric 平台所執行之作業的記錄檔。範例包括建立應用程式和服務、節點狀態變更和升級資訊。
  - [Actor 程式設計模型事件](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-actors-diagnostics/)
  - [Reliable Services 程式設計模型事件](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-services-diagnostics/)
2. 應用程式事件：這些是從您的服務程式碼發出且使用 Visual Studio 範本所提供的 EventSource 協助程式類別所寫出的事件。如需有關如何從應用程式寫入記錄檔的詳細資訊，請參閱這篇[文章](https://azure.microsoft.com/documentation/articles/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/)。


## 將 WAD (Microsoft Azure 診斷) 部署到 Service Fabric 叢集來收集和上傳記錄檔
收集記錄檔的第一個步驟是將 WAD 延伸部署在 Service Fabric 叢集的每個 WM 上。WAD 會收集每個 VM 上的記錄檔，並將它們上傳至您指定的儲存體帳戶。根據您是使用入口網站或 ARM，以及是在建立叢集時或針對已存在的叢集來部署而定，步驟會稍微不同。讓我們看看每個案例的步驟。

### 透過入口網站建立叢集時部署 WAD
為了在建立叢集時將 WAD 部署至叢集的 WM，我們使用下圖所示的「診斷設定」。預設為 [開啟]。![入口網站中用於建立叢集的 WAD 設定](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/portal-cluster-creation-diagnostics-setting.png)

### 使用 ARM 建立叢集時部署 WAD
若要使用 ARM 建立叢集，您需要在建立叢集之前將 WAD 組態 JSON 加入至完整的叢集 ARM 範本。我們在 ARM 範本範例中提供一個 5 VM 叢集 ARM 範本範例，且已在其中加入 WAD 組態，您可以在 Azure 範例庫中的這個位置看到它：[使用 WAD ARM 範本的五節點叢集範例](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad)。

若要查看 ARM 範本中的 WAD 設定，請搜尋 "WadCfg"。若要使用這個範本建立叢集，只要按上面連結所提供的 [部署到 Azure] 按鈕即可。或者，您也可以下載 ARM 範例，加以變更，然後在 Azure Powershell 視窗中使用 `New-AzureResourceGroupDeployment` 命令，使用修改過的範本建立叢集。您需要傳給命令的參數如下。此外，在呼叫此部署命令之前，您可能需要進行一些設定，包括加入 Azure 帳戶 (`Add-AzureAccount`)、選擇訂用帳戶 (`Select-AzureSubscription`)、切換到 ARM 模式 (`Switch-AzureMode AzureResourceManager`)，以及建立資源群組 (如果尚未建立) (`New-AzureResourceGroup`)。

```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploywadarm"></a>將 WAD 部署到現有的叢集
如果現有的叢集上未部署 WAD，您可以使用下列步驟加入 WAD。使用下列 JSON 建立兩個檔案：WadConfigUpdate.json 和 WadConfigUpdateParams.json。

##### WadConfigUpdate.json
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",

  "parameters": {
        "vmNamePrefix": {
      "type": "string"
    },
        "applicationDiagnosticsStorageAccountName": {
      "type": "string"
    },
        "vmCount": {
            "type": "int"
    }
  },

  "resources": [
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(parameters('vmNamePrefix'),copyIndex(0),'/Microsoft.Insights.VMDiagnosticsSettings')]",
            "location": "[resourceGroup().location]",
      "properties": {
        "type": "IaaSDiagnostics",
                "typeHandlerVersion": "1.5",
        "publisher": "Microsoft.Azure.Diagnostics",
                "autoUpgradeMinorVersion": true,
        "settings": {
                    "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                "EtwProviders": {
                    "EtwEventSourceProviderConfiguration": [
                        {
                            "provider": "Microsoft-ServiceFabric-Actors",
                            "scheduledTransferKeywordFilter": "1",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableActorEventTable" }
                        },
                        {
                            "provider": "Microsoft-ServiceFabric-Services",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableServiceEventTable" },
                                        "scheduledTransferPeriod": "PT5M"
                        }
                    ],
                    "EtwManifestProviderConfiguration": [
                        {
                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                        "scheduledTransferLogLevelFilter": "Information",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricSystemEventTable" }
                        }
                    ]
                }
            }
    },
                    "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountNamee')]"
                },
                "protectedSettings": {
                    "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountNamee')]",
                    "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                    "storageAccountEndPoint": "https://core.windows.net/"
                }
            },
            "copy": {
                "name": "vmExtensionLoop",
                "count": "[parameters('vmCount')]"
            }
        }
    ],

    "outputs": {
    }
}
```

##### WadConfigUpdateParams.json
以您建立叢集時為 VM 名稱選擇的前置詞取代 vmNamePrefix，並編輯 vmStorageAccountName，設為您要從 VM 將記錄檔上傳到的儲存體帳戶。```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmNamePrefix": {
            "value": "VM"
        },
        "applicationDiagnosticsStorageAccountName": {
            "value": "testdiagacc"
        },
        "vmCount": {
            "value": 5
        }
    }
}
``` 如上所述建立 json 檔並依照您環境的特點來變更之後，請呼叫此命令，並傳入 Service Fabric 叢集的資源群組名稱。成功執行此命令後，WAD 將部署在所有 VM 上，並開始將叢集中的記錄檔上傳至指定的 Azure 儲存體帳戶中的資料表。此外，在呼叫此部署命令之前，您可能需要進行一些設定，包括加入 Azure 帳戶 (`Add-AzureAccount`)、選擇正確的訂用帳戶 (`Select-AzureSubscription`)，以及切換到 ARM 模式 (`Switch-AzureMode AzureResourceManager`)。```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToWADConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

## 設定 Operational Insights 來檢視和搜尋叢集記錄檔
一旦 WAD 在叢集上的設定且開始將記錄檔上傳至儲存體帳戶，接下來就是設定 Operational Insights，讓您可以透過 Operational Insights 的入口網站 UI 來檢視、搜尋和查詢所有叢集記錄檔。

### 建立 Operational Insights 工作區
若要查看建立 Operational Insights 工作區的步驟，請參閱下列文件。請注意，其中描述兩種不同的方式來建立工作區，請選擇以 Azure 入口網站和訂用帳戶為基礎的方法。在本文件稍後的幾節中，您需要 Operational Insights 工作區的名稱。使用您用來建立所有叢集資源 (包括儲存體帳戶) 的相同訂用帳戶，建立 Operational Insights 工作區。

[Operational Insights 運作](https://technet.microsoft.com/library/mt484118.aspx)

### 設定 Operational Insights 工作區來顯示叢集記錄檔
如上所述建立 Operational Insights 工作區之後，接下來就是設定工作區，從 Azure 資料表中提取 WAD 從叢集上傳的記錄檔。目前無法透過 Operational Insights 入口網站來設定此組態，只能透過 Powershell 命令來完成。執行這個 PS 指令碼。```powershell <# 這個指令碼將會設定 Operations Management Suite 工作區 (又稱為 Operational Insights 工作區) 從 Azure 儲存體帳戶讀取 Microsoft Azure 診斷。

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and ARM storage accounts.

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

$workspace = Select-Workspace $storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try { $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop } catch [Hyak.Common.CloudException] { # 如果 storage insight 不存在，則會傳回「HTTP 找不到」 }

if ($existingConfig) { Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else { if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") { Switch-AzureMode -Name AzureServiceManagement $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary Switch-AzureMode -Name AzureResourceManager } else { $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1 } New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers } ``` 設定 Operational Insights 工作區讀取儲存體帳戶中的 Azure 資料表之後，您應該登入 Azure 入口網站，然後在 [儲存體] 索引標籤中查詢 Operational Insights 資源。應該會顯示類似下面的畫面：![Azure 入口網站中的 Operational Insights 儲存體設定](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### 在 Operational Insights 中搜尋和檢視記錄檔
將 Operational Insights 工作區設定為從指定的儲存體帳戶讀取記錄檔之後，可能需要長達 10 分鐘的時間，記錄才會出現在 Operational Insights UI 中。為了確保有新的記錄檔產生，您應該將 Service Fabric 應用程式部署至您的叢集，因為它將會從 Service Fabric 平台產生操作事件。

1. 若要檢視記錄檔，請在 Operational Insights 入口網站的主頁面上選取 LogSearch。![Operational Insights 記錄檔搜尋選項](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. 在記錄檔搜尋頁面中，使用這個查詢 "Type=ServiceFabricOperationalEvent"，您應該會看到來自叢集的操作記錄，如下所示。若要查看所有 Actor 程式設計模型記錄檔，您必須查詢 "Type=ServiceFabricReliableActorEvent”，若要查看 Reliable Services 程式設計模型的所有記錄檔，請輸入 "Type=ServiceFabricReliableServiceEvent"。![Operational Insights 記錄檔查詢和檢視](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### 協助問題疑難排解的範例查詢
以下是幾個案例及可用來進行疑難排解的查詢範例。

1. 了解 Service Fabric 是否針對特定的服務呼叫 RunAsync。如果您需要排除服務是否在啟動時損毀，您就可能想要這樣做。作法是使用類似下面的查詢來搜尋，並換成符合您已部署的服務和應用程式名稱，然後看看是否傳回任何結果。

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. 如果您執行具狀態服務，且想要檢查它是否擲回 Service Fabric 已標示為失敗的任何例外狀況，您可以類似下列的查詢來找出那些事件。

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. 若要尋找所有已部署的應用程式和服務中由 Actor 方法擲回的任何例外狀況的相對應事件，您可以使用如下的查詢。

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## 更新 WAD 從新的 EventSource 通道收集並上傳記錄檔
若要更新 WAD 從新的 EventSource 通道 (代表您將要部署的新應用程式) 收集記錄檔，您只需要執行[上述小節](#deploywadarm)中相同的步驟即可，其中描述現有叢集的 WAD 設定。在透過 ARM 命令套用組態更新之前，您必須更新 WadConfigUpdate.json 中的 EtwEventSourceProviderConfiguration 區段，加入新的 EventSources 的項目。用於上傳的資料表相同 (ETWEventTable)，因為這是設定給 Operational Insights 讀取應用程式 ETW 事件的資料表。


## 後續步驟
查看針對 [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) 和 [Reliable Services](service-fabric-reliable-services-diagnostics.md) 所發出的診斷事件，以更詳細了解進行問題移難排解時應該調查哪些事件。

<!---HONumber=Nov15_HO4-->