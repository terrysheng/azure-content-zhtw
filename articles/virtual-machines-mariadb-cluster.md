<properties pageTitle="在 Azure 上執行 MariaDB (MySQL) 叢集" description="在 Azure 虛擬機器上建立 MariaDB + Galera MySQL 叢集" services="virtual-machines" documentationCenter="" authors="sabbour" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="12/3/2014" ms.author="v-ahsab" ms.prod="azure"/>

<!--The next line, with one pound sign at the beginning, is the page title--> 
# MariaDB (MySQL) 叢集 - Azure 教學課程

<p>我們要建立 [MariaDB](https://mariadb.org/en/about/) 的多重主機 [Galera](http://galeracluster.com/products/) 叢集，它穩固、可擴充且非常可靠，可在某些時刻取代 MySQL，在 Azure 虛擬機器上的高可用環境中運作。</p>

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

+ [架構概觀]
+ [建立範本]
+ [建立叢集]
+ [為叢集設定負載平衡]
+ [驗證叢集]


## 架構概觀

本主題會執行下列步驟：

1. 建立具有 3 個節點的叢集
2. 將資料磁碟與 OS 磁碟分開
3. 以 RAID-0/等量設定中建立資料磁碟以增加 IOPS
4. 使用 Azure 負載平衡器來平衡 3 個節點的負載
5. 為了減少重複性的工作，請建立一個包含 MariaDB+Galera 的 VM 映像，並使用它來建立另一個叢集 VM。

![Architecture](./media/virtual-machines-mariadb-cluster/Setup.png)

> [AZURE.NOTE]  本主題使用 [Azure CLI] 工具，請務必下載這些工具，並且根據指示將它們連線至您的 Azure 訂用帳戶。如果您需要 [Azure CLI] 中可用命令的參考，請查看這個 [Azure CLI 命令參考]連結。您也必須[建立驗證的 SSH 金鑰]，並且記下「.pem 檔案位置」。


## 建立範本

### 基礎結構

1. 建立可將資源保存在一起的同質群組

		azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
        
2. 建立虛擬網路

		azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet

3. 建立裝載所有磁碟的儲存體帳戶。請注意，請不要在同一個儲存體帳戶放置 40 個以上重度使用的磁碟，以避免達到 20,000 IOPS 的儲存體帳戶限制。在此案例中，因為我們的使用量遠低於這個數字，所以為了簡單起見，我們將在同一個帳戶上儲存所有項目。

		azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster

3. 尋找 CentOS 7 虛擬機器映像的名稱

		azure vm image list | findstr CentOS        
這會產生輸出結果如下所示： `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`。 請在下列步驟中使用該名稱。

4. 建立 VM 範本，並使用您儲存產生之 .pem SSH 金鑰的路徑取代 **/path/to/key.pem**

		azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
        
5. 將 4 個 500GB 的資料磁碟連接到 VM 以便用於 RAID 設定

		FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd        

6. 使用 SSH 方式連線到您在 **mariadbhatemplate.cloudapp.net:22** 建立的範本 VM，然後使用您的私密金鑰連線。

### 軟體

1. 取得 root

        sudo su
        
2. 安裝 RAID 支援：

     - 安裝 mdadm
        
        		yum install mdadm
                
     - 建立具有 EXT4 檔案系統的 RAID0/等量設定
        
				mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
				mdadm --detail --scan >> /etc/mdadm.conf
				mkfs -t ext4 /dev/md0
         
     - 建立掛接點目錄
         
				mkdir /mnt/data
                
     - 抓取新建立之 RAID 裝置的 UUID
        
				blkid | grep /dev/md0
    
     - 編輯 /etc/fstab
        
        		vi /etc/fstab
        
     - 在該處新增裝置，以啟用重新開機時自動掛接的功能，並將 UUID 取代為從先前的 **blkid** 命令取得的值
        
        		UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
        	
     - 掛接新磁碟分割
        
        		mount /mnt/data
            
3. 安裝 MariaDB：    
		
     - 建立 MariaDB.repo 檔案： 
        
              	vi /etc/yum.repos.d/MariaDB.repo
        
     - 在其中填入下列內容      
        
				[mariadb]
				name = MariaDB
				baseurl = http://yum.mariadb.org/10.0/centos7-amd64
				gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
				gpgcheck=1
    
     - 移除現有的 postfix 與 mariadb-libs 以避免衝突
    
    		yum remove postfix mariadb-libs-*
            
     - 安裝 MariaDB 搭配 Galera
    
    		yum install MariaDB-Galera-server MariaDB-client galera
   
4. 將 MySQL 資料目錄移至 RAID 區塊裝置

     - 將目前的 MySQL 目錄複製到其新位置，並移除舊目錄
    
    		cp -avr /var/lib/mysql /mnt/data  
    		rm -rf /var/lib/mysql
      
     - 相對應地設定新目錄上的權限
        
        	chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/  
            
     - 建立符號連結，將舊目錄指向 RAID 磁碟分割上的新位置
    
    		ln -s /mnt/data/mysql /var/lib/mysql   

5. 因為 [SELinux 會干擾叢集操作](http://galeracluster.com/documentation-webpages/configuration.html#selinux)，所以目前的工作階段必須停用它 (直到相容的版本出現為止)。編輯  `/etc/selinux/config` 以停用後續的重新啟動：
    	
	        setenforce 0
    
       然後編輯 `/etc/selinux/config` 以設定 `SELINUX=permissive`
       
6. 確認 MySQL 順利執行

    - 啟動 MySQL
    
    		service mysql start
            
    - 保護 MySQL 安裝、設定 root 密碼、移除匿名使用者、停用遠端 root 登錄並移除測試資料庫
    		
            mysql_secure_installation
            
    - 針對叢集操作與您的應用程式 (選擇性) 在資料庫上建立使用者
   
			mysql -u root -p
			GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
            exit
   
   - 停止 MySQL
   
			service mysql stop
            
7. 建立設定預留位置

	- 編輯 MySQL 設定，以建立叢集設定的預留位置。現在不要取代 **`<Vairables>`** 或取消註解。我們從此範本建立 VM 之後才會需要這個動作。
	
			vi /etc/my.cnf.d/server.cnf
			
	- 編輯 **[galera]** 區段，並將它清除
	
	- 編輯 **[mariadb]** 區段
	
			wsrep_provider=/usr/lib64/galera/libgalera_smm.so 
            binlog_format=ROW
            wsrep_sst_method=rsync
			bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
			default_storage_engine=InnoDB
            innodb_autoinc_lock_mode=2
			
            wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
            #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
            #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
            #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
			#wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server

8. 在防火牆上開啟必要的連接埠 (使用 CentOS 7 上的 FirewallD)
	
	- MySQL： `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
    - GALERA： `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
    - GALERA IST： `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
    - RSYNC： `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
    - 重新載入防火牆： `firewall-cmd --reload`
	
9.  將系統效能最佳化。請參閱本文件的[效能微調策略]了解詳細資訊

	- 再次編輯 MySQL 設定檔
	
			vi /etc/my.cnf.d/server.cnf
		
	- 編輯 **[mariadb]** 區段並附加下列內容
	
	> [AZURE.NOTE] 建議將 **innodb\_buffer\_pool_size** 設定為 VM 記憶體的 70%。這裡為 3.5 GB RAM 的中型 Azure VM 設定為 2.45 GB。
	
	        innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70% of physical memory.
            innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
            max_connections = 5000 # A larger value will give the server more time to recycle idled connections
            innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
            innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
            innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
			query_cache_size = 0
			
10. 停止 MySQL、停止讓 MySQL 服務在啟動時執行，以避免在新增節點時弄亂叢集且取消佈建機器。

		service mysql stop
        chkconfig mysql off
		waagent -deprovision	
		
11. 透過入口網站擷取 VM。目前 [Azure CLI 工具中的問題 #1268] 描述 xplat-cli 工具所擷取的影像沒有擷取連接的資料磁碟)。

	- 透過入口網站將這部電腦關機
    - 按一下 [擷取]，然後將映像名稱指定為 **mariadb-galera-image** 並提供描述，然後選取 [我已經執行 waagent]。
	![Capture the Virtual Machine](./media/virtual-machines-mariadb-cluster/Capture.png)
	![Capture the Virtual Machine](./media/virtual-machines-mariadb-cluster/Capture2.PNG)
	
## 建立叢集
 
從您剛才建立的範本建立 3 個 VM，然後設定並啟動叢集。

1. 從建立的 **mariadb-galera-image** 映像建立第一個 CentOS 7 VM，提供虛擬網路名稱 **mariadbvnet** 和子網路 **mariadb**、機器大小為「中」、傳入雲端服務名稱為 **mariadbha** (或您想要透過 mariadbha.cloudapp.net 存取的任何名稱)、設定此機器的名稱為 **mariadb1** 和使用者名稱為 **azureuser**，以及啟用 SSH 存取和傳遞 SSH 憑證 .pem 檔案，並使用您儲存所產生之 .pem SSH 金鑰的路徑取代 **/path/to/key.pem**。

	> [AZURE.NOTE] 下列命令為清楚起見會分成多行，但是您應該以一行輸入每個命令。

		azure vm create 
        --virtual-network-name mariadbvnet
        --subnet-names mariadb 
        --availability-set clusteravset
		--vm-size Medium 
		--ssh-cert "/path/to/key.pem" 
		--no-ssh-password 
		--ssh 22 
		--vm-name mariadb1 
		mariadbha mariadb-galera-image azureuser 

2. 透過「連線」到目前建立的 **mariadbha** 雲端服務，再建立 2 部虛擬機器，變更「VM 名稱」及「SSH 連接埠」為與相同雲端服務中其他 VM 不衝突的唯一連接埠。

		azure vm create		
        --virtual-network-name mariadbvnet
        --subnet-names mariadb 
        --availability-set clusteravset
		--vm-size Medium
		--ssh-cert "/path/to/key.pem"
		--no-ssh-password
		--ssh 23
		--vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
而針對 MariaDB3

		azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb 
        --availability-set clusteravset
		--vm-size Medium
		--ssh-cert "/path/to/key.pem"
		--no-ssh-password
		--ssh 24
		--vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser
		
3. 在下一個步驟，您必須為 3 部 VM 個別取得內部 IP 位址：

	![Getting IP address](./media/virtual-machines-mariadb-cluster/IP.png)

4. 以 SSH 方式連線到 3 部 VM，並且分別編輯每部 VM 上的設定檔

		sudo vi /etc/my.cnf.d/server.cnf
		
	移除開頭的 **#** 取消註解 **`wsrep_cluster_name`** 和 **`wsrep_cluster_address`**，並且驗證它們是您所想要的結果。
    此外，分別使用 VM 的 IP 位址和名稱取代 **`wsrep_node_address`** 中的 **`<ServerIP>`** 和 **`wsrep_node_name`** 中的 **`<NodeName>`**，並一併取消這幾行的註解。
	
5. 啟動 MariaDB1 上的叢集，並讓它在啟動時執行

		sudo service mysql bootstrap
        chkconfig mysql on
	
6. 啟動 MariaDB2 與 MariaDB3 上的 MySQL，並讓它在啟動時執行

		sudo service mysql start
        chkconfig mysql on
		
## 為叢集設定負載平衡
當您建立叢集的 VM 時，您將它們加入至名為 **clusteravset** 的可用性設定組，以確保它們都放在不同容錯網域和更新網域，以及 Azure 永遠不會同時在所有電腦上進行維護。此設定符合由該 Azure 服務等級協定 (SLA) 所支援的需求。

現在您可以使用 Azure 負載平衡器來平衡我們 3 個節點之間的要求。

在您的電腦上使用 Azure CLI 執行下列命令。
命令參數結構是： `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

	azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306
	
最後，由於 CLI 設定負載平衡器探查間隔為 15 秒 (這可能有點太長)，請在入口網站的 [端點] 下變更任一個 VM 的此值

![Edit endpoint](./media/virtual-machines-mariadb-cluster/Endpoint.PNG)

然後按一下 [重新設定負載平衡集]，並執行下一個步驟

![Reconfigure Load Balanced Set](./media/virtual-machines-mariadb-cluster/Endpoint2.PNG)

然後將 [探查間隔] 變更為 5 秒並儲存

![Change Probe Interval](./media/virtual-machines-mariadb-cluster/Endpoint3.PNG)

## 驗證叢集

困難的工作已經完成。現在您應該可以透過 `mariadbha.cloudapp.net:3306` 存取叢集 (連線到上述位置即可連線到負載平衡器)，而且叢集會在 3 部 VM 之間順暢而有效率地路由要求。

使用您最愛的 MySQL 用戶端連線，或只從一個 VM 連線，確認此叢集正常運作。

	 mysql -u cluster -h mariadbha.cloudapp.net -p
	 
然後建立新的資料庫，並填入一些資料

	CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
	INSERT INTO TestTable (value)  VALUES ('Value1');
	INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;
	
將會產生下表

	+----+--------+
	| id | value  |
	+----+--------+
	|  1 | Value1 |
	|  4 | Value2 |
	+----+--------+
	2 rows in set (0.00 sec)	
	
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

在本文中，您在執行 CentOS 7 的 Azure 虛擬機器上建立了 3 個節點的 MariaDB + Galera 高可用性叢集。VM 透過 Azure 負載平衡器進行負載平衡處理。

您可能會想要了解[在 Linux 上叢集 MySQL 的另一種方法]，以及[最佳化和測試 Azure Linux VM 上的 MySQL 效能]的方法。

<!--Anchors-->
[架構概觀]: #architecture-overview
[建立範本]: #creating-the-template
[建立叢集]: #creating-the-cluster
[為叢集設定負載平衡]: #load-balancing-the-cluster
[驗證叢集]: #validating-the-cluster
[後續步驟]: #next-steps

<!--Image references-->

<!--Link references-->
[Azure CLI]: http://azure.microsoft.com/zh-tw/documentation/articles/xplat-cli/
[Azure CLI 命令參考]: http://azure.microsoft.com/zh-tw/documentation/articles/command-line-tools/
[建立驗證的 SSH 金鑰]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[效能微調策略]: http://azure.microsoft.com/sv-se/documentation/articles/virtual-machines-linux-optimize-mysql-perf/
[最佳化和測試 Azure Linux VM 上的 MySQL 效能]:http://azure.microsoft.com/sv-se/documentation/articles/virtual-machines-linux-optimize-mysql-perf/
[Azure CLI 工具中的問題 #1268]:https://github.com/Azure/azure-xplat-cli/issues/1268
[在 Linux 上叢集 MySQL 的另一種方法]: http://azure.microsoft.com/zh-tw/documentation/articles/virtual-machines-linux-mysql-cluster/

<!--HONumber=42-->
