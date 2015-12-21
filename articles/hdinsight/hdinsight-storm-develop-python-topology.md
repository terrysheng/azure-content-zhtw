<properties
   pageTitle="在 HDinsight 上於 Storm 拓撲中使用 Python 元件 | Microsoft Azure"
   description="了解如何在 Azure HDInsight 上從 Apache Storm 中Python 元件。您將了解如何從 Java 和 Clojure 型 Storm 拓撲中使用 Python 元件。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>

#在 HDInsight 上使用 Python 開發 Apache Storm 拓撲

Apache Storm 支援多種語言，甚至可讓您將數種語言的元件結合成一個拓撲。本文件中，您將了解如何在 HDInsight 上於 Java 和 Clojure 型 Storm 拓撲中使用 Python 元件。

##必要條件

* Python 2.7 或更新版本

* Java JDK 1.7 或更新版本

##Storm 多語言支援

Storm 設計為可以與使用任何程式設計語言撰寫的元件一起使用，但元件必須了解如何使用 [Storm 的 Thrift 定義](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift)。在 Python 中，Apache Storm 專案隨附一個模組，可讓您輕鬆地與 Strom 互動。您可以在 [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py) 找到此模組。

由於 Apache Storm 是在 Java 虛擬機器 (JVM) 上執行的 Java 程序，以其他語言撰寫的元件會以子程序方式執行。JVM 中執行的 Storm 程式碼會使用透過 stdin/stdout 傳送的 JSON 訊息，與這些子程序進行通訊。如需各元件之間通訊的詳細資訊，請參閱[多語言通訊協定](https://storm.apache.org/documentation/Multilang-protocol.html)文件。

###Storm 模組

Storm 模組 (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py,) 提供必要的程式碼，可建立與 Storm 一起使用的 Python 元件。

例如，其中有 `storm.emit` 可發出 Tuple、`storm.logInfo` 可寫入記錄檔。建議您詳閱這個檔案，了解它所提供的內容。

##挑戰

您可以使用 __storm.py__ 模組建立 Python spout 來取用資料、建立 bolt 來處理資料，但是，負責設置元件之間通訊的整個 Storm 拓撲定義，仍是使用 Java 或 Clojure 撰寫。此外，如果您使用 Java，您也必須建立 Java 元件做為 Python 元件的介面。

另外，因為 Storm 叢集是以分散式方式執行，您必須確定任何 Python 元件所需的任何模組，可供叢集的所有背景工作角色節點使用。對於多語言的資源，Storm 沒有辦法輕鬆完成此工作，您必須將所有相依性納入拓撲的 jar 檔案中，或在叢集的每一個背景工作角色節點上手動安裝相依性。

有一些專案試圖克服這些缺點，例如 [Pyleus](https://github.com/Yelp/pyleus) 和 [Streamparse](https://github.com/Parsely/streamparse)。雖然這兩項都可以在以 Linux 為基礎的 HDInsight 叢集上執行，但並不是本文的主要焦點，因為它們在叢集安裝期間需要自訂，而且沒有在 HDInsight 叢集經過完整測試。本文件結尾提供搭配 HDInsight 使用這些架構時的注意事項。

###Java 與Clojure 拓撲定義的比較

在兩種定義拓撲的方法，Clojure 是目前為止最簡單/最乾淨的方法，因為您可以在拓樸定義中直接參考 python 元件。對於 Java 型拓樸定義，您也必須定義 Java 元件來處理一些工作，例如在 Python 元件傳回的 Tuple 中宣告欄位。

本文件說明這兩種方法，並附上範例專案。

##使用 Java 拓撲的 Python 元件

> [AZURE.NOTE]此範例位於 https://github.com/Blackmist/hdinsight-python-storm-wordcount 的 __JavaTopology__ 目錄中。這是一個 Maven 型專案。如果您不熟悉 Maven，請參閱 [在 HDInsight 上使用 Apache Storm 開發以 Java 為基礎的拓撲](hdinsight-storm-develop-java-topology.md)，以取得有關為 Storm 拓撲建立 Maven 專案的詳細資訊。

使用 Python (或其他 JVM 語言元件) 的 Java 型拓樸乍看之下是使用 Java 元件，但如果查看每個 Java spout/bolt，您會看到類似下列的程式碼：

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Java 在這裡叫用 Python，並執行含有實際 blot 邏輯的指令碼。Java spout/bolt (針對此範例) 只是在基礎 Python 元件將發出的 Tuple 中宣告欄位。

在此範例中，實際 Python 檔案儲存在 `/multilang/resources` 目錄中。__pom.xml__ 中會參考 `/multilang` 目錄：

<resources> <resource> <!-- Where the Python bits are kept --> <directory>${basedir}/multilang</directory> </resource> </resources>

這會將 `/multilang` 資料夾中的所有檔案，併入到從這個專案建置的 jar 中。

> [AZURE.IMPORTANT]請注意，這僅指定 `/multilang` 目錄，而不是 `/multilang/resources`。Storm 預期非 JVM 資源都在 `resources` 目錄中，因此在內部已尋找過它。將元件放在此資料夾中，可讓您在 Java 程式碼中直接依名稱參考。例如，`super("python", "countbolt.py");`。另一種看法是 Storm 存取多語言的資源時會將 `resources` 目錄視為根目錄 (/)。
>
> 針對此範例專案，`storm.py` 模組位於 `/multilang/resources` 目錄中。

###建置並執行專案

若要在本機執行此專案，只要使用下列 Maven 命令建置，並以本機模式執行即可：

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

使用 ctrl+c 可結束程序。

若要將專案部署至執行 Apache Storm 的 HDInsight 叢集，請使用下列步驟：

1. 建置 uber jar：

        mvn package

    這樣會此專案的 `/target` 目錄中建立名為__WordCount--1.0-SNAPSHOT.jar__ 的檔案。

2. 使用下列其中一種方法，將 jar 檔案上傳至 Hadoop 叢集：

    * __以 Linux 為基礎的__ HDInsight 叢集：使用 `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` 將 jar 檔案複製到叢集，並以您的 SSH 使用者名稱取代 USERNAME，以 HDInsight 叢集名稱取代 CLUSTERNAME。

        檔案上傳完成之後，使用 SSH 連接到叢集，然後使用 `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount` 啟動拓撲。

    * __以 Windows 為基礎的__ HDInsight 叢集：在瀏覽器中移至 HTTPS://CLUSTERNAME.azurehdinsight.net/，以連接到 Storm 儀表板。以您的 HDInsight 叢集名稱取代 CLUSTERNAME，在出現提示時，提供系統管理員名稱和密碼。

        使用表單執行下列動作：

        * __Jar 檔案__：選取 [瀏覽]，然後選取 __WordCount-1.0-SNAPSHOT.jar__ 檔案
        * __類別名稱__：輸入 `com.microsoft.example.WordCount`
        * __其他參數__：輸入易記名稱來識別拓撲，例如 `wordcount`

        最後，選取 [提交] 啟動拓撲。

> [AZURE.NOTE]Storm 拓撲啟動之後會一直執行到停止 (刪除) 為止。 若要停止拓撲，請從命令列 (例如，Linux 叢集的 SSH 工作階段) 使用 `storm kill TOPOLOGYNAME` 命令，或使用 Storm UI 選取拓撲，然後選取 [刪除] 按鈕。

##使用 Clojure 拓撲的 Python 元件

> [AZURE.NOTE]此範例位於 https://github.com/Blackmist/hdinsight-python-storm-wordcount 的 __ClojureTopology__ 目錄中。

此拓撲由 [Leiningen](http://leiningen.org) 建立，用於[建立新的 Clojure 專案](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project)。之後，已建立結構的專案經過下列修改：

* __project.clj__：加入 Storm 的相依性，並排除部署至 HDInsight 伺服器時可能造成問題的項目。
* __resources/resources__：Leiningen 建立預設的 `resources` 目錄，不過，這裡儲存的檔案似乎會加入至根據這個專案所建立的 jar 檔案的根目錄，但 Storm 需要這些檔案位於名為 `resources` 的子目錄中。因此加入一個子目錄，Python 檔案就儲存在 `resources/resources` 中。在執行階段，這當成存取 Python 元件時的根目錄 (/)。
* __src/wordcount/core.clj__：此檔案包含拓撲定義，而且由 __project.clj__ 檔案參考。如需有關使用 Clojure 定義 Storm 拓撲的詳細資訊，請參閱 [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html)。

###建置並執行專案

__如果要在本機建置並執行專案__，請使用下列命令：

    lein do clean, run

若要停止拓撲，請使用 __Ctrl+C__。

__如果要建置 Uberjar 並部署至 HDInsight__，請使用下列步驟：

1. 建立包含拓撲和必要相依性的 uberjar：

        lein uberjar

    這會建立在 `target\uberjar+uberjar` 目錄中建立名為 `wordcount-1.0-SNAPSHOT.jar` 的新檔案。
    
2. 使用下列其中一種方法，將拓撲部署至 HDInsight 叢集來執行：

    * __以 Linux 為基礎的 HDInsight__
    
        1. 使用 `scp` 將檔案複製到 HDInsight 叢集前端節點。例如：
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            以您叢集的 SSH 使用者取代 USERNAME，並以您的 HDInsight 叢集名稱取代 CLUSTERNAME。
            
        2. 將檔案複製到叢集之後，使用 SSH 連接到叢集並提交工作。如需有關搭配使用 SSH 與 HDInsight 的資訊，請參閱下列其中一份文件：
        
            * [從 Linux、Unix 或 OS X 搭配使用 SSH 與以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [從 Windows 搭配使用 SSH 與以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. 連線之後，使用下列命令啟動拓撲：
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __以 Windows 為基礎的 HDInsight__
    
        1. 在瀏覽器中移至 HTTPS://CLUSTERNAME.azurehdinsight.net/，以連接到 Storm 儀表板。以您的 HDInsight 叢集名稱取代 CLUSTERNAME，在出現提示時，提供系統管理員名稱和密碼。

        2. 使用表單執行下列動作：

            * __Jar 檔案__：選取 [瀏覽]，然後選取 __wordcount-1.0-SNAPSHOT.jar__ 檔案
            * __類別名稱__：輸入 `wordcount.core`
            * __其他參數__：輸入易記名稱來識別拓撲，例如 `wordcount`

            最後，選取 [提交] 啟動拓撲。

> [AZURE.NOTE]Storm 拓撲啟動之後會一直執行到停止 (刪除) 為止。 若要停止拓撲，請從命令列 (Linux 叢集的 SSH 工作階段) 使用 `storm kill TOPOLOGYNAME` 命令，或使用 Storm UI 選取拓撲，然後選取 [刪除] 按鈕。

##Pyleus 架構

[Pyleus](https://github.com/Yelp/pyleus) 是可讓您提供下列項目以輕鬆搭配使用 Python 與 Storm 的一套架構：

* __以 YAML 為基礎的拓撲定義__：不需要了解 Java 或 Clojure 就能輕鬆定義拓撲
* __以 MessagePack 為基礎的序列化程式__：使用 MessagePack 做為預設序列化，而不是 JSON。這可以加速元件之間的傳訊
* __相依性管理__：使用 Virtualenv 確保 Python 相依性部署至所有背景工作角色節點。這需要在背景工作角色節點上安裝 Virtualenv

> [AZURE.IMPORTANT]Pyleus 要求您的開發環境上必須有 Storm。使用基本 Apache Storm 0.9.3 散發可能會導致 jar 與 HDInsight 隨附的 Storm 版本不相容。因此，下列步驟使用 HDInsight 做為開發環境。

使用 HDInsight 前端節點做為建置環境，就可以順利建置範例 Pyleus 拓撲。

1. 在 HDInsight 叢集上佈建新的 Storm 時，您必須確定 Python Virtualenv 存在於叢集節點上。建立以 Linux 為基礎的新 HDInsight 叢集時，請使用下列指令碼動作設定執行[叢集自訂](hdinsight-hadoop-customize-cluster.md)：

    * __名稱__：在這裡提供易記名稱
    * \_\_ 指令碼 URI\_\_：使用 `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh` 做為值。此指令碼會在節點上安裝 Python Virtualenv。
    
        > [AZURE.NOTE]它也會建立一些目錄，供本文件稍後的 Streamparse 架構使用。
        
    * __Nimbus__：勾選此項目將指令碼套用到 Nimbus (前端) 節點。
    * __監督員__：勾選此項目將指令碼套用到監督員 (背景工作角色) 節點
    
    將其他項目保留空白。

1. 建立叢集之後，使用 SSH 連線：

    * [從 Linux、Unix 或 OS X 搭配使用 SSH 與以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [從 Windows 搭配使用 SSH 與以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 在 SSH 連線上，使用下列命令建立新的虛擬環境並安裝 Pyleus：

        virtualenv pyleus_venv
        source pyleus_venv
        pip install pyleus

3. 接著，下載 Pyleus git 儲存機制並建置 WordCount 範例：

        sudo apt-get install git
        git clone https://github.com/Yelp/pyleus.git
        pyleus build pyleus/examples/word_count/pyleus_topology.yaml
    
    建置完成後，目前的目錄中會出現名為 `word_count.jar` 的新檔案。
    
4. 若要將拓撲提交至 Storm 叢集，請使用下列命令：

        pyleus submit -n localhost word_count.jar
    
    `-n` 參數指定 Nimbus 主機。由於是在前端節點上，我們可以使用 `localhost`。
    
    您也可以使用 `pyleus` 命令執行其他 Storm 動作。使用下列命令列出執行中的拓撲，然後刪除 `word_count` 拓撲：
    
        pyleus list -n localhost
        pyleus kill -n localhost word_count

##Streamparse 架構

[Streamparse](https://github.com/Parsely/streamparse) 是可讓您提供下列項目以輕鬆搭配使用 Python 與 Storm 的一套架構：

* __架構__：這可讓您輕鬆建立專案的架構，然後修改檔案以加入您的邏輯
* __Clojure DSL 函式__：這些可以降低在 Clojure 拓撲定義中使用 Python 元件時的繁複性。
* __相依性管理__：使用 Virtualenv 確保 Python 相依性部署至所有背景工作角色節點。這需要在背景工作角色節點上安裝 Virtualenv
* __遠端部署__：Streamparse 可以使用 SSH 自動化將元件部署至背景工作角色節點，而且可以建立 SSH 通道來與 Nimbus 進行通訊。讓您輕鬆地從開發環境部署至以 Linux 為基礎的叢集，例如 HDInsight

> [AZURE.IMPORTANT]Streamparse 依賴的元件需要有 [Unix 訊號](https://en.wikipedia.org/wiki/Unix_signal) (在 Windows 上沒有)。您的開發環境必須是 Linux、Unix 或 OS X，而且 HDInsight 叢集必須以 Linux 為基礎。

1. 在 HDInsight 叢集上佈建新的 Storm 時，您必須確定 Python Virtualenv 存在於叢集節點上。建立以 Linux 為基礎的新 HDInsight 叢集時，請使用下列指令碼動作設定執行[叢集自訂](hdinsight-hadoop-customize-cluster.md)：

    * __名稱__：在這裡提供易記名稱
    * \_\_ 指令碼 URI\_\_：使用 `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh` 做為值。此指令碼會在節點上安裝 Python Virtualenv，並建立 Streamparse 所使用的目錄
    * __Nimbus__：勾選此項目將指令碼套用到 Nimbus (前端) 節點。
    * __監督員__：勾選此項目將指令碼套用到監督員 (背景工作角色) 節點
    
    將其他項目保留空白。
    
    > [AZURE.WARNING]您也必須使用__公開金鑰__來保護 HDInsight 叢集的 SSH 使用者，才能使用 Streamparse 從遠端部署。
    >
    > 如需有關在 HDInsight 上搭配使用密碼與 SSH 的詳細資訊，請參閱下列其中一份文件：
    >
    > * [從 Linux、Unix 或 OS X 搭配使用 SSH 與以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [從 Windows 搭配使用 SSH 與以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 佈建叢集時，使用下列命令在開發環境上安裝 Streamparse：

        pip install streamparse
        
3. 安裝 Streamparse 之後，使用下列命令建立範例專案：

        sparse quickstart wordcount
        
    這會建立名為 `wordcount` 的新目錄，並移入一個範例 Word Count 專案。

4. 切換到 `wordcount` 目錄，以本機模式啟動拓撲：

        cd wordcount
        sparse run

    使用 Ctrl+C 可停止拓撲。

###部署拓撲

建立以 Linux 為基礎的 HDInsight 叢集之後，請使用下列步驟將叢集部署至拓撲：

1. 使用下列命令尋找叢集的背景工作角色節點的完整網域名稱：

        curl -u admin:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts | grep '"host_name" : "worker'
    
    這會擷取叢集的主機資訊、傳送到 grep，然後傳回背景工作角色節點的項目。您應該會看到類似下面的結果：
    
        "host_name" : "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    
    儲存 `"workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"` 資訊，下一步會用到。

2. 開啟`wordcount` 目錄中的 __config.json__ 檔案，變更下列項目：

    * __user__：將此項目設為您為 HDInsight 叢集設定的 SSH 使用者帳戶名稱。這會在部署專案時用來驗證叢集
    * __nimbus__：將此項目設為 `CLUSTERNAME-ssh.azurehdinsight.net`。將 CLUSTERNAME 取代為叢集名稱。與 Nimbus 節點 (叢集的前端節點) 通訊時會使用此項目
    * __workers__：將您使用 curl 擷取的背景工作角色節點的主機名稱寫入 workers 項目中。例如：
    
        ```
"workers": [
    "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net",
    "workernode1.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    ]
        ```
    
    * __virtualenv\_root__：將此項目設為 "/virtualenv"
    
    這會設定 HDInsight 叢集的專案，包括佈建期間由指令碼動作所建立的 `/virtualenv` 目錄。

4. 因為部署在 HDInsight 的 Streamparse 需要透過前端節點將您的驗證轉送至背景工作角色，所以您的工作站上必須啟動 `ssh-agent`。在大多數的作業系統上，它會自動啟動。使用下列命令確認它正在執行：

        echo "$SSH_AUTH_SOCK"
    
    如果 `ssh-agent` 正在執行，則應該會傳回類似下列的回應：
    
        /tmp/ssh-rfSUL1ldCldQ/agent.1792
    
    > [AZURE.NOTE]根據您的作業系統，完整路徑可能有所不同。例如，在 OS X 上，路徑可能類似於 `/private/tmp/com.apple.launchd.vq2rfuxaso/Listeners`。但是如果代理程式正在執行，應該會傳回某些路徑。
    
    如果未傳回任何資訊，請使用 `ssh-agent` 命令啟動代理程式。
    
5. 確認代理程式知道您用來向 HDInsight 伺服器驗證的金鑰。使用下列命令列出代理程式可用的金鑰：

        ssh-add -L
    
    這會傳回已新增至代理程式的私密金鑰。您可以比較這些結果，與您建立 SSH 金鑰來向 HDInsight 驗證時所產生之私密金鑰的內容。
    
    如果未傳回任何資訊，或傳回的資訊不符合您的私密金鑰，請使用下列命令將私密金鑰新增至代理程式：
    
        ssh-add /path/to/key/file
    
    例如，`ssh-add ~/.ssh/id_rsa`。

4. 您也必須設定 SSH，讓它知道應該將轉送用於 HDInsight 叢集。將下列內容加入 `~/.ssh/config`。如果此檔案不存在，請使用下列內容建立檔案：

        Host *.azurehdinsight.net
          ForwardAgent yes
        
        Host *.internal.cloudapp.net
          ProxyCommand ssh CLUSTERNAME-ssh.azurehdinsight.net nc %h %p
    
    將 CLUSTERNAME 取代為 HDInsight 叢集的名稱。
    
    這會在工作站上設定 SSH 代理程式，以支援透過您連接的任何 *.azurehdinsight.net 系統來轉送 SSH 認證。在此案例中，就是您的叢集的前端節點。接下來，它會設定命令將前端節點的 SSH 流量代理到個別的背景工作角色節點 (internal.cloudapp.net)。 這可讓 Streamparse 連接至前端節點，再從那裡連接至每個背景工作角色節點 (對 SSH 帳戶使用金鑰驗證)。
    
5. 最後，使用下列命令將拓撲從本機開發環境提交至 HDInsight 叢集：

        sparse submit
    
    這將會連接到 HDInsight 叢集、部署拓撲和任何 Python 相依性，然後啟動拓撲。

##後續步驟

本文件中，您已了解如何從 Storm 拓撲中使用 Python 元件。請參閱下列文件，了解搭配使用 Python 與 HDInsight 的其他方式。

* [如何使用 Python 串流處理 MapReduce 工作](hdinsight-hadoop-streaming-python.md)
* [如何在 Pig 和 Hive 中使用 Python 使用者定義函數 (UDF)](hdinsight-python.md)

<!-------HONumber=AcomDC_1210_2015--->