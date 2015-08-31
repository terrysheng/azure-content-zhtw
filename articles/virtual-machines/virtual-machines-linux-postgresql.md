<properties
	pageTitle="在執行 Linux 的 Microsoft Azure 虛擬機器上安裝和設定 PostgreSQL。"
	description="了解如何在 Azure 中的 Linux 虛擬機器 (VM) 上安裝和設定 PostgreSQL。"
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
  tags=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="linux"
	ms.workload="infrastructure-services"
	ms.date="08/10/2015"
	ms.author="mingzhan"/>


#安裝和設定 Microsoft Azure 上的 PostgreSQL

PostgreSQL 是與 Oracle 和 DB2 類似的進階開放原始碼資料庫。它包含企業用功能，例如完整的 ACID 的相容性、可靠的交易式程序，以及多版本的並行控制。它也支援標準，例如 ANSI SQL 和 SQL/MED (包括 Oracle、MySQL、MongoDB 和許多其他項目的外部資料包裝函式)。其高度可擴充性支援超過 12 種程序性語言、GIN 和 GIST 索引、空間資料支援和多個類似 NoSQL 的功能，適用於 JSON 或以索引鍵-值為基礎的應用程式。

在本文中，您將學習如何在執行 Linux 的 Azure 虛擬機器上安裝和設定 PostgreSQL。

> [AZURE.NOTE]您必須已經具有執行 Linux 的 Microsoft Azure 虛擬機器，才能完成本教學課程。請參閱 [Azure Linux VM 教學課程](virtual-machines-linux-tutorial.md)以建立並設定 Linux VM，然後再繼續進行。

[在此情況下，使用 1999 連接埠做為 PostgreSQL 連接埠。]

## 安裝 PostgreSQL

連接到您透過 putty 建立的 Linux VM。如果這是您第一次使用 Azure Linux VM，請在[這裡](virtual-machines-linux-use-ssh-key.md)看看如何使用 putty 連接到 Linux VM。

1. 執行下列命令來切換至根 (admin)：

		# sudo su -

2. 某些散發套件有相依性，您必須先安裝這些相依性再安裝 PostgreSQL。檢查此清單中的 distro 並執行適當的命令：

	- Red Hat 基底 Linux：

			# yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

	- Debian 基底 Linux：

 			# apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  

	- SUSE Linux：

			# zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

3. 下載 PostgreSQL 到根目錄，接著將封裝解壓縮：

		# wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

		# tar jxvf  postgresql-9.3.5.tar.bz2

	以上是範例。您可以在[這裡](https://ftp.postgresql.org/pub/source/)找到更詳細的下載位址。

4. 若要啟動組建，請執行以下命令：

		# cd postgresql-9.3.5

		# ./configure --prefix=/opt/postgresql-9.3.5

5. 如果您想要建置所有可以建置的項目 (包括文件 (HTML 和 man 頁面) 和其他模組 (contrib))，請改為執行下列命令：

		# gmake install-world

	您會收到下列確認訊息：

		PostgreSQL, contrib, and documentation successfully made. Ready to install.

## 設定 PostgreSQL

1. (選擇性) 建立符號連結來縮短 PostgreSQL 參考，使其不包含版本號碼：

		# ln -s /opt/pgsql9.3.5 /opt/pgsql

2. 建立資料庫的目錄：

		# mkdir -p /opt/pgsql_data

3. 建立非根使用者，並修改該使用者的設定檔。然後切換到這個新的使用者 (在我們的範例中稱為 *postgres*)：

		# useradd postgres

		# chown -R postgres.postgres /opt/pgsql_data

		# su - postgres

   >[AZURE.NOTE]基於安全性理由，PostgreSQL 會使用非根使用者初始化、啟動或關閉資料庫。


4. 輸入下列命令以編輯 *bash\_profile*。這幾行將會加入至 *bash\_profile* 檔案的結尾：

		cat >> ~/.bash_profile <<EOF
		export PGPORT=1999
		export PGDATA=/opt/pgsql_data
		export LANG=en_US.utf8
		export PGHOME=/opt/pgsql
		export PATH=\$PATH:\$PGHOME/bin
		export MANPATH=\$MANPATH:\$PGHOME/share/man
		export DATA=`date +"%Y%m%d%H%M"`
		export PGUSER=postgres
		alias rm='rm -i'
		alias ll='ls -lh'
		EOF

5. 執行 *bash\_profile* 檔案：

		$ source .bash_profile

6. 利用下列命令驗證安裝：

		$ which psql

	如果您成功安裝，您將會看見下列回應：

		/opt/pgsql/bin/psql

7. 您也可以檢查 PostgreSQL 版本：

		$ psql -V

8. 初始化資料庫：

		$ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W

	您應該會收到下列輸出：

![image](./media/virtual-machines-linux-postgresql/no1.png)

## 設定 PostgreSQL

<!--	[postgres@ test ~]$ exit -->

執行以下命令：

	# cd /root/postgresql-9.3.5/contrib/start-scripts

	# cp linux /etc/init.d/postgresql

修改 /etc/init.d/postgresql 檔案中的兩個變數。前置詞設為 PostgreSQL 的安裝路徑：**/opt/pgsql**。PGDATA 設為 PostgreSQL 的資料儲存路徑：**/opt/pgsql\_data**。

	# sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

	# sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/virtual-machines-linux-postgresql/no2.png)

變更檔案，使其可執行：

	# chmod +x /etc/init.d/postgresql

啟動 PostgreSQL：

	# /etc/init.d/postgresql start

查看 PostgreSQL 端點是否位於：

	# netstat -tunlp|grep 1999

您應該會看見下列輸出：

![image](./media/virtual-machines-linux-postgresql/no3.png)

## 連接到 Postgres 資料庫

請繼續進行，然後再一次切換到 postgres 使用者：

	# su - postgres

建立 Postgres 資料庫：

	$ createdb events

連接到剛建立的事件資料庫：

	$ psql -d events

## 如何建立和刪除 Postgres 資料表

既然我們已經連接到資料庫，我們可以在其中建立資料表。

例如，利用下列命令建立新的範例 Postgres 資料表：

	CREATE TABLE potluck (name VARCHAR(20),	food VARCHAR(30),	confirmed CHAR(1), signup_date DATE);

我們現在已經利用下列資料行名稱和限制設定了 4 個資料行的資料表：

1. “name” 資料行已經由 VARCHAR 命令限制在 20 個字元以下。
2. "Food" 資料行指出每個人將會攜帶的食物項目。VARCHAR 將此文字限制在 30 個字元以下。
3. “confirmed” 資料行記錄該人員是否具有聚會的 RSVP'd。可接受的值為 "Y" 和 "N"。
4. “date” 資料行顯示他們註冊此事件的時間。Postgres 的必要日期格式為 yyyy-mm-dd。

如果您成功建立資料表，您會看到下列內容：

![image](./media/virtual-machines-linux-postgresql/no4.png)

您也可以利用下列命令檢查資料表結構：

![image](./media/virtual-machines-linux-postgresql/no5.png)

### 將資料新增至資料表

首先，將資料插入資料列：

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

您應該會看見此輸出：

![image](./media/virtual-machines-linux-postgresql/no6.png)

您也可以將更多人員新增至資料表。以下是一些選項，或者您可以建立自己的選項：

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### 顯示資料表

使用下列命令來顯示資料表：

	select * from potluck;

輸出如下：

![image](./media/virtual-machines-linux-postgresql/no7.png)

### 刪除資料表中的資料

使用下列命令刪除資料表中的資料：

	delete from potluck where name=’John’;

這樣會刪除 "John" 資料列中的所有資訊。輸出如下：

![image](./media/virtual-machines-linux-postgresql/no8.png)

### 更新資料表中的資料

使用下列命令更新資料表中的資料。對於此項目，Sandy 已確認她會參加，所以我們要將她的 RSVP 從 "N" 變更為 "Y"：

 	UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


##更多關於 PostgreSQL 的內容
立刻在 Microsoft Azure Linux VM 中完成 PostgreSQL 的安裝，並享受在 Microsoft Azure 中使用它的體驗。如需關於 PostgreSQL 的內容，請參閱[這裡](http://www.postgresql.org/)。

<!---HONumber=August15_HO8-->