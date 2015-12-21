<properties
	pageTitle="疑難排解 Azure SQL Database 權限和存取"
	description="疑難排解常見的權限、存取、使用者和登入問題的快速步驟"
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/10/2015"
	ms.author="daleche"/>

#疑難排解常見的 Azure SQL Database 權限和存取問題
使用本主題以獲得授與及移除對 Azure SQL Database 的存取權的快速步驟。如需更完整資訊，請參閱：

- [管理 Azure SQL Database 的資料庫和登入](sql-database-manage-logins.md)
- [保護您的 SQL Database](sql-database-security)
- [SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](https://msdn.microsoft.com/library/bb510589)

##若要變更邏輯伺服器的系統管理密碼
- 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [SQL Server]、從清單中選取伺服器，然後按一下 [重設密碼]。
##為協助確保只有授權的 IP 位址可以存取伺服器
- 請參閱[如何：在 SQL Database 上進行防火牆設定](sql-database-configure-firewall-settings.md)。

##若要在使用者資料庫中建立自主的資料庫使用者
- 使用 [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) 陳述式，並參閱[自主資料庫使用者 - 讓資料庫具有可攜性](https://msdn.microsoft.com/library/ff929188.aspx)。

## 若要使用 Azure Active Directory 驗證自主的資料庫使用者
- 請參閱[使用 Azure Active Directory 驗證連接到 SQL Database](sql-database-aad-authentication.md)。

## 若要在虛擬 master 資料庫中建立高權限使用者的其他登入
-使用 [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) 陳述式，並參閱[管理 Azure SQL Database 的資料庫和登入](sql-database-manage-logins.md)的「管理登入」一節以獲得詳細資訊。

<!---HONumber=AcomDC_1210_2015-->