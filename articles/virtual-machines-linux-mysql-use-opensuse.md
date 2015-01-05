<properties urlDisplayName="Install MySQL" pageTitle="在 Azure 中執行 OpenSUSE Linux 的虛擬機器上安裝 MySQL" metaKeywords="Azure, MySQL" description="Learn to install MySQL on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="kathydav" />

# 在 Azure 中執行 OpenSUSE Linux 的虛擬機器上安裝 MySQL

[MySQL][MySQL] 是廣受使用的開放原始碼 SQL 資料庫。在 [Azure 管理入口網站中][AzurePortal]，您可以建立執行 OpenSUSE Linux 的虛擬機器。接著，就可以在虛擬機器上安裝和設定 MySQL 資料庫。

本教學課程將說明：

- 如何使用管理入口網站，從可透過 Azure 取得的映像建立 OpenSUSE Linux 虛擬機器。
- 如何使用 SSH 或 PuTTY 來連線至虛擬機器。
- 如何在虛擬機器上安裝 MySQL。

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## 建立執行 OpenSUSE Linux 的虛擬機器

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

##在虛擬機器上安裝和執行 MySQL

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

##摘要
在本教學課程中，您了解如何建立執行 OpenSUSE Linux 的虛擬機器，以及從遠端使用 SSH 或 PuTTY 來連接它。您也學到如何在 Linux 虛擬機器上安裝和設定 MySQL。如需 MySQL 的詳細資訊，請參閱 [MySQL 文件][MySQLDocs]。

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com

<!--HONumber=35.1-->
