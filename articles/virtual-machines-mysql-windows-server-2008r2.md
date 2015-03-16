<properties 
	pageTitle="在 Azure 中建立執行 MySQL 的虛擬機器" 
	description="建立執行 Windows Server 2008 R2 的 Azure 虛擬機器，然後在該虛擬機器上安裝及設定 MySQL 資料庫。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="kathydav"/>


# 在 Azure 中執行 Windows Server 2008 R2 的虛擬機器上安裝 MySQL

[MySQL](http://www.mysql.com) 是一種很受歡迎的開放原始碼 SQL 資料庫。在 [Azure 管理入口網站][AzurePreviewPortal]中，您可以從映像庫建立執行 Windows Server 2008 R2 的虛擬機器。接著，就可以在虛擬機器上安裝和設定 MySQL 資料庫。

本教學課程說明如何：

- 使用管理入口網站來建立執行 Windows Server 2008 R2. 的虛擬機器

- 在虛擬機器上安裝和執行 MySQL Community Server。

## 建立執行 Windows Server 的虛擬機器

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## 連接資料磁碟

虛擬機器建立後，請連接資料磁碟。此磁碟可提供您在安裝 MySQL 時所將需要的資料儲存體。請參閱[如何將資料磁碟連接至 Windows 虛擬機器](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/)，並遵循指示連接空磁碟。

## 登入虛擬機器
接著，您將登入虛擬機器，以安裝 MySQL。

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

## 在虛擬機器上安裝和執行 MySQL Community Server
請依照下列步驟來安裝、設定和執行 MySQL Community Server：

1. 使用遠端桌面連線到虛擬機器時，請從 [開始] 功能表中開啟 **Internet Explorer**。 

2. 選取右上方的 [工具] 按鈕。在 [網際網路選項] 中，選取 [安全性] 索引標籤，接著選取 [受信任的網站] 圖示，最後按一下 [網站] 按鈕。將  *http://\*.mysql.com* 加入至受信任的網站清單。

3. 移至[下載 MySQL Community Server] (英文)[MySQLDownloads]。

4. Select 在 [平台] 下拉式功能表中選取 **Microsoft Windows**，然後按一下 [選取]。

5. 尋找最新版的 [Windows (x86, 64-bit), MSI Installer]，然後按一下 [下載]。 

6. 選取 \[No thanks, just start my download!\] (或註冊帳戶)。如果出現提示，請選取鏡像網站來下載 MySQL 安裝程式，並將安裝程式儲存至桌面。

7. 在桌面上按兩下安裝程式開始安裝。

8. 按 [下一步]。

9. 接受授權合約，然後按 [下一步]。

10. 按一下 [一般] 以安裝一般功能。

11. 按一下 [安裝]。

12. 啟動 MySQL Configuration Wizard，然後按 [下一步]。

13. 選取 [詳細組態]，然後按 [下一步]。

14. 如果打算在伺服器上與其他應用程式一起執行 MySQL，請選取 **Server Machine**，否則請選取最符合您需要的選項。按 [下一步]。

15. 選取 **Multifunctional Database**，或選取最符合您需要的選項。按 [下一步]。

16. 選取您在上一節中連接的資料磁碟機。

	![Configure MySQL][MySQLConfig5]

17. 選取 **Decision Support (DSS)/OLAP**，或選取最符合您需要的選項。按 [下一步]。

18. 選取 **Enable TCP/IP Networking** 和 **Add firewall exception for this port** (這樣會在 Windows 防火牆中建立名為 **MySQL Server** 的輸入規則)。

	![Configure MySQL][MySQLConfig7]

19. 如果您需要以多種不同語言儲存文字，請選取 **Best Support For Multilingualism**。否則，請從其他選項中擇一使用。按 [下一步]。

	![Configure MySQL][MySQLConfig8]

20. 選取 **Install As Windows Service** 和 **Launch the MySQL Server automatically**。另外也選取 **Include Bin Directory in Windows PATH**。按 [下一步]。

	![Configure MySQL][MySQLConfig9]

21. 輸入根密碼。不要勾選 **Enable root access from remote machines** 或 **Create An Anonymous Account**。按 [下一步]。

	![Configure MySQL][MySQLConfig10]

22. 按一下 \[執行\] (Execute)，等待設定完成。

23. 按一下 \[完成\] (Finish)。

24. 按一下 \[開始\] (Start)，然後選取 **MySQL 5.x Command Line Client**，以啟動命令列用戶端。

25.  在提示字元中輸入根密碼 (您在前一個步驟中設定)，畫面上將會出現提示，以便您發出 SQL 陳述式來與資料庫互動。

26. 若要建立新的 MySQL 使用者，請在 **mysql>** 提示字元中執行下列命令：

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	行尾的分號 (;) 對結束命令而言十分重要。

27. 若要建立資料庫並授與其  `mysqluser` 使用者權限，請執行下列命令：

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	請注意，只有連線到資料庫的指令碼會使用資料庫使用者名稱和密碼。資料庫使用者帳戶名稱不一定代表電腦上的實際使用者帳戶。

28. 若要從其他電腦登入，請執行下列命令：

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	其中 `ip-address` 是您從中連線到 MySQL 的電腦 IP 位址。
	
29. 若要結束 MySQL 命令列用戶端，請執行下列命令：

		quit

30. 安裝 MySQL 後，請設定端點，以便從遠端存取 MySQL。登入 [Azure 管理入口網站][AzurePreviewPortal]。在 Azure 入口網站中，依序按一下 [虛擬機器]、新虛擬機器的名稱、[端點] 和 [加入端點]。

31. 選取 [加入端點]，按一下箭頭繼續。
	

32. 新增一個名稱為 "MySQL" 的端點、採用通訊協定 **TCP**，且 [公用] 和 [私人] 連接埠均設定為 "3306"。按一下核取記號。這可讓您從遠端存取 MySQL。
	

33. 測試您對 MySQL 的遠端連線。從執行 MySQL 的本機電腦執行如下的命令，以 **mysqluser** 使用者的身分登入：

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	For example, if you named the virtual machine "testwinvm", run this command:

		mysql -u mysqluser -p -h testwinvm.cloudapp.net

## 資源
如需 MySQL 的相關資訊，請參閱 [MySQL 文件](http://dev.mysql.com/doc/)。

[AzurePortal]: http://manage.windowsazure.com
[MySQLDownloads]: http://www.mysql.com/downloads/mysql/




<!--HONumber=42-->
