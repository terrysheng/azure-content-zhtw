<properties
   pageTitle="整合資料湖存放區與其他 Azure 服務 | Azure"
   description="了解資料湖存放區如何與其他 Azure 服務整合"
   documentationCenter=""
   services="data-lake-store" 
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2015"
   ms.author="nitinme"/>

# 整合資料湖存放區與其他 Azure 服務

Azure 資料湖存放區可以與其他 Azure 服務一起使用，以啟用更廣泛的案例。下列文章列出資料湖存放區可以整合的服務。

## 搭配 Azure HDInsight 使用資料湖存放區

您可以佈建 [Azure HDInsight](https://azure.microsoft.com/zh-TW/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) 叢集，其使用資料湖存放區做為 HDFS 相容儲存體。對於此版本，Windows 和 Linux 上的 Hadoop 和 Storm 叢集，您只能使用資料湖存放區做為額外的儲存體。這類叢集仍會使用 Azure 儲存體 (WASB) 做為預設儲存體。但是，對於 Windows 和 Linux 上的 HBase 叢集，您可以使用資料湖存放區做為預設儲存體或額外的儲存體，或同時做為兩者。

如需如何使用資料湖存放區佈建 HDInsight 叢集的指示，請參閱：

* [使用 Azure Preview 入口網站以資料湖存放區佈建 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)
* [使用 Azure PowerShell 以資料湖存放區佈建 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-powershell.md)


## 搭配 Azure 資料湖分析使用資料湖存放區

[Azure 資料湖分析](data-lake-analytics/data-lake-analytics-overview.md)可讓您在雲端規模使用巨量資料。它以動態方式佈建資源，讓您能夠進行分析 TB 或甚至是 EB 的資料，這些資料可以儲存在許多支援的資料來源，其中一個就是資料湖存放區。資料湖分析已特別最佳化以使用 Azure 資料湖存放區 - 提供最高層級的效能、輸送量和您的巨量資料工作負載的平行處理。

如需有關如何搭配資料湖存放區使用資料湖分析的指示，請參閱[使用資料湖存放區開始使用資料湖分析](data-lake-analytics/data-lake-analytics-get-started-portal.md)。


## 搭配 Azure Data Factory 使用資料湖存放區

您可以使用 [Azure Data Factory](https://azure.microsoft.com/zh-TW/services/data-factory/) 從 Azure 資料表、Azure SQL Database、Azure SQL 資料倉儲、Azure 儲存體 Blob 和內部部署資料庫擷取資料。Azure 生態系統中的首選，Azure Data Factory 可以用來協調從這些來源到 Azure 資料湖存放區的資料擷取。

如需有關如何搭配資料湖存放區使用 Azure Data Factory 的指示，請參閱[使用 Data Factory 移動資料湖存放區的資料](data-factory/data-factory-azure-datalake-connector.md)。


## 另請參閱

- [Azure 資料湖存放區概觀](data-lake-store-overview.md)
- [使用入口網站開始使用資料湖存放區](data-lake-store-get-started-portal.md)
- [使用 PowerShell 開始使用資料湖存放區](data-lake-store-get-started-powershell.md)  

<!---HONumber=Nov15_HO1-->