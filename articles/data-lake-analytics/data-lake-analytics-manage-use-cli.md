<properties 
   pageTitle="使用 Azure 命令列介面管理 Azure 資料湖分析 | Azure" 
   description="了解如何使用 Azure CLI 管理資料湖分析帳戶、資料來源、工作和使用者" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/11/2016"
   ms.author="jgao"/>

# 使用 Azure 命令列介面 (CLI) 管理 Azure 資料湖分析

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

了解如何使用 Azure 管理 Azure 資料湖分析帳戶、資料來源、使用者和工作。若要使用其他工具查看管理主題，請按一下上方選取的索引標籤。

**必要條件**

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- **Azure CLI**。請參閱[安裝和設定 Azure CLI](../xplat-cli-install.md)。
	- 下載並安裝[Azure CLI 工具](https://github.com/MicrosoftBigData/AzureDataLake/releases)的**發行前版本**，才能完成這個示範。
- 使用下列命令進行**驗證**：

		azure login
	如需使用公司或學校帳戶驗證的詳細資訊，請參閱[從 Azure CLI 連線至 Azure 訂用帳戶](xplat-cli-connect.md)。
- 使用下列命令，**切換至 Azure 資源管理員模式**：

		azure config mode arm

**若要列出資料湖存放區和資料湖分析命令：**

	azure datalake store
	azure datalake analytics

<!-- ################################ -->
<!-- ################################ -->
## 管理帳戶

您必須擁有資料湖分析帳戶，才能執行任何資料湖分析工作。與 Azure HDInsight 不同的是，分析帳戶未執行工作時，您無需支付該帳戶的費用。您只需支付執行工作時的費用。如需詳細資訊，請參閱 [Azure 資料湖分析概觀](data-lake-analytics-overview.md)。

###建立帳戶

  	azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"


###更新帳戶

下列命令會更新現有資料湖分析帳戶的屬性
  	
	azure datalake analytics account set "<Data Lake Analytics Account Name>"


###列出帳戶

列出資料湖分析帳戶

	azure datalake analytics account list

列出特定資源群組內的資料湖分析帳戶

	azure datalake analytics account list -g "<Azure Resource Group Name>"

取得特定資料湖分析帳戶的詳細資料

	azure datalake analytics account show -g "<Azure Resource Group Name>" -n "<Data Lake Analytics Account Name>"

###刪除資料湖分析帳戶

  	azure datalake analytics account delete "<Data Lake Analytics Account Name>"


<!-- ################################ -->
<!-- ################################ -->
## 管理帳戶資料來源

資料湖分析目前支援下列資料來源：

- [Azure 資料湖存放區](../data-lake-store/data-lake-store-overview.md)
- [Azure 儲存體](../storage/storage-introduction.md)

當您建立分析帳戶時，必須指定 Azure 資料湖儲存體帳戶作為預設的儲存體帳戶。預設的 ADL 儲存體帳戶是用來儲存工作中繼資料與工作稽核記錄。建立分析帳戶後，就可以新增其他資料湖儲存體帳戶和/或 Azure 儲存體帳戶。

### 尋找預設的 ADL 儲存體帳戶

	azure datalake analytics account show "<Data Lake Analytics Account Name>"

值會列在 properties:datalakeStoreAccount:name 下方。

### 新增其他的 Azure Blob 儲存體帳戶

  	azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -b "<Azure Blob Storage Account Short Name>" -k "<Azure Storage Account Key>"

>[AZURE.NOTE] 僅支援 Blob 儲存體簡短名稱。請勿使用 FQDN，例如 "myblob.blob.core.windows.net"。

### 新增其他的資料湖存放區帳戶

  	azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -l "<Data Lake Store Account Name>" [-d]

[-d] 是選用參數，指出所新增的資料湖是預設的資料湖帳戶。

### 更新現有的資料來源

若要將現有的資料湖存放區帳戶設為預設值：

  	azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -l "<Azure Data Lake Store Account Name>" -d
	  
若要更新現有的 Blob 儲存體帳戶金鑰：

  	azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -b "<Blob Storage Account Name>" -k "<New Blob Storage Account Key>"

### 列出資料來源：

	azure datalake analytics account show "<Data Lake Analytics Account Name>"
	
![資料湖分析會列出資料來源](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### 刪除資料來源：

刪除資料湖存放區帳戶：

  	azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Azure Data Lake Store Account Name>"

刪除 Blob 儲存體帳戶：

  	azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Blob Storage Account Name>"

## 管理工作

您必須擁有資料湖分析帳戶，才能建立工作。如需詳細資訊，請參閱[管理資料湖分析帳戶](#manage-accounts)。

### 列出工作

  	azure datalake analytics job list -n "<Data Lake Analytics Account Name>"

![資料湖分析會列出資料來源](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### 取得工作詳細資料

  	azure datalake analytics job show -n "<Data Lake Analytics Account Name>" -j "<Job ID>"
	
### 提交工作

> [AZURE.NOTE] 工作的預設優先順序為 1000，工作的平行處理原則預設程度是 1。

	azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"

### 取消工作

使用 list 命令來尋找工作識別碼，然後使用 cancel 來取消工作。

  	azure datalake analytics job list -n "<Data Lake Analytics Account Name>"
  	azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job ID>"

## 管理目錄

U-SQL 目錄是用來建構資料和程式碼，讓 U-SQL 指令碼可以共用它們。目錄可以讓 Azure 資料湖中的資料具有可能的最高效能。如需詳細資訊，請參閱[使用 U-SQL 目錄](data-lake-analytics-use-u-sql-catalog.md)。
 
###列出目錄項目

	#List databases
	azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t database

	#List tables
	azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t table
	
類型包括 database、schema、assembly、externaldatasource、table、tablevaluedfunction 或 tablestatistics。

###建立目錄密碼

	azure datalake analytics catalog secret create -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

### 修改目錄密碼

  	azure datalake analytics catalog secret set -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

###刪除目錄密碼

	azure datalake analytics catalog secrete delete -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

<!-- ################################ -->
<!-- ################################ -->
## 使用 ARM 群組

應用程式通常由許多元件組成，例如，Web 應用程式、資料庫、資料庫伺服器、儲存體和協力廠商服務。Azure 資源管理員 (ARM) 可讓您將應用程式中的資源做為群組使用，稱為 Azure 資源群組。您可以透過單一、協調的作業來部署、更新、監視或刪除應用程式的所有資源。您會使用部署的範本，且該範本可以用於不同的環境，例如測試、預備和生產環境。您可以檢視整個群組的彙總成本，為您的組織釐清計費。如需詳細資訊，請參閱 [Azure 資源管理員概觀](../resource-group-overview.md)。

資料湖分析服務可包含下列元件：

- Azure 資料湖分析帳戶
- 必要的預設 Azure 資料湖儲存體帳戶
- 其他 Azure 資料湖儲存體帳戶
- 其他 Azure 儲存體帳戶

您可以在某個 ARM 群組下建立上述所有元件，這樣更容易管理。

![Azure 資料湖分析帳戶與儲存體](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

資料湖分析帳戶和相依的儲存體帳戶必須位於相同的 Azure 資料中心。但 ARM 群組可位在不同的資料中心內。


##另請參閱 

- [Microsoft Azure 資料湖分析概觀](data-lake-analytics-overview.md)
- [使用 Azure 入口網站開始使用資料湖分析](data-lake-analytics-get-started-portal.md)
- [使用 Azure 入口網站管理 Azure 資料湖分析](data-lake-analytics-manage-use-portal.md)
- [使用 Azure 入口網站監視和疑難排解 Azure 資料湖分析工作](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0218_2016-->