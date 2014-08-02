<properties linkid="manage-services-hdinsight-howto-monitor-hdinsight" urlDisplayName="Monitor" pageTitle="Monitor HDInsight | Azure" metaKeywords="" description="Learn how to monitor an HDInsight cluster and view Hadoop job history through the Azure management portal." metaCanonical="" services="hdinsight" documentationCenter="" title="How to Monitor HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

如何監視 HDInsight
==================

在本主題中，您將了解如何監視 HDInsight 叢集。

目錄
----

-   [作法：監視 HDInsight 叢集](#monitorcluster)
-   [作法：檢視 Hadoop 工作歷程記錄](#jobhistory)

作法：監視 HDInsight 叢集
-------------------------

若要監視 HDInsight 叢集的健全狀況和叢集上執行的 Hadoop 工作，您可以連線至 HDInsight 儀表板，然後表一下 [監視叢集] 磚。

![HDI.TileMonitorCluster](./media/hdinsight-monitor/HDI.TileMonitorCluster.PNG)

[監視] 頁面如下：

![HDI.MonitorPage](./media/hdinsight-monitor/HDI.MonitorPage.PNG)

右邊顯示 Namenode 和工作追蹤器已經執行，且 4 個資料節點的執行狀態健全。

左邊顯示過去 30 分鐘的對應歸納度量。您可以將監視時間範圍變更為 30 分鐘、1 小時、3 小時、12 小時、1 天、2 天、1 週和 2 週。

作法：檢視 Hadoop 工作歷程記錄
------------------------------

若要檢視 Hadoop 工作歷程記錄，請連線至 HDInsight 儀表板，然後按一下 [工作歷程記錄] 磚。

![HDI.TileJobHistory](./media/hdinsight-monitor/HDI.TileJobHistory.PNG)

這個磚會顯示已執行的工作數目。例如，上圖指出 6 的工作有工作歷程記錄。工作歷程記錄頁面如下：

![HDI.JobHistoryPage](./media/hdinsight-monitor/HDI.JobHistoryPage.PNG)

另請參閱
--------

-   [作法：管理 HDInsight](/en-us/manage/services/hdinsight/howto-administer-hdinsight/)
-   [作法：以程式設計方式部署 HDInsight 叢集](/en-us/manage/services/hdinsight/howto-deploy-cluster/)
-   [作法：以程式設計方式在 HDInsight 叢集上執行遠端工作](/en-us/manage/services/hdinsight/howto-execute-jobs-programmatically/)
-   [教學課程：開始使用 Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)

