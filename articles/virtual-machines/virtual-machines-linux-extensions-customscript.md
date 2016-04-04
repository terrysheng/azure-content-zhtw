<properties
   pageTitle="在 Linux VM 上使用範本的自訂指令碼 | Microsoft Azure"
   description="使用自訂指令碼擴充功能搭配資源管理員範本，將 Linux VM 組態工作自動化"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="11/01/2015"
   ms.author="kundanap"/>

# 使用 Linux VM 的自訂指令碼擴充功能搭配 Azure Resource Manager 範本

本文概要說明如何以自訂指令碼擴充功能撰寫 Azure Resource Manager 範本，以啟動 Linux VM 上的工作負載。


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-extensions-customscript.md)。

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Linux VM 的範本範例

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
    
在上述範例中，以您自己的設定取代檔案的 URL 和檔案名稱。

編寫範本之後，您可以使用 Azure CLI 部署它。

如需在 VM 上使用自訂指令碼擴充功能來設定應用程式的完整範例，請參閱以下範例。

* [Linux VM 上的自訂指令碼擴充功能](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

<!---HONumber=AcomDC_0323_2016-->