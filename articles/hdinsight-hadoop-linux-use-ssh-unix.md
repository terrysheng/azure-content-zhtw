<properties
   pageTitle="從 Linux、Unix 或 OS X 在以 Linux 為基礎的 HDInsight 上搭配使用 SSH 金鑰與 Hadoop | Aure"
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

##從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop (預覽)

以 Linux 為基礎的 HDInsight 叢集提供使用密碼或 SSH 金鑰保護 SSH 存取的選項。本文件提供從 Linux、Unix 或 OS X 用戶端搭配使用 SSH 與 HDInsight 的資訊。

> [AZURE.NOTE] 本文中的步驟假設您是使用 Linux、Unix 或 OS X 用戶端。雖然在安裝有提供 `ssh` 和 `ssh-keygen` 的封裝 (例如 Git for Windows) 後，您可以在 Windows 用戶端上執行這些步驟，但建議 Windows 用戶端依照 [從 Windows 搭配使用 SSH 與以 Linux 為基礎的 HDInsight (Hadoop)] 中的步驟進行。(/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/)。

##必要條件

* 適用於 Linux、Unix 和 OS X 用戶端的 **ssh-keygen** 和 **ssh**。作業系統通常會隨付提供此公用程式，或是可透過封裝管理系統來取得

* 支援 HTML5 的新式網頁瀏覽器

或

* Azure 跨平台命令列工具

##什麼是 SSH？

SSH 是用來登入遠端伺服器並在其中遠端執行命令的公用程式。SSH 會用以 Linux 為基礎的 HDInsight 來與叢集前端節點建立安全連線，並提供命令列供您輸入命令。然後直接在該伺服器上執行命令。

##建立 SSH 金鑰 (選擇性)

在建立以 Linux 為基礎的 HDInsight 叢集時，您可以選擇使用密碼或 SSH 金鑰來驗證伺服器。SSH 金鑰比較安全，因為它們依據的是憑證。如果您計劃搭配使用 SSH 金鑰與叢集，請使用下列資訊。

1. 開啟終端機工作階段，並使用下列命令來查看是否有任何現有 SSH 金鑰

		ls -al ~/.ssh

	在目錄清單中尋找下列檔案。以下是公用 SSH 金鑰的常見名稱。

	* id\_dsa.pub
	* id\_ecdsa.pub
	* id\_ed25519.pub
	* id\_rsa.pub

2. 如果不想使用現有檔案或沒有現有 SSH 金鑰，請用下列命令產生新的檔案。

		ssh-keygen -t rsa

	系統將會提示您輸入下列資訊：

	* 檔案位置 - 預設為 ~/.ssh/id\_rsa。
	* 複雜密碼 - 系統會提示您再次輸入此密碼
	
		> [AZURE.NOTE] 強烈建議您對此金鑰使用安全的複雜密碼。不過如果您忘記此複雜密碼，將無法加以復原。

	命令完成後，您會擁有兩個新檔案，即私密金鑰 (例如 **id\_rsa**) 和公開金鑰 (例如 **id\_rsa.pub**。)

##建立以 Linux 為基礎的 HDInsight 叢集

在建立以 Linux 為基礎的 HDInsight 叢集時，您必須提供先前建立的**公開金鑰**。從 Linux、Unix 或 OS X 用戶端有兩種方式可供建立 HDInsight 叢集：

* **Azure 管理入口網站** - 使用網頁型入口網站來建立叢集

* **Azure 跨平台命令列介面 (xplat-cli)** - 使用命令列命令來建立叢集

這兩種方法都需要**密碼**或**公開金鑰**。如需建立以 Linux 為基礎的 HDInsight 叢集的完整資訊，請參閱 [<a href="/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">佈建 Linux 架構的 HDInsight 叢集</a>]。

###Azure 管理入口網站

使用入口網站來建立以 Linux 為基礎的 HDInsight 叢集時，您必須輸入 **SSH 使用者名稱**，然後選擇輸入**密碼**或 **SSH 公開金鑰**。如果您選擇 **SSH 公開金鑰**，則必須將公開金鑰 (位於 **.pub** 副檔名的檔案中) 貼到下列表單。

![Image of form asking for public key](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] 金鑰檔就只是文字檔案。其內容類似下面這樣。
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

這會使用您提供的密碼或公用金鑰建立指定使用者的登入資訊。

###Azure 跨平台命令列介面

您可以使用 <a href="../xplat-cli/" target="_brad">Azure 跨平台命令列介面</a>，使用 `azure hdinsight cluster create` 命令來建立新叢集。

如需使用這個命令的詳細資訊，請參閱 [<a href="../hdinsight-hadoop-provision-linux-clusters/" target="_blank">使用自訂選項在 HDInsight 中佈建 Hadoop Linux 叢集</a>]。

##連線至以 Linux 為基礎的 HDInsight 叢集

在終端機工作階段使用 SSH 命令，提供使用者名稱和叢集位址以連線到叢集。

* **SSH 位址** - 叢集名稱加上 **-ssh.azurehdinsight.net**。例如，**mycluster-ssh.azurehdinsight.net**。

* **使用者名稱** - 建立叢集時所提供的 SSH 使用者名稱

下列範例以使用者身分 **me** 連線到 **mycluster** 叢集。

	ssh me@mycluster-ssh.azurehdinsight.net

如果您對使用者帳戶使用**密碼**，系統會提示您輸入密碼。

如果您使用以複雜密碼保護的 **SSH 金鑰**，則系統會提示您輸入複雜密碼；否則，SSH 會嘗試使用用戶端上的其中一個本機私密金鑰自動進行驗證。

> [AZURE.NOTE] 如果 SSH 沒有以正確**私密金鑰**自動進行驗證，請使用 **-i** 參數並指定私密金鑰的路徑。下列範例會從 `~/.ssh/id_rsa` 載入**私密金鑰**。
> 
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

##新增其他帳戶

2. 為新的使用者帳戶產生新的**公開金鑰**和**私密金鑰**，如[建立 SSH 金鑰](#create) 一節所述。

	> [AZURE.NOTE] 私密金鑰應該在使用者用來連接到叢集的用戶端上產生，或在建立後安全地傳輸到這個用戶端。

1. 在連往叢集的 SSH 工作階段中，使用下列命令加入新使用者。

		sudo adduser --disabled-password <username> 

	這會建立新的使用者帳戶，但會停用密碼驗證。

2. 使用下列命令建立用來保存金鑰的目錄和檔案。

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. 在 nano 編輯器開啟時，複製並貼上新使用者帳戶的**公開金鑰**內容。最後，使用 **CTRL-X** 來儲存檔案並結束編輯器。

	![image of nano editor with example key](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. 使用下列命令將 .ssh 資料夾和內容的擁有權變更為新的使用者帳戶。

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. 您現在應該就能使用新的使用者帳戶和**私密金鑰**驗證伺服器。

##<a id="tunnel"></a>SSH 通道

SSH 也可用來建立通道以將本機要求 (例如 Web 要求) 傳送到 HDInsight 叢集。要求便會路由至要求的資源，彷彿要求是在 HDInsight 叢集前端節點上產生。

在 HDInsight 叢集上所要存取的 Web 服務是使用叢集之前端節點或背景工作節點的內部網域名稱時，這種方式最為適用。例如，Ambari 網頁的某些區段使用內部網域名稱，例如 **headnode0.mycluster.d1.internal.cloudapp.net**。這些名稱無法從叢集外加以解析，不過透過 SSH 以通道傳送的要求會在叢集內產生，並且將會正確解析。

使用下列步驟來建立 SSH 通道，並設定瀏覽器用此通道來連接到叢集。

1. 下列命令可用來建立通往叢集前端節點的 SSH 通道。

		ssh -C2qTnNf -D 9876 username@clustername-ssh.azurehdinsight.net

	這會建立透過 SSH 將流量由本機連接埠 9876 路由傳送至叢集的連線。可用選項包括：

	* **D 8080** - 會透過通道路由傳送流量的本機連接埠

	* **C** - 壓縮所有資料，因為網路流量大多是文字

	* **2** - 強制 SSH 僅嘗試通訊協定第 2 版

	* **q** - 無訊息模式

	* **T** - 停用虛擬 tty 配置，因為我們只是轉送連接埠
	
	* **n** - 防止讀取 STDIN，因為我們只是轉送連接埠

	* **N** - 不執行遠端命令，因為我們只是轉送連接埠

	* **f** - 在背景中執行

	如果您使用 **SSH 金鑰**設定叢集，您可能需要使用 `-i` 參數並指定 SSH 私密金鑰的路徑。

	在命令完成後，傳送至本機電腦上連接埠 9876 的流量將透過 SSL 路由傳送至叢集前端節點，看起來就像是在該處產生。

2. 設定用戶端程式 (例如 Firefox) 使用 **localhost:9876** 做為 **SOCKS v5** proxy。Firefox 的設定如下所示。

	![image of Firefox settings](./media/hdinsight-hadoop-linux-use-ssh-unix/socks.png)

	> [AZURE.NOTE] 選取 [**遠端 DNS**] 會使用 HDInsight 叢集解析 DNS 要求。若未選取，則會在本機解析 DNS。

	您可以驗證是否透過通道路由傳送流量，方法是在 Firefox 中啟用和停用 Proxy 設定的情況下造訪網站，例如 <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a>。在啟用時，是使用 Microsoft Azure 資料中心內之機器的 IP 位址。

###瀏覽器延伸模組

當設定瀏覽器使用通道的功能在運作時，您通常不會想透過通道傳送所有流量。瀏覽器延伸模組 (例如 <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a>) 支援 URL 要求的模式比對 (僅限 FoxyProxy Standard 或 Plus)，以便只有特定 URL 的要求會透過通道傳送。

如果您已安裝 **FoxyProxy Standard**，請使用下列步驟將它設定為只透過通道轉送 HDInsight 的流量。

1. 在您的瀏覽器中開啟 FoxyProxy 延伸模組。比方說，在 Firefox 中選取 [位址] 欄位旁的 FoxyProxy 圖示。

	![foxyproxy icon](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxy.png)

2. 選取 [**加入新的 Proxy**]、[**一般**] 索引標籤，然後輸入 **HDInsight** 的 Proxy 名稱。

	![foxyproxy general](./media/hdinsight-hadoop-linux-use-ssh-unix/foxygeneral.png)

3. 選取 [**Proxy 詳細資料**] 索引標籤並填入下列欄位。

	* **主機或 IP 位址** - localhost，因為我們使用本機電腦上的 SSH 通道

	* **連接埠** - 用於 SSH 通道的連接埠

	* **SOCKS Proxy** - 選取此選項讓瀏覽器使用通道做為 Proxy

	* **SOCKS v5** - 選取此選項以設定 Proxy 的要求版本

	![foxyproxy proxy](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxyproxy.png)

4. 選取 [**URL 模式**] 索引標籤，然後選取 [**加入新的模式**]。使用下列欄位定義模式，然後按一下 [**確定**]。

	* **模式名稱** - **headnode** - 這是易記的模式名稱

	* **URL 模式** - **\*headnode\*** - 這會定義模式來比對任何 URL 中是否有 **headnode** 一字。

	![foxyproxy pattern](./media/hdinsight-hadoop-linux-use-ssh-unix/foxypattern.png)

4. 選取 [**確定**] 以加入 Proxy 並關閉 [**Proxy 設定**]。

5. 在 FoxyProxy 對話方塊頂端，將 [**選取模式**] 變更為 [**根據預先定義的模式和優先順序使用 Proxy**]，然後選取 [**關閉**]。

	![foxyproxy select mode](./media/hdinsight-hadoop-linux-use-ssh-unix/selectmode.png)

在執行這些步驟後，只有包含 **headnode** 字串之 URL 的要求會透過 SSL 通道路由傳送。

##後續步驟

您已經了解如何使用 SSH 金鑰進行驗證，接著請了解如何在 HDInsight 上搭配使用 MapReduce 和 Hadoop。

* [搭配 HDInsight 使用 Hive](../hdinsight-use-hive/)

* [搭配 HDInsight 使用 Pig](../hdinsight-use-pig/)

* [搭配 HDInsight 使用 MapReduce 工作](../hdinsight-use-mapreduce/)
 
<!--HONumber=47-->
