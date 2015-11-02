<properties 
	pageTitle="使用指令碼動作在以 Linux 為基礎的 HDInsight (Hadoop) 上安裝 Apache Spark | Microsoft Azure" 
	description="在本主題中，您將學習如何使用指令碼動作在以 Linux 為基礎的 HDInsight 叢集上安裝 Spark。透過變更叢集組態或自訂安裝服務與公用程式，指令碼動作可讓您在叢集建立期間自訂叢集。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="Blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/19/2015" 
	ms.author="larryfr"/>

# 在 HDInsight Hadoop 叢集上安裝和使用 Spark

在本文件中，您將學習如何使用指令碼動作來安裝 Spark。指令碼動作可讓您執行指令碼來自訂叢集，但只能在建立叢集時進行。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集][hdinsight-cluster-customize]。安裝 Spark 之後，您也將學習如何在 HDInsight 叢集上執行 Spark 查詢。

> [AZURE.NOTE]HDInsight 也提供 Spark，做為叢集類型，這表示您現在可以直接佈建 Spark 叢集而不必修改 Hadoop 叢集。不過，目前僅限於以 Windows 為基礎的叢集。使用 Spark 叢集類型時，您會取得具備 Spark 1.3.1 版且以 Windows 為基礎的 HDInsight 3.2 版叢集。如需詳細資訊，請參閱[開始使用 HDInsight 上的 Apache Spark](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)。

## <a name="whatis"></a>什麼是 Spark？

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。Spark 的記憶體內計算功能，使其成為機器學習和圖表計算中反覆演算法的絕佳選擇 。

Spark 也可用來執行傳統的磁碟型資料處理。Spark 以避免在中繼階段寫入磁碟的方式，改善傳統的 MapReduce 架構。此外，Spark 與 Hadoop 分散式檔案系統 (HDFS) 和 Azure Blob 儲存體相容，因此可以輕鬆地透過 Spark 來處理現有的資料。

本主題說明如何自訂 HDInsight 叢集以安裝 Spark。

## <a name="whatis"></a>可以安裝哪個版本的 Spark？

在本主題中，我們使用指令碼動作自訂指令碼在 HDInsight 叢集上安裝 Spark。此指令碼會安裝 Spark 1.5.1。

您可以修改此指令碼或建立自有指令碼，以安裝其他版本的 Spark。

## 指令碼會執行哪些作業

此指令碼會將 Spark 1.5.1 版安裝於 `/usr/hdp/current/spark`。

> [AZURE.WARNING]您可能會發現依照預設會安裝某些 Spark 1.3.1 二進位檔在您的 HDInsight 叢集上。但是不應該使用這些檔案，所以在未來的更新會從 HDInsight 叢集映像移除這些檔案。

## <a name="install"></a>使用指令碼動作安裝 Spark

您可以從一個唯讀的 Azure 儲存體 blob 取得在 HDInsight 叢集上安裝 Spark 的範例指令碼，網址為 [https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh](https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh)。本節提供有關如何在使用 Azure 入口網站建立叢集時使用範例指令碼的指示。

> [AZURE.NOTE]您也可以使用 Azure PowerShell 或 HDInsight .NET SDK，以使用此指令碼建立叢集。如需使用這些方法的詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

1. 使用[建立以 Linux 為基礎的 HDInsight 叢集](hdinsight-provision-linux-clusters.md#portal)中的步驟開始建立叢集，但是不要完成建立。

2. 在 [選用設定] 刀鋒視窗中，選取 [指令碼動作]，並提供下列資訊：

	* __名稱__：輸入指令碼動作的易記名稱。
	* __指令碼 URI__：https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh
	* __HEAD__：勾選此選項
	* __背景工作__：取消勾選此選項
	* __ZooKeeper__：取消勾選此選項
	* __參數__：將此欄位保留空白
    
    > [AZURE.NOTE]Spark 範例程式碼只會在前端節點上安裝元件，所以可以取消勾選其他節點類型。

3. 在 [指令碼動作] 底部，使用 [選取] 按鈕以儲存組態。最後，使用 [選用組態] 刀鋒視窗底部的 [選取] 按鈕，儲存選用組態資訊。

4. 繼續如[建立以 Linux 為基礎的 HDInsight 叢集](hdinsight-provision-linux-clusters.md#portal)中所述佈建叢集。

## <a name="usespark"></a>如何在 HDInsight 中使用 Spark？

Spark 提供以 Scala、Python 及 Java 撰寫的 API。您也可以使用互動式 Spark 殼層來執行 Spark 查詢。您的叢集完成建立之後，請使用下列項目連接到您的 HDInsight 叢集：

	ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
	
如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

一旦連接之後，請使用下列各節取得使用 Spark 的特定步驟：

- [使用 Spark 殼層來執行互動式查詢](#sparkshell)
- [使用 Spark 殼層來執行 Spark SQL 查詢](#sparksql) 
- [使用獨立 Scala 程式](#standalone)

###<a name="sparkshell"></a>使用 Spark 殼層來執行互動式查詢

1. 執行下列命令以啟動 Spark 殼層：

		 /usr/hdp/current/spark/bin/spark-shell --master yarn

	在命令完成執行之後，您應該會看到 Scala 提示：

		 scala>

5. 出現 Scala 提示時，輸入下方所示的 Spark 查詢。此查詢會計算 davinci.txt 檔案中每個單字的出現次數，該檔案位於與叢集關聯之 Azure Blob 儲存體上的 /example/data/gutenberg/ 位置中。

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. 輸出應該如以下所示：

		(felt,,1)
		(axle-tree,1)
		(deals,9)
		(virtuous,4)

7. 輸入 :q 以結束 Scala 提示。

		:q

###<a name="sparksql"></a>使用 Spark 殼層來執行 Spark SQL 查詢

Spark SQL 可讓您使用 Spark 來執行以結構化查詢語言 (SQL)、HiveQL 或 Scala 表示的關聯式查詢。在本節中，我們要來看看如何使用 Spark 對範例 Hive 資料表執行 Hive 查詢。本節所用的 Hive 資料表 (稱為 **hivesampletable**) 依預設可在建立叢集時取得。

1. 執行下列命令以啟動 Spark 殼層：

		 /usr/hdp/current/spark/bin/spark-shell --master yarn

	在命令完成執行之後，您應該會看到 Scala 提示：

		 scala>

2. 在 Scala 提示設定 Hive 內容。必須執行此動作，才能透過 Spark 使用 Hive 查詢。

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	> [AZURE.NOTE]本陳述式中的 `sc` 是您啟動 Spark 殼層時所設定的預設 Spark 內容。

5. 使用 Hive 內容執行 Hive 查詢，並將輸出列印到主控台。查詢會擷取特定型號裝置的資料，並將所擷取的記錄數限制在 20 個內。

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. 您應該會看到如下的輸出：

		[820,11:35:17,zh-TW,Android,HTC,Inspire 4G,Louisiana,UnitedStates, 2.7383836,0,1]
		[1055,17:24:08,zh-TW,Android,HTC,Incredible,Ohio,United States,18.0894738,0,0]
		[1067,03:42:29,zh-TW,Windows Phone,HTC,HD7,District Of Columbia,United States,null,0,0]

7. 輸入 :q 以結束 Scala 提示。

		:q

### <a name="standalone"></a>使用獨立 Scala 程式

在本節中，您將建立一個 Scala 應用程式，用來計算範例資料檔案 (/example/data/gutenberg/davinci.txt) 中含有字母 'a' 和 'b' 的行數。

1. 使用下列命令安裝 Scala 建置工具：

		echo "deb http://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
		sudo apt-get update
		sudo apt-get install sbt
		
	出現提示時，請選取 __Y__ 繼續進行。

2. 建立 Scala 專案的目錄結構：

		mkdir -p SimpleScalaApp/src/main/scala
		
3. 建立名為 __simple.sbt__ 的新檔案，其中包含這個專案的組態資訊。

		cd SimpleScalaApp
		nano simple.sbt
		
	使用下列項目做為檔案的內容：

		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"


	> [AZURE.NOTE]請確定在每個項目之間保留那些空白行。
	
	依序按 __Ctrl+X__、__Y__ 和 __Enter__ 鍵以儲存檔案。

4. 使用下列命令在 __SimpleScalaApp/src/main/scala__ 目錄中建立名為 __SimpleApp.scala__ 的新檔案：

		nano src/main/scala/SimpleApp.scala

	使用下列項目做為檔案的內容：

		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf
		
		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//Location of the sample data file on Azure Blob storage
		    val conf = new SparkConf().setAppName("SimpleApplication")
		    val sc = new SparkContext(conf)
		    val logData = sc.textFile(logFile, 2).cache()
		    val numAs = logData.filter(line => line.contains("a")).count()
		    val numBs = logData.filter(line => line.contains("b")).count()
		    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
		  }
		}

	依序按 __Ctrl+X__、__Y__ 和 __Enter__ 鍵以儲存檔案。

5. 從 __SimpleScalaApp__ 目錄中，使用下列命令來建置應用程式，並將其儲存在 jar 檔案中：

		sbt package

	在編譯應用程式之後，您會看到 **simpleapp\_2.10-1.0.jar** 檔案建立在 \_\_SimpleScalaApp/target/scala-2.10** 目錄下。

6. 使用下列命令來執行 SimpleApp.scala 程式：


		/usr/hdp/current/spark/bin/spark-submit --class "SimpleApp" --master yarn target/scala-2.10/simpleapp_2.10-1.0.jar

4. 當程式執行完成時，輸出會顯示在主控台上。

		Lines with a: 21374, Lines with b: 11430

## 後續步驟

- [在 HDInsight 叢集上安裝及使用色調](hdinsight-hadoop-hue-linux.md)。色調是 Web UI，可讓您更輕鬆地建立、執行及儲存 Pig 和 Hive 工作，以及瀏覽您的 HDInsight 叢集的預設儲存體。

- [在 HDInsight 叢集上安裝 R][hdinsight-install-r] 說明如何使用叢集自訂，以在 HDinsight Hadoop 叢集上安裝和使用 R。R 是一個用於統計計算的開放原始碼語言和環境。它提供數百個內建的統計函式及它自己的程式設計語言，此語言結合了函式型和物件導向程式設計的層面。它也提供廣泛的圖形功能。

- [在 HDInsight 叢集上安裝 Giraph](hdinsight-hadoop-giraph-install-linux.md)。在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Giraph。Giraph 可讓您利用 Hadoop 執行圖形處理，且可以搭配 Azure HDInsight 一起使用。

- [在 HDInsight 叢集上安裝 Solr](hdinsight-hadoop-solr-install-linux.md)。在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Solr。Solr 可讓您在儲存的資料上執行功能強大的搜尋作業。

- [在 HDInsight 叢集上安裝色調](hdinsight-hadoop-hue-linux.md)。在 HDInsight Hadoop 叢集上使用叢集自訂安裝色調。色調是一組 Web 應用程式，用來與 Hadoop 叢集互動。



[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[powershell-install-configure]: ../install-configure-powershell.md
 

<!---HONumber=Oct15_HO4-->