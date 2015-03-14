<properties
   pageTitle="MapReduce 與 HDInsight 中的 Hadoop | Azure"
   description="了解如何使用 SSH，以利用 HDInsight 上的 Hadoop 來執行 MapReduce 工作。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# 利用 SSH 搭配使用 Hive 與 HDInsight 上的 Hadoop

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

在本文中，您將學習如何使用 SSH 連線至 HDInsight 叢集上的 Hadoop，然後使用 Hadoop 命令提交 MapReduce 工作。

> [AZURE.NOTE] 如果您已熟悉使用 Linux 型 Hadoop 伺服器，但剛接觸 HDInsight，請參閱 <a href="../hdinsight-hadoop-linux-information/" target="_blank">Linux 型 HDInsight 上的 Hadoop 需知</a>。

## <a id="prereq"></a>必要條件

若要完成這篇文章中的步驟，您需要下列項目。

* Linux 型 HDInsight (HDInsight 上的 Hadoop) 叢集

* SSH 用戶端。Linux、Unix 和 Mac OS 應該具備 ssh 用戶端。Windows 使用者必須下載用戶端，例如 <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>。

## <a id="ssh"></a>使用 SSH 連線

使用 SSH 命令，連線至 HDInsight 叢集的完整網域名稱 (FQDN)。FQDN 將是您提供給叢集的名稱，然後是 **.azurehdinsight.net**。例如，下列會連線至名為 **myhdinsight** 的叢集。

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**如果您提供憑證金鑰進行 SSH 驗證** (在建立 HDInsight 叢集時)，可能需要指定用戶端系統上私密金鑰的位置。

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**如果您提供密碼進行 SSH 驗證** (在建立 HDInsight 叢集時)，則需要在出現提示時提供密碼。

### Putty (Windows 用戶端)

Windows 未提供內建 SSH 用戶端。建議使用 **Putty**，這可以從下列位置進行下載： <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>。

如需使用 Putty 的詳細資訊，請參閱下者的**使用 Putty 連線至 Linux 機器**小節： <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">如何搭配使用 SSH 與 Azure 上的 Linux</a>。

> [AZURE.NOTE] 如果您將憑證用於 HDInsight 叢集的 SSH 驗證，則也需要參閱下者的**建立 PPK for Putty** 小節： <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">如何搭配使用 SSH 與 Azure 上的 Linux</a>

## <a id="hadoop"></a>使用 Hadoop 命令

1. 連線到 HDInsight 叢集之後，請使用下列命令以使用 **Hadoop** 命令來啟動 MapReduce 工作。

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	這樣會啟動 **wordcount** 類別 (內含於 **hadoop-mapreduce-examples.jar** 檔案中)。做為輸入，它會使用 **wasb://example/data/gutenberg/davinci.txt** 文件，而且輸出會儲存至 **wasb:///example/data/WordCountOutput**。

	> [AZURE.NOTE] 如需此 MapReduce 工作和範例資料的詳細資訊，請參閱 <a href="../hdinsight-use-mapreduce/" target="_blank">在 HDInsight 上的 Hadoop 中使用 MapReduce</a>。

2. 工作會在處理時發出詳細資料，最後在工作完成時傳回與下列類似的資訊。

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. 完成之後，請使用下列命令列出儲存至 **wasb://example/data/WordCountOutput** 的輸出檔。

		hadoop fs -ls wasb:///example/data/WordCountOutput

	這應該會顯示兩個檔案：**_SUCCESS** 和 **part-r-00000**。**part-r-00000** 檔案包含這項工作的輸出。

	> [AZURE.NOTE] 某些 MapReduce 工作可能會將結果分成多個 **part-r-#####** 檔案。若是如此，請使用 ##### 尾碼指出檔案的順序。

4. 若要檢視輸出，請使用下列命令。

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	這會顯示 **wasb://example/data/gutenberg/davinci.txt** 檔案中所含的單字清單，以及每個單字的出現次數。以下是要包含在檔案中之資料的範例。

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

## <a id="summary"></a>摘要

如您所見，Hadoop 命令提供簡單的方法，在 HDInsight 叢集上執行 MapReduce 工作，然後檢視工作輸出。

## <a id="nextsteps"></a>後續步驟

如需 HDInsight 中 MapReduce 工作的一般資訊。

* [在 HDInsight Hadoop 上使用 MapReduce](../hdinsight-use-mapreduce/)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊。

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](../hdinsight-use-hive/)

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](../hdinsight-use-pig/)
<!--HONumber=45--> 
