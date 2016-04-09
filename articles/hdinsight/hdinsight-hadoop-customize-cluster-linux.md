<properties
	pageTitle="使用指令碼動作來自訂 HDInsight 叢集 | Microsoft Azure"
	description="學習如何使用指令碼動作在以 Linux 為基礎的 HDInsight 叢集上新增自訂元件。指令碼動作是叢集節點上的 Bash 指令碼，可用來自訂叢集組態，或新增其他服務和公用程式，如 Hue、Solr 或 R。"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/17/2016"
	ms.author="larryfr"/>

# 使用指令碼動作自訂 Linux 型 HDInsight 叢集

HDInsight 提供一個稱為 [指令碼動作]的組態選項，此指令碼動作可叫用用於自訂叢集的自訂指令碼。這些指令碼可使用於叢集建立期間或已在執行中的叢集上，而且用來安裝其他元件或變更組態設定。

> [AZURE.NOTE] 只有以 Linux 為基礎的 HDInsight 叢集能夠在已在執行中的叢集上使用指令碼動作。如需有關搭配以 Windows 為基礎的叢集使用指令碼動作的詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集 (Windows)](hdinsight-hadoop-customize-cluster.md)。

## 了解指令碼動作

指令碼動作只是一個您會提供 URL 和參數的 Bash 指令碼，然後該指令碼會在 HDInsight 叢集節點上執行。以下是指令碼動作的特性和功能。

* 可以限制為__只在特定節點類型上執行__，例如前端節點或背景工作節點。

* 可以是__持續性__或__臨時性__。

    __持續性__指令碼是套用至背景工作節點的指令碼，將在相應增加叢集時所建立的新節點上自動執行。

    持續性指令碼也會將變更套用至其他節點類型 (例如前端節點)，但從功能的觀點來看，保存指令碼的唯一理由，就是它會套用到相應放大叢集時所建立的新背景工作節點。

    > [AZURE.IMPORTANT] 持續性指令碼動作必須有唯一的名稱。

    __臨時性__指令碼不會持續保存；不過，您隨後可以將臨時性指令碼升級為持續性指令碼，或將持續性指令碼降級為臨時性指令碼。

    > [AZURE.IMPORTANT] 建立叢集期間使用的指令碼動作會自動保存下來。
    >
    > 即使您特別指出應予保存，仍然不會保存失敗的指令碼。

* 可以接受指令碼在執行期間所使用的__參數__。

* 在叢集節點上是以__根層級權限__執行。

* 可以透過 __Azure 入口網站__、__Azure PowerShell__ 或 __HDInsight .NET SDK__ 使用。

> [AZURE.IMPORTANT] 沒有任何自動方式可復原指令碼動作所做的變更。如果您需要還原指令碼的效果，您必須了解已做了哪些變更並手動進行還原 (或提供可進行還原的指令碼動作)。

為了協助了解哪些指令碼已套用到叢集，以及判斷可供升級或降級的指令碼 ID，叢集會保留所有已執行指令碼的歷程記錄。

### 叢集建立程序中的指令碼動作

在叢集建立期間使用的指令碼動作與在現有叢集上執行的指令碼動作稍微不同︰

* 此指令碼會__自動保存__。

* 指令碼__失敗__可能會導致叢集建立程序失敗。

下圖說明在建立程序期間執行指令碼動作的時間：

![HDInsight 叢集自訂和叢集建立期間的階段][img-hdi-cluster-states]

當設定 HDInsight 時，已執行指令碼。在此階段，指令碼會以平行方式在叢集中所有指定的節點上執行，在節點上會以根權限執行。

> [AZURE.NOTE] 因為指令碼是以根層級權限在叢集節點上執行，所以您可以執行作業，例如停止和啟動服務，包括 Hadoop 相關服務。如果您停止服務，您必須在指令碼完成執行之前，確定 Ambari 服務及其他 Hadoop 相關服務已啟動並且正在執行。這些服務必須在叢集建立時，成功地判斷叢集的健康情況和狀態。

在叢集建立期間，您可以指定多個指令碼動作，而這些指令碼動作會依其指定的順序被叫用。

> [AZURE.IMPORTANT] 指令碼動作必須在 60 分鐘內完成，否則就會逾時。在叢集佈建期間，會同時執行指令碼與其他安裝和設定程序。與在您開發環境中的執行時間相較，爭用 CPU 時間和網路頻寬等資源可能會導致指令碼需要較長的時間才能完成。
>
> 若要讓執行指令碼所花費的時間降到最低，請避免從原始程式碼下載和編譯應用程式之類的工作。您應預先編譯相關應用程式，並將二進位檔儲存在 Azure Blob 儲存體中，這樣可讓其能夠快速地下載到叢集。

###執行中叢集上的指令碼動作

不同於在叢集建立期間使用的指令碼動作，在執行中叢集上執行的指令碼發生失敗並不會自動導致叢集變更為失敗的狀態。指令碼完成後，叢集應該會回到「執行中」狀態。

> [AZURE.IMPORTANT] 這並非表示您正在執行的叢集不受發生錯誤的指令碼所影響。例如，指令碼可以刪除叢集所需的檔案，變更讓服務失敗的組態等。
>
> 指令碼動作會以根權限執行，因此您應該先確定您了解指令碼的作用，再將它套用到您的叢集。

將指令碼套用到叢集時，叢集狀態會從 [執行中] 變更為 [已接受]，然後是 [HDInsight 組態]，而成功指令碼最終會回到 [執行中]。指令碼狀態會記錄在指令碼動作歷程記錄中，您可以使用此狀態來判斷指令碼是成功或失敗。例如，`Get-AzureRmHDInsightScriptActionHistory` PowerShell Cmdlet 可用來檢視指令碼的狀態。這會傳回類似以下的資訊：

    ScriptExecutionId : 635918532516474303
    StartTime         : 2/23/2016 7:40:55 PM
    EndTime           : 2/23/2016 7:41:05 PM
    Status            : Succeeded

## 範例指令碼動作指令碼

從 Azure 入口網站、Azure PowerShell 或 HDInsight .NET SDK，可以使用指令碼動作指令碼。HDInsight 提供一些指令碼以在 HDInsight 叢集上安裝下列元件：

名稱 | 指令碼
----- | -----
**安裝色調** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh。請參閱[在 HDInsight 叢集上安裝及使用色調](hdinsight-hadoop-hue-linux.md)。
**安裝 R** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh。請參閱[在 HDInsight 叢集上安裝及使用 R](hdinsight-hadoop-r-scripts-linux.md)。
**安裝 Solr** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh。請參閱[在 HDInsight 叢集上安裝及使用 Solr](hdinsight-hadoop-solr-install-linux.md)。
**安裝 Giraph** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh。請參閱[在 HDInsight 叢集上安裝及使用 Giraph](hdinsight-hadoop-giraph-install-linux.md)。
| **預先載入 Hive 程式庫** | https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh。請參閱[在 HDInsight 叢集上新增 Hive 程式庫](hdinsight-hadoop-add-hive-libraries.md) |

## 在建立叢集期間使用指令碼動作

本節提供您可以在建立 HDInsight 叢集時使用指令碼動作的不同方式範例 - 從 Azure 入口網站、使用 ARM 範本、使用 PowerShell Cmdlet，以及使用 .NET SDK。

### 在建立叢集期間從 Azure 入口網站使用指令碼動作

1. 依[在 HDInsight 建立 Hadoop 叢集](hdinsight-provision-clusters.md#portal)中的描述開始建立叢集。

2. 在 [選擇性組態] 的 [指令碼動作] 刀鋒視窗上，按一下 [加入指令碼動作] 以提供有關指令碼動作的詳細資料，如下所示：

	![使用指令碼動作以自訂叢集](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png)

	| 屬性 | 值 |
	| -------- | ----- |
	| 名稱 | 指定指令碼動作的名稱。 |
	| 指令碼 URI | 對自訂叢集所叫用的指令碼指定 URI。 |
	| Head/Worker | 指定執行自訂指令碼的節點 (**Head**、**Worker** 或 **ZooKeeper**)。 |
	| 參數 | 如果指令碼要求，請指定參數。 |

	請按 ENTER 加入一個以上的指令碼動作，以在叢集上安裝多個元件。

3. 按一下 [選取] 以儲存組態並繼續建立叢集。

### 從 Azure 資源管理員範本使用指令碼動作

在本節中，我們使用 Azure 資源管理員 (ARM) 範本來建立 HDInsight 叢集，並且也使用指令碼動作在叢集上安裝自訂元件 (在此範例中為 R)。本節提供範例 ARM 範本以使用指令碼動作建立叢集。

#### 開始之前

* 如需設定工作站以執行 HDInsight Powershell Cmdlet 的相關資訊，請參閱[安裝並設定 Azure PowerShell](../powershell-install-configure.md)。
* 如需如何建立 ARM 範本的指示，請參閱[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。
* 如果您之前未曾搭配使用Azure PowerShell 與資源管理員，請參閱[將 Azure PowerShell 與 Azure 資源管理員搭配使用](../powershell-azure-resource-manager.md)。

#### 使用指令碼動作建立叢集

1. 將下列範本複製到您的電腦上的位置。此範本會在叢集中的前端節點和背景工作角色節點上安裝 R。您也可以確認 JSON 範本是否有效。將您的範本內容貼至 [JSONLint](http://jsonlint.com/)，這是一個線上 JSON 驗證器工具。

			{
		    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
		    "contentVersion": "1.0.0.0",
		    "parameters": {
		        "clusterLocation": {
		            "type": "string",
		            "defaultValue": "West US",
		            "allowedValues": [ "West US" ]
		        },
		        "clusterName": {
		            "type": "string"
		        },
		        "clusterUserName": {
		            "type": "string",
		            "defaultValue": "admin"
		        },
		        "clusterUserPassword": {
		            "type": "securestring"
		        },
		        "sshUserName": {
		            "type": "string",
		            "defaultValue": "username"
		        },
		        "sshPassword": {
		            "type": "securestring"
		        },
		        "clusterStorageAccountName": {
		            "type": "string"
		        },
		        "clusterStorageAccountResourceGroup": {
		            "type": "string"
		        },
		        "clusterStorageType": {
		            "type": "string",
		            "defaultValue": "Standard_LRS",
		            "allowedValues": [
		                "Standard_LRS",
		                "Standard_GRS",
		                "Standard_ZRS"
		            ]
		        },
		        "clusterStorageAccountContainer": {
		            "type": "string"
		        },
		        "clusterHeadNodeCount": {
		            "type": "int",
		            "defaultValue": 1
		        },
		        "clusterWorkerNodeCount": {
		            "type": "int",
		            "defaultValue": 2
		        }
		    },
		    "variables": {
		    },
		    "resources": [
		        {
		            "name": "[parameters('clusterStorageAccountName')]",
		            "type": "Microsoft.Storage/storageAccounts",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-05-01-preview",
		            "dependsOn": [ ],
		            "tags": { },
		            "properties": {
		                "accountType": "[parameters('clusterStorageType')]"
		            }
		        },
		        {
		            "name": "[parameters('clusterName')]",
		            "type": "Microsoft.HDInsight/clusters",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-03-01-preview",
		            "dependsOn": [
		                "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
		            ],
		            "tags": { },
		            "properties": {
		                "clusterVersion": "3.2",
		                "osType": "Linux",
		                "clusterDefinition": {
		                    "kind": "hadoop",
		                    "configurations": {
		                        "gateway": {
		                            "restAuthCredential.isEnabled": true,
		                            "restAuthCredential.username": "[parameters('clusterUserName')]",
		                            "restAuthCredential.password": "[parameters('clusterUserPassword')]"
		                        }
		                    }
		                },
		                "storageProfile": {
		                    "storageaccounts": [
		                        {
		                            "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
		                            "isDefault": true,
		                            "container": "[parameters('clusterStorageAccountContainer')]",
		                            "key": "[listKeys(resourceId(parameters('clusterStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).key1]"
		                        }
		                    ]
		                },
		                "computeProfile": {
		                    "roles": [
		                        {
		                            "name": "headnode",
		                            "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
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
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        }
		                    ]
		                }
		            }
		        }
		    ],
		    "outputs": {
		        "cluster":{
		            "type" : "object",
		            "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
		        }
		    }
		}

2. 啟動 Azure PowerShell 並且登入您的 Azure 帳戶。提供您的認證之後，命令會傳回您的帳戶的相關資訊。

		Add-AzureRmAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. 如果您有多個訂用帳戶，請提供您想要用於部署的訂用帳戶識別碼。

		Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] 您可以使用 `Get-AzureRmSubscription` 來取得與您帳戶關聯的所有訂用帳戶清單，同時也包含每個訂用帳戶的訂閱 Id。

5. 如果您沒有現有資源群組，請建立新的資源群組。提供您的解決方案所需的資源群組名稱和位置。隨即傳回新資源群組的摘要。

		New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. 若要建立資源群組的新部署，請執行 **New-AzureRmResourceGroupDeployment** 命令，並提供必要的參數。參數會包含您部署的名稱、資源群組的名稱、您建立之範本的路徑或 URL。如果您的範本需要任何參數，您也必須傳遞這些參數。在此案例中，用來在叢集上安裝 R 的指令碼動作不需要任何參數。


		New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	系統會提示您針對範本中定義的參數提供值。

7. 部署資源群組之後，您會看到部署的摘要。

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. 如果您的部署失敗，您可以使用下列 Cmdlet 來取得失敗的相關資訊。

		Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

### 在建立叢集期間從 Azure PowerShell 使用指令碼動作

本節中，我們使用 [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) Cmdlet，使用指令碼動作叫用指令碼以自訂叢集。在繼續之前，請確認您已安裝和設定 Azure PowerShell。如需設定工作站以執行 HDInsight PowerShell Cmdlet 的相關資訊，請參閱[安裝並設定 Azure PowerShell](../powershell-install-configure.md)。

執行下列步驟：

1. 開啟 Azure PowerShell 主控台並使用下列命令來登入您的 Azure 訂用帳戶，並宣告一些 PowerShell 變數︰

        # LOGIN TO ZURE
        Login-AzureRmAccount

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionId = "<SubscriptionId>"		# ID of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
        $resourceGroupName = "<ResourceGroupName>"      # The resource group that the HDInsight cluster will be created in

2. 指定組態值 (例如叢集中的節點) 和要使用的預設儲存體。

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureRmSubscription -SubscriptionId $subscriptionId
		$config = New-AzureRmHDInsightClusterConfig
		$config.DefaultStorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccountKey=$storageAccountKey

3. 使用 **Add-AzureRmHDInsightScriptAction** Cmdlet 以叫用指令碼。下列範例使用會在叢集上安裝 R 的指令碼：

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
		$config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh

	**Add-AzureRmHDInsightScriptAction** Cmdlet 可接受下列參數：

	| 參數 | 定義 |
	| --------- | ---------- |
	| 設定 | 要在其中新增指令碼動作資訊的組態物件。 |
	| 名稱 | 指令碼動作的名稱。 |
	| NodeType | 指定執行自訂指定碼的節點。有效值為 **HeadNode** (在前端節點上安裝)、**WorkerNode** (在所有資料節點上安裝)，或 **ZookeeperNode** (在 zookeeper 節點上安裝)。 |
	| 參數 | 指令碼所需的參數。 |
	| Uri | 指定所執行之指令碼的 URI。 |

4. 為此叢集設定 admin/HTTPS 使用者：

        $httpCreds = get-credential

    系統顯示提示時，請輸入 'admin' 做為名稱，並提供密碼。

5. 設定 SSH 認證：

        $sshCreds = get-credential

    出現提示時，請輸入 SSH 使用者名稱和密碼。如果您想要使用認證 (而非密碼) 來保護 SSH 帳戶，請使用空白密碼並將 `$sshPublicKey` 設為您想使用之憑證公開金鑰的內容。例如：

        $sshPublicKey = Get-Content .\path\to\public.key -Raw

4. 最後，建立叢集：

        New-AzureRmHDInsightCluster -config $config -clustername $clusterName -DefaultStorageContainer $containerName -Location $location -ResourceGroupName $resourceGroupName -ClusterSizeInNodes $clusterNodes -HttpCredential $httpCreds -SshCredential $sshCreds -OSType Linux

    如果您是使用公開金鑰來保護 SSH 帳戶，您也必須指定 `-SshPublicKey $sshPublicKey` 做為參數。

建立叢集可能需要幾分鐘的時間。

### 在建立叢集期間從 HDInsight .NET SDK 使用指令碼動作

HDInsight .NET SDK 提供用戶端程式庫，讓您輕鬆地從 .NET 應用程式使用 HDInsight。如需指令碼範例，請參閱[使用 .NET SDK 在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action)。

## 將指令碼動作套用到執行中的叢集

本節提供您可以將指令碼動作套用到執行中 HDInsight 叢集的不同方式範例 - 從 Azure 入口網站、使用 PowerShell Cmdlet，以及使用 .NET SDK。

### 從 Azure 入口網站將指令碼動作套用到執行中的叢集

1. 從 [Azure 入口網站](https://portal.azure.com)，選取您的 HDInsight 叢集。

2. 從 HDInsight 叢集刀鋒視窗，選取 [設定]。

    ![設定圖示](./media/hdinsight-hadoop-customize-cluster-linux/settingsicon.png)

3. 從 [設定] 刀鋒視窗，選取 [指令碼動作]。

    ![指令碼動作連結](./media/hdinsight-hadoop-customize-cluster-linux/settings.png)

4. 從 [指令碼動作] 刀鋒視窗的頂端，選取 [提交新的]。

    ![提交新的圖示](./media/hdinsight-hadoop-customize-cluster-linux/newscriptaction.png)

5. 從 [新增指令碼動作] 刀鋒視窗，輸入下列資訊。

    * __名稱__：要用於此指令碼動作的易記名稱。在此範例中是 `R`。
    * __指令碼 URI__︰指令碼的 URI。在此範例中，`https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh`
    * __前端__、__背景工作__ 和 __Zookeeper__︰勾選應該套用這個指令碼的節點。在此範例中，會勾選 [前端] 和 [背景工作]。
    * __參數__：如果指令碼接受參數，請在此輸入參數。
    * __已保存__︰如果您要保存指令碼，請勾選此項目，此項目也會在您相應增加叢集時套用到新的背景工作節點。

6. 最後，使用 [建立] 按鈕將指令碼套用到叢集。

### 從 Azure PowerShell 將指令碼動作套用到執行中的叢集

在繼續之前，請確認您已安裝和設定 Azure PowerShell。如需設定工作站以執行 HDInsight PowerShell Cmdlet 的相關資訊，請參閱[安裝並設定 Azure PowerShell](../powershell-install-configure.md)。

1. 開啟 Azure PowerShell 主控台並使用下列命令來登入您的 Azure 訂用帳戶，並宣告一些 PowerShell 變數︰

        # LOGIN TO ZURE
        Login-AzureRmAccount

		# PROVIDE VALUES FOR THESE VARIABLES
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
        $saName = "<ScriptActionName>"                  # Name of the script action
        $saURI = "<URI to the script>"                  # The URI where the script is located
        $nodeTypes = "headnode", "workernode"


2. 使用下列命令將指令碼套用到叢集：

        Submit-AzureRmHDInsightScriptAction -ClusterName $clusterName -Name $saName -Uri $saURI -NodeTypes $nodeTypes -PersistOnSuccess

    作業完成後，您應該會收到類似以下的資訊：

        OperationState  : Succeeded
        ErrorMessage    :
        Name            : R
        Uri             : https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
        Parameters      :
        NodeTypes       : {HeadNode, WorkerNode}

### 從 HDInsight .NET SDK 將指令碼動作套用到執行中的叢集

如需使用 .NET SDK 將指令碼套用到叢集的範例，請參閱 [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)。

## 檢視歷程記錄、升級和降級指令碼動作

### 使用 Azure 入口網站

1. 從 [Azure 入口網站](https://portal.azure.com)，選取您的 HDInsight 叢集。

2. 從 HDInsight 叢集刀鋒視窗，選取 [設定]。

    ![設定圖示](./media/hdinsight-hadoop-customize-cluster-linux/settingsicon.png)

3. 從 [設定] 刀鋒視窗，選取 [指令碼動作]。

    ![指令碼動作連結](./media/hdinsight-hadoop-customize-cluster-linux/settings.png)

4. [指令碼動作] 刀鋒視窗上會顯示持續性指令碼清單，以及套用至叢集的指令碼歷程記錄。在以下的螢幕擷取畫面中，您可以看到 Solr 指令碼已在此叢集上執行，但尚未保存任何指令碼動作。

    ![指令碼動作刀鋒視窗](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionhistory.png)

5. 選取歷程記錄中的指令碼，將會顯示此指令碼的 [屬性] 刀鋒視窗。從刀鋒視窗的頂端，您可以重新執行指令碼或將它升級。

    ![指令碼動作屬性刀鋒視窗](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionproperties.png)

6. 您也可以使用 [指令碼動作] 刀鋒視窗上項目右邊的__...__ 來執行動作，例如重新執行、保存或刪除 (適用於持續性動作)。

    ![指令碼動作...使用情況](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### 使用 Azure PowerShell

| 使用下列... | 來 ... |
| ----- | ----- |
| Get-AzureRmHDInsightPersistedScriptAction | 擷取持續性指令碼動作的資訊 |
| Get-AzureRmHDInsightScriptActionHistory | 擷取已套用到叢集的指令碼動作歷程記錄，或特定指令碼的詳細資料 |
| Set-AzureRmHDInsightPersistedScriptAction | 將臨時性指令碼動作升級為持續性指令碼動作 |
| Remove-AzureRmHDInsightPersistedScriptAction | 將持續性指令碼動作降級為臨時性指令碼動作 |

> [AZURE.IMPORTANT] 使用 `Remove-AzureRmHDInsightPersistedScriptAction` 不會復原指令碼所執行的動作，只會移除已保存旗標，讓指令碼不會對新增至叢集的新背景工作節點執行。

下列範例指令碼示範如何使用 Cmdlet 來升級而後降級指令碼。

    # Get a history of scripts
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster

    # From the list, we want to get information on a specific script
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Promote this to a persisted script
    # Note: the script must have a unique name to be promoted
    # if the name is not unique, you will receive an error
    Set-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Demote the script back to ad hoc
    # Note that demotion uses the unique script name instead of
    # execution ID.
    Remove-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -Name "Install Giraph"

### 使用 HDInsight .NET SDK

如需使用 .NET SDK 來擷取叢集中的指令碼歷程記錄、升級或降級指令碼，請參閱 [TBD]()。

## 疑難排解

您可以使用 Ambari Web UI 來檢視指令碼動作所記錄的資訊。不過，如果在叢集建立期間使用了指令碼，而叢集建立因為指令碼錯誤而失敗，則與該叢集相關聯的預設儲存體帳戶中也會有記錄檔。本節提供關於如何使用這兩個選項擷取記錄檔的資訊。

### 使用 Ambari Web UI

1. 在您的瀏覽器中，瀏覽至 https://CLUSTERNAME.azurehdinsight.net。將 CLUSTERNAME 取代為 HDInsight 叢集的名稱。

	出現提示時，輸入管理帳戶名稱 (admin) 和叢集的密碼。您可能必須在 Web 表單中重新輸入系統管理員認證。

2. 在頁面頂端的列中，選取 __ops__ 項目。這會顯示透過 Ambari 在叢集上執行的目前和先前作業的清單。

	![Ambari Web UI 列與選取的 ops](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. 尋找在 [作業] 欄位中有 __run\_customscriptaction__ 的項目。這些項目是在執行指令碼動作時建立的。

	![作業的螢幕擷取畫面](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	選取此項目，並且向下鑽研連結以檢視在叢集上執行指令碼時，產生的 STDOUT 和 STDERR 輸出。

### 從預設的儲存體帳戶存取記錄檔

如果叢集建立因為指令碼動作中的錯誤而失敗，仍可從與該叢集相關聯的預設儲存體帳戶直接存取指令碼動作記錄檔。

* 儲存體記錄檔位於 `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`。

	![作業的螢幕擷取畫面](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

	在其下，記錄檔會個別針對前端節點、背景工作節點和 Zookeeper 節點進行組織。部分範例如下：
	* 前端節點 - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`
	* 背景工作節點 - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`
	* Zookeeper 節點 - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* 對應主機的所有 stdout 和 stderr 都會上傳至儲存體帳戶。每個指令碼動作分別有一個 **output-*.txt** 和  **errors-\*.txt** 。output-*.txt 檔案包含在主機上執行之指令碼的 URI 相關資訊。例如

		'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* 您有可能重複建立具有相同名稱的指令碼動作叢集。在這種情況下，您可以根據 DATE 資料夾名稱來區分相關的記錄檔。例如，在不同的日期為叢集 (mycluster) 建立的資料夾結構將是：
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* 如果您在同一天建立具有相同名稱的指令碼動作叢集，您可以使用唯一的前置詞來識別相關的記錄檔。

* 如果您是在那一天結束時建立叢集，則記錄檔可能會橫跨兩天。在這種情況下，您會看到相同的叢集有兩個不同的日期資料夾。

* 將記錄檔上傳至預設容器可能需要 5 分鐘，特別是大型叢集。因此，如果您想要存取記錄檔，則不應在指令碼動作失敗時立即刪除叢集。


## 支援在 HDInsight 叢集上使用開放原始碼軟體

Microsoft Azure HDInsight 服務是彈性的平台，可讓您使用以 Hadoop 形成之開放原始碼技術的生態系統，在雲端中建置巨量資料應用程式。Microsoft Azure 提供對開放原始碼技術的一般層級支援，如 [Azure 支援常見問題集網站](https://azure.microsoft.com/support/faq/)的＜**支援範圍**＞章節中所述。HDInsight 服務對於某些元件提供額外層級的支援，如下所述。

HDInsight 服務中有兩種類型的開放原始碼元件可用：

- **內建元件** - 這些元件預先安裝在 HDInsight 叢集上，並且提供叢集的核心功能。例如，YARN ResourceManager、Hive 查詢語言 (HiveQL) 及 Mahout 程式庫都屬於這個類別。叢集元件的完整清單可於 [HDInsight 所提供 Hadoop 叢集版本的新功能](hdinsight-component-versioning.md)中取得。

- **自訂元件** - 身為叢集使用者的您可以安裝社群中可用或是您建立的任何元件，或者在工作負載中使用。

> [AZURE.WARNING] 透過 HDInsight 叢集提供的元件會受到完整支援，且 Microsoft 支援服務將協助釐清與解決這些元件的相關問題。
>
> 自訂元件則獲得商務上合理的支援，協助您進一步疑難排解問題。如此可能會進而解決問題，或要求您利用可用管道，以找出開放原始碼技術，從中了解該技術的深度專業知識。例如，有許多社群網站可以使用，像是：[HDInsight 的 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/zh-TW/home?forum=hdinsight)、[http://stackoverflow.com](http://stackoverflow.com)。另外，Apache 專案在 [http://apache.org](http://apache.org) 上有專案網站，例如 [Hadoop](http://hadoop.apache.org/)。

HDInsight 服務提供數種方式以使用自訂元件。無論元件如何使用或如何安裝在叢集上，都適用相同層級的支援。以下是自訂元件可用於 HDInsight 叢集之最常見方式的清單：

1. 工作提交 - Hadoop 或其他類型的工作，執行或使用可以提交給叢集的自訂元件。

2. 叢集自訂 - 在叢集建立期間，您可以指定額外設定和將會安裝在叢集節點上的自訂元件。

3. 範例 - 對於熱門自訂元件，Microsoft 和其他提供者可能會提供如何在 HDInsight 叢集上使用這些元件的範例。提供這些範例，但是沒有支援。

##疑難排解

###歷程記錄不會顯示在叢集建立期間使用的指令碼

如果您的叢集在 2016 年 3 月 15 日之前建立，您在叢集建立期間所用的任何指令碼的指令碼動作歷程記錄中可能看不到任何項目。不過，如果您在 2016 年 3 月 15 日之後調整叢集大小，在叢集建立期間使用的指令碼將會出現在歷程記錄中，因為它們在調整大小作業過程中套用到叢集中的新節點。

有兩種例外狀況：

* 如果您的叢集在 2015 年 9 月 1 日之前建立。這就是引進指令碼動作的時候，因此在此日期之前建立的叢集可能都未使用指令碼動作建立叢集。

* 如果您在叢集建立期間使用了多個指令碼動作，並將相同的名稱、相同的 URI 使用於多個指令碼，但將不同的參數使用於多個指令碼。在這些情況下，您會收到下列錯誤。

    由於現有指令碼的指令碼名稱發生衝突，所以無法在此叢集上執行任何新的指令碼動作。建立叢集時所提供的指令碼名稱全都必須是唯一的名稱。在調整大小時，仍然會執行現有的指令碼。

## 後續步驟

請參閱下列項目，以取得建立和使用指令碼以自訂叢集時的資訊和範例：

- [開發 HDInsight 的指令碼動作指令碼](hdinsight-hadoop-script-actions-linux.md)
- [在 HDInsight 叢集上安裝及使用 R](hdinsight-hadoop-r-scripts-linux.md)
- [在 HDInsight 叢集上安裝及使用 Solr](hdinsight-hadoop-solr-install-linux.md)
- [在 HDInsight 叢集上安裝及使用 Giraph](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "叢集建立期間的階段"

<!---HONumber=AcomDC_0323_2016-->