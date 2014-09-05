<properties linkid="services-linux-cassandra-with-linux" urlDisplayName="Cassandra with Linux" pageTitle="Run Cassandra with Linux on Azure" metaKeywords="" description="Explains how to run a Cassandra cluster on Linux in Azure Virtual Machines." metaCanonical="" services="virtual-machines" documentationCenter="Node.js" title="Running Cassandra with Linux on Azure and Accessing it from Node.js" authors="" solutions="" manager="" editor="" />

在 Azure 上執行 Cassandra 搭配 Linux 並透過 Node.js 進行存取
============================================================

**作者：** Hanu Kommalapati

目錄
----

-   [概觀](#overview)
-   [Cassandra 部署圖解](#schematic)
-   [複合式部署](#composite)
-   [Azure 虛擬機器部署](#deployment)
-   [工作 1：部署 Linux 叢集](#task1)
-   [工作 2：在每部虛擬機器上安裝 Cassandra](#task2)
-   [工作 3：透過 Node.js 存取 Cassandra 叢集](#task3)
-   [結論](#conclusion)

概觀
----

Azure 透過可允許以無結構描述方式儲存商務物件的 Azure 資料表儲存體提供了 NoSQL 資料庫服務。若要使用這項服務，可以透過 Node.JS、.NET、Java 及任何其他可以支援 HTTP 和 REST 的語言。不過，還有其他常用的 NoSQL 資料庫 (例如 Cassandra 和 Couchbase)，這些資料庫因為其無狀態雲端服務模型而無法在 Azure PaaS 上執行。Azure 虛擬機器現在則是允許在不需對程式碼基底做任何變更的情況下，在 Azure 上執行這些 NoSQL 資料庫。本文的用意是要示範如何在虛擬機器上執行 Cassandra 叢集，並透過 Node.js 來存取它。這並未涵蓋用於真實世界生產操作的 Cassandra 部署，這類操作需要顧及多重資料中心 Cassandra 叢集的相關備份和復原策略。在本練習中，我們將使用 Ubuntu 12.04 版 Linux 和 Cassandra 1.0.10；不過，程序可能因 Linux 散發套件而異。

Cassandra 部署圖解
------------------

Azure 虛擬機器功能使得在 Microsoft 公用雲端執行 NoSQL 資料庫 (例如 [Cassandra](http://wiki.apache.org/cassandra/)) 與在私人雲端環境執行這類資料庫一樣容易，唯一的差異在於 Azure 虛擬機器基礎結構特定的虛擬機器組態。撰寫本文時，Cassandra 還無法在 Azure 上以受管理的服務形式提供使用，因此在本文中，我們將著眼於在虛擬機器上設定 Cassandra 叢集，並從另一個也裝載於虛擬機器內的 Linux 執行個體存取此叢集。本文所示的 node.js 程式碼片段也可以從 PaaS 裝載的 Web 應用程式或 Web 服務來使用。Azure 的其中一個核心力量就是可允許能夠充分利用 PaaS 和 IaaS 世界的複合式應用程式模型。

Cassandra 應用程式環境有兩個可行的部署模型：獨立式 (Self-Contained) 虛擬機器部署和複合式部署。在複合式部署中，將會使用 Thrift 介面透過負載平衡器，從 PaaS 裝載的 Azure Web 應用程式 (或 Web 服務) 取用虛擬機器裝載的 Cassandra 叢集。雖然在發生主要空間 (key space) 錯誤時，每個 Cassandra 節點都會透過 Proxy 將要求傳遞給其他對等節點，但是負載平衡器能夠協助提供基本的要求負載平衡。此外，負載平衡器也會建立受防火牆保護的沙箱來提供更好的資料控制。

複合式部署
----------

複合式部署的目標是一方面要將 PaaS 的使用量最大化，一方面又要讓虛擬機器的磁碟使用量保持在絕對的最小值，為的是要避免由虛擬機器的基礎結構管理所加諸的額外負荷。考量到伺服器管理所帶來的額外負荷，因此請只部署那些需要可設定狀態之行為且因各種原因 (包括上市時間、無法查看原始程式碼及存取作業系統的層級低) 而無法輕易修改的元件。

![Composite deployment diagram](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux1.png)

Azure 虛擬機器部署
------------------

![Virtual machine deployment](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux2.png)

上圖中是將一個 4 節點的 Cassandra 叢集部署在虛擬機器內可允許 Thrift 流量的負載平衡器後方。Azure 裝載的 PaaS 應用程式會使用語言特定 Thrift 程式庫來存取叢集。有適用於各種語言 (包括 Java、C\#、Node.js、Python 及 C++) 的程式庫。第二個圖中顯示的獨立式虛擬機器部署會透過在虛擬機器上裝載的另一個雲端服務內執行的應用程式取用資料。

工作 1：部署 Linux 叢集
-----------------------

在虛擬機器預覽版本期間，為了讓 Linux VM 能夠成為相同虛擬網路的一部分，必須將所有機器部署至相同的雲端服務。一般建立叢集的順序如下：

![Sequence diagram for creating a cluster](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux4.png)

**步驟 1：產生 SSH 金鑰組**

在佈建階段，Azure 需要 PEM 或 DER 編碼的 X509 公用金鑰。請參考[如何在 Azure 上使用 SSH 搭配 Linux](http://www.windowsazure.com/zh-tw/manage/linux/how-to-guides/ssh-into-linux/) (英文) 上的指示來產生公用/私密金鑰組。如果您打算在 Windows 或 Linux 上使用 putty.exe 做為 SSH 用戶端，就必須使用 puttygen.exe 將 PEM 編碼的 RSA 私密金鑰轉換成 PPK 格式。如需有關這項作業的指示，請參閱[為 Windows Azure 上的 Linux VM 部署產生 SSH 金鑰組](http://blogs.msdn.com/b/hanuk/archive/2012/06/07/generating-ssh-key-pair-for-linux-vm-deployment-on-windows-azure.aspx) (英文)。

**步驟 2：建立 Ubuntu VM**

若要建立第一部 Ubuntu VM，請登入 Azure 預覽入口網站，依序按一下 **[新增]**、**[虛擬機器]**、**[From Gallery]**、**[Unbuntu Server 12.xx]**，然後再按向右箭號。如需說明如何建立 Linux VM 的教學課程，請參閱[建立執行 Linux 的虛擬機器](http://www.windowsazure.com/zh-tw/manage/linux/tutorials/virtual-machine-from-gallery/) (英文)。

<table data-morhtml="true">
	<tr data-morhtml="true">
		<th data-morhtml="true">欄位名稱</th>
		<th data-morhtml="true">欄位值</th>
		<th data-morhtml="true">備註</th>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">虛擬機器名稱</td>
		<td data-morhtml="true">hk-cas1</td>
		<td data-morhtml="true">這是 VM 的主機名稱</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">新使用者名稱</td>
		<td data-morhtml="true">localadmin</td>
		<td data-morhtml="true">&quot;admin&quot; 在 Ubuntu 12.xx 中是保留的使用者名稱</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">新密碼</td>
		<td data-morhtml="true"><i data-morhtml="true">強式密碼</i></td>
		<td data-morhtml="true"></td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">確認密碼</td>
		<td data-morhtml="true"><i data-morhtml="true">強式密碼</i></td>
		<td data-morhtml="true"></td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">大小</td>
		<td data-morhtml="true">小型</td>
		<td data-morhtml="true">根據 IO 需求選取 VM。 </td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Secure using SSH Key for Authentication</td>
		<td data-morhtml="true">按一下核取方塊</td>
		<td data-morhtml="true">如果您想要以 SSH 金鑰確保安全，請核取此方塊</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">憑證</td>
		<td data-morhtml="true"><i data-morhtml="true">公用金鑰憑證的檔案名稱</i></td>
		<td data-morhtml="true">以 OpenSSL 或其他工具產生的 DER 或 PEM 編碼 SSH 公用金鑰</td>
	</tr>
</table>

在 [VM 模式] 畫面中，輸入下列資訊：

<table data-morhtml="true">
	<tr data-morhtml="true">
		<th data-morhtml="true">欄位名稱</th>
		<th data-morhtml="true">欄位值</th>
		<th data-morhtml="true">備註</th>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">獨立虛擬 VM</td>
		<td data-morhtml="true">「核取」選項按鈕方塊</td>
		<td data-morhtml="true">這是針對第一部 VM，對於後續 VM，我們將使用 [Connect to Existing VM] 選項</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">DNS 名稱</td>
		<td data-morhtml="true"><i data-morhtml="true">唯一名稱</i>.cloudapp.net</td>
		<td data-morhtml="true">提供一個機器中立的負載平衡器名稱</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">儲存體帳戶</td>
		<td data-morhtml="true"><i data-morhtml="true">預設儲存體帳戶</i></td>
		<td data-morhtml="true">使用您建立的預設儲存體帳戶</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">地區/同質群組/虛擬機器</td>
		<td data-morhtml="true">美國西部</td>
		<td data-morhtml="true">選取您 Web 應用程式存取 Cassandra 叢集時的來源地區</td>
	</tr>
</table>

針對所有將加入 Cassandra 叢集的虛擬機器，重複執行上述程序。此時，所有機器都將成為相同網路的一部分，而可以互相 Ping 到對方。如果 Ping 沒有作用，請檢查 VM 的防火牆 (例如 iptables) 組態以確定允許 ICMP。順利測試完網路連線之後，請務必停用 ICMP 以減少攻擊媒介。

**步驟 3：加入負載平衡 Thrift 端點**

在執行完步驟 1 和步驟 2 之後，每部 VM 應該都已經定義 SSH 端點。現在，讓我們來加入使用公用連接埠 9160 的負載平衡 Thrift 端點。順序如下：

a. 從第一部 VM 的詳細資料檢視，按一下 [加入端點]

b. 在 [Add endpoint to virtual machine] 畫面上，選取 [加入端點] 選項按鈕

c. 按一下向右箭號

d. 在 [Specify endpoint details] 畫面上，輸入下列資訊：

<table data-morhtml="true">
	<tr data-morhtml="true">
		<th data-morhtml="true">欄位名稱</th>
		<th data-morhtml="true">欄位值</th>
		<th data-morhtml="true">備註</th>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">名稱</td>
		<td data-morhtml="true">cassandra</td>
		<td data-morhtml="true">任何唯一端點名稱都適用</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">通訊協定</td>
		<td data-morhtml="true">TCP</td>
		<td data-morhtml="true"></td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">公用連接埠</td>
		<td data-morhtml="true">9160</td>
		<td data-morhtml="true">預設 Thrift 連接埠。 </td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">私人連接埠</td>
		<td data-morhtml="true">9160</td>
		<td data-morhtml="true">除非您已在 cassandra.yaml 中變更此連接埠</td>
	</tr>
</table>

完成上述工作之後，第一部 VM 將會顯示 cassandra 端點且其 [負載平衡] 欄位為 [否]。目前可以忽略這個欄位值，因為在我們將此端點加入至後續的 VM 之後，這個欄位值就會變成 [是]。

e. 現在，請選取第二部 VM，然後重複執行上述程序但必須略做修改來加入端點，這些修改就是您將選取 [Load-balance traffic on an existing endpoint] 並從下拉式清單方塊選取 [cassandra-960]。在這個階段，與兩部 VM 都對應的端點將會變更狀態，從 [負載平衡] 狀態 [否] 變成 [是]。

針對叢集中後續的節點，重複執行步驟 "e"。

既然我們已經備妥 VM，現在即可在每部 VM 上安裝 Cassandra。由於 Cassandra 在許多 Linux 散發套件中都不是標準組件，因此讓我們來採取手動部署程序。

[請注意，我們在每部 VM 上都是使用手動方式進行軟體安裝。不過，您可以加速這個程序，方法是安裝一部充分運作的 Cassandra VM，擷取它做為基礎映像，然後從此基礎映像建立其他執行個體。如需有關擷取 Linux 映像的指示，請參閱[如何擷取執行 Linux 之虛擬機器的映像](https://www.windowsazure.com/en-us/manage/linux/how-to-guides/capture-an-image/) (英文)。]

工作 2：在每部虛擬機器上安裝 Cassandra
--------------------------------------

**步驟 1：安裝必要元件**

Cassandra 需要 Java 虛擬機器，因此請使用下列適用於 Debian 衍生版本 (包括 Ubuntu) 的命令來安裝最新版的 JRE：

    sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java7-installer

**步驟 2：Cassandra 安裝**

1.  使用 SSH 登入 Linux (Ubuntu) VM 執行個體。

2.  使用 wget 從 (http://cassandra.apache.org/download/)[http://cassandra.apache.org/download/] 建議的鏡像網站，將 Cassandra 位元 apache-cassandra-bin.tar.gz 下載至 "\~/downloads" 目錄。請注意，下載的檔案中不會包含版本號碼，以便確保該發行集保持版本中立。

3.  執行下列命令將 tar ball 檔案解壓縮至預設登入目錄：

        tar -zxvf downloads/apache-cassandra-bin.tar.gz

    上述程式碼會將封存展開至 apache-cassandra- [版本] 目錄。

4.  建立下列兩個預設目錄來存放記錄檔和資料：

         $ sudo chown -R /var/lib/cassandra
        $ sudo chown -R /var/log/cassandra

5.  將寫入權限授予將執行 Cassandra 的使用者身分識別

        a.   sudo chown -R <user>:<group> /var/lib/cassandra
        b.  sudo chown -R <user>:<group> /var/log/cassandra
        To use current user context, replace the <user> and <group> with $USER and $GROUP

6.  使用下列命令從 apache-cassandra-[版本]/bin 目錄啟動 Cassandra：

        $ ./cassandra

上述程式碼會啟動 Cassandra 做為背景處理程序。使用 -cassandra "f" 則可以前景模式啟動該處理程序。

記錄檔應該會顯示 mx4j 錯誤。在沒有 mx4j 的情況下，Cassandra 仍可正常運作，但是若要管理 Cassandra 安裝，就必須有 mx4j。請先刪除 Cassandra 處理程序，再進行下一步。

**步驟 3：安裝 mx4j**

    a)   Download mx4j:wget [http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download](http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download) -O mx4j.tar.gz
    b)  tar -zxvf mx4j.tar.gz
    c)  cp mx4j-23.0.2/lib/*.jar ~/apache-cassandra-<version>/lib
    d)  rm -rf mx4j-23.0.2
    e)  rm mx4j.tar.gz

在這個階段重新啟動 Cassandra 處理程序

**步驟 4：測試 Cassandra 安裝**

從 Cassandra 的 bin 目錄執行下列命令以使用 Thrift 用戶端進行連線：

    cassandra-cli -h localhost -p 9160

**步驟 5：啟用 Cassandra 的外部連線功能**

根據預設，Cassandra 僅設定成使用回路位址進行接聽，因此若要用於外部連線，必須進行這項變更。

請編輯 "conf/cassandra.yaml"，將 **listen\_address** 和 **rpc\_address** 變更為伺服器的 IP 位址或主機名稱，以便讓其他節點及外部負載平衡器都能看見目前的節點。

請針對叢集中的所有節點，重複執行步驟 1 到 5。

既然所有個別的 VM 都已備妥必要的軟體，現在即可透過種子組態在節點之間建立通訊。如需有關多節點叢集組態的詳細資料，請檢閱位於 <http://wiki.apache.org/cassandra/MultinodeCluster> (英文) 的資訊。

**步驟 6：設定多節點叢集**

編輯 cassandra.yaml 以變更所有 VM 中的下列屬性：

**a) cluster\_name**

預設叢集名稱是設定為 "Test Cluster"；請將它變更為反映您應用程式的名稱。範例："AppStore"。如果您在安裝期間已經以 "Test Cluster" 啟動叢集來進行測試，您將會收到叢集名稱不相符錯誤。若要修正這個錯誤，請刪除 /var/lib/cassandra/data/system 目錄中的所有檔案。

**b) 種子**

新節點將使用這裡指定的 IP 位址來了解環狀拓撲。請設定最可靠的節點做為您的種子，使用逗號分隔的格式："*host1*,*host2*"。範例設定："hk-ub1,hk-ub2"。

我們將接受種子伺服器所提供的預設權杖，因為這不是本練習的焦點所在。若要產生最佳權杖，請參閱位於下列網址的 Python 指令碼：<http://wiki.apache.org/cassandra/GettingStarted>。

請重新啟動所有節點上的 Cassandra 以套用上述變更。

**步驟 7：測試多節點叢集**

安裝至 Cassandra bin 目錄的 nodetool 將可協助叢集操作。我們將使用 nodetool 透過下列命令格式確認叢集設定：

    $ bin/nodetool -h <hostname> -p 7199 ring

如果組態正確，針對 3 節點叢集，它將顯示如以下所示的資訊：

<table data-morhtml="true">
	<tr data-morhtml="true">
		<td data-morhtml="true">位址</td>
		<td data-morhtml="true">DC</td>
		<td data-morhtml="true">機架</td>
		<td data-morhtml="true">Status</td>
		<td data-morhtml="true">狀況</td>
		<td data-morhtml="true">負載</td>
		<td data-morhtml="true">擁有</td>
		<td data-morhtml="true">權杖</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true"></td>
		<td data-morhtml="true"></td>	
		<td data-morhtml="true"></td>	
		<td data-morhtml="true"></td>	
		<td data-morhtml="true"></td>	
		<td data-morhtml="true"></td>	
		<td data-morhtml="true"></td>	
		<td data-morhtml="true">149463697837832744402916220269706844972</td>	
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">10.26.196.68</td>
		<td data-morhtml="true">datacenter1</td>	
		<td data-morhtml="true">rack1</td>	
		<td data-morhtml="true">已啟動</td>	
		<td data-morhtml="true">正常</td>	
		<td data-morhtml="true">15.69 KB</td>	
		<td data-morhtml="true">25.98%</td>	
		<td data-morhtml="true">114445918355431753244435008039926455424</td>	
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">10.26.198.81</td>
		<td data-morhtml="true">datacenter1</td>	
		<td data-morhtml="true">rack1</td>	
		<td data-morhtml="true">已啟動</td>	
		<td data-morhtml="true">正常</td>	
		<td data-morhtml="true">15.69 KB</td>	
		<td data-morhtml="true">53.44%</td>	
		<td data-morhtml="true">70239176883275351288292106998553981501</td>	
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">10.26.198.84</td>
		<td data-morhtml="true">datacenter1</td>	
		<td data-morhtml="true">rack1</td>	
		<td data-morhtml="true">已啟動</td>	
		<td data-morhtml="true">正常</td>	
		<td data-morhtml="true">18.35 KB</td>	
		<td data-morhtml="true">25.98%</td>	
		<td data-morhtml="true">149463697837832744402916220269706844972</td>	
	</tr>
</table>

在這個階段，叢集已經為準備就緒，可供 Thrift 用戶端透過在「部署 Linux 叢集」工作期間所建立的雲端服務 URL (建立第一部 VM 時所提供的 DNS 名稱) 進行存取。

工作 3：透過 Node.js 存取 Cassandra 叢集
----------------------------------------

請使用先前工作中所述的程序在 Azure 上建立 Linux VM。請確定此 VM 是獨立 VM，因為我們將使用它做為用戶端來存取 Cassandra 叢集。我們將先從 GitHub 安裝 Node.js、NPM 及 [cassandra-client](https://github.com/racker/node-cassandra-client)，再從此 VM 連線至 Cassandra 叢集：

**步驟 1：安裝 Node.js 和 NPM**

a) 安裝必要元件

    sudo apt-get install g++ libssl-dev apache2-utils make

b) 我們將使用來自 GitHub 的來源來進行編譯和安裝；為了能夠複製儲存機制，必須先安裝 Git 核心執行階段：

    sudo apt-get install git-core

c) 複製節點儲存機制

    git clone git://github.com/joyent/node.git

d) 上述程式碼會建立名稱為 "node" 的目錄。請執行下列命令序列以編譯安裝 node.js；

    cd node
    ./configure
    make
    sudo make install

e) 執行下列命令以從穩定的二進位檔安裝 NPM

    curl http://npmjs.org/install.sh | sh

**步驟 2：安裝 cassandra-client 封裝**

    npm cassandra-client 

**步驟 3：準備 Cassandra 儲存體**

Cassandra 儲存體使用 KEYSPACE 和 COLUMNFAMILY 的概念，這些概念大致上可相當於 RDBMS 用語中的 DATABASE 和 TABLE 結構。KEYSAPCE 會包含一組 COLUMNFAMILY 定義。每個 COLUMNFAMILY 會包含一組資料列，每個資料列又會包含數個資料欄，如下方的複合檢視所示：

![Rows and columns](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux3.png)

我們將使用先前部署的 Cassandra 叢集，透過建立及查詢上述資料結構示範 node.js 存取。我們將建立一個簡單的 node.js 指令碼來執行叢集的基本準備以儲存客戶資料。指令碼中顯示的技術可以輕鬆用於 node.js Web 應用程式或 Web 服務。請記住，程式碼片段的用意僅在於示範運作方式，若要做為真實世界的方案，所顯示的程式碼還有很大的改進空間 (例如安全性、記錄、延展性等)。

讓我們在指令碼範圍定義必要的變數，以納入來自 cassandra-client 模組的 PooledConnection 以及常用的 keyspace 名稱和 keyspace 連接參數：

    casdemo.js: 
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts:['<azure_svc_name>.cloudapp.net:9160'], 
    keyspace:ksName, use_bigints:false };

在為儲存客戶資料做準備時，我們必須先使用下列指令碼範例建立一個 KEYSPACE：

    casdemo.js: 
    function createKeyspace(callback){
    var cql = 'CREATE KEYSPACE ' + ksName + ' WITH 
    strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
    var sysConOptions = { hosts:['<azure_svc_name>.cloudapp.net:9160'],  
    keyspace:'system', use_bigints:false };
    var con = new pooledCon(sysConOptions);
    con.execute(cql,[],function(err) {
    if(err) {
    console.log("Failed to create Keyspace:" + ksName);
    console.log(err);
       }
    else{
    console.log("Created Keyspace:" + ksName);
    callback(ksConOptions, populateCustomerData);
       }
       });
    con.shutdown();
    } 

createKeysapce 函數會以回呼函數做為引數，這個引數將用來執行 COLUMNFAMILY 建立函數，因為 KEYSPACE 是建立資料欄系列的必要條件。請注意，我們必須連線至「系統」的 KEYSPACE 以取得應用程式 KEYSPACE 定義。在這些程式碼片段中一致地使用了 [Cassandra 查詢語言 (Cassandra Query Language，CQL)](http://cassandra.apache.org/doc/cql/CQL.html) (英文) 來與叢集整合。由於上述指令碼中撰寫的 CQL 並沒有任何參數標記，因此我們在 PooledConnection.execute() 方法使用空白的參數集合 ("[]")。

順利建立主要空間之後，將會執行下列程式碼片段中所示的 createColumnFamily() 函數，以建立必要的 COLUMNFAMILY 定義：

    casdemo.js: 
    //Creates COLUMNFAMILY
    function createColumnFamily(ksConOptions, callback){
    var params = ['customers_cf','custid','varint','custname',
    'text','custaddress','text'];
    var cql = 'CREATE COLUMNFAMILY 
          (
           
           PRIMARY KEY,
           
          , 
           
          )';
    var con =  new pooledCon(ksConOptions);
    con.execute(cql,params,function(err) {
    if(err) {
    console.log("Failed to create column family:" + params[0]);
    console.log(err);
          }
    else{
    console.log("Created column family:" + params[0]);
    callback();
          }
      });
    con.shutdown();
    } 

參數化 CQL 範本將與參數物件結合，以產生有效的 CQL 來建立 COLUMNFAMILY。順利建立 COLUMNFAMILY 之後，將會把所提供的回呼 (在此例中為 populateCustomerData()) 當做非同步呼叫鏈結的一部分來呼叫。

    casdemo.js: 
    //populate Data
    function populateCustomerData() {
    var params = ['John','Infinity Dr, TX', 1];
    updateCustomer(ksConOptions,params);

    params = ['Tom','Fermat Ln, WA', 2];
    updateCustomer(ksConOptions,params);
    }

    //update will also insert the record if none exists
    function updateCustomer(ksConOptions,params)
    {
    var cql = 'UPDATE customers_cf SET custname=
      ,custaddress=
       where 
    custid=
      ';
    var con = new pooledCon(ksConOptions);
    con.execute(cql,params,function(err) {
    if (err) console.log(err);
    else console.log("Inserted customer :" + params[0]);
      });
    con.shutdown();
    }

populateCustomerData() 會將一些資料列插入 COLUMNFAMILY (亦即 customers\_cf) 中。在 Cassandra 查詢語言中，如果在讓 INSERT CQL 陳述式變成多餘的程序中記錄還不存在，UPDATE 將會插入記錄。

到目前為止，我們串聯了以下回呼鏈結：createKeyspace() 至 createColumnFamily() 至 populateCustomerData()。現在即可透過下列程式碼片段執行程式碼：

    casdemo.js:
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts:['<azure_svc_name>.cloudapp.net:9160'], 
    keyspace:ksName, use_bigints:false };

    createKeyspace(createColumnFamily);
    //rest of the not shown

請從殼層命令提示字元執行下列命令以執行指令碼：

    //the following command will create the KEYSPACE, COLUMNFAMILY and //inserts two customer records
    $ node casdemo.js

readCustomer() 方法會存取 Azure 裝載的叢集，並顯示執行 CQL 查詢之後所擷取到的 JSON 程式碼片段：

    casdemo.js: 
    //read the two rows inserted above
    function readCustomer(ksConOptions)
    {
    var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
    var con = new pooledCon(ksConOptions);
    con.execute(cql,[],function(err,rows) {
    if(err) 
    console.log(err);
    else 
    for (var i=0; i<rows.length; i++)
    console.log(JSON.stringify(rows[i]));
        });
    con.shutdown();
    } 

請修改 casdemo.js 以將上述函數加入，並在為先前呼叫的 createKeyspace() 方法加上註解之後呼叫該函數，如以下所示：

    casdemo.js: 
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts:['<azure_svc_name>.cloudapp.net:9160'], 
    keyspace:ksName, use_bigints:false };

    //createKeyspace(createColumnFamily);
    readCustomer(ksConOptions)
    //rest of the code below not shown

結論
----

Azure 虛擬機器功能允許建立 Linux (由 Microsoft 合作夥伴提供映像) 和 Windows 虛擬機器，這可讓您不須做任何變更即可移轉現有的伺服器產品。本文中所討論的 Cassandra NoSQL 資料庫伺服器即是其中一個這樣的例子。本文中設定的 Cassandra 叢集可由 Azure 裝載的雲端服務、協力廠商公用雲端及私人雲端 (Windows 和 Linux OS 環境皆可) 存取。在本文中，我們涵蓋了以 node.js 做為用戶端；不過，也可以從 .NET、Java 及其他語言環境存取 Cassandra。

