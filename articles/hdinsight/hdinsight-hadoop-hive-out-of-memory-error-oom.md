<properties
	pageTitle="記憶體不足錯誤 (OOM) - Hive 設定 |Microsoft Azure"
	description="在 HDInsight 的 Hadoop 中修正因 Hive 查詢而產生的記憶體不足錯誤 (OOM)。客戶案例是一個橫跨許多大型資料表的查詢。"
	keywords="記憶體不足錯誤, OOM, Hive 設定"
	services="hdinsight"
	documentationCenter=""
	authors="rashimg"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="12/10/2015"
	ms.author="rashimg;cgronlun"/>

# 在 Azure HDInsight 的 Hadoop 中使用 Hive 記憶體設定修正記憶體不足 (OOM) 錯誤

我們的客戶所面臨的其中一個常見問題是在使用 Hive 時遇到記憶體不足 (OOM) 錯誤。本文說明一個客戶案例以及我們建議來修正此問題的 Hive 設定。

## 案例：在大型資料表之間使用 Hive 查詢

客戶使用 Hive 執行下列查詢。

	SELECT
		COUNT (T1.COLUMN1) as DisplayColumn1,
		…
		…
		….
	FROM
		TABLE1 T1,
		TABLE2 T2,
		TABLE3 T3,
		TABLE5 T4,
		TABLE6 T5,
		TABLE7 T6
	where (T1.KEY1 = T2.KEY1….
		…
		…

此查詢的一些細微差異：

* T1 是大型資料表 TABLE1 的別名，其中包含多個 STRING 資料行類型。
* 其他資料表的規模都比較小，但還是具有許多資料行。
* 所有資料表都會彼此聯結，在某些情況下，是透過 TABLE1 和其他資料表中的多個資料行來聯結。

當客戶在 24 節點 A3 叢集上的 MapReduce 上使用 Hive 來執行查詢時，該查詢的執行時間大約是 26 分鐘。客戶在 MapReduce 上使用 Hive 執行查詢時，會注意到下列警告訊息：

	Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
	Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

由於查詢大約會在 26 分鐘內完成執行，所以客戶會忽略這些警告，反而開始將重點放在如何進一步改善這個查詢的效能。

客戶會參考[在 Hdinsight 中最佳化 Hadoop 的 Hive 查詢](hdinsight-hadoop-optimize-hive-query.md)，並決定使用 Tez 執行引擎。一旦啟用 Tez 設定來執行同一個查詢之後，該查詢會執行 15 分鐘，然後擲回下列錯誤：

	Status: Failed
	Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
	org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
	org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
	Caused by: java.lang.OutOfMemoryError: Java heap space

客戶接著決定使用較大的 VM (例如 D12)，因為認為較大的 VM 具有更多的堆積空間。即使如此，客戶仍會繼續看到此錯誤。客戶與 HDInsight 小組聯繫，以尋求協助來偵錯這個問題。

## 偵錯記憶體不足 (OOM) 錯誤

我們的支援小組和工程小組合作發現了造成記憶體不足 (OOM) 錯誤的其中一個問題是一個 [ Apache JIRA 中所述的已知問題](https://issues.apache.org/jira/browse/HIVE-8306)。來自 JIRA 中的說明：

	When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

我們查看 hive-site.xml 檔案下方來確定 **hive.auto.convert.join.noconditionaltask** 確實已設定為 **true**：

	<property>
    	<name>hive.auto.convert.join.noconditionaltask</name>
    	<value>true</value>
    	<description>
      		Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
      		If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
      		specified size, the join is directly converted to a mapjoin (there is no conditional task).
    	</description>
  	</property>

根據警告和 JIRA，我們假設對應聯結是引發 Java 堆積空間 OOM 錯誤的導因。讓我們更深入研究這個問題。

如部落格文章 [HDInsight 中的 Hadoop Yarn 記憶體設定](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx)中所述，使用 Tez 執行引擎時，所使用的堆積空間確實是屬於 Tez 容器。請參閱下表，其中會說明 Tez 容器記憶體。

![Tez 容器記憶體圖表：記憶體不足錯誤 OOM](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)


如部落格文章所建議，下列兩個記憶體設定會定義堆積的容器記憶體：**hive.tez.container.size** 和 **hive.tez.java.opts**。從我們的經驗來看，OOM 例外狀況不表示容器大小太小。它表示 Java 堆積大小 (hive.tez.java.opts) 太小。因此，每當您看到 OOM 時，可嘗試增加 **hive.tez.java.opts**。必要時，您可能需要增加 **hive.tez.container.size**。**Java.opts** 設定應該大約 **container.size** 的 80%。

> [AZURE.NOTE]**hive.tez.java.opts** 設定必須一律小於 **hive.tez.container.size**。

由於 D12 電腦具有 28GB 記憶體，因此我們決定使用 10GB (10240 MB) 的容器大小並指派 80% 給 java.opts。您可以使用下列設定，在 Hive 主控台上完成此動作：

	SET hive.tez.container.size=10240
	SET hive.tez.java.opts=-Xmx8192m

根據這些設定，查詢會在 10 分鐘內成功執行。

## 結論：OOM 錯誤和容器大小

遇到 OOM 錯誤不一定表示容器大小太小。相反地，您應該設定記憶體設定，如此一來即可增加堆積大小，至少是容器記憶體大小的 80%。

<!---HONumber=AcomDC_1217_2015-->