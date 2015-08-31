<properties
 pageTitle="在 Azure HDInsight 上使用 JDBC 查詢 Hive"
 description="了解如何在 Azure HDInsight 上使用 JDBC 連接到 Hive 並對儲存於雲端中的資料從遠端執行查詢。"
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="java"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="07/17/2015"
 ms.author="larryfr"/>

#使用 Hive JDBC 驅動程式連接到 Azure HDInsight 上的 Hive

[AZURE.INCLUDE [ODBC-JDBC-選取器](../../includes/hdinsight-selector-odbc-jdbc.md)]

在本文件中，您將學習如何從 Java 應用程式使用 JDBC，從遠端提交 Hive 查詢至 HDInsight 叢集。如需有關 Hive JDBC 介面的詳細資訊，請參閱 [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface)。

##必要條件

若要完成本文中的步驟，您需要下列項目：

* HDInsight 叢集上的 Hadoop。以 Linux 或 Windows 為基礎的叢集都會運作。

* [Java Developer Kit (JDK) 第 7 版](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)或更高版本。

* [Apache Maven](https://maven.apache.org)。Maven 是 Java 專案的專案建置系統，由與本文相關聯的專案所使用。

##Connection string

透過 443 在 Azure 上建立 HDInsight 叢集的 JDBC 連接，並使用 SSL 保護流量。背後有叢集的公用閘道器會將流量重新導向至 HiveServer2 實際接聽的連接埠。因此一般連接字串如下所示：

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

##驗證

建立連接時，您必須指定 HDInsight 叢集系統管理員名稱和密碼。這些內容會驗證對閘道器的要求。例如，下列 Java 程式碼會使用連接字串、系統管理員名稱和密碼開啟新的連接：

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##查詢

一旦建立連接之後，您可以針對 Hive 執行查詢。例如，下列 Java 程式碼會從資料表執行 __SELECT__，將結果限制為只有三個資料列，然後顯示結果：

    sql = "SELECT querytime, market, deviceplatform, devicemodel, state, country from " + tableName + " LIMIT 3";
    stmt2 = conn.createStatement();
    System.out.println("\nRetrieving inserted data:");

    res2 = stmt2.executeQuery(sql);

    while (res2.next()) {
      System.out.println( res2.getString(1) + "\t" + res2.getString(2) + "\t" + res2.getString(3) + "\t" + res2.getString(4) + "\t" + res2.getString(5) + "\t" + res2.getString(6));
    }

##範例 Java 專案

如需在 HDInsight 上使用 Hive 查詢的 Java 用戶端範例，請造訪 [https://github.com/Blackmist/hdinsight-hive-jdbc](https://github.com/Blackmist/hdinsight-hive-jdbc)。遵循儲存機制中的指示建置和執行範例。

##後續步驟

現在您已學會如何搭配 Hive 使用 JDBC，接著請使用下列連結來探索 Azure HDInsight 的其他使用方式。

* [將資料上傳至 HDInsight](hdinsight-upload-data.md)
* [搭配 HDInsight 使用 Hivet](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [搭配 HDInsight 使用 MapReduce 工作](hdinsight-use-mapreduce.md)

<!---HONumber=August15_HO8-->