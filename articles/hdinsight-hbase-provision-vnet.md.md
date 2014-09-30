<properties linkid="manage-services-hdinsight-hbase-provision-on-vnet" urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Provision HBase clusters on Azure Virtual Network | Azure" metaKeywords="" description="Learn how to create HDInsight clusters on Azure Virtual Network." metaCanonical="" services="hdinsight" documentationCenter="" title="Provision HBase clusters on Azure Virtual Network" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao"></tags>

# 在 Azure 虛擬網路上佈建 HBase 叢集

了解如何在 Azure 虛擬網路上建立 HDInsight 叢集。

由於 HBase 叢集已與虛擬網路整合，因此能夠部署到和應用程式相同的虛擬網路，讓應用程式得以和 HBase 直接通訊。其優點包括：

-   Web 應用程式可以和 HBase 叢集節點直接連線，因而能夠使用 HBase Java RPC API 來進行通訊。
-   流量不必經過多個閘道和負載平衡器，因此會提升效能。
-   能夠以更安全的方式處理敏感資訊，不必暴露公用端點。

> [WACOM.NOTE] 目前只有 HBase 叢集可以佈建至 Azure 虛擬網路。Hadoop 叢集不在支援範圍內。

## 本文內容

-   [必要條件][]
-   [將 HBase 叢集佈建至虛擬網路][]
-   [使用 HBase Java RPC API 連線到佈建在虛擬網路中的 HBase 叢集][]
-   [後續步驟][]

## <span id="prerequisites"></span></a>必要條件

開始進行本教學課程之前，您必須具備下列條件：

-   **Azure 訂用帳戶**。Azure 是訂用帳戶型平台。如需取得訂用帳戶的詳細資訊，請參閱[購買選項][]、[成員優惠][]或[免費試用][]。

-   **已安裝並設定 Azure PowerShell 的工作站**。如需指示，請參閱[安裝並設定 Azure PowerShell][]。若要執行 PowerShell 指令碼，您必須以系統管理員的身分執行 Azure PowerShell，並將執行原則設為 *RemoteSigned*。請參閱[執行 Windows PowerShell 指令碼][]。

    執行 PowerShell 指令碼之前，請確定您已使用下列 Cmdlet 連接到 Azure 訂用帳戶：

        Add-AzureAccount

    如果您有多個 Azure 訂用帳戶，請使用下列 Cmdlet 設定目前的訂用帳戶：

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>將 HBase 叢集佈建至虛擬網路。

**若要使用管理入口網站建立虛擬網路：**

1.  登入 [Azure 管理入口網站][]。
2.  依序按一下頁面底部的 [新增]、[網路服務]、[虛擬網路] 及 [快速建立]。
3.  輸入或選取下列值：

    -   **名稱**：虛擬網路的名稱。
    -   **位址空間**：為虛擬網路選擇位址空間，空間要夠大，這樣才能為叢集中的所有節點提供位址。否則，佈建將會失敗。
    -   **VM 計數上限**：選擇其中一個 VM 計數上限。
    -   **位置**：此位置必須與您將建立 HBase 叢集的位置相同。
    -   **DNS 伺服器**：本文使用的是 Azure 提供的內部 DNS 伺服器，因此您可以選擇 [無]。同時還支援更多自訂 DNS 伺服器的進階網路設定。如需詳細指引，請參閱 [][]<http://msdn.microsoft.com/library/azure/jj156088.aspx></a>。

4.  按一下 [建立虛擬網路]。新的虛擬網路名稱隨即會出現在清單中。稍待片刻，直到 [狀態] 欄顯示 [已建立]。
5.  在主要窗格上，按一下您剛建立的虛擬網路。
6.  按一下頁面頂端的 [儀表板]。
7.  將 [quick glance] 下方的 [VIRTUAL NETWORK ID] 記起來。佈建 HBase 叢集時需要用到它。
8.  按一下頁面頂端的 [設定]。
9.  在頁面底部，可看到預設的子網路名稱為 [Subnet-1]。您可以選擇性地重新命名子網路，或針對 HBase 叢集加入新的子網路。記下子網路名稱，佈建叢集時需要用到它。
10. 驗證將用於叢集之子網路的 [CIDR (位址計數)]。位址計數必須大於背景工作節點數量加上 7 的數目 (閘道：2、前端節點：2、Zookeeper：3)。例如，如果您需要 10 個節點的 HBase 叢集，那麼子網路的位址計數必須大於 17 (10 + 7)。否則，部署將會失敗。

    > [WACOM.NOTE] 強烈建議您一個叢集只指定一個子網路。

11. 如果您已更新子網路值，請按一下頁面底部的 [儲存]。

> [WACOM.NOTE] HDInsight 叢集使用 Azure Blob 儲存體來儲存資料。如需詳細資訊，請參閱[在 HDInsight 中搭配使用 Azure Blob 儲存體和 Hadoop][] (英文)。您將會需要儲存體帳戶和 Blob 儲存體容器。儲存體帳戶的位置必須符合虛擬網路位置和叢集位置。

**若要建立 Azure 儲存體帳戶和 Blob 儲存體容器：**

1.  登入 [Azure 管理入口網站][]。
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

**若要使用 Azure PowerShell 佈建 HBase 叢集：**

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

若要測試新的 HBase 叢集，您可以使用[開始在 HDInsight 中搭配使用 HBase 與 Hadoop][] (英文) 中提供的程序。

## <span id="connect"></span></a>使用 HBase Java RPC API 連線到佈建在虛擬網路中的 HBase 叢集

1.  將 IaaS 虛擬機器佈建至相同的 Azure 虛擬網路和相同的子網路中。因此，虛擬機器和 HBase 叢集會使用相同的內部 DNS 伺服器來解析主機名稱。若要這麼做，您必須選擇 [從組件庫] 選項，然後選取虛擬網路，而不是資料中心。如需指示，請參閱[建立執行 Windows Server 的虛擬機器][] (英文)。標準 Windows Server 2012 映像搭配小型 VM 即已足夠。

2.  取得 HBase 叢集的連線專用 DNS 尾碼。若要這麼做，請用 RDP 進入 HBase 叢集 (您將連線至前端節點)，然後從命令提示字元執行 **ipconfig**。如需啟用 RDP 和使用 RDP 連線到叢集的指示，請參閱[使用 Azure 管理入口網站管理 HDInsight 中的 Hadoop 叢集][] (英文)。

    ![hdinsight.hbase.dns.surffix][]

3.  變更虛擬機器的主要 DNS 尾碼設定。這可讓虛擬機器即使沒有明確的尾碼規格，也能自動解析 Hbase 叢集的主機名稱。例如，*workernode0* 的主機名稱會被正確地解析為 HBase 叢集的 workernode0。
    若要變更設定：

    1.  使用 RDP 進入虛擬機器。
    2.  開啟 [本機群組原則編輯器]。可執行檔為 gpedit.msc。
    3.  依序展開 [電腦設定]、[系統管理範本] 及 [網路]，然後按一下 [DNS 用戶端]。
    4.  將 [主要 DNS 尾碼] 設定為在步驟 2 取得的值：

        ![hdinsight.hbase.primary.dns.suffix][]

    1.  按一下 [確定]。
    2.  重新啟動虛擬機器。

    現在虛擬機器已準備好可以和 HBase 叢集通訊。若要測試連線，請從虛擬機器上執行 “ping headnode0”。

## <span id="nextsteps"></span></a>後續步驟

在本教學課程中，我們已了解如何佈建 HBase 叢集。若要深入了解，請參閱：

-   [開始使用 HDInsight][]
-   [在 HDInsight 中佈建 Hadoop 叢集 (英文)][]
-   [開始在 HDInsight 中搭配使用 HBase 與 Hadoop (英文)][開始在 HDInsight 中搭配使用 HBase 與 Hadoop]
-   [使用 HDInsight 中的 HBase 分析 Twitter 情緒 (英文)][]
-   [虛擬網路概觀][]。

  [必要條件]: #prerequisites
  [將 HBase 叢集佈建至虛擬網路]: #hbaseprovision
  [使用 HBase Java RPC API 連線到佈建在虛擬網路中的 HBase 叢集]: #connect
  [後續步驟]: #nextsteps
  [購買選項]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [成員優惠]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [免費試用]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [安裝並設定 Azure PowerShell]: ../install-configure-powershell
  [執行 Windows PowerShell 指令碼]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [Azure 管理入口網站]: https://manage.windowsazure.com
  []: http://msdn.microsoft.com/library/azure/jj156088.aspx
  [在 HDInsight 中搭配使用 Azure Blob 儲存體和 Hadoop]: ../hdinsight-use-blob-storage/
  [開始在 HDInsight 中搭配使用 HBase 與 Hadoop]: ../hdinsight-hbase-get-started/
  [建立執行 Windows Server 的虛擬機器]: ../virtual-machines-windows-tutorial/
  [使用 Azure 管理入口網站管理 HDInsight 中的 Hadoop 叢集]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [hdinsight.hbase.primary.dns.suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
  [開始使用 HDInsight]: ../hdinsight-get-started/
  [在 HDInsight 中佈建 Hadoop 叢集 (英文)]: ../hdinsight-provision-clusters/
  [使用 HDInsight 中的 HBase 分析 Twitter 情緒 (英文)]: ../hdinsight-hbase-twitter-sentiment/
  [虛擬網路概觀]: http://msdn.microsoft.com/library/azure/jj156007.aspx
