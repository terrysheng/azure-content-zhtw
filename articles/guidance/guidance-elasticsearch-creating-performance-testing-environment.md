<properties
   pageTitle="建立 Elasticsearch 的效能測試環境 | Microsoft Azure"
   description="如何設定環境來測試 Elasticsearch 叢集的效能。"
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# 建立 Elasticsearch on Azure 的效能測試環境

本文是[系列文章的其中一篇](guidance-elasticsearch.md)。

本文說明如何設定環境來測試 Elasticsearch 叢集的效能。此設定用於測試資料擷取和查詢工作負載的效能，如[微調 Elasticsearch on Azure 的資料擷取效能][]所述。

效能測試程序使用 [Apache JMeter](http://jmeter.apache.org/)，並搭配一組以特別針對此用途所設定的專用 VM (非 Elasticsearch 叢集的一部分) 而安裝在主要/從屬組態中的[標準組](http://jmeter-plugins.org/wiki/StandardSet/)外掛程式。

每個 Elasticsearch 節點上安裝 [PerfMon 伺服器代理程式](http://jmeter-plugins.org/wiki/PerfMonAgent/)。下列各節提供指示來重新建立測試環境，讓您使用 JMeter 執行自己的效能測試。這些指示假設您已建立 Elasticsearch 叢集並使用 Azure 虛擬網路連接節點。

請注意，測試環境也以一組使用單一 Azure 資源群組所管理的 Azure VM 來運作。

也安裝並設定 [Marvel](https://www.elastic.co/products/marvel)，以更輕鬆地監視及分析 Elasticsearch 叢集的內部層面。如果 JMeter 統計資料顯示效能出現尖峰或低谷，則 Marvel 提供的資訊對於判斷波動原因可能極有價值。

下圖顯示整個系統的結構。

![elasticsearch-arch](./media/guidance-elasticsearch/performance-structure.png)

請注意下列幾點：

- JMeter 主要 VM 執行 Windows Server 來提供 JMeter 主控台的 GUI 環境。JMeter 主要 VM 提供 GUI ( *jmeter* 應用程式)，讓測試人員建立測試、執行測試，並將結果視覺化。此 VM 與實際傳送要求來構成測試的 JMeter 伺服器 VM 協調運作。

- JMeter 從屬 VM 執行 Ubuntu Server (Linux)，這些 VM 沒有 GUI 需求。JMeter 伺服器 VM 執行 JMeter 伺服器軟體 ( *jmeter-server* 應用程式) 將要求傳送至 Elasticsearch 叢集。

- 雖然使用專用主要節點，但不使用專用用戶端節點。

- 視測試的案例而定，叢集中的資料節點數目會有所不同。

- Elasticsearch 叢集中所有節點都執行敬 Marvel 來察執行階段的效能，並執行 JMeter 伺服器代理程式來收集監視資料供稍後分析。

- 當測試 Elasticsearch 2.0.0 和更新版本時，其中一個資料節點也會執行 Kibana，這是 Elasticsearch 2.0.0 和更新版本上執行的 Marvel 版本所需要的。

## 建立虛擬機器的 Azure 資源群組

JMeter 主要電腦必須直接連接到 Elasticsearch 叢集的每個節點，才能收集效能資料。如果 JMeter VNet 不同於 Elasticsearch 叢集 VNet，則這需要使用公用 IP 位址設定每個 Elasticsearch 節點。如果這對於 Elasticsearch 組態造成問題，請考慮在 Elasticsearch 叢集所在的相同 VNet 中使用相同的資源群組實作 JMeter VM，在此情況下，您可以省略這第一個程序。

首先，[建立資源群組](../articles/resource-group-portal/#create-resource-group-and-resources)。本文件假設您的資源群組名稱為 *JMeterPerformanceTest*。如果您想要在 Elasticsearch 叢集所在的相同 VNet 中執行 JMeter VM，請使用與該叢集相同的資源群組，而不要建立新的資源群組。

## 建立 JMeter 主要虛擬機器

接下來，使用 *Windows Server 2008 R2 SP1* 映像[建立 Windows VM](../articles/virtual-machines-windows-tutorial/)。建議您選取具有足夠的核心和記憶體來執行效能測試的 VM 大小。最好是至少有 2 個核心及 3.5GB RAM (A2 標準或更大) 的電腦。

<!-- TODO add info on why disabling diagnostics is positive -->

我們建議您停用診斷。在入口網站中建立 VM 時，這是在 [設定] 刀鋒視窗的 [診斷] 底下的 [監視] 區段中指定。將其他設定保留其預設值。

在入口網站中[檢查資源群組](../articles/resource-group-portal/#browse-resource-groups)，確認已成功建立 VM 和所有相關聯的資源。列出的資源應該包含全部都是相同名稱的 VM、網路安全性群組和公用 IP 位址，還有根據該 VM 的名稱而命名的網路介面和儲存體帳戶。

## 建立 JMeter 從屬虛擬機器

現在使用 *Ubuntu Server 14.04 LTS* 映像[建立 Linux VM](../articles/virtual-machines-linux-tutorial-portal-rm/)。如同 JMeter 主要 VM 一樣，請選取具有足夠的核心和記憶體來執行效能測試的 VM 大小。最好是至少有 2 個核心及至少 3.5GB RAM (標準 A2 或更大) 的電腦。

同樣地，我們建議您停用診斷。

您可以依照所需建立任意數量的從屬 VM。

## 在 JMeter 從屬 VM 上安裝 JMeter 伺服器

JMeter 從屬 VM 執行 Linux，根據預設，您無法藉由開啟遠端桌面連線 (RDP) 來連接它們。相反地，您可以每個 VM 上[使用 PuTTY 來開啟命令列視窗](../articles/virtual-machines-linux-how-to-log-on/)。

一旦連線到其中一個附屬 VM 之後，我們將使用 bash 來設定 JMeter。

首先，安裝執行 JMeter 所需的 Java 執行階段環境。

```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

現在，下載封裝為 zip 檔案的 JMeter 軟體。

```bash
wget http://apache.mirror.anlx.net/jmeter/binaries/apache-jmeter-2.13.zip
```

安裝 unzip 命令，然後用它來解壓縮 JMeter 軟體。軟體會複製到名為 **apache-jmeter-2.13** 的資料夾。

```bash
sudo apt-get install unzip
unzip apache-jmeter-2.13.zip
```

切換至含有 JMeter 可執行檔的 *bin* 目錄，將 *jmeter-server* 和 *jmeter* 程式設為可執行。

```bash
cd apache-jmeter-2.13/bin
chmod u+x jmeter-server
chmod u+x jmeter
```

現在，我們需要編輯位於目前資料夾中的檔案 `jmeter.properties` (使用您最熟悉的文字編輯器，例如 *vi* 或 *vim*)。找出下列幾行：

```yaml
...
client.rmi.localport=0
...
server.rmi.localport=4000
...
```

取消註解 (移除開頭的 ## 字元) 並修改這幾行，如下所示，然後儲存檔案並關閉編輯器：

```yaml
...
client.rmi.localport=4441
...
server.rmi.localport=4440
```

現在，執行下列命令來開啟連接埠 4441 給傳入 TCP 流量 (這是您剛設定 *jmeter-server* 來接聽的連接埠)：

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4441 -j ACCEPT
```

下載包含 JMeter 外掛程式標準集合的 zip 檔案 (這些外掛程式提供效能監視計數器)，然後將檔案解壓縮。在此位置中解壓縮檔案會將外掛程式放在正確的資料夾。

如果提示您取代 LICENSE 檔案，請鍵入 A (表示全部)：

```bash
wget http://jmeter-plugins.org/downloads/file/JMeterPlugins-Standard-1.3.0.zip
unzip JMeterPlugins-Standard-1.3.0.zip
```

使用 `nohup` 在背景中啟動 JMeter 伺服器。它回應時應該會顯示處理序識別碼和訊息，指出它已建立遠端物件，並且已準備好開始接收命令。

```bash
nohup bin/jmeter-server &
```

> [AZURE.NOTE] 如果關閉 VM，則會終止 JMeter 伺服器程式。您將需要連接到 VM，並手動重新啟動它。或者，您也可以將下列命令加入至 `/etc/rc.local` 檔案 (在 *exit 0* 命令之前)，以設定系統在啟動時自動執行 *jmeter-server* 命令：

```bash
sudo -u <username> bash << eoc
cd /home/<username>/apache-jmeter-2.13/bin
nohup ./jmeter-server &
eoc
```

將 `<username>` 替換為您的登入名稱。

將終端機視窗保持開啟會很有用，這可讓您在測試進行時監視 JMeter 伺服器的進度。

您必須為每個 JMeter 從屬 VM 重複這些步驟。

## 在 Elasticsearch 節點上安裝 JMeter 伺服器代理程式

此程序假設您有權限登入 Elasticsearch 節點。如果您已使用 ARM 範本建立叢集，您可以透過跳躍箱 VM 連接到每個節點，如 [Azure 生產環境拓撲](guidance-elasticsearch-running-on-azure.md#elasticsearch-topologies)一節所示。您也可以使用 PuTTY 連接至跳躍箱。

在這裡，您可以使用 *ssh* 命令登入 Elasticsearch 叢集中的每個節點。

以系統管理員身分登入其中一個 Elasticsearch 節點。在 bash 命令提示字元中，輸入下列命令來建立存放 JMeter 伺服器代理程式的資料夾，並移至該資料夾：

```bash
mkdir server-agent
cd server-agent
```

執行下列命令來安裝 *unzip* 命令 (如果尚未安裝的話)，下載 JMeter 伺服器代理程式軟體並將它解壓縮：

```bash
sudo apt-get install unzip
wget http://jmeter-plugins.org/downloads/file/ServerAgent-2.2.1.zip
unzip ServerAgent-2.2.1.zip
```
 
執行下列命令來設定防火牆，並允許 TCP 流量通過連接埠 4444 (這是 JMeter 伺服器代理程式所使用的連接埠)：

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
```

執行下列命令在背景中啟動 JMeter 伺服器代理程式：

```bash
nohup ./startAgent.sh &
```

JMeter 伺服器代理程式應該會以訊息回應，指出它已啟動且正在接聽連接埠 4444。按下 Enter 以取得命令提示字元，然後執行下列命令。將 `<nodename>` 替換為您的節點名稱。如果不確定您的節點名稱，您可以執行 `hostname` 命令找到它：

```bash
telnet <nodename> 4444
```

此命令會開啟 telnet 連接來連到本機電腦上的連接埠 4444。您可以使用這個連接來驗證 JMeter 伺服器代理程式正確執行。

如果 JMeter 伺服器代理程式未執行，您會收到回應

`*telnet: Unable to connect to remote host: Connection refused*.`

如果 JMeter 伺服器代理程式正在執行，且已正確設定連接埠 4444，您應該會看到下列回應：

![](./media/guidance-elasticsearch/performance-telnet-server.png)

> [AZURE.NOTE] telnet 工作階段連線之後不會提供任何一種提示字元。

在 telnet 工作階段中，輸入下列命令：

``` 
test
```

如果 JMeter 伺服器代理程式已設定且正確接聽，它應該會指出已接到命令並回應訊息 *Yep*。

> [AZURE.NOTE] 您可以輸入其他命令，以取得效能監視資料。例如，命令 `metric-single:cpu:idle` 會提供目前 CPU 閒置的時間比例 (這是快照集)。如需完整的命令清單，請瀏覽 [PerfMon 伺服器代理程式](http://jmeter-plugins.org/wiki/PerfMonAgent/)頁面。

在 telnet 工作階段中，輸入下列命令來結束工作階段並返回 bash 命令提示字元：

``` 
exit
```

> [AZURE.NOTE] 如同 JMeter 從屬 VM 一樣，如果您登出，或如果這部電腦已關閉並重新啟動，則必須使用 `startAgent.sh` 命令來手動重新啟動 JMeter 伺服器代理程式。如果您想要讓 JMeter 伺服器代理程式自動啟動，請將下列命令加入至 `/etc/rc.local` 檔案的結尾 (在 *exit 0* 命令之前)。將 `<username>` 替換為您的登入名稱：

```bash
sudo -u <username> bash << eoc
cd /home/<username>/server-agent
nohup ./startAgent.sh &
eoc
```

將 `<username>` 替換為您的登入名稱。

您現在可以對 Elasticsearch 叢集中的每個其他節點重複這整個程序，或者，您可以使用 `scp` 命令，將 server-agent 資料夾和內容複製到每個其他節點，並使用 `ssh` 命令啟動 JMeter 伺服器代理程式，如下所示。將 `<username>` 替換為您的使用者名稱，並將 `<nodename>` 替換為您想要複製並執行軟體的節點名稱 (執行每個命令時，可能會要求您提供密碼)：

```bash
scp -r ~/server-agent <username>@<nodename>:~
ssh <nodename> sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
ssh <nodename> -n -f 'nohup ~/server-agent/startAgent.sh'
```

## 在 JMeter 主要 VM 上安裝和設定 JMeter

在 Azure 入口網站中，按一下 [資源群組]。在 [資源群組] 刀鋒視窗中，按一下包含 JMeter 主要和次要 VM 的資源群組。在 [資源群組] 刀鋒視窗中，按一下 JMeter 主要 VM。在 [虛擬機器] 刀鋒視窗中，按一下 [連接]。網頁瀏覽器出現提示時，開啟 RDP 檔案。Windows 會對 VM 建立遠端桌面連線。出現提示時，輸入 VM 的使用者名稱和密碼。

在 VM 中，使用 Internet Explorer 移至[下載 Windows 版 Java](http://www.java.com/en/download/ie_manual.jsp) 頁面。依照指示下載並執行 Java 安裝程式。

在網頁瀏覽器中，瀏覽至[下載 Apache JMeter](http://jmeter.apache.org/download_jmeter.cgi) 頁面，並下載包含最新二進位檔的 zip。將 zip 儲存在 VM 上方便存取的位置。

移至[自訂 JMeter 外掛程式](http://jmeter-plugins.org/)網站並下載外掛程式標準組。將 zip 儲存在上一個步驟下載的 JMeter 所在的相同資料夾。

在 Windows 檔案總管中，移至包含 apache-jmeter-*xx* zip 檔案的資料夾 (其中 *xx* 是 JMeter 的目前版本)，然後將檔案解壓縮到目前的資料夾 (zip 會建立一個稱為 apache-jmeter-*xxx* 的子資料夾)。

將 JMeterPlugins-Standard-*yyy*.zip 檔案 (其中 *yyy* 是外掛程式的目前版本) 中的檔案解壓縮到 apache-jmeter-*xxx* 資料夾。這會將外掛程式新增至 JMeter 的正確資料夾 (如果出現提示，您可以安心地合併 lib 資料夾，並覆寫授權和讀我檔案)。

移至 apache-jmeter-*xxx*/bin 資料夾，使用 [記事本] 編輯 jmeter.properties 檔案。在 `jmeter.properties` 檔案中，找出標示為 *Remote hosts and RMI configuration* 的區段。在檔案的這個區段中，找出下列這一行：

```yaml
remote_hosts=127.0.0.1
```

變更此行，並將 IP 位址 127.0.0.1 替換為每個 JMeter 從屬伺服器的 IP 位址或主機名稱的逗號分隔清單。例如：

```yaml
remote_hosts=JMeterSub1,JMeterSub2
```

找到下列這一行，然後移除這一行開頭的 `#` 字元，並修改 client.rmi.localport 設定的值：

```yaml
#client.rmi.localport=0
```

to:

```yaml
client.rmi.localport=4440
```

儲存檔案並關閉 [記事本]。

在 Windows 工具列，依序按一下 [開始]、[系統管理工具]] 和 [具有進階安全性的 Windows 防火牆]。在 [具有進階安全性的 Windows 防火牆] 視窗的左窗格中，以滑鼠右鍵按一下 [輸入規則]，然後按一下 [新增規則]。

在 [新增輸入規則精靈] 的 [規則類型] 頁面上，選取 [連接埠]，然後按 [下一步]。在 [通訊協定及連接埠] 頁面上，選取 [TCP]，選取 [特定本機連接埠]，在文字方塊中輸入 `4440-4444`，然後 [下一步]。在 [執行動作] 頁面上，選取 [允許連線]，然後按 [下一步]。在 [設定檔] 頁面上，維持核取所有選項，然後按 [下一步]。在 [名稱] 頁面的 [名稱] 文字方塊中輸入 *JMeter*，然後按一下 [完成]。關閉 [具有進階安全性的 Windows 防火牆] 視窗。

在 Windows 檔案總管中，在 apache-jmeter-*xx*/bin 資料夾中按兩下 *jmeter* Windows 批次檔來啟動 GUI。應該會出現使用者介面：

![](./media/guidance-elasticsearch/performance-image17.png)

在功能表列中，按一下 [執行]，按一下 [遠端啟動]，並確認已列出兩台 JMeter 從屬電腦：

![](./media/guidance-elasticsearch/performance-image18.png)

您現在已準備好開始執行效能測試。

## 安裝並設定 Marvel

如果您在建置叢集時將 MARVEL 和 KIBANA 參數設為 true，適用於 Azure 的 Elasticsearch 快速入門範本會自動安裝並設定適當版本的 Marvel：

![](./media/guidance-elasticsearch/performance-image19.png)

如果您要將 Marvel 加入至現有的叢集，您必須手動執行安裝，但程序會根據您使用 Elasticsearch 1.7.x 版還是 2.x 版而不同，如下列程序所述。

### 搭配 Elasticsearch 1.73 或更早版本安裝 Marvel

如果您使用 Elasticsearch 1.7.3 或更早版本，請在叢集的*每個節點上*執行下列步驟：

- 登入節點並移至 Elasticsearch 主目錄。在 Linux 上，一般的主目錄是 `/usr/share/elasticsearch`。

-  執行下列命令來下載並安裝適用於 Elasticsearch 的 Marvel 外掛程式：

```bash
sudo bin/plugin -i elasticsearch/marvel/latest
```

- 在節點上停止並重新啟動 Elasticsearch：

```bash
sudo service elasticsearch restart
```

- 若要確認已正確安裝 Marvel，請開啟網頁瀏覽器並移至 URL `http://<server>:9200/_plugin/marvel`。將 `<server>` 替換為叢集中任何 Elasticsearch 伺服器的名稱或 IP 位址。確認出現類似如下的頁面：

![](./media/guidance-elasticsearch/performance-image20.png)


### 搭配 Elasticsearch 2.0.0 或更新版本安裝 Marvel

如果您使用 Elasticsearch 2.0.0 或更新版本，請在叢集的*每個節點上*執行下列工作：

登入節點並移至 Elasticsearch 主目錄 (通常是 `/usr/share/elasticsearch`)。執行下列命令來下載並安裝適用於 Elasticsearch 的 Marvel 外掛程式：

```bash
sudo bin/plugin install license
sudo bin/plugin install marvel-agent
```

在節點上停止並重新啟動 Elasticsearch：

```bash
sudo service elasticsearch restart
```

在下列程序中，如果您使用 Elasticsearch 2.0.0 或 Elasticsearch 2.0.1，請將 `<kibana-version>` 替換為 4.2.2，或者，如果您使用 Elasticsearch 2.1.0 或更新版本，請替換為 4.3.1。如果您使用 Elasticsearch 2.0.0 或 Elasticsearch 2.0.1，請將 `<marvel-version>` 替換為 2.0.0，或者，如果您使用 Elasticsearch 2.1.0 或更新版本，請替換為 2.1.0。在叢集的*一個節點上*執行下列工作：

登入節點，並從 [Elasticsearch 下載網站](https://www.elastic.co/downloads/past-releases)下載適合您的 Elasticsearch 版本的 Kibana 組建，然後將套件解壓縮：

```bash
wget https://download.elastic.co/kibana/kibana/kibana-<kibana-version>-linux-x64.tar.gz
tar xvzf kibana-<kibana-version>-linux-x64.tar.gz
```

開啟連接埠 5601 來接受連入要求：

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 5601 -j ACCEPT
```

移至 kibana config 資料夾 (`kibana-<kibana-version>-linux-x64/config`)，編輯 `kibana.yml` 檔案，並加入下列這一行。將 `<server>` 替換為叢集中的 Elasticsearch 伺服器的名稱或 IP 位址。

```yaml
elasticsearch.url: "http://<server>:9200"
```

移至 kibana bin 資料夾 (`kibana-<kibana-version>-linux-x64/bin`)，然後執行下列命令將 Marvel 外掛程式整合到 Kibana：

```bash
sudo ./kibana plugin --install elasticsearch/marvel/<marvel-version>
```

啟動 Kibana：

```bash
sudo nohup ./kibana &
```

若要確認 Marvel 安裝，請開啟網頁瀏覽器並移至 URL `http://<server>:5601/app/marvel`。將 `<server>` 替換為執行 Kibana 的伺服器的名稱或 IP 位址。

確認出現類似如下的頁面 (您的叢集名稱與圖中顯示的名稱可能不同)。

![](./media/guidance-elasticsearch/performance-image21.png)

按一下對應至您的叢集的連結 (上圖中的 elasticsearch210)。應該會出現類似如下的頁面：

![](./media/guidance-elasticsearch/performance-image22.png)

[微調 Elasticsearch on Azure 的資料擷取效能]: guidance-elasticsearch-tuning-data-ingestion-performance.md

<!---HONumber=AcomDC_0224_2016-->