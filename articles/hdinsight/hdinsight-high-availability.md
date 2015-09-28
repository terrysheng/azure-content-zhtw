<properties
	pageTitle="HDInsight 中的 Hadoop 叢集可用性 | Microsoft Azure"
	description="HDInsight 使用額外的前端節點部署高可用性且可靠的叢集。"
	services="hdinsight"
	tags="azure-portal"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="jgao"/>


#HDInsight 上 Hadoop 叢集的可用性和可靠性


HDInsight 可讓客戶部署各種不同的叢集類型，用於不同的資料分析工作負載。目前提供的叢集型別是查詢和分析工作負載的 Hadoop 叢集、NoSQL 工作負載的 HBase 叢集和即時事件處理工作負載的 Storm 叢集。在指定的叢集類型內，有各種節點的不同角色。例如：



- HDInsight 的 Hadoop 叢集利用兩個角色部署：
	- 前端節點 (2 個節點)
	- 資料節點 (至少 1 個節點)

- Hdinsight 的 HBase 叢集利用三個角色部署：
	- 前端伺服器 (2 個節點)
	- 區域伺服器 (至少 1 個節點)
	- 主要/Zookeeper 節點 (3 個節點)

- HDInsight 的 Storm 叢集利用三個角色部署：
	- Nimbus 節點 (2 個節點)
	- 監督員伺服器 (至少 1 個節點)
	- Zookeeper 節點 (3 個節點)

Hadoop 叢集的標準實作通常包含單一前端節點。HDInsight 會透過新增次要前端節點 /前端伺服器/Nimbus 節點來移除這個單一失敗點，以增加管理工作負載所需之服務的可用性和可靠性。這些前端節點/前端伺服器/Nimbus 節點是專為順利管理背景工作節點錯誤所設計的，但任何在前端節點上執行的主要服務中斷都有可能導致叢集停止工作。


已加入 [ZooKeeper](http://zookeeper.apache.org/) 節點 (ZK)，並使用於前端節點的領袖選擇，以確保背景工作節點和閘道 (GW) 知道當作用中前端節點 (Head Node0) 變成非作用中狀態時，容錯移轉至次要前端節點 (Head Node1) 的時機。

![HDInsight Hadoop 實作中的高可靠性前端節點圖表。](./media/hdinsight-high-availability/hadoop.high.availability.architecture.diagram.png)




## 檢查使用中的前端節點服務狀態
若要判斷出作用中的前端節點及其執行的服務狀態，您必須使用遠端桌面通訊協定 (RDP) 連接到 Hadoop 叢集。如需 RDP 指示，請參閱[使用 Azure 預覽入口網站管理 HDInsight 上的 Hadoop 叢集](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)。一旦遠端進入叢集後，按兩下位於桌面上的 **Hadoop 服務可用 ** 圖示，以取得執行 Namenode、Jobtracker、Templeton、Oozieservice、Metastore 和 Hiveserver2 服務的前端節點狀態，或在 HDI 3.0 中，取得執行 Namenode、Resource Manager、History Server、Templeton、Oozieservice、Metastore 和 Hiveserver2 服務的前端節點狀態。

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

在螢幕擷取畫面上，使用中的前端節點為 *headnode0*。

## 存取次要前端節點上的記錄檔

如果要存取事件中次要前端節點 (已成為作用中前端節點) 上的工作記錄，仍然可以使用瀏覽 JobTracker UI (如其為主要作用中節點般)。若要存取 JobTracker，您必須使用 RDP 連接到 Hadoop 叢集 (如上一節所述)。一旦遠端進入叢集後，按兩下位於桌面上的 **Hadoop 名稱節點狀態**圖示，然後按一下 [**名稱節點記錄**] 以前往次要前端節點上的記錄目錄。

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)


## 設定前端節點的大小
根據預設，前端節點會配置為大型虛擬機器 (VM)。這個大小適合管理大部分在叢集上執行的 Hadoop 工作。但有些情況可能會因前端節點而要求超大型 VM。例如，叢集必須管理大量的小型 Oozie 工作。

可以使用 Azure PowerShell Cmdlet 或 HDInsight SDK 設定超大型 VM。

使用 Azure PowerShell 建立與佈建叢集的說明已記錄在 [使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)。超大型前端節點的組態要求將 `-HeadNodeVMSize ExtraLarge` 參數加入此程式碼中所使用的 `New-AzureHDInsightcluster` Cmdlet。

    # Create a new HDInsight cluster in Azure PowerShell
	# Configured with an ExtraLarge head-node VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

SDK 的情況十分類似。使用 SDK 建立與佈建叢集的說明已記錄在 [使用 HDInsight .NET SDK](hdinsight-provision-clusters.md#sdk)。超大型前端節點的組態要求將 `HeadNodeSize = NodeVMSize.ExtraLarge` 參數加入此程式碼中所使用的 `ClusterCreateParameters()` 方法。

    # Create a new HDInsight cluster with the HDInsight SDK
	# Configured with an ExtraLarge head-node VM
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

- [ZooKeeper](http://zookeeper.apache.org/)
- [使用 RDP 連接到 HDInsight 叢集](hdinsight-administer-use-management-portal.md#rdp)
- [使用 HDInsight .NET SDK](hdinsight-provision-clusters.md#sdk)

<!---HONumber=Sept15_HO3-->