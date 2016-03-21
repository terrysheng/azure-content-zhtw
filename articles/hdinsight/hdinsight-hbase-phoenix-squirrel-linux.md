<properties 
   pageTitle="使用 HDinsight 中的 Apache Phoenix 和 SQuirreL | Microsoft Azure" 
   description="了解如何使用 HDinsight 中的 Apache Phoenix，以及如何在您的工作站上安裝與設定 SQuirreL 以連線到 HDInsight 中的 HBase 叢集。" 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="03/04/2016"
   ms.author="jgao"/>

# 在 HDinsight 中搭配 Linux 型 HBase 叢集使用 Apache Phoenix  

了解如何在 HDInsight 中使用 [Apache Phoenix](http://phoenix.apache.org/)，以及如何使用 SQLLine。如需有關 Phoenix 的詳細資訊，請參閱 [15 分鐘內了解 Phoenix](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html)。如需 Phoenix 文法，請參閱 [Phoenix 文法](http://phoenix.apache.org/language/index.html)。

>[AZURE.NOTE] 如需 HDInsight 中的 Phoenix 版本資訊，請參閱 [HDInsight 在 Hadoop 叢集版本中提供什麼新功能？][hdinsight-versions]

##使用 SQLLine
[SQLLine](http://sqlline.sourceforge.net/) 是執行 SQL 的命令列公用程式。

###必要條件
開始使用 SQLLine 之前，您必須具備下列項目：

- **HDInsight 中的 HBase 叢集**。如需有關佈建 HBase 叢集的資訊，請參閱[開始使用 HDInsight 中的 Apache HBase][hdinsight-hbase-get-started]。
- **透過遠端桌面通訊協定連接到 HBase 叢集**。如需相關指示，請參閱[使用 Azure 傳統入口網站管理 HDInsight 中的 Hadoop 叢集][hdinsight-manage-portal]。


當您連接到 HBase 叢集時，您必須連接到其中一個 Zookeeper。每個 HDInsight 叢集有 3 個 Zookeeper。

**找出 Zookeeper 主機名稱**

1. 瀏覽至 **https://<ClusterName>.azurehdinsight.net** 來開啟 Ambari。
2. 輸入 HTTP (叢集) 使用者名稱和密碼來登入。
3. 在左側功能表中，按一下 [ZooKeeper]。您應該會看到列出了 3 台「ZooKeeper 伺服器」。
4. 按一下其中一台列出的「ZooKeeper 伺服器」。在 [摘要] 窗格中，找到 [主機名稱]。它類似 *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*。

**使用 SQLLine**

1. 使用 SSH 連線到叢集。如需指示，請根據您用戶端電腦的作業系統而定，來參閱[從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md) 或[從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows)。

2. 從 SSH，執行下列命令以執行 SQLLine：

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure

2. 執行下列命令來建立 HBase 資料表，並插入一些資料︰

		CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
	
		!tables
		
		UPSERT INTO Company VALUES(1, 'Microsoft');
		
		SELECT * FROM Company;
        
        !quit

如需詳細資訊，請參閱 [SQLLine 手冊](http://sqlline.sourceforge.net/#manual)和 [Phoenix 文法](http://phoenix.apache.org/language/index.html)。


 
##後續步驟
在本文中，您已經學會如何在 HDInsight 中使用 Apache Phoenix。若要深入了解，請參閱：

- [HDInsight HBase 概觀][hdinsight-hbase-overview]：HBase 是建置於 Hadoop 上的 Apache 開放原始碼 NoSQL 資料庫，可針對大量非結構化及半結構化資料，提供隨機存取功能和強大一致性。
- [在 Azure 虛擬網路上佈建 HBase 叢集][hdinsight-hbase-provision-vnet]：由於 HBase 叢集已與虛擬網路整合，因此能夠部署到和應用程式相同的虛擬網路，讓應用程式得以和 HBase 直接通訊。
- [設定 HDInsight 中的 HBase 複寫](hdinsight-hbase-geo-replication.md)：了解如何跨兩個 Azure 資料中心設定 HBase 複寫。 
- [利用 HDInsight 中的 HBase 分析 Twitter 情緒][hbase-twitter-sentiment]：了解如何使用 HDInsight 之 Hadoop 叢集中的 HBase，執行巨量資料的即時[情緒分析](http://en.wikipedia.org/wiki/Sentiment_analysis)。

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 

<!---HONumber=AcomDC_0309_2016-->