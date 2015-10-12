<properties
   pageTitle="部署和管理 HDInsight 上的 Apache Storm 拓撲 | Microsoft Azure"
   description="使用 Storm Dashboard on HDInsight，了解如何部署、監視和管理 Apache Storm 拓撲。使用適用於 Visual Studio 的 Hadoop 工具。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/14/2015"
   ms.author="larryfr"/>

# 部署和管理以 Linux 為基礎的 HDInsight 上的 Apache Storm 拓撲

在本文件中，了解在 HDInsight 叢集上管理和監視於以 Linux 為基礎的 Storm 上執行的 Storm 拓撲的基本概念。

> [AZURE.IMPORTANT]本文中的步驟需要 HDInsight 叢集上以 Linux 為基礎的 Storm。如需部署和監視以 Windows 為基礎的 HDInsight 上的拓撲的詳細資訊，請參閱[部署和管理以 Windows 為基礎的 HDInsight 上的Apache Storm 拓撲](hdinsight-storm-deploy-monitor-topology.md)

## 必要條件

- **HDInsight 叢集上以 Linux 為基礎的 Storm**：請參閱[開始使用 Apache Storm on HDInsight](hdinsight-storm-get-started-linux.md) 以取得建立叢集的步驟

- **了解 SSH 和 SCP**：如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：
    - **Linux、Unix 或 OS X 用戶端**：請參閱[從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
    - **Windows 用戶端**：請參閱[從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

- **SCP 用戶端**：與所有 Linux、Unix 和 OS X 系統一起提供。對於 Windows 用戶端，我們建議 PSCP，可從 [PuTTY 下載頁面](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)取得。

## 啟動 Storm 拓撲

1. 使用 SSH 連接到 HDInsight 叢集。將 **USERNAME** 取代為您的 SSH 登入名稱。將 **CLUSTERNAME** 取代為 HDInsight 叢集名稱：

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    如需有關使用 SSH 連線至 HDInsight 叢集的詳細資訊，請參閱下列文件：
    
        - **Linux, Unix or OS X clients**: See [Use SSH with Linux-based Hadoop on HDInsight from Linux, OS X or Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
        
        - **Windows clients**: See [Use SSH with Linux-based Hadoop on HDInsight from Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 使用下列命令以啟動範例拓撲：

        storm jar storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    這會在叢集上啟動範例 WordCount 拓撲。它會隨機產生句子並計算句子中每個字詞的出現次數。

    > [AZURE.NOTE]將拓撲提交至叢集時，您必須先複製包含叢集的 jar 檔案，再使用 `storm` 命令。可以從檔案所在的用戶端使用 `scp` 命令來完成這個動作。例如，`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > WordCount 範例和其他 Storm 入門範例已經包含在叢集上，位置是 `/usr/hdp/current/storm-client/contrib/storm-starter/`。

##使用 Storm 命令監視和管理

`storm` 公用程式可讓您從命令列使用執行中拓撲。以下是常用命令的清單。使用 `storm -h` 以取得完整的命令清單。

###列出拓撲

使用下列命令來列出所有執行中拓撲：

    storm list
    
此命令傳回的資訊類似如下：

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

###停用和重新啟動

停用拓撲會暫停它，直到刪除或重新啟動。使用下列項目以停用和重新啟動：

    storm Deactivate TOPOLOGYNAME
    
    storm Activate TOPOLOGYNAME

###刪除執行中拓撲

Storm 拓撲一旦啟動之後，將會繼續執行直到停止。若要停止拓撲，請使用下列命令：

    storm stop TOPOLOGYNAME

###重新平衡

重新平衡拓撲可以讓系統修訂拓撲的平行處理原則。例如，如果您已調整叢集的大小以新增更多節點，重新平衡可讓執行中的拓撲使用新的節點。

> [AZURE.WARNING]重新平衡拓撲首先會停用拓撲，然後跨叢集平均重新分佈背景工作角色，最後讓拓撲返回發生重新平衡之前的狀態。因此如果拓撲是作用中，它會再次變成作用中。如果已停用，它將保持停用。

    storm rebalance TOPOLOGYNAME

##使用 Storm UI 監視和管理

Storm UI 提供 Web 介面以使用執行中拓撲，而且包含在您的 HDInsight 叢集。

> [AZURE.IMPORTANT]Storm UI 不是透過網際網路公開可用，而是必須使用 HDInsight 叢集前端節點的 SSH 通道來存取。如需建立及使用 SSH 通道的詳細資訊，請參閱[使用 SSH 通道來存取 Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie 及其他 Web UI](hdinsight-linux-ambari-ssh-tunnel.md)。

使用下列步驟來檢視 Storm UI：

1. 對您的 HDInsight 叢集的 Ambari Web 開啟網頁瀏覽器。Ambari Web 的 URL 是 https://CLUSTERNAME.azurehdinsight.net，其中 __CLUSTERNAME__ 是您的叢集的名稱。

2. 從頁面左邊的服務清單中選取 [Storm]。然後從 [快速連結] 選取 [Storm UI]。

    ![快速連結中的 Storm UI 項目](./media/hdinsight-storm-deploy-monitor-topology-linux/ambari-storm.png)

    這會顯示 Storm UI︰

    ![Storm UI](./media/hdinsight-storm-deploy-monitor-topology-linux/storm-ui.png)

> [AZURE.NOTE]使用 Storm UI 時，您可能會發現某些版本的 Internet Explorer 在您第一次造訪之後無法正確重新整理 UI。例如，可能不會顯示您提交的新拓撲，或是可能將先前已停用的拓撲顯示為使用中。Microsoft 知道這個問題，並正在找出解決方案。

### 主頁面

Storm UI 的主頁面會提供下列資訊：- **叢集摘要**：Storm 叢集的基本資訊。- **拓撲摘要**：執行中拓撲的清單。使用本節中的連結來檢視特定拓撲的詳細資訊。- **監督員摘要**：Storm 監督員的相關資訊。- **Nimbus 組態**：叢集的 Nimbus 組態。

### 拓撲摘要

選取 [拓撲摘要] 區段中的連結會顯示拓撲的下列資訊：- **拓撲摘要**：拓撲的基本資訊。- **拓撲動作**：您可以對拓撲執行的管理動作。- **啟動**：繼續處理已停用的拓撲。- **停用**：暫停執行中拓撲。- **重新平衡**：調整拓撲的平行處理原則。變更叢集中的節點數目之後，您應該重新平衡執行中拓撲。這可讓拓撲調整平行處理原則，以彌補叢集中增加或減少的節點數目。

      For more information, see <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Understanding the parallelism of a Storm topology</a>.

  - **終止**：在指定的逾時之後結束 Storm 拓撲。

- **拓撲統計資料**：拓撲的統計資料。使用 [視窗] 資料行中的連結，以設定頁面上其餘項目的時間範圍。
- **Spout**：拓撲所使用的 Spout。使用本節中的連結檢視特定 Spout 的詳細資訊。
- **Bolt**：拓撲所使用的 Bolt。使用本節中的連結檢視特定 Bolt 的詳細資訊。
- **拓撲組態**：所選取拓撲的組態。

### Spout 和 Bolt 摘要

從 [Spout] 或 [Bolt] 區段中選取 Spout 會顯示所選取項目的下列資訊：- **元件摘要**：Spout 或 Bolt 的基本資訊。- **Spout/Bolt 統計資料**：Spout 或 Bolt 的統計資料。使用 [時段] 資料行中的連結以設定頁面上其他項目的時間範圍。- **輸入統計資料** (只有 Bolt)：Bolt 所耗用的輸入串流的相關資訊。- **輸出統計資料**：此 Spout 或 Bolt 所發出的串流的相關資訊。- **執行程式**：Spout 或 Bolt 的執行個體的相關資訊。選取特定執行程式的 [連接埠] 項目，以檢視針對此執行個體所產生之診斷資訊的記錄。- **錯誤**：此 Spout 或 Bolt 的任何錯誤資訊。

## REST API

Storm UI 是以 REST API 為建置基礎，因此您可以使用 REST API 執行類似的管理和監視功能。您可以使用 REST API 建立自訂工具來管理和監視 Storm 拓撲。

如需詳細資訊，請參閱 <a href="https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md" target="_base">Storm UI REST API</a>下列資訊專用於搭配使用 REST API 與 Apache Storm on HDInsight。

> [AZURE.IMPORTANT]Storm REST API 不是透過網際網路公開可用，而是必須使用 HDInsight 叢集前端節點的 SSH 通道來存取。如需建立及使用 SSH 通道的詳細資訊，請參閱[使用 SSH 通道來存取 Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie 及其他 Web UI](hdinsight-linux-ambari-ssh-tunnel.md)。

### 基底 URI

以 Linux 為基礎的 HDInsight 叢集上的 REST API 的基底 URI 是 **https://headnode0:8744/api/v1/</a>**。

### 驗證

REST API 的要求必須使用**基本驗證**，因此請使用 HDInsight 叢集管理員名稱和密碼。

> [AZURE.NOTE]因為使用純文字傳送基本驗證，所以您應該**一律**使用 HTTPS 來保護與叢集通訊的安全。

### 傳回值

從 REST API 傳回的資訊只能從叢集或與叢集相同之 Azure 虛擬網路上的虛擬機器內使用。例如，無法從網際網路存取針對 Zookeeper 伺服器所傳回的完整網域名稱 (FQDN)。

## 後續步驟

現在，您已經了解如何使用 Storm 儀表板來部署和監視拓撲、了解如何[使用 Maven 來開發以 Java 為基礎的拓撲](hdinsight-storm-develop-java-topology.md)。

若需更多範例拓撲的清單，請參閱 [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)。

<!---HONumber=Oct15_HO1-->