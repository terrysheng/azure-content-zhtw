<properties
	pageTitle="疑難排解 Azure SQL Database 常見的連接問題"
	description="找出並解決 Azure SQL Database 常見之連接錯誤的步驟。"
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/29/2016"
	ms.author="daleche"/>

# 疑難排解 Azure SQL Database 常見的連接問題
[AZURE.INCLUDE [支援免責聲明](../../includes/support-disclaimer.md)]

Azure SQL Database 的連接問題可以廣泛地分類為下列任一項︰

- 暫時性錯誤 (短期或間歇性)
- 持續性或非暫時性錯誤 (定期重複發生的錯誤)

如果您的應用程式發生暫時性錯誤，請檢閱下列主題以取得有關如何疑難排解這些錯誤並減少其發生頻率的提示︰

- [疑難排解無法使用 伺服器 &lt;y&gt; 上的資料庫 &lt;x&gt; (錯誤：40613)](sql-database-troubleshoot-connection.md)
- [排解、診斷和防止 SQL Database 的 SQL 連接錯誤和暫時性錯誤](sql-database-connectivity-issues.md)

如果應用程式持續無法連接到 SQL Azure Database，通常表示下列其中一項發生問題︰

- 防火牆組態。Azure SQL Database 或用戶端防火牆封鎖 Azure SQL Database 的連接。
- 用戶端上的網路重新設定：例如，新的 IP 位址或 Proxy 伺服器。
- 使用者錯誤︰例如，輸入錯誤的連線參數，例如連接字串中的伺服器名稱。

## 解決永久性連線問題的步驟
1.	設定[防火牆規則](sql-database-configure-firewall-settings.md)允許用戶端 IP 位址。
2.	在用戶端與網際網路之間的所有防火牆上，請確定開放連接埠 1433 供輸出連線使用。檢閱[設定 Windows 防火牆以允許 SQL Server 存取](https://msdn.microsoft.com/library/cc646023.aspx)，以取得其他指標。
3.	請確認您的連接字串和其他連線設定。請參閱[連線能力問題主題](sql-database-connectivity-issues.md#connections-to-azure-sql-database)中的「連接字串」一節。
4.	檢查儀表板中的服務健全狀況。如果您認為沒有區域性停電，請參閱[從中斷復原](sql-database-disaster-recovery.md)以了解復原到新區域的步驟。
5.	如果連線問題持續發生，請選取 [Azure 支援](https://azure.microsoft.com/support/options)網站上的 [取得支援]，來提出 Azure 支援要求。

<!---HONumber=AcomDC_0330_2016-->