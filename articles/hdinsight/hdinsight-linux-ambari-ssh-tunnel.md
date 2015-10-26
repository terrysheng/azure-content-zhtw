<properties
pageTitle="使用 SSH 通道來存取 Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie 及其他 Web UI"
description="了解如何使用 SSH 通道，安全地瀏覽以 Linux 為基礎的 HDInsight 節點上裝載的 Web 資源。"
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
ms.date="10/09/2015"
ms.author="larryfr"/>

#使用 SSH 通道來存取 Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie 及其他 Web UI

以 Linux 為基礎的 HDInsight 叢集可讓您透過網際網路存取 Ambari Web UI，但無法存取 UI 的某些功能。例如，經由 Ambari 呈現的其他服務的 Web UI。若要使用 Ambari Web UI 的完整功能，您必須在叢集前端使用 SSH 通道。

##什麼需要 SSH 通道？

Ambari 中有數個功能表在沒有 SSH 通道的情況下，會不完整填入，因為它們都依賴在叢集上執行的其他 Hadoop 服務所公開的網站和服務。通常這些網站並未受到保護，因此直接在網際網路上公開並不安全。有時候服務會在另一個叢集節點 (例如 Zookeeper 節點) 上執行網站。

下列是 Ambari Web UI 使用的服務，如果沒有 SSH 通道就無法存取這些服務：

* ResourceManager、
* JobHistory、
* NameNode、
* 執行緒堆疊、
* Oozie Web UI
* HBase Master 和記錄 UI
* Storm UI

如果您使用指令碼動作來自訂叢集，則您安裝的任何服務或公用程式，都會需要 SSH 通道才能公開 Web UI。例如，如果您使用指令碼動作安裝 Hue，就必須使用 SSH 通道來存取 Hue Web UI。

##什麼是 SSH 通道？

[安全殼層 (SSH) 通道](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling)將流量路由傳送到本機工作站上的連接埠的方式為，透過與 HDInsight 叢集前端節點的 SSH 連線，接著在前端節點上解析要求，使要求如同在前端節點上產生。接著，透過工作站的通道，將回應路由傳送回去。

##先決條件

針對 Web 流量使用 SSH 通道時，您必須擁有下列項目：

* SSH 用戶端。若為 Linux 和 Unix 發佈或 Macintosh OS X，`ssh` 命令會隨作業系統提供。若為 Windows，我們建議使用 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

	> [AZURE.NOTE]如果您想要使用 `ssh` 或 PuTTY 以外的 SSH 用戶端，請參考您用戶端的說明文件，了解如何建立 SSH 通道。

* 可以設定為使用 SOCKS Proxy 的網頁瀏覽器。

* __(選擇性)__：如 [FoxyProxy](http://getfoxyproxy.org/,) 之類的外掛程式，其可套用的規則是只將特定要求透過通道進行路由傳送。

	> [AZURE.WARNING]若無 FoxyProxy 之類的外掛程式，所有透過瀏覽器建立的要求可能都會透過通道進行路由傳送。這會導致瀏覽器中的網頁載入速度較慢。

##<a name="usessh"></a>使用 SSH 命令建立通道

使用下列命令，利用 `ssh` 命令建立 SSH 通道。用您 HDInsight 叢集的 SSH 使用者取代 __USERNAME__，並用您 HDInsight 叢集的名稱取代 __CLUSTERNAME__。

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

這會建立透過 SSH 將流量由本機連接埠 9876 路由傳送至叢集的連線。可用選項包括：

* **D 9876** - 會透過通道路由傳送流量的本機連接埠。

* **C** - 壓縮所有資料，因為網路流量大多是文字。

* **2** - 強制 SSH 僅嘗試通訊協定第 2 版。

* **q** - 無訊息模式。

* **T** - 停用虛擬 tty 配置，因為我們只是轉送連接埠。

* **n** - 防止讀取 STDIN，因為我們只是轉送連接埠。

* **N** - 不執行遠端命令，因為我們只是轉送連接埠。

* **f** - 在背景中執行。

如果您使用 SSH 金鑰設定叢集，您可能需要使用 `-i` 參數並指定 SSH 私密金鑰的路徑。

在命令完成後，傳送至本機電腦上連接埠 9876 的流量將透過安全通訊端層 (SSL) 路由傳送至叢集前端節點，看起來就像是在該處產生。

##<a name="useputty"></a>使用 PuTTY 建立通道

使用下列步驟，利用 PuTTY 建立 SSH 通道。

1. 開啟 PuTTY，並輸入連線資訊。如果您不熟悉 PuTTY，請參閱[從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)，以取得如何搭配 HDInsight 使用 PuTTY 的資訊。

2. 在對話方塊左側的 [**類別**] 區段中，依序展開 [**連接**] 和 [**SSH**]，最後選取 [**通道**]。

3. 在 [**控制 SSH 連接埠轉送的選項**] 表單中提供下列資訊：

	* **來源連接埠** - 您想要轉送之用戶端上的連接埠。例如，**9876**。

	* **目的地** - 以 Linux 為基礎的 HDInsight 叢集的 SSH 位址。例如，**mycluster-ssh.azurehdinsight.net**。

	* **動態** - 啟用動態 SOCKS Proxy 路由。

	![通道選項的映像](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. 按一下 [**新增**] 以新增設定，然後按一下 [**開啟**] 開啟 SSH 連線。

5. 出現提示時，登入伺服器。這會建立 SSH 工作階段，並啟用通道。

##從瀏覽器使用通道

> [AZURE.NOTE]本節中的步驟使用 FireFox 瀏覽器，因為它在 Linux、Unix、Macintosh OS X 和 Windows 系統上均可隨意運用。其他最新的瀏覽器，如 Google Chrome、Microsoft Edge 或 Apple Safari 等應該也可以運作；不過，某些步驟中所使用的 FoxyProxy 外掛程式可能無法適用於所有瀏覽器。

1. 將瀏覽器設定為使用 **localhost:9876** 做為 **SOCKS v5** Proxy。Firefox 的設定如下所示。如果您使用與 9876 不同的連接埠，請將連接埠變更為您所用的連接埠：

	![Firefox 設定的映像](./media/hdinsight-linux-ambari-ssh-tunnel/socks.png)

	> [AZURE.NOTE]選取 [**遠端 DNS**] 會使用 HDInsight 叢集解析網域名稱系統 (DNS) 要求。若未選取，則會在本機解析 DNS。

2. 在 Firefox 中啟用和停用 Proxy 設定的情況下造訪網站，例如 [http://www.whatismyip.com/](http://www.whatismyip.com/)，即可驗證是否透過通道路由傳送流量。在啟用設定時，是使用 Microsoft Azure 資料中心內之機器的 IP 位址。

###瀏覽器延伸模組

當設定瀏覽器使用通道的功能在運作時，您通常不會想透過通道傳送所有流量。[FoxyProxy](http://getfoxyproxy.org/) 等瀏覽器延伸模組支援 URL 要求的模式比對 (僅限 FoxyProxy Standard 或 Plus)，以便只有特定 URL 的要求會透過通道傳送。

如果您已安裝 FoxyProxy Standard，請使用下列步驟將它設定為只透過通道轉送 HDInsight 的流量。

1. 在您的瀏覽器中開啟 FoxyProxy 延伸模組。比方說，在 Firefox 中選取 [位址] 欄位旁的 FoxyProxy 圖示。

	![foxyproxy 圖示](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxy.png)

2. 選取 [**加入新的 Proxy**]、選取 [**一般**] 索引標籤，然後輸入 **HDInsightProxy** 的 Proxy 名稱。

	![foxyproxy 一般](./media/hdinsight-linux-ambari-ssh-tunnel/foxygeneral.png)

3. 選取 [**Proxy 詳細資料**] 索引標籤並填入下列欄位：

	* **主機或 IP 位址** - 這是 localhost，因為我們使用本機電腦上的 SSH 通道。

	* **連接埠** - 這是用於 SSH 通道的連接埠。

	* **SOCKS Proxy** - 選取此選項讓瀏覽器使用通道做為 Proxy。

	* **SOCKS v5** - 選取此選項以設定 Proxy 的要求版本。

	![foxyproxy proxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxyproxy.png)

4. 選取 [**URL 模式**] 索引標籤，然後選取 [**加入新的模式**]。使用下列欄位定義模式，然後按一下 [**確定**]：

	* **模式名稱** - **headnode** - 這是易記的模式名稱。

	* **URL 模式** - ***headnode*** - 這會定義模式來比對任何 URL 中是否有 **headnode** 一字。

	![foxyproxy 模式](./media/hdinsight-linux-ambari-ssh-tunnel/foxypattern.png)

	> [AZURE.NOTE]如果使用 __HBase__ 叢集，您也必須新增下列模式，因為它所主控的 Web UI 在叢集的 Zookeeper 節點上：
	>
	> * __模式名稱__ - __zookeeper__
	> * __URL 模式__ - __*zookeeper*__
	>
	> 如果使用 __Storm__ 叢集，您必須加入下列模式，做為擷取記錄時，背景工作節點的 IP 位址的 Storm UI 連結。我們正在努力做更改，以在未來的更新中使用網域名稱。
	>
	> * __模式名稱__ - __nodesbyip__
	> * __URL 模式__ - __*10.0.0.*__

4. 選取 [**確定**] 以加入 Proxy 並關閉 [**Proxy 設定**]。

5. 在 FoxyProxy 對話方塊頂端，將 [**選取模式**] 變更為 [**根據預先定義的模式和優先順序使用 Proxy**]，然後按一下 [**關閉**]。

	![foxyproxy 選取模式](./media/hdinsight-linux-ambari-ssh-tunnel/selectmode.png)

在執行這些步驟後，只有包含 **headnode** 字串之 URL 的要求會透過 SSL 通道路由傳送。

##驗證 Ambari Web UI

建立叢集後，請使用下列步驟來確認您可以從 Ambari Web 存取服務 Web UI：

1. 在瀏覽器中前往 https://CLUSTERNAME.azurehdinsight.net，其中 CLUSTERNAME 是您 HDInsight 叢集的名稱。

	出現提示時，請輸入您叢集的管理員使用者名稱 (admin) 和密碼。Ambari Web UI 可能會出現第二次的提示。若是如此，請重新輸入資訊。

2. 在 Ambari Web UI 中，選取頁面左邊清單中的 YARN。

	![選取 YARN 的影像](./media/hdinsight-linux-ambari-ssh-tunnel/yarnservice.png)

3. 顯示 YARN 服務資訊時，請選取 [快速連結]。叢集前端節點的清單隨即出現。選取其中一個前端節點，然後選取 [ResourceManager UI]。

	![展開 [快速連結] 功能表的影像](./media/hdinsight-linux-ambari-ssh-tunnel/yarnquicklinks.png)

	> [AZURE.NOTE]如果您的網際網路連線速度較慢，或前端節點非常忙碌，則當您選取 [快速連結] 時，可能會看見等候指標而非功能表。若是如此，請等待一兩分鐘，讓系統從伺服器接收資料，然後再次嘗試列出清單。


	> [AZURE.TIP]如果您的螢幕解析度較低，或瀏覽器視窗沒有最大化，則 [快速連結] 功能表中的某些項目可能會在畫面右邊遭到截斷。若是如此，請使用滑鼠展開功能表，然後使用向右鍵來將畫面捲動到右邊，以便看到功能表的其餘部分。

4. 您應該會看到類似如下的頁面：

	![YARN ResourceManager UI 的影像](./media/hdinsight-linux-ambari-ssh-tunnel/yarnresourcemanager.png)

	> [AZURE.TIP]請注意此頁的 URL，它應該類似於 __http://headnode1.CLUSTERNAME-ssh.j8.internal.cloudapp.net:8088/cluster__。這使用了節點的內部完整網域名稱 (FQDN)，而且必須使用 SSH 通道才能存取。

##後續步驟

既然您已經學會如何建立及使用 SSH 通道，請參閱下列資訊，了解如何使用 Ambari 監視和管理叢集：

* [使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)

如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

<!---HONumber=Oct15_HO3-->