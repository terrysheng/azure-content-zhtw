<properties
	pageTitle="使用 Azure PowerShell 建置您的第一個 Azure Data Factory 管線"
	description="在本教學課程中，您將使用 Azure PowerShell，建立範例 Azure Data Factory 管線。"
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="12/15/2015"
	ms.author="spelluru"/>

# 使用 Azure PowerShell 建置您的第一個 Azure Data Factory 管線
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


在本文中，您將了解如何使用 Azure 資源管理員範本來建立您的第一個管線。本教學課程包含下列步驟：

1.	建立 Data Factory。
2.	建立連結服務 (資料存放區、運算) 和資料集。
3.	建立管線。

 

## 先決條件
除了「教學課程概觀」主題中所列的必要條件，您還需要安裝下列項目：

- **安裝 Azure PowerShell**。按照[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 一文中的指示操作，在您的電腦上安裝最新版的 Azure PowerShell。
- 本文不提供 Azure Data Factory 服務的概念性概觀。如需有關服務的詳細概觀，請閱讀 [Azure Data Factory 簡介](data-factory-introduction.md)。 
- 若要了解資源管理員 (ARM) 範本，請參閱[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。 
 

## 步驟 1：建立 ARM 範本

在 **C:\\ADFGetStarted** 資料夾中，使用下列內容建立名為 **ADFTutorialARM.json** 的 JSON 檔案：

> [AZURE.IMPORTANT]變更 **storageAccountName** 和 **storageAccountKey** 變數的值。也變更 **dataFactoryName**，因為名稱必須是唯一的。

	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "TutorialDataFactoryARM",
	        "storageAccountName":  "<stroage account name>" ,
	        "storageAccountKey":  "<storage account key>",
	        "apiVersion": "2015-10-01",
	        "storageLinkedServiceName": "StorageLinkedService",
	        "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
	        "blobOutputDataset": "AzureBlobOutput",
	        "singleQuote": "'"
	    },
	    "resources": [
	        {
	            "name": "[variables('dataFactoryName')]",
	            "apiVersion": "[variables('apiVersion')]",
	            "type": "Microsoft.DataFactory/datafactories",
	            "location": "westus",
	            "resources": [
	                {
	                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
	                    "type": "linkedservices",
	                    "name": "[variables('storageLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "AzureStorage",
	                        "typeProperties": {
	                            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "linkedservices",
	                    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "HDInsightOnDemand",
	                        "typeProperties": {
	                            "version": "3.2",
	                            "clusterSize": 1,
	                            "timeToLive": "00:30:00",
	                            "linkedServiceName": "StorageLinkedService"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobOutputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                      "properties": {
	                        "type": "AzureBlob",
	                        "linkedServiceName": "StorageLinkedService",
	                        "typeProperties": {
	                          "folderPath": "data/partitioneddata",
	                          "format": {
	                            "type": "TextFormat",
	                            "columnDelimiter": ","
	                          }
	                        },
	                        "availability": {
	                          "frequency": "Month",
	                          "interval": 1
	                        }
	                      }
	                    },
	                    {
	                        "dependsOn": [
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
	                        ],
	                        "type": "datapipelines",
	                        "name": "[variables('dataFactoryName')]",
	                        "apiVersion": "[variables('apiVersion')]",
	                        "properties": {
	                            "description": "My first Azure Data Factory pipeline using ARM",
	                            "activities": [
	                              {
	                                "type": "HDInsightHive",
	                                "typeProperties": {
	                                  "scriptPath": "script/partitionweblogs.hql",
	                                  "scriptLinkedService": "StorageLinkedService",
	                                  "defines": {
                                        "partitionedtable": "[concat('wasb://data@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"	                                  }
	                                },
	                                "outputs": [
	                                  {
	                                    "name": "[variables('blobOutputDataset')]"
	                                  }
	                                ],
	                                "scheduler": {
	                                    "frequency": "Month",
	                                    "interval": 1
	                                },
	                                "policy": {
	                                  "concurrency": 1,
	                                  "retry": 3
	                                },
	                                "name": "RunSampleHiveActivity",
	                                "linkedServiceName": "HDInsightOnDemandLinkedService"
	                              }
	                            ],
	                            "start": "2014-01-01",
	                            "end": "2014-01-02"
	                        }
	                    }
	            ]
	        }
	    ]
	}


## 步驟 2：使用 ARM 範本部署 Data Factory 實體

1. 開啟 Azure PowerShell 並執行下列命令。將 Azure PowerShell 維持在開啟狀態，直到本教學課程結束為止。如果您關閉並重新開啟，則需要再次執行這些命令。
	- 執行 **Login-AzureRmAccount**，並輸入您用來登入 Azure 入口網站的使用者名稱和密碼。  
	- 執行 **Get-AzureSubscription** 以檢視此帳戶的所有訂用帳戶。
	- 執行 **Select-AzureSubscription SubscriptionName** 以選取您想要使用的訂用帳戶。此訂用帳戶應該與您在 Azure 入口網站中使用的相同。
1. 執行下列命令，使用您在步驟 1 中建立的 ARM 範本來部署 Data Factory 實體。 

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## 監視管線
 
1.	登入 [Azure 入口網站](http://portal.azure.com/)後，按一下 [瀏覽]，然後選取 [Data Factory]。![瀏覽 -> Data Factory](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	在 [Data Factory] 刀鋒視窗中，按一下您建立的 Data Factory (**TutorialFactoryARM**)。	
2.	在您 Data Factory 的 [Data Factory] 刀鋒視窗中，按一下 [圖表] 磚。![圖表磚](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	在 [圖表檢視] 中，您會看到管線的概觀，以及在本教學課程中使用的資料集。
	
	![圖表檢視](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. 在 [圖表檢視] 中，按兩下 **AzureBlobOutput** 資料集。您會看到目前正在處理的配量。

	![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. 當處理完成時，您會看到配量處於 [就緒] 狀態。請注意，建立隨選 HDInsight 叢集通常需要一些時間。 

	![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)	
10. 當配量處於**就緒**狀態時，檢查您 blob 儲存體中 **data** 容器內 **partitioneddata** 資料夾的輸出資料。  
 

<!---HONumber=AcomDC_1217_2015-->