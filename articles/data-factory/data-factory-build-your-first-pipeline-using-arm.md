<properties
	pageTitle="建置您的第一個 Data Factory (ARM 範本) |Microsoft Azure"
	description="在本教學課程中，您將使用 Azure 資源管理員範本，來建立範例 Azure Data Factory 管線。"
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
	ms.date="03/03/2016"
	ms.author="spelluru"/>

# 教學課程：使用 Azure Resource Manager 範本建置您的第一個 Azure Data Factory
> [AZURE.SELECTOR]
- [教學課程概觀](data-factory-build-your-first-pipeline.md)
- [使用 Data Factory 編輯器](data-factory-build-your-first-pipeline-using-editor.md)
- [使用 PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [使用 Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [使用資源管理員範本](data-factory-build-your-first-pipeline-using-arm.md)


在本文中，您將了解如何使用 Azure 資源管理員 (ARM) 範本來建立您的第一個 Azure Data Factory。


## 先決條件
除了「教學課程概觀」主題中所列的必要條件，您還需要安裝下列項目：

- 您**必須**詳讀[教學課程概觀](data-factory-build-your-first-pipeline.md)一文，並完成必要的步驟，再進一步繼續。 
- **安裝 Azure PowerShell**。按照[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 一文中的指示，在您的電腦上安裝最新版的 Azure PowerShell。
- 本文不提供 Azure Data Factory 服務的概念性概觀。如需有關服務的詳細概觀，請詳讀 [Azure Data Factory 簡介](data-factory-introduction.md)。 
- 若要了解 Azure 資源管理員 (ARM) 範本，請參閱[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。 


## 步驟 1：建立 ARM 範本

在 **C:\\ADFGetStarted** 資料夾中，使用下列內容建立名為 **ADFTutorialARM.json** 的 JSON 檔案：

> [AZURE.IMPORTANT] 變更 **storageAccountName** 和 **storageAccountKey** 變數的值。也變更 **dataFactoryName**，因為名稱必須是唯一的。

範本允許您建立以下 Data Factory 項目。

1. 名為 **TutorialDataFactoryARM** 的 **ata Factory**。資料處理站可以有一或多個管線。其中的管線可以有一或多個活動。例如，「複製活動」會從來源複製資料到目的地資料存放區，HDInsight Hive 活動則是執行 Hive 指令碼轉換輸入資料以產生輸出資料。 
2. 兩個**連結的服務**：**StorageLinkedService** 和 **HDInsightOnDemandLinkedService**。這些連結的服務會將您的 Azure 儲存體帳戶和 Azure HDInsight 叢集連結到您的 Data Factory。Azure 儲存體帳戶會保留此範例中管線的輸入和輸出資料。HDInsight 連結服務會用來執行此範例中管線活動指定的 Hive 指令碼。您必須識別案例中使用的資料存放區/計算服務，並建立連結的服務將這些服務連結到 Data Factory。 
3. 兩個 (輸入/輸出) 資料集：AzureBlobInput 和 AzureBlobOutput。這些資料集代表 Hive 處理的輸入和輸出資料。這些資料集是您稍早在本教學課程中建立的 **StorageLinkedService**。連結的服務會指向 Azure 儲存體帳戶，而資料集則會指定保留輸入和輸出資料儲存體中的容器、資料夾和檔案名稱。   

按一下 [使用 Data Factory 編輯器] 索引標籤，切換到詳細說明此範本中使用的 JSON 屬性的文章。

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
	        "blobInputDataset": "AzureBlobInput",
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
	                    "name": "[variables('blobInputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "type": "AzureBlob",
						        "linkedServiceName": "StoraegLinkedService",
						        "typeProperties": {
						            "fileName": "input.log",
						            "folderPath": "adfgetstarted/inputdata",
						            "format": {
						                "type": "TextFormat",
						                "columnDelimiter": ","
						            }
						        },
						        "availability": {
						            "frequency": "Month",
						            "interval": 1
						        },
						        "external": true,
						        "policy": {}
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
						        "published": false,
						        "type": "AzureBlob",
						        "linkedServiceName": "StorageLinkedService",
						        "typeProperties": {
						            "folderPath": "adfgetstarted/partitioneddata",
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
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
	                        ],
	                        "type": "datapipelines",
	                        "name": "[variables('dataFactoryName')]",
	                        "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "description": "My first Azure Data Factory pipeline",
						        "activities": [
						            {
						                "type": "HDInsightHive",
						                "typeProperties": {
						                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
						                    "scriptLinkedService": "StorageLinkedService",
						                    "defines": {
		                        				"inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
		                        				"partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
						                    }
						                },
						                "inputs": [
						                    {
						                        "name": "AzureBlobInput"
						                    }
						                ],
						                "outputs": [
						                    {
						                        "name": "AzureBlobOutput"
						                    }
						                ],
						                "policy": {
						                    "concurrency": 1,
						                    "retry": 3
						                },
						                "scheduler": {
						                    "frequency": "Month",
						                    "interval": 1
						                },
						                "name": "RunSampleHiveActivity",
						                "linkedServiceName": "HDInsightOnDemandLinkedService"
						            }
						        ],
						        "start": "2014-02-01T00:00:00Z",
						        "end": "2014-02-02T00:00:00Z",
						        "isPaused": false
						    }
	                    }
	            ]
	        }
	    ]
	}


請注意：

- Data Factory 會以上述 JSON 為您建立以 Windows 為基礎的 HDInsight 叢集。您也可以讓它建立以 Linux 為基礎的 HDInsight 叢集。如需詳細資訊，請參閱 [HDInsight 隨選連結服務](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)。 
- 您可以使用自己的 HDInsight 叢集，不必使用隨選的 HDInsight 叢集。如需詳細資訊，請參閱 [HDInsight 連結服務](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)。
- HDInsight 叢集會在您於 JSON 中指定的 Blob 儲存體 (linkedServiceName) 建立預設容器。HDInsight 不會在刪除叢集時刪除此容器。原先的設計就是如此。使用 HDInsight 隨選連結服務時，除非有現有的即時叢集 (timeToLive)，否則每當需要處理配量時，就會建立 HDInsight 叢集，並在處理完成時予以刪除。

	隨著處理的配量越來越多，您會在 Azure Blob 儲存體中看到許多容器。如果在疑難排解作業時不需要這些容器，建議您加以刪除以降低儲存成本。這些容器的名稱遵循下列模式："adf**yourdatafactoryname**-linkedservicename-datetimestamp"。請使用 [Microsoft 儲存體總管](http://storageexplorer.com/)等工具刪除 Azure Blob 儲存體中的容器。

如需詳細資訊，請參閱 [HDInsight 隨選連結服務](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)。

> [AZURE.NOTE] 您可以在 [Github](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json) 上找到可供建立 Azure Data Factory 的其他 ARM 範本範例。

## 步驟 2：使用 ARM 範本部署 Data Factory 實體

1. 開啟 Azure PowerShell 並執行下列命令。將 Azure PowerShell 維持在開啟狀態，直到本教學課程結束為止。如果您關閉並重新開啟，則需要再次執行這些命令。
	- 執行 **Login-AzureRmAccount**，並輸入您用來登入 Azure 入口網站的使用者名稱和密碼。  
	- 執行 **Get-AzureSubscription** 以檢視此帳戶的所有訂用帳戶。
	- 執行 **Select-AzureSubscription SubscriptionName** 以選取您想要使用的訂用帳戶。此訂用帳戶應該與您在 Azure 入口網站中使用的相同。
1. 執行下列命令，使用您在步驟 1 中建立的 ARM 範本來部署 Data Factory 實體。 

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## 監視管線
 
1.	登入 [Azure 入口網站](https://portal.azure.com/)後，按一下 [瀏覽]，然後選取 [Data Factory]。![瀏覽 -> Data Factory](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	在 [Data Factory] 刀鋒視窗中，按一下您建立的 Data Factory (**TutorialFactoryARM**)。	
2.	在您 Data Factory 的 [Data Factory] 刀鋒視窗中，按一下 [圖表]。![圖表磚](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	在 [圖表檢視] 中，您會看到管線的概觀，以及在本教學課程中使用的資料集。
	
	![圖表檢視](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. 在 [圖表檢視] 中，按兩下 **AzureBlobOutput** 資料集。您會看到目前正在處理的配量。

	![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. 處理完成時，您會看到配量處於 [就緒] 狀態。請注意，建立隨選 HDInsight 叢集通常需要一些時間 (大約 20 分鐘)。 

	![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)	
10. 當配量處於**就緒**狀態時，檢查您 Blob 儲存體中 **adfgetstarted** 容器內 **partitioneddata** 資料夾的輸出資料。  
 

<!---HONumber=AcomDC_0309_2016-->