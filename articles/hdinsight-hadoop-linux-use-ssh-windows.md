<properties
   pageTitle="從 Windows 在以 Linux 為基礎的 HDInsight 上搭配使用 SSH 金鑰與 Hadoop | Aure"
   description="了解如何建立和使用 SSH 金鑰來驗證以 Linux 為基礎的 HDInsight 叢集。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

##從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop (預覽)

以 Linux 為基礎的 HDInsight 叢集提供使用密碼或 SSH 金鑰保護 SSH 存取的選項。本文提供從 Windows 用戶端使用 PuTTY SSH 用戶端連接到 HDInsight 的資訊。

> [AZURE.NOTE] 本文中的步驟假設您是使用 Windows 用戶端。如果您使用 Linux、 Unix 或 OS X 用戶端，請參閱 [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/)。

##必要條件

* 適用於 Windows 用戶端的 **PuTTY** 和 **PuTTYGen**。這些公用程式可從下列位置取得：<a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>

* 支援 HTML5 的新式網頁瀏覽器

或

* Azure PowerShell

或

* Azure 跨平台命令列工具

##什麼是 SSH？

SSH 是用來登入遠端伺服器並在其中遠端執行命令的公用程式。SSH 會用以 Linux 為基礎的 HDInsight 來與叢集前端節點建立安全連線，並提供命令列供您輸入命令。然後直接在該伺服器上執行命令。

##建立 SSH 金鑰 (選擇性)

在建立以 Linux 為基礎的 HDInsight 叢集時，您可以選擇使用密碼或 SSH 金鑰來驗證伺服器。SSH 金鑰比較安全，因為它們依據的是憑證。如果您計劃搭配使用 SSH 金鑰與叢集，請使用下列資訊。

1. 開啟 **PuTTYGen**。

2. 在 [**要產生的金鑰類型**] 中選取 [**SSH-2 RSA**]，然後按一下 [**產生**]。

	![PuTTYGen interface](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. 在進度列下方的區域中移動滑鼠直到進度列填滿為止。移動滑鼠時會產生用來產生金鑰的隨機資料。

	![moving the mouse around](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

	在產生金鑰後，就會顯示公開金鑰。

4. 若要提高安全性，您可以在 [**金鑰複雜密碼**] 欄位中輸入複雜密碼，然後在 [**確認複雜密碼**] 欄位中輸入相同的值。

	![passphrase](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)
	
	> [AZURE.NOTE] 強烈建議您對此金鑰使用安全的複雜密碼。不過如果您忘記此複雜密碼，將無法加以復原。

5. 按一下 [**儲存私密金鑰**] 將金鑰儲存到 **.ppk** 檔案。此金鑰會用來驗證以 Linux 為基礎的 HDInsight 叢集。

	> [AZURE.NOTE] 此金鑰可用來存取以 Linux 為基礎的 HDInsight 叢集，因此請將它儲存在安全的位置。

6. 按一下 [**儲存公開金鑰**] 將金鑰儲存為 **.txt** 檔案。這可讓您在日後建立其他以 Linux 為基礎的 HDInsight 叢集時重複使用此公開金鑰。

	> [AZURE.NOTE] **PuTTYGen** 的頂端也會顯示此公開金鑰。您可以用滑鼠右鍵按一下此欄位，複製值並貼到表單中，例如 Azure 管理入口網站中的 HDInsight 精靈。

##建立以 Linux 為基礎的 HDInsight 叢集

在建立以 Linux 為基礎的 HDInsight 叢集時，您必須提供先前建立的**公開金鑰**。從 Windows 用戶端，有兩種方式可以建立以 Linux 為基礎的 HDInsight 叢集：

* **Azure 管理入口網站** - 使用網頁型入口網站來建立叢集

* **Azure 跨平台命令列介面 (xplat-cli)** - 使用命令列命令來建立叢集

這兩種方法都需要**公開金鑰**。如需建立以 Linux 為基礎的 HDInsight 叢集的完整資訊，請參閱 [<a href="./hdinsight-hadoop-provision-linux-clusters/" target="_blank">佈建 Linux 架構的 HDInsight 叢集</a>]。

###Azure 管理入口網站

使用入口網站來建立以 Linux 為基礎的 HDInsight 叢集時，您必須在下列表單中輸入使用者名稱和密碼或公開金鑰。

![Image of form asking for public key](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

這會建立指定使用者的登入資訊，並啟用密碼驗證或 SSH 金鑰驗證。

###Azure 跨平台命令列介面

您可以使用 <a href="../xplat-cli/" target="_brad">Azure 跨平台命令列介面</a>，使用 `azure hdinsight cluzter create` 命令來建立新叢集。

如需使用這個命令的詳細資訊，請參閱 [<a href="../hdinsight-hadoop-provision-linux-clusters/" target="_blank">使用自訂選項在 HDInsight 中佈建 Hadoop Linux 叢集</a>]。

##<a id="connect"></a>連線至以 Linux 為基礎的 HDInsight 叢集

1. 開啟 PuTTY。

	![putty interface](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. 如果您在建立使用者帳戶時提供 **SSH 金鑰**，您必須執行下列步驟來選取要在驗證叢集時使用的 [**私密金鑰**]。

	在 [**類別**] 中，依序展開 [**連接**] 和 [**SSH**]，然後選取 [**驗證**]。最後，按一下 [**瀏覽**]，然後選取內含**私密金鑰**的 **.ppk**。

	![putty interface, select private key](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. 在 [**類別**] 中，選取 [**工作階段**]。在 [**PuTTY 工作階段的基本選項**] 畫面上，將您 HDInsight 伺服器的 SSH 位址輸入到 [**主機名稱 (或 IP 位址)**] 欄位。SSH 位址是叢集名稱加上 **-ssh.azurehdinsight.net**。例如，**mycluster-ssh.azurehdinsight.net**。

	![putty interface with ssh address entered](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. 若要儲存連線資訊供日後使用，請在 [**儲存的工作階段**] 底下輸入此連線的名稱，然後按一下 [**儲存**]。連線便會加入已儲存的工作階段清單。

5. 按一下 [**開啟**] 來連線到叢集。

	> [AZURE.NOTE] 如果這是您第一次連線到叢集，您會收到安全性警示。這是正常現象 - 請選取 [**是**] 快取伺服器的 RSA2 金鑰以繼續進行。

6. 出現提示時，輸入您建立叢集時所輸入的使用者。如果您對使用者提供**密碼**，系統會提示您一併輸入密碼。

##新增其他帳戶

2. 如先前所述為新的使用者帳戶產生新的**公開金鑰**和**私密金鑰**。

1. 在連往叢集的 SSH 工作階段中，使用下列命令加入新使用者。

		sudo adduser --disabled-password <username> 

	這會建立新的使用者帳戶，但會停用密碼驗證。

2. 使用下列命令建立用來保存金鑰的目錄和檔案。

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. 在 nano 編輯器開啟時，複製並貼上新使用者帳戶的**公開金鑰**內容。最後，使用 **CTRL-X** 來儲存檔案並結束編輯器。

	![image of nano editor with example key](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

4. 使用下列命令將 .ssh 資料夾和內容的擁有權變更為新的使用者帳戶。

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. 您現在應該就能使用新的使用者帳戶和**私密金鑰**驗證伺服器。

##<a id="tunnel"></a>SSH 通道

SSH 也可用來建立通道以將本機要求 (例如 Web 要求) 傳送到 HDInsight 叢集。要求便會路由至要求的資源，彷彿要求是在 HDInsight 叢集前端節點上產生。

在 HDInsight 叢集上所要存取的 Web 服務是使用叢集之前端節點或背景工作節點的內部網域名稱時，這種方式最為適用。例如，Ambari 網頁的某些區段使用內部網域名稱，例如 **headnode0.mycluster.d1.internal.cloudapp.net**。這些名稱無法從叢集外加以解析，不過透過 SSH 以通道傳送的要求會在叢集內產生，並且將會正確解析。

使用下列步驟來建立 SSH 通道，並設定瀏覽器用此通道來連接到叢集。

1. 開啟 PuTTY，並輸入 [[連線]] 區段中所記載的連線資訊(#connect) 。

2. 在對話方塊左側的 [**類別**] 區段中，依序展開 [**連線**] 和 [**SSH**]，最後選取 [**通道**]。

2. 在 [**控制 SSH 連接埠轉送的選項**] 表單中提供下列資訊。

	* **來源連接埠** - 您想要轉送之用戶端上的連接埠。例如，**9876**

	* **目的地** - 以 Linux 為基礎的 HDInsight 叢集的 SSH 位址。例如 **mycluster-ssh.azurehdinsight.net**

	* **動態** - 啟用動態 SOCKS Proxy 路由

	![image of tunneling options](./media/hdinsight-hadoop-linux-use-ssh-windows/puttytunnel.png)

3. 最後，選取 [**新增**] 來加入設定，然後選取 [**開啟**] 開啟 SSH 連線。

4. 出現提示時，登入伺服器。這會建立 SSH 工作階段，並啟用通道。

2. 設定用戶端程式 (例如 Firefox) 使用 **localhost:9876** 做為 **SOCKS v5** proxy。Firefox 的設定如下所示。

	![image of Firefox settings](./media/hdinsight-hadoop-linux-use-ssh-windows/socks.png)

	> [AZURE.NOTE] 選取 [**遠端 DNS**] 會使用 HDInsight 叢集解析 DNS 要求。若未選取，則會在本機解析 DNS。

	您可以驗證是否透過通道路由傳送流量，方法是在 Firefox 中啟用和停用 Proxy 設定的情況下造訪網站，例如 <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a>。在啟用時，是使用 Microsoft Azure 資料中心內之機器的 IP 位址。

###瀏覽器延伸模組

當設定瀏覽器使用通道的功能在運作時，您通常不會想透過通道傳送所有流量。瀏覽器延伸模組 (例如 <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a>) 支援 URL 要求的模式比對 (僅限 FoxyProxy Standard 或 Plus)，以便只有特定 URL 的要求會透過通道傳送。

如果您已安裝 **FoxyProxy Standard**，請使用下列步驟將它設定為只透過通道轉送 HDInsight 的流量。

1. 在您的瀏覽器中開啟 FoxyProxy 延伸模組。比方說，在 Firefox 中選取 [位址] 欄位旁的 FoxyProxy 圖示。

	![foxyproxy icon](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxy.png)

2. 選取 [**加入新的 Proxy**]、[**一般**] 索引標籤，然後輸入 **HDInsight** 的 Proxy 名稱。

	![foxyproxy general](./media/hdinsight-hadoop-linux-use-ssh-windows/foxygeneral.png)

3. 選取 [**Proxy 詳細資料**] 索引標籤並填入下列欄位。

	* **主機或 IP 位址** - localhost，因為我們使用本機電腦上的 SSH 通道

	* **連接埠** - 用於 SSH 通道的連接埠

	* **SOCKS Proxy** - 選取此選項讓瀏覽器使用通道做為 Proxy

	* **SOCKS v5** - 選取此選項以設定 Proxy 的要求版本

	![foxyproxy proxy](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxyproxy.png)

4. 選取 [**URL 模式**] 索引標籤，然後選取 [**加入新的模式**]。使用下列欄位定義模式，然後按一下 [**確定**]。

	* **模式名稱** - **headnode** - 這是易記的模式名稱

	* **URL 模式** - **\*headnode\*** - 這會定義模式來比對任何 URL 中是否有 **headnode** 一字。

	![foxyproxy pattern](./media/hdinsight-hadoop-linux-use-ssh-windows/foxypattern.png)

4. 選取 [**確定**] 以加入 Proxy 並關閉 [**Proxy 設定**]。

5. 在 FoxyProxy 對話方塊頂端，將 [**選取模式**] 變更為 [**根據預先定義的模式和優先順序使用 Proxy**]，然後選取 [**關閉**]。

	![foxyproxy select mode](./media/hdinsight-hadoop-linux-use-ssh-windows/selectmode.png)

在執行這些步驟後，只有包含 **headnode** 字串之 URL 的要求會透過 SSL 通道路由傳送。

##後續步驟

您已經了解如何使用 SSH 金鑰進行驗證，接著請了解如何在 HDInsight 上搭配使用 MapReduce 和 Hadoop。

* [搭配 HDInsight 使用 Hive](../hdinsight-use-hive/)

* [搭配 HDInsight 使用 Pig](../hdinsight-use-pig/)

* [搭配 HDInsight 使用 MapReduce 工作](../hdinsight-use-mapreduce/)
 
<!--HONumber=47-->
