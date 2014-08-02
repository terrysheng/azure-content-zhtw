<properties linkid="manage-linux-common-task-mongodb-virtual-machine" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Linux virtual machine in Azure" metaKeywords="Azure vm CentOS, Azure vm Linux, Linux vm, Linux MongoDB" description="Learn how to create an Azure virtual machine with CentOS Linux, and then use SSH or PuTTY to install MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

在 Azure 中執行 CentOS Linux 的虛擬機器上安裝 MongoDB
=====================================================

[MongoDB](http://www.mongodb.org/) (英文) 是受歡迎的高效能開放原始碼 NoSQL 資料庫。您可以使用 Azure 管理入口網站，從映像庫建立執行 Linux 的虛擬機器。然後在虛擬機器上安裝及設定 MongoDB 資料庫。

您將了解：

-   如何使用管理入口網站，從映像庫選取及安裝執行 CentOS Linux 的 Linux 虛擬機器。

-   如何使用 SSH 或 PuTTY 連線到虛擬機器。
-   如何在虛擬機器上安裝 MongoDB。

建立執行 CentOS Linux 的虛擬機器
--------------------------------

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

連接資料磁碟
------------

[WACOM.INCLUDE [attach-data-disk-centos-vm-in-portal](../includes/attach-data-disk-centos-vm-in-portal.md)]

在虛擬機器上安裝及執行 MongoDB
------------------------------

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

摘要
----

在本教學課程中，您已了解如何建立 Linux 虛擬機器及使用 SSH 或 PuTTY 遠端連線至此虛擬機器。同時也已了解如何在 Linux 虛擬機器上安裝及設定 MongoDB。如需有關 MongoDB 的詳細資訊，請參閱 [MongoDB 文件](http://www.mongodb.org/display/DOCS/Home) (英文)。

