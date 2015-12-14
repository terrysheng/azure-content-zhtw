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
   ms.date="11/18/2015"
   ms.author="jgao"/>

# 在 Azure 虛擬網路上佈建 HBase 叢集 

了解如何在 [Azure 虛擬網路][1]上建立 Azure HDInsight HBase 叢集。

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [在 Azure 虛擬網路上佈建 HBase 叢集](hdinsight-hbase-provision-vnet-v1.md)

由於 HBase 叢集已與虛擬網路整合，因此能夠部署到與您應用程式相同的虛擬網路，讓應用程式得以和 HBase 直接通訊。其優點包括：

- Web 應用程式可以直接連接到 HBase 叢集節點，因而能夠使用 HBase Java 遠端程序呼叫 (RPC) API 來進行通訊。
- 透過使流量不須經過多個閘道器及負載平衡器，以提升其效能。
- 能夠以更安全的方式處理敏感資訊，而不會暴露公用端點。

##必要條件
開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **具有 Azure PowerShell 的工作站**。請參閱[安裝及使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。如需指示，請參閱[安裝並設定 Azure PowerShell](../install-configure-powershell.md)。若要執行 Azure PowerShell 指令碼，您必須以系統管理員的身分執行 Azure PowerShell，並將執行原則設為 *RemoteSigned*。請參閱[使用 Set-executionpolicy cmdlet][2]。

	執行 Azure PowerShell 指令碼之前，請確定您已使用下列 Cmdlet 連接到 Azure 訂用帳戶：

		Add-AzureAccount

	如果您有多個 Azure 訂用帳戶，請使用下列 Cmdlet 設定目前的訂用帳戶：

		Select-AzureSubscription <AzureSubscriptionName>


## 將 HBase 叢集佈建到虛擬網路

應用程式通常由許多元件組成。在本教學課程中，您要先準備：

- Azure 虛擬網路
- Azure 儲存體帳戶
- Azure HDInsight HBase 叢集
- (選擇性) 做為 DNS 伺服器的 Azure 虛擬機器

Azure 資源管理員可讓您將應用程式中的資源做為群組使用。您可以透過單一、協調的作業來部署、更新或刪除應用程式的所有資源。您會使用部署的範本，且該範本可以用於不同的環境，例如測試、預備和生產環境。您可以檢視整個群組的彙總成本，為您的組織釐清計費。

**建立資源群組**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [新增]、[管理]，然後按一下 [資源群組]。
3. 輸入或選取下列值：

	- **資源群組名稱**：輸入資源群組名稱。
	- **訂用帳戶**：選取用於此資源群組的 Azure 訂用帳戶。
	- **資源群組位置**：選取 Azure 資料中心。此位置不一定要與 HDInsight 叢集位置相符。

4. 按一下 [建立]。

佈建 HBase 叢集之前，您需要有 Azure 虛擬網路。

**使用 Azure 入口網站建立虛擬網路**

1. 登入[入口網站](https://portal.azure.com)。
2. 依序按一下 [新增]、[網路] 和 [虛擬網路]。
3. 如果您要使用的是以 Windows 為基礎的 HDInsight 叢集，請在 [選取部署模型] 中選取 [傳統]；如果要使用的是以 Linux 為基礎的 HDInsight 叢集，請選取 [資源管理員]。最後，按一下 [建立]。

    > [AZURE.NOTE]以 Windows 為基礎的叢集需要 v1 (傳統) 虛擬網路，而以 Linux 為基礎的叢集需要 v2 (Azure 資源管理員) 虛擬網路。如果您沒有正確的網路類型，當您建立叢集時就無法使用。
    >
    > 如果虛擬網路上的資源不能為您計劃要建立的叢集所用，您可以建立可為叢集使用的新虛擬網路，並連接到不相容的虛擬網路。然後在叢集需要的網路版本中建立叢集，因為兩個網路聯結在一起，所以它就可以存取其他網路中的資源。如需連接傳統和新虛擬網路的詳細資訊，請參閱[連接傳統 VNet 和新的 VNet](../virtual-network/virtual-networks-arm-asm-s2s.md)。
    
4. 輸入或選取下列值：

	- **名稱**：虛擬網路的名稱。
	- **位址空間**：為虛擬網路選擇夠大的位址空間，如此才能為叢集中的所有節點提供位址。否則，佈建將會失敗。要逐步解說此教學課程，您可以使用預設值。按一下 [確定] 儲存變更。
    
        > [AZURE.NOTE]如果您要使用的這個虛擬網路有多個 HDInsight 叢集，強烈建議您每個叢集只指定一個子網路。
         
	- **資源群組**：選取您稍早在本教學課程中建立的資源群組。
	- **訂用帳戶**：選取您想要用於此虛擬網路的 Azure 訂用帳戶。
	- **位置** - 此位置必須與您將建立的 HBase 叢集相同。
    
        > [AZURE.NOTE]Azure HDInsight 僅支援以位置為基礎的虛擬網路，目前無法使用以同質群組為基礎的虛擬網路。

5. 按一下 [建立]。

根據預設，此虛擬網路使用 Azure 所提供的內部網域名稱系統 (DNS) 伺服器。同時也支援更多自訂 DNS 伺服器的進階網路功能設定。如需詳細指引，請參閱[名稱解析 (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

**(選擇性) 將 DNS 伺服器虛擬機器加入虛擬網路**

DNS 伺服器是選擇性的，但在某些案例中為必要。程序已記錄在[設定兩個 Azure 虛擬網路之間的 DNS][hdinsight-hbase-replication-dns]。基本上，您需要執行下列步驟：

1. 將 Azure 虛擬機器加入虛擬網路
2. 設定虛擬機器的靜態 IP 位址
3. 將 DNS 伺服器角色加入虛擬機器
4. 將 DNS 伺服器指派給虛擬網路

**使用 Azure 入口網站佈建 HBase 叢集**

> [AZURE.NOTE]如需使用 Azure PowerShell 佈建新 HBase 叢集的相關資訊，請參閱[使用 Azure PowerShell 佈建 HBase 叢集](#powershell)。


**建立 HDInsight 叢集**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 依序按一下 [新增]、[資料分析] 及 [HDInsight]。

    ![在 Azure 入口網站中建立新的叢集](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "在 Azure 入口網站中建立新的叢集")

3. 輸入或選取下列值：

  - **叢集名稱**：輸入叢集的名稱。如果該叢集名稱可使用，則名稱旁邊將會出現綠色的核取記號。
  - **叢集類型**：選取 [HBase]。
  - **叢集作業系統**：選取 [Windows Server 2012 R2 資料中心]。
  - **訂用帳戶**：選取將用於佈建此叢集的 Azure 訂用帳戶。
  - **資源群組**：選取您稍早在本教學課程中建立的資源群組。
  - **認證**：設定 Hadoop 使用者 (HTTP 使用者) 的使用者名稱和密碼。如果您啟用該叢集的遠端桌面，您必須設定遠端桌面使用者的使用者名稱、密碼和帳戶到期日。在底部按一下 [選取] 以儲存此變更。
  - **資料來源**：選取現有 Azure 儲存體帳戶或建立新的帳戶，這將用來做為該叢集的預設檔案系統。預設容器的預設名稱為叢集名稱。儲存體帳戶的位置也會決定該叢集所在的位置。
  - **節點定價層**：若為用於學習或評估用途，請選取 1 個區域節點，將成本降至最低。

  	- **選取方法**：將此設為 [來自所有訂用帳戶]，即可瀏覽您所有訂用帳戶中的儲存體帳戶。如果您想要輸入現有儲存體帳戶的 [儲存體名稱] 和 [存取金鑰]，請將此設為 [存取金鑰]。
  	- **選取儲存體帳戶/建立新的帳戶**：按一下 [選取儲存體帳戶]，瀏覽並選取您要與叢集產生關聯的現有儲存體帳戶。或按一下 [建立新項目] 來建立新的儲存體帳戶。使用出現的欄位輸入儲存體帳戶名稱。如果該名稱可用，將會出現綠色核取記號。
    - **選擇預設容器**：使用此選項可輸入要用於該叢集的預設容器名稱。雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。
  	- **位置**：儲存體帳戶所在或預計建立所在的地理區域。這個位置將會決定叢集位置。該叢集與預設儲存體帳戶必須並存於相同的 Azure 資料中心。

  - **節點定價層**：設定您針對該叢集所需的背景工作角色節點數目。該叢集的預估成本將會顯示在此刀鋒視窗內。
	- **選用組態**：在此教學課程中，您只需要設定 [虛擬網路]。選取您稍早在本教學課程中已建立的虛擬網路。請確定您也可以選取子網路。

4. 按一下 [建立]。


若要開始使用新的 HBase 叢集，您可以使用＜[開始在 HDInsight 中搭配使用 HBase 與 Hadoop](../hdinsight-hbase-get-started.md)＞中提供的程序。

##使用 HBase Java RPC API 連接到佈建在虛擬網路中的 HBase 叢集

1.	將基礎結構即服務 (IaaS) 虛擬機器佈建至相同的 Azure 虛擬網路和相同的子網路中。因此，虛擬機器和 HBase 叢集會使用相同的內部 DNS 伺服器來解析主機名稱。若要這麼做，您必須選擇 [從組件庫] 選項，並選取虛擬網路而不是資料中心。如需指示，請參閱[建立執行 Windows Server 的虛擬機器](../virtual-machines-windows-tutorial.md)。標準 Windows Server 2012 映像搭配小型 VM 即已足夠。

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

	> [AZURE.NOTE]您也可以使用遠端桌面連接到 HBase 叢集 (將連接到前端節點)，再從命令提示字元執行 **ipconfig** 以取得 DNS 尾碼。如需啟用遠端桌面通訊協定 (RDP) 並使用 RDP 連接到叢集的指示，請參閱[使用 Azure 入口網站在 HDInsight 中管理 Hadoop 叢集][hdinsight-admin-portal]。
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

> [AZURE.NOTE]如需 Azure 虛擬網路中的名稱解析的詳細資訊，包括如何使用您自己的 DNS 伺服器，請參閱[名稱解析 (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

##使用 Azure PowerShell 佈建 HBase 叢集

**使用 Azure PowerShell 佈建 HBase 叢集**

1. 開啟 Azure PowerShell 整合式指令碼環境 (ISE)。
2. 複製下列內容，並將其貼到指令碼窗格中：

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$resourceGroup = "<AzureResourceGroupName>"
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"

		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password)

		New-AzureHDInsightCluster -ResourceGroupName $resourceGroup `
		                          -ClusterName $hbaseClusterName `
				                    	-ClusterType HBase `
				                    	-Location $location `
				                    	-ClusterSizeInNodes $clusterSize `
		                          -HttpCredential $creds `
				                    	-VirtualNetworkId $vnetID `
				                    	-SubnetName $subNetName `
				                    	-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
				                    	-DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainer $storageContainerName


3. 按一下 [執行指令碼]，或按 [F5]。
4. 若要驗證叢集，您可以從 Azure 入口網站檢查，或從底部窗格執行下列 Azure PowerShell Cmdlet：

	Get-AzureHDInsightCluster

##後續步驟

在本教學課程中，您已了解如何佈建 HBase 叢集。若要深入了解，請參閱：

- [開始使用 HDInsight](../hdinsight-get-started.md)
- [在 HDInsight 中設定 HBase 複寫](hdinsight-hbase-geo-replication.md)
- [在 HDInsight 中佈建 Hadoop 叢集](hdinsight-provision-clusters.md)
- [開始在 HDInsight 中搭配使用 HBase 與 Hadoop](../hdinsight-hbase-get-started.md)
- [使用 HDInsight 中的 HBase 分析 Twitter 情緒](../hdinsight-hbase-twitter-sentiment.md)
- [虛擬網路概觀][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started.md
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines-windows-tutorial.md

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


[powershell-install]: ../install-configure-powershell.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "佈建新 HBase 叢集的詳細資料"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "使用指令碼動作以自訂 HBase 叢集"

[azure-preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_1203_2015-->