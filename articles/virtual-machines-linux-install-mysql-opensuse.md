<properties linkid="manage-linux-common-task-mysql-virtual-machine" urlDisplayName="Install MySQL" pageTitle="Install MySQL on a Linux virtual machine in Azure" metaKeywords="Azure vm OpenSUSE, Linux vm" description="Learn how to create an Azure virtual machine with OpenSUSE Linux, and then use SSH or PuTTY to install MySQL." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running OpenSUSE Linux in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# 在 Azure 中執行 OpenSUSE Linux 的虛擬機器上安裝 MySQL

[MySQL][MySQL] 是一種很受歡迎的開放原始碼 SQL 資料庫。在 [Azure 管理入口網站][Azure 管理入口網站]中，您可以從映像庫建立執行 OpenSUSE Linux 的虛擬機器。接著，就可以在虛擬機器上安裝和設定 MySQL 資料庫。

在本教學課程中，您將了解：

-   如何使用管理入口網站從組件庫建立 OpenSUSE Linux 虛擬機器。

-   如何使用 SSH 或 PuTTY 來連線至虛擬機器。

-   如何在虛擬機器上安裝 MySQL。

## 建立執行 OpenSUSE Linux 的虛擬機器

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

## 在虛擬機器上安裝和執行 MySQL

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

## 摘要

在本教學課程中，您已了解如何建立 OpenSUSE Linux 虛擬機器，以及使用 SSH 或 PuTTY 從遠端連接到此虛擬機器。您也學到如何在 Linux 虛擬機器上安裝和設定 MySQL。如需 MySQL 的詳細資訊，請參閱 [MySQL 文件][MySQL 文件]。

  [MySQL]: http://www.mysql.com
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [MySQL 文件]: http://dev.mysql.com/doc/
