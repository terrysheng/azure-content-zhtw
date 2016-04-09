<properties
   pageTitle="使用 Windows VM 擴充功能編寫範本 | Microsoft Azure"
   description="了解如何使用 Windows VM 擴充功能編寫 Azure Resource Manager 範本"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# 使用 Windows VM 擴充功能編寫 Azure Resource Manager 範本

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

從 Azure PowerShell，執行下列 Azure PowerShell Cmdlet：

      Get-AzureVMAvailableExtension


此 Cmdlet 會傳回發行者名稱、擴充功能名稱和版本，如下所示：

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

這三個屬性分別對應至上述範本程式碼片段中的 "publisher"、"type" 和 "typeHandlerVersion"。

>[AZURE.NOTE]建議一律使用最新的擴充功能版本，以取得最新的功能。

## 識別擴充功能組態參數的結構描述

編寫擴充功能範本的下一個步驟是識別用於提供組態參數的格式。每個延伸模組都支援自己的參數集。

若要查看 Windows 擴充功能的範例組態，請按一下 [Windows 擴充功能範例](virtual-machines-windows-extensions-configuration-samples.md)。


請參閱下列項目以取得 VM 擴充功能的完整範本。

[Windows VM 上的自訂指令碼延伸模組](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)


編寫範本之後，您可以使用 Azure PowerShell 部署它。

<!---HONumber=AcomDC_0323_2016-->