<properties
   pageTitle="使用 Azure VM 延伸模組編寫範本 | Microsoft Azure"
	description="深入了解如何使用延伸模組編寫範本"
	services="virtual-machines"
	documentationCenter=""
	authors="kundanap"
	manager="timlt"
	editor=""/>

<tags
   ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/01/2015"
	ms.author="kundanap"/>

# 使用 VM 延伸模組編寫 Azure 資源管理員範本。

## Azure 資源管理員範本概觀。

Azure 資源管理員範本可讓您藉由定義資源之間的相依性，以宣告方式指定 JSON 語言中的 Azure IaaS 基礎結構。如需 Azure 資源管理員範本的詳細概觀，請參閱以下文章：

<a href="https://azure.microsoft.com/zh-TW/documentation/articles/resource-group-overview/" target="_blank">資源群組概觀</a>。<br/><a href="https://azure.microsoft.com/zh-TW/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli/" target="_blank">以 Azure CLI 部署範本</a>。<br/><a href="https://azure.microsoft.com/zh-TW/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/" target="_blank">以 Azure Powershell 部署範本</a>。

## VM 延伸模組的範例範本程式碼片段。
用於部署延伸模組的範本程式碼片段如下所示：

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

## 識別任何延伸模組的 publisher、type 和 the typeHandlerVersion。

Azure VM 延伸模組是由 Microsoft 和受信任的第 3 方發行者所發佈，每個延伸模組會依其 publisher、type 和 typeHandlerVersion 進行唯一識別。其判斷方式如下：

從 Azure PowerShell，執行下列 Azure PowerShell Cmdlet：

      Get-AzureVMAvailableExtension

從 Azure CLI，執行下列 Azure PowerShell Cmdlet：

      Azure VM Extension list

此 Cmdlet 會傳回發行者名稱、延伸模組名稱和版本，如下所示：

       Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

這三個屬性分別對應至上述範本程式碼片段中的 "publisher"、"type" 和 "typeHandlerVersion"。注意︰建議一律使用最新的延伸模組版本，以取得最新的功能。

## 識別延伸模組組態參數的結構描述

編寫延伸模組範本的下一個步驟是識別用於提供組態參數的格式。每個延伸模組都支援自己的參數集。

若要查看 Windows 延伸模組的一些範例組態，請按一下 [Windows 延伸模組範例](virtual-machines-extensions-configuration-samples-windows.md)文件。

若要查看 Linux 延伸模組的一些範例組態，請按一下 [Linux 延伸模組範例](virtual-machines-extensions-configuration-samples-linux.md)文件。

請參閱 VM 範本的下列項目以取得 VM 延伸模組的完整範本。

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">在 Linux VM 上的自訂指令碼延伸模組</a>。</br><a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">在 Windows VM 上的自訂指令碼延伸模組</a>。

編寫範本之後，您可以使用 Azure CLI 或 Azure Powershell 部署它們。

<!---HONumber=September15_HO1-->