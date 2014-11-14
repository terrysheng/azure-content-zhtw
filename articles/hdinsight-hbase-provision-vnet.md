<properties urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="在 Azure 虛擬網路上佈建 HBase 叢集 | Azure" metaKeywords="" description="了解如何在 Azure 虛擬網路上建立 HDInsight 叢集。" metaCanonical="" services="hdinsight" documentationCenter="" title="在 Azure 虛擬網路上佈建 HBase 叢集" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao" />

# 在 Azure 虛擬網路上佈建 HBase 叢集

了解如何在 Azure 虛擬網路上建立 HDInsight 叢集。

由於 HBase 叢集已與虛擬網路整合，因此能夠部署到和應用程式相同的虛擬網路，讓應用程式得以和 HBase 直接通訊。其優點包括：

-   Web 應用程式可以和 HBase 叢集節點直接連線，因而能夠使用 HBase Java RPC API 來進行通訊。
-   流量不必經過多個閘道和負載平衡器，因此會提升效能。
-   能夠以更安全的方式處理敏感資訊，不必暴露公用端點。

## 本文內容

-   [必要條件][必要條件]
-   [將 HBase 叢集佈建至虛擬網路][將 HBase 叢集佈建至虛擬網路]
-   [使用 HBase Java RPC API 連線到佈建在虛擬網路中的 HBase 叢集][使用 HBase Java RPC API 連線到佈建在虛擬網路中的 HBase 叢集]
-   [使用 PowerShell 佈建 HBase 叢集][使用 PowerShell 佈建 HBase 叢集]
-   [後續步驟][後續步驟]

## <span id="prerequisites"></span></a>必要條件

開始進行本教學課程之前，您必須具備下列條件：

-   **Azure 訂用帳戶**。Azure 是訂用帳戶型平台。如需取得訂用帳戶的詳細資訊，請參閱[購買選項][購買選項]、[成員優惠][成員優惠]或[免費試用][免費試用]。

-   **已安裝並設定 Azure PowerShell 的工作站**。如需指示，請參閱[安裝並設定 Azure PowerShell][安裝並設定 Azure PowerShell]。若要執行 PowerShell 指令碼，您必須以系統管理員的身分執行 Azure PowerShell，並將執行原則設為 *RemoteSigned*。請參閱[執行 Windows PowerShell 指令碼][執行 Windows PowerShell 指令碼]。

    執行 PowerShell 指令碼之前，請確定您已使用下列 Cmdlet 連接到 Azure 訂用帳戶：

        Add-AzureAccount

    如果您有多個 Azure 訂用帳戶，請使用下列 Cmdlet 設定目前的訂用帳戶：

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>將 HBase 叢集佈建至虛擬網路。

**若要使用管理入口網站建立虛擬網路：**

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。
2.  依序按一下頁面底部的 [新增]、[網路服務]、[虛擬網路] 及 [快速建立]。
3.  輸入或選取下列值：

    -   **名稱**：虛擬網路的名稱。
    -   **位址空間**：為虛擬網路選擇位址空間，空間要夠大，這樣才能為叢集中的所有節點提供位址。否則，佈建將會失敗。
    -   **VM 計數上限**：選擇其中一個 VM 計數上限。
    -   **位置**：此位置必須與您將建立 HBase 叢集的位置相同。
    -   **DNS 伺服器**：本文使用的是 Azure 提供的內部 DNS 伺服器，因此您可以選擇 [無]。同時還支援更多自訂 DNS 伺服器的進階網路設定。如需詳細指引，請參閱 <http://msdn.microsoft.com/library/azure/jj156088.aspx>。

4.  按一下 [建立虛擬網路]。新的虛擬網路名稱隨即會出現在清單中。稍待片刻，直到 [狀態] 欄顯示 [已建立]。
5.  在主要窗格上，按一下您剛建立的虛擬網路。
6.  按一下頁面頂端的 [儀表板]。
7.  將 [quick glance] 下方的 [VIRTUAL NETWORK ID] 記起來。佈建 HBase 叢集時需要用到它。
8.  按一下頁面頂端的 [設定]。
9.  在頁面底部，可看到預設的子網路名稱為 [Subnet-1]。您可以選擇性地重新命名子網路，或針對 HBase 叢集加入新的子網路。記下子網路名稱，佈建叢集時需要用到它。
10. 驗證將用於叢集之子網路的 [CIDR (位址計數)]。位址計數必須大於背景工作節點數量加上 7 的數目 (閘道：2、前端節點：2、Zookeeper：3)。例如，如果您需要 10 個節點的 HBase 叢集，那麼子網路的位址計數必須大於 17 (10 + 7)。否則，部署將會失敗。

    > [WACOM.NOTE] 強烈建議您一個叢集只指定一個子網路。

11. 如果您已更新子網路值，請按一下頁面底部的 [儲存]。

> [WACOM.NOTE] HDInsight 叢集使用 Azure Blob 儲存體來儲存資料。如需詳細資訊，請參閱[在 HDInsight 中搭配使用 Azure Blob 儲存體和 Hadoop][在 HDInsight 中搭配使用 Azure Blob 儲存體和 Hadoop] (英文)。您將會需要儲存體帳戶和 Blob 儲存體容器。儲存體帳戶的位置必須符合虛擬網路位置和叢集位置。

**若要建立 Azure 儲存體帳戶和 Blob 儲存體容器：**

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。
2.  按一下左下角的 [新增]，並指向 [資料服務]，接著指向 [儲存體]，然後按一下 [快速建立]。

3.  輸入或選取下列值：

    -   **URL**：儲存體帳戶的名稱
    -   **位置**：儲存體帳戶的位置。請確定此位置符合虛擬網路位置。不支援同質群組。
    -   **複寫**：針對測試目的，請使用 [本機備援] 來降低成本。

4.  按一下 [建立儲存體帳戶]。您將在儲存體清單中看見新的儲存體帳戶。
5.  等候新儲存體帳戶的 [狀態] 變更為 [線上]。
6.  按一下清單中的新儲存體帳戶加以選取。
7.  按一下頁面底部的 [MANAGE ACCESS KEYS]。
8.  請記下 [儲存體帳戶名稱] 和 [主要存取金鑰] (或 [次要存取金鑰]。這兩個金鑰都有效)。稍後在教學課程中需要這些資訊。
9.  按一下頁面頂端的 [容器]。
10. 按一下頁面底部的 [新增]。
11. 輸入容器名稱。此容器會用來作為 HBase 叢集的預設容器。依預設，預設的容器名稱會符合叢集名稱。讓 [存取] 欄位保持 [私人]。
12. 按一下核取圖示以建立容器。

**使用 Azure 入口網站佈建 HBase 叢集：**

> [WACOM.NOTE] 如需有關使用 PowerShell 佈建新的 HBase 叢集的相關資訊，請參閱＜[使用 PowerShell 佈建 HBase 叢集][使用 PowerShell 佈建 HBase 叢集]＞。

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。
2.  按一下左下角的 [新增]，並指向 [資料服務]，接著指向 [HDINSIGHT]，然後按一下 [自訂建立]。
3.  輸入 [叢集名稱]，選取要用於叢集的 [HDINSIGHT 版本]。

    ![cluster name and version fields][cluster name and version fields]

4.  選取要為叢集建立的 [資料節點] 數目，以及要用於此叢集的區域或 Azure 虛擬網路 ([區域/虛擬網路])。

    ![number of nodes and region fields][number of nodes and region fields]

5.  輸入要用於此叢集的系統管理員 [使用者名稱] 和 [密碼]。

    ![administrator name and password fields][administrator name and password fields]

6.  選擇使用新的或現有的儲存體帳戶。如果是新的，請輸入 [帳戶名稱] 和 [預設容器] 來建立。最後，選取勾選記號來建立叢集。

    ![storage account selection][storage account selection]

若要開始使用新的 HBase 叢集，您可以使用＜[開始在 HDInsight 中搭配使用 HBase 與 Hadoop][開始在 HDInsight 中搭配使用 HBase 與 Hadoop]＞中提供的程序。

## <span id="connect"></span></a>使用 HBase Java RPC API 連線到佈建在虛擬網路中的 HBase 叢集

1.  將 IaaS 虛擬機器佈建至相同的 Azure 虛擬網路和相同的子網路中。因此，虛擬機器和 HBase 叢集會使用相同的內部 DNS 伺服器來解析主機名稱。若要這麼做，您必須選擇 [從組件庫] 選項，然後選取虛擬網路，而不是資料中心。如需指示，請參閱[建立執行 Windows Server 的虛擬機器][建立執行 Windows Server 的虛擬機器] (英文)。標準 Windows Server 2012 映像搭配小型 VM 即已足夠。

2.  使用 Java 應用程式從遠端連接到 HBase 時，您必須使用完整網域名稱 (FQDN)。為了決定此名稱，您必須取得 HBase 叢集的連線專用 DNS 尾碼。請使用 Curl 來查詢 Ambari，或使用遠端桌面連接到叢集。

    -   **Curl** - 使用下列命令：

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        在傳回的 JSON 資料中，找出 "host\_name" 項目。這會包含叢集中節點的完整網域名稱 (FQDN)。例如：

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        以叢集名稱開頭的網域名稱部分就是 DNS 尾碼。例如，mycluster.b1.cloudapp.net。

    -   **PowerShell** - 使用下列 PowerShell 指令碼來註冊 **Get-ClusterDetail** 函數，此函數可用來傳回 DNS 尾碼。

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

    > [WACOM.NOTE] 您也可以使用遠端桌面連接到 HBase 叢集 (將會連接到前端節點)，再從命令提示字元執行 **ipconfig** 來取得 DNS 尾碼。如需啟用 RDP 和使用 RDP 連線到叢集的指示，請參閱[使用 Azure 管理入口網站管理 HDInsight 中的 Hadoop 叢集][使用 Azure 管理入口網站管理 HDInsight 中的 Hadoop 叢集] (英文)。
    >
    > ![hdinsight.hbase.dns.surffix][hdinsight.hbase.dns.surffix]

<!--  3.  Change the Primary DNS Suffix configuration of the virtual machine. This enables virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to the workernode0 of the HBase cluster.       To make the configuration change:      1. RDP into the virtual machine.      2. Open **Local Group Policy Editor**. The executable is gpedit.msc.     3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.      - Set **Primary DNS Suffix** to the value obtained in the step 2:           ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]     4. Click **OK**.      5. Reboot the virtual machine. -->

若要驗證虛擬機器能夠與 HBase 叢集通訊，請從虛擬機器使用下列命令 `ping headnode0.<dns suffix>`。例如，ping headnode0.mycluster.b1.cloudapp.net

若要在 Java 應用程式中使用此資訊，您可以依照＜[使用 Maven 建置在 HDInsight (Hadoop) 上使用 HBase 的 Java 應用程式][使用 Maven 建置在 HDInsight (Hadoop) 上使用 HBase 的 Java 應用程式]＞(英文) 中的步驟來建立應用程式。若要讓應用程式連接到遠端 HBase 伺服器，請修改此範例中的 **hbase-site.xml** 檔案，以使用 ZooKeeper 的 FQDN。例如：

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [WACOM.NOTE] 如需 Azure 虛擬網路中的名稱解析的詳細資訊，包括如何使用您自己的 DNS 伺服器，請參閱＜[名稱解析 (DNS)][名稱解析 (DNS)]＞。

## <span id="powershell"></span></a>使用 Azure PowerShell 佈建 HBase 叢集：\*\*

1.  開啟 PowerShell ISE。
2.  複製下列內容，並將其貼到指令碼窗格中。

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

3.  按一下 [執行指令碼]，或按 [F5]。
4.  若要驗證叢集，您可以在管理入口網站檢查叢集，或從底部窗格執行下列 PowerShell Cmdlet：

    Get-AzureHDInsightCluster

## <span id="nextsteps"></span></a>後續步驟

在本教學課程中，我們已了解如何佈建 HBase 叢集。若要深入了解，請參閱：

-   [開始使用 HDInsight][開始使用 HDInsight]
-   [在 HDInsight 中佈建 Hadoop 叢集 (英文)][在 HDInsight 中佈建 Hadoop 叢集 (英文)]
-   [開始在 HDInsight 中搭配使用 HBase 與 Hadoop (英文)][開始在 HDInsight 中搭配使用 HBase 與 Hadoop]
-   [使用 HDInsight 中的 HBase 分析 Twitter 情緒 (英文)][使用 HDInsight 中的 HBase 分析 Twitter 情緒 (英文)]
-   [虛擬網路概觀][虛擬網路概觀]。

  [必要條件]: #prerequisites
  [將 HBase 叢集佈建至虛擬網路]: #hbaseprovision
  [使用 HBase Java RPC API 連線到佈建在虛擬網路中的 HBase 叢集]: #connect
  [使用 PowerShell 佈建 HBase 叢集]: #powershell
  [後續步驟]: #nextsteps
  [購買選項]: http://azure.microsoft.com/zh-tw/pricing/purchase-options/
  [成員優惠]: http://azure.microsoft.com/zh-tw/pricing/member-offers/
  [免費試用]: http://azure.microsoft.com/zh-tw/pricing/free-trial/
  [安裝並設定 Azure PowerShell]: ../install-configure-powershell
  [執行 Windows PowerShell 指令碼]: http://technet.microsoft.com/zh-tw/library/ee176949.aspx
  [Azure 管理入口網站]: https://manage.windowsazure.com
  [在 HDInsight 中搭配使用 Azure Blob 儲存體和 Hadoop]: ../hdinsight-use-blob-storage/
  [cluster name and version fields]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png
  [number of nodes and region fields]: ./media/hdinsight-hbase-provision-vnet/hbasewizard2.png
  [administrator name and password fields]: ./media/hdinsight-hbase-provision-vnet/hbasewizard3.png
  [storage account selection]: ./media/hdinsight-hbase-provision-vnet/hbasewizard4.png
  [開始在 HDInsight 中搭配使用 HBase 與 Hadoop]: ../hdinsight-hbase-get-started/
  [建立執行 Windows Server 的虛擬機器]: ../virtual-machines-windows-tutorial/
  [使用 Azure 管理入口網站管理 HDInsight 中的 Hadoop 叢集]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [使用 Maven 建置在 HDInsight (Hadoop) 上使用 HBase 的 Java 應用程式]: azure.microsoft.com/zh-tw/documentation/articles/hdinsight-hbase-build-java-maven/
  [名稱解析 (DNS)]: http://msdn.microsoft.com/zh-tw/library/azure/jj156088.aspx
  [開始使用 HDInsight]: ../hdinsight-get-started/
  [在 HDInsight 中佈建 Hadoop 叢集 (英文)]: ../hdinsight-provision-clusters/
  [使用 HDInsight 中的 HBase 分析 Twitter 情緒 (英文)]: ../hdinsight-hbase-twitter-sentiment/
  [虛擬網路概觀]: http://msdn.microsoft.com/library/azure/jj156007.aspx
