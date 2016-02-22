<properties 
	pageTitle="建立在 HDInsight Spark 叢集上執行的獨立 Scala 應用程式 | Microsoft Azure" 
	description="了解如何建立在 HDInsight Spark 叢集上執行的獨立 Spark 應用程式。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# 建立獨立 Scala 應用程式，並在 HDInsight Spark 叢集 (Linux) 上執行

本文提供相關的逐步指引，說明如何使用 IntelliJ IDEA 開發以 Scala 撰寫的獨立 Spark 應用程式。本文以 Apache Maven 做為建置系統，並且以 IntelliJ IDEA 為 Scala 提供的現有 Maven 原型做為起始點。概括來說，要在 IntelliJ IDEA 中建立 Scala 應用程式，必須執行下列步驟：


* 以 Maven 做為建置系統。
* 更新專案物件模型 (POM) 檔案，以解析 Spark 模組相依性
* 以 Scala 撰寫應用程式。
* 產生可提交至 HDInsight Spark 叢集的 jar 檔案。
* 使用 spark-submit 在 Spark 叢集上執行應用程式。

**必要條件**

* Azure 訂用帳戶。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight Linux 上的 Apache Spark 叢集。如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。
* Oracle Java Development Kit。您可以從[這裡](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)加以安裝。
* Java IDE。本文使用 IntelliJ IDEA 15.0.1。您可以從[這裡](https://www.jetbrains.com/idea/download/)加以安裝。 


## 安裝 IntelliJ IDEA 的 Scala 外掛程式

如果 IntelliJ IDEA 安裝未提示您啟 Scala 外掛程式，請啟動 IntelliJ IDEA，然後完成下列步驟以安裝此外掛程式：

1. 啟動 IntelliJ IDEA，並在 [歡迎使用] 畫面中按一下 [設定]，然後按一下 [外掛程式]。

	![啟用 Scala 外掛程式](./media/hdinsight-apache-spark-create-standalone-application/enable-scala-plugin.png)

2. 在下一個畫面中，按一下左下角的 [安裝 JetBrains 外掛程式]。在開啟的 [瀏覽 JetBrains 外掛程式] 對話方塊中搜尋 Scala，然後按一下 [安裝]。

	![安裝 Scala 外掛程式](./media/hdinsight-apache-spark-create-standalone-application/install-scala-plugin.png)

3. 此外掛程式安裝成功之後，請按一下 [重新啟動 IntelliJ IDEA] 按鈕，以重新啟動 IDE。

## 建立獨立 Scala 專案

1. 啟動 IntelliJ IDEA，並建立新的專案。在新增專案對話方塊中選取下列選項，然後按 [下一步]。

	![建立 Maven 專案](./media/hdinsight-apache-spark-create-standalone-application/create-maven-project.png)

	* 選取 [Maven] 做為專案類型。
	* 指定 [專案 SDK]。按一下 [新增]，然後導覽至 Java 安裝目錄 (通常是 `C:\Program Files\Java\jdk1.8.0_66`)。
	* 選取 [從原型建立] 選項。
	* 從原型清單中，選取 **org.scala-tools.archetypes:scala-archetype-simple**。這會建立正確的目錄結構，並下載撰寫 Scala 程式所需的預設相依性。

2. 為 [GroupId]、[ArtifactId] 和 [版本] 提供相關值。按 [下一步]。

3. 在下一個對話方塊中 (您可以在此處指定 Maven 主目錄及其他使用者設定) 接受預設值，然後按 [下一步]。

4. 在最後一個對話方塊中指定專案名稱和位置，然後按一下 [完成]。

5. 刪除位於 **src\\test\\scala\\com\\microsoft\\spark\\example** 的 **MySpec.Scala** 檔案。應用程式並不需要此檔案。

6. 如有必要，請重新命名預設來源和測試檔案。在IntelliJ IDEA 的左窗格中，導覽至 **src\\main\\scala\\com.microsoft.spark.example**。以滑鼠右鍵按一下 **App.scala**、按一下 [重構]、按一下 [重新命名檔案]，在對話方塊中提供應用程式的新名稱，然後按一下 [重構]。

	![重新命名檔案](./media/hdinsight-apache-spark-create-standalone-application/rename-scala-files.png)

7. 在後續步驟中，您將會更新 pom.xml，以定義 Spark Scala 應用程式的相依性。若要自動下載並解析這些相依性，您必須據以設定 Maven。

	![設定 Maven 以進行自動下載](./media/hdinsight-apache-spark-create-standalone-application/configure-maven.png)

	1. 從 [檔案] 功能表中，按一下 [設定]。
	2. 在 [設定] 對話方塊中，導覽至 [建置、執行、部署] > [建置工具] > [Maven] > [匯入]。
	3. 選取 [自動匯入 Maven 專案] 的選項。
	4. 按一下 [套用]，然後按一下 [確定]。 


8. 更新 Scala 原始程式檔，以納入您的應用程式程式碼。開啟現有的範例程式碼，並將其取代為下列程式碼，然後儲存變更。此程式碼會從 HVAC.csv (所有 HDInsight Spark 叢集上均有提供) 讀取資料、擷取在第六個資料行中只有個位數的資料列，並將輸出寫入到叢集預設儲存體容器下的 **/HVACOut**。

		package com.microsoft.spark.example

		import org.apache.spark.SparkConf
		import org.apache.spark.SparkContext
		
		/**
		  * Test IO to wasb
		  */
		object WasbIOTest {
		  def main (arg: Array[String]): Unit = {
		    val conf = new SparkConf().setAppName("WASBIOTest")
		    val sc = new SparkContext(conf)
		
		    val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		    //find the rows which have only one digit in the 7th column in the CSV
		    val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
		
		    rdd1.saveAsTextFile("wasb:///HVACout")
		  }
		}


9. 更新 pom.xml。

	1.  在 `<project><properties>` 內新增下列項目：

			<scala.version>2.10.4</scala.version>
    		<scala.compat.version>2.10.4</scala.compat.version>
			<scala.binary.version>2.10</scala.binary.version>

	2. 在 `<project><dependencies>` 內新增下列項目：

			<dependency>
		      <groupId>org.apache.spark</groupId>
		      <artifactId>spark-core_${scala.binary.version}</artifactId>
		      <version>1.4.1</version>
		    </dependency>

	儲存 pom.xml 的變更。

10. 建立 .jar 檔案。IntelliJ IDEA 允許將 JAR 建立為專案的構件。請執行下列步驟：

	1. 在 [檔案] 功能表中，按一下 [專案結構]。
	2. 在 [專案結構] 對話方塊中，按一下 [構件]，然後按一下加號。在快顯對話方塊中按一下 [JAR]，然後按一下 [從具有相依性的模組]。

		![建立 JAR](./media/hdinsight-apache-spark-create-standalone-application/create-jar-1.png)

	3. 在 [從模組建立 JAR] 對話方塊中，對 [主要類別] 按一下省略符號 (![省略符號](./media/hdinsight-apache-spark-create-standalone-application/ellipsis.png) )。

	4. 在 [選取主要類別] 對話方塊中，選取依預設出現的類別，然後按一下 [確定]。

		![建立 JAR](./media/hdinsight-apache-spark-create-standalone-application/create-jar-2.png)

	5. 在 [從模組建立 JAR] 對話方塊中，確定已選取 [擷取至目標 JAR]，然後按一下 [確定]。這會建立具有所有相依性的單一 JAR。

		![建立 JAR](./media/hdinsight-apache-spark-create-standalone-application/create-jar-3.png)

	6. [輸出配置] 索引標籤會列出所有納入 Maven 專案中的 jar。您可以選取並刪除 Scala 應用程式未直接依存的 jar。對於我們在此處建立的應用程式，您可以移除最後一個 (**SparkSimpleApp 編譯輸出**) 以外的所有 jar。選取要刪除的 jar，然後按一下 [刪除] 圖示。

		![建立 JAR](./media/hdinsight-apache-spark-create-standalone-application/delete-output-jars.png)

		請確實選取 [在建置時建立] 方塊，以確保在每次建置或更新專案時都會建立 jar。依序按一下 [套用] 及 [確定]。

	7. 在功能表列中按一下 [建置，然後按一下 [建立專案]。您也可以按一下 [建置構件]，以建立 jar。輸出 jar 會建立在 **\\out\\artifacts** 下。

		![建立 JAR](./media/hdinsight-apache-spark-create-standalone-application/output.png)

## 在 Spark 叢集上執行應用程式

若要在叢集上執行應用程式，您必須執行下列動作：

* **將應用程式 jar 複製到與叢集相關聯的 Azure 儲存體 Blob**。您可以使用命令列公用程式 [**AzCopy**](storage/storage-use-azcopy.md) 來執行此動作。另外也有很多用戶端可用來上傳資料。您可以在[在 HDInsight 上將 Hadoop 作業的資料上傳](hdinsight-upload-data.md)中找到其詳細資訊。

* **使用 Livy 從遠端提交應用程式作業至** Spark 叢集。HDInsight 上的 Spark 叢集包含會公開 REST 端點以從遠端提交 Spark 作業的 Livy。如需詳細資訊，請參閱[搭配 HDInsight 上的 Spark 叢集利用 Livy 遠端提交 Spark 作業](hdinsight-apache-spark-livy-rest-interface.md)。


## <a name="seealso"></a>另請參閱


* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### 案例

* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](hdinsight-apache-spark-eventhub-streaming.md)

* [使用 HDInsight 中的 Spark 進行網站記錄分析](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### 建立及執行應用程式

* [利用 Livy 在 Spark 叢集上遠端執行作業](hdinsight-apache-spark-livy-rest-interface.md)

### 工具和擴充功能

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式來建立和提交 Spark Scala 應用程式)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### 管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0211_2016-->