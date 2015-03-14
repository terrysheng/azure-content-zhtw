<properties 
	pageTitle="Azure Linux Vm 上的 MySQL 效能最佳化" 
	description="了解如何最佳化在執行 Linux 的 Azure 虛擬機器 (VM) 上執行的 MySQL。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="NingKuang" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/27/2014" 
	ms.author="ningk"/>

#Azure Linux Vm 上的 MySQL 效能最佳化 

有許多因素影響 MySQL 在 Azure 上的效能，包括虛擬硬體的選取和軟體的組態。本文著重於透過儲存體、系統和資料庫組態來最佳化效能。

##利用 Azure 虛擬機器上的 RAID 
儲存體是影響雲端環境之資料庫效能的關鍵因素。相較於單一磁碟，RAID 可透過並行存取提供更快速的存取。請參閱[標準 RAID 層級](http://en.wikipedia.org/wiki/Standard_RAID_levels)了解詳細資訊。   

透過 RAID 能大幅改善 Azure 中的磁碟 I/O 輸送量和 I/O 回應時間。我們的實驗測試顯示，當 RAID 的數目變成兩倍時 (從 2 到 4、4 到 8，依此類推)，磁碟 I/O 輸送量平均能變成兩倍，而 I/O 回應時間平均能減少一半。請參閱[附錄 A](#AppendixA) 了解詳細資訊。  

除了磁碟 I/O，增加 RAID 層級也會改善 MySQL 的效能。請參閱[附錄 B](#AppendixB) 了解詳細資訊。  

您可能也會想要考慮的區塊大小。通常當您有較大的區塊時，您會有較低的額外負荷，特別是針對大型的寫入。不過，當區塊大小太大時，它可能會增加額外負荷，而且您無法利用 RAID 的優點。目前預設的大小為 512KB，經證實這是一般生產環境的最佳大小。請參閱[附錄 C](#AppendixC) 了解詳細資訊。   

請注意，您可加入的磁碟數目對於不同的虛擬機器類型有所限制。這些限制在 [Azure 的虛擬機器和雲端服務大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)中有詳細討論。雖然您可以選擇使用較少的磁碟設定 RAID，但您需要 4 個連接的資料磁碟，才能按照本文中的 RAID 範例進行。  

本文假設您已經建立 Linux 虛擬機器，並且已安裝和設定 MySQL。請參考「如何在 Azure 上安裝 MySQL」了解入門的詳細資訊。  
  
###在 Azure 上設定 RAID
下列步驟說明如何使用 Windows Azure 管理入口網站在 Azure 上建立 RAID。您也可以使用 Windows PowerShell 指令碼設定 RAID。 
在此範例中我們將使用 4 個磁碟設定 RAID 0。  

####步驟 1：將資料磁碟加入您的虛擬機器  

在 Azure 管理入口網站中的 [虛擬機器] 頁面中，按一下您想要加入資料磁碟的虛擬機器。在此範例中，該虛擬機器為 mysqlnode1。  

![][1]

在該虛擬機器的頁面上，按一下 [儀表板]。  

![][2]
 

在工作列上，按一下 [連接]。
 
![][3]

然後按一下 [連接空的磁碟]。  

![][4]
 
對於資料磁碟，[主機快取偏好設定] 應該設為 [無]。  

這會新增一個空的磁碟到您的虛擬機器。請再重複此步驟三次，好讓您有 4 個用於 RAID 的資料磁碟。  

您可以在核心訊息記錄查看虛擬機器中加入的磁碟。例如，若要在 Ubuntu 上查看此記錄，請使用下列命令：  

	sudo grep SCSI /var/log/dmesg

####步驟 2：使用額外的磁碟建立 RAID
請遵循此文件了解詳細的 RAID 設定步驟：  

[http://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-RAID/](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-RAID/)

>[AZURE.NOTE] 如果您正在使用 XFS 檔案系統，在您建立 RAID 之後請遵循下列步驟。

若要在 Debian、Ubuntu 或 Linux Mint 上安裝 XFS 請使用下列命令：  

	apt-get -y install xfsprogs  

 若要在 Fedora、CentOS 或 RHEL 上安裝 XFS 請使用下列命令：  

	yum -y install xfsprogs  xfsdump 


####步驟 3：設定新的儲存體路徑
使用下列命令：  

	root@mysqlnode1:~# mkdir -p /RAID0/mysql

####步驟 4：將原始資料複製到新的儲存體路徑
使用下列命令：  

	root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

####步驟 5：修改權限，使 MySQL 能夠存取 (讀取和寫入) 資料磁碟
使用下列命令：  

	root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


##調整磁碟 I/O 排程演算法
Linux 實作四種類型的 I/O 排程演算法：  

-	NOOP 演算法 (No Operation)
-	期限演算法 (Deadline)
-	完全公平佇列演算法 (CFQ)
-	預算期間演算法(Anticipatory)  

您可以在不同情況選取不同的 I/O 排程器以最佳化效能。在完全隨機存取的環境中，CFQ 和 Deadline 演算法的效能沒有太大的差別。通常建議將 MySQL 資料庫環境設為 Deadline 以維持穩定性。如果有大量的循序 I/O，CFQ 可能會降低磁碟 I/O 的效能。   

對於 SSD 和其他設備，使用 NOOP 或 Deadline 可以達到比「預設」排程器更好的效能。   

自核心 2.5 開始，預設的 I/O 排程演算法是 Deadline。自核心 2.6.18 開始，CFQ 為預設的 I/O 排程演算法。您可以在核心開機時間指定此設定，或在系統執行時動態地修改此設定。  

下列範例會示範如何檢查，以及將 NOOP 演算法設定為預設排程器。  

Debian 散發套件系列：

###步驟 1. 檢視目前的 I/O 排程器
使用下列命令：  

	root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler 

您會看到下列輸出，表示目前的排程器。  

	noop [deadline] cfq 


###步驟 2.變更目前裝置 (/dev/sda) 的 I/O 排程演算法
使用下列命令：  

	azureuser@mysqlnode1:~$ sudo su -
	root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
	root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
	root@mysqlnode1:~# update-grub

>[AZURE.NOTE] 只針對 /dev/sda 做此設定並沒有幫助。必須在所有資料庫所在的磁碟上做此設定。  

您應該會看到下列輸出，表示已經成功地重建 grub.cfg，且預設排程器已經更新為 NOOP。  

	Generating grub configuration file ...
	Found linux image: /boot/vmlinuz-3.13.0-34-generic
	Found initrd image: /boot/initrd.img-3.13.0-34-generic
	Found linux image: /boot/vmlinuz-3.13.0-32-generic
	Found initrd image: /boot/initrd.img-3.13.0-32-generic
	Found memtest86+ image: /memtest86+.elf
	Found memtest86+ image: /memtest86+.bin
	done

針對 Redhat 散發套件系列，您只需要下列命令：   

	echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

##設定系統檔案作業設定
一項最佳做法是停用檔案系統上的 Atime 記錄功能。Atime 是上一次存取檔案的時間。每當存取檔案時，檔案系統會在記錄檔中記錄時間戳記。不過，這項資訊很少使用。如果不需要，您可以將它停用，這樣能減少整體的磁碟存取時間。  
 
若要停用 Atime 記錄，您必須修改檔案系統組態檔案 /etc/fstab，並加入 **noatime** 選項。  

例如，編輯 vim /etc/fstab 檔案並新增 noatime，如下所示。  

	# CLOUD_IMG: This file was created/modified by the Cloud Image build process
	UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
	#Add the "noatime" option below to disable atime logging
	UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
	/dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

然後使用下列命令重新掛接檔案系統：  

	mount -o remount /RAID0

測試修改過的結果。請注意，當您修改測試檔案時，存取時間不會更新。  

修改前範例：		

![][5]
 
修改後範例：

![][6]

##增加高並行存取之系統控制代碼的最大數目
MySQL 是高並行存取資料庫。Linux 的並行控制代碼預設數目是 1024，這不總是足夠。**使用下列步驟增加系統的最大並行控制代碼，以支援 MySQL** 的高並行存取。

###步驟 1：修改 limits.conf 檔案
在 /etc/security/limits.conf 檔案加入下列四行，以增加允許之並行控制代碼的最大值。請注意，65536 是系統能支援的最大數目。   

	* soft nofile 65536
	* hard nofile 65536
	* soft nproc 65536
	* hard nproc 65536

###步驟 2：針對新的限制更新系統
執行以下命令：  

	ulimit -SHn 65536
	ulimit -SHu 65536 

###步驟 3：確定在開機時間已更新限制
將下列啟動命令加入 /etc/rc.local 檔案中，使它在每個開機時間內生效。  

	echo "ulimit -SHn 65536" >>/etc/rc.local
	echo "ulimit -SHu 65536" >>/etc/rc.local

##MySQL 資料庫最佳化 
您可以將設定 Azure 上 MySQL 的相同效能調整策略用於內部部署電腦。  

主要 I/O 的最佳化規則：   

-	增加快取的大小。
-	減少 I/O 回應時間。

若要最佳化 MySQL 伺服器設定，您可以更新 my.cnf 檔案，也就是伺服器和用戶端電腦的預設組態檔。  

下列組態項目是影響 MySQL 效能的主要因素：  

-	**innodb_buffer_pool_size**：緩衝集區包含經過緩衝處理的資料和索引。這通常設定為 70% 的實體記憶體。
-	**innodb_log_file_size**：這是重做記錄檔大小。您可以使用重做記錄檔確保寫入作業快速可靠，並可在損毀之後復原。此項設定為 512 MB，將提供您充足的空間來記錄寫入作業。
-	**max_connections**：有時候應用程式無法適當地關閉連線。較大的值可讓伺服器有更多時間來回收閒置的連線。連線數目上限為 10000，但建議的最大值為 5000。
-	**Innodb_file_per_table**：此設定會啟用或停用 InnoDB 在個別檔案儲存資料表的功能。開啟此設定會確保能有效地套用幾個進階的管理作業。從效能觀點來看，它可以加速資料表空間傳輸，並將零碎資料管理效能最佳化。所以建議將此項設為 ON。</br>
	自 MySQL 5.6 開始，預設設定是 ON。因此，不需要採取動作。對於其他早於 5.6 的版本，預設設定是 OFF。需要將此項設為 ON。且需要在資料載入之前透用它，因為只有新建立的資料表會受到影響。
-	**innodb_flush_log_at_trx_commit**：預設值是 1，且範圍設定為 0~2。預設值是最適合獨立的 MySQL 資料庫的選項。2 的設定有最好的資料完整性，而且很適合 MySQL 叢集中的 Master。0 的設定允許資料遺失，這會影響可靠性，但在某些情況會有較佳的效能，這適合 MySQL 叢集中的 Slave。
-	**Innodb_log_buffer_size**：記錄緩衝區可讓交易執行，而不需要在交易認可之前排清磁碟的記錄檔。不過，如果有大型二進位物件或文字欄位，會非常快速地耗用快取，將會觸發頻繁的磁碟 I/O。如果 Innodb_log_waits 狀態變數不是 0，最好增加緩衝區大小。
-	**query_cache_size**：最佳的選項是一開始就停用它。將 query_cache_size 設為 0 (在 MySQL 5.6 中此為預設值)，並使用其他方法來加速查詢。  
  
請參閱[附錄 D](AppendixD) 了解最佳化之後的效能比較。


##開啟分析效能瓶頸的 MySQL 緩慢查詢記錄
 MySQL 緩慢查詢記錄可以協助您識別 MySQL 的緩慢查詢。啟用 MySQL 緩慢查詢記錄之後，您可以使用像是 **mysqldumpslow** 的 MySQL 工具來識別效能瓶頸。  

請注意，預設未啟用此選項。開啟緩慢查詢記錄可能會耗用一些 CPU 資源。因此，建議您在疑難排解效能瓶頸時啟用此選項。

###步驟 1：將下列幾行加入 my.cnf 檔案的結尾進行修改   

	long_query_time = 2
	slow_query_log = 1
	slow_query_log_file = /RAID0/mysql/mysql-slow.log

###步驟 2：重新啟動 MySQL 伺服器
	service  mysql  restart

###步驟 3：使用 "show" 命令檢查設定是否生效
 
![][7]   
   
![][8]
 
在此範例中，您可以看到緩慢查詢功能已開啟。然後您可以使用 **mysqldumpslow** 工具判斷效能瓶頸，並最佳化效能，例如加入索引。





##附錄

以下是在特定的實驗室環境所產生的樣本效能測試資料，它們提供使用不同效能調整方法，所得到的效能資料趨勢之一般性背景，不過，這些資料可能會因為不同環境或產品版本而有所差異。 

<a name="AppendixA"></a>附錄 A：

**不同 RAID 層級的磁碟效能 (IOPS)** 


![][9]
 
**測試命令：**  

	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

>AZURE.NOTE: 這項測試的工作負載會使用 64 個執行緒，嘗試達到 RAID 的最高上限。

<a name="AppendixB"></a>附錄 B：

**使用不同 RAID 層級比較 MySQL 效能 (輸送量)**   

(XFS 檔案系統)

 
![][10]  
![][11]

**測試命令：**

	mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write -engine=innodb

**使用不同 RAID 層級比較 MySQL 效能 (OLTP)**  
![][12]

**測試命令：**

	time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

<a name="AppendixC"></a>附錄 C： 

**比較不同區塊大小的磁碟效能 (IOPS)**  

(XFS 檔案系統)

![][13]

**測試命令：**  

	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

請注意，這項測試所使用的檔案大小分別為 30 GB 和 1 GB，使用 RAID 0 (4 個磁碟) XFS 檔案系統。


<a name="AppendixD"></a>附錄 D：

**最佳化前與最佳化後的 MySQL 效能比較 (輸送量)**  

(XFS 檔案系統)

  
![][14]

**測試命令：**

	mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write -engine=innodb,misam

**預設及最佳化的組態設定如下：**

|參數	|預設	|最佳化
|-----------|-----------|-----------
|**innodb_buffer_pool_size**	|無	|7G
|**innodb_log_file_size**	|5M	|512M
|**max_connections**	|100	|5000
|**innodb_file_per_table**	|0	|1
|**innodb_flush_log_at_trx_commit**	|1	|2
|**innodb_log_buffer_size**	|8M	|128M
|**query_cache_size**	|16M	|0


如需更多及更詳細的最佳化組態參數，請參考 MySQL 官方指示。  

[http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)  

[http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)

**測試環境**  

|硬體	|詳細資訊
|-----------|-------
|CPU	|AMD Opteron(tm) 處理器 4171 HE/4 核心
|記憶體	|14G
|磁碟	|10G/disk
|作業系統	|Ubuntu 14.04.1 LTS



[1]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-01.png
[2]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-02.png
[3]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-03.png
[4]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-04.png
[5]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-05.png
[6]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-06.png
[7]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-07.png
[8]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-08.png
[9]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-09.png
[10]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-10.png
[11]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-11.png
[12]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-12.png
[13]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-13.png
[14]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-14.png


<!--HONumber=42-->
