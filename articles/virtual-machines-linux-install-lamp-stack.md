<properties linkid="manage-linux-common-tasks-lampstack" urlDisplayName="Install LAMP stack" pageTitle="Install the LAMP stack on a Linux virtual machine" metaKeywords="" description="Learn how to install the LAMP stack on a Linux virtual machine (VM) in Azure. You can install on Ubuntu or CentOS." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install the LAMP Stack on a Linux virtual machine in Azure" authors="" solutions="" manager="" editor="" />

在 Azure 中的 Linux 虛擬機器上安裝 LAMP 堆疊
============================================

LAMP 堆疊由下列不同元素組成：

-   **L**inux - 作業系統
-   **A**pache - Web 伺服器
-   **M**ySQL - 資料庫伺服器
-   **P**HP - 程式設計語言

安裝在 Ubuntu 上
----------------

您需要安裝下列封裝：

-   `apache2`
-   `mysql-server`
-   `php5`
-   `php5-mysql`
-   `libapache2-mod-auth-mysql`
-   `libapache2-mod-php5`
-   `php5-xsl`
-   `php5-gd`
-   `php-pear`

您可以使用單一 `apt-get install` 命令來執行此作業：

    apt-get install apache2 mysql-server php5 php5-mysql libapache2-mod-auth-mysql libapache2-mod-php5 php5-xsl php5-gd php-pear

安裝在 CentOS 上
----------------

您需要安裝下列封裝：

-   `httpd`
-   `mysql`
-   `mysql-server`
-   `php`
-   `php-mysql`

您可以使用單一 `yum install` 命令來執行此作業：

    yum install httpd mysql mysql-server php-php-mysql

設定
----

1.  設定 **Apache**。

    1.  您需要重新啟動 Apache Web 伺服器。執行以下命令：

             sudo /etc/init.d/apache2 restart

    2.  檢查安裝結果是否正常運作。在瀏覽器中移至：<http://localhost>。應該會顯示 "It works!"。

2.  設定 **MySQL**。
    1.  執行下列命令來設定 mysql 的根密碼

             mysqladmin -u root -p password yourpassword

    2.  使用 `mysql` 或各種 MySQL 用戶端來登入主控台。

3.  設定 **PHP**。

    1.  執行下列命令來啟用 Apache PHP 模組：

             sudo a2enmod php5

    2.  執行下列命令來重新啟動 Apache：

             sudo service apache2 restart

進階閱讀
--------

有許多關於在 Ubuntu 上設定 LAMP 堆疊的資源可供參考。

-   <https://help.ubuntu.com/community/ApacheMySQLPHP>
-   <http://fedorasolved.org/server-solutions/lamp-stack>

