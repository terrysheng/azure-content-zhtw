<properties
   pageTitle="使用 Azure VM 延伸模組編寫範本 | Microsoft Azure"
   description="深入了解如何使用延伸模組編寫範本"
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
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# 使用 VM 延伸模組編寫 Azure 資源管理員範本。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。
 

## Azure 資源管理員範本概觀。

Azure 資源管理員範本可讓您藉由定義資源之間的相依性，以宣告方式指定 JSON 語言中的 Azure IaaS 基礎結構。如需 Azure 資源管理員範本的詳細概觀，請參閱以下文章：

[資源群組概觀](../resource-group-overview.md)

## VM 延伸模組的範例範本程式碼片段。
若要將 VM 延伸模組部署到 Azure 資源管理員範本中，您必須以宣告方式在範本中指定延伸模組組態。以下是用來指定延伸模組組態的格式。

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

您可以在以上程式碼中看到，延伸模組範本包含兩個主要部分：

1. 延伸模組名稱、發行者和版本。
2. 延伸模組組態。

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

若要查看 Windows 延伸模組的範例組態，請按一下 [Windows 延伸模組範例](virtual-machines-extensions-configuration-samples-windows.md)文件。

若要查看 Linux 延伸模組的範例組態，請按一下 [Linux 延伸模組範例](virtual-machines-extensions-configuration-samples-linux.md)文件。

請參閱 VM 範本的下列項目以取得 VM 延伸模組的完整範本。

[Windows VM 上的自訂指令碼延伸模組](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

[Linux VM 上的自訂指令碼延伸模組](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

編寫範本之後，您可以使用 Azure CLI 或 Azure Powershell 部署它們。

<!---HONumber=Oct15_HO3-->