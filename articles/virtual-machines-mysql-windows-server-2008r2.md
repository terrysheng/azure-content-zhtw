<properties linkid="manage-windows-common-tasks-install-mysql" urlDisplayName="Install MySQL" pageTitle="Create a virtual machine running MySQL in Azure " metaKeywords="Azure virtual machines, Azure Windows Server, Azure installing MySQL, Azure configuring MySQL, Azure databases" description="Create an Azure virtual machine running Windows Server 2008 R2, and then install and configure a MySQL database on the virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="12/15/2013" ms.author="kathydav" />

# 在 Azure 中執行 Windows Server 2008 R2 的虛擬機器上安裝 MySQL

[MySQL][MySQL] 是一種很受歡迎的開放原始碼 SQL 資料庫。在 [Azure 管理入口網站][Azure 管理入口網站]中，您可以從映像庫建立執行 Windows Server 2008 R2 的虛擬機器。接著，就可以在虛擬機器上安裝和設定 MySQL 資料庫。

在本教學課程中，您將了解如何：

-   使用管理入口網站來建立執行 Windows Server 2008 R2. 的虛擬機器

-   在虛擬機器上安裝和執行 MySQL Community Server。

## 建立執行 Windows Server 2008 R2 的虛擬機器

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

## 連接資料磁碟

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

## 在虛擬機器上安裝和執行 MySQL Community Server

請依照下列步驟來安裝、設定和執行 MySQL Community Server：

1.  使用遠端桌面連線到虛擬機器之後，從 [開始] 功能表中開啟 Internet Explorer。

2.  選取右上方的 [工具] 按鈕。在 [網際網路選項] 中，選取 [安全性] 索引標籤，接著選取 [受信任的網站] 圖示，最後按一下 [網站] 按鈕。將 <i>http://\*.mysql.com </i> 加入受信任的網站清單。

3.  移至[下載 MySQL Community Server][下載 MySQL Community Server] (英文)。

4.  在 [Platform] 下拉式功能表中選取 [Microsoft Windows]，然後按一下 [Select]。

5.  尋找最新版的 [Windows (x86, 64-bit), MSI Installer]，然後按一下 [Download]。

6.  選取 [No thanks, just start my download!]\(或註冊帳戶)。如果出現提示，請選取鏡像網站來下載 MySQL 安裝程式，並將安裝程式儲存至桌面。

7.  在桌面上按兩下安裝程式開始安裝。

8.  按 [下一步]。

    ![MySQL Setup][MySQL Setup]

9.  接受授權合約，然後按 [下一步]。

    ![MySQL Setup][1]

10. 按一下 [Typical] 安裝一般功能。

    ![MySQL Setup][2]

11. 按一下 [Install]。

    ![MySQL Setup][3]

12. 啟動 MySQL Configuration Wizard，然後按 [Next]。

    ![Configure MySQL][Configure MySQL]

13. 選取 [Detailed Configuration]，然後按 [Next]。

    ![Configure MySQL][4]

14. 如果打算在伺服器上與其他應用程式一起執行 MySQL，請選取 [Server Machine]，否則請選取最符合您需要的選項。按 [下一步]。

    ![Configure MySQL][5]

15. 選取 [Multifunctional Database]，或選取最符合您需要的選項。按 [下一步]。

    ![Configure MySQL][6]

16. 選取您在上述步驟中連接的資料磁碟機。

    ![Configure MySQL][7]

17. 選取 [Decision Support (DSS)/OLAP]，或選取最符合您需要的選項。按 [下一步]。

    ![Configure MySQL][8]

18. 選取 [Enable TCP/IP Networking] 和 [Add firewall exception for this port] \(這樣會在 Windows 防火牆中建立名為 **MySQL Server** 的輸入規則)。

    ![Configure MySQL][9]

19. 如果您需要以許多不同語言來儲存文字，請選取 [Best Support For Multilingualism]，否則請選取最符合您需要的選項。按 [下一步]。

    ![Configure MySQL][10]

20. 選取 [Install As Windows Service] 和 [Launch the MySQL Server automatically]。另外也選取 [Include Bin Directory in Windows PATH]。按 [下一步]。

    ![Configure MySQL][11]

21. 輸入根密碼。不要勾選 [Enable root access from remote machines] 或 [Create An Anonymous Account]。按 [下一步]。

    ![Configure MySQL][12]

22. 按一下 [Execute]，等待設定完成。

    ![Configure MySQL][13]

23. 按一下 [完成]。

    ![Configure MySQL][14]

24. 按一下 [開始]，選取 [MySQL 5.x Command Line Client] 來啟動命令列用戶端。

25. 在提示字元中輸入根密碼 (您在前一個步驟中設定)，畫面上將會出現提示，以便您發出 SQL 陳述式來與資料庫互動。

26. 若要建立新的 MySQL 使用者，請在 **mysql\>** 提示字元中執行下列命令：

        mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

    請注意，行尾的分號 (;) 對結束命令而言十分重要。

27. 若要建立資料庫並授與其 `mysqluser` 使用者權限，請發出下列命令：

        mysql> CREATE DATABASE testdatabase;
        mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

    請注意，只有連線到資料庫的指令碼會使用資料庫使用者名稱和密碼。資料庫使用者帳戶名稱不一定代表電腦上的實際使用者帳戶。

28. 若要從其他電腦登入，請執行下列命令：

        mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

    `ip-address` 是您從中連線到 MySQL 的電腦 IP 位址。

29. 若要結束 MySQL 命令列用戶端，請發出下列命令：

        quit

30. 安裝 MySQL 後，您必須設定端點，以便遠端存取 MySQL。登入 [Azure 管理入口網站][Azure 管理入口網站]。在 Azure 入口網站中，依序按一下 [虛擬機器]、新虛擬機器的名稱、[端點] 和 [加入端點]。

    ![Endpoints][Endpoints]

31. 選取 [加入端點]，按一下箭頭繼續。

    ![Endpoints][15]

32. 新增一個名稱為 "MySQL" 的端點、採用通訊協定 **TCP**，且 [公用] 和 [私人] 連接埠均設定為 "3306"。按一下核取記號。如此一來，即可遠端存取 MySQL。

    ![Endpoints][16]

33. 您可以從遠端連線至 Azure 中的虛擬機器上執行的 MySQL。從執行 MySQL 的本機電腦上，執行下列命令，以您在上述步驟中建立的 **mysqluser** 使用者身分登入：

        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

    例如，使用本教學課程中建立的虛擬機器，命令如下：

        mysql -u mysqluser -p -h testwinvm.cloudapp.net

## 摘要

在本教學課程中，您學到如何建立 Windows 2008 R2 虛擬機器和從遠端連接它。您也學到如何在虛擬機器上安裝和設定 MySQL，以及建立資料庫和新的 MySQL 使用者。如需 MySQL 的詳細資訊，請參閱 [MySQL 文件][MySQL 文件]。

  [MySQL]: http://www.mysql.com
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [create-and-configure-windows-server-2008-vm-in-portal]: ../includes/create-and-configure-windows-server-2008-vm-in-portal.md
  [attach-data-disk-windows-server-2008-vm-in-portal]: ../includes/attach-data-disk-windows-server-2008-vm-in-portal.md
  [下載 MySQL Community Server]: http://www.mysql.com/downloads/mysql/
  [MySQL Setup]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall1.png
  [1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall2.png
  [2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall3.png
  [3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall4.png
  [Configure MySQL]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig1.png
  [4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig2.png
  [5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig3.png
  [6]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig4.png
  [7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
  [8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig6.png
  [9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
  [10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
  [11]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
  [12]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png
  [13]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig11.png
  [14]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig12.png
  [Endpoints]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL0.png
  [15]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL1.png
  [16]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL.png
  [MySQL 文件]: http://dev.mysql.com/doc/
