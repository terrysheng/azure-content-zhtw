<properties 
	pageTitle="在 Azure 虛擬網路上佈建 HBase 叢集 | Azure" 
	description="了解如何在 Azure 虛擬網路上建立 HDInsight 叢集。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="jgao"/>

# 在 Azure 虛擬網路上佈建 HBase 叢集

了解如何在 [Azure 虛擬網路上建立 HDInsight 叢集][1]。 

由於 HBase 叢集已與虛擬網路整合，因此能夠部署到和應用程式相同的虛擬網路，讓應用程式得以和 HBase 直接通訊。其優點包括：

- Web 應用程式可以和 HBase 叢集節點直接連線，因而能夠使用 HBase Java RPC API 來進行通訊。
- 流量不必經過多個閘道和負載平衡器，因此會提升效能。 
- 能夠以更安全的方式處理敏感資訊，不必暴露公用端點。


##本文內容

- [必要條件](#prerequisites)
- [將 HBase 叢集佈建至虛擬網路](#hbaseprovision)
- [使用 HBase Java RPC API 連線到佈建在虛擬網路中的 HBase 叢集](#connect)
- [使用 PowerShell 佈建 HBase 叢集](#powershell)
- [後續步驟](#nextsteps)

##<a id="prerequisites"></a>必要條件
開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂閱**。Azure 是訂用帳戶型平台。如需有關如何取得訂閱的詳細資訊，請參閱[購買選項][azure-purchase-options]、[會員優惠][azure-member-offers]，或[免費試用版][azure-free-trial]。

- **已安裝並設定 Azure PowerShell 的工作站**。如需指示，請參閱[安裝並設定 Azure PowerShell][powershell-install]。若要執行 PowerShell 指令碼，您必須以系統管理員的身分執行 Azure PowerShell，並將執行原則設為 *RemoteSigned*。請參閱[使用 Set-ExecutionPolicy Cmdlet][2]。

	執行 PowerShell 指令碼之前，請確定您已使用下列 Cmdlet 連接到 Azure 訂用帳戶：

		Add-AzureAccount

	如果您有多個 Azure 訂用帳戶，請使用下列 Cmdlet 設定目前的訂用帳戶：

		Select-AzureSubscription <AzureSubscriptionName>


##<a id="hbaseprovision"></a>將 HBase 叢集佈建至虛擬網路。 

**若要使用管理入口網站建立虛擬網路：**

1. 登入 [Azure 管理入口網站][azure-portal]。
2. 按一下頁面底部的 [**新增**]，然後依序按一下 [**網路服務**]、[**虛擬網路**] 和 [**快速建立**]。
3. 輸入或選取下列值：

	- **名稱**：虛擬網路的名稱。
	- **位址空間**：為虛擬網路選擇位址空間，空間要夠大，這樣才能為叢集中的所有節點提供位址。否則，佈建將會失敗。如需本教學課程的逐步指南，您可以挑選任三個選項。 
	- **VM 計數上限**：選擇其中一個 VM 計數上限。這個值會決定可以在位址空間中建立的可能主機 (VM) 數目。如需本教學課程的逐步指南，**4096 [CIDR:/20]** 即已足夠。 
	- **位置**：此位置必須與您將建立 HBase 叢集的位置相同。
	- **DNS 伺服器**：本文使用的是 Azure 提供的內部 DNS 伺服器，因此您可以選擇 [**無**]。同時還支援更多自訂 DNS 伺服器的進階網路設定。如需詳細指引，請參閱[名稱解析 (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx)。
4. 按一下 [**建立虛擬網路**]。新的虛擬網路名稱隨即會出現在清單中。稍待片刻，直到 [狀態] 欄顯示 [**已建立**]。
5. 在主要窗格上，按一下您剛建立的虛擬網路。
6. 按一下頁面頂端的 [**儀表板**]。
7. 將 [**快速瀏覽**] 下方的 [**虛擬網路識別碼**] 記起來。佈建 HBase 叢集時需要用到它。
8. 按一下頁面頂端的 [**設定**]。
9. 在頁面底部，可看到預設的子網路名稱為 [**Subnet-1**]。您可以選擇性地重新命名子網路，或針對 HBase 叢集加入新的子網路。記下子網路名稱，佈建叢集時需要用到它。
10. 驗證將用於叢集之子網路的 [**CIDR (位址計數)**]。位址計數必須大於背景工作節點數量加上 7 的數目 (閘道：2、前端節點：2、Zookeeper：3).例如，如果您需要 10 個節點的 HBase 叢集，那麼子網路的位址計數必須大於 17 (10 + 7)。否則，部署將會失敗。

	> [AZURE.NOTE] 強烈建議您一個叢集只指定一個子網路。 

11. 如果您已更新子網路值，請按一下頁面底部的 [**儲存**]。



> [AZURE.NOTE] HDInsight 叢集使用 Azure Blob 儲存體來儲存資料。如需詳細資訊，請參閱[在 HDInsight 中搭配使用 Azure Blob 儲存體和 Hadoop][hdinsight-storage]。您將會需要儲存體帳戶和 Blob 儲存體容器。儲存體帳戶的位置必須符合虛擬網路位置和叢集位置。

**若要建立 Azure 儲存體帳戶和 Blob 儲存體容器：**

1. 登入 [Azure 管理入口網站][azure-portal]。
2. 按一下左下角的 [**新增**]，並指向 [**資料服務**]，接著指向 [**儲存體**]，然後按一下 [**快速建立**]。

3. 輸入或選取下列值：

	- **URL**：儲存體帳戶的名稱
	- **位置**：儲存體帳戶的位置。請確定此位置符合虛擬網路位置。不支援同質群組。
	- **複寫**：針對測試目的，請使用 [**本機備援**] 來降低成本。

4. 按一下 [建立儲存體帳戶]。您將在儲存體清單中看見新的儲存體帳戶。 
5. 等候新儲存體帳戶的 [**狀態**] 變更為 [**線上**]。
6. 按一下清單中的新儲存體帳戶加以選取。
7. 按一下頁面底部的 [**管理存取金鑰**]。
8. 請記下 [**儲存體帳戶名稱**] 和 [**主要存取金鑰**] (或 [**次要存取金鑰**]。這兩個金鑰都有效)。稍後在教學課程中需要這些資訊。
9. 按一下頁面頂端的 [**容器**]。
10. 按一下頁面底部的 [**新增**]。
11. 輸入容器名稱。此容器會用來作為 HBase 叢集的預設容器。依預設，預設的容器名稱會符合叢集名稱。讓 [**存取**] 欄位保持 [**私人**]。  
12. 按一下核取圖示以建立容器。

**使用 Azure 入口網站佈建 HBase 叢集：**

> [AZURE.NOTE] 如需有關使用 PowerShell 佈建新的 HBase 叢集的相關資訊，請參閱[使用 PowerShell 佈建 HBase 叢集]。(#powershell)。

1. 登入 [Azure 管理入口網站][azure-portal]。

2. 按一下左下角的 [**新增**]，並指向 [**資料服務**]，接著指向 [**HDInsight**]，然後按一下 [**自訂建立**]。

3. 輸入叢集名稱，選取叢集類型為 HBase，然後選取 HDINSIGHT 版本。

	![Provide details for the HBase cluster][img-provision-cluster-page1]

	按一下向右按鈕。

4. 輸入或選取下列：

	- **資料節點**：輸入 HBase 叢集的資料節點數目。 
	- **區域/虛擬網路**：選取您已建立的 Azure 虛擬網路。
	- **虛擬網路子網路**：選取子網路。預設名稱是 **Subnet-1**。

	按一下向右按鈕。

5. 輸入要用於此叢集的 Hadoop 使用者**使用者名稱**和**密碼**，然後按一下向右按鈕。
6. 選取是否要使用新的儲存體帳戶或現有的儲存體帳戶，來作為叢集的預設儲存體帳戶，然後按一下向右按鈕。

7. 在 [**指令碼動作**] 頁面上，按一下 [**加入指令碼動作**]，以提供自訂指令碼的詳細資料，您想要執行該指令碼以自訂叢集，做為正在建立的叢集。例如，您可以使用指令碼動作來自訂叢集以安裝 <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集][hdinsight-customize-cluster]。 
	
	![Configure Script Action to customize an HDInsight HBase cluster][img-provision-cluster-page5]

	<table border='1'>
		<tr><th>屬性</th><th>值</th></tr>
		<tr><td>名稱</td>
			<td>指定指令碼動作的名稱。</td></tr>
		<tr><td>指令碼 URI</td>
			<td>指定要叫用來自訂叢集的指令碼的 URI。</td></tr>
		<tr><td>節點類型</td>
			<td>指定執行自訂指定碼的節點。您可以選擇<b>所有節點</b>、<b>僅前端節點</b>或僅<b>背景工作節點</b>。
		<tr><td>參數</td>
			<td>如果指令碼要求，請指定參數。</td></tr>
	</table>

	您可以加入一個以上的指令碼動作，以在叢集上安裝多個元件。加入指令碼之後，按一下核取記號以開始佈建叢集。


若要開始使用新的 HBase 叢集，您可以使用[開始在 HDInsight 中搭配使用 HBase 與 Hadoop][hbase-get-started] 中提供的程序。

##<a id="connect"></a>使用 HBase Java RPC API 連線到佈建在虛擬網路中的 HBase 叢集

1.	將 IaaS 虛擬機器佈建至相同的 Azure 虛擬網路和相同的子網路中。因此，虛擬機器和 HBase 叢集會使用相同的內部 DNS 伺服器來解析主機名稱。若要這麼做，您必須選擇 [從組件庫] 選項，然後選取虛擬網路，而不是資料中心。如需指示，請參閱[建立執行 Windows Server 的虛擬機器][vm-create]。標準 Windows Server 2012 映像搭配小型 VM 即已足夠。
	
2.	使用 Java 應用程式從遠端連接到 HBase 時，您必須使用完整網域名稱 (FQDN)。為了決定此名稱，您必須取得 HBase 叢集的連線專用 DNS 尾碼。請使用 Curl 來查詢 Ambari，或使用遠端桌面連接到叢集。

	* **Curl** - 使用下列命令：

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		在傳回的 JSON 資料中，找出 "host_name" 項目。這會包含叢集中節點的完整網域名稱 (FQDN)。例如：

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		以叢集名稱開頭的網域名稱部分就是 DNS 尾碼。例如，mycluster.b1.cloudapp.net。

	* **PowerShell** - 使用下列 PowerShell 指令碼來註冊 **Get-ClusterDetail** 函數，此函數可用來傳回 DNS 尾碼。

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
			     Displays information to facilitate HDInsight cluster to cluster scinario within same virtual network.
				.Description
				 This command shows following 4 properties of an HDInsight cluster.
				 1. ZookeeperQuorum (only support HBase type cluster)
					Shows the value of hbase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (only support HBase type cluster)
					Shows the value of hbase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (only support HBase type cluster)
					Shows a list of host FQDNs which run HBase rest server.
				 4. FQDNSuffix (support all type cluster)
					Shows FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of hbase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of hbase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs which run HBase rest server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows FQDN suffix of hosts in the cluster.
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

		執行 PowerShell 指令碼之後，透過下列命令使用 Get-ClusterDetail 函數傳回 DNS 尾碼。使用此命令時，請指定您的 HDInsight HBase 叢集名稱、管理員名稱和管理員密碼。

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		這會傳回 DNS 尾碼。例如，**yourclustername.b4.internal.cloudapp.net**。

	> [AZURE.NOTE] 您也可以使用遠端桌面連接到 HBase 叢集 (將會連接到前端節點)，再從命令提示字元執行 **ipconfig** 來取得 DNS 尾碼。如需啟用 RDP 和使用 RDP 連線到叢集的指示，請參閱[使用 Azure 管理入口網站管理 HDInsight 中的 Hadoop 叢集][hdinsight-admin-portal]。
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!-- 
3.	變更虛擬機器的主要 DNS 尾碼設定。這可讓虛擬機器即使沒有明確的尾碼規格，也能自動解析 Hbase 叢集的主機名稱。例如， *workernode0* 的主機名稱會被正確地解析為 HBase 叢集的 workernode0。 

	若要變更設定：

	1. 使用 RDP 進入虛擬機器。 
	2. 開啟 [**本機群組原則編輯器**]。可執行檔為 gpedit.msc。
	3. 依序展開 [**電腦設定**]、[**系統管理範本**] 及 [**網路**]，然後按一下 [**DNS 用戶端**]。 
	- 將 [**主要 DNS 尾碼**] 設定為在步驟 2 取得的值： 

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. 按一下 [確定]。 
	5. 重新啟動虛擬機器。
-->

若要驗證虛擬機器能夠與 HBase 叢集通訊，請從虛擬機器使用下列命令  `ping headnode0.<dns suffix>`。例如，ping headnode0.mycluster.b1.cloudapp.net

若要在 Java 應用程式中使用此資訊，您可以依照[使用 Maven 建置在 HDInsight (Hadoop) 上使用 HBase 的 Java 應用程式](azure.microsoft.com/zh-tw/documentation/articles/hdinsight-hbase-build-java-maven/)中的步驟來建立應用程式。若要讓應用程式連接到遠端 HBase 伺服器，請修改此範例中的 **hbase-site.xml** 檔案，以使用 ZooKeeper 的 FQDN。例如：

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE] 如需 Azure 虛擬網路中的名稱解析的詳細資訊，包括如何使用您自己的 DNS 伺服器，請參閱[名稱解析 (DNS)](http://msdn.microsoft.com/zh-tw/library/azure/jj156088.aspx)。

##<a id="powershell"></a>使用 Azure PowerShell 佈建 HBase 叢集

**若要使用 Azure PowerShell 佈建 HBase 叢集**

1. 開啟 PowerShell ISE。
2. 複製下列內容，並將其貼到指令碼窗格中。

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"
		
		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 
		
		New-AzureHDInsightCluster -Name $hbaseClusterName `
		                          -ClusterType HBase `
		                          -Version 3.1 `
		                          -Location $location `
		                          -ClusterSizeInNodes $clusterSize `
		                          -Credential $creds `
		                          -VirtualNetworkId $vnetID `
		                          -SubnetName $subNetName `
		                          -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		                          -DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainerName $storageContainerName


3. 按一下 [**執行指令碼**]，或按 [**F5**]。
4. 若要驗證叢集，您可以在管理入口網站檢查叢集，或從底部窗格執行下列 PowerShell Cmdlet：

	Get-AzureHDInsightCluster 

##<a id="nextsteps"></a>後續步驟

在本教學課程中，我們已了解如何佈建 HBase 叢集。若要深入了解，請參閱：

- [開始使用 HDInsight][hdinsight-get-started]
- [在 HDInsight 中佈建 Hadoop 叢集][hdinsight-provision] 
- [開始在 HDInsight 中搭配使用 HBase 與 Hadoop][hbase-get-started]
- [使用 HDInsight 中的 HBase 分析 Twitter 情緒][hbase-twitter-sentiment]
- [虛擬網路概觀][vnet-overview]。


[1]: http://azure.microsoft.com/zh-tw/services/virtual-network/
[2]: http://technet.microsoft.com/zh-tw/library/ee176961.aspx
[3]: http://technet.microsoft.com/zh-tw/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started/
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment/
[vnet-overview]: http://msdn.microsoft.com/library/azure/jj156007.aspx
[vm-create]: ../virtual-machines-windows-tutorial/

[azure-portal]: https://manage.windowsazure.com
[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-purchase-options]: http://azure.microsoft.com/zh-tw/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/zh-tw/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/zh-tw/pricing/free-trial/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/#rdp

[hdinsight-powershell-reference]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn479228.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Provision details for the new HBase cluster"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Use Script Action to customize an HBase cluster"<!--HONumber=42-->
