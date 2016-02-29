<properties
	pageTitle="在虛擬網路上佈建 HBase 叢集 | Microsoft Azure"
	description="開始在 Azure HDInsight 中使用 HBase。了解如何在 Azure 虛擬網路上建立 HDInsight HBase 叢集。"
	keywords=""
	services="hdinsight,virtual-network"
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
   ms.date="02/17/2016"
   ms.author="jgao"/>

# 在 Azure 虛擬網路上建立 HBase 叢集 

了解如何在 [Azure 虛擬網路][1]上建立 Azure HDInsight HBase 叢集。

由於 HBase 叢集已與虛擬網路整合，因此能夠部署到與您應用程式相同的虛擬網路，讓應用程式得以和 HBase 直接通訊。其優點包括：

- Web 應用程式可以直接連接到 HBase 叢集節點，因而能夠使用 HBase Java 遠端程序呼叫 (RPC) API 來進行通訊。
- 透過使流量不須經過多個閘道器及負載平衡器，以提升其效能。
- 能夠以更安全的方式處理敏感資訊，而不會暴露公用端點。

###必要條件
開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **具有 Azure PowerShell 的工作站**。請參閱[安裝及使用 Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。

## 在虛擬網路上建立 HBase 叢集

本節中，您將在 HDInsight 中使用 [Azure ARM 範本](../resource-group-template-deploy.md)建立以 Linux 為基礎的 HBase 叢集。進行本教學課程並不需要具備 Azure ARM 範本經驗。如需其他叢集建立方法及了解各項設定，請參閱[建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。如需有關使用 ARM 範本在 HDInsight 中建立 Hadoop 叢集的詳細資訊，請參閱[使用 ARM 範本在 HDInsight 中建立 Hadoop 叢集](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

1. 按一下以下影像，以在 Azure 入口網站中開啟 ARM 範本。ARM 範本位於公用 Blob 容器中。 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/zh-TW/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 從 [參數] 刀鋒視窗，輸入下列資料：

    - **ClusterName**：輸入您將建立的 Hadoop 叢集的名稱。
    - **叢集登入名稱和密碼**：預設登入名稱是 **admin**。
    - **SSH 使用者名稱和密碼**：預設使用者名稱是 **sshuser**。您可以將它重新命名。 

    許多屬性已硬式編碼至範本。例如：
    
    - 位置：美國東部
    - 叢集背景工作節點計數：4
    - 預設儲存體帳戶：<Cluster Name>store
    - 虛擬網路名稱：<Cluster Name>-vnet
    - 虛擬網路位址空間：10.0.0.0/16
    - 子網路名稱：default
    - 子網路位址範圍：10.0.0.0/24

3. 按一下 [確定] 儲存參數。
4. 在 [自訂部署] 刀鋒視窗中，按一下 [資源群組] 下拉式方塊，然後按按一下 [新增] 來建立新的資源群組。資源群組是聚集叢集、相依儲存體帳戶和其他已連結資源的容器。
5. 按一下 [法律條款]，然後按一下 [建立]。
6. 按一下 [建立]。您會看到新的圖格，標題為 [提交範本部署的部署]。大約需要 20 分鐘的時間來建立叢集。一旦建立叢集後，您可以在入口網站按一下 [叢集] 刀鋒視窗來開啟它。

完成本教學課程之後，您可以刪除叢集。利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地進行刪除。您也需支付 HDInsight 叢集的費用 (即使未使用)。由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。如需有關刪除叢集的指示，請參閱[使用 Azure 入口網站管理 HDInsight 中的 Hadoop 叢集](hdinsight-administer-use-management-portal.md#delete-clusters)。

若要開始使用新的 HBase 叢集，您可以使用＜[開始在 HDInsight 中搭配使用 HBase 與 Hadoop](hdinsight-hbase-tutorial-get-started.md)＞中提供的程序。

##使用 HBase Java RPC API 連接到 HBase 叢集

1.	相同的 Azure 虛擬網路和相同的子網路中建立基礎結構即服務 (IaaS) 虛擬機器。因此，虛擬機器和 HBase 叢集會使用相同的內部 DNS 伺服器來解析主機名稱。若要這麼做，您必須選擇 [從組件庫] 選項，並選取虛擬網路而不是資料中心。如需指示，請參閱[建立執行 Windows Server 的虛擬機器](../virtual-machines/virtual-machines-windows-tutorial.md)。標準 Windows Server 2012 映像搭配小型 VM 即已足夠。

2.	使用 Java 應用程式從遠端連接到 HBase 時，您必須使用完整網域名稱 (FQDN)。若要決定此名稱，您必須取得 HBase 叢集的連線特定 DNS 尾碼。請使用 Curl 以查詢 Ambari，或使用遠端桌面連接到叢集。

	* **Curl** - 使用下列命令：

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		在傳回的 JavaScript 物件標記法 (JSON) 資料中，找出 "host\_name" 項目。這會包含叢集中節點的 FQDN。例如：

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		以叢集名稱開頭的網域名稱部分就是 DNS 尾碼。例如，mycluster.b1.cloudapp.net。

	* **Azure PowerShell** - 使用下列 Azure PowerShell 指令碼以註冊 **Get-ClusterDetail** 函數，並可使用該函數傳回 DNS 尾碼：

			function Get-ClusterDetail(
			    [String]
			    [Parameter( Position=0, Mandatory=$true )]
			    $ClusterDnsName,
				[String]
			    [Parameter( Position=1, Mandatory=$true )]
			    $Username,
				[String]
			    [Parameter( Position=2, Mandatory=$true )]
			    $Password,
			    [String]
			    [Parameter( Position=3, Mandatory=$true )]
			    $PropertyName
				)
			{
			<#
			    .SYNOPSIS
			     Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
				.Description
				 This command shows the following 4 properties of an HDInsight cluster:
				 1. ZookeeperQuorum (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (supports only HBase type cluster)
					Shows a list of host FQDNs that run the HBase REST server.
				 4. FQDNSuffix (supports all cluster types)
					Shows the FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of HBase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs that run the HBase REST server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows the FQDN suffix of hosts in the cluster.
			#>

				$DnsSuffix = ".azurehdinsight.net"

				$ClusterFQDN = $ClusterDnsName + $DnsSuffix
				$webclient = new-object System.Net.WebClient
				$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

				if($PropertyName -eq "ZookeeperQuorum")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
				}
				if($PropertyName -eq "ZookeeperClientPort")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
				}
				if($PropertyName -eq "HBaseRestServers")
				{
					$Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
					$Response1 = $webclient.DownloadString($Url1)
					$JsonObject1 = $Response1 | ConvertFrom-Json
					$PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

					$Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
					$Response2 = $webclient.DownloadString($Url2)
					$JsonObject2 = $Response2 | ConvertFrom-Json
					foreach ($host_component in $JsonObject2.host_components)
					{
						$ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
						Write-host $ConnectionString
					}
				}
				if($PropertyName -eq "FQDNSuffix")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					$FQDN = $JsonObject.host_components[0].HostRoles.host_name
					$pos = $FQDN.IndexOf(".")
					$Suffix = $FQDN.Substring($pos + 1)
					Write-host $Suffix
				}
			}

		執行 Azure PowerShell 指令碼之後，透過下列命令使用 **Get-ClusterDetail** 函數傳回 DNS 尾碼。使用此命令時，請指定您的 HDInsight HBase 叢集名稱、管理員名稱和管理員密碼。

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		這會傳回 DNS 尾碼。例如，**yourclustername.b4.internal.cloudapp.net**。

	> [AZURE.NOTE] 您也可以使用遠端桌面連接到 HBase 叢集 (將連接到前端節點)，再從命令提示字元執行 **ipconfig** 以取得 DNS 尾碼。如需啟用遠端桌面通訊協定 (RDP) 並使用 RDP 連接到叢集的指示，請參閱[使用 Azure 入口網站在 HDInsight 中管理 Hadoop 叢集][hdinsight-admin-portal]。
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!--
3.	Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

	To make the configuration change:

	1. RDP into the virtual machine.
	2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
	3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
	- Set **Primary DNS Suffix** to the value obtained in step 2:

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Click **OK**.
	5. Reboot the virtual machine.
-->

若要驗證虛擬機器能夠與 HBase 叢集通訊，請從虛擬機器使用命令 `ping headnode0.<dns suffix>`。例如，ping headnode0.mycluster.b1.cloudapp.net。

若要在 Java 應用程式中使用此資訊，您可以依照＜[使用 Maven 建置在 HDInsight (Hadoop) 上使用 HBase 的 Java 應用程式](hdinsight-hbase-build-java-maven.md)＞(英文) 中的步驟來建立應用程式。若要讓應用程式連接到遠端 HBase 伺服器，請修改此範例中的 **hbase-site.xml** 檔案，以使用 Zookeeper 的 FQDN。例如：

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE] 如需 Azure 虛擬網路中的名稱解析的詳細資訊，包括如何使用您自己的 DNS 伺服器，請參閱[名稱解析 (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

##後續步驟

在本教學課程中，您已了解如何建立 HBase 叢集。若要深入了解，請參閱：

- [開始使用 HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [在 HDInsight 中設定 HBase 複寫](hdinsight-hbase-geo-replication.md)
- [在 HDInsight 中建立 Hadoop 叢集](hdinsight-provision-clusters.md)
- [開始在 HDInsight 中搭配使用 HBase 與 Hadoop](hdinsight-hbase-tutorial-get-started.md)
- [使用 HDInsight 中的 HBase 分析 Twitter 情緒](hdinsight-hbase-analyze-twitter-sentiment.md)
- [虛擬網路概觀][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "佈建新 HBase 叢集的詳細資料"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "使用指令碼動作以自訂 HBase 叢集"

[azure-preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0218_2016-->