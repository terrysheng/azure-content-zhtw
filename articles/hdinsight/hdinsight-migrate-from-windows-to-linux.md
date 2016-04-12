<properties
pageTitle="從以 Windows 為基礎的 HDInsight 移轉至以 Linux 為基礎的 HDInsight | Azure"
description="了解如何從以 Windows 為基礎的 HDInsight 叢集移轉至以 Linux 為基礎的 HDInsight 叢集。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="03/28/2016"
ms.author="larryfr"/>

#從以 Windows 為基礎的 HDInsight 叢集移轉至以 Linux 為基礎的叢集

雖然以 Windows 為基礎的 HDInsight 能提供在雲端中輕鬆使用 Hadoop 的方法，您可能會發現您需要以 Linux 為基礎的叢集，以運用解決方案所需的工具和技術。Hadoop 生態系統的許多內容都是在以 Linux 為基礎的系統上開發的，因此部分內容可能無法在以 Windows 為基礎的 HDInsight 上使用。除此之外，許多針對 Hadoop 的書籍、影片及其他訓練材料，都會假設您正在使用 Linux 系統。

本文件提供 Windows 和 Linux 上 HDInsight 差異的詳細資料，以及如何將現有工作負載移轉至以 Linux 為基礎之叢集的指導方針。

## 移轉工作

下列為移轉的一般工作流程。

![移轉工作流程圖表](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1.  閱讀本文件的每個區段，來了解在將現有工作負載及工作等項目移轉至以 Linux 為基礎的叢集時，可能需要進行的變更。

2.  建立以 Linux 為基礎的叢集做為測試/品質保證環境。如需建立以 Linux 為基礎之叢集的詳細資訊，請參閱[在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-provision-linux-clusters.md)。

3.  將現有的工作、資料來源及接收複製到新的環境。請參閱＜將資料複製到測試環境＞一節以取得詳細資料。

4.  執行驗證測試以確保您的工作在新叢集上會如預期般運作。

當您已驗證一切都會如預期般運作之後，請為移轉排定停機時間。在停機期間，請執行下列動作。

1.  備份所有儲存在本機叢集節點上的暫時性資料。例如，如果您的資料是直接儲存在前端節點上。

2.  刪除以 Windows 為基礎的叢集。

3.  使用和以 Windows 為基礎之叢集所使用的相同預設資料存放區，建立以 Linux 為基礎的叢集。這將能允許新叢集針對現有的生產資料繼續運作。

4.  匯入任何已備份的暫時性資料。

5.  使用新叢集啟動工作/繼續處理。

### 將資料複製到測試環境

雖然複製資料和工作的方法有很多，但是本區段所討論的兩個方法，是將檔案直接移至測試叢集最簡單的方法。

#### HDFS DFS 複製

您可以透過下列步驟使用 Hadoop HDFS 命令，來直接將資料從現有生產叢集的儲存體複製到新測試叢集的儲存體。

1. 尋找現有叢集的儲存體帳戶和預設容器資訊。您可以使用下列 Azure PowerShell 指令碼來執行此動作。

        $clusterName="Your existing HDInsight cluster name"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
        write-host "Default container: $clusterInfo.DefaultStorageContainer"

2. 依照＜在 HDInsight 中建立以 Linux 為基礎的叢集＞文件中的步驟建立新的測試環境。於建立叢集之前停止遵循步驟，並改為選取 [選擇性組態]。

3. 從 [選擇性組態] 刀鋒視窗中，選取 [連結的儲存體帳戶]。

4. 選取 [新增儲存體金鑰]，並在出現提示時，選取步驟 1 中由 PowerShell 指令碼傳回的儲存體帳戶。在每個刀鋒視窗上按一下 [選取] 來關閉它們。最後，建立叢集。

5. 建立叢集之後，使用 **SSH** 來連線至該叢集。 如果您不熟悉搭配 HDInsight 使用 SSH 的方式，請參閱下列文章。

    * [從 Windows 用戶端搭配使用 SSH 與以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [從 Linux、Unix 及 Mac 用戶端搭配使用 SSH 與以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

6. 從 SSH 工作階段中，使用下列命令來將檔案從已連結的儲存體帳戶複製到新的預設儲存體帳戶。將 CONTAINER 和 ACCOUNT 取代為步驟 1 中由 PowerShell 指令碼傳回的容器和帳戶資訊。將資料路徑取代為資料檔案路徑。

        hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location

    [AZURE.NOTE] 如果包含資料的目錄結構並不存在於測試環境上，您可以使用下列命令建立它。

        hdfs dfs -mkdir -p /new/path/to/create

    `-p` 參數可讓您在路徑中建立所有目錄。

#### 在 Azure 儲存體 Blob 之間進行直接複製

此外，您也可能會想要使用 `Start-AzureStorageBlobCopy` Azure PowerShell Cmdlet 在 HDInsight 之外的儲存體帳戶之間複製 Blob。如需詳細資訊，請參閱＜搭配使用 Azure PowerShell 與 Azure 儲存體＞一文中的＜如何管理 Azure Blob＞一節。

##用戶端技術

通常來說，用戶端技術 (例如 [Azure PowerShell Cmdlet](../powershell-install-configure.md)、[Azure CLI](../xplat-cli-install.md) 或是 [Hadoop 的 .NET SDK](https://hadoopsdk.codeplex.com/)) 在以 Linux 為基礎的叢集上都會以相同的方式運作，因為它們皆依賴在這兩個叢集作業系統類型上都相同的 REST API。

##伺服器端技術

下列表格提供移轉 Windows 特定之伺服器端元件的指導方針。

| 如果您正在使用這項技術... | 請執行此動作... |
| ----- | ----- |
| **PowerShell** (伺服器端指令碼，包含於叢集建立期間使用的指令碼動作) | 重寫為 Bash 指令碼。針對指令碼動作，請參閱[使用指令碼動作自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)和[以 Linux 為基礎之 HDInsight 的指令碼動作開發](hdinsight-hadoop-script-actions-linux.md)。 |
| **Azure CLI** (伺服器端指令碼) | 雖然 Azure CLI 可在 Linux 上使用，它並沒有預先安裝在 HDInsight 叢集前端節點上。如果您需要針對伺服器端指令碼使用它，請參閱[安裝 Azure CLI](../xplat-cli-install.md) 來取得在以 Linux 為基礎的平台上進行安裝的資訊。 |
| **.NET 元件** | .NET 目前並不受以 Linux 為基礎的 HDInsight 叢集支援，但此功能會在未來透過更新新增。如果您需要立即進行移轉，您必須以 Java 或 Python 重寫您的元件。 |
| **Win32 元件或其他僅限 Windows 的技術** | 指導方針將視元件或技術而有所不同。您可能可以找到與 Linux 相容的版本，也可能會需要尋找替代的解決方案，或是重寫此元件。 |

##叢集建立

本節將提供叢集建立之差異的資訊。

### SSH 使用者

以 Linux 為基礎的 HDInsight 使用**安全殼層 (SSH)** 通訊協定來為叢集節點提供遠端存取功能。大部分的 SSH 用戶端和以 Windows 為基礎之叢集的遠端桌面不同，它們並不會提供圖形化的使用者經驗，而是提供允許您在叢集上執行命令的命令列。某些用戶端 (例如 [MobaXterm](http://mobaxterm.mobatek.net/)) 除了提供遠端命令列之外，也提供圖形化的檔案系統瀏覽器。

在叢集建立期間，您必須提供一個 SSH 使用者，以及一個**密碼**或**公開金鑰憑證**以進行驗證。

我們建議使用公開金鑰憑證，因為它比密碼更安全。憑證驗證會產生已簽署的公開/私人金鑰組，然後在建立叢集時提供公開金鑰。使用 SSH 連線至伺服器時，用戶端上的私人金鑰將會為連線提供驗證。

如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱：

- [從 Windows 用戶端搭配使用 SSH 與 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md)

- [從 Linux、Unix 及 OS X 用戶端搭配使用 SSH 與 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

### 叢集自訂

搭配以 Linux 為基礎之叢集使用的**指令碼動作**必須以 Bash 指令碼撰寫。雖然指令碼動作可在叢集建立期間使用，它們也可以用來在以 Linux 為基礎之叢集已啟動並開始執行之後進行自訂。如需詳細資訊，請參閱[使用指令碼動作自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)和[以 Linux 為基礎之 HDInsight 的指令碼動作開發](hdinsight-hadoop-script-actions-linux.md)。

另一個自訂功能是 **bootstrap**。針對 Windows 叢集，這可讓您指定其他搭配 Hive 使用之程式庫的位置。在叢集建立之後，這些程式庫將自動可搭配 Hive 查詢使用，而不需使用 `ADD JAR`。

針對以 Linux 為基礎的叢集，bootstrap 並不提供此功能。請改為使用[在叢集建立期間新增 Hive 程式庫](hdinsight-hadoop-add-hive-libraries.md)中所記錄的指令碼動作。

### 虛擬網路

以 Windows 為基礎的 HDInsight 僅支援傳統虛擬網路，而以 Linux 為基礎的 HDInsight 則需要資源管理員虛擬網路。如果您在傳統虛擬網路中有著以 Linux 為基礎的 HDInsight 叢集必須連接的資源，請參閱[將傳統虛擬網路連接到資源管理員虛擬網路](../virtual-network/virtual-networks-arm-asm-s2s.md)。

如需搭配 HDInsight 使用 Azure 虛擬網路之設定需求的詳細資訊，請參閱[使用虛擬網路延伸 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md)。

##管理與監視

有許多您可能曾搭配以 Windows 為基礎之 HDInsight 使用的 Web UI (例如工作歷程記錄或 Yarn UI)，皆可透過 Ambari 使用。此外，Ambari Hive 檢視能提供使用您的網頁瀏覽器執行 Hive 查詢的方法。以 Linux 為基礎的叢集可透過下列位置使用 Ambari Web UI：https://CLUSTERNAME.azurehdinsight.net。

如需使用 Ambari 的詳細資訊，請參閱下列文件：

- [Ambari Web](hdinsight-hadoop-manage-ambari.md)

- [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

### Ambari 警示

Ambari 擁有能通知您叢集潛在問題的警示系統。警示將會以紅色或黃色的項目出現在 Ambari Web UI 中，您也可以透過 REST API 擷取它們。

> [AZURE.IMPORTANT] Ambari 警示表示的是*可能的*問題，而不是*實際的*問題。例如，您可能會收到無法存取 HiveServer2 的警示，但實際上您仍然可以正常存取它。
>
> 許多警示都是針對某項服務實作為以間隔為基礎的查詢，並會預期在特定的時間範圍內收到回應。因此警示本身並不代表服務已關閉，而只是單純表示該服務沒有在預期的時間範圍內傳回結果。

通常來說，您應該先評估某個警示是否已長時間持續發生，或者是否與使用者先前針對叢集所回報的某個問題有關聯，再對它採取動作。

##檔案系統位置

Linux 叢集檔案系統的展開方式和以 Windows 為基礎的 HDInsight 叢集不同。使用下列表格來尋找常用的檔案。

| 我需要尋找... | 它位於... |
| ----- | ----- |
| 組態 | `/etc`。例如，`/etc/hadoop/conf/core-site.xml` |
| 記錄檔 | `/var/logs` |
| Hortonworks Data Platform (HDP) | `/usr/hdp`。有兩個目錄位於這裡，一個是目前的 HDP 版本 (例如 `2.2.9.1-1`)，另一個則是 `current`。`current` 目錄內含位於版本號碼目錄中之檔案和目錄的符號連結，可做為方便存取 HDP 檔案的方法，因為版本號碼會隨著 HDP 版本更新而變更。 |
| hadoop-streaming.jar | `/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

通常來說，如果您知道檔案的名稱，便可以使用下列來自 SSH 工作階段的命令來尋找檔案路徑：

    find / -name FILENAME 2>/dev/null

您也可以搭配檔案名稱使用萬用字元。例如，`find / -name *streaming*.jar 2>/dev/null` 將會傳回任何檔案名稱包含「streaming」之 jar 檔案的路徑。

##Hive、Pig 及 MapReduce

以 Linux 為基礎之叢集上的 Pig 和 MapReduce 工作負載和以 Windows 為基礎的版本非常相似，主要的差異在於，如果您是使用遠端桌面來連線至以 Windows 為基礎的叢集並執行工作，您必須針對以 Linux 為基礎的叢集使用 SSH。

- [搭配 SSH 使用 Pig](hdinsight-hadoop-use-pig-ssh.md)

- [搭配 SSH 使用 MapReduce](hdinsight-hadoop-use-mapreduce-ssh.md)

### Hive

下列圖表提供移轉 Hive 工作負載的指導方針。

| 以 Windows 為基礎時，我是使用... | 以 Linux 為基礎時... |
| ----- | ----- |
| **Hive 編輯器** | [Ambari 中的 Hive 檢視](hdinsight-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` 以啟用 Tez | Tez 是以 Linux 為基礎之叢集的預設執行引擎，因此已不再需要 SET 陳述式。 |
| 伺服器上的 CMD 檔案或指令碼是做為 Hive 工作的一部分進行叫用 | 使用 Bash 指令碼 |
| 來自遠端桌面的 `hive` 命令 | 使用 [Beeline](hdinsight-hadoop-use-hive-beeline.md) 或是[來自 SSH 工作階段的 Hive](hdinsight-hadoop-use-hive-ssh.md) |

##Storm

| 以 Windows 為基礎時，我是使用... | 以 Linux 為基礎時... |
| ----- | ----- |
| Storm Dashboard | 無法使用 Storm Dashboard。請參閱[在以 Linux 為基礎的 HDInsight 上部署與管理 Storm 拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)以了解提交拓撲的方法。 |
| Storm UI | Storm UI 可在下列位置取得：https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio 以建立、部署及管理 C# 或混合式拓撲 | 以 Linux 為基礎的叢集目前並不支援 .NET 拓撲，但會在未來透過更新新增此支援。在此之前，如果您需要進行移轉，便必須以 Java 重新實作您的拓撲。請參閱[開發以 Java 為基礎的拓撲](hdinsight-storm-develop-java-topology.md)來取得建立以 Java 為基礎之拓撲的詳細資訊。 |

##HBase

在以 Linux 為基礎的叢集上，HBase 的 znode 父項目為 `/hbase-unsecure`。您必須針對任何使用原生 HBase Java API 的 Java 用戶端應用程式，在其組態中做出此設定。

請參閱[建置以 Java 為基礎的 HBase 應用程式](hdinsight-hbase-build-java-maven.md)以取得設定此值的範例用戶端。

##Spark

Spark 叢集之前可在預覽期間於 Windows 叢集上使用，不過發行後的 Spark 只能在以 Linux 為基礎的叢集上使用。以 Windows 為基礎的 Spark 預覽叢集和以 Linux 為基礎的 Spark 叢集之間並沒有移轉路徑。

##已知問題

### Azure Data Factory 自訂 .NET 活動

Azure Data Factory 自訂 .NET 活動目前並不受以 Linux 為基礎的 HDInsight 叢集所支援。您應該改為使用下列其中一個方法，來將自訂活動實作為 ADF 管線的一部分。

-   在 Azure Batch 集區上執行 .NET 活動。請參閱[在 Azure 資料處理站管線中使用自訂活動](../data-factory/data-factory-use-custom-activities.md/#AzureBatch)的＜使用 Azure Batch 連結服務＞一節。

-   將活動實作為 MapReduce 活動。請參閱[從 Data Factory 叫用 MapReduce 程式](../data-factory/data-factory-map-reduce.md)以取得詳細資訊。

### 行尾結束符號

通常來說，以 Windows 為基礎之系統上的行尾結束符號是使用 CRLF，而以 Linux 為基礎的系統則使用 LF。如果您產生或預期擁有 CRLF 行尾結束符號的資料，便可能需要修改產生者或取用者來搭配 LF 行尾結束符號運作。

例如，使用 Azure PowerShell 來在以 Windows 為基礎的叢集上查詢 HDInsight，將會傳回 CRLF 的資料。在以 Linux 為基礎的叢集上使用相同的查詢，將會傳回 LF。在許多情況下，這對資料取用者來說並不重要，但是在移轉至以 Linux 為基礎的叢集之前，仍然應該予以調查。

如果您擁有會直接在 Linux 叢集節點上執行的指令碼 (例如搭配 Hive 使用的 Python 指令碼，或是 MapReduce 工作)，您應該總是使用 LF 做為行尾結束符號。如果您使用 CRLF，便可能會在以 Linux 為基礎的叢集上執行指令碼時遭遇到錯誤。

如果您知道指令碼並沒有包含擁有內嵌 CR 字元的字串，您可以使用下列其中一種方法來大量變更行尾結束符號：

-   **如果您擁有計畫上傳至叢集的指令碼**，請在將指令碼上傳至叢集之前，使用下列 PowerShell 陳述式將行尾結束符號從 CRLF 變更為 LF。

        $original_file ='c:\path\to\script.py'
        $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
        [IO.File]::WriteAllText($original_file, $text)

-   **如果您擁有已經位於叢集所使用之儲存體中的指令碼**，您可以從 SSH 工作階段對以 Linux 為基礎的叢集使用下列命令來修改指令碼。

        hdfs dfs -get wasb:///path/to/script.py oldscript.py
        tr -d '\r' < oldscript.py > script.py
        hdfs dfs -put -f script.py wasb:///path/to/script.py

##後續步驟

-   [了解如何建立以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)

-   [從 Windows 用戶端使用 SSH 連線至以 Linux 為基礎的叢集](hdinsight-hadoop-linux-use-ssh-windows.md)

-   [從 Linux、Unix，或 Mac 用戶端使用 SSH 連線至以 Linux 為基礎的叢集](hdinsight-hadoop-linux-use-ssh-unix.md)

-   [使用 Ambari 管理以 Linux 為基礎的叢集](hdinsight-hadoop-manage-ambari.md)

<!---HONumber=AcomDC_0330_2016-->