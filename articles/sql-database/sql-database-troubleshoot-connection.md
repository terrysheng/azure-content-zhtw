<properties
	pageTitle="疑難排解 Azure SQL Database 的「目前無法使用伺服器上的資料庫」"
	description="找出並解決 Azure SQL Database 連接錯誤的步驟。"
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
	ms.date="02/12/2016"
	ms.author="daleche"/>

# 疑難排解「目前無法使用伺服器上的資料庫。請稍後重試連接。」和其他連接錯誤
「伺服器 <servername> 上的資料庫 <dbname> 目前無法使用。」是 Azure SQL Database 最常見的暫時性連接錯誤。暫時性連接錯誤通常是因為規劃的事件 (例如，軟體升級) 或未規劃的事件 (例如，處理序當機)。這些錯誤通常很短暫，從幾秒鐘到最多一分鐘。如果您收到不同的錯誤，請評估[錯誤訊息](sql-database-develop-error-messages.md)以獲得發生原因的線索、判斷問題為暫時性或永久性，然後使用本主題中的指導方針處理。

## 解決暫時性連線問題的步驟
1.	檢查 [Microsoft Azure 服務儀表板](https://azure.microsoft.com/status)以得知任何已知的中斷。
2.	請確定您的應用程式使用重試邏輯。如需一般重試策略，請參閱[連線問題](sql-database-connectivity-issues.md)與[最佳做法和設計方針](sql-database-connect-central-recommendations.md)。然後請參閱[程式碼範例](sql-database-develop-quick-start-client-code-samples.md)以獲得詳情。
3.	由於資料庫接近其資源限制，因此看起來可能像是暫時性連線問題。請參閱[移難排解效能問題](sql-database-troubleshoot-performance.md)。
4.	如果仍然有連線問題，請選取 [Azure 支援](https://azure.microsoft.com/support/options)網站上的 [取得支援]，來提出 Azure 支援要求。

## 解決永久性連線問題的步驟
如果應用程式完全無法連線，通常是 IP 和防火牆設定的問題。這可能包括用戶端上的網路重新設定 (例如，新的 IP 位址或 Proxy)。連接參數輸入錯誤 (例如連接字串) 也很常見。

1.	設定[防火牆規則](sql-database-configure-firewall-settings.md)允許用戶端 IP 位址。
2.	在用戶端與網際網路之間的所有防火牆上，請確定開放連接埠 1433 供輸出連線使用。
3.	請確認您的連接字串和其他連線設定。請參閱[連線能力問題主題](sql-database-connectivity-issues.md)中的「連接字串」一節。
4.	檢查儀表板中的服務健全狀況。如果您認為沒有區域性停電，請參閱[從中斷復原](sql-database-disaster-recovery.md)以了解復原到新區域的步驟。
5.	如果仍然有連線問題，請選取 [Azure 支援](https://azure.microsoft.com/support/options)網站上的 [取得支援]，來提出 Azure 支援要求。

<!---HONumber=AcomDC_0218_2016-->