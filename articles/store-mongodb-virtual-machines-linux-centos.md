<properties urlDisplayName="Install MongoDB" pageTitle="在 Azure 中執行 CentOS Linux 的虛擬機器上安裝 MongoDB" metaKeywords="Azure, MongoDB" description="了解如何在 Azure 的虛擬機器上安裝 Mongo DB。" metaCanonical="" services="" documentationCenter="" title="在 Azure 中執行 CentOS Linux 的虛擬機器上安裝 MongoDB" authors="bbenz, MSOpenTech" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="" ms.topic="article" ms.date="01/01/1900" ms.author="bbenz, MSOpenTech" />

# 在 Azure 中執行 CentOS Linux 的虛擬機器上安裝 MongoDB

[MongoDB][MongoDB] (英文) 是受歡迎的高效能開放原始碼 NoSQL 資料庫。您可以使用 [Azure 管理入口網站][Azure 管理入口網站]，從映像庫建立執行 CentOS Linux 的虛擬機器。然後在虛擬機器上安裝及設定 MongoDB 資料庫。

您將了解：

-   如何使用管理入口網站從映像庫建立執行 CentOS Linux 的虛擬機器。
-   如何使用 SSH 或 PuTTY 來連線至虛擬機器。
-   如何在虛擬機器上安裝 MongoDB。

## 建立執行 CentOS Linux 的虛擬機器

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

## 在虛擬機器上安裝及執行 MongoDB

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

## 摘要

在本教學課程中，您已了解如何建立 Linux 虛擬機器及使用 SSH 或 PuTTY 遠端連線至此虛擬機器。同時也已了解如何在 Linux 虛擬機器上安裝及設定 MongoDB。如需有關 MongoDB 的詳細資訊，請參閱 [MongoDB 文件][MongoDB 文件] (英文)。

  [MongoDB]: http://www.mongodb.org/
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [MongoDB 文件]: http://www.mongodb.org/display/DOCS/Home
