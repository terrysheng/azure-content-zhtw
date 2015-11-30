<properties 
   pageTitle="使用跨平台命令列介面開始使用資料湖存放區 | Microsoft Azure"
   description="使用 Azure 跨平台命令列建立資料湖存放區帳戶並執行基本作業" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="11/13/2015"
   ms.author="nitinme"/>

# 使用 Azure 命令列開始使用 Azure 資料湖存放區

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)

了解如何使用 Azure 命令列介面建立 Azure 資料湖存放區帳戶並執行基本作業，例如建立資料夾、上傳和下載資料檔案、刪除您的帳戶等等。如需有關資料湖存放區的詳細資訊，請參閱[資料湖存放區概觀](data-lake-store-overview.md)。

Azure CLI 會在 Node.js 中實作。此工具可在任何支援 Node.js 的平台上使用，包括 Windows、Mac 和 Linux。Azure CLI 為開放原始碼。原始程式碼會在 GitHub 中進行管理 (<a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>)。本文只涵蓋使用 Azure CLI 搭配資料湖存放區。如需如何使用 Azure CLI 的一般指引，請參閱[如何使用 Azure CLI][azure-command-line-tools]。


##必要條件

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/zh-TW/pricing/free-trial/)。
- **啟用您的 Azure 訂用帳戶**以使用資料湖存放區公開預覽版。請參閱[指示](data-lake-store-get-started-portal.md#signup)。 
- **Azure CLI** - 請參閱 [安裝及設定 Azure CLI](../xplat-cli-install.md) 以取得安裝和設定資訊。在安裝 CLI 之後，請務必重新啟動您的電腦。

##登入您的 Azure 訂用帳戶

依照[從 Azure 命令列介面 (Azure CLI) 連線到 Azure 訂用帳戶](xplat-cli-connect.md)中記載的步驟，使用 __login__ 方法連線到您的訂用帳戶。


## 建立 Azure 資料湖存放區帳戶

開啟命令提示字元、殼層或終端機工作階段並執行下列命令。

1. 登入您的 Azure 訂用帳戶：

		azure login

	系統會提示您開啟網頁並輸入驗證碼。遵循頁面上的指示登入您的 Azure 訂用帳戶。

2. 使用下列命令來切換至 Azure 資源管理員模式︰

		azure config mode arm


3. 列出您帳戶的 Azure 訂用帳戶。

		azure account list


4. 如果您有多個 Azure 訂用帳戶，請使用下列命令設定 Azure CLI 命令將使用的訂用帳戶：

		azure account set <subscriptionname>

5. 建立新的資源群組。在下列命令中，提供您想要使用的參數值。

		azure group create <resourceGroup> <location>

	如果位置名稱包含空格，請將它放在引號中。例如 "East US 2"。

5. 建立資料湖存放區帳戶。

		azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## 在您的資料湖存放區中建立資料夾

您可以在您的 Azure 資料湖存放區帳戶下建立資料夾，用於管理與存放資料。使用下列命令在資料湖存放區的根目錄建立名為 "mynewfolder" 的資料夾。

	azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

例如：

	azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## 將資料上傳至您的資料湖存放區

您可以在根層級直接將資料上傳至資料湖存放區，或上傳至您在帳戶內建立的資料夾。下列程式碼片段示範如何將一些範例資料上傳至您在上一節中建立的資料夾 (**mynewfolder**)。

如果您要尋找一些可上傳的範例資料，您可以從 [Azure 資料湖 Git 儲存機制](https://github.com/MicrosoftBigData/AzureDataLake/tree/master/SQLIPSamples/SampleData/AmbulanceData)取得 **Ambulance Data** 資料夾。下載檔案並將它儲存在電腦的本機目錄上，例如 C:\\sampledata。

	azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

例如：

	azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## 列出資料湖存放區中的檔案

使用下列命令列出資料湖存放區中的檔案。

	azure datalake store filesystem list <dataLakeStoreAccountName> <path>

例如：

	azure datalake store filesystem list mynewdatalakestore /mynewfolder

此命令的輸出應類似這樣：

	info:    Executing command datalake store filesystem list
	data:    accessTime: 1446245025257
	data:    blockSize: 268435456
	data:    group: NotSupportYet
	data:    length: 1589881
	data:    modificationTime: 1446245105763
	data:    owner: NotSupportYet
	data:    pathSuffix: vehicle1_09142014.csv
	data:    permission: 777
	data:    replication: 0
	data:    type: FILE
	data:    ------------------------------------------------------------------------------------
	info:    datalake store filesystem list command OK

## 重新命名、下載與刪除資料湖存放區中的資料

* **若要重新命名檔案**，請使用下列命令：

    	azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

	例如：

		azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **若要下載檔案**，請使用下列命令。請確定您指定的目的地路徑已存在。

		azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

	例如：

		azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **若要刪除檔案**，請使用下列命令：

		azure datalake store filesystem delete <dataLakeStoreAccountName> <path> 

	例如：

		azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv
	
	出現提示時，請輸入 **Y** 刪除項目。

## 檢視資料湖存放區中資料夾的存取控制清單

使用下列命令來檢視資料湖存放區資料夾中的 ACL。在目前版本中，ACL 可以只在資料湖存放區的根目錄上設定。因此，下面的路徑參數一律為根目錄 (/)。

	azure datalake store permissions show <dataLakeStoreName> <path>

例如：

	azure datalake store permissions show mynewdatalakestore /


## 刪除資料湖存放區帳戶

使用下列命令刪除資料湖存放區帳戶。

	azure datalake store account delete <dataLakeStoreAccountName>

例如：

	azure datalake store account delete mynewdatalakestore

出現提示時，請輸入 **Y** 刪除帳戶。

## 其他建立資料湖存放區帳戶的方法

- [使用入口網站開始使用資料湖存放區](data-lake-store-get-started-portal.md)
- [使用 .NET SDK 開始使用資料湖存放區](data-lake-store-get-started-net-sdk.md)
- [使用 PowerShell 開始使用資料湖存放區](data-lake-store-get-started-powershell.md)


## 後續步驟

- [保護資料湖存放區中的資料](data-lake-store-secure-data.md)
- [搭配資料湖存放區使用 Azure 資料湖分析](data-lake-analytics-get-started-portal.md)
- [搭配資料湖存放區使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md

<!---HONumber=Nov15_HO4-->