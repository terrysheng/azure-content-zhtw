<properties 
   pageTitle="使用 Distcp 將送至/來自 WASB 的資料複製到資料湖存放區中 | Microsoft Azure"
   description="使用 Distcp 工具將送至/來自 Azure 儲存體 Blob 的資料複製到資料湖存放區" 
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
   ms.date="01/06/2016"
   ms.author="nitinme"/>

# 使用 Distcp 在 Azure 儲存體 Blob 與資料湖存放區之間複製資料

在您建立可存取資料湖存放區帳戶的 HDInsight 叢集後，您可以使用 Distcp 之類的 Hadoop 生態系統工具，將**送至/來自** HDInsight 叢集儲存體 (WASB) 的資料複製到資料湖存放區帳戶中。本文提供執行此作業的相關指示。

##必要條件

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- **啟用您的 Azure 訂用帳戶**以使用資料湖存放區公開預覽版。請參閱[指示](data-lake-store-get-started-portal.md#signup)。 
- 可存取資料湖存放區帳戶的 **Azure HDInsight 叢集**。請參閱[建立具有資料湖存放區的 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。請確實為叢集啟用遠端桌面。

## 從遠端桌面 (Windows 叢集) 或 SSH (Linux 叢集) 使用 Distcp

HDInsight 叢集隨附 Distcp 公用程式，可用來將不同來源的資料複製到 HDInsight 叢集。如果您已將 HDInsight 叢集設定為使用資料湖存放區做為額外的儲存體，則您也可以使用現成可用的 Distcp 公用程式將資料複製到資料湖存放區帳戶，或從中複製資料。在本節中，我們將討論如何使用 Distcp 公用程式。

1. 如果您有 Windows 叢集，請從遠端連接到可存取資料湖存放區帳戶的 HDInsight 叢集。如需指示，請參閱[使用 RDP 連接到叢集](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)。從叢集桌面開啟 Hadoop 命令列。

	如果您有 Linux 叢集，請使用 SSH 連接到叢集。請參閱[連線至以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)。從 SSH 提示字元執行命令。

3. 確認您是否可存取 Azure 儲存體 Blob (WASB)。執行以下命令：

		hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

	這應會提供儲存體 blob 中的內容清單。

4. 同樣地，請確認您是否可從叢集存取資料湖存放區帳戶。執行以下命令：

		hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

	這應會提供資料湖存放區帳戶中的檔案/資料夾清單。

5. 使用 Distcp 將資料從 WASB 複製到資料湖存放區帳戶。

		hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

	這會將 WASB 中的 **/example/data/gutenberg/** 資料夾的內容複製到資料湖存放區帳戶中的 **/myfolder**。

6. 同樣地，請使用 Distcp 將資料從資料湖存放區帳戶複製到 WASB。

		hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

	這會將資料湖存放區帳戶中的 **/myfolder** 的內容複製到 WASB 中的 **/example/data/gutenberg/** 資料夾。

## 另請參閱

- [將資料從 Azure 儲存體 Blob 複製到資料湖存放區](data-lake-store-copy-data-azure-storage-blob.md)
- [保護資料湖存放區中的資料](data-lake-store-secure-data.md)
- [搭配資料湖存放區使用 Azure 資料湖分析](data-lake-analytics-get-started-portal.md)
- [搭配資料湖存放區使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0107_2016-->