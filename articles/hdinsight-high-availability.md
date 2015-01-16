<properties urlDisplayName="HDInsight High Availability" pageTitle="HDInsight 中的 Hadoop 叢集可用性 | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight 部署高可用性和可靠性的叢集。" services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Availability of Hadoop clusters in HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />


#HDInsight 上 Hadoop 叢集的可用性和可靠性

## 簡介 ##
已在 HDInsight 所部署的 Hadoop 叢集中新增第二個前端節點，以提升管理工作負載所需服務的可用性和可靠性。Hadoop 叢集的標準實作通常包含單一前端節點。這些叢集是專為順利管理背景工作節點錯誤所設計的，但任何在前端節點上執行的主要服務中斷都有可能導致叢集停止運作。 

![](http://i.imgur.com/jrUmrH4.png)

HDInsight 會透過新增次要前端節點 (Head Node1)，來將這個單一失敗點移除。已新增 [ZooKeeper][zookeeper] 節點 (ZK) 並將其用於前端節點的「選出領導者」，以確保背景工作節點和閘道 (GW) 知道容錯移轉至次要前端節點 (Head Node1) 的時機，以及作用中前端節點 (HeadNode0) 變成非作用中狀態的時機。


## 如何檢查作用中前端節點的服務狀態 ##
若要判斷哪個前端節點處於作用中狀態，並要檢查在該前端節點上執行的服務狀態，您必須使用遠端桌面通訊協定 (RDP) 連線到 Hadoop 叢集。依預設，系統會停用 Azure 中的遠端進入叢集功能，因此您必須首先將它啟用。如需如何在入口網站中執行此動作的指示，請參閱[使用 RDP 連線到 HDInsight 叢集](../hdinsight-administer-use-management-portal/#rdp)
一旦遠端連線進入叢集後，連按兩下位於桌面上的 [**Hadoop Service Available Status**] 圖示，以取得執行 Namenode、Jobtracker、Templeton、Oozieservice、Metastore 和 Hiveserver2 服務的前端節點狀態，或在 HDI 3.0 中，取得執行 Namenode、Resource Manager、History Server、Templeton、Oozieservice、Metastore 和 Hiveserver2 的前端節點狀態。

![](http://i.imgur.com/MYTkCHW.png)


## 如何存取次要前端節點上的記錄檔 ##

如果次要前端節點變成作用中前端節點，若要存取該節點上的工作記錄，瀏覽 JobTracker UI 仍然可以使用，和在主要作用中節點上操作沒有兩樣。若要存取工作追蹤器，您必須使用遠端桌面通訊協定 (RDP) 連線到 Hadoop 叢集 (如上一節所述)。一旦遠端連線進入叢集後，連按兩下位於桌面上的 [**Hadoop Name Node**] 圖示，然後按一下 [**NameNode logs**] 以取得次要前端節點上的記錄目錄。

![](http://i.imgur.com/eL6jzgB.png)


## 如何設定前端節點的大小 ##
依預設，前端節點會被配置為大型 VM。這個大小適合管理大部分在叢集上執行的 Hadoop 工作。但有些案例因為前端節點需要，所以可能會要求超大型 VM。例如，叢集必須管理大量的小型 Oozie 工作。 

您可以使用 Azure PowerShell Cmdlet 或 HDInsight SDK 來設定超大型 VM。

使用 PowerShell 建立與佈建叢集的說明記載於[使用 PowerShell 管理 HDInsight](../hdinsight-administer-use-powershell/)。超大型前端節點的組態要求將 `-HeadNodeVMSize ExtraLarge` 參數加入此程式碼中所使用的 `New-AzureHDInsightcluster` Cmdlet。

    # 在 Azure PowerShell 中建立新 HDInsight 叢集
	# 使用 ExtraLarge Headnode VM 設定
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

SDK 的情況十分類似。使用 SDK 建立與佈建叢集的說明記載於[使用 HDInsight .NET SDK ](../hdinsight-provision-clusters/#sdk)。超大型前端節點的組態要求將 `HeadNodeSize = NodeVMSize.ExtraLarge` 參數加入此程式碼中所使用的 `ClusterCreateParameters()` 方法。

    # Create a new HDInsight cluster with the HDInsight SDK
	# Configured with an ExtraLarge Headnode VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
    Name = clustername,
    Location = location,
    HeadNodeSize = NodeVMSize.ExtraLarge,
    DefaultStorageAccountName = storageaccountname,
    DefaultStorageAccountKey = storageaccountkey,
    DefaultStorageContainer = containername,
    UserName = username,
    Password = password,
    ClusterSizeInNodes = clustersize
    };


**參考**	

- [ZooKeeper][zookeeper]
- [使用 RDP 連線到 HDInsight 叢集](../hdinsight-administer-use-management-portal/#rdp)
- [使用 HDInsight .NET SDK](../hdinsight-provision-clusters/#sdk) 


[zookeeper]: http://zookeeper.apache.org/ 







<!--HONumber=35.1-->
