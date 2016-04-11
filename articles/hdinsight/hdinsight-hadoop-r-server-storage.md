<properties
   pageTitle="適用於 HDInsight Premium 上的 R 伺服器的 Azure 儲存體選項 | Azure"
   description="了解 HDInsight Premium 上 R 伺服器的使用者可用的不同儲存體選項"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="jeffstok"
/>

# 適用於 HDInsight Premium 上的 R 伺服器的 Azure 儲存體選項

HDI 叢集上的 R 伺服器具有 Azure Blob 和即將推出的 Azure Data Lake 儲存體的存取權，做為保存資料、程式碼、分析的結果物件等等的方法。

當您在 HDInsight 中建立 Hadoop 叢集時，您需要指定一個 Azure 儲存體帳戶。該帳戶特定的 Blob 儲存體容器被指定為保留您建立的叢集的檔案系統，也就是 Hadoop 分散式檔案系統 (HDFS)。基於效能目的，系統會在與您指定的主要儲存體帳戶相同資料中心內建立 HDInsight 叢集。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](hdinsight-hadoop-use-blob-storage.md "在 HDInsight 上使用 Azure Blob 儲存體")。


## 使用多個 Azure Blob 儲存體帳戶

如有需要，可以使用您的 HDI 叢集存取多個 Azure 儲存體帳戶或容器。若要這樣做，您必須在建立叢集時於 UI 中指定其他儲存體帳戶，並且遵循下列步驟，以便在 R 中使用它們。

1.	假設您使用儲存體帳戶名稱 “storage1” 和預設容器 “container1” 建立 HDInsight 叢集。您也指定其他儲存體帳戶 “storage2"。  
2.	現在您將 “mycsv.csv” 檔案複製到目錄 “/share”，並且想要在該檔案上執行分析。  

    hadoop fs –mkdir /share hadoop fs –copyFromLocal myscsv.scv /share

3.	在 R 程式碼中，您將節點設為 “default”，並且設定要處理的目錄和檔案

    myNameNode <- "default" myPort <- 0

  資料的位置

    bigDataDirRoot <- "/share"  

  定義 Spark 計算內容

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  設定計算內容

    rxSetComputeContext(mySparkCluster)

  定義 HDFS 檔案系統

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  指定 HDFS 中的輸入檔案以進行分析

    inputFile <-file.path(bigDataDirRoot,"mycsv.csv")
 
所有目錄和檔案參考會指向儲存體帳戶 wasb://container1@storage1.blob.core.windows.net，因為這是與 HDInsight 叢集相關聯的**預設儲存體帳戶**。

現在假設您想要處理稱為 “mySpecial.csv” 的檔案，它位於儲存體帳戶名稱 “storage2” 上的容器 “container2” 的目錄 “/private” 中。

在 R 程式碼中，您將名稱節點參考變更為 “storage2" 儲存體帳戶。

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

  資料的位置

    bigDataDirRoot <- "/private"

  定義 Spark 計算內容

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  設定計算內容

    rxSetComputeContext(mySparkCluster)

  定義 HDFS 檔案系統

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  指定 HDFS 中的輸入檔案以進行分析

    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")
 
所有目錄和檔案參考現在會指向儲存體帳戶 wasb://container2@storage2.blob.core.windows.net，因為這是您指定的**名稱節點**。

請注意，您必須在儲存體帳戶 “storage2” 上設定 /user/RevoShare/<SSH username> 目錄︰

    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>


## 使用邊緣節點上的 Azure 檔案 

在稱為 [Azure 檔案](../storage/storage-how-to-use-files-linux.md "Azure 檔案")的邊緣節點上還有還可以使用便利的資料儲存體選項，讓您將 Azure 儲存體檔案共用掛接至 Linux 檔案系統。這對於儲存資料檔案、R 指令碼以及結果物件相當便利，該結果物件在稍後可以於邊緣節點 (而不是 HDFS) 上使用原生檔案系統時需要。使用 Azure 檔案的一大優點是可掛接檔案共用，以及讓任何具有受支援的作業系統 (Win，Linux) 的系統使用，例如您或其他小組成員具有的其他 HDInsight 叢集、Azure VM 或甚至是內部部署系統。


## 後續步驟

現在您已了解如何建立包含 R 伺服器的新 HDInsight 叢集，以及透過 SSH 工作階段使用 R 主控台的基本概念，請參閱下列內容以探索在 HDInsight 上使用 R 伺服器的其他方式。

- [Hadoop 上的 R 伺服器的概觀](hdinsight-hadoop-r-server-overview.md)
- [開始使用 Hadoop 中的 R 伺服器](hdinsight-hadoop-r-server-get-started.md)
- [將 RStudio 伺服器新增至 HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [適用於 HDInsight Premium 上的 R 伺服器的計算內容選項](hdinsight-hadoop-r-server-compute-contexts.md)

<!---HONumber=AcomDC_0330_2016-->