<properties
   	pageTitle="在 HDInsight 中使用 cURL 與 Azure REST API 在 Linux 上建立 Hadoop、HBase 或 Storm 叢集 | Microsoft Azure"
   	description="了解如何使用 cURL、Azure 資源管理員範本以及 Azure REST API 來建立以 Linux 為基礎的 HDInsight 叢集。您可以指定叢集類型 (Hadoop、HBase、或 Storm) 或使用指令碼來安裝自訂元件。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="02/05/2016"
   	ms.author="larryfr"/>

#在 HDInsight 中使用 cURL 和 Azure REST API 建立以 Linux 為基礎的叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-selector-create-clusters.md)]

Azure REST API 可讓您對裝載於 Azure 平台的服務執行管理作業，包括建立新的資源，例如以 Linux 為基礎的 HDInsight 叢集。本文件中，您將了解如何建立 Azure 資源管理員範本來設定 HDInsight 叢集和相關聯的儲存體，然後使用 cURL 將範本部署到 Azure REST API，以建立新的 HDInsight 叢集。

> [AZURE.IMPORTANT] 本文件中的步驟使用 HDInsight 叢集的背景工作節點預設數目 (4)。如果您在建立叢集時或在建立後調整叢集時規劃有 32 個以上的背景工作節點，則您必須選取具有至少 8 個核心和 14 GB ram 的前端節點大小。
>
> 如需節點大小和相關成本的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

###必要條件

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- __Azure CLI__。Azure CLI 用來建立服務主體，此主體再用於為 Azure REST API 的要求產生驗證權杖。

    如需安裝 CLI 的相關資訊，請參閱[安裝 Azure CLI](../xplat-cli-install.md)。

- __cURL__。此公用程式由封裝管理系統提供，也可以從 [http://curl.haxx.se/](http://curl.haxx.se/) 下載。

    > [AZURE.NOTE] 如果您使用 PowerShell 執行本文件中的命令，您必須先移除依預設建立的 `curl` 別名。當您從 PowerShell 提示字元使用 `curl` 命令時，此別名會使用 PowerShell Cmdlet 的 Invoke-WebRequest，而不是 cURL，造成本文件中使用的許多命令傳回錯誤。
    > 
    > 如果要移除此別名，請從 PowerShell 提示字元執行下列命令：
    >
    > ```Remove-item alias:curl`
    >
    > 移除別名後，就應該能夠使用已安裝在您系統上的 cURL 版本。

##建立範本

Azure 資源管理範本是描述__資源群組__與其中所有資源 (例如 HDInsight) 的 JSON 文件。 這個以範本為基礎的方法可讓您在一個範本中定義 HDInsight 所需的所有資源，並透過可將變更套用至群組的__部署__，整體管理群組的變更。

範本通常分成兩個部分：範本自己，以及依您的組態而填入特定值的參數檔。例如，叢集名稱、系統管理員名稱和密碼。直接使用 REST API 時，您必須將這些值合併到一個檔案中。此 JSON 文件的格式為：

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

例如，以下是 [https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password) 提供的範本和參數檔合併工具，它會建立以 Linux 為基礎的叢集，並使用密碼保護 SSH 使用者帳戶。

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
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
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
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
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

本文件中的步驟引用此範例。在文件的最後，以您要用於叢集的值取代 __Parameters__ 區段中的預留位置 _value_。

##登入您的 Azure 訂用帳戶

依照[從 Azure 命令列介面 (Azure CLI) 連接到 Azure 訂用帳戶](../xplat-cli-connect.md)中記載的步驟，使用 __login__ 方法連線到您的訂用帳戶。

##建立服務主體

> [AZURE.IMPORTANT] 執行下列連結文件中的步驟時，您必須進行下列變更：
> 
> * 當步驟表示要使用的值為 __reader__ 時，您必須改用 __owner__。這會建立可在訂用帳戶上變更服務的服務主體，而這是建立 HDInsight 叢集所必要的。
>
> 您也必須儲存在此程序中使用的資訊，包括：
> 
> * 訂用帳戶 ID - 使用 `azure account list` 時收到
> * 租用戶 ID - 使用 `azure account list` 時收到
> * 應用程式 ID - 建立服務主體時傳回
> * 服務主體的密碼 - 建立服務主體時使用

執行[使用 Azure 資源管理員驗證服務主體](https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/#authenticate-service-principal-with-password---azure-cli)文件中＜使用密碼驗證服務主體 - Azure CLI＞一節的步驟。這會建立新的服務主體，可用來驗證叢集建立要求。

##取得驗證權杖

使用下列命令從 Azure 取得新的權杖。以建立服務主體時所儲存的資訊取代 __TENANTID__、__APPLICATIONID__ 和 __PASSWORD__：

    curl -X "POST" "https://login.microsoftonline.com/TENANTID/oauth2/token" \
    -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    --data-urlencode "client_id=APPLICATIONID" \
    --data-urlencode "grant_type=client_credentials" \
    --data-urlencode "client_secret=PASSWORD" \
    --data-urlencode "resource=https://management.azure.com/"

如果這個要求成功，您會收到 200 系列的回應，且回應主體會包含 JSON 文件。

> [AZURE.IMPORTANT] 這個要求所傳回的 JSON 文件將包含名為 __access\_token__ 的元素；這個元素的值是存取權杖，必須用來驗證本文件接下來各節所使用的要求。

##建立資源群組

使用下列命令建立新的資源群組。您必須先建立群組，才能建立 HDInsight 叢集等資源。

* 以建立服務主體時所收到的訂用帳戶 ID 取代 __SUBSCRIPTIONID__。
* 以上一步所收到的存取權杖取代 __ACCESSTOKEN__。
* 以您要在其中建立資源群組和資源的資料中心取代 __DATACENTERLOCATION__。例如 'South Central US'。 
* 以您要用於此群組的名稱取代 __GROUPNAME__：

    curl -X "PUT" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json" \\ -d $'{ "location": "DATACENTERLOCATION" }’

如果這個要求成功，您會收到 200 系列的回應，且回應主體會包含 JSON 文件，內含群組的相關資訊。`"provisioningState"` 元素包含的值為 `"Succeeded"`。

##建立部署

使用下列命令將叢集組態 (範本和參數值) 部署到資源群組。

* 以先前使用的值取代 __SUBSCRIPTIONID__ 和 __ACCESSTOKEN__。 
* 以您在上一節建立的資源群組名稱取代 __GROUPNAME__。
* 以您要用於此部署的名稱取代 __DEPLOYMENTNAME__。

    curl -X "PUT" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME/providers/microsoft.resources/deployments/DEPLOYMENTNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json" \\ -d "{set your body string to the template and parameters}"

> [AZURE.NOTE] 如果您已將包含範本和參數的 JSON 文件儲存到檔案，您可以使用下列命令，而不是 `-d "{ template and parameters}"'：
>
> ```--data-binary "@/path/to/file.json"```

如果這個要求成功，您會收到 200 系列的回應，且回應主體會包含 JSON 文件，內含部署作業的相關資訊。

> [AZURE.IMPORTANT] 請注意，部署已送出，但目前尚未完成。部署通常需要大約 15 分鐘才會完成。

##檢查部署的狀態

若要檢查部署的狀態，請使用下列命令：

* 以先前使用的值取代 __SUBSCRIPTIONID__ 和 __ACCESSTOKEN__。 
* 以您在上一節建立的資源群組名稱取代 __GROUPNAME__。

    curl -X "GET" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME/providers/microsoft.resources/deployments/DEPLOYMENTNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json"

這會傳回包含部署作業相關資訊的 JSON 文件。`"provisioningState"` 元素會包含部署的狀態；如果狀態包含的值為 `"Succeeded"`，表示部署已順利完成。現在，您的叢集應該可供使用。

##後續步驟

既然您已成功建立 HDInsight 叢集，請使用下列內容來了解如何使用您的叢集。

###Hadoop 叢集

* [〈搭配 HDInsight 使用 Hivet〉](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [〈搭配 HDInsight 使用 MapReduce〉](hdinsight-use-mapreduce.md)

###HBase 叢集

* [開始在 HDInsight 上使用 HBase](hdinsight-hbase-tutorial-get-started-linux.md)
* [在 HDInsight 上開發適用於 HBase 的 Java 應用程式](hdinsight-hbase-build-java-maven-linux.md)

###Storm 叢集

* [在 HDInsight 上開發適用於 Storm 的 Java 拓撲](hdinsight-storm-develop-java-topology.md)
* [在 HDInsight 上的 Storm 中使用 Python 元件](hdinsight-storm-develop-python-topology.md)
* [在 HDInsight 上使用 Storm 部署和監視拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_0224_2016-->