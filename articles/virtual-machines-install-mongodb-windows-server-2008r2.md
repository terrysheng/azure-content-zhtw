<properties linkid="manage-windows-common-task-mongodb-vm" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Windows Server virtual machine" metaKeywords="Azure vm, Azure MongoDB, Azure remote desktop" description="Learn how to create an Azure virtual machine with Windows Server 2008 R2, and then use Remote Desktop to install MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running Windows Server in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />




在 Azure 中執行 Windows Server 的虛擬機器上安裝 MongoDB
=======================================================

[MongoDB](http://www.mongodb.org/) (英文) 是受歡迎的高效能開放原始碼 NoSQL 資料庫。您可以使用 [Azure 管理入口網站](http://manage.windowsazure.com)，從映像庫建立一個執行 Windows Server 的虛擬機器。然後在虛擬機器上安裝及設定 MongoDB 資料庫。

在本教學課程中，您將了解：

-   如何使用管理入口網站從映像庫建立執行 Windows Server 的虛擬機器。
-   如何使用遠端桌面連線到虛擬機器。
-   如何在虛擬機器上安裝 MongoDB。

建立執行 Windows Server 2008 R2 的虛擬機器
------------------------------------------

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

連接資料磁碟
------------

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

在虛擬機器上安裝及執行 MongoDB
------------------------------

[WACOM.INCLUDE [install-and-run-mongo-on-win2k8-vm](../includes/install-and-run-mongo-on-win2k8-vm.md)]

摘要
----

在本教學課程中，您已了解如何建立 Windows Server 虛擬機器並從遠端連線至此虛擬機器。同時也已了解如何在 Windows 虛擬機器上安裝及設定 MongoDB。如需有關 MongoDB 的詳細資訊，請參閱 [MongoDB 文件](http://www.mongodb.org/display/DOCS/Home) (英文)。

