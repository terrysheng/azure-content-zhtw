<properties
   pageTitle="針對 Azure VM 延伸模組的失敗進行疑難排解 | Microsoft Azure"
   description="深入了解針對 Azure VM 延伸模組的失敗進行疑難排解"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# 針對 Azure VM 延伸模組的失敗進行疑難排解。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]本文涵蓋內容包括如何使用資源管理員部署模型。

## Azure 資源管理員範本概觀。

Azure 資源管理員範本可讓您藉由定義資源之間的相依性，以宣告方式指定 JSON 語言中的 Azure IaaS 基礎結構。


按一下文章[撰寫延伸模組範本](virtual-machines-extensions-authoring-templates.md)來深入了解撰寫使用延伸模組的範本。

在本文中我們將了解如何疑難排解某些常見的 VM 延伸模組失敗。

## 檢視延伸模組狀態：
Azure 資源管理員範本可以從 Azure Powershell 或 Azure CLI 執行。一旦執行範本之後，即可以從 Azure 資源總管或命令列工具檢視延伸模組狀態。這裡有一些範例：

Azure CLI：

      azure vm get-instance-view

Azure Powershell：

      Get-AzureVM -ResourceGroupName $RGName -Name $vmName -Status

以下是範例輸出：

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## 針對延伸模組失敗進行疑難排解：

### 在 VM 上重新執行延伸模組：

如果您使用自訂指令碼延伸模組在 VM 上執行指令碼，有時候可能會遇到雖然成功建立了 VM 但指令碼卻失敗的錯誤。在這樣的情況下，若要從錯誤中復原，建議您移除延伸模組並再次重新執行範本。請注意：未來將增強這項功能，以移除對解除安裝延伸模組的需求。

#### 從 Azure CLI 移除延伸模組：

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

其中 "publsher-name" 對應的延伸模組類型來自 "azure vm get-instance-view" 的輸出，而名稱是來自範本的延伸模組資源名稱

#### 從 Azure Powershell 移除延伸模組：

    Remove-AzureVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

一旦移除了延伸模組，範本就可以重新執行並在 VM 上執行指令碼。

<!---HONumber=Oct15_HO1-->