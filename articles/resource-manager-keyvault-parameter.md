<properties
   pageTitle="透過資源管理員範本使用金鑰保存庫密碼 | Microsoft Azure"
   description="示範如何在部署期間從金鑰保存庫中傳遞密碼做為參數。"
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="tomfitz"/>

# 在部署期間傳遞安全值

當您需要在部署期間傳遞安全值 (例如密碼) 做為參數時，可以將該值儲存為 [Azure 金鑰保存庫](./key-vault/key-vault-whatis.md)中的密碼，並在其他資源管理員範本中參考該值。您只能在範本中包含密碼的參考，因此該密碼永遠都不會公開，而您不需要每次部署資源時手動輸入該密碼的值。您會指定哪些使用者或服務主體可以存取密碼。

## 部署金鑰保存庫和密碼

若要建立可從其他資源管理員範本參考的金鑰保存庫，您必須將 **enabledForTemplateDeployment** 屬性設為 **true**，而且必須為將執行參考該密碼之部署的使用者或服務主體授與存取權。

若要了解如何部署金鑰保存庫和密碼，請參閱[金鑰保存庫結構描述](resource-manager-template-keyvault.md)和[金鑰保存庫密碼結構描述](resource-manager-template-keyvault-secret.md)。

## 參考密碼

您會從將值傳遞至範本的參數檔案中參考密碼。您可以藉由傳遞金鑰保存庫的資源識別碼和密碼的名稱來參考密碼。

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

整個參數檔案可能看起來如下：

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

接受密碼的參數應該是 **securestring**。下列範例顯示範本的相關區段，該範本會部署需要系統管理員密碼的 SQL Server。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }




## 後續步驟

- 如需金鑰保存庫的一般資訊，請參閱[開始使用 Azure 金鑰保存庫](./key-vault/key-vault-get-started.md)。
- 如需搭配虛擬機器使用金鑰保存庫的相關資訊，請參閱 [Azure Resource Manager 的安全性考量](best-practices-resource-manager-security.md)。
- 如需參考金鑰密碼的完整範例，請參閱[金鑰保存庫範例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)。

<!---HONumber=AcomDC_0224_2016-->