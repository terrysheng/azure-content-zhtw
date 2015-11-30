<properties
   pageTitle="從 Windows 搭配使用 SSH 金鑰與 Linux 架構叢集上的 Hadoop | Microsoft Azure"
   description="了解如何建立和使用 SSH 金鑰來驗證以 Linux 為基礎的 HDInsight 叢集。使用 PuTTY SSH 用戶端，從 Windows 用戶端連接叢集。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="11/16/2015"
   ms.author="larryfr"/>

#從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

[安全殼層 (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) 可讓您使用命令列介面在以 Linux 為基礎的 HDInsight 叢集上遠端執行作業。本文提供從以 Windows 為基礎的用戶端使用 PuTTY SSH 用戶端連接到 HDInsight 的資訊。

> [AZURE.NOTE]本文中的步驟假設您是使用以 Windows 為基礎的用戶端。如果您使用 Linux、Unix 或 OS X 用戶端，請參閱[從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)。

##必要條件

* 適用於以 Windows 為基礎的用戶端的 **PuTTY** 和 **PuTTYGen**您可從下列位置取得這些公用程式：[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

* 支援 HTML5 的新式網頁瀏覽器。

或

* [適用於 Mac、Linux 和 Windows 的 Azure CLI](../xplat-cli-install.md)。

##什麼是 SSH？

SSH 是用來登入遠端伺服器並在其中遠端執行命令的公用程式。SSH 會用以 Linux 為基礎的 HDInsight 來與叢集前端節點建立加密的連線，並提供命令列供您輸入命令。然後直接在該伺服器上執行命令。

###SSH 使用者名稱

SSH 使用者名稱為您用來向 HDInsight 叢集驗證的名稱。當您在叢集建立期間指定 SSH 使用者名稱時，會在叢集中所有節點上建立此使用者。建立叢集之後，您可以使用此使用者名稱連線至 HDInsight 叢集前端節點。然後您可以從前端節點連線至個別的工作節點。

###SSH 密碼或公開金鑰

SSH 使用者可以使用密碼或公開金鑰來驗證。密碼是您自己設定的字串，公開金鑰則是為唯一識別您而產生之密碼編譯金鑰組的一部份。

金鑰會比密碼更安全，但是需要額外步驟才能產生金鑰，且您必須將含有金鑰的檔案保存在安全位置。如果任何人取得金鑰檔案存取權，他們就可以取得您帳戶的存取權。或者，如果您遺失金鑰檔案，您將無法登入帳戶。

金鑰組包含公開金鑰 (會傳送至 HDInsight 伺服器) 和私密金鑰 (會保留在您的用戶端機器中)。 當您使用 SSH 連線至 HDInsight 伺服器時，SSH 用戶端將會使用您電腦上的私密金鑰向伺服器驗證。

##建立 SSH 金鑰

如果您計劃搭配使用 SSH 金鑰與叢集，請使用下列資訊。如果您計畫使用密碼，可以略過此小節。

1. 開啟 PuTTYGen。

2. 在 [**要產生的金鑰類型**] 中選取 [**SSH-2 RSA**]，然後按一下 [**產生**]。

	![PuTTYGen 介面](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. 在進度列下方的區域中移動滑鼠直到進度列填滿為止。移動滑鼠時會產生用來產生金鑰的隨機資料。

	![移動滑鼠](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

	在產生金鑰後，就會顯示公開金鑰。

4. 若要提高安全性，您可以在 [**金鑰複雜密碼**] 欄位中輸入複雜密碼，然後在 [**確認複雜密碼**] 欄位中輸入相同的值。

	![複雜密碼](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)

	> [AZURE.NOTE]強烈建議您對此金鑰使用安全的複雜密碼。不過如果您忘記此複雜密碼，將無法加以復原。

5. 按一下 [**儲存私密金鑰**] 將金鑰儲存到 **.ppk** 檔案。此金鑰會用來驗證以 Linux 為基礎的 HDInsight 叢集。

	> [AZURE.NOTE]此金鑰可用來存取以 Linux 為基礎的 HDInsight 叢集，因此請將它儲存在安全的位置。

6. 按一下 [**儲存公開金鑰**] 將金鑰儲存為 **.txt** 檔案。這可讓您在日後建立其他以 Linux 為基礎的 HDInsight 叢集時重複使用此公開金鑰。

	> [AZURE.NOTE]PuTTYGen 的頂端也會顯示此公開金鑰。當您使用 Azure Preview 入口網站建立叢集時，可以用滑鼠右鍵按一下此欄位，複製值並將其貼入表單。

##建立以 Linux 為基礎的 HDInsight 叢集

在建立以 Linux 為基礎的 HDInsight 叢集時，您必須提供先前建立的公開金鑰。從以 Windows 為基礎的用戶端，有兩種方式可以建立以 Linux 為基礎的 HDInsight 叢集：

* **Azure Preview 入口網站** - 使用網頁型入口網站來建立叢集。

* **適用於 Mac、Linux 和 Windows 的 Azure CLI** - 使用命令列命令建立叢集。

這兩種方法都需要公開金鑰。如需建立以 Linux 為基礎的 HDInsight 叢集的完整資訊，請參閱[佈建以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

###Azure Preview 入口網站

使用 [Azure Preview 入口網站][preview-portal]來建立以 Linux 為基礎的 HDInsight 叢集時，您必須輸入 [**SSH 使用者名稱**]，然後選擇以輸入 [**密碼**] 或 [**SSH 公開金鑰**]。

如果您選取 [**SSH 公開金鑰**]，則可以將公開金鑰 (在 PuttyGen 會顯示於 [__貼到 OpenSSH 授權金鑰檔案的公開金鑰__] 欄位) 貼入 [__SSH 公開金鑰__] 欄位，或選取 [__選取檔案__] 以瀏覽和選取包含公開金鑰的檔案。

![要求公開金鑰的表單映像](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

這會建立指定使用者的登入資訊，並啟用密碼驗證或 SSH 金鑰驗證。

###適用於 Mac、Linux 和 Windows 的 Azure 命令列介面

您可以使用[適用於 Mac、Linux 和 Windows 的 Azure CLI](../xplat-cli-install.md)，使用 `azure hdinsight cluster create` 來建立新叢集。

如需使用這個命令的詳細資訊，請參閱[使用自訂選項在 HDInsight 中佈建 Hadoop Linux 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

##連線至以 Linux 為基礎的 HDInsight 叢集

1. 開啟 PuTTY。

	![putty 介面](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. 如果您在建立使用者帳戶時提供 SSH 金鑰，您必須執行下列步驟來選取要在驗證叢集時使用的私密金鑰：

	在 [**類別**] 中，依序展開 [**連接**] 和 [**SSH**]，然後選取 [**驗證**]。最後，按一下 [**瀏覽**]，然後選取內含私密金鑰的 .ppk 檔案。

	![putty 介面，選取私密金鑰](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. 在 [**類別**] 中，選取 [**工作階段**]。在 [**PuTTY 工作階段的基本選項**] 畫面上，將您的 HDInsight 伺服器的 SSH 位址輸入到 [**主機名稱 (或 IP 位址)**] 欄位。SSH 位址是叢集名稱加上 **-ssh.azurehdinsight.net**。例如，**mycluster-ssh.azurehdinsight.net**。

	![已輸入 ssh 位址的 putty 介面](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. 若要儲存連線資訊供日後使用，請在 [**儲存的工作階段**] 底下輸入此連線的名稱，然後按一下 [**儲存**]。連線便會加入已儲存的工作階段清單。

5. 按一下 [**開啟**] 來連線到叢集。

	> [AZURE.NOTE]如果這是您第一次連線到叢集，您會收到安全性警示。這是正常現象。請選取 [**是**] 快取伺服器的 RSA2 金鑰以繼續進行。

6. 出現提示時，輸入您建立叢集時所輸入的使用者。如果您對使用者提供密碼，系統會提示您一併輸入密碼。

> [AZURE.NOTE]上述步驟假設您使用連接埠 22，這將會連接到 HDInsight 叢集上的前端節點 0。如果您使用連接埠 23，您將會連接到前端節點 1。如需前端節點的詳細資訊，請參閱 [HDInsight 上 Hadoop 叢集的可用性和可靠性](hdinsight-high-availability-linux.md)。

###連接至背景工作節點

背景工作節點無法直接從 Azure 資料中心外部存取，但是可以透過 SSH 從叢集前端節點存取。

如果您在建立使用者帳戶時提供 SSH 金鑰，當您想要連接至背景工作節點時，您必須執行下列步驟來選取要在驗證叢集時使用的私密金鑰。

1. 從下列位置安裝 Pageant：[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。此公用程式是用於快取 PuTTY 的 SSH 金鑰。

2. 執行 Pageant。它會最小化為狀態列中的圖示。以滑鼠右鍵按一下圖示，然後選取 [**新增金鑰**]。

    ![新增金鑰](./media/hdinsight-hadoop-linux-use-ssh-windows/addkey.png)

3. 當瀏覽對話方塊顯示時，請選取包含金鑰的 .ppk 檔案，然後按一下 [**開啟**]。這樣會將金鑰新增至 Pageant，在連接至叢集時將金鑰提供給 PuTTY。

    > [AZURE.IMPORTANT]如果您使用 SSH 金鑰來保護您的帳戶的安全，您必須先完成上述步驟，才能連接至背景工作節點。

4. 開啟 PuTTY。

5. 如果您使用 SSH 金鑰進行驗證，請在 [**類別**] 區段中，依序展開 [**連接**]、[**SSH**]，然後選取 [**驗證**]。

    在 [**驗證參數**] 區段中，啟用 [**允許代理程式轉送**]。這樣會允許 PuTTY 在連接至背景工作節點時，透過連接至叢集前端節點，自動通過憑證驗證。

    ![允許代理程式轉送](./media/hdinsight-hadoop-linux-use-ssh-windows/allowforwarding.png)

6. 如稍早所述連接到叢集。如果您使用 SSH 金鑰進行驗證，不需要選取金鑰 - 會對叢集使用新增至 Pageant 的 SSH 金鑰來進行驗證。

7. 建立連接之後，請使用下列項目以擷取您的叢集中的節點清單。將 *ADMINPASSWORD* 取代為您的叢集系統管理員帳戶的密碼。將 *CLUSTERNAME* 取代為您叢集的名稱。

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    這樣會以 JSON 格式傳回叢集中節點的資訊，包括 `host_name`，其中包含每個節點的完整網域名稱 (FQDN)。以下是 **curl** 命令傳回之 `host_name` 項目的範例：

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

8. 當您具有想要連接之背景工作節點的清單之後，請從 PuTTY 工作階段使用下列命令，以開啟與背景工作節點的連線：

        ssh USERNAME@FQDN

    將 *USERNAME* 取代為您的 SSH 使用者名稱，將 *FQDN* 取代為背景工作節點的 FQDN。例如，`workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`。

    > [AZURE.NOTE]如果您使用密碼以驗證您的 SSH 工作階段，則系統會提示您再次輸入密碼。如果您使用 SSH 金鑰，連線應該沒有任何提示即會完成。

9. 建立工作階段之後，針對 PuTTY 的提示會從 `username@hn0-clustername` 變更為 `username@wn0-clustername`，以指出您已連接至背景工作節點。目前您執行的任何命令會在背景工作節點上執行。

10. 完成在背景工作節點上執行動作之後，請使用 `exit` 命令以關閉背景工作節點的工作階段。這樣會帶您返回 `username@hn0-clustername` 提示字元。

##新增更多帳戶

如果您需要將更多帳戶新增至您的叢集，請執行下列步驟：

1. 如先前所述為新的使用者帳戶產生新的公開金鑰和私密金鑰。

2. 在連往叢集的 SSH 工作階段中，使用下列命令加入新使用者：

		sudo adduser --disabled-password <username>

	這會建立新的使用者帳戶，但會停用密碼驗證。

3. 使用下列命令建立用來保存金鑰的目錄和檔案：

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

4. 在 nano 編輯器開啟時，複製並貼上新使用者帳戶的公開金鑰內容。最後，使用 **Ctrl-X** 來儲存檔案並結束編輯器。

	![具有範例金鑰之 nano 編輯器的映像](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

5. 使用下列命令將 .ssh 資料夾和內容的擁有權變更為新的使用者帳戶：

		sudo chown -hR <username>:<username> /home/<username>/.ssh

6. 您現在應該就能使用新的使用者帳戶和私密金鑰驗證伺服器。

##<a id="tunnel"></a>SSH 通道

SSH 可用來建立通道以將本機要求 (例如 Web 要求) 傳送到 HDInsight 叢集。要求便會路由至要求的資源，彷彿要求是在 HDInsight 叢集前端節點上產生。

> [AZURE.IMPORTANT]SSH 通道是存取某些 Hadoop 服務之 Web UI 的必要項目。例如，[作業記錄] UI 或 [資源管理員] UI 都只能使用 SSH 通道存取。

如需建立及使用 SSH 通道的詳細資訊，請參閱[使用 SSH 通道來存取 Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie 及其他 Web UI](hdinsight-linux-ambari-ssh-tunnel.md)。

##後續步驟

您已經了解如何使用 SSH 金鑰進行驗證，接著請了解如何在 HDInsight 上搭配使用 MapReduce 和 Hadoop。

* [搭配 HDInsight 使用 Hivet](hdinsight-use-hive.md)

* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)

* [搭配 HDInsight 使用 MapReduce 工作](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

<!---HONumber=Nov15_HO4-->