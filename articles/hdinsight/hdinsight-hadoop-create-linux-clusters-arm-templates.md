<properties
   pageTitle="使用 ARM 範本在 HDInsight 中建立 Linux 型 Hadoop 叢集 | Microsoft Azure"
   	description="了解如何使用 Azure ARM 範本建立 Azure HDInsight 的叢集。"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/08/2016"
   ms.author="jgao"/>

# 使用 ARM 範本在 HDInsight 中建立 Linux 型 Hadoop 叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-selector-create-clusters.md)]

了解如何使用 Azure 資源管理員 (ARM) 範本建立 HDInsight 叢集。如需詳細資訊，請參閱[使用 Azure 資源管理員範本部署應用程式](../resource-group-template-deploy.md)。如需其他叢集建立工具和功能的資訊，請按一下此頁面頂端的索引標籤，或參閱[叢集建立方法](hdinsight-provision-clusters.md#cluster-creation-methods)。

##必要條件：

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

開始執行本文中的指示之前，您必須擁有以下項目：

- [Azure 訂用帳戶](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- [Azure PowerShell](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater) 和/或 [Azure CLI](../xplat-cli-install.md)。 

## ARM 範本

ARM 範本可讓您在單一的協調作業中，輕鬆地為您的應用程式建立 HDInsight 叢集、其依存的資源 (例如預設儲存體帳戶) 和其他資源 (例如使用 Apache Sqoop 所需的 Azure SQL Database)。在範本中，您會定義應用程式所需的資源，並指定部署參數以針對不同的環境輸入值。範本由 JSON 與運算式所組成，可讓您用來為部署建構值。

用來建立 HDInsight 叢集和相依 Azure 儲存體帳戶的 ARM 範本可在[附錄 A](#appx-a-arm-template) 中找到。使用跨平台 [VSCode](https://code.visualstudio.com/#alt-downloads) (附檔名為 [ARM 附檔名](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)) 或文字編輯器，將範本儲存至您工作站上的檔案。您將了解如何使用各種方法來呼叫此範本。

如需 ARM 範本的詳細資訊，請參閱

- [編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)
- [使用 Azure 資源管理員範本部署應用程式](../resource-group-template-deploy.md)


## 使用 PowerShell 部署

以下程序會建立以 Linux 為基礎的 HDInsight 叢集。

**使用 ARM 範本部署叢集**

1. 將[附錄 A](#appx-a-arm-template) 的 JSON 檔案儲存到您的工作站。PowerShell 指令碼中的檔案名稱是 *C:\\HDITutorials-ARM\\hdinsight-arm-template.json*。
2. 視需要設定參數和變數。
3. 使用下列 PowerShell 指令碼執行範本：

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>" 
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName";clusterStorageAccountName="$defaultStorageAccountName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

	PowerShell 指令碼只會設定叢集名稱。儲存體帳戶名稱是硬式編碼於範本中。系統將提示您輸入叢集使用者密碼 (預設的使用者名稱是 *admin*) 和 SSH 使用者密碼 (預設的 SSH 使用者名稱是 *sshuser*)。
	
如需詳細資訊，請參閱[使用 PowerShell 進行部署](../resource-group-template-deploy.md#deploy-with-powershell)。

## 使用 Azure CLI 進行部署

下列範例會藉由呼叫 ARM 範本來建立叢集和其依存的儲存體帳戶與容器：

	azure login
	azure config mode arm
    azure group create -n hdi1229rg -l "East US"
    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json"
    
系統將提示您輸入叢集名稱、叢集使用者密碼 (預設的使用者名稱是 *admin*) 和 SSH 使用者密碼 (預設的 SSH 使用者名稱是 *sshuser*)。提供內嵌參數 ︰

    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "c:\Tutorials\HDInsightARM\create-linux-based-hadoop-cluster-in-hdinsight.json" --parameters '{"clusterName":{"value":"hdi1229"},"clusterLoginPassword":{"value":"Pass@word1"},"sshPassword":{"value":"Pass@word1"}}'

## 使用 REST API 進行部署

請參閱[使用 REST API 部署](../resource-group-template-deploy.md#deploy-with-the-rest-api)。

## 透過 Visual Studio 部署

有了 Visual Studio，您可以透過其使用者介面建立資源群組專案，並將其部署至 Azure。選取要包含在您專案中的資源類型後，這些資源會自動新增至資源管理員範本中。該專案也提供 PowerShell 指令碼來部署範本。

如需搭配資源群組使用 Visual Studio 的簡介，請參閱[透過 Visual Studio 建立與部署 Azure 資源群組](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

##後續步驟
在本文中，您學到幾種建立 HDInsight 叢集的方法。若要深入了解，請參閱下列文章：

- 如需透過 .NET 用戶端程式庫部署資源的範例，請參閱[使用 .NET 程式庫和範本部署資源](../virtual-machines/virtual-machines-windows-csharp-template.md)。
- 如需部署應用程式的深入範例，請參閱[透過可預測方式在 Azure 中佈建和部署微服務](../app-service-web/app-service-deploy-complex-application-predictably.md)。
- 如需將您的方案部署到不同環境的指引，請參閱 [Microsoft Azure 中的開發和測試環境](../solution-dev-test-environments.md)。
- 若要了解 Azure Resource Manager 範本的區段，請參閱[編寫範本](../resource-group-authoring-templates.md)。
- 如需您可以在 Azure Resource Manager 範本中使用的函式清單，請參閱[範本函式](../resource-group-template-functions.md)。

##附錄 A：ARM 範本

下列 Azure 資源管理員範本會建立 Linux 型 Hadoop 叢集與相依的 Azure 儲存體帳戶。

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the cluster login."
        }
        },
        "sshUserName": {
        "type": "string",
        "defaultValue": "sshuser",
        "metadata": {
            "description": "These credentials can be used to remotely access the cluster and the edge node virtual machine."
        }
        },
        "sshPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the ssh user."
        }
        },
        "location": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Australia East",
            "Australia Southeast"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterType": {
        "type": "string",
        "defaultValue": "hadoop",
        "allowedValues": [
            "hadoop",
            "hbase",
            "storm",
            "spark"
        ],
        "metadata": {
            "description": "The type of the HDInsight cluster to create."
        }
        },  
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 2,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }      
    },
    "variables": {
        "defaultApiVersion": "2015-05-01-preview",
        "clusterApiVersion": "2015-03-01-preview",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"      
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [],
        "tags": {},
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
        {
        "name": "[parameters('clusterName')]",
        "type": "Microsoft.HDInsight/clusters",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('clusterApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/',variables('clusterStorageAccountName'))]"
        ],
        "tags": {},
        "properties": {
            "clusterVersion": "3.2",
            "osType": "Linux",
            "clusterDefinition": {
            "kind": "[parameters('clusterType')]",
            "configurations": {
                "gateway": {
                "restAuthCredential.isEnabled": true,
                "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                }
            }
            },
            "storageProfile": {
            "storageaccounts": [
                {
                "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                "isDefault": true,
                "container": "[parameters('clusterName')]",
                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                }
            ]
            },
            "computeProfile": {
            "roles": [
                {
                "name": "headnode",
                "targetInstanceCount": "1",
                "hardwareProfile": {
                    "vmSize": "Large"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                },
                {
                "name": "workernode",
                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                "hardwareProfile": {
                    "vmSize": "Large"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                }
            ]
            }
        }
        }
    ],
    "outputs": {
        "cluster": {
        "type": "object",
        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
        }
    }
    }

<!---HONumber=AcomDC_0323_2016-->