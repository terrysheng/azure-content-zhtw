<properties
   pageTitle="VM 上使用範本的自訂指令碼 | Microsoft Azure"
   description="使用自訂指令碼擴充功能搭配資源管理員範本，將 Windows 和 Linux Azure VM 組態工作自動化"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="07/01/2015"
   ms.author="kundanap"/>

# 使用自訂指令碼擴充功能搭配 Azure 資源管理員範本

這篇文章概要說明如何以自訂指令碼擴充功能撰寫 Azure 資源管理員範本，以啟動 Linux 或 Windows VM 上的工作負載。

如需自訂指令碼擴充功能的概觀，請參閱[這篇](virtual-machines-extensions-customscript.md)文章。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-extensions-customscript.md)。

自從自訂指令碼擴充功能可供使用後，已廣泛用來在 Windows 和 Linux VM 上設定工作負載。隨著 Azure 資源管理員範本的引入，使用者現在可以建立單一的範本，不只可用於佈建 VM，也可在 VM 上設定工作負載。

## Azure 資源管理員範本概觀

Azure 資源管理員範本可讓您藉由定義資源之間的相依性，以宣告方式指定 JSON 語言中的 Azure IaaS 基礎結構。如需 Azure 資源管理員範本的詳細概觀，請參閱以下文章：

<a href="https://azure.microsoft.com/zh-TW/documentation/articles/resource-group-overview/" target="_blank">資源群組概觀</a>。<br/><a href="https://azure.microsoft.com/zh-TW/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli/" target="_blank">以 Azure CLI 部署範本</a>。<br/><a href="https://azure.microsoft.com/zh-TW/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/" target="_blank">以 Azure Powershell 部署範本</a>。

### 執行自訂指令碼擴充功能的先決條件

1. 從<a href="http://azure.microsoft.com/downloads" target="_blank">這裡</a>安裝最新版的 Azure PowerShell Cmdlet 或 Azure CLI。
2. 如果指令碼將在現有 VM 上執行，請確定會在該 VM 上啟用 VM 代理程式，如果沒有，請依照這篇<a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">文章</a>安裝一個 VM。
3. 將您想要在 VM 上執行的指令碼上傳到 Azure 儲存體。指令碼可以來自單一或多個儲存體容器。
4. 或者也能將指令碼上傳至 Github 帳戶。
5. 指令碼應該以由延伸模組依序要啟動的項目指令碼啟動其他指令碼的方式來撰寫。

## 使用自訂指令碼擴充功能搭配範本的概觀：

為了部署範本，我們使用 Azure 服務管理 API 中可用之相同版本的自訂指令碼延伸模組。本延伸模組支援將檔案上傳至 Azure 儲存體帳戶或 Github 位置的相同參數和案例。搭配範本使用時，主要差異在於應該指定正確版本的延伸模組，而不是以 majorversion.* 格式指定版本。

 ## Linux VM 上自訂指令碼擴充功能的範本程式碼片段

在範本的資源區段中定義下列延伸模組資源

      {
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "MyCustomScriptExtension",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
    "properties":
    {
      "publisher": "Microsoft.OSTCExtensions",
      "type": "CustomScriptForLinux",
      "typeHandlerVersion": "1.2",
      "settings": {
      "fileUris": [ "https: //raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh                        ],
      "commandToExecute": "shmongo-install-ubuntu.sh"
      }
    }
    }

## Windows VM 上自訂指令碼擴充功能的範本程式碼片段

在範本的資源區段中定義下列資源

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.4",
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

在上述範例中，以您自己的設定取代檔案的 URL 和檔案名稱。

撰寫範本之後，您可以使用 Azure CLI 或 Azure Powershell 部署它們。

如需在 VM 上使用自訂指令碼擴充功能來設定應用程式的完整範例，請參閱以下範例。

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Linux VM 上的自訂指令碼延伸模組</a>。</br> <a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">Windows VM 上的自訂指令碼延伸模組</a>。

<!---HONumber=Oct15_HO3-->