<properties title="Analyzing sensor data using Hive with HDInsight" pageTitle="使用 Hive 和 Microsoft Azure HDInsight (Hadoop) 分析感應器資料" description="了解如何使用 Hive 和 Excel 透過 HDInsight (Hadoop) 分析和視覺化感應器資料" metaKeywords="Azure hdinsight hive, Azure hdinsight hive sensor, azure hadoop hive, azure hadoop sensor, azure hadoop excel, azure hdinsight excel" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="larryfr" />

#使用 Hive 搭配 HDInsight 來分析感應器資料

了解如何在 HDInsight (Hadoop) 上使用 Hive 來分析感應器資料，然後在 Microsoft Excel 中使用 Power View 將資料視覺化。

在此範例中，您將使用 Hive 來處理暖氣、通風和空調 (HVAC) 系統所產生的歷史資料，以識別無法可靠地維持規定溫度的系統。您將了解如何：

- 建立 HIVE 資料表來查詢逗點分隔值 (CSV) 檔案中儲存的資料
- 建立 HIVE 查詢來分析資料
- 使用 Microsoft Excel 連接到 HDInsight (使用 ODBC 連線) 來擷取已分析的資料
- 使用 Power View 將資料視覺化

![A diagram of the solution architecture](./media/hdinsight-use-hive-sensor-data-analysis/hvac-architecture.png)

##必要條件：

* HDInsight (Hadoop) 叢集 - 請參閱[在 HDInsight 中佈建 Hadoop 叢集](/zh-tw/documentation/articles/hdinsight-provision-clusters/) 以取得建立叢集的相關資訊

* Microsoft Excel 2013

	> [WACOM.NOTE] Microsoft Excel 是用於 [Power View](https://support.office.com/en-US/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US)的資料視覺效果，目前僅在 Windows 上提供。

* [Microsoft Hive ODBC 驅動程式](http://www.microsoft.com/zh-tw/download/details.aspx?id=40886)

##執行範例

1. 從 Azure 管理入口網站，按一下您要執行範例的叢集，然後按一下底部的 [**Query Console**]。或者，您也可以使用下列 URL 直接開啟 Query Console

	 	https://<clustername>.azurehdinsight.net

	出現提示時，使用您佈建此叢集時所用的系統管理員使用者名稱和密碼來驗證。

2. 從開啟的網頁中，按一下 [**Getting Started Gallery**] 索引標籤，然後在 [**範例**] 類別下，按一下 [**Website Log Analysis**] 範例。

3. 依照網頁上提供的指示完成範例。

<!--HONumber=35_1-->
