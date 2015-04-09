<properties 
	pageTitle="使用 HDInsight Hadoop 中的 Hive 進行網站記錄分析 | Azure" 
	description="了解如何使用 HDInsight 上的 Hive 來分析網站記錄。您將使用記錄檔做為 HDInsight 資料表的輸入，然後使用 HiveQL 來查詢資料。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="nitinme"/>

# 使用 HDInsight 上的 Hive 分析網站的記錄

了解如何使用 HDInsight 上的 HiveQL 來分析網站的記錄。網站記錄分析可用於根據類似活動來區隔對象、依人口統計將網站訪客分類、了解他們檢視的內容、他們來自的網站等。

在此範例中，您將了解如何使用 HDInsight 叢集來分析網站記錄檔，以深入了解一天之中來自外部網站的造訪次數，以及使用者遭遇網站錯誤的摘要。您將了解如何：

- 連接至含有網站記錄檔的 Azure 儲存體 Blob
- 建立 HIVE 資料表來查詢這些記錄
- 建立 HIVE 查詢來分析資料
- 使用 Microsoft Excel 連接到 HDInsight (使用 ODBC 連線) 來擷取已分析的資料

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##必要條件

- 您必須已佈建 **HDInsight 叢集**。如需指示，請參閱＜[佈建 HDInsight 叢集][hdinsight-provision]＞。 
- 您必須安裝 Microsoft Excel 2010 或 Microsoft Excel 2013。
- 您必須有 [Microsoft Hive ODBC 驅動程式](http://www.microsoft.com/download/details.aspx?id=40886)，才能將資料從 Hive 匯入 Excel 中。


##執行範例

1. 從 [Azure 管理入口網站] 中，按一下您要執行範例的叢集，然後按一下底部的 [**查詢主控台**]。或者，您也可以使用下列 URL 直接開啟 Query Console：

	 	https://<clustername>.azurehdinsight.net
	
	出現提示時，使用您佈建叢集時所用的系統管理員使用者名稱和密碼來驗證。
  
2. 從開啟的網頁中，按一下 [**Getting Started Gallery**] 索引標籤，然後在 [**範例**] 類別下按一下 [**Website Log Analysis**] 範例。
3. 依照網頁上提供的指示完成範例。

##後續步驟
嘗試範例以了解如何使用 Azure HDInsight 來分析感應器資料。請參閱[搭配使用 Hive 與 HDInsight 來分析感應器資料][hdinsight-sensor-data-sample]。


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png

<!--HONumber=49-->